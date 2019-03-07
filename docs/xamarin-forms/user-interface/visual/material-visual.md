---
title: Oggetto visivo materiali di xamarin. Forms
description: Oggetto visivo materiali di xamarin. Forms è utilizzabile per creare applicazioni xamarin. Forms identica, o in gran parte identici, in iOS e Android.
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2019
ms.openlocfilehash: 46ffe29f898e2f7bd8fcbe759f5a2f54e3dd60e1
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557503"
---
# <a name="xamarinforms-material-visual"></a>Oggetto visivo materiali di xamarin. Forms

Oggetto visivo materiali di xamarin. Forms è utilizzabile per creare applicazioni xamarin. Forms identica, o in gran parte identici, in iOS e Android. Questo risultato viene ottenuto tramite altri renderer che implementano un aspetto materiale, con le applicazioni di consenso esplicito all'aspetto attraverso il `Visual` proprietà:

```xaml
<ContentPage ...
             Visual="Material">
    ...
</ContentPage>
```

Il codice c# equivalente è:

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

> [!IMPORTANT]
> Il `Visual` proprietà definita nel `VisualElement` (classe), con le visualizzazioni che eredita il `Visual` valore della proprietà dai relativi oggetti padre. Pertanto, l'impostazione di `Visual` proprietà su un `ContentPage` assicura che eventuali visualizzazioni supportate nella pagina userà tale aspetto visivo. Inoltre, il `Visual` proprietà può essere sottoposto a override in una vista.

Renderer Material sono attualmente incluse per le viste seguenti in iOS e Android:

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

In iOS e Android, il progetto di piattaforma deve avere il [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/ https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) pacchetto NuGet installato. Dopo aver installato il pacchetto NuGet, è necessario il codice di inizializzazione in ogni progetto della piattaforma *dopo aver* il `Xamarin.Forms.Forms.Init` chiamata al metodo. Per iOS, usare il codice seguente:

```csharp
global::Xamarin.Forms.Forms.Init();
FormsMaterial.Init();
```

In Android, è necessario passare gli stessi parametri che vengono passati al `Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.Init(this, bundle);
FormsMaterial.Init(this, bundle);
```

> [!IMPORTANT]
> In Android, materiale funziona solo con TargetFramework 9.0 (28 API). Inoltre, il progetto di piattaforma deve usare v28 delle librerie di supporto e il tema deve ereditare da un tema Material componenti o continuare a ereditare da un tema AppCompat. Per altre informazioni, vedere [Guida introduttiva per Android con i componenti di materiale](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

Le schermate seguenti illustrano un'interfaccia utente che include le quattro viste per le quali materiali renderer esistano, ma viene eseguito il rendering tramite i renderer predefinito:

[![Screenshot del renderer predefinito, in iOS e Android](material-visual-images/default-renderers.png "le viste usando il renderer predefinito")](material-visual-images/default-renderers-large.png#lightbox)

Le schermate seguenti illustrano la stessa interfaccia utente viene eseguito il rendering tramite i renderer di materiale:

[![Screenshot del renderer di materiale, su iOS e Android](material-visual-images/material-renderers.png "le viste usando il renderer di materiale")](material-visual-images/material-renderers-large.png#lightbox)

> [!NOTE]
> Con i renderer materiali i controlli di rendering rimangono controlli nativi e di conseguenza è comunque presente le differenze di interfaccia utente tra piattaforme per le aree, ad esempio i tipi di carattere, ombreggiature, colori e l'elevazione dei privilegi.

## <a name="material-renderers"></a>Renderer materiali

Renderer Material personalizzabili, proprio come i renderer predefinito, utilizzando le classi di base seguenti:

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

## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
