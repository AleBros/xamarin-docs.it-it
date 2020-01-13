---
title: Eventi touch e movimenti in Xamarin.iOS
description: Questo documento descrive come usare eventi di tocco, multitocco, movimenti, più movimenti e movimenti personalizzati nelle applicazioni Xamarin.iOS.
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 9dd06044ea9c4bf28e785932d316b3222c6cd16b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009150"
---
# <a name="touch-events-and-gestures-in-xamarinios"></a>Eventi touch e movimenti in Xamarin.iOS

È importante comprendere gli eventi di tocco e le API Touch in un'applicazione iOS, in quanto sono centrali per tutte le interazioni fisiche con il dispositivo. Tutte le interazioni con il tocco coinvolgono un oggetto `UITouch`. In questo articolo verrà illustrato come usare la classe `UITouch` e le relative API per supportare il tocco. In seguito, si espanderanno le proprie conoscenze per apprendere come supportare i movimenti.

## <a name="enabling-touch"></a>Abilitazione tocco

I controlli in `UIKit`, quelli sottoclassati da UIControl, dipendono dall'interazione dell'utente con i movimenti incorporati in UIKit e pertanto non è necessario abilitare il tocco. È già abilitato.

Tuttavia, molte delle visualizzazioni in `UIKit` non dispongono di tocco abilitato per impostazione predefinita. Esistono due modi per abilitare il tocco su un controllo. Il primo consiste nel controllare la casella di controllo interazione utente abilitata nel riquadro delle proprietà di iOS designer, come illustrato nello screenshot seguente:

 [![](touch-in-ios-images/image1.png "Check the User Interaction Enabled checkbox in the Property Pad of the iOS Designer")](touch-in-ios-images/image1.png#lightbox)

È anche possibile usare un controller per impostare la proprietà `UserInteractionEnabled` su true in una classe `UIView`. Questa operazione è necessaria se l'interfaccia utente viene creata nel codice.

La riga di codice seguente è un esempio:

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>Eventi di tocco

Ci sono tre fasi di tocco che si verificano quando l'utente tocca lo schermo, sposta il dito o rimuove il dito. Questi metodi sono definiti in `UIResponder`, che è la classe di base per UIView. iOS sostituirà i metodi associati nell'`UIView` e il `UIViewController` per gestire il tocco:

- `TouchesBegan`: viene chiamato quando lo schermo viene toccato per la prima volta.
- `TouchesMoved`: questo metodo viene chiamato quando viene modificata la posizione del tocco quando l'utente scorre le dita intorno allo schermo.
- `TouchesEnded` o `TouchesCancelled`: viene chiamato `TouchesEnded` quando le dita dell'utente vengono sollevate dallo schermo.  `TouchesCancelled` viene chiamato se iOS Annulla il tocco, ad esempio se un utente scorre il dito da un pulsante per annullare una pressione.

Gli eventi Touch passano in modo ricorsivo attraverso lo stack di UIViews, per verificare se l'evento Touch si trova all'interno dei limiti di un oggetto visualizzazione. Questa operazione viene spesso denominata _hit testing_. Verranno prima chiamati sulla `UIView` o `UIViewController` di primo livello, quindi verranno chiamati sulla `UIView` e `UIViewControllers` sotto di essi nella gerarchia di visualizzazione.

Verrà creato un oggetto `UITouch` ogni volta che l'utente tocca lo schermo. L'oggetto `UITouch` include i dati sul tocco, ad esempio il momento in cui si è verificato il tocco, la posizione in cui si è verificato il tocco e così via. Agli eventi Touch viene passata una proprietà touchs, un `NSSet` contenente uno o più tocchi. È possibile usare questa proprietà per ottenere un riferimento a un tocco e determinare la risposta dell'applicazione.

Le classi che eseguono l'override di uno degli eventi touch devono prima chiamare l'implementazione di base e quindi ottenere l'oggetto `UITouch` associato all'evento. Per ottenere un riferimento al primo tocco, chiamare la proprietà `AnyObject` e eseguirne il cast come `UITouch` come illustrato nell'esempio seguente:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        //code here to handle touch
    }
}
```

iOS riconosce automaticamente i ritocchi rapidi successivi sullo schermo e li raccoglie tutti come un unico tocco in un singolo oggetto `UITouch`. In questo modo il controllo di un doppio tocco è semplice come il controllo della proprietà `TapCount`, come illustrato nel codice seguente:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        if (touch.TapCount == 2)
        {
            // do something with the double touch.
        }
    }
}
```

## <a name="multi-touch"></a>Funzionalità multitocco

Il multitocco non è abilitato per impostazione predefinita nei controlli. È possibile abilitare la funzionalità multitocco in iOS designer, come illustrato nello screenshot seguente:

 [![](touch-in-ios-images/image2.png "Multi-touch enabled in the iOS Designer")](touch-in-ios-images/image2.png#lightbox)

È anche possibile impostare la funzionalità multitocco a livello di codice impostando la proprietà `MultipleTouchEnabled` come illustrato nella riga di codice seguente:

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

Per determinare il numero di dita che hanno toccato lo schermo, usare la proprietà `Count` sulla proprietà `UITouch`:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>Determinazione della posizione del tocco

Il metodo `UITouch.LocationInView` restituisce un oggetto CGPoint che include le coordinate del tocco in una visualizzazione specificata. Inoltre, è possibile verificare se tale posizione si trova all'interno di un controllo chiamando il metodo `Frame.Contains`. Il frammento di codice seguente mostra un esempio di questo:

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

Ora che sono state apprese le informazioni sugli eventi di tocco in iOS, verranno fornite informazioni sui riconoscitori dei movimenti.

## <a name="gesture-recognizers"></a>Riconoscitori di movimento

I riconoscitori dei movimenti possono semplificare e ridurre notevolmente l'impegno di programmazione per supportare il tocco in un'applicazione. i riconoscitori di movimento iOS aggregano una serie di eventi di tocco in un singolo evento Touch.

Xamarin.iOS fornisce la classe `UIGestureRecognizer` come classe di base per i riconoscitori di movimento predefiniti seguenti:

- *UITapGestureRecognizer* : questa operazione è per uno o più rubinetti.
- *UIPinchGestureRecognizer* : pizzicare e diffondere le dita.
- *UIPanGestureRecognizer* : panoramica o trascinamento.
- *UISwipeGestureRecognizer* : scorrimento in qualsiasi direzione.
- *UIRotationGestureRecognizer* : rotazione di due dita in un movimento in senso orario o antiorario.
- *UILongPressGestureRecognizer* : premere e tenere premuto, a volte a volte con un lungo clic.

Il modello di base per l'uso di un riconoscimento di movimento è il seguente:

1. **Creare un'istanza del riconoscitore di movimento** , innanzitutto creare un'istanza di una sottoclasse `UIGestureRecognizer`. L'oggetto di cui viene creata un'istanza verrà associato a una vista e verrà sottoposto a Garbage Collection quando la vista viene eliminata. Non è necessario creare questa vista come variabile a livello di classe.
1. **Configurare le impostazioni di movimento** : il passaggio successivo consiste nel configurare il riconoscimento del movimento. Per un elenco delle proprietà che è possibile impostare per controllare il comportamento di un'istanza di `UIGestureRecognizer`, vedere la documentazione di Novell su `UIGestureRecognizer` e le relative sottoclassi.
1. **Configurare la destinazione** : a causa del patrimonio Objective-C, Xamarin.iOS non genera eventi quando un riconoscimento di movimento corrisponde a un movimento.  `UIGestureRecognizer` dispone di un metodo, `AddTarget`, che può accettare un delegato anonimo o un selettore Objective-C con il codice da eseguire quando il riconoscitore di movimento crea una corrispondenza.
1. **Abilita riconoscimento movimento** : Analogamente agli eventi tocco, i movimenti sono riconosciuti solo se le interazioni tocco sono abilitate.
1. **Aggiungere il riconoscitore di movimento alla visualizzazione** . il passaggio finale consiste nell'aggiungere il movimento a una vista chiamando `View.AddGestureRecognizer` e passandogli un oggetto del riconoscimento di movimento.

Per ulteriori informazioni su come implementarli nel codice, vedere gli [esempi di riconoscimento dei movimenti](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples) .

Quando viene chiamata la destinazione del movimento, viene passato un riferimento al movimento che si è verificato. Ciò consente alla destinazione del movimento di ottenere informazioni sul movimento che si è verificato. La quantità di informazioni disponibili dipende dal tipo di riconoscimento dei movimenti utilizzato. Vedere la documentazione di Novell per informazioni sui dati disponibili per ogni sottoclasse `UIGestureRecognizer`.

È importante ricordare che, una volta che un riconoscitore di movimento è stato aggiunto a una vista, la vista (e tutte le visualizzazioni sottostanti) non riceverà alcun evento Touch. Per consentire gli eventi Touch contemporaneamente con i movimenti, la proprietà `CancelsTouchesInView` deve essere impostata su false, come illustrato nel codice seguente:

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

Ogni `UIGestureRecognizer` dispone di una proprietà state che fornisce informazioni importanti sullo stato del riconoscimento di movimento. Ogni volta che viene modificato il valore di questa proprietà, iOS chiamerà il metodo di sottoscrizione per dargli un aggiornamento. Se un riconoscimento di movimenti personalizzato non aggiorna mai la proprietà state, il Sottoscrittore non viene mai chiamato, rendendo inutile il riconoscimento dei movimenti.

I movimenti possono essere riepilogati come uno dei due tipi seguenti:

1. *Discreto* : questi movimenti vengono generati solo la prima volta che vengono riconosciuti.
1. *Continuo* : questi movimenti continuano a essere attivati fino a quando vengono riconosciuti.

I riconoscitori di movimento esistono in uno degli Stati seguenti:

- *Possibile* : si tratta dello stato iniziale di tutti i riconoscitori di movimento. Si tratta del valore predefinito della proprietà state.
- *Avviato* : quando viene riconosciuto per la prima volta un movimento continuo, lo stato viene impostato su avviato. In questo modo, le sottoscrizioni consentono di distinguere tra l'avvio del riconoscimento del movimento e la modifica.
- *Modificato* : dopo l'inizio di un movimento continuo, ma non è stato completato, lo stato verrà impostato su modificato ogni volta che un tocco viene spostato o modificato, purché sia ancora compreso nei parametri previsti del movimento.
- *Annullato* : questo stato verrà impostato se il riconoscimento passa da inizio a modificato, quindi i tocchi cambiano in modo che non corrispondano più al modello del movimento.
- *Riconosciuta* : lo stato verrà impostato quando il riconoscimento del movimento corrisponde a un set di tocchi e indicherà al Sottoscrittore che il movimento è terminato.
- *Terminato* : si tratta di un alias per lo stato riconosciuto.
- *Operazione non riuscita* : quando il riconoscitore di movimento non è più in grado di corrispondere ai tocchi per cui è in ascolto, lo stato verrà modificato in non riuscito.

Xamarin.iOS rappresenta questi valori nell'enumerazione `UIGestureRecognizerState`.

## <a name="working-with-multiple-gestures"></a>Utilizzo di più movimenti

Per impostazione predefinita, iOS non consente l'esecuzione simultanea di movimenti predefiniti. Al contrario, ogni riconoscimento di movimento riceverà gli eventi di tocco in un ordine non deterministico. Il frammento di codice seguente illustra come eseguire contemporaneamente un riconoscimento di movimento:

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

È anche possibile disabilitare un movimento in iOS. Sono disponibili due proprietà delegate che consentono a un riconoscitore di movimenti di esaminare lo stato di un'applicazione e gli eventi di tocco correnti, per prendere decisioni su come e se deve essere riconosciuto un movimento. I due eventi sono:

1. *ShouldReceiveTouch* : questo delegato viene chiamato immediatamente prima che il riconoscitore di movimento venga passato a un evento Touch e offra la possibilità di esaminare i tocchi e decidere quali tocchi verranno gestiti dal riconoscimento di movimento.
1. *ShouldBegin* : viene chiamato quando un riconoscimento tenta di cambiare lo stato da possibile a un altro stato. Se si restituisce false, lo stato del riconoscimento del movimento verrà forzato a non riuscire.

È possibile eseguire l'override di questi metodi con una `UIGestureRecognizerDelegate`fortemente tipizzata, un delegato debole o un binding tramite la sintassi del gestore dell'evento, come illustrato nel frammento di codice seguente:

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

Infine, è possibile accodare un riconoscitore di movimento in modo che abbia esito positivo solo se si verifica un errore in un altro riconoscitore di movimento. Ad esempio, un singolo riconoscimento di movimento tocco dovrebbe avere esito positivo solo quando un riconoscimento del movimento del doppio tocco ha esito negativo. Il seguente frammento di codice fornisce un esempio di questo:

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>Creazione di un gesto personalizzato

Sebbene iOS fornisca alcuni riconoscitori di movimento predefiniti, potrebbe essere necessario creare i riconoscitori dei movimenti personalizzati in alcuni casi. La creazione di un riconoscimento di movimenti personalizzato prevede i passaggi seguenti:

1. Sottoclasse `UIGestureRecognizer`.
1. Eseguire l'override dei metodi dell'evento Touch appropriati.
1. Propagazione dello stato di riconoscimento tramite la proprietà di stato della classe base.

Un esempio pratico sarà illustrato nella procedura dettagliata [using touch in iOS](ios-touch-walkthrough.md) .
