---
title: Creazione di un effetto
description: Gli effetti semplificano la personalizzazione di un controllo. Questo articolo spiega come creare un effetto che cambia il colore di sfondo del controllo Entry quando il controllo riceve lo stato attivo.
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: c07848b808d023439c88117924e69c336984630b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771503"
---
# <a name="creating-an-effect"></a>Creazione di un effetto

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect)

_Gli effetti semplificano la personalizzazione di un controllo. In questo articolo viene illustrato come creare un effetto che modifica il colore di sfondo del Entry controllo quando il controllo ottiene lo stato attivo._

Il processo per la creazione di un effetto in ogni progetto specifico della piattaforma è come segue:

1. Creare una sottoclasse della classe `PlatformEffect`.
1. Eseguire l'override del metodo `OnAttached` scrivere la logica per personalizzare il controllo.
1. Eseguire l'override del metodo `OnDetached` e scrivere la logica per pulire la personalizzazione del controllo, se necessario.
1. Aggiungere [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) un attributo alla classe dell'effetto. Questo attributo imposta uno spazio dei nomi a livello di società per gli effetti, per evitare che si verifichino conflitti con altri effetti omonimi. Si noti che questo attributo può essere applicato solo una volta per ogni progetto.
1. Aggiungere [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) un attributo alla classe dell'effetto. Questo attributo registra l'effetto con un ID univoco che viene usato da Xamarin.Forms, insieme al nome gruppo, per individuare l'effetto prima di applicarlo a un controllo. L'attributo accetta due parametri: il nome del tipo di effetto e una stringa univoca che consente di individuare l'effetto prima di applicarlo a un controllo.

L'effetto può quindi essere utilizzato associandolo al controllo appropriato.

> [!NOTE]
> La specifica di un effetto nel progetto di ogni piattaforma è facoltativa. Se si tenta di usare un effetto quando non ne sono stati registrati, verrà restituito un valore non null che non esegue alcuna operazione.

L'applicazione di esempio illustra un `FocusEffect` che cambia il colore di sfondo di un controllo quando ottiene lo stato attivo. Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](creating-images/focus-effect.png "Focus Effect Project Responsibilities")

Un [`Entry`](xref:Xamarin.Forms.Entry) controllo `HomePage` nella classe viene `FocusEffect` personalizzato dalla classe in ogni progetto specifico della piattaforma. Ogni classe `FocusEffect` deriva dalla classe `PlatformEffect` per ogni piattaforma. Di conseguenza il rendering di ogni controllo `Entry` viene eseguito con un colore di sfondo specifico della piattaforma, che cambia quando il controllo ottiene lo stato attivo, come illustrato negli screenshot seguenti:

![](creating-images/screenshots-1.png "Focus Effect on each Platform")
![](creating-images/screenshots-2.png "Focus Effect on each Platform")

## <a name="creating-the-effect-on-each-platform"></a>Creazione dell'effetto in ogni piattaforma

Le sezioni seguenti illustrano l'implementazione specifica per la piattaforma della classe `FocusEffect`.

## <a name="ios-project"></a>Progetto iOS

L'esempio di codice seguente mostra l'implementazione di `FocusEffect` per il progetto iOS:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(EffectsDemo.iOS.FocusEffect), nameof(EffectsDemo.iOS.FocusEffect))]
namespace EffectsDemo.iOS
{
    public class FocusEffect : PlatformEffect
    {
        UIColor backgroundColor;

        protected override void OnAttached ()
        {
            try {
                Control.BackgroundColor = backgroundColor = UIColor.FromRGB (204, 153, 255);
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);

            try {
                if (args.PropertyName == "IsFocused") {
                    if (Control.BackgroundColor == backgroundColor) {
                        Control.BackgroundColor = UIColor.White;
                    } else {
                        Control.BackgroundColor = backgroundColor;
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

Il metodo`OnAttached` imposta la proprietà `BackgroundColor` del controllo su viola chiaro con il metodo `UIColor.FromRGB` e archivia questo colore in un campo. Questa funzionalità è incapsulata in un blocco `try`/`catch`, nell'evenienza in cui il controllo a cui l'effetto è associato non avesse una proprietà `BackgroundColor`. Il metodo `OnDetached` non fornisce alcuna implementazione perché non sono necessarie operazioni di pulizia.

L'override di `OnElementPropertyChanged` risponde alle modifiche delle proprietà associabili nel controllo di Xamarin.Forms. Quando [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) la proprietà `BackgroundColor` viene modificata, la proprietà del controllo viene modificata in bianco se il controllo ha lo stato attivo, in caso contrario viene modificato in viola chiaro. Questa funzionalità è incapsulata in un blocco `try`/`catch`, nell'evenienza in cui il controllo a cui l'effetto è associato non avesse una proprietà `BackgroundColor`.

## <a name="android-project"></a>Progetto Android

L'esempio di codice seguente mostra l'implementazione di `FocusEffect` per il progetto Android:

```csharp
using System;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(EffectsDemo.Droid.FocusEffect), nameof(EffectsDemo.Droid.FocusEffect))]
namespace EffectsDemo.Droid
{
    public class FocusEffect : PlatformEffect
    {
        Android.Graphics.Color originalBackgroundColor = new Android.Graphics.Color(0, 0, 0, 0);
        Android.Graphics.Color backgroundColor;

        protected override void OnAttached()
        {
            try
            {
                backgroundColor = Android.Graphics.Color.LightGreen;
                Control.SetBackgroundColor(backgroundColor);
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);
            try
            {
                if (args.PropertyName == "IsFocused")
                {
                    if (((Android.Graphics.Drawables.ColorDrawable)Control.Background).Color == backgroundColor)
                    {
                        Control.SetBackgroundColor(originalBackgroundColor);
                    }
                    else
                    {
                        Control.SetBackgroundColor(backgroundColor);
                    }
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

Il metodo `OnAttached` esegue una chiamata al metodo `SetBackgroundColor` per impostare il colore di sfondo del controllo su verde chiaro e archivia anche questo colore in un campo. Questa funzionalità è incapsulata in un blocco `try`/`catch`, nell'evenienza in cui il controllo a cui l'effetto è associato non avesse una proprietà `SetBackgroundColor`. Il metodo `OnDetached` non fornisce alcuna implementazione perché non sono necessarie operazioni di pulizia.

L'override di `OnElementPropertyChanged` risponde alle modifiche delle proprietà associabili nel controllo di Xamarin.Forms. Quando [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) la proprietà viene modificata, il colore di sfondo del controllo viene modificato in bianco se il controllo ha lo stato attivo, in caso contrario viene modificato in verde chiaro. Questa funzionalità è incapsulata in un blocco `try`/`catch`, nell'evenienza in cui il controllo a cui l'effetto è associato non avesse una proprietà `BackgroundColor`.

## <a name="universal-windows-platform-projects"></a>Progetti della piattaforma UWP (Universal Windows Platform)

L'esempio di codice seguente mostra l'implementazione di `FocusEffect` per il progetto UWP (Universal Windows Platform):

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(EffectsDemo.UWP.FocusEffect), nameof(EffectsDemo.UWP.FocusEffect))]
namespace EffectsDemo.UWP
{
    public class FocusEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            try
            {
                (Control as Windows.UI.Xaml.Controls.Control).Background = new SolidColorBrush(Colors.Cyan);
                (Control as FormsTextBox).BackgroundFocusBrush = new SolidColorBrush(Colors.White);
            }
            catch (Exception ex)
            {
                Debug.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }
    }
}
```

Il metodo `OnAttached` imposta la proprietà `Background` del controllo su ciano e imposta la proprietà `BackgroundFocusBrush` su bianco. Questa funzionalità è incapsulata in un blocco `try`/`catch`, nell'evenienza in cui il controllo a cui l'effetto è associato non avesse queste proprietà. Il metodo `OnDetached` non fornisce alcuna implementazione perché non sono necessarie operazioni di pulizia.

## <a name="consuming-the-effect"></a>Utilizzo dell'effetto

Il processo per l'utilizzo di un effetto da un progetto di libreria condiviso o da una libreria .NET Standard di Xamarin.Forms è come segue:

1. Dichiarare un controllo che verrà personalizzato dall'effetto.
1. Associare l'effetto al controllo aggiungendolo [`Effects`](xref:Xamarin.Forms.Element.Effects) all'insieme del controllo.

> [!NOTE]
> Un'istanza dell'effetto può essere associata solo a un singolo controllo. Pertanto, per poter essere usato su due controlli, un effetto deve essere risolto due volte.

## <a name="consuming-the-effect-in-xaml"></a>Utilizzo dell'effetto in XAML

Nell'esempio di codice [`Entry`](xref:Xamarin.Forms.Entry) XAML seguente `FocusEffect` viene illustrato un controllo a cui è associato l'oggetto :

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

La classe `FocusEffect` nella libreria .NET Standard supporta l'utilizzo dell'effetto in XAML, come viene illustrato nell'esempio di codice seguente:

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ($"MyCompany.{nameof(FocusEffect)}")
    {
    }
}
```

La `FocusEffect` classe sottoclassi della [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) classe , che rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno che in genere è specifico della piattaforma. La `FocusEffect` classe chiama il costruttore della classe base, passando un parametro costituito [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) da una concatenazione del nome del [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) gruppo di risoluzione (specificato utilizzando l'attributo nella classe effect) e dell'ID univoco specificato utilizzando l'attributo nella classe effect. Pertanto, [`Entry`](xref:Xamarin.Forms.Entry) quando l'oggetto viene inizializzato `MyCompany.FocusEffect` in fase di esecuzione, una nuova istanza di viene aggiunta alla raccolta del [`Effects`](xref:Xamarin.Forms.Element.Effects) controllo.

Gli effetti possono inoltre essere associati ai controlli per mezzo di un comportamento o utilizzando le proprietà associate. Per altre informazioni sull'associazione di un effetto a un controllo utilizzando un comportamento, vedere [Reusable EffectBehavior](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md) (EffectBehavior riutilizzabile). Per altre informazioni sull'associazione di un effetto a un controllo usando le proprietà associate, vedere [Passing Parameters to an Effect](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md) (Passare parametri a un effetto).

## <a name="consuming-the-effect-in-cnum"></a>Utilizzo dell'effetto in C&num;

Nell'esempio di codice seguente viene illustrato l'equivalente [`Entry`](xref:Xamarin.Forms.Entry) in C:

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

L'oggetto `FocusEffect` viene `Entry` associato all'istanza aggiungendo l'effetto all'insieme del controllo, come illustrato nell'esempio di codice seguente:The is attached to the instance by adding the effect to the control's [`Effects`](xref:Xamarin.Forms.Element.Effects) collection, as demonstrated in the following code example:

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ($"MyCompany.{nameof(FocusEffect)}"));
  ...
}
```

Restituisce [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) [`Effect`](xref:Xamarin.Forms.Effect) un per il nome specificato, ovvero una concatenazione del [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) nome del gruppo di risoluzione (specificato [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) utilizzando l'attributo nella classe effect) e dell'ID univoco specificato utilizzando l'attributo nella classe effect. Se una piattaforma non fornisce l'effetto, il metodo `Effect.Resolve` restituirà un valore non `null`.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come creare [`Entry`](xref:Xamarin.Forms.Entry) un effetto che modifica il colore di sfondo del controllo quando il controllo ottiene lo stato attivo.

## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effetto](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [Background Color Effect (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-backgroundcoloreffect) (Effetto colore di sfondo)
- [Focus Effect (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect) (Effetto stato attivo)
