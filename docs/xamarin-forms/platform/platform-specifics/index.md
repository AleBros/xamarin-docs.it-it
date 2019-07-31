---
title: Funzionalità specifiche della piattaforma
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come utilizzare e creare specifiche della piattaforma.
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: f6190b9c0d29d57d6d509bdff25e2ce3572e3a3c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646842"
---
# <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

_Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti._

Il processo per l'utilizzo di una specifica della piattaforma tramite XAML o tramite il codice fluent API è come segue:

1. Aggiungere un `xmlns` dichiarazione o `using` direttiva per il [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) dello spazio dei nomi.
1. Aggiungere un `xmlns` dichiarazione o `using` direttiva dello spazio dei nomi che contiene le funzionalità specifiche della piattaforma:
    1. In iOS, questo è il [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi.
    1. In Android, questo è il [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi. Per Android AppCompat, questo è il [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) dello spazio dei nomi.
    1. Nella piattaforma Windows universale, questo è il [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) dello spazio dei nomi.
1. Applicare le specifiche della piattaforma da XAML o dal codice con il `On<T>` API fluent. Il valore di `T` può essere il [`iOS`](xref:Xamarin.Forms.PlatformConfiguration.iOS), [`Android`](xref:Xamarin.Forms.PlatformConfiguration.Android), o [`Windows`](xref:Xamarin.Forms.PlatformConfiguration.Windows) tipi dal [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) dello spazio dei nomi.

> [!NOTE]
> Si noti che il tentativo di utilizzare una specifica della piattaforma su una piattaforma in cui non è disponibile non comporterà un errore. Al contrario, il codice verrà eseguito senza la specifica della piattaforma in corso l'applicazione.

Funzionalità specifiche della piattaforma utilizzati tramite il `On<T>` codice fluent return API [`IPlatformElementConfiguration`](xref:Xamarin.Forms.IPlatformElementConfiguration`2) oggetti. In tal modo funzionalità specifiche della piattaforma più venga richiamato sullo stesso oggetto con metodo di propagazione.

Per ulteriori informazioni sulle specifiche della piattaforma fornite da Novell. Forms, vedere specifiche della piattaforma [iOS](~/xamarin-forms/platform/ios/index.md), specifiche della [piattaforma Android](~/xamarin-forms/platform/android/index.md)e [specifiche della piattaforma Windows](~/xamarin-forms/platform/windows/index.md).

## <a name="creating-platform-specifics"></a>Creazione di specifiche della piattaforma

I fornitori possono creare le proprie specifiche della piattaforma con effetti. Un effetto fornisce la funzionalità specifica, che viene quindi esposto tramite un oggetto specifico della piattaforma. Il risultato è un effetto che può essere utilizzato più facilmente tramite XAML e un codice fluent API.

Il processo per la creazione di una specifica della piattaforma è come segue:

1. Implementare la funzionalità specifica come un effetto. Per altre informazioni, vedere [creando un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Creare una classe specifica della piattaforma che esporrà l'effetto. Per altre informazioni, vedere [creazione di una classe specifica della piattaforma](#creating-a-platform-specific-class).
1. Nella classe specifica della piattaforma, implementare una proprietà associata per consentire la specifica della piattaforma devono essere usati tramite XAML. Per altre informazioni, vedere [aggiunta di una proprietà associata](#adding-an-attached-property).
1. Nella classe specifica della piattaforma, implementare i metodi di estensione per consentire la specifica della piattaforma devono essere usati tramite un codice fluent API. Per altre informazioni, vedere [aggiunta di metodi di estensione](#adding-extension-methods).
1. Modificare l'implementazione effettiva in modo che l'effetto viene applicato solo se la specifica della piattaforma è stata richiamata sulla stessa piattaforma come l'effetto. Per altre informazioni, vedere [creando l'effetto](#creating-the-effect).

Il risultato derivante dall'esposizione di un effetto di una specifica della piattaforma è che gli effetti possono essere utilizzati più facilmente tramite XAML e un codice fluent API.

> [!NOTE]
> Si prevede che i fornitori utilizzerà questa tecnica per creare i propri-funzionalità specifiche della piattaforma, per facilitare l'utilizzo da parte degli utenti. Mentre gli utenti possono scegliere di creare le proprie funzionalità specifiche della piattaforma, tenere presente che richiede più codice rispetto alla creazione e utilizzo di un effetto.

L' [applicazione di esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) illustra `Shadow` una specifica della piattaforma che aggiunge un'ombreggiatura al testo visualizzato da [`Label`](xref:Xamarin.Forms.Label) un controllo:

![](images/screenshots.png "Specifica della piattaforma shadow")

L' [applicazione di esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) implementa `Shadow` la piattaforma specifica in ogni piattaforma, per una maggiore facilità di comprensione. Tuttavia, a parte l'implementazione ogni effetto specifico della piattaforma, l'implementazione della classe Shadow è in gran parte identico per ogni piattaforma. Pertanto, questa guida illustra l'implementazione della classe di ombreggiatura ed effetto associato in un'unica piattaforma.

Per altre informazioni sugli effetti, vedere [personalizzazione dei controlli con effetti](~/xamarin-forms/app-fundamentals/effects/index.md).

### <a name="creating-a-platform-specific-class"></a>Creazione di una classe specifica della piattaforma

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

#### <a name="adding-an-attached-property"></a>Aggiunta di una proprietà associata

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

Il `IsShadowed` proprietà associata viene utilizzata per aggiungere i `MyCompany.LabelShadowEffect` effetto e lo rimuove dalla, il controllo che il `Shadow` classe è associata a. Ciò collegato proprietà registri il `OnIsShadowedPropertyChanged` metodo che verrà eseguito quando viene modificato il valore della proprietà. A sua volta, questo metodo chiama il `AttachEffect` oppure `DetachEffect` metodo per aggiungere o rimuovere l'effetto in base al valore del `IsShadowed` proprietà associata. L'effetto viene aggiunto o rimosso dal controllo di modifica del controllo [`Effects`](xref:Xamarin.Forms.Element.Effects) raccolta.

> [!NOTE]
> Si noti che l'effetto viene risolto specificando un valore che è una concatenazione del nome di gruppo di risoluzione e identificatore univoco che viene specificata per l'implementazione effettiva. Per altre informazioni, vedere [creando un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md).

Per altre informazioni sulle proprietà associate, vedere [collegati proprietà](~/xamarin-forms/xaml/attached-properties.md).

#### <a name="adding-extension-methods"></a>Aggiunta di metodi di estensione

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

Il `IsShadowed` e `SetIsShadowed` metodi di estensione richiama get e set di funzioni di accesso per il `IsShadowed` proprietà associata, rispettivamente. Ogni metodo di estensione opera sul `IPlatformElementConfiguration<iOS, FormsElement>` tipo, che specifica che la specifica della piattaforma può essere richiamata in [`Label`](xref:Xamarin.Forms.Label) istanze da iOS.

#### <a name="creating-the-effect"></a>Creazione dell'effetto

Il `Shadow` specifiche della piattaforma aggiunge il `MyCompany.LabelShadowEffect` a un [`Label`](xref:Xamarin.Forms.Label)e lo rimuove. Nell'esempio di codice riportato di seguito viene illustrato il `LabelShadowEffect` implementazione per il progetto iOS:

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

### <a name="consuming-the-platform-specific"></a>Utilizzo dell'oggetto specifico della piattaforma

Il `Shadow` specifiche della piattaforma verrà utilizzati in XAML mediante l'impostazione di `Shadow.IsShadowed` proprietà associata una `boolean` valore:

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [ShadowPlatformSpecific (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)
- [Specifiche della piattaforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Specifiche della piattaforma Android](~/xamarin-forms/platform/android/index.md)
- [Specifiche della piattaforma Windows](~/xamarin-forms/platform/windows/index.md)
- [Personalizzazione dei controlli con effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md)
- [API PlatformConfiguration](xref:Xamarin.Forms.PlatformConfiguration)
