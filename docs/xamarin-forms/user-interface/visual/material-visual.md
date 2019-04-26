---
title: Oggetto visivo materiali di xamarin. Forms
description: Oggetto visivo materiali di xamarin. Forms è utilizzabile per creare applicazioni xamarin. Forms identica, o in gran parte identici, in iOS e Android.
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: cf6ab8266b0798ccbf29078313bbc7454125a1af
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026792"
---
# <a name="xamarinforms-material-visual"></a>Oggetto visivo materiali di xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)

[Progettazione dei materiali](https://material.io) è un sistema solido progettazione creato da Google, che stabilisce le dimensioni, colore, spaziatura e altri aspetti del modo in cui le visualizzazioni e layout deve apparire e comportarsi.

Oggetto visivo materiali di xamarin. Forms è utilizzabile per applicare le regole di Material Design per le applicazioni xamarin. Forms, la creazione di applicazioni con un aspetto identica, o in gran parte identici, in iOS e Android. Quando Visual materiale è abilitata, le visualizzazioni supportate adottano la stessa progettazione multipiattaforma, creazione di un aspetto unificato. Questo risultato viene ottenuto con i renderer di materiale, che si applicano le regole di Material Design.

Il processo per l'abilitazione di xamarin. Forms materiale Visual nell'applicazione è:

1. Aggiungere il [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) pacchetto NuGet per i progetti per la piattaforma Android e iOS. Questo pacchetto NuGet fornisce ottimizzati i renderer di Material Design in iOS e Android. In iOS, il pacchetto fornisce la dipendenza transitiva al [Xamarin.iOS.MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents), ovvero un C# associazione a Google [componenti di materiale per iOS](https://material.io/develop/ios/). In Android, il pacchetto fornisce le destinazioni di compilazione per assicurarsi che il TargetFramework sia correttamente configurato.
1. Inizializzare i renderer di materiale in ogni progetto della piattaforma. Per altre informazioni, vedere [inizializzare renderer material](#initialize-material-renderers).
1. Utilizzare i renderer di materiale impostando il [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) proprietà `Material` su tutte le pagine che è consigliabile adottare le regole di Material Design. Per altre informazioni, vedere [utilizzano i renderer materiali](#consume-material-renderers).
1. [facoltativo] Personalizzare i renderer di materiale. Per altre informazioni, vedere [personalizzare renderer material](#customize-material-renderers).

> [!IMPORTANT]
> In Android, il renderer material richiedono almeno la versione 5.0 (API 21) o versione successiva e un TargetFramework della versione 9.0 (28 API). Inoltre, il progetto di piattaforma richiede librerie di supporto Android 28.0.0 o superiore, e il tema deve ereditare da un tema Material componenti oppure continuare a ereditare da un tema AppCompat. Per altre informazioni, vedere [Guida introduttiva per Android con i componenti di materiale](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

I renderer materiali attualmente inclusi nel [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) pacchetto NuGet per le viste seguenti:

- [`Button`](xref:Xamarin.Forms.Button)
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

## <a name="initialize-material-renderers"></a>Inizializzare i renderer materiali

Dopo aver installato il [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) di pacchetti NuGet, il materiale renderer devono essere inizializzati in ogni progetto della piattaforma.

In iOS, questo deve verificarsi nel **AppDelegate.cs** richiamando il `FormsMaterial.Init` metodo *dopo* il `Xamarin.Forms.Forms.Init` (metodo):

```csharp
global::Xamarin.Forms.Forms.Init();
FormsMaterial.Init();
```

In Android, questo deve verificarsi **MainActivity.cs** richiamando il `FormsMaterial.Init` metodo *dopo* il `Xamarin.Forms.Forms.Init` metodo:

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
FormsMaterial.Init(this, savedInstanceState);
```

## <a name="consume-material-renderers"></a>Usare renderer material

Le applicazioni possono acconsentire esplicitamente tramite i renderer di materiale impostando il [ `VisualElement.Visual` ](xref:Xamarin.Forms.VisualElement.Visual) proprietà in una pagina, layout o una visualizzazione, per `Material`:

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

Il [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) proprietà può essere impostata su qualsiasi tipo che implementa `IVisual`, con il [ `VisualMarker` ](xref:Xamarin.Forms.VisualMarker) classe che fornisce quanto segue `IVisual` proprietà:

- `Default` : indica che deve eseguire il rendering nella visualizzazione utilizzando il renderer predefinito.
- `MatchParent` : indica che la visualizzazione deve utilizzare il renderer stesso come elemento padre diretto.
- `Material` : indica che deve eseguire il rendering nella visualizzazione usando un renderer del materiale.

> [!IMPORTANT]
> Il [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) proprietà definita nel [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) (classe), con le visualizzazioni che eredita il `Visual` valore della proprietà dai relativi oggetti padre. Pertanto, l'impostazione di `Visual` proprietà in un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) assicura che eventuali visualizzazioni supportate nella pagina utilizzerà tale oggetto visivo. Inoltre, il `Visual` proprietà può essere sottoposto a override in una vista.

Le schermate seguenti illustrano un'interfaccia utente viene eseguito il rendering tramite i renderer predefinito:

[![Screenshot del renderer predefinito, in iOS e Android](material-visual-images/default-renderers.png "le viste usando il renderer predefinito")](material-visual-images/default-renderers-large.png#lightbox)

Le schermate seguenti illustrano la stessa interfaccia utente viene eseguito il rendering tramite i renderer di materiale:

[![Screenshot del renderer di materiale, su iOS e Android](material-visual-images/material-renderers.png "le viste usando il renderer di materiale")](material-visual-images/material-renderers-large.png#lightbox)

Sono le principali differenze visibili tra i renderer predefinito e material renderer, riportati di seguito, che converte in maiuscolo i renderer di material [ `Button` ](xref:Xamarin.Forms.Button) testo e arrotondare gli angoli del [ `Frame` ](xref:Xamarin.Forms.Frame)bordi. Tuttavia, renderer material usare controlli nativi e pertanto potrebbero ancora essere differenze di interfaccia utente tra piattaforme per le aree, ad esempio i tipi di carattere, ombreggiature, colori e l'elevazione dei privilegi.

> [!NOTE]
> Componenti di progettazione materiale rispettano con precisione le linee guida di Google. I renderer di Material Design di conseguenza, prevalgono il ridimensionamento e il comportamento. Quando si richiedono un maggiore controllo di stili o il comportamento, è comunque possibile creare il proprio [effetto](~/xamarin-forms/app-fundamentals/effects/index.md), [comportamento](~/xamarin-forms/app-fundamentals/behaviors/index.md), o [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) per ottenere i dettagli necessari.

## <a name="customize-material-renderers"></a>Personalizzare i renderer materiali

Renderer di materiale, facoltativamente, personalizzabili, proprio come i renderer di impostazione predefinita, tramite le classi di base seguenti:

- `MaterialButtonRenderer`
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

Il codice seguente illustra un esempio di personalizzazione di `MaterialProgressBarRenderer` classe:

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

In questo esempio, il `ExportRendererAttribute` specifica che il `CustomMaterialProgressBarRenderer` classe viene usata per eseguire il rendering le [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar) visualizzazione, con la `IVisual` tipo registrato come terzo argomento.

> [!NOTE]
> Un renderer che specifica un `IVisual` tipo, come parte della relativa `ExportRendererAttribute`, verrà usato per eseguire il rendering scelto in viste, anziché il renderer predefinito. In fase di selezione del renderer, il `Visual` proprietà della vista vengono ispezionate e inclusi nel processo di selezione del renderer.

Per altre informazioni sui renderer personalizzati, vedere [renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [Oggetto visivo Material (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)
- [Creare un renderer oggetti visivi di xamarin. Forms](create.md)
- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
