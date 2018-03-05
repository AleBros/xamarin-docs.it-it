---
title: Rapido tecniche di interazione per watchOS 3
description: "Questo articolo vengono illustrate le tecniche di breve Apple è aggiunto watchOS 3 e come implementarli in xamarin. IOS per Apple Watch."
ms.topic: article
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 75a8e807a68a3fccfa76fc7ba1f260818b25174d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="quick-interaction-techniques-for-watchos-3"></a>Rapido tecniche di interazione per watchOS 3

_Questo articolo vengono illustrate le tecniche di breve Apple è aggiunto watchOS 3 e come implementarli in xamarin. IOS per Apple Watch._

Fornisce le interazioni dell'utente rapido sono essenziali per la creazione di efficaci applicazioni Apple Watch e complessità. Nuovo per watchOS 3, Apple ha aggiunto il supporto per i riconoscitori di movimento, accedere alle nuove tecniche di notifica utente e la navigazione e chioma digitale. Questa operazione, nonché il supporto aggiunto per SceneKit e SpriteKit, consente allo sviluppatore di creare facilmente completo e concise interfacce che sono reattiva e rapido.

## <a name="what-are-quick-interactions"></a>Quali sono le interazioni rapido

Per uno sviluppatore che viene utilizzato per la creazione di applicazioni per iOS o Mac OS (in cui la quantità di tempo che trascorre un utente interagisce con l'app è misurata in ore o minuti), progettazione di un'applicazione ha esito positivo per il Apple Watch può rivelarsi complessa e richiede un altro approccio.

In watchOS, l'utente richiede in genere generare i relativi polso, rapidamente interagire con un'applicazione (in genere per un paio di secondi), quindi eliminare le polso e continuare lo stato che si verificasse.

Di seguito sono indicati alcuni esempi di interazioni rapide tipiche nel Apple Watch:

- Avvio di un timer.
- Verifica del tempo.
- Contrassegnare un elemento all'esterno di un elenco di attività.

Per raggiungere questi obiettivi, deve essere un'app sul Apple Watch:

- **Concise** , vale a dire che con un rapido riepilogo l'utente deve essere in grado di ottenere le informazioni necessarie. 
- **Eseguibili** -che indica gli utenti deve essere in grado di prendere decisioni circostanziate e veloce.
- **Reattiva** , ovvero l'utente non deve dipendere per ricevere le informazioni che devono essere oppure per ottenere l'azione desiderata.

### <a name="quick-interactions-length"></a>Lunghezza interazioni rapido

A causa della natura concise delle app di Apple Watch, Apple suggerisce che la lunghezza di un'interazione rapida ideale deve essere di due secondi o meno. In seguito a questo limite secondo di due, lo sviluppatore dovrà spesa una notevole quantità di tempo sia progettazione e implementazione di un'app di Apple Watch. 

## <a name="new-watchos-3-features-and-apis"></a>Nuovo watchOS 3 funzionalità e API

Apple è aggiunto numerose nuove funzionalità e API WatchKit per assistere lo sviluppatore di aggiungere le interazioni rapide delle app di Apple Watch:

- watchOS 3 fornisce l'accesso a nuovi tipi di input, ad esempio utente:
    - Riconoscitori di movimento
    - Rotazione chioma digitale 
- watchOS 3 vengono fornite nuove modalità di visualizzazione e l'aggiornamento delle informazioni, ad esempio:
    - Navigazione della tabella avanzate
    - Il nuovo supporto per framework notifica utente
    - Integrazione di SpriteKit e SceneKit

Mediante l'implementazione di queste nuove funzionalità, lo sviluppatore possibile garantire che la propria applicazione watchOS 3 Glanceable, realizzabili e Responsive.

### <a name="gesture-recognizer-support"></a>Supporto per il riconoscimento di movimento

Se lo sviluppatore ha implementato i riconoscitori di movimento in iOS, deve trattarsi di dimestichezza con modalità di funzionamento i riconoscitori di movimento in watchOS 3. Per aggiornare, i riconoscitori di movimento sono oggetti che eventi tocco di basso livello di analisi in movimenti riconoscibili, predefiniti.

watchOS 3 offrirà supporto per i riconoscitori di movimento seguenti quattro:

- Tipi di movimenti discreti:
    - La combinazione di scorrere (`WKSwipeGestureRecognizer`).
    - I movimenti tocco (`WKTapGestureRecognizer`).
- Tipi di movimento continua:
    - Il movimento zoom (`WKPanGestureRecognizer`).
    - La combinazione di pressione prolungata (`WKLongPressGestureRecognizer`).

Per implementare una ai nuovi riconoscitori di movimento, trascinarlo su una superficie di progettazione in iOS progettazione di Visual Studio per Mac e configurarne le proprietà.

Nel codice, risposta all'azione di riconoscimento per gestire l'azione viene attivata dall'utente. Nuovamente, questa operazione viene eseguita nello stesso modo come potrebbe essere gestito in iOS.

#### <a name="discrete-gesture-states"></a>Stati di movimento discreti

Per i movimenti discreti, l'azione viene chiamato quando l'azione viene riconosciuto e uno stato (`WKGestureRecognizerState`) viene assegnato come:

[ ![](quick-interaction-techniques-images/quick01.png "Stati di movimento discreti")](quick-interaction-techniques-images/quick01.png)

Tutti i movimenti discreti inizia `Possible` lo stato e transizione in uno di `Failed` o `Recognized` stato. Quando si utilizzano i movimenti discreti, lo sviluppatore in genere non gestione direttamente lo stato. Al contrario, si basano sull'azione di cui viene chiamato quando l'azione viene riconosciuto solo.

#### <a name="continuous-gesture-states"></a>Stati di movimento continua

I movimenti continui sono leggermente diversi dai movimenti discreta, in cui l'azione viene chiamato più volte come viene riconosciuta la combinazione di:

[ ![](quick-interaction-techniques-images/quick02.png "Stati di movimento continua")](quick-interaction-techniques-images/quick02.png)

Nuovamente, i movimenti continua viene avviata nel `Possible` sullo stato dello stato, ma il numero degli aggiornamenti. In questo caso lo sviluppatore dovrà prendere in considerazione lo stato del sistema di riconoscimento e l'aggiornamento dell'interfaccia utente dell'applicazione durante la `Changed` fase fino a quando il movimento è infine `Recognized` o `Canceled`.

#### <a name="gesture-recognizer-usage-tips"></a>Suggerimenti sull'utilizzo riconoscitore di movimento

Quando si utilizzano i riconoscitori di movimento in watchOS 3, Apple Suggerire le operazioni seguenti:

- Aggiungere i riconoscitori di movimento a elementi del gruppo anziché singoli controlli. Poiché l'Apple Watch ha dimensioni fisiche dello schermo più piccole, gli elementi di gruppo tendono a essere più grande e più destinazioni per l'utente prema. Inoltre, i riconoscitori di movimento possono entrare in conflitto con incorporata movimenti già in controlli dell'interfaccia utente nativa.
- Impostare le relazioni di dipendenza nello Storyboard di espressioni di controllo dell'applicazione.
- Alcuni movimenti hanno la precedenza rispetto ad altri tipi di azione, ad esempio:
    - Lo scorrimento
    - Forzare il tocco
 
### <a name="digital-crown-rotation"></a>Rotazione chioma digitale

Mediante l'implementazione di supporto chioma digitale nelle proprie App watchOS 3, uno sviluppatore può fornire maggiore navigazione interazioni di velocità e precisione per i propri utenti.

Poiché watchOS 2, dell'app dell'Apple Watch è possibile utilizzare il `WKInterfacePicker` oggetto per accedere il chioma digitale fornendo un elenco di `WKPickerItems` e uno stile di selezione (elenco, in pila o sequenza di immagini). watchOS quindi consentito all'utente di utilizzare il chioma digitali per selezionare un elemento dall'elenco.

Quando si utilizza un `WKInterfacePicker`, WatchKit gestisce la maggior parte del lavoro per:

- L'elenco e gli elementi dell'interfaccia singoli di disegno.
- Elaborazione degli eventi chioma digitale.
- Chiamata di un'azione quando viene selezionato un elemento.

Nuovo per watchOS 3, lo sviluppatore dispone ora dell'accesso diretto agli eventi di rotazione chioma digitale che consente di creare i propri elementi dell'interfaccia utente di rispondere ai valori di rotazione.

Accesso chioma digitale viene fornito dagli elementi seguenti:

- `WKCrownSequencer` -Consente di accedere a rotazioni al secondo.
- `WKCrownDelegate` -Consente di accedere agli eventi di rotazione delta.

#### <a name="rotations-per-second"></a>Rotazioni al secondo

Accede rotazioni al secondo dal chioma digitale risulta utile quando le animazioni di base funzionante con fisica. Per accedere le rotazioni al secondo, utilizzare il `CrownSequencer` proprietà del `WKInterfaceController` dell'estensione di espressioni di controllo. Ad esempio:

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>Rotazione delta

Utilizzare i delta di rotazione dal chioma digitali per contare il numero di rotazione. Utilizzare il `CrownDidRotate` eseguire l'override del metodo del `WKCrownDelegate` accedano i delta di rotazione. Ad esempio:

```csharp
using System;
using WatchKit;
using Foundation;

namespace MonkeyWatch.MonkeySeeExtension
{
    public class CrownDelegate : WKCrownDelegate
    {
        #region Computed Properties
        public double AccumulatedRotations { get; set;}
        #endregion

        #region Constructors
        public CrownDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void CrownDidRotate (WKCrownSequencer crownSequencer, double rotationalDelta)
        {
            base.CrownDidRotate (crownSequencer, rotationalDelta);

            // Accumulate rotations
            AccumulatedRotations += rotationalDelta;
        }
        #endregion
    }
}
```

In questo caso l'applicazione gestisce un accumulatore (`AccumulatedRotations`) per determinare il numero di rotazione. Una rotazione completa parte digitale è uguale a un delta accumulato di `1.0` e sarebbe una rotazione metà `0.5`.

Apple ha lasciato il compito dello sviluppatore per determinare come i conteggi di rotazione corrispondono per la riservatezza delle modifiche per l'elemento dell'interfaccia utente da aggiornare.

Il segno (`+/-`) del Delta rotazione indica la direzione che l'utente è attivato il chioma digitale:

[ ![](quick-interaction-techniques-images/quick03.png "Il segno del Delta rotazione indica la direzione che l'utente è attivato il chioma digitale")](quick-interaction-techniques-images/quick03.png)


Se l'utente scorre backup, verrà restituito WatchKit delta positivo e se lo scorrimento verso il basso, quindi delta negativo viene restituito, indipendentemente da quali orientamento l'utente è che l'orologio in.

#### <a name="digital-crown-focus"></a>Stato attivo chioma digitale

Esattamente come qualsiasi altro elemento di interfaccia, il chioma digitali è presente il concetto di concentrazione. Questo stato attivo può essere spostato dal chioma digitale per altri elementi dell'interfaccia in base a come l'utente interagisce con l'orologio. 

Ad esempio, uno dei seguenti controlli può rubare lo stato attivo parte digitale:

- Selezione
- Slider
- Controller di scorrimento

Spetta allo sviluppatore di determinare quando il relativo elemento di interfaccia personalizzata deve essere lo stato attivo parte digitale. Apple consiglia di usare il nuovo riconoscitori di movimento per ottenere lo stato attivo nell'elemento dell'interfaccia utente personalizzata.

### <a name="vertical-paging"></a>Spostamento verticale

È la modalità standard che un utente si sposta una visualizzazione tabella in un'app watchOS per scorrere i dati desiderati, toccare su una riga specifica per visualizzare la visualizzazione dettagliata, toccare il pulsante Indietro al termine di visualizzazione dei dettagli e ripetere il processo per qualsiasi altra informazione che il y sono interessati all'interno della tabella:

[ ![](quick-interaction-techniques-images/quick04.png "Lo spostamento tra una tabella e la visualizzazione dettagli")](quick-interaction-techniques-images/quick04.png)

Nuovo per watchOS 3, lo sviluppatore può attivare il Paging verticale sui controlli di visualizzazione tabella. Con questa funzionalità è abilitata, l'utente può scorrere per individuare una riga di tabella e scegliere la riga per visualizzare il dettaglio come prima. Tuttavia, è possibile ora scorrere rapidamente per selezionare la riga successiva nella tabella o in basso per selezionare la riga precedente (oppure utilizzare il chioma digitale), tutto senza dover tornare alla visualizzazione tabella innanzitutto:

[ ![](quick-interaction-techniques-images/quick05.png "Lo spostamento tra una tabella e la visualizzazione di dettaglio e scorrimento rapido su e giù per spostarsi tra le altre righe")](quick-interaction-techniques-images/quick05.png)

Per abilitare questa modalità, aprire Storyboard dell'app watchOS in Xcode per la modifica, selezionare la visualizzazione della tabella e controllare il **Paging dettaglio verticale** casella di controllo:

[ ![](quick-interaction-techniques-images/quick06.png "Selezionare la casella di controllo Paging dettaglio verticale")](quick-interaction-techniques-images/quick06.png)

Verificare che la tabella utilizza Segues per visualizzare la visualizzazione dettagliata e salvare le modifiche allo Storyboard e tornare a Visual Studio per Mac per la sincronizzazione.

Lo sviluppatore è possibile coinvolgere a livello di programmazione spostamento verticale di una riga specifica tramite il codice seguente su una vista tabella:

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

Quando si usa il Paging verticale, lo sviluppatore deve essere a conoscenza che WatchKit gestirà automaticamente il precaricamento del controller e di conseguenza, alcuni metodi del ciclo di vita del controller possono essere chiamati prima che l'interfaccia utente è visibile o meno.

### <a name="notification-enhancements"></a>Miglioramenti di notifica

Notifica sono la forma principale di interazione rapida in cui un utente si verifica in genere in watchOS e rimangono disponibile dopo il primo Apple Watch e watchOS 1.

Un tipico interazione rapido di notifica è il seguente:

1. L'utente abbia tattili la notifica quando viene ricevuta una notifica di nuovo.
2. Generano loro polso per visualizzare l'interfaccia Cerca breve per la notifica.
3. Se continuano a mantenere le polso generato, watchOS diventa automaticamente l'interfaccia di notifica di ricerca lunghi.

Esistono diversi modi che un utente può rispondere alla notifica:

- Per una ben definiti e presentati notifica, dall'utente per non eseguire alcuna operazione e chiudere semplicemente la notifica.
- È anche possibile toccare della notifica per avviare l'app watchOS.
- Per una notifica che supporta azioni personalizzate, l'utente può selezionare una delle azioni personalizzate. Possono essere:
    - **Azioni di primo piano** -questi avviare l'app per eseguire l'azione.
    - **Le azioni in background** : sono stati indirizzati sempre iPhone in watchOS 2, ma può essere indirizzato a watchApp in watchOS 3.

Novità di watchOS 3:

* Notifica utilizza un'API simile in tutte le piattaforme (iOS, watchOS, tvOS e macOS).
* Notifica locale possa essere pianificata di Apple Watch.
* Notifica di sfondo verrà indirizzata all'estensione dell'app se sono state pianificate nel Apple Watch.

#### <a name="notification-scheduling-and-delivery"></a>Notifica di pianificazione e recapito

Notifica da iPhone dell'utente, è necessario inoltrare l'Apple Watch quando si verifica quanto segue:

* Schermata dell'iPhone è disattivata.
* L'Apple Watch viene attrezzatura e sbloccato.

In watchOS 3, le notifiche locale possono essere pianificate di Apple Watch e vengono fornite solo nelle espressioni di controllo. È attivo allo sviluppatore di pianificare un iPhone corrispondente notifica se è necessaria per l'app.

Includendo lo stesso identificatore di notifica in entrambe le versioni di iPhone delle notifiche e Apple Watch, impedisce le notifiche duplicate venga visualizzata sul controllo. La versione di Apple Watch della notifica avrà la precedenza rispetto alla versione iPhone.

Poiché watchOS 3 utilizza lo stesso `UINotification` framework API come 10, iOS, vedere il nostro iOS 10 [Framework notifica utente](~/ios/platform/user-notifications/index.md) documentazione per maggiori dettagli.

### <a name="using-spritekit-and-scenekit"></a>Utilizzo di SpriteKit e SceneKit

Nuovo per watchOS 3, lo sviluppatore può ora utilizzare oggetti SpritKit sia SceneKit nella progettazione dell'interfaccia utente dell'applicazione per presentare grafica sia 2D e 3D.

Due nuove classi di interfaccia sono state aggiunte per supportare questa funzionalità:

- `WKInterfaceSKScene` -Per l'utilizzo con la grafica 2D SpriteKit.
- `WKInterfaceSCNScene` -Per l'utilizzo con la grafica 3D SceneKit.

Per utilizzare questi oggetti, semplicemente trascinarli nell'area di progettazione all'interno Storyboard dell'applicazione di espressioni di controllo in Generatore del Xcode di interfaccia e utilizzare il **attributi controllo** per configurarli.

A questo punto, utilizzo di scene SpriteKit o SceneKit funziona allo stesso analogo a quanto avviene all'interno di un'app iOS. L'applicazione di espressioni di controllo mostrerà una `WKInterfaceSKScene` chiamando uno del `Present` metodi. Per SceneKit, impostare semplicemente il `Scene` proprietà del `WKInterfaceSCNScene` oggetto.

## <a name="actionable-complications"></a>Complicazioni utilizzabili

In watchOS 2, Apple ha introdotto complicazioni per le applicazioni di terze parti 3rd. WatchOS 3, Apple ha espanso il possibilità che uno sviluppatore può includere in una complicazione WatchKit. 

Inoltre, più incorporati in espressioni di controllo dei caratteri tipografici possono ora includere complicazioni ed espressioni di controllo esistente è rivolto già complicazioni supportati possono ora inclusi anche ulteriori complessità.

Anche nuovi è la possibilità per un utente per scorrere rapidamente verso sinistra o destra per passare tutte le facce di espressioni di controllo che sono stati installati nel loro Apple Watch. Usando la nuova raccolta nell'app iPhone complementare dell'Apple Watch, l'utente può aggiungere e personalizzare nuovi facce di espressioni di controllo e i problemi che possono includere.

A causa di queste nuove funzionalità, Apple suggerisce che ogni app in Apple Watch devono includere almeno una complicazione e di conseguenza, tutte le app Native Apple Watch disporrà complicazioni.

Complessità forniscono le funzionalità seguenti per un'app:

- Sono altamente concise perché sono sempre presenti sul quadrante dell'espressione di controllo.
- Complicazioni vengono aggiornati di frequente da watchOS. Qualsiasi app che include una complicazione sul quadrante di espressioni di controllo attualmente visualizzato dell'utente viene aggiornata almeno due volte un'ora.
- Qualsiasi app con una complicazione sul quadrante di espressioni di controllo attualmente visualizzato dell'utente viene mantenuto in memoria che rende l'app di avviare rapidamente e migliora la velocità di risposte dall'app.
- Complicazioni semplificano all'utente di avviare le funzionalità specifiche in un'app watchOS.

## <a name="glanceable-notification"></a>Notifica concise

Notifica di Apple Watch consentono di grande e personalizzabile rapidamente informare l'utente degli eventi o nuove informazioni, ad esempio i messaggi in ingresso o raggiungere un obiettivo in un'app allenamenti.

Tramite una notifica, preziosi possono essere rapidamente presentate all'utente. In molti casi, una notifica ben progettata può rimuovere la necessità per l'utente avviare effettivamente l'app.

Novità di watchOS 3, tutte le notifiche ora supportano:

- SpriteKit
- SceneKit
- inline Video

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>Interfaccia utente avanzata con SpriteKit e SceneKit

In genere, uno sviluppatore può essere considerata dell'interfaccia utente del gioco quando SpriteKit e SceneKit indicati. Tuttavia, può essere utile per la creazione di interfacce utente che includono layout personalizzati, contenuto e le animazioni che non sono possibili in WatchKit solo in caso contrario non giochi SpriteKit sia SceneKit.

Ad esempio, una notifica all'utente da una foto app di condivisione è possibile utilizzare SpriteKit per fornire un'esperienza utente avanzata includendo l'utente che ha registrato l'immagine con l'immagine effettiva e altre informazioni personalizzate che arricchisce l'esperienza utente.

Inoltre, SpriteKit sia SceneKit può essere combinato con gli elementi UI WatchKit standard nella progettazione dell'interfaccia utente dell'applicazione.

## <a name="simple-navigation"></a>Spostamento semplice

watchOS 3 presenta vari modi che uno sviluppatore può semplificare la navigazione all'interno delle App watchOS, ad esempio il nuovo [Paging verticale](#Vertical-Paging), [supporto riconoscitore di movimento](#Gesture-Recognizer-Support) e [chioma digitale Rotazione](#Digital-Crown-Rotation) funzionalità descritti in precedenza.

Il chioma digitale è univoca per l'Apple Watch e può essere utilizzata in molti modi diversi per semplificare la navigazione. Ad esempio, un'applicazione di timer può utilizzare il chioma digitali per eseguire lo scrubbing tramite lunghezze timer disponibili.

Movimenti personalizzati possono essere presentati modi nuovi e univoci per l'utente di interagire con un'applicazione di espressioni di controllo e consente anche di semplificare la navigazione delle app.

Apple è consigliabile determinare la modalità di combinare tutte le nuove funzionalità di interazione rapido aggiunta in watchOS 3 per presentare RTF, semplice e rapido utilizzare interfacce app watchOS.

## <a name="finishing-the-quick-interaction"></a>Terminare l'interazione rapido

Un'esperienza di interazione rapido ben progettata assegnare all'utente il livello di confidenza per eliminare i relativi polso (e disattivare con l'app) quando si hanno terminato l'interazione corrente.

Dove in particolare, questo diventa un problema è quando l'applicazione di espressioni di controllo è eseguire qualsiasi tipo di connessione di rete o condividere informazioni con app iPhone complementare. Questo spesso può causare un indicatore di attesa durante la transazione, che non è auspicabile durante un'interazione rapida. Vedere l'esempio seguente:

[ ![](quick-interaction-techniques-images/quick07.png "Diagramma dell'espressione di controllo app esegue una connessione di rete e condivisione di informazioni con app iPhone complementare")](quick-interaction-techniques-images/quick07.png)

1. L'utente sceglie un elemento per l'acquisto nell'orologio.
2. Essi toccare il pulsante di acquisto.
3. L'app viene avviata la transazione di rete e viene visualizzato un indicatore di caricamento.
4. Successivamente, al completamento della transazione e l'applicazione visualizza una conferma di acquisto.
5. L'utente rilascia loro polso e consente di scollegare con l'app.

Dal momento in cui che l'utente tocca il pulsante di acquisto fino a quando la transazione viene completata, hanno loro polso generato esaminando un indicatore di caricamento. Per risolvere questa situazione, Apple suggerisce che lo sviluppatore deve presentare un feedback immediato all'utente anziché mostrare un indicatore di caricamento. 

Utilizza modello suggerito di Apple, esaminare nuovamente la stessa interazione rapido:

[ ![](quick-interaction-techniques-images/quick08.png "Diagramma del modello suggerito mele")](quick-interaction-techniques-images/quick08.png)

1. L'utente sceglie un elemento per l'acquisto nell'orologio.
2. Essi toccare il pulsante di acquisto.
3. L'app viene avviata la transazione di rete e viene visualizzato un messaggio che informa che l'acquisto è stato avviato correttamente.
4. L'utente rilascia loro polso e consente di scollegare con l'app.
5. Quando la transazione viene completata correttamente successivamente, l'app consente di visualizzare una notifica per informare l'utente di un acquisto corretta locale.

Questa volta non appena l'utente tocca il pulsante di acquisto che vengono visualizzati un messaggio che informa che l'acquisto è stato avviato, in modo che con fiducia può eliminare i relativi polso e terminare l'interazione rapido a questo punto. In un secondo momento vengono informati dell'esito positivo o negativo della transazione in una notifica all'utente. In questo modo, l'utente solo interagisce con l'app durante le fasi del processo "attive".

Per le applicazioni che eseguono operazioni in rete, possono utilizzare uno sfondo `NSURLSession` per gestire la comunicazione di rete con un'attività di download. Ciò consente all'app di essere riattivato in background per elaborare le informazioni scaricate. Per app che richiedono l'elaborazione in background, usare un'asserzione di attività in Background per gestire l'elaborazione necessaria.

## <a name="quick-interaction-design-tips"></a>Suggerimenti relativi alla progettazione breve

Poiché la lunghezza desiderata di un'interazione rapida è di due secondi o meno, lo sviluppatore deve concentrarsi sulla progettazione di interazioni dell'app fin dall'inizio del processo di progettazione. Individuare le aree in cui tali interazioni possono essere semplificate (mediante la tecnica descritta in precedenza) e utilizzano le nuove funzionalità di watchOS 3 per rendere l'app reattiva e rapido.

Apple suggerisce le operazioni seguenti:

- Concentrarsi sulle interazioni rapido riportando le funzionalità dell'app più avanti.
- Utilizzare complicazioni e le notifiche utente per esporre le funzioni comuni.
- Creare l'interfaccia utente concise avanzato con SceneKit e SpriteKit.
- Quando possibile, semplificare la navigazione all'interno dell'app.
- Non apportare mai l'utente in attesa, consentono di eliminare i relativi polso e disattivare con l'app appena possibile.

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati le tecniche di breve Apple è aggiunto watchOS 3 e come implementarli in xamarin. IOS per Apple Watch.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di watchOS](https://developer.xamarin.com/samples/watchos/all/)
