---
title: Creazione di un effetto
description: Gli effetti di semplificare la personalizzazione di un controllo. Questo articolo illustra come creare un effetto che cambia il colore di sfondo del controllo voce quando il controllo riceve lo stato attivo.
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: b29d83999724a35293882f7b9efc0158171c4fd2
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998160"
---
# <a name="creating-an-effect"></a>Creazione di un effetto

_Gli effetti di semplificare la personalizzazione di un controllo. Questo articolo illustra come creare un effetto che cambia il colore di sfondo del controllo voce quando il controllo riceve lo stato attivo._

Il processo per la creazione di un effetto in ogni progetto specifico della piattaforma è come segue:

1. Creare una sottoclasse del `PlatformEffect` classe.
1. Eseguire l'override di `OnAttached` per la logica al metodo e scrittura per personalizzare il controllo.
1. Eseguire l'override di `OnDetached` per la logica al metodo e scrittura per pulire la personalizzazione dei controlli, se necessario.
1. Aggiungere un [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) attributo alla classe effettiva. Questo attributo imposta uno spazio dei nomi a livello di società per gli effetti, evitare conflitti con altri effetti con lo stesso nome. Si noti che questo attributo può essere applicato solo una volta per ogni progetto.
1. Aggiungere un [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) attributo alla classe effettiva. Questo attributo registra l'effetto con un ID univoco che viene usato da xamarin. Forms, insieme al nome di gruppo, per individuare l'effetto prima di applicarla a un controllo. L'attributo accetta due parametri: il nome del tipo di effetto e una stringa univoca che verrà usata per individuare l'effetto prima di applicarla a un controllo.

L'effetto può quindi essere utilizzato associandolo al controllo appropriato.

> [!NOTE]
> È facoltativo per fornire un effetto in ogni progetto della piattaforma. Tenta di usare un effetto di quando uno non è registrato, restituirà un valore non null che non esegue alcuna operazione.

L'applicazione di esempio viene illustrato un `FocusEffect` che cambia il colore di sfondo di un controllo quando ottiene lo stato attivo. Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](creating-images/focus-effect.png "Responsabilità di progetto effetto messa a fuoco")

Un' [ `Entry` ](xref:Xamarin.Forms.Entry) control per il `HomePage` personalizzato dal `FocusEffect` classe in ogni progetto specifico della piattaforma. Ciascuna `FocusEffect` deriva dalla classe di `PlatformEffect` classe per ogni piattaforma. Di conseguenza il `Entry` controllo viene eseguito il rendering con un colore di sfondo specifico della piattaforma, che cambia quando il controllo riceve lo stato attivo, come illustrato negli screenshot seguenti:

![](creating-images/screenshots-1.png "Effetto su ogni piattaforma di concentrarsi")
![](creating-images/screenshots-2.png "concentrarsi effetto in ogni piattaforma")

## <a name="creating-the-effect-on-each-platform"></a>Creare l'effetto in ogni piattaforma

Le sezioni seguenti illustrano l'implementazione specifica della piattaforma del `FocusEffect` classe.

## <a name="ios-project"></a>Progetto iOS

Nell'esempio di codice riportato di seguito viene illustrato il `FocusEffect` implementazione per il progetto iOS:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(FocusEffect), "FocusEffect")]
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

Il `OnAttached` metodo imposta la `BackgroundColor` proprietà del controllo in viola chiaro con la `UIColor.FromRGB` (metodo) e archivia anche questo colore in un campo. Questa funzionalità viene eseguito il wrapping un `try` / `catch` blocco nel caso in cui il controllo è collegata l'effetto non dispone di un `BackgroundColor` proprietà. Nessuna implementazione avviene tramite il `OnDetached` metodo perché non è necessaria alcuna operazione di pulizia.

Il `OnElementPropertyChanged` override risponde alle modifiche di proprietà associabili del controllo di xamarin. Forms. Quando la [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused) le modifiche alle proprietà, il `BackgroundColor` proprietà del controllo viene modificato al bianco se il controllo ha lo stato attivo, in caso contrario viene modificato in viola chiaro. Questa funzionalità viene eseguito il wrapping un `try` / `catch` blocco nel caso in cui il controllo è collegata l'effetto non dispone di un `BackgroundColor` proprietà.

## <a name="android-project"></a>Progetto Android

Nell'esempio di codice riportato di seguito viene illustrato il `FocusEffect` implementazione per il progetto Android:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.Droid
{
    public class FocusEffect : PlatformEffect
    {
        Android.Graphics.Color backgroundColor;

        protected override void OnAttached ()
        {
            try {
                backgroundColor = Android.Graphics.Color.LightGreen;
                Control.SetBackgroundColor (backgroundColor);

            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);
            try {
                if (args.PropertyName == "IsFocused") {
                    if (((Android.Graphics.Drawables.ColorDrawable)Control.Background).Color == backgroundColor) {
                        Control.SetBackgroundColor (Android.Graphics.Color.Black);
                    } else {
                        Control.SetBackgroundColor (backgroundColor);
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

Il `OnAttached` chiamate al metodo il `SetBackgroundColor` metodo per impostare il colore di sfondo del controllo alla luce verde e archivia anche questo colore in un campo. Questa funzionalità viene eseguito il wrapping un `try` / `catch` blocco nel caso in cui il controllo è collegata l'effetto non dispone di un `SetBackgroundColor` proprietà. Nessuna implementazione avviene tramite il `OnDetached` metodo perché non è necessaria alcuna operazione di pulizia.

Il `OnElementPropertyChanged` override risponde alle modifiche di proprietà associabili del controllo di xamarin. Forms. Quando la [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused) le modifiche alle proprietà, il colore di sfondo del controllo viene modificato al bianco se il controllo ha lo stato attivo, in caso contrario diventa verde chiaro. Questa funzionalità viene eseguito il wrapping un `try` / `catch` blocco nel caso in cui il controllo è collegata l'effetto non dispone di un `BackgroundColor` proprietà.

## <a name="universal-windows-platform-projects"></a>Progetti di Universal Windows Platform

Nell'esempio di codice riportato di seguito viene illustrato il `FocusEffect` implementazione per i progetti Universal Windows Platform (UWP):

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(FocusEffect), "FocusEffect")]
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

Il `OnAttached` metodo imposta la `Background` proprietà del controllo ciano, e i set di `BackgroundFocusBrush` proprietà verso il bianco. Questa funzionalità viene eseguito il wrapping un `try` / `catch` blocco nel caso in cui il controllo è collegata l'effetto non dispone di queste proprietà. Nessuna implementazione avviene tramite il `OnDetached` metodo perché non è necessaria alcuna operazione di pulizia.

## <a name="consuming-the-effect"></a>L'effetto di utilizzo

Il processo per l'utilizzo di un effetto da un progetto di libreria condivisa o la libreria xamarin. Forms .NET Standard è come segue:

1. Dichiarare un controllo personalizzato per l'effetto.
1. Collegare l'effetto sul controllo, aggiungerlo al controllo [ `Effects` ](xref:Xamarin.Forms.Element.Effects) raccolta.

> [!NOTE]
> Un'istanza di effetto può essere associata solo a un singolo controllo. Pertanto, un effetto deve essere risolto due volte per usarlo in due controlli.

## <a name="consuming-the-effect-in-xaml"></a>Utilizzo l'effetto in XAML

L'esempio di codice XAML seguente mostra un' [ `Entry` ](xref:Xamarin.Forms.Entry) controllo a cui il `FocusEffect` collegato:

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

Il `FocusEffect` classe nella libreria .NET Standard supporta il consumo di effetto in XAML e viene illustrato nell'esempio di codice seguente:

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ("MyCompany.FocusEffect")
    {
    }
}
```

Il `FocusEffect` classe sottoclassi il [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) classe che rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno che è in genere specifica della piattaforma. Il `FocusEffect` classe chiama il costruttore di classe di base, passando un parametro costituito da una concatenazione del nome del gruppo di risoluzione (specificata tramite il [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) attributo della classe effetto), e l'ID univoco che è stato specificato usando il [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) attributo della classe effettiva. Pertanto, quando la [ `Entry` ](xref:Xamarin.Forms.Entry) viene inizializzata in fase di esecuzione, una nuova istanza della `MyCompany.FocusEffect` viene aggiunto al controllo [ `Effects` ](xref:Xamarin.Forms.Element.Effects) raccolta.

Gli effetti possono inoltre essere collegati ai controlli con un comportamento o utilizzando le proprietà associate. Per altre informazioni sul collegamento di un effetto a un controllo utilizzando un comportamento, vedere [riutilizzabile EffectBehavior](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md). Per altre informazioni sul collegamento di un effetto a un controllo usando le proprietà associate, vedere [passaggio di parametri per un effetto](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md).

## <a name="consuming-the-effect-in-cnum"></a>Utilizzo l'effetto in C&num;

L'equivalente [ `Entry` ](xref:Xamarin.Forms.Entry) nel linguaggio c# viene illustrato nell'esempio di codice seguente:

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

Il `FocusEffect` collegata al `Entry` istanza aggiungendo l'effetto del controllo [ `Effects` ](xref:Xamarin.Forms.Element.Effects) insieme, come illustrato nell'esempio di codice seguente:

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ("MyCompany.FocusEffect"));
  ...
}
```

Il [ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String)) restituisce un [ `Effect` ](xref:Xamarin.Forms.Effect) per il nome specificato, che è una concatenazione del nome del gruppo risoluzione (specificato utilizzando il [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) attributo della classe effetto) e l'ID univoco che è stato specificato usando il [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) attributo della classe effettiva. Se una piattaforma non fornisce l'effetto, la `Effect.Resolve` metodo verrà restituito un non -`null` valore.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come creare un effetto che cambia il colore di sfondo di [ `Entry` ](xref:Xamarin.Forms.Entry) controllare quando il controllo riceve lo stato attivo.


## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effetto](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [Effetto colore di sfondo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/effects/backgroundcoloreffect/)
- [Effetto messa a fuoco (esempio)](https://developer.xamarin.com/samples/xamarin-forms/effects/focuseffect/)
