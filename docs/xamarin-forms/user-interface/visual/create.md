---
title: Creare un renderer oggetti visivi di xamarin. Forms
description: Creare oggetti visivi di xamarin. Forms in modo selettivo da applicare agli oggetti VisualElement, senza la necessità di visualizzazioni di xamarin. Forms sottoclasse.
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: a11c2045fa6119d0689834c35794bc8913c80bd6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023729"
---
# <a name="create-a-xamarinforms-visual-renderer"></a>Creare un renderer oggetti visivi di xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)

Oggetto visivo xamarin. Forms consente renderer essere create e applicate selettivamente alle [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) oggetti, senza la necessità di visualizzazioni di xamarin. Forms sottoclasse. Un renderer che specifica un `IVisual` tipo, come parte della relativa `ExportRendererAttribute`, verrà usato per eseguire il rendering scelto in viste, anziché il renderer predefinito. In fase di selezione del renderer, il `Visual` proprietà della vista vengono ispezionate e inclusi nel processo di selezione del renderer.

> [!IMPORTANT]
> Attualmente le [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) proprietà non può essere modificata dopo la visualizzazione è stato eseguito il rendering, ma verrà modificato in una versione futura.

Il processo di creazione e l'utilizzo di un renderer oggetti visivi di xamarin. Forms è:

1. Creare i renderer di piattaforma per la visualizzazione desiderata. Per altre informazioni, vedere [creare renderer](#create-platform-renderers).
1. Creare un tipo che deriva da `IVisual`. Per altre informazioni, vedere [creare un tipo IVisual](#create-an-ivisual-type).
1. Registrare il `IVisual` tipo come parte del `ExportRendererAttribute` che decora il renderer. Per altre informazioni, vedere [registrare il tipo IVisual](#register-the-ivisual-type).
1. Utilizzare il renderer Visual impostando il [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) proprietà nella vista per la `IVisual` nome. Per altre informazioni, vedere [utilizzare il renderer Visual](#consume-the-visual-renderer).
1. [facoltativo] Registrare un nome per il `IVisual` tipo. Per altre informazioni, vedere [registrare un nome per il tipo IVisual](#register-a-name-for-the-ivisual-type).

## <a name="create-platform-renderers"></a>Creare i renderer di piattaforma

Per informazioni sulla creazione di una classe di rendering, vedere [renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Tuttavia, si noti che un renderer oggetti visivi di xamarin. Forms viene applicato a una visualizzazione senza la necessità per creare una sottoclasse della visualizzazione.

Le classi renderer descritte in questo argomento implementano una classe personalizzata [ `Button` ](xref:Xamarin.Forms.Button) che visualizza il testo con un'ombreggiatura.

### <a name="ios"></a>iOS

Esempio di codice seguente illustra il renderer di pulsante per iOS:

```csharp
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.TitleShadowOffset = new CoreGraphics.CGSize(1, 1);
            Control.SetTitleShadowColor(Color.Black.ToUIColor(), UIKit.UIControlState.Normal);
        }
    }
}
```

### <a name="android"></a>Android

Esempio di codice seguente illustra il renderer di pulsante per Android:

```csharp
public class CustomButtonRenderer : Xamarin.Forms.Platform.Android.AppCompat.ButtonRenderer
{
    public CustomButtonRenderer(Context context) : base(context)
    {
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.SetShadowLayer(5, 3, 3, Color.Black.ToAndroid());
        }
    }
}
```

## <a name="create-an-ivisual-type"></a>Creare un tipo IVisual

Nella libreria multipiattaforma, creare un tipo che deriva da `IVisual`:

```csharp
public class CustomVisual : IVisual
{
}
```

Il `CustomVisual` tipo può quindi essere registrato in classi renderer, permettendo [ `Button` ](xref:Xamarin.Forms.Button) oggetti per acconsentire esplicitamente tramite il renderer.

## <a name="register-the-ivisual-type"></a>Registrare il tipo IVisual

Nei progetti di piattaforma, le classi renderer con decorare il `ExportRendererAttribute`:

```csharp
[assembly: ExportRenderer(typeof(Xamarin.Forms.Button), typeof(CustomButtonRenderer), new[] { typeof(CustomVisual) })]
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        ...
    }
}
```

In questo esempio, il `ExportRendererAttribute` specifica che il `CustomButtonRenderer` classe viene usata per eseguire il rendering utilizzano [ `Button` ](xref:Xamarin.Forms.Button) oggetti, con la `IVisual` tipo registrato come terzo argomento. Un renderer che specifica un `IVisual` tipo, come parte della relativa `ExportRendererAttribute`, verrà usato per eseguire il rendering scelto in viste, anziché il renderer predefinito.

## <a name="consume-the-visual-renderer"></a>Utilizzare il renderer Visual

Oggetto [ `Button` ](xref:Xamarin.Forms.Button) oggetto possa acconsentire esplicitamente utilizzando le classi renderer impostando relativo [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) proprietà `Custom`:

```xaml
<Button Visual="Custom"
        Text="CUSTOM BUTTON"
        BackgroundColor="{StaticResource PrimaryColor}"
        TextColor="{StaticResource SecondaryTextColor}"
        HorizontalOptions="FillAndExpand" />
```

> [!NOTE]
> In XAML, un convertitore di tipi Elimina la necessità di includere il suffisso "Visual" nel [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) valore della proprietà. Tuttavia, il nome completo del tipo può anche essere specificato.

Il codice C# equivalente è:

```csharp
Button button = new Button { Text = "CUSTOM BUTTON", ... };
button.Visual = new CustomVisual();
```

In fase di selezione del renderer, il [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) proprietà della [ `Button` ](xref:Xamarin.Forms.Button) vengono ispezionati e inclusi nel processo di selezione del renderer. Se non si trova un renderer, verrà utilizzato il renderer predefinito di xamarin. Forms.

Le schermate seguenti illustrano il rendering [ `Button` ](xref:Xamarin.Forms.Button), che consente di visualizzare il testo con un'ombreggiatura:

[![Screenshot del pulsante personalizzato con il testo dell'ombreggiatura, in iOS e Android](material-visual-images/custom-button.png "pulsante con il testo con ombreggiatura")](material-visual-images/custom-button-large.png#lightbox)

## <a name="register-a-name-for-the-ivisual-type"></a>Registrare un nome per il tipo IVisual

Il [ `VisualAttribute` ](xref:Xamarin.Forms.VisualAttribute) può essere utilizzato per registrare facoltativamente un nome diverso per il `IVisual` tipo. Questo approccio è utilizzabile per risolvere i conflitti di denominazione tra diverse librerie di Visual o in situazioni in cui si vuole semplicemente fare riferimento a un oggetto visivo con un nome diverso rispetto al relativo nome tipo.

Il [ `VisualAttribute` ](xref:Xamarin.Forms.VisualAttribute) devono essere definiti a livello di assembly nella libreria multipiattaforma o nel progetto di piattaforma:

```csharp
[assembly: Visual("MyVisual", typeof(CustomVisual))]
```

Il `IVisual` tipo può quindi essere utilizzato con il nome registrato:

```xaml
<Button Visual="MyVisual"
        ... />
```

> [!NOTE]
> Durante l'utilizzo di un oggetto visivo con il nome registrato, è necessario incluso un suffisso "Visual".

## <a name="related-links"></a>Collegamenti correlati

- [Oggetto visivo Material (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)
- [Oggetto visivo materiali di xamarin. Forms](material-visual.md)
- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
