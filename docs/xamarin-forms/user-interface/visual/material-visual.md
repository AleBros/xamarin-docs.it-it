---
title: Oggetti visivi del materiale Novell. Forms
description: Novell. Forms Visual Material può essere usato per creare applicazioni Novell. Forms che sembrano sostanzialmente identiche in iOS e Android.
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/25/2019
ms.openlocfilehash: 4a960075ebea0fae4cde8d79bdc38641a0b1dc8a
ms.sourcegitcommit: 1fdc6215f00863735bf0d2bf0afcaa45b714b453
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465524"
---
# <a name="xamarinforms-material-visual"></a>Oggetti visivi del materiale Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

Il [progetto di materiale](https://material.io) è un sistema di progettazione dogmatico creato da Google, che prevede la dimensione, il colore, la spaziatura e altri aspetti del modo in cui le visualizzazioni e i layout dovrebbero apparire e comportarsi.

Novell. Forms Visual Material può essere usato per applicare le regole di progettazione del materiale alle applicazioni Novell. Forms, creando applicazioni che sembrano sostanzialmente identiche in iOS e Android. Quando l'oggetto visivo materiale è abilitato, le visualizzazioni supportate adottano la stessa progettazione multipiattaforma, creando un aspetto unificato.

[schermate visive materiali ![](material-visual-images/material-visual-cropped.png)](material-visual-images/material-visual.png#lightbox)

Il processo per l'abilitazione dell'oggetto visivo del materiale Novell. Forms nell'applicazione è:

1. Aggiungere il pacchetto NuGet [Novell. Forms. Visual. Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) ai progetti della piattaforma iOS e Android. Questo pacchetto NuGet fornisce renderer di progettazione di materiali ottimizzati in iOS e Android. In iOS il pacchetto fornisce la dipendenza transitiva a [Novell. iOS. MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents), che è un C# binding ai componenti materiali di Google [per iOS](https://material.io/develop/ios/). In Android, il pacchetto fornisce destinazioni di compilazione per garantire che il TargetFramework sia configurato correttamente.
1. Inizializzare l'oggetti visivi materiali in ogni progetto di piattaforma. Per altre informazioni, vedere [Initialize Material Visual](#initialize-material-visual).
1. Creare controlli visivi materiali impostando la proprietà [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) su `Material` in qualsiasi pagina che debba adottare le regole di progettazione del materiale. Per altre informazioni, vedere [utilizzare renderer di materiali](#apply-material-visual).
1. opzionale Personalizzare i controlli del materiale. Per altre informazioni, vedere [Customize Material Controls](#customize-material-visual).

> [!IMPORTANT]
> In Android, l'oggetto visivo materiale richiede una versione minima di 5,0 (API 21) o successiva e un TargetFramework della versione 9,0 (API 28). Inoltre, il progetto della piattaforma richiede le librerie di supporto per Android 28.0.0 o versione successiva e il suo tema deve ereditare da un tema dei componenti materiali o continuare a ereditare da un tema di AppCompat. Per altre informazioni, vedere [Introduzione ai componenti materiali per Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

Material Visual supporta attualmente i controlli seguenti:

- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Button`](xref:Xamarin.Forms.Button)
- `CheckBox`
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

I controlli Material sono realizzati dai renderer di materiali che applicano le regole di progettazione del materiale. Dal punto di vista funzionale, i renderer di materiali non sono diversi dai renderer predefiniti. Per altre informazioni, vedere [Customize Material Visual](#customize-material-visual).

## <a name="initialize-material-visual"></a>Inizializza oggetti visivi materiali

Dopo l'installazione del pacchetto NuGet [Novell. Forms. Visual. Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) , i renderer di materiali devono essere inizializzati in ogni progetto di piattaforma.

In iOS questo dovrebbe verificarsi in **AppDelegate.cs** richiamando il metodo `Xamarin.Forms.FormsMaterial.Init` *dopo* il metodo `Xamarin.Forms.Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.Init();
global::Xamarin.Forms.FormsMaterial.Init();
```

In Android questa operazione dovrebbe essere eseguita in **MainActivity.cs** richiamando il metodo `Xamarin.Forms.FormsMaterial.Init` *dopo* il metodo `Xamarin.Forms.Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
global::Xamarin.Forms.FormsMaterial.Init(this, savedInstanceState);
```

## <a name="apply-material-visual"></a>Applica oggetti visivi materiali

Le applicazioni possono abilitare l'oggetto visivo materiale impostando la proprietà [`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual) in una pagina, un layout o una vista per `Material`:

```xaml
<ContentPage Visual="Material"
             ...>
    ...
</ContentPage>
```

Il codice C# equivalente è:

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

Impostando la proprietà `VisualElement.Visual` su `Material` si indica all'applicazione di usare i renderer visivi materiali anziché i renderer predefiniti. La proprietà [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) può essere impostata su qualsiasi tipo che implementa `IVisual`, con la classe [`VisualMarker`](xref:Xamarin.Forms.VisualMarker) che fornisce le proprietà `IVisual` seguenti:

- `Default`: indica che è necessario eseguire il rendering della visualizzazione utilizzando il renderer predefinito.
- `MatchParent`: indica che la visualizzazione deve usare lo stesso renderer dell'elemento padre diretto.
- `Material`: indica che è necessario eseguire il rendering della visualizzazione utilizzando un renderer di materiali.

> [!IMPORTANT]
> La proprietà [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) è definita nella classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) , con le visualizzazioni che ereditano il valore della proprietà `Visual` dagli elementi padre. Pertanto, l'impostazione della proprietà `Visual` in un [`ContentPage`](xref:Xamarin.Forms.ContentPage) garantisce che tutte le visualizzazioni supportate nella pagina utilizzeranno tale oggetto visivo. Inoltre, la proprietà `Visual` può essere sottoposta a override in una vista.

Gli screenshot seguenti mostrano un'interfaccia utente di cui viene eseguito il rendering usando i renderer predefiniti:

[![Screenshot dei renderer predefiniti in iOS e Android](material-visual-images/default-renderers.png "Visualizzazioni con renderer predefiniti")](material-visual-images/default-renderers-large.png#lightbox)

Gli screenshot seguenti mostrano la stessa interfaccia utente di cui è stato eseguito il rendering usando i renderer di materiale:

[![Screenshot dei renderer di materiali, in iOS e Android](material-visual-images/material-renderers.png "Visualizzazioni con renderer di materiali")](material-visual-images/material-renderers-large.png#lightbox)

Le principali differenze visibili tra i renderer predefiniti e i renderer di materiali, illustrati di seguito, sono che i renderer di materiali capitalizzano [`Button`](xref:Xamarin.Forms.Button) testo e arrotondano gli angoli dei bordi [`Frame`](xref:Xamarin.Forms.Frame) . Tuttavia, i renderer di materiali utilizzano controlli nativi e, di conseguenza, potrebbero essere presenti differenze nell'interfaccia utente tra le piattaforme per aree quali tipi di carattere, ombreggiature, colori ed elevazione.

> [!NOTE]
> I componenti di progettazione del materiale si attengono strettamente alle linee guida di Google. Di conseguenza, i renderer di progettazione materiali sono distorti rispetto al dimensionamento e al comportamento. Quando è necessario un maggiore controllo degli stili o del comportamento, è comunque possibile creare un proprio [effetto](~/xamarin-forms/app-fundamentals/effects/index.md), un [comportamento](~/xamarin-forms/app-fundamentals/behaviors/index.md)o un [renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) per ottenere i dettagli necessari.

## <a name="customize-material-visual"></a>Personalizza oggetti visivi materiali

Il pacchetto Visual NuGet Material è una raccolta di renderer che realizzano i controlli Novell. Forms. La personalizzazione dei controlli visivi materiali è identica alla personalizzazione dei controlli predefiniti.

Gli effetti sono la tecnica consigliata quando l'obiettivo è personalizzare un controllo esistente. Se è presente un renderer visuale Material, è meno lavoro personalizzare il controllo con un effetto rispetto a quello della sottoclasse del renderer. Per ulteriori informazioni sugli effetti, vedere [Novell. Forms Effects](~/xamarin-forms/app-fundamentals/effects/index.md).

I renderer personalizzati sono la tecnica consigliata quando un renderer di materiali non esiste. Con l'oggetto visivo Material sono incluse le classi renderer seguenti:

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

La sottoclasse di un renderer di materiali è quasi identica ai renderer non di materiale. Tuttavia, quando si esporta un renderer che esegue una sottoclasse di un renderer di materiali, è necessario fornire un terzo argomento all'attributo `ExportRenderer` che specifica il tipo di `VisualMarker.MaterialVisual`:

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ProgressBar), typeof(CustomMaterialProgressBarRenderer), new[] { typeof(VisualMarker.MaterialVisual) })]
namespace MyApp.Android
{
    public class CustomMaterialProgressBarRenderer : MaterialProgressBarRenderer
    {
        //...
    }
}
```

In questo esempio, il `ExportRendererAttribute` specifica che la classe `CustomMaterialProgressBarRenderer` verrà utilizzata per eseguire il rendering della visualizzazione [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) , con il tipo di `IVisual` registrato come terzo argomento.

> [!NOTE]
> Un renderer che specifica un tipo di `IVisual`, come parte del relativo `ExportRendererAttribute`, verrà utilizzato per il rendering delle visualizzazioni di cui è stato scelto il rendering, anziché il renderer predefinito. Al momento della selezione del renderer, la proprietà `Visual` della vista viene controllata e inclusa nel processo di selezione del renderer.

Per ulteriori informazioni sui renderer personalizzati, vedere [renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [Materiale visivo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Creare un renderer Visual Novell. Forms](create.md)
- [Effetti Novell. Forms](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
