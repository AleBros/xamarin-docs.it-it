---
title: "descrizione specifica della piattaforma": "specifici della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come utilizzare e creare specifiche della piattaforma ".
ms. prod: Novell MS. AssetID: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/01/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

_Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati._

Di seguito è illustrato il processo per l'utilizzo di una piattaforma specifica tramite XAML oppure tramite l'API di codice Fluent:

1. Aggiungere una `xmlns` dichiarazione o una `using` direttiva per lo [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) spazio dei nomi.
1. Aggiungere una `xmlns` dichiarazione o una `using` direttiva per lo spazio dei nomi che contiene la funzionalità specifica della piattaforma:
    1. In iOS questo è lo [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi.
    1. In Android, questo è lo [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) spazio dei nomi. Per Android AppCompat, questo è lo [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) spazio dei nomi.
    1. Nel piattaforma UWP (Universal Windows Platform), questo è lo [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) spazio dei nomi.
1. Applicare la specifica della piattaforma da XAML o dal codice con l' `On<T>` API Fluent. Il valore di `T` può essere costituito [`iOS`](xref:Xamarin.Forms.PlatformConfiguration.iOS) [`Android`](xref:Xamarin.Forms.PlatformConfiguration.Android) [`Windows`](xref:Xamarin.Forms.PlatformConfiguration.Windows) dai tipi, o dallo [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) spazio dei nomi.

> [!NOTE]
> Si noti che il tentativo di utilizzare una piattaforma specifica in una piattaforma in cui non è disponibile non genererà un errore. Al contrario, il codice verrà eseguito senza l'applicazione di specifiche della piattaforma.

Le specifiche della piattaforma utilizzate tramite l' `On<T>` API del codice Fluent restituiscono [`IPlatformElementConfiguration`](xref:Xamarin.Forms.IPlatformElementConfiguration`2) oggetti. Ciò consente di richiamare più specifiche della piattaforma sullo stesso oggetto con la propagazione del metodo.

Per ulteriori informazioni sulle specifiche della piattaforma fornite da, vedere specifiche della piattaforma Xamarin.Forms [iOS](~/xamarin-forms/platform/ios/index.md), [specifiche della piattaforma Android](~/xamarin-forms/platform/android/index.md)e [specifiche della piattaforma Windows](~/xamarin-forms/platform/windows/index.md).

## <a name="creating-platform-specifics"></a>Creazione di specifiche della piattaforma

I fornitori possono creare le proprie specifiche della piattaforma con effetti. Un effetto fornisce la funzionalità specifica, che viene quindi esposta tramite una specifica della piattaforma. Il risultato è un effetto che può essere più facilmente utilizzato tramite XAML e tramite un'API di codice Fluent.

Il processo per la creazione di una specifica piattaforma è il seguente:

1. Implementare la funzionalità specifica come effetto. Per ulteriori informazioni, vedere [creazione di un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Creare una classe specifica della piattaforma che esporrà l'effetto. Per ulteriori informazioni, vedere [creazione di una classe specifica della piattaforma](#creating-a-platform-specific-class).
1. Nella classe specifica della piattaforma implementare una proprietà associata per consentire l'utilizzo dell'oggetto specifico della piattaforma tramite XAML. Per ulteriori informazioni, vedere [aggiunta di una proprietà associata](#adding-an-attached-property).
1. Nella classe specifica della piattaforma implementare i metodi di estensione per consentire l'utilizzo della piattaforma specifica tramite un'API di codice Fluent. Per ulteriori informazioni, vedere [aggiunta di metodi di estensione](#adding-extension-methods).
1. Modificare l'implementazione dell'effetto in modo che l'effetto venga applicato solo se l'oggetto specifico della piattaforma è stato richiamato sulla stessa piattaforma dell'effetto. Per ulteriori informazioni, vedere [creazione dell'effetto](#creating-the-effect).

Il risultato dell'esposizione di un effetto come specifico della piattaforma è che l'effetto può essere più facilmente utilizzato tramite XAML e tramite un'API di codice Fluent.

> [!NOTE]
> Si prevede che i fornitori utilizzeranno questa tecnica per creare le proprie specifiche della piattaforma, per semplificare l'utilizzo da parte degli utenti. Sebbene gli utenti possano scegliere di creare le proprie specifiche della piattaforma, è necessario tenere presente che richiede un codice maggiore rispetto alla creazione e all'utilizzo di un effetto.

L' [applicazione di esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) illustra una `Shadow` specifica della piattaforma che aggiunge un'ombreggiatura al testo visualizzato da un [`Label`](xref:Xamarin.Forms.Label) controllo:

![](images/screenshots.png "Shadow Platform-Specific")

L' [applicazione di esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) implementa la `Shadow` piattaforma specifica in ogni piattaforma, per una maggiore facilità di comprensione. Tuttavia, a parte l'implementazione di ogni effetto specifico della piattaforma, l'implementazione della classe Shadow è sostanzialmente identica per ogni piattaforma. Pertanto, questa guida è incentrata sull'implementazione della classe Shadow e sull'effetto associato su una singola piattaforma.

Per ulteriori informazioni sugli effetti, vedere [personalizzazione dei controlli con effetti](~/xamarin-forms/app-fundamentals/effects/index.md).

### <a name="creating-a-platform-specific-class"></a>Creazione di una classe specifica della piattaforma

Una specifica della piattaforma viene creata come `public static` classe:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

Nelle sezioni seguenti viene illustrata l'implementazione dell' `Shadow` effetto specifico della piattaforma e associato.

#### <a name="adding-an-attached-property"></a>Aggiunta di una proprietà associata

È necessario aggiungere una proprietà associata all'oggetto `Shadow` specifico della piattaforma per consentire l'utilizzo tramite XAML:

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

La `IsShadowed` proprietà associata viene utilizzata per aggiungere l' `MyCompany.LabelShadowEffect` effetto a e rimuoverlo da, il controllo a cui `Shadow` è associata la classe. Tale proprietà associata registra il metodo `OnIsShadowedPropertyChanged` che verrà eseguito quando cambia il valore della proprietà. A sua volta, questo metodo chiama `AttachEffect` il `DetachEffect` metodo o per aggiungere o rimuovere l'effetto in base al valore della `IsShadowed` proprietà associata. L'effetto viene aggiunto o rimosso dal controllo modificando la raccolta del controllo [`Effects`](xref:Xamarin.Forms.Element.Effects) .

> [!NOTE]
> Si noti che l'effetto viene risolto specificando un valore che è una concatenazione del nome del gruppo di risoluzione e dell'identificatore univoco specificato nell'implementazione dell'effetto. Per ulteriori informazioni, vedere [creazione di un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md).

Per altre informazioni sulle proprietà associate, vedere [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md).

#### <a name="adding-extension-methods"></a>Aggiunta di metodi di estensione

I metodi di estensione devono essere aggiunti alla `Shadow` piattaforma specifica per consentire l'utilizzo tramite un'API del codice Fluent:

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

I `IsShadowed` `SetIsShadowed` metodi di estensione e richiamano rispettivamente le funzioni di accesso get e set per la `IsShadowed` proprietà associata. Ogni metodo di estensione opera sul `IPlatformElementConfiguration<iOS, FormsElement>` tipo, che specifica che l'oggetto specifico della piattaforma può essere richiamato nelle [`Label`](xref:Xamarin.Forms.Label) istanze di iOS.

#### <a name="creating-the-effect"></a>Creazione dell'effetto

La `Shadow` specifica della piattaforma aggiunge l'oggetto `MyCompany.LabelShadowEffect` a un oggetto [`Label`](xref:Xamarin.Forms.Label) e lo rimuove. L'esempio di codice seguente mostra l'implementazione di `LabelShadowEffect` per il progetto iOS:

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

Il `UpdateShadow` metodo imposta le `Control.Layer` proprietà per creare l'ombreggiatura, a condizione che la `IsShadowed` proprietà associata sia impostata su `true` e che l'oggetto `Shadow` specifico della piattaforma sia stato richiamato sulla stessa piattaforma per cui è implementato l'effetto. Questo controllo viene eseguito con il `OnThisPlatform` metodo.

Se il `Shadow.IsShadowed` valore della proprietà associata viene modificato in fase di esecuzione, l'effetto deve rispondere rimuovendo l'ombreggiatura. Pertanto, viene utilizzata una versione sottoposta a override del `OnElementPropertyChanged` metodo per rispondere alla modifica della proprietà associabile chiamando il `UpdateShadow` metodo.

Per ulteriori informazioni sulla creazione di un effetto, vedere [creazione di un effetto](~/xamarin-forms/app-fundamentals/effects/creating.md) e [passaggio di parametri effetto come proprietà associate](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md).

### <a name="consuming-the-platform-specific"></a>Utilizzo dell'oggetto specifico della piattaforma

La `Shadow` specifica della piattaforma viene utilizzata in XAML impostando la `Shadow.IsShadowed` proprietà associata su un `boolean` valore:

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [ShadowPlatformSpecific (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)
- [Funzionalità specifiche della piattaforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Funzionalità specifiche della piattaforma Android](~/xamarin-forms/platform/android/index.md)
- [Specifiche della piattaforma Windows](~/xamarin-forms/platform/windows/index.md)
- [Personalizzazione dei controlli con effetti](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md)
- [API PlatformConfiguration](xref:Xamarin.Forms.PlatformConfiguration)
