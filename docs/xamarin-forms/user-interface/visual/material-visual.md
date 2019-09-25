---
title: Oggetti visivi del materiale Novell. Forms
description: Il materiale visivo Novell. Forms può essere usato per creare applicazioni Novell. Forms che hanno un aspetto identico o in gran parte identico in iOS e Android.
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: b735541d51321231775b025745e68c54552697d3
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71198490"
---
# <a name="xamarinforms-material-visual"></a>Oggetti visivi del materiale Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

Il [progetto di materiale](https://material.io) è un sistema di progettazione dogmatico creato da Google, che prevede la dimensione, il colore, la spaziatura e altri aspetti del modo in cui le visualizzazioni e i layout dovrebbero apparire e comportarsi.

Novell. Forms Visual Material può essere usato per applicare le regole di progettazione del materiale alle applicazioni Novell. Forms, creando applicazioni che sembrano identiche o in gran parte identiche in iOS e Android. Quando l'oggetto visivo materiale è abilitato, le visualizzazioni supportate adottano la stessa progettazione multipiattaforma, creando un aspetto unificato. Questa operazione viene eseguita con renderer di materiali che applicano le regole di progettazione del materiale.

Il processo per l'abilitazione dell'oggetto visivo del materiale Novell. Forms nell'applicazione è:

1. Aggiungere il pacchetto NuGet [Novell. Forms. Visual. Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) ai progetti della piattaforma iOS e Android. Questo pacchetto NuGet fornisce renderer di progettazione di materiali ottimizzati in iOS e Android. In iOS il pacchetto fornisce la dipendenza transitiva a [Novell. iOS. MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents), che è un C# binding ai componenti materiali di Google [per iOS](https://material.io/develop/ios/). In Android, il pacchetto fornisce destinazioni di compilazione per garantire che il TargetFramework sia configurato correttamente.
1. Inizializzare i renderer di materiali in ogni progetto di piattaforma. Per altre informazioni, vedere [Initialize Material renderers](#initialize-material-renderers).
1. Utilizzare i renderer di materiale impostando la [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) `Material` proprietà su in tutte le pagine che devono adottare le regole di progettazione del materiale. Per altre informazioni, vedere [utilizzare renderer di materiali](#consume-material-renderers).
1. opzionale Personalizzare i renderer di materiali. Per altre informazioni, vedere [personalizzare i renderer di materiali](#customize-material-renderers).

> [!IMPORTANT]
> In Android, i renderer di materiali richiedono una versione minima di 5,0 (API 21) o successiva e un TargetFramework della versione 9,0 (API 28). Inoltre, il progetto della piattaforma richiede le librerie di supporto per Android 28.0.0 o versione successiva e il suo tema deve ereditare da un tema dei componenti materiali o continuare a ereditare da un tema di AppCompat. Per altre informazioni, vedere [Guida introduttiva per Android con i componenti di materiale](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

I renderer di materiali sono attualmente inclusi nel pacchetto NuGet [Novell. Forms. Visual. Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) per le visualizzazioni seguenti:

- [`Button`](xref:Xamarin.Forms.Button)
- `CheckBox`
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)

A livello funzionale, il renderer materiale non sono diversi per i renderer predefinito.

## <a name="initialize-material-renderers"></a>Inizializzare i renderer di materiali

Dopo l'installazione del pacchetto NuGet [Novell. Forms. Visual. Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) , i renderer di materiali devono essere inizializzati in ogni progetto di piattaforma.

In iOS questo dovrebbe verificarsi in **AppDelegate.cs** richiamando il `Xamarin.Forms.FormsMaterial.Init` metodo *dopo* il `Xamarin.Forms.Forms.Init` metodo:

```csharp
global::Xamarin.Forms.Forms.Init();
global::Xamarin.Forms.FormsMaterial.Init();
```

In Android questa operazione dovrebbe essere eseguita in **MainActivity.cs** richiamando `Xamarin.Forms.FormsMaterial.Init` il metodo *dopo* il `Xamarin.Forms.Forms.Init` metodo:

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
global::Xamarin.Forms.FormsMaterial.Init(this, savedInstanceState);
```

## <a name="consume-material-renderers"></a>Utilizzare renderer di materiali

Le applicazioni possono acconsentire esplicitamente all'uso dei renderer di [`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual) materiale impostando la proprietà su una pagina, un layout `Material`o una vista per:

```xaml
<ContentPage Visual="Material"
             ...>
    ...
</ContentPage>
```

Il codice C# equivalente è il seguente:

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

La [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) proprietà può essere impostata su qualsiasi tipo che implementa `IVisual`, con la [`VisualMarker`](xref:Xamarin.Forms.VisualMarker) classe che fornisce le `IVisual` proprietà seguenti:

- `Default`: indica che è necessario eseguire il rendering della visualizzazione utilizzando il renderer predefinito.
- `MatchParent`: indica che la visualizzazione deve usare lo stesso renderer del padre diretto.
- `Material`: indica che la visualizzazione deve essere sottoposta a rendering utilizzando un renderer di materiali.

> [!IMPORTANT]
> La [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) proprietà è definita [`VisualElement`](xref:Xamarin.Forms.VisualElement) nella classe, con le visualizzazioni che ereditano `Visual` il valore della proprietà dagli elementi padre. Pertanto, impostando `Visual` la proprietà su [`ContentPage`](xref:Xamarin.Forms.ContentPage) un oggetto si garantisce che tutte le visualizzazioni supportate nella pagina utilizzeranno tale oggetto visivo. Inoltre, il `Visual` proprietà può essere sottoposto a override in una vista.

Gli screenshot seguenti mostrano un'interfaccia utente di cui viene eseguito il rendering usando i renderer predefiniti:

[![Screenshot dei renderer predefiniti in iOS e Android](material-visual-images/default-renderers.png "Visualizzazioni con renderer predefiniti")](material-visual-images/default-renderers-large.png#lightbox)

Le schermate seguenti illustrano la stessa interfaccia utente viene eseguito il rendering tramite i renderer di materiale:

[![Screenshot dei renderer di materiali, in iOS e Android](material-visual-images/material-renderers.png "Visualizzazioni con renderer di materiali")](material-visual-images/material-renderers-large.png#lightbox)

Le principali differenze visibili tra i renderer predefiniti e i renderer di materiali, illustrati di seguito, sono che i renderer del materiale [`Button`](xref:Xamarin.Forms.Button) capitalizzano il testo e arrotondano [`Frame`](xref:Xamarin.Forms.Frame) gli angoli dei bordi. Tuttavia, i renderer di materiali utilizzano controlli nativi e, di conseguenza, potrebbero essere presenti differenze nell'interfaccia utente tra le piattaforme per aree quali tipi di carattere, ombreggiature, colori ed elevazione.

> [!NOTE]
> I componenti di progettazione del materiale si attengono strettamente alle linee guida di Google. Di conseguenza, i renderer di progettazione materiali sono distorti rispetto al dimensionamento e al comportamento. Quando è necessario un maggiore controllo degli stili o del comportamento, è comunque possibile creare un proprio [effetto](~/xamarin-forms/app-fundamentals/effects/index.md), un [comportamento](~/xamarin-forms/app-fundamentals/behaviors/index.md)o un [renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) per ottenere i dettagli necessari.

## <a name="customize-material-renderers"></a>Personalizzare i renderer di materiali

I renderer di materiale possono facoltativamente essere personalizzati, proprio come i renderer predefiniti, tramite le classi base seguenti:

- `MaterialButtonRenderer`
- `MaterialCheckBoxRenderer`
- `MaterialEntryRenderer`
- `MaterialFrameRenderer`
- `MaterialProgressBarRenderer`
- `MaterialDatePickerRenderer`
- `MaterialTimePickerRenderer`
- `MaterialPickerRenderer`
- `MaterialActivityIndicatorRenderer`
- `MaterialEditorRenderer`
- `MaterialSliderRenderer`
- `MaterialStepperRenderer`

Il codice seguente illustra un esempio di personalizzazione `MaterialProgressBarRenderer` della classe:

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ProgressBar), typeof(CustomMaterialProgressBarRenderer), new[] { typeof(VisualMarker.MaterialVisual) })]
namespace MyApp.Android
{
    public class CustomMaterialProgressBarRenderer : MaterialProgressBarRenderer
    {
        ...
    }
}
```

`ExportRendererAttribute` In questo esempio, specifica che la `CustomMaterialProgressBarRenderer` classe verrà utilizzata per eseguire il rendering della [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) visualizzazione, con il `IVisual` tipo registrato come terzo argomento.

> [!NOTE]
> Un renderer che specifica un `IVisual` tipo, come parte del relativo `ExportRendererAttribute`, verrà usato per il rendering delle visualizzazioni di cui è stato scelto il rendering, anziché il renderer predefinito. In fase di selezione del renderer, il `Visual` proprietà della vista vengono ispezionate e inclusi nel processo di selezione del renderer.

Per ulteriori informazioni sui renderer personalizzati, vedere [renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [Materiale visivo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Creare un renderer Visual Novell. Forms](create.md)
- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
