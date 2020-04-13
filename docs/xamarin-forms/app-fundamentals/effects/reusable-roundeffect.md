---
title: Xamarin.Forms RoundEffect riutilizzabile
description: RoundEffect è un effetto riutilizzabile che può essere applicato a qualsiasi controllo derivato da VisualElement per eseguire il rendering del controllo come un cerchio.
ms.prod: xamarin
ms.assetid: B5DE7507-B565-4EE5-9897-27E5733FD173
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/25/2019
ms.openlocfilehash: 851ed7a2ad1c416b4d03d583b9d0aeb7f7774eea
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73055960"
---
# <a name="xamarinforms-reusable-roundeffect"></a>Xamarin.Forms RoundEffect riutilizzabile

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)

Il RoundEffect semplifica il rendering di qualsiasi controllo che deriva da VisualElement come un cerchio. Questo effetto può essere utilizzato per creare immagini circolari, pulsanti e altri controlli:

[![Schermate RoundEffect su iOS e Android](example-roundeffect-images/round-effect-cropped.png)](example-roundeffect-images/round-effect.png#lightbox)

## <a name="create-a-shared-routingeffect"></a>Creare un RoutingEffect condivisoCreate a shared RoutingEffect

È necessario creare una classe di effetti nel progetto condiviso per creare un effetto multipiattaforma. L'applicazione di `RoundEffect` esempio crea una `RoutingEffect` classe vuota che deriva dalla classe:The sample application creates an empty class that derives from the class:

```csharp
public class RoundEffect : RoutingEffect
{
    public RoundEffect() : base($"Xamarin.{nameof(RoundEffect)}")
    {
    }
}
```

Questa classe consente al progetto condiviso di risolvere i riferimenti all'effetto nel codice o in XAML, ma non fornisce alcuna funzionalità. L'effetto deve avere implementazioni per ogni piattaforma.

## <a name="implement-the-android-effect"></a>Implementare l'effetto Android

Il progetto di piattaforma `RoundEffect` Android definisce `PlatformEffect`una classe che deriva da . Questa classe è `assembly` contrassegnata con attributi che consentono a Xamarin.Forms di risolvere la classe effetto:

```csharp
[assembly: ResolutionGroupName("Xamarin")]
[assembly: ExportEffect(typeof(RoundEffectDemo.Droid.RoundEffect), nameof(RoundEffectDemo.Droid.RoundEffect))]
namespace RoundEffectDemo.Droid
{
    public class RoundEffect : PlatformEffect
    {
        // ...
    }
}
```

La piattaforma Android utilizza `OutlineProvider` il concetto di un per definire i bordi di un controllo. Il progetto di `CornerRadiusProvider` esempio include una `ViewOutlineProvider` classe che deriva dalla classe:The sample project includes a class that derives from the class:

```csharp
class CornerRadiusOutlineProvider : ViewOutlineProvider
{
    Element element;

    public CornerRadiusOutlineProvider(Element formsElement)
    {
        element = formsElement;
    }

    public override void GetOutline(Android.Views.View view, Outline outline)
    {
        float scale = view.Resources.DisplayMetrics.Density;
        double width = (double)element.GetValue(VisualElement.WidthProperty) * scale;
        double height = (double)element.GetValue(VisualElement.HeightProperty) * scale;
        float minDimension = (float)Math.Min(height, width);
        float radius = minDimension / 2f;
        Rect rect = new Rect(0, 0, (int)width, (int)height);
        outline.SetRoundRect(rect, radius);
    }
}
```

Questa classe `Width` utilizza `Height` le proprietà e dell'istanza Xamarin.Forms `Element` per calcolare un raggio che è la metà della dimensione più breve.

Una volta definito un `RoundEffect` provider di struttura, la classe può utilizzarlo per implementare l'effetto:Once an outline provider is defined the class can consume it to implement the effect:

```csharp
public class RoundEffect : PlatformEffect
{
    ViewOutlineProvider originalProvider;
    Android.Views.View effectTarget;

    protected override void OnAttached()
    {
        try
        {
            effectTarget = Control ?? Container;
            originalProvider = effectTarget.OutlineProvider;
            effectTarget.OutlineProvider = new CornerRadiusOutlineProvider(Element);
            effectTarget.ClipToOutline = true;
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Failed to set corner radius: {ex.Message}");
        }
    }

    protected override void OnDetached()
    {
        if(effectTarget != null)
        {
            effectTarget.OutlineProvider = originalProvider;
            effectTarget.ClipToOutline = false;
        }
    }
}
```

Il `OnAttached` metodo viene chiamato quando l'effetto è associato a un elemento. L'oggetto esistente `OutlineProvider` viene salvato in modo che possa essere ripristinato quando l'effetto viene scollegato. Una nuova istanza `CornerRadiusOutlineProvider` di viene `OutlineProvider` `ClipToOutline` utilizzata come e è impostata su true per ritagliare gli elementi traboccanti ai bordi del contorno.

Il `OnDetatched` metodo viene chiamato quando l'effetto viene rimosso `OutlineProvider` da un elemento e ripristina il valore originale.

> [!NOTE]
> A seconda del tipo `Control` di elemento, la proprietà può essere o meno null. Se `Control` la proprietà non è null, gli angoli arrotondati possono essere applicati direttamente al controllo. Tuttavia, se è null gli angoli `Container` arrotondati devono essere applicati all'oggetto. Il `effectTarget` campo consente di applicare l'effetto all'oggetto appropriato.

## <a name="implement-the-ios-effect"></a>Implementare l'effetto iOS

Il progetto di piattaforma iOS definisce una `RoundEffect` classe che deriva da `PlatformEffect`. Questa classe è `assembly` contrassegnata con attributi che consentono a Xamarin.Forms di risolvere la classe effetto:

```csharp
[assembly: ResolutionGroupName("Xamarin")]
[assembly: ExportEffect(typeof(RoundEffectDemo.iOS.RoundEffect), nameof(RoundEffectDemo.iOS.RoundEffect))]
namespace RoundEffectDemo.iOS
{
    public class RoundEffect : PlatformEffect
    {
        // ...
    }
```

In iOS, i `Layer` controlli hanno `CornerRadius` una proprietà, che dispone di una proprietà. L'implementazione `RoundEffect` della classe in iOS calcola il `CornerRadius` raggio dell'angolo appropriato e aggiorna la proprietà del layer:The class implementation on iOS calculates the appropriate corner radius and updates the layer's property:

```csharp
public class RoundEffect : PlatformEffect
{
    nfloat originalRadius;
    UIKit.UIView effectTarget;

    protected override void OnAttached()
    {
        try
        {
            effectTarget = Control ?? Container;
            originalRadius = effectTarget.Layer.CornerRadius;
            effectTarget.ClipsToBounds = true;
            effectTarget.Layer.CornerRadius = CalculateRadius();
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Failed to set corner radius: {ex.Message}");
        }
    }

    protected override void OnDetached()
    {
        if (effectTarget != null)
        {
            effectTarget.ClipsToBounds = false;
            if (effectTarget.Layer != null)
            {
                effectTarget.Layer.CornerRadius = originalRadius;
            }
        }
    }

    float CalculateRadius()
    {
        double width = (double)Element.GetValue(VisualElement.WidthRequestProperty);
        double height = (double)Element.GetValue(VisualElement.HeightRequestProperty);
        float minDimension = (float)Math.Min(height, width);
        float radius = minDimension / 2f;

        return radius;
    }
}
```

Il `CalculateRadius` metodo calcola un raggio in base alla dimensione minima `Element`di Xamarin.Forms . Il `OnAttached` metodo viene chiamato quando l'effetto è associato a `CornerRadius` un controllo e aggiorna la proprietà del livello. Imposta la `ClipToBounds` proprietà `true` su modo che gli elementi in equioranti vengano ritagliati ai bordi del controllo. Il `OnDetatched` metodo viene chiamato quando l'effetto viene rimosso da un controllo e inverte queste modifiche, ripristinando il raggio dell'angolo originale.

> [!NOTE]
> A seconda del tipo `Control` di elemento, la proprietà può essere o meno null. Se `Control` la proprietà non è null, gli angoli arrotondati possono essere applicati direttamente al controllo. Tuttavia, se è null gli angoli `Container` arrotondati devono essere applicati all'oggetto. Il `effectTarget` campo consente di applicare l'effetto all'oggetto appropriato.

## <a name="consume-the-effect"></a>Consumare l'effetto

Una volta implementato, l'effetto tra le piattaforme, può essere utilizzato dai controlli Xamarin.Forms. Un'applicazione comune `RoundEffect` di `Image` è rendere un oggetto circolare. Il codice XAML seguente mostra `Image` l'effetto applicato a un'istanza:The following XAML shows the effect being applied to an instance:

```xaml
<Image Source=outdoors"
       HeightRequest="100"
       WidthRequest="100">
    <Image.Effects>
        <local:RoundEffect />
    </Image.Effects>
</Image>
```

L'effetto può essere applicato anche nel codice:The effect can also be applied in code:

```csharp
var image = new Image
{
    Source = ImageSource.FromFile("outdoors"),
    HeightRequest = 100,
    WidthRequest = 100
};
image.Effects.Add(new RoundEffect());
```

La `RoundEffect` classe può essere applicata a `VisualElement`qualsiasi controllo che deriva da .

> [!NOTE]
> Affinché l'effetto calcoli il raggio corretto, il controllo a cui è applicato deve avere un ridimensionamento esplicito. Pertanto, `HeightRequest` `WidthRequest` il e proprietà devono essere definite. Se il controllo interessato `StackLayout` `HorizontalOptions` viene visualizzato in un oggetto , la relativa proprietà non deve utilizzare uno dei valori **Expand,** `LayoutOptions.CenterAndExpand` ovvero se non disporrà di dimensioni accurate.

## <a name="related-links"></a>Collegamenti correlati

- [Applicazione di esempio RoundEffect](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)
- [Introduzione agli effetti](~/xamarin-forms/app-fundamentals/effects/introduction.md)
- [Creazione di un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md)
