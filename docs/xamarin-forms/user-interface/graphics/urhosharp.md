---
title: Utilizzo di UrhoSharp inXamarin.Forms
description: Questo articolo illustra come usare UrhoSharp per aggiungere grafica 3D a un' Xamarin.Forms applicazione per la visualizzazione avanzata.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c0eed1a451d62025562ac5fff4f12be96f0bf53
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137657"
---
# <a name="using-urhosharp-in-xamarinforms"></a>Utilizzo di UrhoSharp inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/urho-samples/tree/master/FormsSample)

## <a name="what-is-urhosharp"></a>Che cos'è UrhoSharp?

[UrhoSharp](~/graphics-games/urhosharp/index.md) è un potente motore 3D per gli sviluppatori Novell e .NET. L' [Introduzione](~/graphics-games/urhosharp/introduction.md) fornisce ulteriori informazioni sulla libreria UrhoSharp. [queste note](~/graphics-games/urhosharp/using.md) descrivono come programmare scene e azioni.

UrhoSharp può essere usato per eseguire il rendering della grafica nelle Xamarin.Forms applicazioni.
Questo [esempio](https://github.com/xamarin/urho-samples/tree/master/FormsSample) illustra come usare UrhoSharp per costruire un grafico 3D interattivo:

![](urhosharp-images/ios-animation.gif "UrhoSharp 3D Interactive Chart on iOS")
![](urhosharp-images/android-animation.gif "UrhoSharp 3D Interactive Chart on Android")

## <a name="adding-the-urhosharp-nuget-packages"></a>Aggiunta dei pacchetti NuGet UrhoSharp

Prima di usare UrhoSharp, gli sviluppatori devono aggiungere il pacchetto NuGet UrhoSharp alla propria soluzione. Questa guida presuppone un Xamarin.Forms progetto con un progetto di libreria iOS, Android e .NET standard. Tutto il codice verrà scritto nel progetto di libreria .NET Standard; Tuttavia è necessario aggiungere UrhoSharp NuGet anche ai progetti iOS e Android.

Il pacchetto NuGet UrhoSharp. Forms contiene tutti gli oggetti necessari per creare oggetti UrhoSharp. Il pacchetto NuGet UrhoSharp. Forms include la `UrhoSurface` classe, usata per ospitare UrhoSharp in Xamarin.Forms .
Per iniziare, fare clic con il pulsante destro del mouse sulla cartella **pacchetti** nel progetto libreria .NET standard e selezionare **Aggiungi pacchetti...**. Immettere il termine di ricerca **UrhoSharp. Forms**, selezionare **UrhoSharp per Xamarin.Forms **e quindi fare clic su **Aggiungi pacchetto**.

[![](urhosharp-images/add-package-sml.png "Add Packages Dialog")](urhosharp-images/add-package.png#lightbox "Add Packages Dialog")

Il pacchetto NuGet UrhoSharp. Forms verrà aggiunto al progetto:

![](urhosharp-images/packages.png "Packages Folder")

Ripetere i passaggi precedenti per i progetti specifici della piattaforma (ad esempio iOS e Android).

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>Procedura dettagliata: aggiunta di UrhoSharp a un' Xamarin.Forms app

Questi passaggi descrivono il codice nell' Xamarin.Forms esempio UrhoSharp:

1. [Crea una pagina form Novell](#1)
2. [Aggiungere UrhoSurface](#2)
3. [Creare un'applicazione Urho](#3)
4. [Aggiungere la classe Charts a UrhoSurface](#4)
5. [Interazione con UrhoSharp](#5)

Si noti che nell'esempio vengono utilizzate le funzionalità di C# 6 e che potrebbero non essere compilate in versioni precedenti di Visual Studio.

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1. creare una pagina Novell Forms

Il codice seguente mostra una Xamarin.Forms pagina `UrhoPage` prima dell'aggiunta di qualsiasi codice correlato a Urho:

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

UrhoSharp può essere ospitato in un `ContentPage` come altri Xamarin.Forms controlli.
Il frammento di codice seguente mostra un `UrhoSurface` aggiunto alla Xamarin.Forms pagina:

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

Vedere la `Charts` classe per l'implementazione della grafica 3D di Urho usata in questo esempio. Il contorno di codice di base è illustrato di seguito. si noti che la classe implementa `Urho.Application` che è diversa dalla `Xamarin.Forms.Application` classe implementata in **app.cs**.

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

La [documentazione di UrhoSharp](~/graphics-games/urhosharp/index.md) contiene altre informazioni su come creare scene e azioni 3D.

<a name="4"/>

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4. aggiungere la classe Charts a UrhoSurface

Usare il `UrhoSurface.Show<T>` metodo generico per aggiungere l'applicazione Urho alla Xamarin.Forms pagina. Il frammento di codice seguente mostra il codice aggiuntivo necessario per creare la `Charts` classe:

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

Nota: il `Show<T>` metodo è asincrono e deve essere chiamato con la `await` parola chiave.

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5. interazione con UrhoSharp

L'esempio consente di selezionare e modificare le barre del grafico. La `Charts` classe espone `Bars` e `SelectedBar` per abilitare questa interazione.

Per ogni "barra" è stato aggiunto un gestore eventi di selezione dopo che `Charts` è stato eseguito il rendering della classe, scorrendo la `Bars` raccolta esposta:

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

Il gestore eventi usa il valore del Xamarin.Forms `Slider` controllo per regolare l'altezza della barra specificata:

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

Infine, collegare i due `Slider` controlli in modo che, quando cambiano i valori, l'area di disegno UrhoSharp sia interessata. Il primo dispositivo di scorrimento ruota l'immagine del grafico 3D e il secondo dispositivo di scorrimento regola l'altezza della barra selezionata.

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

Le animazioni nella [parte superiore della pagina](#what-is-urhosharp) mostrano l'esempio in esecuzione.

## <a name="summary"></a>Riepilogo

Questa pagina Mostra come è possibile usare UrhoSharp per aggiungere la visualizzazione dei dati 3D a Xamarin.Forms . Leggere la [documentazione di UrhoSharp](~/graphics-games/urhosharp/index.md) per altre informazioni su come creare scenari Urho che possono essere inclusi nelle Xamarin.Forms app usando il metodo illustrato in precedenza.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di grafici (C# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
