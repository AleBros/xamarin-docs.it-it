---
title: Touch con iOS
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 78783089303eba09b0ee36534b0078b82674a1c6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="touch-in-ios"></a>Touch con iOS

È importante comprendere gli eventi di tocco e toccare API in un'applicazione iOS, come se fossero centrale per tutte le interazioni con il dispositivo fisiche. Tutte le interazioni di tocco comportano un `UITouch` oggetto. In questo articolo verrà illustrato come utilizzare la `UITouch` classe e le relative API per supportare il tocco. In un secondo momento, si espanderà in conoscendo per informazioni su come supportare i movimenti.

## <a name="enabling-touch"></a>Abilitazione di Touch

Controlli in `UIKit` – tali sottoclassato da UIControl – dipendono pertanto interazione dell'utente che hanno movimenti integrati in UIKit e pertanto non è necessaria per abilitare Touch. È già abilitato.

Tuttavia, molte delle visualizzazioni della `UIKit` non dispone di tocco abilitato per impostazione predefinita. Esistono due modi per abilitare tocco su un controllo. La prima consiste nel controllare la casella di controllo abilitato interazione dell'utente nel riquadro proprietà della finestra di progettazione, iOS come illustrato nella schermata seguente:

 [![](touch-in-ios-images/image1.png "Selezionare la casella di controllo interazione utente è abilitato nel riquadro proprietà della finestra di progettazione iOS")](touch-in-ios-images/image1.png#lightbox)

È inoltre possibile utilizzare un controller per impostare il `UserInteractionEnabled` proprietà su true in un `UIView` classe. Ciò è necessario se l'interfaccia utente viene creato nel codice.

La riga di codice seguente è riportato un esempio:

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>Eventi di tocco

Esistono tre fasi di tocco che si verificano quando l'utente tocca lo schermo, sposta il dito o rimuove il dito. Questi metodi sono definiti in `UIResponder`, che è la classe base per UIView. iOS sostituiranno i metodi associati nel `UIView` e `UIViewController` per gestire tocco:

-  `TouchesBegan` : Viene chiamato quando la schermata viene innanzitutto interessata.
-  `TouchesMoved` : Viene chiamato quando il percorso delle modifiche tocco come l'utente è scorrevole le dita sullo schermo.
-  `TouchesEnded` o `TouchesCancelled` – `TouchesEnded` viene chiamato quando venga sollevato il dito dell'utente esterno dello schermo.  `TouchesCancelled` viene chiamato se iOS Annulla il tocco, ad esempio, se un utente scorre il propria dito lontano da un pulsante per annullare la pressione di un.


Toccare in modo ricorsivo viaggio eventi verso il basso attraverso lo stack di UIViews, per verificare se l'evento tocco è entro i limiti di un oggetto visualizzazione. Spesso si tratta di _Hit testing_. Verrà chiamati innanzitutto sul livello più alto `UIView` o `UIViewController` e successivamente viene chiamato sul `UIView` e `UIViewControllers` sottostanti nella gerarchia della visualizzazione.

Oggetto `UITouch` verrà creato l'oggetto ogni volta che l'utente tocca lo schermo. Il `UITouch` oggetto include i dati sul tocco, ad esempio quando il tocco si è verificato, in cui si è verificato, se è stata portata una scorrere e così via. Gli eventi tocco vengono passati a una proprietà ritocchi – un `NSSet` contenente uno o più elementi. È possibile utilizzare questa proprietà per ottenere un riferimento a un tocco e determinare la risposta dell'applicazione.

Le classi che eseguono l'override di uno degli eventi tocco devono prima chiamare l'implementazione di base e quindi ottenere il `UITouch` oggetto associato all'evento. Per ottenere un riferimento a primo tocco, chiamare il `AnyObject` proprietà e di eseguirne il cast come un `UITouch` come illustrato nell'esempio seguente:

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

iOS riconosce automaticamente rapida successivo tocca sullo schermo e verrà raccolti tutti come un solo tocco in una singola `UITouch` oggetto. In questo modo il controllo per un doppio tocco semplice quanto la verifica di `TapCount` proprietà, come illustrato nel codice seguente:

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

## <a name="multi-touch"></a>Multi-Touch

Multi-touch non è abilitato per impostazione predefinita, i controlli. Multi-touch può essere abilitato nella finestra di progettazione, iOS, come illustrato nella schermata seguente:

 [![](touch-in-ios-images/image2.png "Multi-touch abilitata nella finestra di progettazione iOS")](touch-in-ios-images/image2.png#lightbox)

È anche possibile impostare a livello di programmazione Multi-touch impostando il `MultipleTouchEnabled` proprietà come illustrato nella riga di codice seguente:

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

Per determinare quanti dita interessate la schermata, utilizzare il `Count` proprietà la `UITouch` proprietà:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>Determinazione del percorso tocco

Il metodo `UITouch.LocationInView` restituisce un oggetto CGPoint che contiene le coordinate del tocco all'interno di una determinata visualizzazione. Inoltre, è possibile verificare se tale percorso è all'interno di un controllo chiamando il metodo `Frame.Contains`. Frammento di codice seguente viene illustrato un esempio:

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

Ora che la comprensione degli eventi tocco è disponibile in iOS, esaminiamo i riconoscitori di movimento.

## <a name="gesture-recognizers"></a>Riconoscitori di movimento

I riconoscitori di movimento notevolmente consente di semplificare e ridurre il lavoro di programmazione per il supporto di tocco in un'applicazione. i riconoscitori di movimento iOS aggregano una serie di eventi tocco in un evento solo tocco.

Xamarin fornisce la classe `UIGestureRecognizer` come classe di base dei tipi di riconoscimento di movimento predefiniti seguenti:

-  *UITapGesturesRecognizer* : si tratta di uno o più scelte.
-  *UIPinchGestureRecognizer* – Pinching e diffusione dita distanti.
-  *UIPanGestureRecognizer* : panoramica o trascinamento.
-  *UISwipeGestureRecognizer* : passaggio in qualsiasi direzione.
-  *UIRotationGestureRecognizer* : rotazione di due dita un movimento in senso orario o in senso antiorario.
-  *UILongPressGestureRecognizer* : premere e tenere premuto, detti talvolta una pressione prolungata o long-fare clic su.


Il modello di base all'utilizzo di un riconoscimento di movimento è il seguente:

1.  **Creare un'istanza il riconoscitore di movimento** : innanzitutto, creare un'istanza un `UIGestureRecognizer` sottoclasse. L'oggetto che viene creata un'istanza verrà associato da una vista e verrà garbage raccolti quando viene eliminata la visualizzazione. Non è necessario creare la visualizzazione come una variabile a livello di classe.
1.  **Configurare le impostazioni di movimento** : il passaggio successivo consiste nel configurare il riconoscitore di movimento. Consultare la documentazione di Xamarin su `UIGestureRecognizer` e delle relative sottoclassi per un elenco di proprietà che è possibile impostare per controllare il comportamento di un `UIGestureRecognizer` istanza.
1.  **Configurare la destinazione** : a causa di sua esperienza Objective-C, xamarin. IOS non genera eventi quando un movimento corrisponde a un riconoscimento di movimento.  `UIGestureRecognizer` dispone di un metodo – `AddTarget` – che può accettare un delegato anonimo o un selettore Objective-C con il codice da eseguire quando il riconoscitore di movimento determina una corrispondenza.
1.  **Abilitare riconoscitore di movimento** : esattamente come con gli eventi tocco, i movimenti vengono riconosciuti solo se sono abilitate le interazioni di tocco.
1.  **Aggiungere il riconoscitore di movimento alla vista** : il passaggio finale consiste nell'aggiungere l'azione a una visualizzazione chiamando `View.AddGestureRecognizer` e passando un oggetto riconoscitore di movimento.

Consultare la [esempi riconoscitore di movimento](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples) per ulteriori informazioni su come implementarli nel codice.

Quando viene chiamata la destinazione del movimento, verrà passato un riferimento all'azione che si sono verificati. In questo modo la destinazione di movimento ottenere informazioni sull'azione che si sono verificati. La quantità di informazioni disponibili dipende dal tipo di riconoscitore di movimento che è stato utilizzato. Vedere la documentazione di Xamarin per informazioni sui dati disponibili per ogni `UIGestureRecognizer` sottoclasse.

È importante tenere presente che dopo l'aggiunta di un riconoscimento di movimento a una visualizzazione, la vista (e le viste sottostanti) non riceverà gli eventi tocco. Per consentire gli eventi di tocco simultaneamente con i movimenti, il `CancelsTouchesInView` proprietà deve essere impostata su false, come illustrato nel codice seguente:

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

Ogni `UIGestureRecognizer` ha una proprietà di stato che fornisce importanti informazioni sullo stato di riconoscitore di movimento. Ogni volta che cambia il valore di questa proprietà, iOS chiamerà il metodo sottoscrizione assegnandogli un aggiornamento. Se un riconoscimento di movimenti personalizzati non aggiorna mai la proprietà State, il sottoscrittore non viene mai chiamato, il rendering inutile riconoscitore di movimento.

I movimenti possono essere riepilogati come uno dei due tipi:

1.  *Discreti* : questi ora di attivare il primo solo i movimenti vengono riconosciuti.
1.  *Continua* – questi movimenti continuano a generare come vengono riconosciuti.


I riconoscitori di movimento si trova in uno dei seguenti stati:

-  *Possibili* : si tratta dello stato iniziale di tutti i riconoscitori di movimento. Questo è il valore predefinito della proprietà di stato.
-  *Began* : quando un movimento continuo prima viene riconosciuto, lo stato è impostato su Began. In questo modo esegue la sottoscrizione per distinguere tra all'avvio di riconoscimento di movimento e quando viene modificato.
-  *Changed* – un movimento continuo è iniziata, ma non è stata completata, lo stato verrà essere impostato su modificato ogni volta che un tocco o alla modifica, fino a quando ha ancora superato i parametri previsti del movimento.
-  *Annullato* : verrà impostato questo stato se il riconoscimento è passato da Began a modificato e quindi i ritocchi modificati in modo da non rientrano nel modello del movimento.
-  *Riconosciuto* : lo stato verrà impostato quando il riconoscitore di movimento corrisponda a un set di elementi e informa il sottoscrittore che ha completato l'azione.
-  *Fine* : si tratta di un alias per lo stato Recognized.
-  *Operazione non riuscita* : quando il riconoscitore di movimento non può corrispondere il tocco è in ascolto per, lo stato verrà modificato in non riuscito.


Xamarin rappresenta questi valori nel `UIGestureRecognizerState` enumerazione.

## <a name="working-with-multiple-gestures"></a>Utilizzo di più i movimenti

Per impostazione predefinita, iOS non consente i movimenti predefinito eseguire contemporaneamente. Al contrario, ciascun riconoscitore di movimento riceverà gli eventi di tocco in modo non deterministico. Frammento di codice seguente viene illustrato come eseguire un riconoscimento di movimento contemporaneamente:

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

È anche possibile disabilitare un movimento in iOS. Sono disponibili due proprietà di delegato che consentono un riconoscimento di movimento esaminare lo stato di un'applicazione e gli eventi di tocco corrente, per prendere decisioni su come e se deve essere riconosciuto un movimento. I due eventi sono:

1.  *ShouldReceiveTouch* : questo delegato viene chiamato appena prima che il gestore di riconoscimento viene passato a un evento di tocco e offre l'opportunità per esaminare il tocco e decidere quali elementi verranno gestiti dal riconoscitore di movimento.
1.  *ShouldBegin* : viene chiamato quando un riconoscimento tenta di modificare lo stato da possibili a un altro stato. Restituzione di false forzerà lo stato di riconoscitore di movimento per essere modificato in non riuscito.


È possibile eseguire l'override di questi metodi con un oggetto fortemente tipizzato `UIGestureRecognizerDelegate`, un delegato debole, o binding tramite la sintassi del gestore eventi, come illustrato dal frammento di codice seguente:

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

Infine, è possibile per mettere in coda riconoscitore di movimento in modo che riuscirà solo se ha esito negativo di un'altra riconoscitore di movimento. Ad esempio, un riconoscimento di movimento unico tocco deve avere esito positivo solo quando si verifica un errore di un riconoscimento di movimento doppio tocco. Frammento di codice seguente viene fornito un esempio di questo oggetto:

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>Creazione di un'azione personalizzata

Sebbene iOS fornisce alcuni predefinito i riconoscitori di movimento, potrebbe essere necessario creare i riconoscitori di movimento personalizzato in alcuni casi. Creazione di un riconoscimento di movimento personalizzato prevede i passaggi seguenti:

1.  Sottoclasse `UIGestureRecognizer` .
1.  Eseguire l'override dei metodi di eventi tocco appropriato.
1.  Visualizzi lo stato di riconoscimento tramite proprietà di stato della classe base.


Un esempio pratico di questo verrà trattato nel [tramite tocco in iOS](ios-touch-walkthrough.md) procedura dettagliata.
