---
title: Eventi di tocco ed i movimenti in xamarin. IOS
description: Questo documento descrive come usare gli eventi di tocco, multitocco, movimenti, più i movimenti e movimenti personalizzati nelle applicazioni xamarin. IOS.
ms.prod: xamarin
ms.assetid: DA666DC9-446E-4CD1-B5A0-C6FFBC7E53AD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f7160c48e1b1ac85f4aa0173c0eb9f42b8fefca2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218618"
---
# <a name="touch-events-and-gestures-in-xamarinios"></a>Eventi di tocco ed i movimenti in xamarin. IOS

È importante comprendere gli eventi di tocco e input tocco API in un'applicazione iOS, così come sono fondamentali per tutte le interazioni fisiche con il dispositivo. Tutte le interazioni tramite tocco implicano un `UITouch` oggetto. In questo articolo si apprenderà come usare il `UITouch` classe e le relative API per il supporto di tocco. In un secondo momento, si elaborerà nostre conoscenze per informazioni su come supportare i movimenti.

## <a name="enabling-touch"></a>Abilitazione di Touch

Controlla `UIKit` – tali sottoclassato da UIControl – dipendono in questo caso l'interazione dell'utente che hanno movimenti incorporati in UIKit e pertanto non è necessaria per abilitare il tocco. È già abilitato.

Tuttavia, molte delle visualizzazioni in `UIKit` non è abilitato per impostazione predefinita il tocco. Esistono due modi per abilitare il tocco di un controllo. Il primo consiste per controllare la casella di controllo abilitato l'interazione dell'utente nel riquadro della proprietà di iOS Designer, come illustrato nello screenshot seguente:

 [![](touch-in-ios-images/image1.png "Selezionare la casella di controllo abilitato l'interazione dell'utente nel riquadro della proprietà di iOS Designer")](touch-in-ios-images/image1.png#lightbox)

È anche possibile usare un controller per impostare il `UserInteractionEnabled` proprietà su true in un `UIView` classe. Ciò è necessario se l'interfaccia utente viene creato nel codice.

La riga di codice seguente è riportato un esempio:

```csharp
imgTouchMe.UserInteractionEnabled = true;
```

## <a name="touch-events"></a>Eventi di tocco

Esistono tre fasi di tocco che si verificano quando l'utente tocca lo schermo, si sposta il dito o rimuove il dito. Questi metodi sono definiti `UIResponder`, ovvero la classe base per UIView. iOS sostituiranno i metodi associati nel `UIView` e il `UIViewController` per la gestione tocco:

-  `TouchesBegan` : Viene chiamato quando la schermata prima di tutto viene manipolata.
-  `TouchesMoved` : Viene chiamato quando la posizione delle modifiche tocco dell'utente è scorrevole le dita sullo schermo.
-  `TouchesEnded` oppure `TouchesCancelled` – `TouchesEnded` viene chiamato quando le dita dell'utente vengono eseguito il lift fuori dallo schermo.  `TouchesCancelled` viene chiamato se iOS consente di annullare il tocco, ad esempio, se un utente scorre il propria dito lontano da un pulsante per annullare una pressione.


Tocco in modo ricorsivo travel eventi verso il basso attraverso lo stack di UIViews, controllare se l'evento di tocco è all'interno di un oggetto visualizzazione. Spesso si tratta _Hit testing_. Verrà chiamati innanzitutto sul livello più alto `UIView` oppure `UIViewController` e quindi verrà chiamato sul `UIView` e `UIViewControllers` sottostanti nella gerarchia di visualizzazione.

Oggetto `UITouch` verrà creato l'oggetto ogni volta che l'utente tocca lo schermo. Il `UITouch` oggetto include dati relativi al tocco, ad esempio quando il tocco si è verificata, in cui si è verificato, se il tocco è stato un scorrere rapidamente e così via. Gli eventi di tocco vengono passati a una proprietà tocchi – un `NSSet` contenente uno o più tocchi. È possibile usare questa proprietà per ottenere un riferimento a un tocco e determinare la risposta dell'applicazione.

Le classi che eseguono l'override di uno degli eventi tocco devono prima chiamare l'implementazione di base e quindi ottenere il `UITouch` oggetto associato all'evento. Per ottenere un riferimento a primo tocco, chiamare il `AnyObject` proprietà ed eseguire il cast come un `UITouch` come mostrato nell'esempio seguente:

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

iOS riconosce automaticamente rapido successiva tocca sullo schermo e verrà raccolti tutti come un solo tocco in una singola `UITouch` oggetto. In questo modo il controllo per un doppio tocco facile come il controllo di `TapCount` proprietà, come illustrato nel codice seguente:

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

Multi-touch non è abilitato per impostazione predefinita nei controlli. Multi-touch può essere abilitata in iOS Designer, come illustrato nello screenshot seguente:

 [![](touch-in-ios-images/image2.png "Multi-touch abilitata in iOS Designer")](touch-in-ios-images/image2.png#lightbox)

È anche possibile impostare a livello di programmazione multitouch impostando il `MultipleTouchEnabled` proprietà come illustrato nella riga di codice seguente:

```csharp
imgTouchMe.MultipleTouchEnabled = true;
```

Per determinare quanti dita modificate la schermata, usare il `Count` proprietà di `UITouch` proprietà:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    base.TouchesBegan (touches, evt);
    lblNumberOfFingers.Text = "Number of fingers: " + touches.Count.ToString();
}
```

## <a name="determining-touch-location"></a>Determinazione del percorso di tocco

Il metodo `UITouch.LocationInView` restituisce un oggetto CGPoint che contiene le coordinate del tocco all'interno di una determinata visualizzazione. Inoltre, è possibile verificare se tale posizione è all'interno di un controllo, chiamare il metodo `Frame.Contains`. Il frammento di codice seguente viene illustrato un esempio:

```csharp
if (this.imgTouchMe.Frame.Contains (touch.LocationInView (this.View)))
{
    // the touch event happened inside the UIView imgTouchMe.
}
```

Ora che abbiamo comprendere gli eventi di tocco in iOS, cerchiamo di comprendere i riconoscitori di movimento.

## <a name="gesture-recognizers"></a>Riconoscitori di movimento

I riconoscitori di movimento possono notevolmente semplificare e ridurre lo sforzo di programmazione per il supporto di tocco in un'applicazione. i riconoscitori di movimento iOS aggregano una serie di eventi tattili in un evento singolo tocco.

Xamarin. IOS fornisce la classe `UIGestureRecognizer` come classe di base per i riconoscitori di movimento predefiniti seguenti:

-  *UITapGestureRecognizer* – si tratta di uno o più scelte.
-  *UIPinchGestureRecognizer* – Pinching e diffondere le dita distanti.
-  *UIPanGestureRecognizer* : panoramica o trascinamento.
-  *UISwipeGestureRecognizer* – scorrendo rapidamente in qualsiasi direzione.
-  *UIRotationGestureRecognizer* : rotazione di due dita un movimento in senso orario o antiorario.
-  *UILongPressGestureRecognizer* – premere e tenere premuto, talvolta detta un prolungata clic o pressione prolungata.


Il modello di base all'uso di un sistema di riconoscimento di movimento è come segue:

1.  **Creare un'istanza di riconoscitore di movimento** : in primo luogo, creare un'istanza un `UIGestureRecognizer` sottoclasse. Verrà associato l'oggetto che viene creata un'istanza da una visualizzazione e verrà garbage raccolti quando la vista viene eliminata. Non è necessario creare questa visualizzazione come una variabile a livello di classe.
1.  **Configurare le impostazioni di movimento** : il passaggio successivo consiste nel configurare il riconoscitore di movimento. Consultare la documentazione di Xamarin in merito `UIGestureRecognizer` e delle relative sottoclassi per un elenco delle proprietà che è possibile impostare per controllare il comportamento di un `UIGestureRecognizer` istanza.
1.  **Configurare la destinazione** : a causa di sua esperienza di Objective-C, xamarin. IOS non generano eventi quando un riconoscitore di movimento corrisponde a un movimento.  `UIGestureRecognizer` dispone di un metodo – `AddTarget` – che può accettare un delegato anonimo o un selettore di Objective-C con il codice da eseguire quando il riconoscitore di movimento stabilisce una corrispondenza.
1.  **Abilitare riconoscitore di movimento** : proprio come con gli eventi di tocco, movimenti vengono riconosciuti solo se sono abilitate le interazioni tramite tocco.
1.  **Aggiungere il riconoscitore di movimento alla vista** : consente il passaggio finale consiste nell'aggiungere il movimento a una visualizzazione chiamando `View.AddGestureRecognizer` e passando un oggetto di riconoscitore di movimento.

Vedere il [esempi riconoscitore di movimento](~/ios/app-fundamentals/touch/ios-touch-walkthrough.md#Gesture_Recognizer_Samples) per altre informazioni su come implementarli nelle code.

Quando viene chiamata la destinazione del movimento, verrà passato un riferimento all'azione che si sono verificati. In questo modo la destinazione di movimento ottenere informazioni sull'azione che si sono verificati. La quantità di informazioni disponibile dipende dal tipo di riconoscitore di movimento che è stato usato. Vedere la documentazione per informazioni sui dati disponibili per ognuno di Xamarin `UIGestureRecognizer` sottoclasse.

È importante tenere presente che dopo aver aggiunto un riconoscitore di movimento per una vista, la vista (e tutte le viste sotto di essa) non riceverà gli eventi di tocco. Per consentire gli eventi di tocco simultaneamente i movimenti di `CancelsTouchesInView` proprietà deve essere impostata su false, come illustrato nel codice seguente:

```csharp
_tapGesture.Recognizer.CancelsTouchesInView = false;
```

Ogni `UIGestureRecognizer` dispone di una proprietà di stato che fornisce informazioni importanti sullo stato di riconoscimento di movimento. Ogni volta che il valore di questa proprietà viene modificato, iOS chiamerà il metodo sottoscrizione assegnandogli un aggiornamento. Se un riconoscitore di movimento personalizzato non aggiorna mai la proprietà State, il sottoscrittore non viene mai chiamato, il rendering di riconoscitore di movimento inutile.

I movimenti possono essere riepilogati come uno dei due tipi:

1.  *Discreti* : questi movimenti solo fire la prima ora vengono riconosciute.
1.  *Continua* : questi movimenti continuano a generare, purché vengono riconosciute.


I riconoscitori di movimento si trova in uno dei seguenti stati:

-  *Possibili* – questo è lo stato iniziale di tutti i riconoscitori di movimento. Questo è il valore predefinito della proprietà di stato.
-  *Began* : quando un movimento continuo prima di tutto viene riconosciuto, lo stato è impostato su Began. In questo modo esegue la sottoscrizione per distinguere tra all'avvio di riconoscimento del movimento e quando è stato modificato.
-  *Changed* – dopo che un movimento continuo è iniziata, ma non è stata completata, lo stato verrà impostato Changed ogni volta che un tocco si sposta o cambia, purché si trovi ancora parametri previsti del movimento.
-  *Annullato* : verrà impostato questo stato se il riconoscimento è passata da Began a Changed e quindi i tocchi non modificati non più in modo da adattare il modello del movimento.
-  *Riconosciuto* : lo stato verrà impostato quando il riconoscitore di movimento corrisponde a un set di tocco e informa il sottoscrittore che il movimento è terminato.
-  *Terminata* – si tratta di un alias per lo stato Recognized.
-  *Operazione non riuscita* : quando il riconoscitore di movimento può non corrispondono più ai tocchi è in ascolto per, lo stato verrà impostato su Failed.


Xamarin. IOS rappresenta tali valori nel `UIGestureRecognizerState` enumerazione.

## <a name="working-with-multiple-gestures"></a>Utilizzo di movimenti più

Per impostazione predefinita, iOS non supporta i movimenti predefiniti per l'esecuzione simultanea. Al contrario, ogni riconoscitore di movimento riceveranno gli eventi di tocco in modo non deterministico. Il frammento di codice seguente illustrato come effettuare una riconoscitore di movimento eseguito contemporaneamente:

```csharp
gesture.ShouldRecognizeSimultaneously += (UIGestureRecognizer r) => { return true; };
```

È anche possibile disabilitare un movimento in iOS. Sono disponibili due proprietà di delegato che consentono una riconoscitore di movimento esaminare lo stato di un'applicazione e gli eventi di tocco corrente, per prendere decisioni su come e se deve essere riconosciuto un movimento. I due eventi sono:

1.  *ShouldReceiveTouch* : questo delegato viene chiamato appena prima che il riconoscitore di movimento viene passato un evento di tocco e fornisce un'opportunità per esaminare i tocchi e decidere quali i tocchi verranno gestiti dal riconoscimento di movimento.
1.  *ShouldBegin* : viene chiamato quando un sistema di riconoscimento tenta di modificare lo stato da possibili a un altro stato. Restituzione di false forza lo stato di riconoscimento di movimento per essere modificato in non riuscito.


È possibile eseguire l'override di questi metodi con un oggetto fortemente tipizzato `UIGestureRecognizerDelegate`, un delegato debole, o binding tramite la sintassi del gestore dell'evento, come illustrato dal frammento di codice seguente:

```csharp
gesture.ShouldReceiveTouch += (UIGestureRecognizer r, UITouch t) => { return true; };
```

Infine, è possibile inserire in coda un riconoscitore di movimento in modo che verrà completata solo se ha esito negativo di un'altra riconoscitore di movimento. Ad esempio, riconoscitore di movimento un singolo tocco deve avere esito positivo solo quando si verifica un errore di riconoscitore di movimento un doppio tocco. Il frammento di codice seguente viene fornito un esempio di questo oggetto:

```csharp
singleTapGesture.RequireGestureRecognizerToFail(doubleTapGesture);
```

## <a name="creating-a-custom-gesture"></a>Creazione di un'azione personalizzata

Sebbene iOS offre alcuni predefinito i riconoscitori di movimento, potrebbe essere necessario creare i riconoscitori di movimento personalizzato in alcuni casi. Creazione di un sistema di riconoscimento di movimento personalizzato prevede i passaggi seguenti:

1.  Sottoclasse `UIGestureRecognizer` .
1.  Eseguire l'override dei metodi di eventi di tocco appropriato.
1.  Visualizzi lo stato di riconoscimento tramite proprietà di stato della classe di base.


Verrà illustrato un esempio pratico di questa nel [tramite tocco in iOS](ios-touch-walkthrough.md) procedura dettagliata.
