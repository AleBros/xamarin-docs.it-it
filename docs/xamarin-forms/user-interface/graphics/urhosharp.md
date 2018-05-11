---
title: Utilizzo di UrhoSharp in xamarin. Forms
description: UrhoSharp può essere utilizzato per aggiungere elementi grafici 3D a un'applicazione per la visualizzazione avanzata
ms.prod: xamarin
ms.assetid: 0646B98E-CC04-4537-9715-9F82338FD7FF
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/11/2016
ms.openlocfilehash: 1a982078f7a3fb2ba462cd7d6f1420b1d27618f7
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="using-urhosharp-in-xamarinforms"></a>Utilizzo di UrhoSharp in xamarin. Forms

## <a name="what-is-urhosharp"></a>Che cos'è UrhoSharp?

[UrhoSharp](~/graphics-games/urhosharp/index.md) è un potente motore 3D per gli sviluppatori di Xamarin e .NET. Il [Introduzione](~/graphics-games/urhosharp/introduction.md) fornisce ulteriori informazioni sulla libreria, UrhoSharp e [queste note](~/graphics-games/urhosharp/using.md) viene descritto come programmare le scene e azioni.

UrhoSharp può essere utilizzato per il rendering della grafica nelle applicazioni di xamarin. Forms.
Questo [esempio](https://github.com/xamarin/urho-samples/tree/master/FormsSample) viene illustrato come UrhoSharp può essere usato per costruire un grafico 3D interattivo:

![](urhosharp-images/ios-animation.gif "Grafico interattivo UrhoSharp 3D in iOS")
![](urhosharp-images/android-animation.gif "UrhoSharp grafico interattivo 3D in Android")

## <a name="adding-the-urhosharp-nuget-packages"></a>Aggiunta di pacchetti UrhoSharp Nuget

Prima di utilizzare UrhoSharp, gli sviluppatori devono aggiungere il pacchetto UrhoSharp Nuget per la propria soluzione. Questa guida si presuppone un progetto xamarin. Forms con un iOS, Android e .NET Standard progetto di libreria. Tutto il codice verrà scritto nel progetto di libreria .NET Standard; mentre UrhoSharp Nuget devono essere aggiunti troppo per i progetti iOS e Android.

Il pacchetto UrhoSharp.Forms Nuget contiene tutti gli oggetti necessari per creare oggetti UrhoSharp. Il pacchetto nuget UrhoSharp.Forms include il `UrhoSurface` (classe), che viene utilizzato per l'host UrhoSharp in xamarin. Forms.
Per iniziare, fare clic sui **pacchetti** cartella nel progetto di libreria .NET Standard e selezionare **Aggiungi pacchetti...** . Immettere il termine di ricerca **UrhoSharp.Forms**selezionare **UrhoSharp per xamarin. Forms**, quindi fare clic su **Aggiungi pacchetto**.

[![](urhosharp-images/add-package-sml.png "Finestra di dialogo pacchetti Aggiungi")](urhosharp-images/add-package.png#lightbox "pacchetti finestra di dialogo Aggiungi")

Il pacchetto UrhoSharp.Forms NuGet verrà aggiunto al progetto:

![](urhosharp-images/packages.png "Cartella di pacchetti")

Ripetere i passaggi precedenti per i progetti specifici della piattaforma (ad esempio iOS e Android).

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>Procedura dettagliata: Aggiunta di UrhoSharp a un'app xamarin. Forms

Questi passaggi viene descritto il codice nell'esempio di UrhoSharp xamarin. Forms:

1. [Creare una pagina di form di Xamarin](#1)
2. [Aggiungere il UrhoSurface](#2)
3. [Compilare un'applicazione Urho](#3)
4. [Aggiungere la classe di grafici di UrhoSurface](#4)
5. [L'interazione con UrhoSharp](#5)

Si noti che l'esempio utilizza le funzionalità di c# 6 e potrebbe non essere compilato in versioni precedenti di Visual Studio.

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1. Creare una pagina di form di Xamarin

Il codice seguente viene mostrata una pagina di xamarin. Forms `UrhoPage` prima di qualsiasi codice correlato Urho è stata aggiunta:

```csharp
public class UrhoPage : ContentPage
{
  Slider selectedBarSlider, rotationSlider;

  public UrhoPage()
  {
    // we'll add Urho later

    rotationSlider = new Slider(0, 500, 250);

    selectedBarSlider = new Slider(0, 5, 2.5);

    Title = " UrhoSharp + Xamarin.Forms";
    Content = new StackLayout {
      Padding = new Thickness (12, 12, 12, 40),
      VerticalOptions = LayoutOptions.FillAndExpand,
      Children = {
        rotationSlider,
        new Label { Text = "SELECTED VALUE:" },
        selectedBarSlider,
      }
    };
  }
```

<a name="2"/>

### <a name="2-add-the-urhosurface"></a>2. Aggiungere il UrhoSurface

UrhoSharp possono essere ospitati in un `ContentPage` Analogamente ad altri controlli di xamarin. Forms.
Il frammento di codice seguente viene illustrato un `UrhoSurface` aggiunti alla pagina di xamarin. Forms:

```csharp
using Urho;
using Urho.Forms;
...
public class UrhoPage : ContentPage
{
  UrhoSurface urhoSurface;

  public UrhoPage()
  {
    urhoSurface = new UrhoSurface();
    urhoSurface.VerticalOptions = LayoutOptions.FillAndExpand;
...
    Content = new StackLayout {
    Padding = new Thickness (12, 12, 12, 40),
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = {
      urhoSurface,  // added
      new Label { Text = "ROTATION:" },
      rotationSlider,
      new Label { Text = "SELECTED VALUE:" },
      selectedBarSlider,
    }
  };
```

<a name="3"/>

### <a name="3-build-a-urho-application"></a>3. Compilare un'applicazione Urho

Consultare la `Charts` classe per l'implementazione di grafica 3D Urho utilizzata in questo esempio. La struttura di base di codice è illustrata di seguito, si noti che la classe implementa `Urho.Application` che è diverso per il `Xamarin.Forms.Application` classe implementata in **app. cs**.

```csharp
using Urho;
using Urho.Actions;
using Urho.Gui;
using Urho.Shapes;

namespace FormsSample
{
    public class Charts : Urho.Application
    {
    public Charts (ApplicationOptions options = null) : base(options) { }
    protected override void Start ()
    {
      ...
    }
    protected override void OnUpdate(float timeStep)
    {
      ...
    }
```

Il [UrhoSharp documentazione](~/graphics-games/urhosharp/index.md) contiene ulteriori informazioni su come creare azioni e scene 3D.

<a name="4"/>

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4. Aggiungere la classe di grafici di UrhoSurface

Utilizzare il `UrhoSurface.Show<T>` un metodo generico per aggiungere l'applicazione Urho alla pagina di xamarin. Forms. Nel frammento di codice seguente viene illustrato il codice aggiuntivo necessario per creare la `Charts` classe:

```csharp
public class UrhoPage : ContentPage
{
  Charts urhoApp;
  ...
  protected override async void OnAppearing ()
  {
    urhoApp = await urhoSurface.Show<Charts> (new ApplicationOptions(assetsFolder: null)
      { Orientation = ApplicationOptions.OrientationType.Portrait });
  }
```

Nota: il `Show<T>` metodo è asincrono e deve essere chiamato con il `await` (parola chiave).

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5. L'interazione con UrhoSharp

Nell'esempio viene consentito barre del grafico essere selezionato e modificato. Il `Charts` classe espone il `Bars` e `SelectedBar` per abilitare questa interazione.

Ogni barra"" dispone di un gestore di evento di selezione aggiunto dopo il `Charts` classe è stato eseguito il rendering, scorrendo esposti `Bars` raccolta:

```csharp
protected override async void OnAppearing ()
{
  urhoApp = await urhoSurface.Show<Charts>(new ApplicationOptions(assetsFolder: null) { Orientation = ApplicationOptions.OrientationType.Portrait });
  foreach (var bar in urhoApp.Bars)
  {
    bar.Selected += OnBarSelection;
  }
}
```

Il gestore dell'evento viene utilizzato il valore di xamarin. Forms `Slider` controllo regolare l'altezza della barra degli strumenti specificata:

```csharp
private void OnBarSelection(Bar bar)
{
  //reset value
  selectedBarSlider.ValueChanged -= OnValuesSliderValueChanged;
  selectedBarSlider.Value = bar.Value;
  selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
}

void OnValuesSliderValueChanged(object sender, ValueChangedEventArgs e)
{  // C# 6
  if (urhoApp?.SelectedBar != null)
  {
    urhoApp.SelectedBar.Value = (float)e.NewValue;
  }
}
```

Infine, associare i due `Slider` controlli in modo che quando viene modificato il valore dell'area di disegno UrhoSharp è interessato. Il primo dispositivo di scorrimento Ruota l'immagine del grafico 3D e il secondo dispositivo di scorrimento regola l'altezza della barra selezionata.

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

Le animazioni di [superiore della pagina](#) Mostra l'esecuzione di esempio.

## <a name="summary"></a>Riepilogo

Questa pagina viene illustrato come utilizzare UrhoSharp per aggiungere una visualizzazione dei dati 3D a xamarin. Forms. Lettura di [UrhoSharp documentazione](~/graphics-games/urhosharp/index.md) per ulteriori informazioni su come creare le scene Urho che possono essere incluso in App xamarin. Forms usando il metodo illustrato in precedenza.


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di grafici (c# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
