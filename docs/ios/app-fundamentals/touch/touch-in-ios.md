---
title: Eventi touch e movimenti in Novell. iOS
description: Questo documento descrive come usare eventi di tocco, multitocco, movimenti, più movimenti e movimenti personalizzati nelle applicazioni Novell. iOS.
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 577bc7af34c463aec65148bd97dc5dd49262d699
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767090"
---
# <a name="touch-events-and-gestures-in-xamarinios"></a>Eventi touch e movimenti in Novell. iOS

È importante comprendere gli eventi di tocco e le API Touch in un'applicazione iOS, in quanto sono centrali per tutte le interazioni fisiche con il dispositivo. Tutte le interazioni con tocco `UITouch` coinvolgono un oggetto. In questo articolo verrà illustrato come usare la classe e `UITouch` le relative API per supportare il tocco. In seguito, si espanderanno le proprie conoscenze per apprendere come supportare i movimenti.

## <a name="enabling-touch"></a>Abilitazione tocco

I controlli `UIKit` in, ovvero quelli sottoclassati da UIControl, dipendono dall'interazione dell'utente che hanno movimenti incorporati in UIKit e pertanto non è necessario abilitare il tocco. È già abilitato.

Tuttavia, molte delle visualizzazioni in `UIKit` non hanno il tocco abilitato per impostazione predefinita. Esistono due modi per abilitare il tocco su un controllo. Il primo consiste nel controllare la casella di controllo interazione utente abilitata nel riquadro delle proprietà di iOS designer, come illustrato nello screenshot seguente:

 [![](touch-in-ios-images/image1.png "Controllare la casella di controllo interazione utente abilitata nel riquadro delle proprietà di iOS designer")](touch-in-ios-images/image1.png#lightbox)

È anche possibile usare un controller per impostare la `UserInteractionEnabled` proprietà su true in una `UIView` classe. Questa operazione è necessaria se l'interfaccia utente viene creata nel codice.

La riga di codice seguente è un esempio:

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>Eventi di tocco

Ci sono tre fasi di tocco che si verificano quando l'utente tocca lo schermo, sposta il dito o rimuove il dito. Questi metodi sono definiti in `UIResponder`, che è la classe di base per UIView. iOS sostituirà i metodi associati in `UIView` e per gestire il `UIViewController` tocco:

- `TouchesBegan`: Questo metodo viene chiamato quando lo schermo viene toccato per la prima volta.
- `TouchesMoved`: Questo metodo viene chiamato quando viene modificata la posizione del tocco quando l'utente scorre le dita intorno allo schermo.
- `TouchesEnded`o `TouchesCancelled` :`TouchesEnded` viene chiamato quando le dita dell'utente vengono sollevate dallo schermo.  `TouchesCancelled`viene chiamato se iOS Annulla il tocco, ad esempio se un utente scorre il dito da un pulsante per annullare una pressione.

Gli eventi Touch passano in modo ricorsivo attraverso lo stack di UIViews, per verificare se l'evento Touch si trova all'interno dei limiti di un oggetto visualizzazione. Questa operazione viene spesso denominata _hit testing_. Verranno prima chiamati `UIView` in primo piano o `UIViewController` , quindi `UIView` verranno chiamati su e `UIViewControllers` sotto di essi nella gerarchia di visualizzazione.

Verrà `UITouch` creato un oggetto ogni volta che l'utente tocca lo schermo. L' `UITouch` oggetto include i dati sul tocco, ad esempio il momento in cui si è verificato il tocco, la posizione in cui si è verificato il tocco e così via. Agli eventi Touch viene passata una proprietà touchs, `NSSet` che contiene uno o più tocchi. È possibile usare questa proprietà per ottenere un riferimento a un tocco e determinare la risposta dell'applicazione.

Le classi che eseguono l'override di uno degli eventi touch devono prima chiamare l'implementazione di base `UITouch` e quindi ottenere l'oggetto associato all'evento. Per ottenere un riferimento al primo tocco, chiamare la `AnyObject` proprietà e eseguirne il cast `UITouch` come illustrato nell'esempio seguente:

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

iOS riconosce automaticamente i ritocchi rapidi successivi sullo schermo e li raccoglie tutti come un unico tocco in un `UITouch` singolo oggetto. In questo modo il controllo di un doppio tocco è semplice come `TapCount` il controllo della proprietà, come illustrato nel codice seguente:

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

 [![](touch-in-ios-images/image2.png "Funzionalità multitocco abilitata in iOS designer")](touch-in-ios-images/image2.png#lightbox)

È anche possibile impostare la funzionalità multitocco a livello di codice impostando `MultipleTouchEnabled` la proprietà come illustrato nella riga di codice seguente:

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

Per determinare il numero di dita che hanno toccato lo schermo `Count` , utilizzare la `UITouch` proprietà nella proprietà:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>Determinazione della posizione del tocco

Il metodo `UITouch.LocationInView` restituisce un oggetto CGPoint che include le coordinate del tocco in una visualizzazione specificata. Inoltre, è possibile testare per verificare se tale posizione si trova all'interno di un controllo chiamando `Frame.Contains`il metodo. Il frammento di codice seguente mostra un esempio di questo:

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

Ora che sono state apprese le informazioni sugli eventi di tocco in iOS, verranno fornite informazioni sui riconoscitori dei movimenti.

## <a name="gesture-recognizers"></a>Riconoscitori di movimento

I riconoscitori dei movimenti possono semplificare e ridurre notevolmente l'impegno di programmazione per supportare il tocco in un'applicazione. i riconoscitori di movimento iOS aggregano una serie di eventi di tocco in un singolo evento Touch.

Novell. iOS fornisce la classe `UIGestureRecognizer` come classe di base per i riconoscitori di movimento predefiniti seguenti:

- *UITapGestureRecognizer* : questa operazione è per uno o più rubinetti.
- *UIPinchGestureRecognizer* : pizzicare e diffondere le dita.
- *UIPanGestureRecognizer* : panoramica o trascinamento.
- *UISwipeGestureRecognizer* : scorrimento in qualsiasi direzione.
- *UIRotationGestureRecognizer* : rotazione di due dita in un movimento in senso orario o antiorario.
- *UILongPressGestureRecognizer* : premere e tenere premuto, a volte a volte con un lungo clic.

Il modello di base per l'uso di un riconoscimento di movimento è il seguente:

1. **Creare un'istanza del riconoscitore di movimento** , creare prima `UIGestureRecognizer` di tutto un'istanza di una sottoclasse. L'oggetto di cui viene creata un'istanza verrà associato a una vista e verrà sottoposto a Garbage Collection quando la vista viene eliminata. Non è necessario creare questa vista come variabile a livello di classe.
1. **Configurare le impostazioni di movimento** : il passaggio successivo consiste nel configurare il riconoscimento del movimento. Per un elenco delle proprietà `UIGestureRecognizer` che è possibile impostare per controllare il comportamento di un' `UIGestureRecognizer` istanza, consultare la documentazione di Novell in e le relative sottoclassi.
1. **Configurare la destinazione** : a causa del patrimonio Objective-C, Novell. iOS non genera eventi quando un riconoscimento di movimento corrisponde a un movimento.  `UIGestureRecognizer`dispone di un metodo `AddTarget` , che può accettare un delegato anonimo o un selettore Objective-C con il codice da eseguire quando il riconoscitore di movimenti crea una corrispondenza.
1. **Abilita riconoscimento movimento** : Analogamente agli eventi tocco, i movimenti sono riconosciuti solo se le interazioni tocco sono abilitate.
1. **Aggiungere il riconoscitore di movimento alla visualizzazione** . il passaggio finale consiste nell'aggiungere il movimento a una vista chiamando `View.AddGestureRecognizer` e passandogli un oggetto del riconoscimento di movimento.

Per ulteriori informazioni su come implementarli nel codice, vedere gli [esempi di riconoscimento dei movimenti](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples) .

Quando viene chiamata la destinazione del movimento, viene passato un riferimento al movimento che si è verificato. Ciò consente alla destinazione del movimento di ottenere informazioni sul movimento che si è verificato. La quantità di informazioni disponibili dipende dal tipo di riconoscimento dei movimenti utilizzato. Vedere la documentazione di Novell per informazioni sui dati disponibili per ogni `UIGestureRecognizer` sottoclasse.

È importante ricordare che, una volta che un riconoscitore di movimento è stato aggiunto a una vista, la vista (e tutte le visualizzazioni sottostanti) non riceverà alcun evento Touch. Per consentire gli eventi Touch contemporaneamente con i movimenti, `CancelsTouchesInView` la proprietà deve essere impostata su false, come illustrato nel codice seguente:

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

Ogni `UIGestureRecognizer` ha una proprietà state che fornisce informazioni importanti sullo stato del riconoscimento di movimento. Ogni volta che viene modificato il valore di questa proprietà, iOS chiamerà il metodo di sottoscrizione per dargli un aggiornamento. Se un riconoscimento di movimenti personalizzato non aggiorna mai la proprietà state, il Sottoscrittore non viene mai chiamato, rendendo inutile il riconoscimento dei movimenti.

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

Novell. iOS rappresenta questi valori nell' `UIGestureRecognizerState` enumerazione.

## <a name="working-with-multiple-gestures"></a>Utilizzo di più movimenti

Per impostazione predefinita, iOS non consente l'esecuzione simultanea di movimenti predefiniti. Al contrario, ogni riconoscimento di movimento riceverà gli eventi di tocco in un ordine non deterministico. Il frammento di codice seguente illustra come eseguire contemporaneamente un riconoscimento di movimento:

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

È anche possibile disabilitare un movimento in iOS. Sono disponibili due proprietà delegate che consentono a un riconoscitore di movimenti di esaminare lo stato di un'applicazione e gli eventi di tocco correnti, per prendere decisioni su come e se deve essere riconosciuto un movimento. I due eventi sono:

1. *ShouldReceiveTouch* : questo delegato viene chiamato immediatamente prima che il riconoscitore di movimento venga passato a un evento Touch e offra la possibilità di esaminare i tocchi e decidere quali tocchi verranno gestiti dal riconoscimento di movimento.
1. *ShouldBegin* : viene chiamato quando un riconoscimento tenta di cambiare lo stato da possibile a un altro stato. Se si restituisce false, lo stato del riconoscimento del movimento verrà forzato a non riuscire.

È possibile eseguire l'override di questi metodi con `UIGestureRecognizerDelegate`un delegato fortemente tipizzato, debole o associato tramite la sintassi del gestore dell'evento, come illustrato nel frammento di codice seguente:

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

Infine, è possibile accodare un riconoscitore di movimento in modo che abbia esito positivo solo se si verifica un errore in un altro riconoscitore di movimento. Ad esempio, un singolo riconoscimento di movimento tocco dovrebbe avere esito positivo solo quando un riconoscimento del movimento del doppio tocco ha esito negativo. Il seguente frammento di codice fornisce un esempio di questo:

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>Creazione di un gesto personalizzato

Sebbene iOS fornisca alcuni riconoscitori di movimento predefiniti, potrebbe essere necessario creare i riconoscitori dei movimenti personalizzati in alcuni casi. La creazione di un riconoscimento di movimenti personalizzato prevede i passaggi seguenti:

1. Sottoclasse `UIGestureRecognizer` .
1. Eseguire l'override dei metodi dell'evento Touch appropriati.
1. Propagazione dello stato di riconoscimento tramite la proprietà di stato della classe base.

Un esempio pratico sarà illustrato nella procedura dettagliata [using touch in iOS](ios-touch-walkthrough.md) .
