---
title: Tecniche per interazioni rapide per watchOS 3 in Xamarin
description: Questo articolo illustra le tecniche per interazioni rapide Apple ha aggiunto in watchOS 3 e come implementarli in xamarin. IOS per Apple Watch.
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 6a8e74860efd606ae6dd565ea7e3f67884eefc11
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103218"
---
# <a name="quick-interaction-techniques-for-watchos-3-in-xamarin"></a>Tecniche per interazioni rapide per watchOS 3 in Xamarin

_Questo articolo illustra le tecniche per interazioni rapide Apple ha aggiunto in watchOS 3 e come implementarli in xamarin. IOS per Apple Watch._

Fornire interazioni dell'utente rapida sono essenziali per la creazione di applicazioni Apple Watch accattivanti e complessità. Nuovo a watchOS 3, Apple ha aggiunto il supporto per i riconoscitori di movimento, accesso ai Crown digitale e nuove tecniche di navigazione e notifica all'utente. Questa operazione, insieme al supporto aggiunto per SceneKit sia SpriteKit, consentono allo sviluppatore di creare con facilità interfacce avanzate e glanceable veloce e reattivo.

## <a name="what-are-quick-interactions"></a>Quali sono le interazioni rapide

Per gli sviluppatori che consente di creare applicazioni per iOS o macOS, in cui la quantità di tempo che un utente dedica l'interazione con l'app è espresso in minuti o ore, la progettazione di un'app di successo per l'Apple Watch può risultare difficile e richiede un oggetto diverso approccio.

In watchOS, l'utente desidera in genere generare il polso, rapidamente interagire con un'app (in genere per un paio di secondi), quindi eliminare il polso e continuare ripristinato lo stato che vengono eseguite.

Di seguito sono indicati alcuni esempi di interazioni rapide tipiche sul Apple Watch:

- A partire un timer.
- Verifica le condizioni meteo.
- Contrassegnare un elemento all'esterno di un elenco di attività.

Per raggiungere questi obiettivi, deve essere un'app nel Apple Watch:

- **Glanceable** -vale a dire che con un rapido riepilogo l'utente deve essere in grado di ottenere le informazioni di cui hanno bisogno. 
- **Utilità pratica** -che significa che gli utenti debba essere in grado di prendere decisioni ben informate e rapide.
- **Velocità di risposta** -che significa che l'utente deve attendere mai per ricevere le informazioni di cui hanno bisogno oppure per ottenere l'azione hanno l'esigenza di.

### <a name="quick-interactions-length"></a>Lunghezza di interazioni rapide

A causa della natura glanceable dell'App Apple Watch, suggerite da Apple che la lunghezza ideale di un'interazione rapida deve essere di due secondi o meno. In seguito a questo limite secondo due, lo sviluppatore dovrà dedicano una quantità elevata di tempo sia progettazione e implementazione di un'app per Apple Watch. 

## <a name="new-watchos-3-features-and-apis"></a>Nuovo watchOS 3 funzioni e API

Apple ha aggiunto diverse nuove funzionalità e le API a WatchKit per consentono allo sviluppatore di aggiungere le interazioni rapide alle proprie App Apple Watch:

- watchOS 3 consente di accedere a nuovi tipi di input, ad esempio utente:
    - Riconoscitori di movimento
    - Rotazione Crown digitale 
- watchOS 3 offre nuove modalità di visualizzazione e l'aggiornamento delle informazioni, ad esempio:
    - Navigazione migliorata di tabella
    - Nuovo supporto per framework notifica utente
    - Integrazione SpriteKit e SceneKit

Implementando queste nuove funzionalità, lo sviluppatore può verificare che l'app watchOS 3 sia Glanceable, realizzabili e reattivo.

### <a name="gesture-recognizer-support"></a>Supporto di riconoscitore di movimento

Se lo sviluppatore ha implementato i riconoscitori di movimento in iOS, è consigliabile notevole familiarità con modalità di funzionamento i riconoscitori di movimento in watchOS 3. Per aggiornare, i riconoscitori di movimento sono oggetti che analizza gli eventi di tocco di basso livello in movimenti predefiniti, riconoscibili.

watchOS 3 supporterà i riconoscitori di movimento seguenti quattro:

- Tipi di movimenti discreti:
    - Il gesto di scorrimento rapido (`WKSwipeGestureRecognizer`).
    - Il gesto tocco (`WKTapGestureRecognizer`).
- Tipi di movimento continua:
    - Il movimento di zoom (`WKPanGestureRecognizer`).
    - Il movimento di pressione prolungata (`WKLongPressGestureRecognizer`).

Per implementare uno dei riconoscitori di movimento nuovo, è sufficiente trascinarlo nell'area di progettazione in iOS Designer in Visual Studio per Mac e configurarne le proprietà.

Nel codice, risposta all'azione del riconoscimento per gestire il movimento viene attivato dall'utente. Anche in questo caso, questa operazione viene eseguita esattamente come verrebbe essere gestita in iOS.

#### <a name="discrete-gesture-states"></a>Stati di movimento discreti

Per i movimenti discreti, l'azione viene chiamato quando viene riconosciuto il movimento e uno stato (`WKGestureRecognizerState`) viene assegnato come:

[![](quick-interaction-techniques-images/quick01.png "Stati di movimento discreti")](quick-interaction-techniques-images/quick01.png#lightbox)

Tutti i movimenti discreti come inizio la `Possible` lo stato e transizione a uno il `Failed` o `Recognized` dello stato. Quando si usano i movimenti discreti, lo sviluppatore in genere non riguarda direttamente lo stato. Al contrario, si basano sull'azione che è chiamato quando il movimento viene riconosciuto solo.

#### <a name="continuous-gesture-states"></a>Stati di movimento continua

I movimenti continuati sono leggermente diversi da movimenti discreta, in cui l'azione viene chiamata più volte come il movimento è in corso il riconoscimento:

[![](quick-interaction-techniques-images/quick02.png "Stati di movimento continua")](quick-interaction-techniques-images/quick02.png#lightbox)

Anche in questo caso, i movimenti continua viene avviata nel `Possible` dello stato, ma l'avanzamento il numero degli aggiornamenti. In questo caso lo sviluppatore dovrà prendere in considerazione lo stato del motore di riconoscimento e aggiornare l'interfaccia utente dell'app durante la `Changed` fase fino a quando il movimento è finalmente `Recognized` o `Canceled`.

#### <a name="gesture-recognizer-usage-tips"></a>Suggerimenti sull'utilizzo riconoscitore di movimento

Quando si lavora con i riconoscitori di movimento in watchOS 3, Apple consiglia quanto segue:

- Aggiungere i riconoscitori di movimento per raggruppare gli elementi invece di singoli controlli. Poiché l'Apple Watch ha dimensioni dello schermo fisico inferiori, raggruppare gli elementi tendono a essere più grande e più facile destinazioni per l'utente prema. Inoltre, i riconoscitori di movimento possano determinare conflitti con incorporato movimenti già nei controlli dell'interfaccia utente nativa.
- Impostare le relazioni di dipendenza nello Storyboard dell'app per le espressioni di controllo.
- Alcuni movimenti hanno la precedenza rispetto ad altri tipi di movimento, ad esempio:
    - Lo scorrimento
    - Forzare il tocco
 
### <a name="digital-crown-rotation"></a>Rotazione Crown digitale

Mediante l'implementazione di supporto digitale Crown nelle proprie App watchOS 3, uno sviluppatore può consentire la navigazione maggiore interazioni tra velocità e precisione per gli utenti.

Poiché watchOS 2, dell'app Apple Watch è possibile usare la `WKInterfacePicker` oggetto a cui accedere il Crown digitali, fornendo un elenco di `WKPickerItems` e uno stile di selezione (elenco, in pila o sequenza di immagini). quindi, watchOS consentito all'utente di usare il Crown digitali per selezionare un elemento dall'elenco.

Quando si usa un `WKInterfacePicker`, gestisce la maggior parte del lavoro da WatchKit:

- Nell'elenco e gli elementi dell'interfaccia singole di disegno.
- Elaborazione degli eventi Crown digitale.
- La chiamata a un'azione quando viene selezionato un elemento.

Nuovo a watchOS 3, lo sviluppatore ha ora accesso diretto agli eventi di rotazione Crown digitale che consente di creare i propri elementi dell'interfaccia utente che rispondono a valori di rotazione.

Viene fornito accesso digitale in Crown dagli elementi seguenti:

- `WKCrownSequencer` -Fornisce l'accesso per le rotazioni al secondo.
- `WKCrownDelegate` -Fornisce l'accesso a eventi delta rotazionali.

#### <a name="rotations-per-second"></a>Rotazioni al secondo

L'accesso a rotazioni al secondo dal Crown digitale risulta utile quando lavoro con fisica base animazioni. Per accedere le rotazioni al secondo, usare il `CrownSequencer` proprietà del `WKInterfaceController` dell'estensione di espressioni di controllo. Ad esempio:

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>Delta di rotazionali

Usare i delta dal Crown digitale rotazionale per contare il numero di rotazioni. Usare la `CrownDidRotate` eseguire l'override del metodo di `WKCrownDelegate` accedere i delta rotazionali. Ad esempio:

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

In questo caso l'app mantiene un accumulatore (`AccumulatedRotations`) per determinare il numero delle rotazioni. È uguale a un delta cumulativo di una rotazione completa del Crown Digital `1.0` e una rotazione metà sarebbe `0.5`.

Apple ha lasciato compito dello sviluppatore per determinare la corrispondano tra i conteggi di rotazione al livello di riservatezza delle modifiche per l'elemento dell'interfaccia utente in fase di aggiornamento.

Il segno (`+/-`) dal valore differenziale rotazionale indica la direzione che l'utente sta diventando la Crown digitale:

[![](quick-interaction-techniques-images/quick03.png "Il segno del Delta rotazionale indica la direzione che l'utente sta diventando la Crown digitale")](quick-interaction-techniques-images/quick03.png#lightbox)


Se l'utente viene effettuato uno scorrimento backup, WatchKit restituirà i delta positivi e se scorrendo verso il basso, quindi i delta negativi verranno restituiti, indipendentemente da quali orientamento l'utente è che le espressioni di controllo in.

#### <a name="digital-crown-focus"></a>Stato attivo Crown digitale

Proprio come qualsiasi altro elemento di interfaccia, il Crown digitale è presente il concetto di interesse. Questo stato attivo può essere spostato dal Crown digitali per altri elementi dell'interfaccia basate sul modo in cui l'utente interagisce con l'orologio. 

Uno dei seguenti controlli, ad esempio, potrebbe rubare l'obiettivo del Crown digitale:

- Selezione
- Slider
- Lo scorrimento Controller

È responsabilità dello sviluppatore determinare quando deve essere l'obiettivo del Crown digitale rispettivo elemento di interfaccia personalizzata. Suggerite da Apple con il nuovo riconoscitori di movimento per ottenere lo stato attivo nell'elemento dell'interfaccia utente personalizzata.

### <a name="vertical-paging"></a>Spostamento verticale

Il metodo standard che un utente si sposta una visualizzazione tabella in un'app watchOS consiste per scorrere la porzione di dati desiderata, toccare su una riga specifica da visualizzare nella visualizzazione dettagliata, toccare il pulsante indietro quando terminato di visualizzare i dettagli e ripetere il processo per qualsiasi altra informazione che la y sono interessati all'interno della tabella:

[![](quick-interaction-techniques-images/quick04.png "Lo spostamento tra una tabella e la visualizzazione dettagli")](quick-interaction-techniques-images/quick04.png#lightbox)

Nuovo a watchOS 3, lo sviluppatore può attivare il Paging verticali i controlli di visualizzazione tabella. Con questa funzionalità è abilitata, l'utente può scorrere per trovare una riga della visualizzazione tabella e toccare la riga per visualizzarne i dettagli come prima. Tuttavia, si può ora far scorrere backup per selezionare la riga successiva nella tabella o il basso e selezionare la riga precedente (oppure utilizzare il Crown digitale), tutto senza dover tornare alla visualizzazione tabella prima di tutto:

[![](quick-interaction-techniques-images/quick05.png "Lo spostamento tra una tabella e la visualizzazione di dettaglio e scorrendo rapidamente su e giù per spostarsi tra le altre righe")](quick-interaction-techniques-images/quick05.png#lightbox)

Per abilitare questa modalità, aprire Storyboard dell'app watchOS in Xcode per la modifica, selezionare la visualizzazione di tabella e verificare i **Paging dettaglio verticale** casella di controllo:

[![](quick-interaction-techniques-images/quick06.png "Selezionare la casella di controllo Paging dettaglio verticale")](quick-interaction-techniques-images/quick06.png#lightbox)

Assicurarsi che la tabella usi segue per visualizzare la visualizzazione dettagliata e salvare le modifiche apportate allo Storyboard e tornare a Visual Studio per Mac per la sincronizzazione.

Lo sviluppatore è possibile coinvolgere a livello di programmazione Paging verticale a una riga specifica usando il codice seguente su una vista di tabella:

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

Quando si usa il Paging verticale, lo sviluppatore deve essere a conoscenza che WatchKit gestirà automaticamente il precaricamento dei controller e di conseguenza, alcuni metodi del ciclo di vita del controller possono essere chiamati prima che l'interfaccia utente è visibile o meno.

### <a name="notification-enhancements"></a>Miglioramenti di notifica

Notifica sono la forma principale di rapida interazione in cui un utente si verifica in genere in watchOS e sono state disponibile dopo il primo di Apple Watch e watchOS 1.

Un tipico interazioni rapide di notifica è il seguente:

1. L'utente ritiene Aptico la notifica quando viene ricevuta una nuova notifica.
2. La generazione del loro polso per visualizzare l'interfaccia aspetto breve per la notifica.
3. Se continuano a mantenere il polso generato, watchOS diventa automaticamente l'interfaccia di notifica di ricerca lunghi.

Esistono vari modi in cui un utente può rispondere alla notifica:

- Per un ben definite e visualizzata la notifica, l'utente non eseguono alcuna operazione e chiudere semplicemente la notifica.
- È anche possibile toccare della notifica per avviare l'app watchOS.
- Per una notifica che supporta le azioni personalizzate, l'utente può selezionare una delle azioni personalizzate. Possono essere:
    - **Le azioni in primo piano** -questi avviare l'app per eseguire l'azione.
    - **Le azioni in background** : sono state sempre indirizzate allo iPhone in watchOS 2 ma possono essere indirizzate a watchApp in watchOS 3.

Novità di watchOS 3:

* Notifica Usa un'API simile in tutte le piattaforme (iOS, watchOS, tvOS e macOS).
* Notifica locale può essere pianificata nel Apple Watch.
* Notifica in background verrà indirizzata all'estensione dell'app se sono state pianificate nel Apple Watch.

#### <a name="notification-scheduling-and-delivery"></a>Notifica pianificazione e recapito

Notifica da iPhone dell'utente, è necessario inoltrare l'Apple Watch quando si verifica quanto segue:

* Schermata dell'iPhone è disattivata.
* L'Apple Watch è indossato ed è stata sbloccata.

In watchOS 3, le notifiche locali possono essere pianificate nel Apple Watch e vengono fornite solo le espressioni di controllo. Spetta allo sviluppatore di pianificare un iPhone corrispondente notifica se richiesto dall'app.

Includendo lo stesso identificatore di notifica sul Apple Watch e iPhone versioni delle notifiche, che impedisce le notifiche duplicate venga visualizzata nuovamente nelle espressioni di controllo. La versione di Apple Watch della notifica avrà la precedenza rispetto alla versione di iPhone.

Poiché watchOS 3 Usa lo stesso `UINotification` framework di API come 10, iOS, vedere di iOS 10 [Framework notifica utente](~/ios/platform/user-notifications/index.md) informazioni più dettagliate.

### <a name="using-spritekit-and-scenekit"></a>Con SpriteKit e SceneKit

Nuovo a watchOS 3, lo sviluppatore può ora utilizzare oggetti SpritKit sia SceneKit nella progettazione dell'interfaccia utente dell'app relativi presentare grafica sia 2D e 3D.

Sono state aggiunte due nuove classi di interfaccia per supportare questa funzionalità:

- `WKInterfaceSKScene` -Per l'utilizzo di grafica 2D SpriteKit.
- `WKInterfaceSCNScene` -Per l'utilizzo di grafica 3D di SceneKit.

Per usare questi oggetti, è sufficiente trascinarli nell'area di progettazione all'interno Storyboard dell'app per le espressioni di controllo di Interface Builder di Xcode e usare la **Attributes Inspector** per configurarli.

A questo punto, funzionante con SpriteKit o SceneKit scene funziona esattamente come avviene all'interno di un'app per iOS. L'app watch presenterà una `WKInterfaceSKScene` chiamando uno del `Present` metodi. Per SceneKit, è sufficiente impostare il `Scene` proprietà del `WKInterfaceSCNScene` oggetto.

## <a name="actionable-complications"></a>Problemi di utilità pratiche

In watchOS 2, Apple ha introdotto complicazioni per le app di terze parti 3rd. In watchOS 3, Apple ha esteso le funzionalità che uno sviluppatore può includere in una complicazione WatchKit. 

Inoltre, più predefinite nelle espressioni di controllo facce possono ora includere complicazioni e i volti delle espressioni di controllo esistente che già supportate complicazioni ora includere anche altre complicazioni.

Anche nuovi è costituito dalla possibilità per un utente di scorrere rapidamente verso sinistra o destra per passare tutti i volti di espressioni di controllo che vengono installate nel loro Apple Watch. Usando la nuova raccolta nell'app iPhone per complementare dell'Apple Watch, l'utente può aggiungere e personalizzare nuove icone di espressioni di controllo e in tutte le complicazioni che possono includere.

Grazie a queste nuove funzionalità, suggerite da Apple che ogni app in Apple Watch anche deve includere almeno una complicazione e di conseguenza, tutte le app Native di Apple Watch disporrà a complicazioni.

Complicazioni forniscono le funzionalità seguenti a un'app:

- Sono altamente glanceable perché sono sempre presenti nel quadrante dell'orologio.
- Complicazioni vengono aggiornati di frequente per watchOS. Qualsiasi app che include una complicazione nel quadrante di orologio attualmente visualizzato dell'utente viene aggiornato almeno due volte un'ora.
- Qualsiasi app con una complicazione nel quadrante di orologio attualmente visualizzato dell'utente viene mantenuta in memoria che consente di avviare rapidamente l'app e migliora la velocità di risposta dall'app.
- Complicazioni semplificano l'utente può avviare le funzionalità specifiche in un'app watchOS.

## <a name="glanceable-notification"></a>Notifica glanceable

Notifica per Apple Watch consentono di eccezionali e personalizzabile rapidamente informare l'utente di nuove informazioni quali i messaggi in ingresso o raggiungere un obiettivo in un'app per gli allenamenti o eventi.

Usando una notifica, preziose informazioni possono essere rapidamente presentate all'utente. In molte situazioni, una notifica ben progettata può rimuovere la necessità per l'utente avviare effettivamente l'app.

Nuovo a watchOS 3, tutte le notifiche ora supportano:

- SpriteKit
- SceneKit
- inline Video

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>Interfaccia utente avanzata con SpriteKit e SceneKit

In genere, uno sviluppatore potrebbe pensare di gioco dell'interfaccia utente quando vengono citati SpriteKit e SceneKit. Tuttavia, può essere utile per la creazione di interfacce utente che includono i layout personalizzati, contenuti e le animazioni che non sono possibili in WatchKit solo in caso contrario, non di gioco SpriteKit sia SceneKit.

Ad esempio, una notifica all'utente da un'app di condivisione foto può usare SpriteKit per offrire un'esperienza utente avanzata, includendo l'utente che ha pubblicato il quadro insieme alle altre informazioni personalizzate che arricchisce l'esperienza utente e l'immagine effettiva.

Inoltre sia SpriteKit SceneKit può essere combinato con gli elementi UI WatchKit standard nella progettazione dell'interfaccia utente dell'app.

## <a name="simple-navigation"></a>Spostamento semplice

watchOS 3 presenta vari modi che uno sviluppatore può semplificare la navigazione all'interno delle App watchOS, ad esempio la nuova [Paging verticale](#Vertical-Paging), [supporto riconoscitore di movimento](#Gesture-Recognizer-Support) e [Crown digitale Rotazione](#Digital-Crown-Rotation) funzionalità illustrate in precedenza.

Il Crown digitale è univoca per l'Apple Watch e può essere usata in diversi modi per semplificare la navigazione. Ad esempio, un'applicazione di timer può utilizzare il Crown digitali per eseguire lo scrubbing attraverso le lunghezze di timer disponibili.

Movimenti personalizzati possono presentare modi nuovi e univoci per l'utente può interagire con un'app e consente inoltre di semplificare la navigazione delle app.

Apple consiglia alla ricerca di modi combinare tutte le nuove funzionalità di interazione rapida aggiunte in watchOS 3 per presentare avanzato, facilmente e rapidamente a usare le interfacce app watchOS.

## <a name="finishing-the-quick-interaction"></a>Terminare l'interazione rapida

Un'esperienza di interazioni rapide ben progettato fornirà all'utente la fiducia necessaria per eliminare il polso (e disattivare con l'app) quando vengono completati l'interazione corrente.

Dove in particolare, questo diventa un problema è quando l'app watch viene esegue qualsiasi tipo di connessione di rete o condivisione delle informazioni con l'app iPhone complementare. Questo spesso può causare un indicatore di attesa mentre la transazione è in corso, che non è opportuno durante un'interazione rapida. Vedere l'esempio seguente:

[![](quick-interaction-techniques-images/quick07.png "Diagramma dell'app per le espressioni di controllo eseguire una connessione di rete e condivisione delle informazioni con l'app iPhone companion")](quick-interaction-techniques-images/quick07.png#lightbox)

1. L'utente sceglie un elemento per l'acquisto nelle espressioni di controllo.
2. Si tocca il pulsante di acquisto.
3. L'app viene avviata la transazione di rete e viene visualizzato un indicatore di caricamento.
4. Successivamente, al completamento della transazione e l'app Visualizza una conferma di acquisto.
5. L'utente rilascia il polso e consente di scollegare con l'app.

Dal momento in cui che l'utente tocca il pulsante di acquisto fino a quando la transazione viene completata, hanno il polso generato esaminando un indicatore di caricamento. Per risolvere questa situazione, suggerite da Apple che lo sviluppatore deve presentare un feedback immediato all'utente anziché mostrare un indicatore di caricamento. 

Usa modello consigliato di Apple, dare un'occhiata anche in questo caso l'interazione rapida stesso:

[![](quick-interaction-techniques-images/quick08.png "Diagramma del modello consigliato di mele")](quick-interaction-techniques-images/quick08.png#lightbox)

1. L'utente sceglie un elemento per l'acquisto nelle espressioni di controllo.
2. Si tocca il pulsante di acquisto.
3. L'app viene avviata la transazione di rete e viene visualizzato un messaggio che informa che l'acquisto è stato avviato correttamente.
4. L'utente rilascia il polso e consente di scollegare con l'app.
5. Quando la transazione viene completata correttamente alcuni minuti in un secondo momento, l'app Visualizza una notifica locale per informare l'utente di un acquisto ha esito positivo.

Questa volta, non appena l'utente tocca il pulsante di acquisto che vengono visualizzati un messaggio che informa che l'acquisto è stato avviato, in modo possono rilasciare loro polso e terminano a questo punto l'interazione rapida in tutta sicurezza. In un secondo momento vengono informati dell'esito positivo o negativo della transazione in una notifica all'utente. In questo modo, l'utente sta interagendo solo con l'app durante le fasi del processo "attive".

Per le app sono le reti, è possibile usare uno sfondo `NSURLSession` per gestire la comunicazione di rete con un'attività di download. In questo modo l'app può essere riattivato in background per elaborare le informazioni scaricate. Per l'app che richiedono l'elaborazione in background, usare un'asserzione di attività in Background per gestire l'elaborazione necessaria.

## <a name="quick-interaction-design-tips"></a>Suggerimenti sulla progettazione di interazioni rapide

Poiché la lunghezza desiderata di un'interazione rapida è di due secondi o meno, lo sviluppatore deve concentrarsi sulla progettazione di interazioni dell'app sin dall'inizio del processo di progettazione. Individuare le aree in cui tali interazioni possono essere semplificati (tramite la tecnica descritta in precedenza) e usano le nuove funzionalità di watchOS 3 per rendere l'app, veloce e reattivo.

Le seguenti suggerite da Apple:

- Concentrati sulle interazioni rapide unendo le funzionalità dell'app usate più avanti.
- Usare complicazioni e le notifiche utente per le funzionalità e funzioni comuni della superficie di attacco.
- Creare l'interfaccia utente avanzata, glanceable con SceneKit e SpriteKit.
- Quando possibile, semplificare la navigazione all'interno dell'app.
- Non apportare mai l'utente di attesa, consentire loro di eliminare il polso e disattivare con l'app appena possibile.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato le tecniche per interazioni rapide Apple ha aggiunto in watchOS 3 e come implementarli in xamarin. IOS per Apple Watch.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di watchOS](https://developer.xamarin.com/samples/watchos/all/)
