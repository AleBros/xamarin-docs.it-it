---
title: Uso di UrhoSharp in xamarin. Forms
description: Questo articolo illustra come UrhoSharp consente di aggiungere elementi grafici 3D per un'applicazione xamarin. Forms per la visualizzazione avanzata.
ms.prod: xamarin
ms.assetid: 0646B98E-CC04-4537-9715-9F82338FD7FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/11/2016
ms.openlocfilehash: 60c647a9a09ebffa5a9d50c799c09cf0dbf2e4ac
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728010"
---
# <a name="using-urhosharp-in-xamarinforms"></a>Uso di UrhoSharp in xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/urho-samples/tree/master/FormsSample)

## <a name="what-is-urhosharp"></a>Che cos'è UrhoSharp?

[UrhoSharp](~/graphics-games/urhosharp/index.md) è un potente motore 3D per gli sviluppatori di Xamarin e .NET. Il [introduction](~/graphics-games/urhosharp/introduction.md) spiega in maggiore dettaglio la libreria di UrhoSharp, e [queste note](~/graphics-games/urhosharp/using.md) descrivono come programmare le scene e azioni.

UrhoSharp può essere utilizzato per eseguire il rendering della grafica nelle applicazioni xamarin. Forms.
Ciò [esempio](https://github.com/xamarin/urho-samples/tree/master/FormsSample) viene illustrato come usare UrhoSharp per costruire un grafico 3D interattivo:

![](urhosharp-images/ios-animation.gif "UrhoSharp 3D Interactive Chart on iOS")
![](urhosharp-images/android-animation.gif "UrhoSharp 3D Interactive Chart on Android")

## <a name="adding-the-urhosharp-nuget-packages"></a>Aggiunta dei pacchetti NuGet UrhoSharp

Prima di usare UrhoSharp, gli sviluppatori devono aggiungere il pacchetto NuGet UrhoSharp alla propria soluzione. Questa guida si presuppone che un progetto xamarin. Forms con un iOS, Android e .NET Standard progetto di libreria. Tutto il codice verrà scritto nel progetto di libreria .NET Standard; Tuttavia è necessario aggiungere UrhoSharp NuGet anche ai progetti iOS e Android.

Il pacchetto NuGet UrhoSharp. Forms contiene tutti gli oggetti necessari per creare oggetti UrhoSharp. Il pacchetto NuGet UrhoSharp. Forms include la classe `UrhoSurface`, che viene usata per ospitare UrhoSharp in Novell. Forms.
Per iniziare, fare clic con il pulsante destro del mouse sulla cartella **pacchetti** nel progetto libreria .NET standard e selezionare **Aggiungi pacchetti...** . Immettere il termine di ricerca **UrhoSharp. Forms**, selezionare **UrhoSharp per Novell. Forms**, quindi fare clic su **Aggiungi pacchetto**.

[![](urhosharp-images/add-package-sml.png "Add Packages Dialog")](urhosharp-images/add-package.png#lightbox "Add Packages Dialog")

Il pacchetto UrhoSharp.Forms NuGet verrà aggiunto al progetto:

![](urhosharp-images/packages.png "Packages Folder")

Ripetere i passaggi precedenti per progetti specifici della piattaforma (ad esempio iOS e Android).

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>Procedura dettagliata: Aggiunta di UrhoSharp in un'app xamarin. Forms

I passaggi seguenti descrivono il codice nell'esempio di UrhoSharp xamarin. Forms:

1. [Per creare una pagina di Xamarin Forms](#1)
2. [Aggiungere il UrhoSurface](#2)
3. [Compilare un'applicazione Urho](#3)
4. [Aggiungere la classe di grafici di UrhoSurface](#4)
5. [L'interazione con UrhoSharp](#5)

Si noti che l'esempio Usa C# 6 funzionalità e potrebbero non essere compilate in versioni precedenti di Visual Studio.

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1. creare una pagina Novell Forms

Il codice seguente illustra una pagina di xamarin. Forms `UrhoPage` prima che qualsiasi codice correlato all'Urho è stato aggiunto:

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

### <a name="2-add-the-urhosurface"></a>2. aggiungere il UrhoSurface

UrhoSharp possono essere ospitati in un `ContentPage` Analogamente ad altri controlli xamarin. Forms.
Il frammento di codice seguente mostra un `UrhoSurface` aggiunto alla pagina di xamarin. Forms:

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

### <a name="3-build-a-urho-application"></a>3. creare un'applicazione Urho

Fare riferimento al `Charts` classe per l'implementazione di grafica 3D Urho utilizzata in questo esempio. La struttura di base del codice è illustrata di seguito, si noti che la classe implementa `Urho.Application` che è diverso per il `Xamarin.Forms.Application` classe che viene implementato in **App.cs**.

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

Il [documentazione di UrhoSharp](~/graphics-games/urhosharp/index.md) contiene altre informazioni su come creare azioni e scene 3D.

<a name="4"/>

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4. aggiungere la classe Charts a UrhoSurface

Usare il `UrhoSurface.Show<T>` metodo generico per aggiungere l'applicazione Urho alla pagina di xamarin. Forms. Il frammento di codice seguente viene illustrato il codice aggiuntivo necessario per creare il `Charts` classe:

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

Nota: i `Show<T>` metodo è asincrono e deve essere chiamato con il `await` (parola chiave).

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5. interazione con UrhoSharp

L'esempio consente a barre del grafico essere selezionato e modificato. Il `Charts` classe espone il `Bars` e `SelectedBar` per abilitare questa interazione.

Ogni "bar" dispone di un gestore di evento di selezione aggiunto dopo il `Charts` classe è stato eseguito il rendering, mediante l'iterazione esposti `Bars` raccolta:

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

Il gestore eventi Usa il valore di xamarin. Forms `Slider` controllo per regolare l'altezza della barra degli strumenti specificata:

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

Infine, collegare i due `Slider` controlla in modo che quando il loro valore viene modificato l'area di disegno di UrhoSharp è interessato. Il primo dispositivo di scorrimento consente di ruotare l'immagine del grafico 3D e il dispositivo di scorrimento seconda regola l'altezza della barra selezionata.

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

Le animazioni con il [superiore della pagina](#what-is-urhosharp) Mostra l'esempio in esecuzione.

## <a name="summary"></a>Riepilogo

Questa pagina illustra come UrhoSharp può essere utilizzato per aggiungere la visualizzazione dei dati 3D a xamarin. Forms. Leggere il [documentazione di UrhoSharp](~/graphics-games/urhosharp/index.md) per altre informazioni su come creare scene Urho che possono essere inclusi nelle App xamarin. Forms usando il metodo illustrato in precedenza.

## <a name="related-links"></a>Collegamenti correlati

- [I grafici di esempio (C# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
