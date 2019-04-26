---
title: Controller Bluetooth per tvOS in Xamarin e remoti per Siri
description: Questo articolo descrive come lavorare con le periferiche di gioco Bluetooth nelle App tvOS scritte con Xamarin e remoti per Siri.
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 79022f7a454ea423fa3112a4c4ade2bcd471fbb8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60933070"
---
# <a name="siri-remote-and-bluetooth-controllers-for-tvos-in-xamarin"></a>Controller Bluetooth per tvOS in Xamarin e remoti per Siri

Gli utenti dell'app xamarin. tvos verranno non interagisce con relativa interfaccia direttamente con iOS in cui vengono toccati immagini sullo schermo del dispositivo, ma è indirettamente da attraverso le chat room tramite il [remoti per Siri](#The-Siri-Remote).

Se l'app è un gioco, è possibile, facoltativamente, creare il supporto per l'entità 3rd, apportate per iOS (IFM) [periferiche di gioco Bluetooth](#Bluetooth-Game-Controllers) in anche nell'app.

[![](remote-bluetooth-images/intro01.png "Il Controller di gioco e Bluetooth remoto")](remote-bluetooth-images/intro01.png#lightbox)

Questo articolo descrive la [remoti per Siri](#The-Siri-Remote), [Touch movimenti nell'area](#Touch-Surface-Gestures) e [pulsanti remoto Siri](#Siri-Remote-Buttons) e viene illustrato come utilizzarle tramite [movimenti e Gli storyboard](#Gestures-and-Storyboards), [codice e i movimenti](#Gestures-and-Code) e [la gestione degli eventi di basso livello](#Low-Level-Event-Handling). Infine, illustra [uso di periferiche di gioco](#Working-with-Game-Controllers) in un'app xamarin. tvos.

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>Il remoti per Siri

Il modo principale che verranno interagisce con Apple TV e app xamarin. tvos, gli utenti è tramite il remoti per Siri incluso. Apple progettato per colmare la distanza tra l'utente che si trova nel divano e l'interfaccia utente di un Apple TV visualizzata tutta la stanza sullo schermo TV remoto.

La sfida di sviluppatore di app tvOS è la creazione di un'interfaccia utente rapida, facile da usare e visivamente accattivanti che si basa su surface tocco di Siri Remote, accelerometro, il Giroscopio e pulsanti.

[![](remote-bluetooth-images/remote01.png "Il remoti per Siri")](remote-bluetooth-images/remote01.png#lightbox)

Il remoti per Siri sono gli utilizzi previsto all'interno dell'app tvOS e le funzionalità seguenti:

|Funzionalità|Utilizzo delle App di generale|Utilizzo delle App di gioco|
|---|---|---|
|**Nell'area di tocco**<br />Scorrere rapidamente per passare, fare clic per selezionare e tenere premuto per i menu contestuali.|**Scorrere verso il tocco /**<br />Navigazione nell'interfaccia utente tra gli elementi con stato attivabili.<br /><br />**Fare clic su**<br />Attiva (messa a fuoco) selezionato.|**Scorrere verso il tocco /**<br />Dipende dalla progettazione di giochi e può essere utilizzato come un riempimento D toccando i bordi.<br /><br />**Fare clic su**<br />Eseguire la funzione di pulsante primario.|
|**Menu**<br />Premere per tornare alla schermata precedente o menu.|Restituisce alla schermata precedente e viene chiuso per Apple TV Home screen dalla schermata app principale.|Sospendere e riprendere la modalità di gioco, consente di tornare alla schermata precedente e viene chiusa per Apple TV Home screen dalla schermata app principale.|
|**Siri/ricerca**<br />Nei paesi con Siri, premere e tenere premuto per il controllo voce, in tutti gli altri paesi, viene visualizzata la schermata di ricerca.|N/D|N/D|
|**Riproduci/Pausa**<br />Riprodurre e sospendere supporti o offre una funzione secondaria nelle app.|Avvia la riproduzione multimediale e sospendere o riprendere la riproduzione.|Esegue la funzione di pulsante secondario o video introduttivo su Ignora (se presente).|
|**Home**<br />Premere per tornare alla schermata iniziale, fare doppio clic per visualizzare le App in esecuzione, premere e tenere premuto per dispositivo di sospensione.|N/D|N/D|
|**Volume**<br />I controlli collegati volume apparecchiature audio/video.|N/D|N/D|

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>Superficie sui movimenti tocco

È in grado di rilevare un'ampia gamma di movimenti di un singolo dito che è possibile rispondere a nell'app xamarin. tvos Touch area di Siri Remote:

|Scorrimento rapido|Fare clic su|Tocco|
|---|---|---|
|![](remote-bluetooth-images/Gesture01.png)|![](remote-bluetooth-images/Gesture02.png)|![](remote-bluetooth-images/Gesture03.png)|
|Selezione (dello stato attivo) viene spostata tra gli elementi dell'interfaccia utente sullo schermo (verso l'alto, in basso a sinistra, pulsante destro del mouse). Scorrendo rapidamente è utilizzabile per scorrere gli elenchi di grandi dimensioni del contenuto rapidamente con l'inerzia.|Attiva l'elemento selezionato (messa a fuoco) o funge da pulsante primario in un gioco. Facendo clic e tenendo premuto può attivare funzioni secondarie o i menu contestuali.|Certamente preso alla leggera toccando la superficie Touch sui bordi funziona come pulsanti direzionali su un D-blocco note, lo stato attivo passa alto, basso, a sinistra o destra a seconda dell'area toccato. A seconda dell'app, è utilizzabile per visualizzare i controlli nascosti.|

Apple offre i seguenti suggerimenti per l'uso di movimenti Touch area:

* **Distinguere tra clic e tocca** -facendo clic su un'azione intenzionale dall'utente ed è particolarmente adatto per la selezione, attivazione e il pulsante principale di un gioco. Se tocchi risulta più evidente e devono essere utilizzata con cautela, poiché l'utente è spesso bloccata da remoto Siri a loro disposizione e accidentalmente può attivare un evento di tocco con facilità.
* **Non ridefinire i movimenti Standard** -l'utente ha un'aspettativa che movimenti specifici eseguirà le azioni specifiche, è consigliabile non ridefinire il significato o la funzione di tali movimenti nell'app. L'unica eccezione è un'app di gioco durante la modalità di gioco attivo.
* **Definiscono nuovi gesti sporadicamente** -anche in questo caso, l'utente ha un'aspettativa che movimenti specifici eseguirà le azioni specifiche. È consigliabile evitare la definizione di movimenti personalizzati per eseguire le azioni standard. E anche in questo caso, giochi sono l'eccezione più normale in cui movimenti personalizzati possono aggiungere gioco divertente e coinvolgente e concreto al gioco.
* **Se appropriato, rispondere a D Pad tocca** - Pennellini toccando nell'angolo bordi dell'area di Touch verranno react, ad esempio un tastierino D in un messa a fuoco lo spostamento di gioco o in direzione verso l'alto, verso il basso, a sinistra o verso destra. Se appropriato, è necessario rispondere a questi movimenti nel gioco o dell'app.

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Pulsanti remoti per Siri

Oltre ai movimenti nell'area di tocco, l'app può rispondere all'utente facendo clic su area di tocco o premendo il pulsante Play/Pausa. Se si accede a Siri remoto usando il Framework di Controller di gioco, è anche possibile rilevare il pressione del pulsante di Menu. 

Inoltre, pressione di pulsanti di menu possono essere rilevati tramite un riconoscitore di movimento con lo standard `UIKit` elementi. Se si intercetta il pressione del pulsante di Menu, si sarà essere responsabile della chiusura la visualizzazione e il Controller di visualizzazione corrente e tornare a quello precedente.

> [!IMPORTANT]
> È consigliabile **sempre** assegnare una funzione al pulsante Riproduci/Pausa nel sistema remoto. Inserire un pulsante non funzionale può rendere l'app ricerca interrotto all'utente finale. Se non si dispone di una funzione valida per questo pulsante, assegnare la stessa funzione di pulsante primario (Touch fare clic nell'area).

<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>I movimenti e storyboard

Il modo più semplice per lavorare con i remoti per Siri nell'app xamarin. tvos consiste nell'aggiungere i riconoscitori di movimento per le visualizzazioni nella finestra di progettazione dell'interfaccia.

Per aggiungere un sistema di riconoscimento di movimenti, eseguire le operazioni seguenti:

1. Nel **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file e aprirlo e modificarlo nella finestra di progettazione di interfaccia.
2. Trascinare un **riconoscitore di movimento toccare** dal **libreria** e rilasciarla sulla vista: 

    [![](remote-bluetooth-images/storyboard01.png "Un riconoscitore di movimento Tap")](remote-bluetooth-images/storyboard01.png#lightbox)
3. Controllare **selezionare** nel **pulsante** sezione del **attributo Inspector**: 

    [![](remote-bluetooth-images/storyboard02.png "Controlla selezionare")](remote-bluetooth-images/storyboard02.png#lightbox)
4. **Selezionare** significa che il movimento di rispondere alla richiesta utente facendo clic sulle **Touch superficie** nel sistema remoto Siri. È anche possibile scegliere di rispondere al **dal Menu**, **Riproduci/Pausa**, **backup**, **verso il basso**, **sinistra** e **Destra** pulsanti.
5. Successivamente, collegare un **azione** dal **toccare riconoscitore di movimento** e denominarlo `TouchSurfaceClicked`: 

    [![](remote-bluetooth-images/storyboard03.png "Un'azione dal riconoscimento di movimento Tap")](remote-bluetooth-images/storyboard03.png#lightbox)
6. Salvare le modifiche e tornare a Visual Studio per Mac.

Modificare il Controller di visualizzazione (esempio `FirstViewController.cs`) file e aggiungere il codice seguente per gestire il movimento di attivazione:

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

Per altre informazioni sull'utilizzo degli storyboard, vedere la [App Tvos Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). In particolare il [creazione dell'interfaccia utente](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface) e [scrittura del codice con Outlet e azioni](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) sezioni.

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>I movimenti e codice

Facoltativamente, è possibile creare i movimenti direttamente in C# del codice e aggiungerle alle visualizzazioni in un'interfaccia utente. Ad esempio, per aggiungere una serie di scorrere i riconoscitori di movimento, modificare il Controller di visualizzazione e aggiungere il codice seguente:

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

Se si sta creando un tipo personalizzato basato sul `UIKit` nell'app xamarin. tvos (ad esempio `UIView`), hai anche la possibilità di fornire una gestione a basso livello della pressione pulsante tramite `UIPress` gli eventi. 

Oggetto `UIPress` evento è a tvOS ciò che un `UITouch` evento sia ai dispositivi iOS, ad eccezione di `UIPress` restituisce informazioni sul pulsante preme nel sistema remoto Siri o ad altri collegati dispositivi Bluetooth (ad esempio un Controller di gioco). `UIPress` eventi descrivono il pressione del pulsante e il relativo stato (Began, Canceled, modificate o terminato). 

Per i pulsanti analogici nei dispositivi, ad esempio Bluetooth periferiche di gioco, `UIPress` restituisce anche la quantità di forza viene applicato al pulsante. Il `Type` proprietà del `UIPress` evento definisce quale pulsante fisico è stato modificato, mentre il resto delle proprietà descrivono la modifica che si sono verificati.

Il codice seguente illustra un esempio di gestione di basso livello `UIPress` gli eventi per un `UIView`:

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

Come per gli `UITouch` eventi, se è necessario implementare uno qualsiasi del `UIPress` esegue l'override di eventi, è necessario implementare tutti e quattro.

<a name="Bluetooth-Game-Controllers" />

## <a name="bluetooth-game-controllers"></a>Periferiche di gioco Bluetooth

Oltre il remoti per Siri standard che viene fornito con la Apple TV, 3rd party, apportate per iOS periferiche di gioco Bluetooth (IFM) può essere confrontato con Apple TV e consentono di controllare l'app xamarin. tvos.

[![](remote-bluetooth-images/game01.png "Periferiche di gioco Bluetooth")](remote-bluetooth-images/game01.png#lightbox)

Periferiche di gioco sono utilizzabile per migliorare la modalità di gioco e fornire un senso di full immersion in un gioco. Possono inoltre essere utilizzati per controllare l'interfaccia Apple TV standard in modo che l'uso senza dover passare tra il computer remoto e il controller.

> [!IMPORTANT]
> Bluetooth gioco sono un acquisto facoltativo che potrebbero rendere gli utenti finali, l'app non può forzare l'utente per l'acquisto di uno. Se l'app supporta periferiche di gioco deve anche supportare il remoti per Siri in modo che il gioco è utilizzabile da tutti gli utenti di Apple TV.

Un Controller di gioco sono gli utilizzi previsto all'interno dell'app tvOS e le funzionalità seguenti:

|Funzionalità|Utilizzo delle App di generale|Utilizzo delle App di gioco|
|---|---|---|
|**D-Pad**|Consente di spostarsi tra gli elementi dell'interfaccia utente (modifiche dello stato attivo).|Dipende dal gioco.|
|**A**|Attiva l'elemento selezionato (messa a fuoco).|Esegue la funzione primaria del pulsante e le azioni di finestra di dialogo di conferma.|
|**B**|Restituisce alla schermata precedente o viene chiuso alla schermata iniziale se nella schermata principale dell'app.|Esegue la funzione di pulsante secondario o restituisce alla schermata precedente.|
|**X**|Avvia la riproduzione multimediale o sospendere e riprendere la riproduzione.|Dipende dal gioco.|
|**Y**|N/D|Dipende dal gioco.|
|**Menu**|Restituisce alla schermata precedente o viene chiuso alla schermata iniziale se nella schermata principale dell'app.|Sospendere o riprendere la modalità di gioco, consente di tornare alla schermata precedente o si chiude la schermata Home se nella schermata principale dell'app.|
|**Pulsante sinistro interno di una sessione**|Esegue la navigazione a sinistra.|Dipende dal gioco.|
|**Trigger a sinistra**|Esegue la navigazione a sinistra.|Dipende dal gioco.|
|**Pulsante destro interno di una sessione**|Consente di spostarsi a destra.|Dipende dal gioco.|
|**Trigger a destra**|Consente di spostarsi a destra|Dipende dal gioco.|
|**Thumbstick sinistro**|Consente di spostarsi tra gli elementi dell'interfaccia utente (modifiche dello stato attivo).|Dipende dal gioco.|
|**Thumbstick a destra**|N/D|Dipende dal gioco.|

Apple offre i seguenti suggerimenti per l'uso di periferiche di gioco:

- **Verificare le connessioni di Controller di gioco** -l'app tvOS possa essere avviato e interrotti in qualsiasi momento dall'utente finale. È sempre deve verificare la presenza di un Controller di gioco all'avvio dell'app o volte attivi e agire in base alle esigenze.
- **Verificare il funzionamento dell'App sia remoti per Siri periferiche di gioco** -non richiedono agli utenti di passare il remoti per Siri e un Controller di gioco per usare l'app. Testare l'app spesso con entrambi i tipi di controller di assicurare che tutto ciò che è facile passare e funziona come previsto.
- **Fornire un modo nuovo** -premendo il pulsante di Menu deve sempre restituire alla schermata precedente. Se l'utente si trova la schermata dell'app principale, il pulsante di Menu deve restituire li alla schermata Home Apple TV. Durante la modalità di gioco, il pulsante di Menu venga visualizzato un avviso fornendo all'utente la possibilità di sospendere o riprendere la modalità di gioco o tornare al menu principale.

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>Uso di periferiche di gioco

Come indicato in precedenza, oltre a standard remoti per Siri fornito con Apple TV, l'utente può facoltativamente collegare un 3rd party, apportate per iOS (IFM) Bluetooth periferiche di gioco e usarlo per controllare l'applicazione xamarin. tvos.

Se l'app è necessario input di basso livello di controller, si può usare Apple [Framework di Controller di gioco](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276) che ha le seguenti modifiche per tvOS:

- Il profilo del Controller di gioco Micro (`GCMicroGamepad`) è stato aggiunto come destinazione il remoti per Siri.
- Il nuovo `GCEventViewController` classe può essere utilizzata per instradare gli eventi del gioco tramite l'app. Vedere le [Input di Controller di gioco determinazione](#determining-game-controller-input) sezione di seguito per altri dettagli.

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>Requisiti per il supporto di gioco

Apple ha diversi requisiti specifici che devono essere soddisfatte se l'app xamarin. tvos supporta periferiche di gioco:

- **È necessario il supporto remoto Siri** -è sempre necessario supportare la remoti per Siri. Il tuo gioco non può richiedere una 3rd party gioco sia riproducibile.
- **È necessario supportare il Layout del controllo esteso** -tvOS tutte le periferiche di gioco sono non formfitting estesi controller.
- **Giochi devono essere Playable con controller stand-alone** -se un Controller di gioco estese supportate dall'app, deve essere riproducibile esclusivamente con quel Controller di gioco.
- **È necessario supportare il pulsante Play/Pausa** -durante la modalità di gioco, se l'utente preme il pulsante Play/Pausa, dovrebbe visualizzare un avviso fornendo all'utente la possibilità di sospendere o riprendere la modalità di gioco o tornare al menu principale.

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>Abilitazione del supporto di gioco

Per abilitare il supporto di gioco nell'app xamarin. tvos, fare doppio clic il `Info.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo:

[![](remote-bluetooth-images/game02.png "L'editor Info. plist")](remote-bluetooth-images/game02.png#lightbox)

Sotto il **gioco** sezione, inserire un segno di spunta dal **Abilita periferiche di gioco**, quindi verificare tutti i tipi di Controller di gioco che saranno supportati dall'app.

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>Utilizzando il remoti per Siri come periferica di gioco

Il remoti per Siri forniti con Apple TV è utilizzabile come Controller di gioco limitato. Quali altri controller di gioco, viene visualizzato in gioco Controller Framework come una `GCController` oggetto e supporta la `GCMotion` e il `GCMicroGamepad` profili.

Quando viene usato come un Controller di gioco, Siri remoto presenta le caratteristiche seguenti:

- Area di tocco è utilizzabile come D-riquadro che fornisce i dati di input analogici.
- Il computer remoto può essere usato con orientamento verticale o orizzontale e l'app decide se l'oggetto profilo deve invertire automaticamente i dati di input.
- Scegliere l'area Touch funge da pulsante **oggetto** in un Controller di gioco.
- Il pulsante Play/Pausa funge da pulsante **X** in un Controller di gioco.
- Il pulsante di Menu deve visualizzare un avviso fornendo all'utente la possibilità di sospendere o riprendere la modalità di gioco o tornare al menu principale.

<a name="Summary" />

### <a name="determining-game-controller-input"></a>Determinazione di Input di gioco

A differenza di iOS in cui gli eventi di gioco possono essere ricevuti in parallelo con gli eventi di tocco, tvOS elabora tutti gli eventi di basso livello per offrire ad alto livello `UIKit` gli eventi. Di conseguenza, se è necessario accedere agli eventi di gioco di basso livelli, è necessario disattivare `UIKit`del comportamento predefinito.

Su tvOS, quando si desidera elaborare l'input di gioco direttamente è necessario usare un `GCEventViewController` (o una sottoclasse) per visualizzare il gioco dell'interfaccia utente. Ogni volta che un `GCEventViewController` è il *risponditore prima*, input di gioco saranno acquisiti e recapitati all'App tramite il Framework di Controller di gioco.

È possibile usare la `UserInteractionEnabled` proprietà del `GCEventViewController` classe da attivare/disattivare la modalità gli eventi vengono elaborati e gestiti.

Per informazioni sull'implementazione del supporto per il Controller di gioco, vedi di Apple [uso di periferiche di gioco](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html) sezione il [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html) e [di gioco Guida per programmatori](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html).

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato il nuovo remoti per Siri fornito con i pulsanti di remoti per Siri, i movimenti Touch Surface e Apple TV. Successivamente, trattato uso di movimenti e storyboard, movimenti e codice e gli eventi di basso livello. Infine, se illustrato l'uso di periferiche di gioco.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
