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
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304163"
---
# <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

_Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati._

Il processo per l'utilizzo di una specifica della piattaforma tramite XAML o tramite il codice fluent API è come segue:

1. Aggiungere una dichiarazione di `xmlns` o `using` direttiva per lo spazio dei nomi [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) .
1. Aggiungere una dichiarazione di `xmlns` o una direttiva `using` per lo spazio dei nomi che contiene la funzionalità specifica della piattaforma:
    1. In iOS si tratta dello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) .
    1. In Android, questo è lo spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) . Per Android AppCompat, questo è lo spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) .
    1. Nel piattaforma UWP (Universal Windows Platform), questo è lo spazio dei nomi [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) .
1. Applicare la specifica della piattaforma da XAML o dal codice con l'API `On<T>` Fluent. Il valore di `T` può essere i tipi [`iOS`](xref:Xamarin.Forms.PlatformConfiguration.iOS), [`Android`](xref:Xamarin.Forms.PlatformConfiguration.Android)o [`Windows`](xref:Xamarin.Forms.PlatformConfiguration.Windows) dello spazio dei nomi [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) .

> [!NOTE]
> Si noti che il tentativo di utilizzare una specifica della piattaforma su una piattaforma in cui non è disponibile non comporterà un errore. Al contrario, il codice verrà eseguito senza la specifica della piattaforma in corso l'applicazione.

Le specifiche della piattaforma utilizzate tramite l'API di codice `On<T>` Fluent restituiscono [`IPlatformElementConfiguration`](xref:Xamarin.Forms.IPlatformElementConfiguration`2) oggetti. In tal modo funzionalità specifiche della piattaforma più venga richiamato sullo stesso oggetto con metodo di propagazione.

Per ulteriori informazioni sulle specifiche della piattaforma fornite da Novell. Forms, vedere specifiche della piattaforma [iOS](~/xamarin-forms/platform/ios/index.md), specifiche della [piattaforma Android](~/xamarin-forms/platform/android/index.md)e [specifiche della piattaforma Windows](~/xamarin-forms/platform/windows/index.md).

## <a name="creating-platform-specifics"></a>Creazione di specifiche della piattaforma

I fornitori possono creare le proprie specifiche della piattaforma con effetti. Un effetto fornisce la funzionalità specifica, che viene quindi esposto tramite un oggetto specifico della piattaforma. Il risultato è un effetto che può essere utilizzato più facilmente tramite XAML e un codice fluent API.

Il processo per la creazione di una specifica della piattaforma è come segue:

1. Implementare la funzionalità specifica come un effetto. Per ulteriori informazioni, vedere [creazione di un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Creare una classe specifica della piattaforma che esporrà l'effetto. Per ulteriori informazioni, vedere [creazione di una classe specifica della piattaforma](#creating-a-platform-specific-class).
1. Nella classe specifica della piattaforma, implementare una proprietà associata per consentire la specifica della piattaforma devono essere usati tramite XAML. Per ulteriori informazioni, vedere [aggiunta di una proprietà associata](#adding-an-attached-property).
1. Nella classe specifica della piattaforma, implementare i metodi di estensione per consentire la specifica della piattaforma devono essere usati tramite un codice fluent API. Per ulteriori informazioni, vedere [aggiunta di metodi di estensione](#adding-extension-methods).
1. Modificare l'implementazione effettiva in modo che l'effetto viene applicato solo se la specifica della piattaforma è stata richiamata sulla stessa piattaforma come l'effetto. Per ulteriori informazioni, vedere [creazione dell'effetto](#creating-the-effect).

Il risultato derivante dall'esposizione di un effetto di una specifica della piattaforma è che gli effetti possono essere utilizzati più facilmente tramite XAML e un codice fluent API.

> [!NOTE]
> Si prevede che i fornitori utilizzerà questa tecnica per creare i propri-funzionalità specifiche della piattaforma, per facilitare l'utilizzo da parte degli utenti. Mentre gli utenti possono scegliere di creare le proprie funzionalità specifiche della piattaforma, tenere presente che richiede più codice rispetto alla creazione e utilizzo di un effetto.

Nell' [applicazione di esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) viene illustrato un `Shadow` specifico della piattaforma che aggiunge un'ombreggiatura al testo visualizzato da un controllo [`Label`](xref:Xamarin.Forms.Label) :

![](images/screenshots.png "Shadow Platform-Specific")

L' [applicazione di esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) implementa i `Shadow` specifici della piattaforma in ogni piattaforma, per una maggiore facilità di comprensione. Tuttavia, a parte l'implementazione ogni effetto specifico della piattaforma, l'implementazione della classe Shadow è in gran parte identico per ogni piattaforma. Pertanto, questa guida illustra l'implementazione della classe di ombreggiatura ed effetto associato in un'unica piattaforma.

Per ulteriori informazioni sugli effetti, vedere [personalizzazione dei controlli con effetti](~/xamarin-forms/app-fundamentals/effects/index.md).

### <a name="creating-a-platform-specific-class"></a>Creazione di una classe specifica della piattaforma

Una specifica della piattaforma viene creata come classe `public static`:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

Le sezioni seguenti illustrano l'implementazione del `Shadow` effetto specifico della piattaforma e associato.

#### <a name="adding-an-attached-property"></a>Aggiunta di una proprietà associata

È necessario aggiungere una proprietà associata alla `Shadow` specifica della piattaforma per consentire l'utilizzo tramite XAML:

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

Il `IsShadowed` proprietà associata viene usato per aggiungere l'effetto `MyCompany.LabelShadowEffect` a e rimuoverlo da, il controllo a cui è collegata la classe `Shadow`. Tale proprietà associata registra il metodo `OnIsShadowedPropertyChanged` che verrà eseguito quando cambia il valore della proprietà. A sua volta, questo metodo chiama il metodo `AttachEffect` o `DetachEffect` per aggiungere o rimuovere l'effetto in base al valore della proprietà associata `IsShadowed`. L'effetto viene aggiunto o rimosso dal controllo modificando la raccolta di [`Effects`](xref:Xamarin.Forms.Element.Effects) del controllo.

> [!NOTE]
> Si noti che l'effetto viene risolto specificando un valore che è una concatenazione del nome di gruppo di risoluzione e identificatore univoco che viene specificata per l'implementazione effettiva. Per ulteriori informazioni, vedere [creazione di un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md).

Per altre informazioni sulle proprietà associate, vedere [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md).

#### <a name="adding-extension-methods"></a>Aggiunta di metodi di estensione

I metodi di estensione devono essere aggiunti alla `Shadow` specifica della piattaforma per consentire l'utilizzo tramite un'API del codice Fluent:

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

I metodi di estensione `IsShadowed` e `SetIsShadowed` richiamano le funzioni di accesso get e set per la proprietà associata `IsShadowed`, rispettivamente. Ogni metodo di estensione opera sul tipo di `IPlatformElementConfiguration<iOS, FormsElement>`, che specifica che è possibile richiamare la specifica della piattaforma sulle istanze [`Label`](xref:Xamarin.Forms.Label) da iOS.

#### <a name="creating-the-effect"></a>Creazione dell'effetto

Il `Shadow` specifico della piattaforma aggiunge l'`MyCompany.LabelShadowEffect` a un [`Label`](xref:Xamarin.Forms.Label)e lo rimuove. L'esempio di codice seguente mostra l'implementazione di `LabelShadowEffect` per il progetto iOS:

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

Il metodo `UpdateShadow` imposta `Control.Layer` proprietà per creare l'ombreggiatura, purché la proprietà associata `IsShadowed` sia impostata su `true`e purché il `Shadow` specifico della piattaforma sia stato richiamato sulla stessa piattaforma per cui è implementato l'effetto. Questo controllo viene eseguito con il metodo `OnThisPlatform`.

Se il `Shadow.IsShadowed` valore della proprietà associata viene modificato in fase di esecuzione, l'effetto deve rispondere rimuovendo l'ombreggiatura. Pertanto, viene utilizzata una versione sottoposta a override del metodo `OnElementPropertyChanged` per rispondere alla modifica della proprietà associabile chiamando il metodo `UpdateShadow`.

Per ulteriori informazioni sulla creazione di un effetto, vedere [creazione di un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md) e [passaggio di parametri effetto come proprietà associate](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md).

### <a name="consuming-the-platform-specific"></a>Utilizzo dell'oggetto specifico della piattaforma

Il `Shadow` specifico della piattaforma viene utilizzato in XAML impostando la proprietà `Shadow.IsShadowed` associata a un valore `boolean`:

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

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [ShadowPlatformSpecific (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)
- [Specifiche della piattaforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Specifiche della piattaforma Android](~/xamarin-forms/platform/android/index.md)
- [Specifiche della piattaforma Windows](~/xamarin-forms/platform/windows/index.md)
- [Personalizzazione dei controlli con effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md)
- [API PlatformConfiguration](xref:Xamarin.Forms.PlatformConfiguration)
