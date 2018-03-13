---
title: Creazione di un effetto
description: Gli effetti di semplificare la personalizzazione di un controllo. In questo articolo viene illustrato come creare un effetto che modifica il colore di sfondo del controllo voce quando il controllo riceve lo stato attivo.
ms.topic: article
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: 3a66ec9f935159e4854a12584a6c9f70ab805abb
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="creating-an-effect"></a>Creazione di un effetto

_Gli effetti di semplificare la personalizzazione di un controllo. In questo articolo viene illustrato come creare un effetto che modifica il colore di sfondo del controllo voce quando il controllo riceve lo stato attivo._

Il processo per la creazione di un effetto in ogni progetto specifico della piattaforma è come segue:

1. Creare una sottoclasse di `PlatformEffect` classe.
1. Eseguire l'override di `OnAttached` (metodo) e scrittura logica per personalizzare il controllo.
1. Eseguire l'override di `OnDetached` (metodo) e scrittura logica per pulire la personalizzazione del controllo, se necessario.
1. Aggiungere un [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) attributo alla classe effettiva. Questo attributo imposta uno spazio dei nomi "wide" di società per ottenere effetti, prevenzione dei conflitti con altri effetti con lo stesso nome. Si noti che questo attributo può essere applicato solo una volta per ogni progetto.
1. Aggiungere un [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) attributo alla classe effettiva. Questo attributo registra l'effetto con un ID univoco utilizzato da xamarin. Forms, insieme al nome di gruppo, per individuare l'effetto prima di applicarla a un controllo. L'attributo accetta due parametri: il nome del tipo dell'effetto e una stringa univoca che verrà utilizzata per individuare l'effetto prima di applicarla a un controllo.

L'effetto può quindi essere usato da collegarlo al controllo appropriato.

> [!NOTE]
> È facoltativo per fornire un effetto in ogni progetto della piattaforma. Tentativo di utilizzare un effetto di quando uno non è registrato, restituirà un valore non null che non esegue alcuna operazione.

L'applicazione di esempio viene illustrato un `FocusEffect` che modifica il colore di sfondo di un controllo quando ottiene lo stato attivo. Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](creating-images/focus-effect.png "Responsabilità di progetto effetto lo stato attivo")

Un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control per il `HomePage` personalizzato dalla `FocusEffect` classe in ogni progetto specifico della piattaforma. Ogni `FocusEffect` deriva dalla classe di `PlatformEffect` classe per ogni piattaforma. Di conseguenza, il `Entry` controllo viene eseguito il rendering con un colore di sfondo specifico della piattaforma, che cambia quando il controllo riceve lo stato attivo, come illustrato nelle schermate seguenti:

![](creating-images/screenshots-1.png "Concentrarsi effetto in ciascuna piattaforma")
![](creating-images/screenshots-2.png "concentrarsi effetto in ciascuna piattaforma")

## <a name="creating-the-effect-on-each-platform"></a>Creare l'effetto in ciascuna piattaforma

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

Il `OnAttached` metodo imposta la `BackgroundColor` proprietà del controllo da viola chiaro con il `UIColor.FromRGB` (metodo) e archivia anche il colore in un campo. Questa funzionalità viene inserita in un `try` / `catch` blocco nel caso in cui il controllo è collegata l'effetto non dispone di un `BackgroundColor` proprietà. Viene fornita alcuna implementazione per il `OnDetached` metodo perché non è necessaria alcuna operazione di rimozione.

Il `OnElementPropertyChanged` override risponde alle modifiche di proprietà associabile nel controllo xamarin. Forms. Quando il [ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/) modifiche delle proprietà, il `BackgroundColor` proprietà del controllo viene modificato in vuoto se il controllo ha lo stato attivo, in caso contrario viene modificata in viola chiaro. Questa funzionalità viene inserita in un `try` / `catch` blocco nel caso in cui il controllo è collegata l'effetto non dispone di un `BackgroundColor` proprietà.

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

Il `OnAttached` chiamate al metodo di `SetBackgroundColor` per impostare il colore di sfondo del controllo da luce verde e archivia anche il colore in un campo. Questa funzionalità viene inserita in un `try` / `catch` blocco nel caso in cui il controllo è collegata l'effetto non dispone di un `SetBackgroundColor` proprietà. Viene fornita alcuna implementazione per il `OnDetached` metodo perché non è necessaria alcuna operazione di rimozione.

Il `OnElementPropertyChanged` override risponde alle modifiche di proprietà associabile nel controllo xamarin. Forms. Quando il [ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/) modifiche delle proprietà, il colore di sfondo del controllo viene impostato su vuoto se il controllo ha lo stato attivo, in caso contrario viene modificata in verde chiaro. Questa funzionalità viene inserita in un `try` / `catch` blocco nel caso in cui il controllo è collegata l'effetto non dispone di un `BackgroundColor` proprietà.

## <a name="windows-phone--universal-windows-platform-projects"></a>Windows Phone & progetti con piattaforma Windows universale

Nell'esempio di codice riportato di seguito viene illustrato il `FocusEffect` implementazione per i progetti Windows Phone e Windows piattaforma UWP (Universal):

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.WinRT;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.WinPhone81
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

Il `OnAttached` metodo imposta la `Background` proprietà del controllo da ciano e imposta il `BackgroundFocusBrush` proprietà vuoto. Questa funzionalità viene inserita in un `try` / `catch` blocco nel caso in cui il controllo è associato l'effetto di non dispone di queste proprietà. Viene fornita alcuna implementazione per il `OnDetached` metodo perché non è necessaria alcuna operazione di rimozione.

## <a name="consuming-the-effect"></a>L'effetto di utilizzo

Il processo per l'utilizzo di un effetto da una libreria di xamarin. Forms classe portabile (PCL) o un progetto di libreria condivisa è come segue:

1. Dichiarare un controllo che verrà personalizzato per l'effetto.
1. Collegare l'effetto del controllo aggiungendola al controllo [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) insieme.

> [!NOTE]
> Un'istanza effettiva può essere collegata solo a un singolo controllo. Pertanto, è necessario risolvere un effetto due volte per utilizzarlo in due controlli.

## <a name="consuming-the-effect-in-xaml"></a>Utilizzo l'effetto in XAML

Nell'esempio di codice XAML seguente viene un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllo a cui il `FocusEffect` è collegato:

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

La `FocusEffect` classe nella libreria di classi Portabile supporta l'utilizzo di effetto in XAML e viene illustrato nell'esempio di codice seguente:

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ("MyCompany.FocusEffect")
    {
    }
}
```

Il `FocusEffect` classe sottoclassi di [ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/) (classe), che rappresenta un effetto indipendente dalla piattaforma che esegue il wrapping di un effetto interno che è in genere specifica della piattaforma. Il `FocusEffect` classe chiama il costruttore di classe di base, passando un parametro costituito da una concatenazione del nome del gruppo di risoluzione (specificati mediante il [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) attributo sulla classe effetto), e l'ID univoco che è stato specificato mediante il [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) attributo sulla classe effettiva. Pertanto, quando il [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) viene inizializzata in fase di esecuzione, una nuova istanza di `MyCompany.FocusEffect` viene aggiunto al controllo [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) insieme.

Effetti possono inoltre essere collegati ai controlli utilizzando un comportamento o utilizzando le proprietà associate. Per ulteriori informazioni sulla connessione di un effetto a un controllo utilizzando un comportamento, vedere [EffectBehavior riutilizzabile](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md). Per ulteriori informazioni sulla connessione di un effetto a un controllo utilizzando le proprietà associate, vedere [passaggio di parametri per un effetto](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md).

## <a name="consuming-the-effect-in-cnum"></a>Utilizzo l'effetto in C&num;

L'equivalente [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) in c# è illustrato nell'esempio di codice seguente:

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

Il `FocusEffect` è collegato il `Entry` istanza aggiungendo l'effetto del controllo [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) insieme, come illustrato nell'esempio di codice seguente:

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ("MyCompany.FocusEffect"));
  ...
}
```

Il [ `Effect.Resolve` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.Resolve/p/System.String/) restituisce un [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) per il nome specificato, ovvero una concatenazione del nome del gruppo di risoluzione (specificati mediante il [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) attributo sulla classe effetto) e l'ID univoco specificata utilizzando il [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) attributo sulla classe effettiva. Se l'effetto, non ne fornisce una piattaforma di `Effect.Resolve` metodo verrà restituito un non -`null` valore.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come creare un effetto che modifica il colore di sfondo di [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllare quando il controllo riceve lo stato attivo.


## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effetto](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)
- [PlatformEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/)
- [Effetto colore di sfondo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/effects/backgroundcoloreffect/)
- [Lo stato attivo effetto (esempio)](https://developer.xamarin.com/samples/xamarin-forms/effects/focuseffect/)
