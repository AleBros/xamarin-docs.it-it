---
title: Creazione di un effetto
description: Gli effetti semplificano la personalizzazione di un controllo. Questo articolo spiega come creare un effetto che cambia il colore di sfondo del controllo Entry quando il controllo riceve lo stato attivo.
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: f1c18c30a2da019fb9d1c09fac17c9f095dafedc
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739350"
---
# <a name="creating-an-effect"></a>Creazione di un effetto

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect)

_Gli effetti semplificano la personalizzazione di un controllo. Questo articolo spiega come creare un effetto che cambia il colore di sfondo del controllo Entry quando il controllo riceve lo stato attivo._

Il processo per la creazione di un effetto in ogni progetto specifico della piattaforma è come segue:

1. Creare una sottoclasse della classe `PlatformEffect`.
1. Eseguire l'override del metodo `OnAttached` scrivere la logica per personalizzare il controllo.
1. Eseguire l'override del metodo `OnDetached` e scrivere la logica per pulire la personalizzazione del controllo, se necessario.
1. Aggiungere un attributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) alla classe di effetto. Questo attributo imposta uno spazio dei nomi a livello di società per gli effetti, per evitare che si verifichino conflitti con altri effetti omonimi. Si noti che questo attributo può essere applicato solo una volta per ogni progetto.
1. Aggiungere un attributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) alla classe di effetto. Questo attributo registra l'effetto con un ID univoco che viene usato da Xamarin.Forms, insieme al nome gruppo, per individuare l'effetto prima di applicarlo a un controllo. L'attributo accetta due parametri: il nome del tipo di effetto e una stringa univoca che consente di individuare l'effetto prima di applicarlo a un controllo.

L'effetto può quindi essere utilizzato associandolo al controllo appropriato.

> [!NOTE]
> La specifica di un effetto nel progetto di ogni piattaforma è facoltativa. Se si tenta di usare un effetto quando non ne sono stati registrati, verrà restituito un valore non null che non esegue alcuna operazione.

L'applicazione di esempio illustra un `FocusEffect` che cambia il colore di sfondo di un controllo quando ottiene lo stato attivo. Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazione tra di esse:

![](creating-images/focus-effect.png "Responsabilità dei progetti per l'effetto stato attivo")

Un controllo [`Entry`](xref:Xamarin.Forms.Entry) in `HomePage` è personalizzato dalla classe `FocusEffect` in ogni progetto specifico della piattaforma. Ogni classe `FocusEffect` deriva dalla classe `PlatformEffect` per ogni piattaforma. Di conseguenza il rendering di ogni controllo `Entry` viene eseguito con un colore di sfondo specifico della piattaforma, che cambia quando il controllo ottiene lo stato attivo, come illustrato negli screenshot seguenti:

![](creating-images/screenshots-1.png "Effetto stato attivo su ogni piattaforma")
![](creating-images/screenshots-2.png "Effetto stato attivo su ogni piattaforma")

## <a name="creating-the-effect-on-each-platform"></a>Creazione dell'effetto in ogni piattaforma

Le sezioni seguenti illustrano l'implementazione specifica della piattaforma della classe `FocusEffect`.

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

L'override di `OnElementPropertyChanged` risponde alle modifiche delle proprietà associabili nel controllo di Xamarin.Forms. Quando la proprietà [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) cambia, la proprietà `BackgroundColor` del controllo imposta bianco, se il controllo ha lo stato attivo, oppure viola chiaro in caso contrario. Questa funzionalità è incapsulata in un blocco `try`/`catch`, nell'evenienza in cui il controllo a cui l'effetto è associato non avesse una proprietà `BackgroundColor`.

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

L'override di `OnElementPropertyChanged` risponde alle modifiche delle proprietà associabili nel controllo di Xamarin.Forms. Quando la proprietà [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) cambia, il colore di sfondo del controllo imposta bianco, se il controllo ha lo stato attivo, oppure verde chiaro in caso contrario. Questa funzionalità è incapsulata in un blocco `try`/`catch`, nell'evenienza in cui il controllo a cui l'effetto è associato non avesse una proprietà `BackgroundColor`.

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
1. Associare l'effetto al controllo aggiungendolo alla raccolta [`Effects`](xref:Xamarin.Forms.Element.Effects) del controllo.

> [!NOTE]
> Un'istanza dell'effetto può essere associata solo a un singolo controllo. Pertanto, per poter essere usato su due controlli, un effetto deve essere risolto due volte.

## <a name="consuming-the-effect-in-xaml"></a>Utilizzo dell'effetto in XAML

L'esempio di codice XAML seguente mostra un controllo [`Entry`](xref:Xamarin.Forms.Entry) a cui è associato `FocusEffect`:

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

La classe `FocusEffect` crea una sottoclasse della classe [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect), che rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno, in genere specifico della piattaforma. La classe `FocusEffect` chiama il costruttore della classe di base, passando un parametro costituito da una concatenazione del nome del gruppo di risoluzione, specificato usando l'attributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) della classe di effetto, e dell'ID univoco specificato usando l'attributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) sulla classe di effetto. Pertanto, quando il controllo [`Entry`](xref:Xamarin.Forms.Entry) viene inizializzato in fase di esecuzione, una nuova istanza di `MyCompany.FocusEffect` viene aggiunta alla raccolta [`Effects`](xref:Xamarin.Forms.Element.Effects) del controllo.

Gli effetti possono inoltre essere associati ai controlli per mezzo di un comportamento o utilizzando le proprietà associate. Per altre informazioni sull'associazione di un effetto a un controllo utilizzando un comportamento, vedere [Reusable EffectBehavior](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md) (EffectBehavior riutilizzabile). Per altre informazioni sull'associazione di un effetto a un controllo usando le proprietà associate, vedere [Passing Parameters to an Effect](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md) (Passare parametri a un effetto).

## <a name="consuming-the-effect-in-cnum"></a>Utilizzo dell'effetto in C&num;

Il codice C# equivalente per il controllo [`Entry`](xref:Xamarin.Forms.Entry) è visualizzato nell'esempio seguente:

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

`FocusEffect` viene associato all'istanza di `Entry` aggiungendo l'effetto alla raccolta [`Effects`](xref:Xamarin.Forms.Element.Effects) del controllo, come illustrato nell'esempio di codice seguente:

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ($"MyCompany.{nameof(FocusEffect)}"));
  ...
}
```

[`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) restituisce un valore [`Effect`](xref:Xamarin.Forms.Effect) per il nome specificato, costituito da una concatenazione del nome del gruppo di risoluzione, specificato usando l'attributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) della classe di effetto, e dell'ID univoco specificato usando l'attributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) sulla classe di effetto. Se una piattaforma non fornisce l'effetto, il metodo `Effect.Resolve` restituirà un valore non `null`.

## <a name="summary"></a>Riepilogo

Questo articolo ha spiegato come creare un effetto che cambia il colore di sfondo del controllo [`Entry`](xref:Xamarin.Forms.Entry) quando il controllo riceve lo stato attivo.


## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [Background Color Effect (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-backgroundcoloreffect) (Effetto colore di sfondo)
- [Focus Effect (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect) (Effetto stato attivo)
