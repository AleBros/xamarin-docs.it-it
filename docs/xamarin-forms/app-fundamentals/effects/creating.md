---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0ed11ccacec73dd3c5c220c41bffd090af6c5b27
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139386"
---
# <a name="creating-an-effect"></a>Creazione di un effetto

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect)

_Gli effetti semplificano la personalizzazione di un controllo. Questo articolo illustra come creare un effetto che modifica il colore di sfondo del controllo entry quando il controllo ottiene lo stato attivo._

Il processo per la creazione di un effetto in ogni progetto specifico della piattaforma è come segue:

1. Creare una sottoclasse della classe `PlatformEffect`.
1. Eseguire l'override del metodo `OnAttached` scrivere la logica per personalizzare il controllo.
1. Eseguire l'override del metodo `OnDetached` e scrivere la logica per pulire la personalizzazione del controllo, se necessario.
1. Aggiungere un [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) attributo alla classe Effect. Questo attributo imposta uno spazio dei nomi a livello di società per gli effetti, per evitare che si verifichino conflitti con altri effetti omonimi. Si noti che questo attributo può essere applicato solo una volta per ogni progetto.
1. Aggiungere un [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) attributo alla classe Effect. Questo attributo registra l'effetto con un ID univoco usato da Xamarin.Forms , insieme al nome del gruppo, per individuare l'effetto prima di applicarlo a un controllo. L'attributo accetta due parametri: il nome del tipo di effetto e una stringa univoca che consente di individuare l'effetto prima di applicarlo a un controllo.

L'effetto può quindi essere utilizzato associandolo al controllo appropriato.

> [!NOTE]
> La specifica di un effetto nel progetto di ogni piattaforma è facoltativa. Se si tenta di usare un effetto quando non ne sono stati registrati, verrà restituito un valore non null che non esegue alcuna operazione.

L'applicazione di esempio illustra un `FocusEffect` che cambia il colore di sfondo di un controllo quando ottiene lo stato attivo. Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](creating-images/focus-effect.png "Focus Effect Project Responsibilities")

Un [`Entry`](xref:Xamarin.Forms.Entry) controllo su `HomePage` viene personalizzato dalla `FocusEffect` classe in ogni progetto specifico della piattaforma. Ogni classe `FocusEffect` deriva dalla classe `PlatformEffect` per ogni piattaforma. Di conseguenza il rendering di ogni controllo `Entry` viene eseguito con un colore di sfondo specifico della piattaforma, che cambia quando il controllo ottiene lo stato attivo, come illustrato negli screenshot seguenti:

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

L' `OnElementPropertyChanged` override risponde alle modifiche delle proprietà associabili nel Xamarin.Forms controllo. Quando la [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) proprietà viene modificata, la `BackgroundColor` proprietà del controllo viene modificata in bianco se il controllo ha lo stato attivo; in caso contrario, viene modificato in viola chiaro. Questa funzionalità è incapsulata in un blocco `try`/`catch`, nell'evenienza in cui il controllo a cui l'effetto è associato non avesse una proprietà `BackgroundColor`.

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

L' `OnElementPropertyChanged` override risponde alle modifiche delle proprietà associabili nel Xamarin.Forms controllo. Quando la [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) proprietà viene modificata, il colore di sfondo del controllo viene modificato in bianco se il controllo ha lo stato attivo; in caso contrario, viene modificato in verde chiaro. Questa funzionalità è incapsulata in un blocco `try`/`catch`, nell'evenienza in cui il controllo a cui l'effetto è associato non avesse una proprietà `BackgroundColor`.

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

Di seguito viene descritto il processo per l'utilizzo di un effetto da una Xamarin.Forms libreria .NET standard o da un progetto di libreria condivisa:

1. Dichiarare un controllo che verrà personalizzato dall'effetto.
1. Collega l'effetto al controllo aggiungendolo alla raccolta del controllo [`Effects`](xref:Xamarin.Forms.Element.Effects) .

> [!NOTE]
> Un'istanza dell'effetto può essere associata solo a un singolo controllo. Pertanto, per poter essere usato su due controlli, un effetto deve essere risolto due volte.

## <a name="consuming-the-effect-in-xaml"></a>Utilizzo dell'effetto in XAML

Nell'esempio di codice XAML seguente viene illustrato un [`Entry`](xref:Xamarin.Forms.Entry) controllo a cui `FocusEffect` è associato l'oggetto:

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

La `FocusEffect` classe sottoclassi della [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) classe, che rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno che in genere è specifico della piattaforma. La `FocusEffect` classe chiama il costruttore della classe base, passando un parametro costituito da una concatenazione del nome del gruppo di risoluzione (specificato utilizzando l' [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) attributo nella classe Effect) e dall'ID univoco specificato utilizzando l' [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) attributo nella classe Effect. Pertanto, quando [`Entry`](xref:Xamarin.Forms.Entry) viene inizializzato in fase di esecuzione, una nuova istanza di `MyCompany.FocusEffect` viene aggiunta alla raccolta del controllo [`Effects`](xref:Xamarin.Forms.Element.Effects) .

Gli effetti possono inoltre essere associati ai controlli per mezzo di un comportamento o utilizzando le proprietà associate. Per altre informazioni sull'associazione di un effetto a un controllo utilizzando un comportamento, vedere [Reusable EffectBehavior](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md) (EffectBehavior riutilizzabile). Per altre informazioni sull'associazione di un effetto a un controllo usando le proprietà associate, vedere [Passing Parameters to an Effect](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md) (Passare parametri a un effetto).

## <a name="consuming-the-effect-in-cnum"></a>Utilizzo dell'effetto in C&num;

L'equivalente [`Entry`](xref:Xamarin.Forms.Entry) in C# è illustrato nell'esempio di codice seguente:

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

`FocusEffect`Viene collegato all' `Entry` istanza di aggiungendo l'effetto alla raccolta del controllo [`Effects`](xref:Xamarin.Forms.Element.Effects) , come illustrato nell'esempio di codice seguente:

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ($"MyCompany.{nameof(FocusEffect)}"));
  ...
}
```

[`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String))Restituisce un oggetto [`Effect`](xref:Xamarin.Forms.Effect) per il nome specificato, ovvero una concatenazione del nome del gruppo di risoluzione (specificato utilizzando l' [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) attributo sulla classe Effect) e l'ID univoco specificato utilizzando l' [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) attributo nella classe Effect. Se una piattaforma non fornisce l'effetto, il metodo `Effect.Resolve` restituirà un valore non `null`.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare un effetto che modifica il colore di sfondo del [`Entry`](xref:Xamarin.Forms.Entry) controllo quando il controllo ottiene lo stato attivo.

## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effetto](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [Background Color Effect (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-backgroundcoloreffect) (Effetto colore di sfondo)
- [Focus Effect (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect) (Effetto stato attivo)
