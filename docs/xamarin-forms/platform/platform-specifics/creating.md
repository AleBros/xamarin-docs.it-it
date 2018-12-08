---
title: Creazione di funzionalità specifiche della piattaforma
description: Questo articolo illustra come esporre un effetto tramite una specifica della piattaforma.
ms.prod: xamarin
ms.assetid: 0D0E6274-6EF2-4D40-BB77-3D8E53BCD24B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/23/2016
ms.openlocfilehash: 3f68b7c1ef9603d4a6d21e0c00a4cc8f4f7d818a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058526"
---
# <a name="creating-platform-specifics"></a>Creazione di funzionalità specifiche della piattaforma

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/)

_I fornitori possono creare le proprie funzionalità specifiche della piattaforma con effetti. Un effetto fornisce la funzionalità specifica, che viene quindi esposto tramite un oggetto specifico della piattaforma. Il risultato è un effetto che può essere utilizzato più facilmente tramite XAML e un codice fluent API. Questo articolo illustra come esporre un effetto tramite una specifica della piattaforma._

## <a name="overview"></a>Panoramica

Il processo per la creazione di una specifica della piattaforma è come segue:

1. Implementare la funzionalità specifica come un effetto. Per altre informazioni, vedere [creando un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Creare una classe specifica della piattaforma che esporrà l'effetto. Per altre informazioni, vedere [creazione di una classe specifica della piattaforma](#creating).
1. Nella classe specifica della piattaforma, implementare una proprietà associata per consentire la specifica della piattaforma devono essere usati tramite XAML. Per altre informazioni, vedere [aggiunta di una proprietà associata](#attached_property).
1. Nella classe specifica della piattaforma, implementare i metodi di estensione per consentire la specifica della piattaforma devono essere usati tramite un codice fluent API. Per altre informazioni, vedere [aggiunta di metodi di estensione](#extension_methods).
1. Modificare l'implementazione effettiva in modo che l'effetto viene applicato solo se la specifica della piattaforma è stata richiamata sulla stessa piattaforma come l'effetto. Per altre informazioni, vedere [creando l'effetto](#creating_the_effect).

Il risultato derivante dall'esposizione di un effetto di una specifica della piattaforma è che gli effetti possono essere utilizzati più facilmente tramite XAML e un codice fluent API.

> [!NOTE]
> Si prevede che i fornitori utilizzerà questa tecnica per creare i propri-funzionalità specifiche della piattaforma, per facilitare l'utilizzo da parte degli utenti. Mentre gli utenti possono scegliere di creare le proprie funzionalità specifiche della piattaforma, tenere presente che richiede più codice rispetto alla creazione e utilizzo di un effetto.

L'applicazione di esempio viene illustrato un `Shadow` specifiche della piattaforma che consente di aggiungere un'ombreggiatura per il testo visualizzato da un [ `Label` ](xref:Xamarin.Forms.Label) controllo:

![](creating-images/screenshots.png "Specifica della piattaforma shadow")

L'applicazione di esempio implementa il `Shadow` specifiche della piattaforma in ogni piattaforma, per facilitare la comprensione. Tuttavia, a parte l'implementazione ogni effetto specifico della piattaforma, l'implementazione della classe Shadow è in gran parte identico per ogni piattaforma. Pertanto, questa guida illustra l'implementazione della classe di ombreggiatura ed effetto associato in un'unica piattaforma.

Per altre informazioni sugli effetti, vedere [personalizzazione dei controlli con effetti](~/xamarin-forms/app-fundamentals/effects/index.md).

<a name="creating" />

## <a name="creating-a-platform-specific-class"></a>Creazione di una classe specifica della piattaforma

Viene creata una specifica della piattaforma come un `public static` classe:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

Le sezioni seguenti illustrano l'implementazione del `Shadow` specifiche della piattaforma e relativi effetti.

<a name="attached_property" />

### <a name="adding-an-attached-property"></a>Aggiunta di una proprietà associata

Una proprietà associata deve essere aggiunto al `Shadow` specifiche della piattaforma per consentire l'utilizzo tramite XAML:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        const string EffectName = "MyCompany.LabelShadowEffect";

        public static readonly BindableProperty IsShadowedProperty =
            BindableProperty.CreateAttached("IsShadowed",
                                            typeof(bool),
                                            typeof(Shadow),
                                            false,
                                            propertyChanged: OnIsShadowedPropertyChanged);

        public static bool GetIsShadowed(BindableObject element)
        {
            return (bool)element.GetValue(IsShadowedProperty);
        }

        public static void SetIsShadowed(BindableObject element, bool value)
        {
            element.SetValue(IsShadowedProperty, value);
        }

        ...

        static void OnIsShadowedPropertyChanged(BindableObject element, object oldValue, object newValue)
        {
            if ((bool)newValue)
            {
                AttachEffect(element as FormsElement);
            }
            else
            {
                DetachEffect(element as FormsElement);
            }
        }

        static void AttachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || controller.EffectIsAttached(EffectName))
            {
                return;
            }
            element.Effects.Add(Effect.Resolve(EffectName));
        }

        static void DetachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || !controller.EffectIsAttached(EffectName))
            {
                return;
            }

            var toRemove = element.Effects.FirstOrDefault(e => e.ResolveId == Effect.Resolve(EffectName).ResolveId);
            if (toRemove != null)
            {
                element.Effects.Remove(toRemove);
            }
        }
    }
}
```

Il `IsShadowed` proprietà associata viene utilizzata per aggiungere i `MyCompany.LabelShadowEffect` effetto e lo rimuove dalla, il controllo che il `Shadow` classe è associata a. Ciò collegato proprietà registri il `OnIsShadowedPropertyChanged` metodo che verrà eseguito quando viene modificato il valore della proprietà. A sua volta, questo metodo chiama il `AttachEffect` oppure `DetachEffect` metodo per aggiungere o rimuovere l'effetto in base al valore del `IsShadowed` proprietà associata. L'effetto viene aggiunto o rimosso dal controllo di modifica del controllo [ `Effects` ](xref:Xamarin.Forms.Element.Effects) raccolta.

> [!NOTE]
> Si noti che l'effetto viene risolto specificando un valore che è una concatenazione del nome di gruppo di risoluzione e identificatore univoco che viene specificata per l'implementazione effettiva. Per altre informazioni, vedere [creando un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md).

Per altre informazioni sulle proprietà associate, vedere [collegati proprietà](~/xamarin-forms/xaml/attached-properties.md).

<a name="extension_methods" />

### <a name="adding-extension-methods"></a>Aggiunta di metodi di estensione

I metodi di estensione devono essere aggiunto al `Shadow` specifiche della piattaforma per consentire l'utilizzo tramite un codice fluent API:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        ...
        public static bool IsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config)
        {
            return GetIsShadowed(config.Element);
        }

        public static IPlatformElementConfiguration<iOS, FormsElement> SetIsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config, bool value)
        {
            SetIsShadowed(config.Element, value);
            return config;
        }
        ...
    }
}
```

Il `IsShadowed` e `SetIsShadowed` metodi di estensione richiama get e set di funzioni di accesso per il `IsShadowed` proprietà associata, rispettivamente. Ogni metodo di estensione opera sul `IPlatformElementConfiguration<iOS, FormsElement>` tipo, che specifica che la specifica della piattaforma può essere richiamata in [ `Label` ](xref:Xamarin.Forms.Label) istanze da iOS.

<a name="creating_the_effect" />

### <a name="creating-the-effect"></a>Creazione di effetto

Il `Shadow` specifiche della piattaforma aggiunge il `MyCompany.LabelShadowEffect` a un [ `Label` ](xref:Xamarin.Forms.Label)e lo rimuove. Nell'esempio di codice riportato di seguito viene illustrato il `LabelShadowEffect` implementazione per il progetto iOS:

```csharp
[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace ShadowPlatformSpecific.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            UpdateShadow();
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);

            if (args.PropertyName == Shadow.IsShadowedProperty.PropertyName)
            {
                UpdateShadow();
            }
        }

        void UpdateShadow()
        {
            try
            {
                if (((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.CornerRadius = 5;
                    Control.Layer.ShadowColor = UIColor.Black.CGColor;
                    Control.Layer.ShadowOffset = new CGSize(5, 5);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
                else if (!((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.ShadowOpacity = 0;
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

Il `UpdateShadow` metodo imposta `Control.Layer` delle proprietà per creare l'ombreggiatura, a condizione che il `IsShadowed` associata è impostata su `true`a condizione che il `Shadow` specifiche della piattaforma è stato richiamato sulla stessa piattaforma che il Effetto viene implementato. Questo controllo viene eseguito con il `OnThisPlatform` (metodo).

Se il `Shadow.IsShadowed` collegato modifiche dei valori di proprietà in fase di esecuzione, le esigenze di effetto di rispondere rimuovendo l'ombreggiatura. Pertanto, una versione override del `OnElementPropertyChanged` metodo viene utilizzato per rispondere alla modifica di proprietà associabili chiamando il `UpdateShadow` (metodo).

Per altre informazioni sulla creazione di un effetto, vedere [creando un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md) e [passando i parametri di effetto come proprietà associata](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md).

## <a name="consuming-a-platform-specific"></a>Utilizzo di una specifica della piattaforma

Il `Shadow` specifiche della piattaforma verrà utilizzati in XAML mediante l'impostazione di `Shadow.IsShadowed` proprietà associata una `boolean` valore:

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

Per altre informazioni sull'uso delle funzionalità specifiche della piattaforma, vedere [funzionalità specifiche della piattaforma utilizzo](~/xamarin-forms/platform/platform-specifics/consuming/index.md).

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come esporre un effetto tramite una specifica della piattaforma. Il risultato è un effetto che può essere utilizzato più facilmente tramite XAML e un codice fluent API.


## <a name="related-links"></a>Collegamenti correlati

- [ShadowPlatformSpecific (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/)
- [Personalizzazione dei controlli con effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md)
