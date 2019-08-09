---
title: Creare un renderer Visual Novell. Forms
description: Creare oggetti visivi Novell. Forms da applicare in modo selettivo agli oggetti visualElement senza dover sottoclassare le visualizzazioni Novell. Forms.
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: 8173ebcc174df6e34a53f226a43083bd28941031
ms.sourcegitcommit: 2e5a6b8bcd1a073b54604f51538fd108e1c2a8e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68869383"
---
# <a name="create-a-xamarinforms-visual-renderer"></a>Creare un renderer Visual Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

L'oggetto visivo Novell. Forms consente di creare e applicare in modo selettivo i renderer agli [`VisualElement`](xref:Xamarin.Forms.VisualElement) oggetti senza dover sottoclassare le visualizzazioni Novell. Forms. Un renderer che specifica un `IVisual` tipo, come parte del relativo `ExportRendererAttribute`, verrà usato per il rendering delle visualizzazioni di cui è stato scelto il rendering, anziché il renderer predefinito. In fase di selezione del renderer, il `Visual` proprietà della vista vengono ispezionate e inclusi nel processo di selezione del renderer.

> [!IMPORTANT]
> Attualmente la [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) proprietà non può essere modificata dopo il rendering della vista, ma verrà modificata in una versione futura.

Il processo per la creazione e l'utilizzo di un renderer Visual Novell. Forms è il seguente:

1. Creare renderer della piattaforma per la visualizzazione richiesta. Per altre informazioni, vedere [create renderers](#create-platform-renderers).
1. Creare un tipo che deriva da `IVisual`. Per altre informazioni, vedere [creare un tipo IVisual](#create-an-ivisual-type).
1. Registrare il `IVisual` tipo come parte dell'oggetto `ExportRendererAttribute` che decora i renderer. Per ulteriori informazioni, vedere [la pagina relativa alla registrazione del tipo IVisual](#register-the-ivisual-type).
1. Utilizzare il renderer visivo impostando la [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) proprietà nella vista `IVisual` sul nome. Per ulteriori informazioni, vedere [utilizzare il renderer visuale](#consume-the-visual-renderer).
1. opzionale Registrare un nome per il `IVisual` tipo. Per altre informazioni, vedere [registrare un nome per il tipo di IVisual](#register-a-name-for-the-ivisual-type).

## <a name="create-platform-renderers"></a>Crea renderer della piattaforma

Per informazioni sulla creazione di una classe renderer, vedere renderer [personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Si noti tuttavia che un renderer Visual Novell. Forms viene applicato a una vista senza dover sottoclassare la visualizzazione.

Le classi renderer descritte di seguito implementano un oggetto [`Button`](xref:Xamarin.Forms.Button) personalizzato che Visualizza il testo con un'ombreggiatura.

### <a name="ios"></a>iOS

L'esempio di codice seguente mostra il renderer del pulsante per iOS:

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

L'esempio di codice seguente mostra il renderer del pulsante per Android:

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

## <a name="create-an-ivisual-type"></a>Creazione di un tipo IVisual

Nella libreria multipiattaforma creare un tipo che deriva da `IVisual`:

```csharp
public class CustomVisual : IVisual
{
}
```

Il `CustomVisual` tipo può quindi essere registrato con le classi renderer, [`Button`](xref:Xamarin.Forms.Button) consentendo agli oggetti di acconsentire esplicitamente all'uso dei renderer.

## <a name="register-the-ivisual-type"></a>Registrare il tipo di IVisual

Nei progetti della piattaforma decorare gli spazi dei nomi renderer con `ExportRendererAttribute`:

```csharp
[assembly: ExportRenderer(typeof(Xamarin.Forms.Button), typeof(CustomButtonRenderer), new[] { typeof(CustomVisual) })]
namespace VisualDemos.iOS
{
    public class CustomButtonRenderer : ButtonRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
        {
            // ...
        }
    }
}
```

In questo esempio per il progetto della piattaforma iOS, `ExportRendererAttribute` specifica che la `CustomButtonRenderer` classe verrà [`Button`](xref:Xamarin.Forms.Button) utilizzata per eseguire il rendering di oggetti che utilizzano, `IVisual` con il tipo registrato come terzo argomento. Un renderer che specifica un `IVisual` tipo, come parte del relativo `ExportRendererAttribute`, verrà usato per il rendering delle visualizzazioni di cui è stato scelto il rendering, anziché il renderer predefinito.

## <a name="consume-the-visual-renderer"></a>Utilizzare il renderer visivo

Un [`Button`](xref:Xamarin.Forms.Button) oggetto può acconsentire esplicitamente all'uso delle classi renderer [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) impostando `Custom`la proprietà su:

```xaml
<Button Visual="Custom"
        Text="CUSTOM BUTTON"
        BackgroundColor="{StaticResource PrimaryColor}"
        TextColor="{StaticResource SecondaryTextColor}"
        HorizontalOptions="FillAndExpand" />
```

> [!NOTE]
> In XAML, un convertitore di tipi Elimina la necessità di includere il suffisso "Visual" [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) nel valore della proprietà. È tuttavia possibile specificare anche il nome completo del tipo.

Il codice C# equivalente è:

```csharp
Button button = new Button { Text = "CUSTOM BUTTON", ... };
button.Visual = new CustomVisual();
```

Al momento della selezione del renderer [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) , la proprietà [`Button`](xref:Xamarin.Forms.Button) di viene controllata e inclusa nel processo di selezione del renderer. Se non si trova un renderer, verrà usato il renderer predefinito Novell. Forms.

Gli screenshot seguenti mostrano il rendering [`Button`](xref:Xamarin.Forms.Button), che Visualizza il testo con un'ombreggiatura:

[![Screenshot del pulsante personalizzato con testo ombreggiato, in iOS e Android](material-visual-images/custom-button.png "Pulsante con testo ombreggiato")](material-visual-images/custom-button-large.png#lightbox)

## <a name="register-a-name-for-the-ivisual-type"></a>Registrare un nome per il tipo di IVisual

Può essere utilizzato per registrare facoltativamente un nome diverso per il `IVisual` tipo. [`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute) Questo approccio può essere utilizzato per risolvere i conflitti di denominazione tra librerie visive diverse o in situazioni in cui si desidera solo fare riferimento a un oggetto visivo con un nome diverso rispetto al nome del tipo.

Il [`VisualAttribute`](xref:Xamarin.Forms.VisualAttribute) deve essere definito a livello di assembly nella libreria multipiattaforma o nel progetto di piattaforma:

```csharp
[assembly: Visual("MyVisual", typeof(CustomVisual))]
```

Il `IVisual` tipo può quindi essere utilizzato tramite il nome registrato:

```xaml
<Button Visual="MyVisual"
        ... />
```

> [!NOTE]
> Quando si utilizza un oggetto visivo tramite il nome registrato, è necessario includere qualsiasi suffisso "Visual".

## <a name="related-links"></a>Collegamenti correlati

- [Materiale visivo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Oggetti visivi del materiale Novell. Forms](material-visual.md)
- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
