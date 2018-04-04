---
title: Creazione di piattaforma specifiche
description: Fornitori possono creare propri specifiche di piattaforma con effetti. Un effetto fornisce la funzionalità specifica, che viene quindi esposto tramite un specifico della piattaforma. Il risultato è un effetto che può essere utilizzato più facilmente tramite XAML e un codice di Microsoft Office fluent API. In questo articolo viene illustrato come esporre un effetto tramite una specifica della piattaforma.
ms.prod: xamarin
ms.assetid: 0D0E6274-6EF2-4D40-BB77-3D8E53BCD24B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/23/2016
ms.openlocfilehash: 6283e22d75d9e52ad3e2f300617818c98d887481
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="creating-platform-specifics"></a>Creazione di piattaforma specifiche

_Fornitori possono creare propri specifiche di piattaforma con effetti. Un effetto fornisce la funzionalità specifica, che viene quindi esposto tramite un specifico della piattaforma. Il risultato è un effetto che può essere utilizzato più facilmente tramite XAML e un codice di Microsoft Office fluent API. In questo articolo viene illustrato come esporre un effetto tramite una specifica della piattaforma._

## <a name="overview"></a>Panoramica

Il processo per la creazione di una specifica della piattaforma è come segue:

1. Implementare la funzionalità specifica come un effetto. Per ulteriori informazioni, vedere [creando un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Creare una classe specifica della piattaforma che esporrà l'effetto. Per ulteriori informazioni, vedere [creazione di una classe specifica della piattaforma](#creating).
1. Nella classe specifica della piattaforma, implementare una proprietà associata per consentire la specifica della piattaforma deve essere utilizzato tramite XAML. Per ulteriori informazioni, vedere [aggiunta di una proprietà associata](#attached_property).
1. Nella classe specifica della piattaforma, implementare i metodi di estensione per consentire la specifica della piattaforma per l'utilizzo tramite un codice di Microsoft Office fluent API. Per ulteriori informazioni, vedere [aggiunta di metodi di estensione](#extension_methods).
1. Modificare l'implementazione effettiva, in modo che l'effetto viene applicata solo se è stato richiamato specifiche della piattaforma sulla stessa piattaforma come l'effetto. Per ulteriori informazioni, vedere [creando l'effetto](#creating_the_effect).

Il risultato di esporre un effetto di una specifica della piattaforma è che gli effetti possono essere utilizzati più facilmente tramite XAML e un codice di Microsoft Office fluent API.

> [!NOTE]
> Si prevede che i fornitori utilizzerà questa tecnica per creare i propri specifiche di piattaforma, per facilitare l'utilizzo da parte degli utenti. Mentre gli utenti possono scegliere di creare i propri specifiche di piattaforma, si noti che è necessario altro codice di creazione e utilizzo di un effetto.

L'applicazione di esempio viene illustrato un `Shadow` specifico della piattaforma che consente di aggiungere un'ombreggiatura al testo visualizzato da un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controllo:

![](creating-images/screenshots.png "Shadow specifico della piattaforma")

L'applicazione di esempio implementa il `Shadow` specifico della piattaforma in ogni piattaforma, per facilitare la comprensione. Tuttavia, a parte l'implementazione ogni effetto specifico della piattaforma, l'implementazione della classe replicata è in gran parte identico per ogni piattaforma. Pertanto, questa guida illustra l'implementazione della classe replicata ed effetto associato in un'unica piattaforma.

Per ulteriori informazioni sugli effetti, vedere [Customizing Controls con effetti](~/xamarin-forms/app-fundamentals/effects/index.md).

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

Le sezioni seguenti illustrano l'implementazione del `Shadow` effetto specifico della piattaforma e l'oggetto associato.

<a name="attached_property" />

### <a name="adding-an-attached-property"></a>Aggiunta di una proprietà associata

Aggiungere una proprietà associata di `Shadow` specifico della piattaforma per consentire l'utilizzo tramite XAML:

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

Il `IsShadowed` proprietà associata viene utilizzata per aggiungere il `MyCompany.LabelShadowEffect` effetto e rimuoverlo dalla, il controllo che il `Shadow` classe è associata a. Questo collegato proprietà registri il `OnIsShadowedPropertyChanged` metodo che verrà eseguita quando cambia il valore della proprietà. A sua volta, questo metodo chiama il `AttachEffect` o `DetachEffect` metodo per aggiungere o rimuovere l'effetto in base al valore del `IsShadowed` proprietà associata. L'effetto viene aggiunto o rimosso dal controllo di modifica del controllo [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) insieme.

> [!NOTE]
> Si noti che l'effetto viene risolto specificando un valore che è una concatenazione del nome del gruppo di risoluzione di un identificatore univoco che viene specificato per l'implementazione effettiva. Per ulteriori informazioni, vedere [creando un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md).

Per ulteriori informazioni sulle proprietà associate, vedere [collegato proprietà](~/xamarin-forms/xaml/attached-properties.md).

<a name="extension_methods" />

### <a name="adding-extension-methods"></a>Aggiunta di metodi di estensione

Metodi di estensione devono essere aggiunto per il `Shadow` specifico della piattaforma per consentire l'utilizzo tramite un codice di Microsoft Office fluent API:

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

Il `IsShadowed` e `SetIsShadowed` metodi di estensione richiamare get e set di funzioni di accesso per il `IsShadowed` proprietà collegata, rispettivamente. Ogni metodo di estensione opera sul `IPlatformElementConfiguration<iOS, FormsElement>` tipo, che specifica che la specifica della piattaforma può essere richiamato su [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) istanze IOS.

<a name="creating_the_effect" />

### <a name="creating-the-effect"></a>L'effetto di creazione

Il `Shadow` aggiunge specifiche della piattaforma di `MyCompany.LabelShadowEffect` per un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)e lo rimuove. Nell'esempio di codice riportato di seguito viene illustrato il `LabelShadowEffect` implementazione per il progetto iOS:

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

Il `UpdateShadow` metodo imposta `Control.Layer` proprietà per creare l'ombreggiatura, a condizione che il `IsShadowed` proprietà associata è impostata su `true`a condizione che il `Shadow` specifico della piattaforma è stato richiamato sulla stessa piattaforma che il Effetto viene implementato per. Questo controllo viene eseguito con il `OnThisPlatform` metodo.

Se il `Shadow.IsShadowed` collegato valore proprietà viene modificato in fase di esecuzione, le esigenze di effetto di rispondere rimuovendo l'ombreggiatura. Pertanto, una versione sottoposta a override del `OnElementPropertyChanged` metodo viene utilizzato per rispondere alla modifica proprietà associabile chiamando il `UpdateShadow` metodo.

Per ulteriori informazioni sulla creazione di un effetto, vedere [creando un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md) e [Passing Parameters effetto come proprietà associata](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md).

## <a name="consuming-a-platform-specific"></a>Utilizzo di una specifica della piattaforma

Il `Shadow` specifico della piattaforma verrà utilizzato in XAML impostando i `Shadow.IsShadowed` associata a un `boolean` valore:

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

In alternativa, possono essere utilizzato da codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

Per ulteriori informazioni sull'utilizzo di piattaforma specifiche, vedere [le specifiche della piattaforma utilizzo](~/xamarin-forms/platform/platform-specifics/consuming/index.md).

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come esporre un effetto tramite una specifica della piattaforma. Il risultato è un effetto che può essere utilizzato più facilmente tramite XAML e un codice di Microsoft Office fluent API.


## <a name="related-links"></a>Collegamenti correlati

- [ShadowPlatformSpecific (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/)
- [Personalizzazione dei controlli con effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md)
