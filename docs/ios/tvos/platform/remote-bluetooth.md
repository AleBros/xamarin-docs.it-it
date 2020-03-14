---
title: Controller Siri remoto e Bluetooth per tvOS in Novell
description: Questo articolo descrive come usare i controller dei giochi di Siri remote e Bluetooth nelle app tvOS scritte con Novell.
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: c0338fce694d61dc19484c56dbc00bb854d0d0d7
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304891"
---
# <a name="siri-remote-and-bluetooth-controllers-for-tvos-in-xamarin"></a>Controller Siri remoto e Bluetooth per tvOS in Novell

Gli utenti dell'app Novell. tvOS non interagisce direttamente con l'interfaccia, come con iOS, dove toccano immagini sullo schermo del dispositivo, ma indirettamente da tutta la stanza usando [Siri Remote](#The-Siri-Remote).

Se l'app è un gioco, è possibile compilare facoltativamente il supporto per i [controller di gioco Bluetooth](#Bluetooth-Game-Controllers) di terze parti, creati per iOS (IFM) nell'app.

[![](remote-bluetooth-images/intro01.png "The Bluetooth Remote and Game Controller")](remote-bluetooth-images/intro01.png#lightbox)

In questo articolo vengono descritti i pulsanti di [Siri Remote](#The-Siri-Remote), [Touch Surface](#Touch-Surface-Gestures) e [Siri Remote](#Siri-Remote-Buttons) e viene illustrato come utilizzarli tramite [movimenti e storyboard](#Gestures-and-Storyboards), [movimenti e codice](#Gestures-and-Code) e [gestione degli eventi di basso livello](#Low-Level-Event-Handling). Infine, viene illustrato come [usare i controller di gioco](#Working-with-Game-Controllers) in un'app Novell. tvOS.

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>Siri remoto

La modalità principale con cui gli utenti interagiranno con Apple TV e con l'app Novell. tvOS è tramite l'inclusivo Siri remote. Apple ha progettato la modalità remota per colmare la distanza tra l'utente seduto sul divano e l'interfaccia utente di Apple TV visualizzata nella stanza dello schermo TV.

La tua sfida come sviluppatore di app tvOS è la creazione di un'interfaccia utente rapida, facile da usare e visivamente accattivante che sfrutta la superficie di tocco, l'accelerometro e i pulsanti di Siri remote.

[![](remote-bluetooth-images/remote01.png "The Siri Remote")](remote-bluetooth-images/remote01.png#lightbox)

Siri Remote presenta le seguenti funzionalità e gli utilizzi previsti nell'app tvOS:

|Caratteristica|Utilizzo generale delle app|Utilizzo app gioco|
|---|---|---|
|**Superficie di tocco**<br />Scorrere rapidamente per spostarsi, premere per selezionare e tenere premuto per i menu contestuali.|**Toccare/scorrere rapidamente**<br />Spostamento dell'interfaccia utente tra elementi attivabili.<br /><br />**Fare clic**<br />Attiva l'elemento selezionato (messa a fuoco).|**Toccare/scorrere rapidamente**<br />Dipende dalla progettazione dei giochi e può essere usato come D-Pad toccando i bordi.<br /><br />**Fare clic**<br />Eseguire la funzione del pulsante principale.|
|**Menu**<br />Premere per tornare alla schermata o al menu precedente.|Torna alla schermata precedente e si chiude alla schermata iniziale di Apple TV dalla schermata principale dell'app.|Sospendere e riprendere il gameplay, tornare alla schermata precedente e uscire dalla schermata iniziale di Apple TV dalla schermata principale dell'app.|
|**Siri/ricerca**<br />In paesi con Siri, premere e tenere premuto per il controllo vocale, in tutti gli altri paesi, Visualizza la schermata di ricerca.|n/d|n/d|
|**Riproduzione/sospensione**<br />Riprodurre e sospendere i supporti o fornire una funzione secondaria nelle app.|Avvia la riproduzione dei supporti e sospende/riprende la riproduzione.|Esegue la funzione del pulsante secondario o ignora il video introduttivo (se esistente).|
|**Home**<br />Premere per tornare alla schermata iniziale, fare doppio clic per visualizzare le applicazioni in esecuzione, premere e tenere premuto per sospendere il dispositivo.|n/d|n/d|
|**Volume**<br />Controlla il volume del dispositivo audio/video collegato.|n/d|n/d|

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>Movimenti della superficie di tocco

La superficie di tocco di Siri Remote è in grado di rilevare un'ampia gamma di movimenti a dito singolo a cui è possibile rispondere nell'app Novell. tvOS:

|Scorrimento rapido|Fare clic su|Tocco|
|---|---|---|
|![](remote-bluetooth-images/Gesture01.png)|![](remote-bluetooth-images/Gesture02.png)|![](remote-bluetooth-images/Gesture03.png)|
|Sposta la selezione (stato attivo) tra gli elementi dell'interfaccia utente sullo schermo (verso l'alto, verso sinistra, verso destra). È possibile usare l'operazione di scorrimento rapido per scorrere rapidamente elenchi di contenuti di grandi dimensioni usando l'inerzia.|Attiva l'elemento selezionato (messa a fuoco) o funge da pulsante principale in un gioco. Facendo clic e tenendo premuto è possibile attivare i menu contestuali o le funzioni secondarie.|Toccare leggermente la superficie di tocco sui bordi agisce come pulsanti direzionali su un D-Pad, spostando lo stato attivo verso l'alto, verso il basso, verso sinistra o verso destra, a seconda dell'area toccata. A seconda dell'app, può essere usato per rivelare i controlli nascosti.|

Apple fornisce i suggerimenti seguenti per l'utilizzo dei movimenti della superficie di tocco:

- **Distinguere tra clic e tocchi** , facendo clic su un'azione intenzionale da parte dell'utente ed è particolarmente adatta per la selezione, l'attivazione e il pulsante principale di un gioco. Il tocco è più semplice e deve essere usato con moderazione, in quanto l'utente ha spesso a disposizione il Siri remoto e può attivare un evento tap in modo semplice.
- **Non ridefinire i movimenti standard** : l'utente ha la previsione che i movimenti specifici eseguiranno azioni specifiche. non è necessario ridefinire il significato o la funzione di questi movimenti nell'app. L'unica eccezione è rappresentata da un'app di gioco durante il gioco attivo.
- **Definire nuovi movimenti sporadicamente** , l'utente ha la previsione che i movimenti specifici eseguiranno azioni specifiche. È consigliabile evitare di definire movimenti personalizzati per eseguire azioni standard. Anche in questo caso, i giochi sono l'eccezione più usuale, in cui i movimenti personalizzati possono aggiungere divertimento e gioco immersivo al gioco.
- **Se necessario, rispondere ai rubinetti D-Pad,** toccando leggermente i bordi dell'angolo della superficie di tocco, si reagirà come un d-pad in un controller di gioco spostando lo stato attivo o direzione verso l'alto, verso il basso, verso sinistra o verso destra. Se necessario, è necessario rispondere a questi movimenti nell'app o nel gioco.

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Pulsanti remoti Siri

Oltre ai movimenti sulla superficie di tocco, l'app può rispondere all'utente facendo clic sulla superficie di tocco o premendo il pulsante Play/Pause. Se si accede a Siri remote usando il Framework di Game controller, è anche possibile rilevare il pulsante di menu premuto.

Inoltre, le pressioni dei pulsanti di menu possono essere rilevate usando un riconoscitore di movimento con elementi `UIKit` standard. Se si preme il pulsante di menu premuto, sarà necessario chiudere la visualizzazione corrente e il controller di visualizzazione e tornare a quello precedente.

> [!IMPORTANT]
> È **sempre** consigliabile assegnare una funzione al pulsante Play/Pause sul computer remoto. Se si dispone di un pulsante non funzionante, l'app potrebbe essere danneggiata dall'utente finale. Se non si dispone di una funzione valida per questo pulsante, assegnare la stessa funzione del pulsante principale (clic sulla superficie di tocco).

<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>Movimenti e storyboard

Il modo più semplice per usare Siri remote nell'app Novell. tvOS consiste nell'aggiungere i riconoscitori di movimento alle visualizzazioni in progettazione interfaccia.

Per aggiungere un sistema di riconoscimento dei movimenti, procedere come segue:

1. Nella **Esplora soluzioni**fare doppio clic sul file `Main.storyboard` e aprirlo per modificare la finestra di progettazione interfaccia.
2. Trascinare un **riconoscimento movimento tocco** dalla **libreria** e rilasciarlo nella visualizzazione:

    [![](remote-bluetooth-images/storyboard01.png "A Tap Gesture Recognizer")](remote-bluetooth-images/storyboard01.png#lightbox)
3. **Selezionare Seleziona** nella sezione **pulsante** di **controllo attributi**:

    [![](remote-bluetooth-images/storyboard02.png "Check Select")](remote-bluetooth-images/storyboard02.png#lightbox)
4. **Selezionare** significa che il movimento risponderà all'utente facendo clic sulla **superficie di tocco** sul Siri remoto. È inoltre possibile rispondere ai pulsanti **menu**, **Esegui/Sospendi**, **su**, **giù**, **sinistro** e **destro** .
5. Successivamente, collegare un' **azione** dal riconoscimento del **movimento Tap** e chiamarla `TouchSurfaceClicked`:

    [![](remote-bluetooth-images/storyboard03.png "An Action from the Tap Gesture Recognizer")](remote-bluetooth-images/storyboard03.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac.

Modificare il file del controller di visualizzazione (esempio `FirstViewController.cs`) e aggiungere il codice seguente per gestire il movimento attivato:

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class FirstViewController : UIViewController
    {
        ...

        #region Custom Actions
        partial void TouchSurfaceClicked (Foundation.NSObject sender) {
            // Handle click here
            ...
        }
        #endregion
    }
}
```

Per ulteriori informazioni sull'utilizzo degli storyboard, vedere la [Guida introduttiva Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md). In particolare, la [creazione dell'interfaccia utente](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface) e [la scrittura del codice con le sezioni Outlets e actions](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) .

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>Movimenti e codice

Facoltativamente, è possibile creare movimenti direttamente nel C# codice e aggiungerli alle visualizzazioni nell'interfaccia utente. Ad esempio, per aggiungere una serie di riconoscimenti di movimenti di scorrimento, modificare il controller di visualizzazione e aggiungere il codice seguente:

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class SecondViewController : UIViewController
    {
        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Wire-up gestures
            var upGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Up";
                ButtonLabel.Text = "Swiped Up";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Up
            };
            this.View.AddGestureRecognizer (upGesture);

            var downGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Down";
                ButtonLabel.Text = "Swiped Down";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Down
            };
            this.View.AddGestureRecognizer (downGesture);

            var leftGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Left";
                ButtonLabel.Text = "Swiped Left";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Left
            };
            this.View.AddGestureRecognizer (leftGesture);

            var rightGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Right";
                ButtonLabel.Text = "Swiped Right";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Right
            };
            this.View.AddGestureRecognizer (rightGesture);
        }
        #endregion
    }
}
```

<a name="Low-Level-Event-Handling" />

## <a name="low-level-event-handling"></a>Gestione degli eventi di basso livello

Se si sta creando un tipo personalizzato basato su `UIKit` nell'app Novell. tvOS (ad esempio `UIView`), è anche possibile fornire una gestione di basso livello della pressione del pulsante tramite `UIPress` eventi.

Un evento `UIPress` consiste nel tvOS di un evento `UITouch` per iOS, ad eccezione del fatto che `UIPress` restituisce informazioni sulle pressioni dei pulsanti sul Siri remoto o su altri dispositivi Bluetooth collegati (ad esempio un controller di gioco). `UIPress` eventi descrivono il pulsante premuto e il relativo stato (inizio, annullato, modificato o terminato).

Per i pulsanti analoghi nei dispositivi come i controller di gioco Bluetooth, `UIPress` restituisce anche la quantità di forza applicata al pulsante. La proprietà `Type` dell'evento `UIPress` definisce il pulsante fisico che ha modificato lo stato, mentre le altre proprietà descrivono la modifica che si è verificata.

Il codice seguente illustra un esempio di gestione di eventi `UIPress` di basso livello per un `UIView`:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvRemote
{
    public partial class EventView : UIView
    {
        #region Computed Properties
        public override bool CanBecomeFocused {
            get {
                return true;
            }
        }
        #endregion

        #region
        public EventView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void PressesBegan (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesBegan (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Red;
                }
            }
        }

        public override void PressesCancelled (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesCancelled (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }

        public override void PressesChanged (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesChanged (presses, evt);
        }

        public override void PressesEnded (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesEnded (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }
        #endregion
    }
}
```

Come per gli eventi di `UITouch`, se è necessario implementare uno degli override degli eventi di `UIPress`, è necessario implementare tutti e quattro.

<a name="Bluetooth-Game-Controllers" />

## <a name="bluetooth-game-controllers"></a>Controller del gioco Bluetooth

Oltre al comando standard Siri Remote fornito con Apple TV, i controller di gioco Bluetooth di terze parti, eseguiti per iOS (IFM), possono essere abbinati a Apple TV e usati per controllare l'app Novell. tvOS.

[![](remote-bluetooth-images/game01.png "Bluetooth Game Controllers")](remote-bluetooth-images/game01.png#lightbox)

I controller dei giochi possono essere usati per migliorare la giocabilità e fornire un'idea di coinvolgimento in un gioco. Possono anche essere usati per controllare l'interfaccia standard di Apple TV, in modo che l'uso non debba passare tra il controller remoto e il controller.

> [!IMPORTANT]
> I controller di gioco Bluetooth sono un acquisto facoltativo che può essere creato dagli utenti finali, l'app non può forzare l'acquisto di un utente. Se l'app supporta i controller di gioco, deve supportare anche Siri remote, in modo che il gioco sia utilizzabile da tutti gli utenti di Apple TV.

Un controller di gioco presenta le funzionalità e gli utilizzi previsti seguenti all'interno dell'app tvOS:

|Caratteristica|Utilizzo generale delle app|Utilizzo app gioco|
|---|---|---|
|**D-Pad**|Consente di spostarsi tra gli elementi dell'interfaccia utente (modifica lo stato attivo).|Dipende da gioco.|
|**A**|Attiva l'elemento selezionato (messa a fuoco).|Esegue la funzione del pulsante Primary e conferma le azioni della finestra di dialogo.|
|**B**|Torna alla schermata precedente o esce dalla schermata iniziale se si trova nella schermata principale dell'app.|Esegue la funzione del pulsante secondario o torna alla schermata precedente.|
|**X**|Avvia la riproduzione dei supporti o sospende/riprende la riproduzione.|Dipende da gioco.|
|**S**|n/d|Dipende da gioco.|
|**Menu**|Torna alla schermata precedente o esce dalla schermata iniziale se si trova nella schermata principale dell'app.|Sospendere o riprendere il gameplay, tornare alla schermata precedente o uscire dalla schermata iniziale se si trova nella schermata principale dell'app.|
|**Pulsante sinistro**|Consente di spostarsi verso sinistra.|Dipende da gioco.|
|**Trigger a sinistra**|Consente di spostarsi verso sinistra.|Dipende da gioco.|
|**Pulsante destro della spalla**|Consente di spostarsi a destra.|Dipende da gioco.|
|**Trigger a destra**|Sposta a destra|Dipende da gioco.|
|**Levetta a sinistra**|Consente di spostarsi tra gli elementi dell'interfaccia utente (modifica lo stato attivo).|Dipende da gioco.|
|**Levetta destro**|n/d|Dipende da gioco.|

Apple fornisce i suggerimenti seguenti per lavorare con i controller di gioco:

- **Confermare le connessioni del controller di gioco** : l'app tvOS può essere avviata e arrestata in qualsiasi momento dall'utente finale. È consigliabile verificare sempre la presenza di un controller di gioco all'ora di inizio o di fine dell'app e intraprendere le azioni necessarie.
- **Assicurarsi che l'app funzioni sia su Siri remote che su controller di gioco** . non richiedere agli utenti di passare da Siri remote a un controller di gioco e viceversa per usare l'app. Testare l'app spesso con entrambi i tipi di controller garantendo che tutto sia facile da esplorare e funzioni come previsto.
- **Fornire un modo per eseguire il backup** del pulsante di menu deve sempre tornare alla schermata precedente. Se l'utente si trova nella schermata principale dell'app, il pulsante di menu deve tornare alla schermata iniziale di Apple TV. Durante il gioco, il pulsante di menu Visualizza un avviso che consente all'utente di sospendere o riprendere il gameplay o tornare al menu principale.

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>Uso dei controller di gioco

Come indicato in precedenza, oltre alla versione standard di Siri Remote fornita con Apple TV, l'utente può facoltativamente associare un controller di gioco Bluetooth di terze parti, creato per iOS (IFM) e usarlo per controllare l'app Novell. tvOS.

Se l'app richiede un input del controller di basso livello, è possibile usare il [Framework di Game controller](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276) di Apple con le modifiche seguenti per tvOS:

- Il profilo di micro Game controller (`GCMicroGamepad`) è stato aggiunto per la destinazione Siri remote.
- La nuova classe di `GCEventViewController` può essere usata per indirizzare gli eventi di Game controller tramite l'app. Per altri dettagli, vedere la sezione [determinazione dell'input del controller di gioco](#determining-game-controller-input) di seguito.

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>Requisiti del supporto del controller di gioco

Apple presenta diversi requisiti specifici che devono essere soddisfatti se l'app Novell. tvOS supporta i controller di gioco:

- **È necessario supportare Siri Remote** . è sempre necessario supportare Siri remote. Il gioco non può richiedere la riproducibilità di un controller di gioco di terze parti.
- **È necessario supportare il layout di controllo esteso** : tutti i controller di gioco tvOS sono controller estesi non formfitting.
- I **giochi devono essere riproducibili con i controller** autonomi. se l'app supporta un controller di gioco esteso, deve essere riproducibile solo con tale controller di gioco.
- **È necessario supportare il pulsante Riproduci/Sospendi** -durante il gioco, se l'utente preme il pulsante Riproduci/Sospendi, dovrebbe essere visualizzato un avviso che consente all'utente di sospendere o riprendere il gameplay oppure tornare al menu principale.

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>Abilitazione del supporto del controller di gioco

Per abilitare il supporto del controller di gioco nell'app Novell. tvOS, fai doppio clic sul file `Info.plist` nel **Esplora soluzioni** per aprirlo per la modifica:

[![](remote-bluetooth-images/game02.png "The Info.plist editor")](remote-bluetooth-images/game02.png#lightbox)

Nella sezione **Game controller** inserire un segno di spunta per **abilitare i controller di gioco**, quindi controllare tutti i tipi di controller di gioco che saranno supportati dall'app.

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>Uso di Siri remote come controller di gioco

I Siri remote disponibili con Apple TV possono essere usati come controller di gioco limitato. Analogamente ad altri controller dei giochi, viene visualizzato nel Framework di Game controller come oggetto `GCController` e supporta i profili `GCMotion` e `GCMicroGamepad`.

Il Siri Remote presenta le caratteristiche seguenti quando viene usato come controller di gioco:

- È possibile usare la superficie di tocco come un D-Pad che fornisce dati di input analoghi.
- Remote può essere usato in un orientamento verticale o orizzontale e l'app decide se l'oggetto profilo deve capovolgere automaticamente i dati di input.
- Facendo clic sull'area di tocco si agisce come la pressione del pulsante **a** in un controller di gioco.
- Il pulsante Riproduci/Sospendi funge da pulsante **X** in un controller di gioco.
- Il pulsante di menu Visualizza un avviso che consente all'utente di sospendere o riprendere il gameplay o tornare al menu principale.

<a name="Summary" />

### <a name="determining-game-controller-input"></a>Determinazione dell'input del controller di gioco

A differenza di iOS, in cui gli eventi del Game controller possono essere ricevuti in parallelo con gli eventi di tocco, tvOS elabora tutti gli eventi di basso livello per fornire eventi `UIKit` di alto livello. Di conseguenza, se è necessario accedere agli eventi del controller di gioco di basso livello, è necessario disattivare il comportamento predefinito di `UIKit`.

In tvOS, quando si vuole elaborare direttamente l'input di Game controller, è necessario usare una `GCEventViewController` (o una sottoclasse) per visualizzare l'interfaccia utente del gioco. Ogni volta che un `GCEventViewController` è il *primo risponditore*, l'input del controller di gioco viene acquisito e distribuito all'app tramite il Framework del controller di gioco.

È possibile utilizzare la proprietà `UserInteractionEnabled` della classe `GCEventViewController` per impostare il modo in cui gli eventi vengono elaborati e gestiti.

Per informazioni sull'implementazione del supporto del controller di gioco, vedere la sezione uso di Apple con i controller di [gioco](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html) nella Guida alla [programmazione delle app per TvOS e la](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html) Guida alla programmazione di [Game controller](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html).

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha analizzato il nuovo Siri Remote fornito con i pulsanti di Apple TV, movimenti della superficie di tocco e Siri remote. Viene quindi analizzato l'utilizzo di movimenti e storyboard, movimenti e codice ed eventi di basso livello. Infine, se si discute di usare i controller di gioco.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
