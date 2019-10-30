---
title: Novell. Forms riutilizzabile RoundEffect
description: RoundEffect è un effetto riutilizzabile che può essere applicato a qualsiasi controllo derivante da visualElement per eseguire il rendering del controllo come cerchio.
ms.prod: xamarin
ms.assetid: B5DE7507-B565-4EE5-9897-27E5733FD173
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/25/2019
ms.openlocfilehash: 851ed7a2ad1c416b4d03d583b9d0aeb7f7774eea
ms.sourcegitcommit: aa7e9d2c370ba9cbc830f10b94b4cd4221fc5467
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73055960"
---
# <a name="xamarinforms-reusable-roundeffect"></a>Novell. Forms riutilizzabile RoundEffect

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)

RoundEffect semplifica il rendering di qualsiasi controllo che deriva da visualElement come cerchio. Questo effetto può essere utilizzato per creare immagini circolari, pulsanti e altri controlli:

[![screenshot RoundEffect in iOS e Android](example-roundeffect-images/round-effect-cropped.png)](example-roundeffect-images/round-effect.png#lightbox)

## <a name="create-a-shared-routingeffect"></a>Creare un RoutingEffect condiviso

Per creare un effetto multipiattaforma, è necessario creare una classe Effect nel progetto Shared. L'applicazione di esempio crea una classe `RoundEffect` vuota che deriva dalla classe `RoutingEffect`:

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

Il progetto di piattaforma Android definisce una classe `RoundEffect` che deriva da `PlatformEffect`. Questa classe è contrassegnata con `assembly` attributi che consentono a Novell. Forms di risolvere la classe Effect:

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

La piattaforma Android usa il concetto di `OutlineProvider` per definire i bordi di un controllo. Il progetto di esempio include una classe `CornerRadiusProvider` che deriva dalla classe `ViewOutlineProvider`:

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

Questa classe usa le proprietà `Width` e `Height` dell'istanza di `Element` di Novell. Forms per calcolare un raggio che rappresenta la metà della dimensione più breve.

Una volta definito un provider di struttura, il `RoundEffect` classe può utilizzarlo per implementare l'effetto:

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

Il metodo `OnAttached` viene chiamato quando l'effetto è associato a un elemento. L'oggetto `OutlineProvider` esistente viene salvato per poter essere ripristinato quando l'effetto viene scollegato. Una nuova istanza del `CornerRadiusOutlineProvider` viene utilizzata come `OutlineProvider` e `ClipToOutline` è impostata su true per ritagliare gli elementi che superano i bordi del contorno.

Il metodo `OnDetatched` viene chiamato quando l'effetto viene rimosso da un elemento e ripristina il valore di `OutlineProvider` originale.

> [!NOTE]
> A seconda del tipo di elemento, la proprietà `Control` può essere null o non può essere null. Se la proprietà `Control` non è null, gli angoli arrotondati possono essere applicati direttamente al controllo. Tuttavia, se è null, gli angoli arrotondati devono essere applicati all'oggetto `Container`. Il campo `effectTarget` consente l'applicazione dell'effetto all'oggetto appropriato.

## <a name="implement-the-ios-effect"></a>Implementare l'effetto iOS

Il progetto della piattaforma iOS definisce una classe `RoundEffect` che deriva da `PlatformEffect`. Questa classe è contrassegnata con `assembly` attributi che consentono a Novell. Forms di risolvere la classe Effect:

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

In iOS i controlli hanno una proprietà `Layer`, che ha una proprietà `CornerRadius`. L'implementazione della classe `RoundEffect` in iOS calcola il raggio dell'angolo appropriato e aggiorna la proprietà del `CornerRadius` del livello:

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

Il metodo `CalculateRadius` calcola un raggio in base alla dimensione minima del `Element`Novell. Forms. Il metodo `OnAttached` viene chiamato quando l'effetto è associato a un controllo e aggiorna la proprietà del `CornerRadius` del livello. Imposta la proprietà `ClipToBounds` su `true` in modo che gli elementi di overflow vengano ritagliati ai bordi del controllo. Il metodo `OnDetatched` viene chiamato quando l'effetto viene rimosso da un controllo e inverte le modifiche, ripristinando il raggio dell'angolo originale.

> [!NOTE]
> A seconda del tipo di elemento, la proprietà `Control` può essere null o non può essere null. Se la proprietà `Control` non è null, gli angoli arrotondati possono essere applicati direttamente al controllo. Tuttavia, se è null, gli angoli arrotondati devono essere applicati all'oggetto `Container`. Il campo `effectTarget` consente l'applicazione dell'effetto all'oggetto appropriato.

## <a name="consume-the-effect"></a>Utilizzare l'effetto

Una volta implementato l'effetto su più piattaforme, può essere utilizzato dai controlli Novell. Forms. Un'applicazione comune del `RoundEffect` sta rendendo un oggetto `Image` circolare. Il codice XAML seguente illustra l'effetto applicato a un'istanza di `Image`:

```xaml
<Image Source=outdoors"
       HeightRequest="100"
       WidthRequest="100">
    <Image.Effects>
        <local:RoundEffect />
    </Image.Effects>
</Image>
```

L'effetto può essere applicato anche nel codice:

```csharp
var image = new Image
{
    Source = ImageSource.FromFile("outdoors"),
    HeightRequest = 100,
    WidthRequest = 100
};
image.Effects.Add(new RoundEffect());
```

La classe `RoundEffect` può essere applicata a qualsiasi controllo che deriva da `VisualElement`.

> [!NOTE]
> Per ottenere il corretto funzionamento del raggio, il controllo a cui è applicato deve avere un dimensionamento esplicito. Pertanto, è necessario definire le proprietà `HeightRequest` e `WidthRequest`. Se il controllo interessato viene visualizzato in una `StackLayout`, la relativa proprietà `HorizontalOptions` non deve usare uno dei valori di **espansione** , ad esempio `LayoutOptions.CenterAndExpand` o non avrà dimensioni accurate.

## <a name="related-links"></a>Collegamenti correlati

- [Applicazione di esempio RoundEffect](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)
- [Introduzione agli effetti](~/xamarin-forms/app-fundamentals/effects/introduction.md)
- [Creazione di un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md)
