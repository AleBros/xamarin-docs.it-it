---
title: Tecniche di interazione rapida per watchos 3 in Novell
description: Questo articolo illustra le tecniche di interazione rapida che Apple ha aggiunto in watchos 3 e come implementarle in Novell. iOS per Apple Watch.
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: a0719dce3b7f5484168dc5679237b01286f5de78
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286751"
---
# <a name="quick-interaction-techniques-for-watchos-3-in-xamarin"></a>Tecniche di interazione rapida per watchos 3 in Novell

_Questo articolo illustra le tecniche di interazione rapida che Apple ha aggiunto in watchos 3 e come implementarle in Novell. iOS per Apple Watch._

Fornire interazioni utente rapide è essenziale per la creazione di applicazioni e complicazioni di Apple Watch accattivanti. Una novità di watchos 3, Apple ha aggiunto il supporto per i riconoscitori di movimento, l'accesso alla Digital Crown e le nuove tecniche di notifica e navigazione degli utenti. Questo, insieme al supporto aggiunto sia per SceneKit che per SpriteKit, consente allo sviluppatore di creare con facilità interfacce accattivanti e accattivanti, rapide e reattive.

## <a name="what-are-quick-interactions"></a>Che cosa sono le interazioni rapide

Per uno sviluppatore usato per la creazione di applicazioni per iOS o macOS (in cui la quantità di tempo impiegato da un utente per l'interazione con l'app viene misurata in minuti o ore), la progettazione di un'app corretta per la Apple Watch può essere una sfida e richiede un'altra approccio.

In watchos, l'utente in genere vuole alzare di livello il polso, interagire rapidamente con un'app (in genere per un breve paio di secondi), quindi rilasciare il polso e continuare a usare qualsiasi operazione.

Di seguito sono riportati alcuni esempi di interazioni rapide tipiche sul Apple Watch:

- Avvio di un timer.
- Verifica del tempo.
- Contrassegno di un elemento da un elenco todo.

Per raggiungere questi obiettivi, un'applicazione nel Apple Watch deve essere:

- Con un' **occhiata** , il che significa che, con un rapido riepilogo, l'utente deve essere in grado di ottenere le informazioni necessarie. 
- **Azionabile** , che significa che gli utenti devono essere in grado di prendere decisioni rapide e informate.
- **Reattivo** : indica che l'utente non deve mai attendere di ricevere le informazioni necessarie o per ottenere l'azione desiderata.

### <a name="quick-interactions-length"></a>Lunghezza interazioni rapide

A causa della natura di Apple Watch app, Apple suggerisce che la lunghezza ideale di un'interazione rapida deve essere di due secondi o meno. Come risultato di questo limite di due secondi, lo sviluppatore dovrà dedicare una notevole quantità di tempo alla progettazione e all'implementazione di un'app Apple Watch. 

## <a name="new-watchos-3-features-and-apis"></a>Nuove funzionalità e API di watchos 3

Apple ha aggiunto diverse nuove funzionalità e API a WatchKit per aiutare gli sviluppatori ad aggiungere interazioni rapide alle app Apple Watch:

- watchos 3 consente di accedere ai nuovi tipi di input utente, ad esempio:
  - Riconoscitori di movimento
  - Rotazione Digital Crown 
- watchos 3 offre nuove modalità di visualizzazione e aggiornamento delle informazioni, ad esempio:
  - Esplorazione avanzata delle tabelle
  - Nuovo supporto per il Framework notifiche utente
  - Integrazione di SpriteKit e SceneKit

Grazie all'implementazione di queste nuove funzionalità, lo sviluppatore può garantire che l'app watchos 3 sia rapida, interoperabile e reattiva.

### <a name="gesture-recognizer-support"></a>Supporto del riconoscimento di movimento

Se lo sviluppatore ha implementato i riconoscitori di movimento in iOS, dovrebbe avere una certa familiarità con il funzionamento dei riconoscitori di movimento in watchos 3. Per eseguire l'aggiornamento, i riconoscitori dei movimenti sono oggetti che analizzano eventi di tocco di basso livello in movimenti riconoscibili e predefiniti.

watchos 3 supporterà i quattro riconoscitori di movimento seguenti:

- Tipi di movimenti discreti:
  - Gesto di scorrimento (`WKSwipeGestureRecognizer`).
  - Movimento TAP (`WKTapGestureRecognizer`).
- Tipi di movimento continuo:
  - Movimento della panoramica (`WKPanGestureRecognizer`).
  - Gesto con pressione prolungata`WKLongPressGestureRecognizer`().

Per implementare uno dei nuovi riconoscitori di movimento, è sufficiente trascinarlo su un'area di progettazione in iOS designer in Visual Studio per Mac e configurarne le proprietà.

Nel codice rispondere all'azione del sistema di riconoscimento per gestire il movimento attivato dall'utente. Anche in questo caso, questa operazione viene eseguita nello stesso modo in cui verrebbe gestita in iOS.

#### <a name="discrete-gesture-states"></a>Stati di movimento discreti

Per i movimenti discreti, l'azione viene chiamata quando il movimento viene riconosciuto e uno stato`WKGestureRecognizerState`() viene assegnato come:

[![](quick-interaction-techniques-images/quick01.png "Stati di movimento discreti")](quick-interaction-techniques-images/quick01.png#lightbox)

Tutti i movimenti discreti iniziano nello `Possible` stato e la transizione `Failed` allo stato o `Recognized` . Quando si usano movimenti discreti, lo sviluppatore in genere non si occupa direttamente dello stato. Si basano invece sull'azione chiamata quando il movimento viene riconosciuto solo.

#### <a name="continuous-gesture-states"></a>Stati di movimento continuo

I movimenti continui sono leggermente diversi dai movimenti discreti, in cui l'azione viene chiamata più volte durante il riconoscimento del movimento:

[![](quick-interaction-techniques-images/quick02.png "Stati di movimento continuo")](quick-interaction-techniques-images/quick02.png#lightbox)

Anche in questo caso, i `Possible` movimenti continui iniziano nello stato, ma avanzano su più aggiornamenti. Lo sviluppatore dovrà prendere in considerazione lo stato del sistema di riconoscimento e aggiornare l'interfaccia utente dell'app `Changed` durante la fase fino a quando `Recognized` il `Canceled`movimento non è finale o.

#### <a name="gesture-recognizer-usage-tips"></a>Suggerimenti sull'utilizzo del riconoscitore di movimento

Quando si utilizzano i riconoscitori di movimento in watchos 3, Apple suggerisce quanto segue:

- Aggiungere i riconoscitori di movimento agli elementi del gruppo anziché ai singoli controlli. Poiché il Apple Watch ha dimensioni di schermo fisico inferiori, gli elementi del gruppo tendono a essere più grandi e più semplici destinazioni che l'utente può raggiungere. Inoltre, i riconoscitori dei movimenti possono entrare in conflitto con i movimenti predefiniti già presenti nei controlli dell'interfaccia utente nativi.
- Impostare le relazioni di dipendenza nello storyboard dell'app Watch.
- Alcuni movimenti hanno la precedenza su altri tipi di movimento, ad esempio:
  - Scorrimento
  - Force Touch
 
### <a name="digital-crown-rotation"></a>Rotazione Digital Crown

Implementando il supporto Digital Crown nelle app watchos 3, uno sviluppatore può garantire una maggiore velocità di navigazione e interazioni di precisione per gli utenti.

Dal momento che watchos 2, Apple Watch app può usare `WKInterfacePicker` l'oggetto per accedere al Digital Crown fornendo un elenco di `WKPickerItems` e uno stile di selezione (elenco, sequenza di immagini o in pila). watchos ha quindi consentito all'utente di usare la Digital Crown per selezionare un elemento dall'elenco.

Quando si usa `WKInterfacePicker`un, WatchKit gestisce la maggior parte delle operazioni:

- Disegno dell'elenco e dei singoli elementi dell'interfaccia.
- Elaborazione degli eventi Digital Crown.
- Chiamata di un'azione quando viene selezionato un elemento.

Una novità di watchos 3, lo sviluppatore ora ha accesso diretto agli eventi di rotazione Digital Crown che consentono loro di creare elementi dell'interfaccia utente personalizzati che rispondono ai valori di rotazione.

Digital Crown l'accesso viene fornito dagli elementi seguenti:

- `WKCrownSequencer`: Fornisce l'accesso alle rotazioni al secondo.
- `WKCrownDelegate`: Fornisce l'accesso agli eventi Delta rotazionali.

#### <a name="rotations-per-second"></a>Rotazioni al secondo

L'accesso alle rotazioni al secondo dall'Digital Crown è utile quando si lavora con animazioni basate sulla fisica. Per accedere alle rotazioni al secondo, utilizzare la `CrownSequencer` proprietà dell'oggetto `WKInterfaceController` dell'estensione Watch. Ad esempio:

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>Delta rotazionali

Usare i Delta rotazionali dalla Digital Crown per contare il numero di rotazioni. Usare il `CrownDidRotate` metodo `WKCrownDelegate` di override di per accedere ai Delta rotazionali. Ad esempio:

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

Qui l'app gestisce un accumulatore`AccumulatedRotations`() per determinare il numero di rotazioni. Una rotazione completa del Digital Crown è uguale a una Delta accumulata di `1.0` e una metà della rotazione `0.5`sarebbe.

Apple ha lasciato lo sviluppatore per determinare il modo in cui i conteggi della rotazione corrispondono alla sensibilità delle modifiche nell'elemento dell'interfaccia utente da aggiornare.

Il segno (`+/-`) del Delta rotazionale indica la direzione in cui l'utente sta trasformando la Digital Crown:

[![](quick-interaction-techniques-images/quick03.png "Il segno del Delta rotazionale indica la direzione in cui l'utente sta trasformando il Digital Crown")](quick-interaction-techniques-images/quick03.png#lightbox)


Se l'utente sta scorrendo verso l'alto, WatchKit restituirà Delta positivi e, in caso di scorrimento verso il basso, verranno restituiti Delta negativi, a prescindere dall'orientamento che l'utente sta indossando.

#### <a name="digital-crown-focus"></a>Digital Crown lo stato attivo

Analogamente a qualsiasi altro elemento dell'interfaccia, il Digital Crown ha il concetto di messa a fuoco. Questo stato attivo può essere spostato dalla Digital Crown ad altri elementi dell'interfaccia in base alla modalità di interazione dell'utente con l'espressione di controllo. 

Ad esempio, uno dei controlli seguenti potrebbe sottrarre l'attenzione del Digital Crown:

- Selezione
- Slider
- Controller di scorrimento

Spetta allo sviluppatore determinare quando l'elemento dell'interfaccia personalizzata deve essere l'obiettivo del Digital Crown. Apple suggerisce di usare i nuovi riconoscitori dei movimenti per ottenere lo stato attivo nell'elemento personalizzato dell'interfaccia utente.

### <a name="vertical-paging"></a>Paging verticale

Il modo standard in cui un utente accede a una visualizzazione tabella in un'app watchos consiste nello scorrere fino al pezzo di dati desiderato, toccare una riga specifica per visualizzare la visualizzazione dettagliata, toccare il pulsante indietro al termine della visualizzazione dei dettagli e ripetere il processo per tutte le altre informazioni che il y è interessato all'interno della tabella:

[![](quick-interaction-techniques-images/quick04.png "Passaggio tra una tabella e la visualizzazione dettagli")](quick-interaction-techniques-images/quick04.png#lightbox)

Nuovo con watchos 3, lo sviluppatore può abilitare il paging verticale nei controlli di visualizzazione tabella. Se questa funzionalità è abilitata, l'utente può scorrere per trovare una riga di visualizzazione tabella e toccare la riga per visualizzarne i dettagli come prima. Tuttavia, ora è possibile scorrere verso l'alto per selezionare la riga successiva nella tabella o verso il basso per selezionare la riga precedente (oppure utilizzare il Digital Crown), il tutto senza dover tornare prima alla visualizzazione tabella:

[![](quick-interaction-techniques-images/quick05.png "Spostamento tra una tabella e la visualizzazione dettagli e scorrimento verso l'alto e verso il basso per spostarsi tra le altre righe")](quick-interaction-techniques-images/quick05.png#lightbox)

Per abilitare questa modalità, aprire lo storyboard dell'app watchos in Xcode per la modifica, selezionare la visualizzazione tabella e selezionare la casella di controllo **Vertical detail paging** :

[![](quick-interaction-techniques-images/quick06.png "Casella di controllo per il paging dei dettagli verticali")](quick-interaction-techniques-images/quick06.png#lightbox)

Assicurarsi che la tabella utilizzi gli elementi segue per visualizzare la visualizzazione dettagliata e salvare le modifiche apportate allo storyboard e tornare a Visual Studio per Mac per la sincronizzazione.

Lo sviluppatore può coinvolgere a livello di codice il paging verticale in una riga specifica utilizzando il codice seguente in una vista tabella:

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

Quando si usa il paging verticale, lo sviluppatore deve tenere presente che WatchKit gestirà automaticamente il precaricamento dei controller e, di conseguenza, alcuni metodi del ciclo di vita del controller possono essere chiamati prima che l'interfaccia utente sia effettivamente visibile.

### <a name="notification-enhancements"></a>Miglioramenti delle notifiche

La notifica è la forma principale di interazione rapida che un utente ha in genere sperimentato in watchos e che è stata disponibile dal primo Apple Watch e watchos 1.

Di seguito è riportata una tipica interazione rapida di notifica:

1. Quando viene ricevuta una nuova notifica, l'utente percepisce l'ansa di notifica.
2. Si sollevano i polsi per vedere l'interfaccia breve per la notifica.
3. Se continuano a tenere alzati i polsi, watchos passa automaticamente all'interfaccia di notifica Long look.

È possibile che un utente risponda alla notifica in diversi modi:

- Per una notifica ben definita e presentata, l'utente non eseguirà alcuna operazione e chiuderà semplicemente la notifica.
- Potrebbero anche toccare la notifica per avviare l'app watchos.
- Per una notifica che supporta azioni personalizzate, l'utente potrebbe selezionare una delle azioni personalizzate. I possibili valori sono i seguenti:
  - **Azioni in primo piano** : avviano l'app per eseguire l'azione.
  - **Azioni in background** : sono sempre state indirizzate all'iPhone in watchos 2 ma possono essere indirizzate a watchApp in watchos 3.

Novità per watchos 3:

- La notifica usa un'API simile in tutte le piattaforme (iOS, watchos, tvOS e macOS).
- La notifica locale può essere pianificata nel Apple Watch.
- La notifica in background verrà instradata all'estensione dell'app se è stata pianificata nel Apple Watch.

#### <a name="notification-scheduling-and-delivery"></a>Pianificazione e recapito delle notifiche

La notifica dall'iPhone dell'utente sarà successiva alla Apple Watch quando si verifica quanto segue:

- Lo schermo dell'iPhone è disattivato.
- Il Apple Watch viene utilizzato ed è stato sbloccato.

In watchos 3 le notifiche locali possono essere pianificate nel Apple Watch e vengono recapitate solo nell'orologio. Lo sviluppatore può pianificare una notifica iPhone corrispondente se è richiesta dall'app.

Includendo lo stesso identificatore di notifica per le versioni Apple Watch e iPhone delle notifiche, impedisce la visualizzazione delle notifiche duplicate nell'orologio. La versione Apple Watch della notifica avrà la precedenza sulla versione di iPhone.

Poiché watchos 3 usa lo stesso `UINotification` Framework API di iOS 10, vedere la documentazione del [Framework di notifica utente](~/ios/platform/user-notifications/index.md) iOS 10 per altri dettagli.

### <a name="using-spritekit-and-scenekit"></a>Uso di SpriteKit e SceneKit

Una novità di watchos 3, lo sviluppatore può ora usare gli oggetti SpritKit e SceneKit nella progettazione dell'interfaccia utente dell'app per presentare grafica 2D e 3D.

Per supportare questa funzionalità sono state aggiunte due nuove classi di interfaccia:

- `WKInterfaceSKScene`-Per lavorare con SpriteKit 2D graphics.
- `WKInterfaceSCNScene`-Per lavorare con SceneKit 3D graphics.

Per usare questi oggetti, è sufficiente trascinarli nell'area di progettazione all'interno dello storyboard dell'app Watch nel Interface Builder di Xcode e usare il **controllo attributi** per configurarli.

Da questo punto, l'uso di SpriteKit o SceneKit Scenes funziona nello stesso modo in cui si trova all'interno di un'app iOS. L'app Watch presenta una `WKInterfaceSKScene` chiamando uno `Present` dei metodi. Per SceneKit, è sufficiente impostare `Scene` la proprietà `WKInterfaceSCNScene` dell'oggetto.

## <a name="actionable-complications"></a>Complicazioni interoperabili

In watchos 2 Apple ha introdotto alcune complicazioni per le app di terze parti. In watchos 3, Apple ha ampliato le capacità che uno sviluppatore può includere in una complicazione WatchKit. 

Inoltre, la maggior parte delle espressioni di controllo predefinite ora può includere complicazioni e visi di controllo esistenti che possono ora includere ancora più complicazioni.

Inoltre, una novità è la possibilità per un utente di scorrere rapidamente verso sinistra o verso destra per passare attraverso tutti i visi che hanno installato sul proprio Apple Watch. Usando la nuova raccolta nell'app iPhone complementare Apple Watch, l'utente può aggiungere e personalizzare i nuovi visi e le eventuali complicazioni che possono includere.

A causa di queste nuove funzionalità, Apple suggerisce che ogni app in Apple Watch deve includere anche almeno una complicazione e, di conseguenza, tutte le app Apple Watch Native presentano ora delle complicazioni.

Le complicazioni forniscono le funzionalità seguenti a un'app:

- Sono estremamente esaltabili poiché sono sempre presenti sul quadrante dell'orologio.
- Le complicazioni vengono spesso aggiornate da watchos. Qualsiasi app che includa una complicazione sul quadrante dell'orologio attualmente visualizzato dell'utente viene aggiornata almeno due volte all'ora.
- Qualsiasi app con una complicazione sul quadrante dell'orologio attualmente visualizzato viene mantenuta in memoria, rendendo l'app avviata rapidamente e migliorando la velocità delle risposte dall'app.
- Le complicazioni consentono all'utente di avviare in modo semplice funzionalità specifiche in un'app watchos.

## <a name="glanceable-notification"></a>Notifica a colpo d'occhio

La notifica su Apple Watch offre un modo eccezionale e personalizzabile per informare rapidamente l'utente di eventi o nuove informazioni, ad esempio messaggi in arrivo o raggiungere un obiettivo in un'app di allenamento.

Utilizzando una notifica, le informazioni importanti possono essere presentate rapidamente all'utente. In molte situazioni, una notifica ben progettata può rimuovere la necessità per l'utente di avviare effettivamente l'app.

Novità di watchos 3, tutte le notifiche ora supportano:

- SpriteKit
- SceneKit
- Video inline

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>Interfaccia utente avanzata con SpriteKit e SceneKit

In genere, uno sviluppatore potrebbe pensare all'interfaccia utente del gioco quando vengono citati SpriteKit e SceneKit. Tuttavia, SpriteKit e SceneKit possono essere utili per la creazione di interfacce utente non di gioco che includono layout personalizzati, contenuto e animazioni che non sono altrimenti possibili in WatchKit.

Ad esempio, una notifica utente da un'app di condivisione foto può usare SpriteKit per offrire un'esperienza utente avanzata includendo l'utente che ha pubblicato l'immagine insieme a un'immagine effettiva e altre informazioni personalizzate che arricchiscono l'esperienza utente.

Inoltre, SpriteKit e SceneKit possono essere combinati con gli elementi dell'interfaccia utente di WatchKit standard nella progettazione dell'interfaccia utente dell'app.

## <a name="simple-navigation"></a>Navigazione semplice

watchos 3 presenta diversi modi in cui uno sviluppatore può semplificare la navigazione all'interno delle app watchos, ad esempio il nuovo [paging verticale](#vertical-paging), il [supporto del riconoscimento dei movimenti](#gesture-recognizer-support) e le funzionalità di [rotazione Digital Crown](#digital-crown-rotation) presentate sopra.

Il Digital Crown è univoco per l'Apple Watch e può essere usato in molti modi diversi per semplificare la navigazione. Ad esempio, un'applicazione timer può utilizzare il Digital Crown per pulire le lunghezze del timer disponibili.

I movimenti personalizzati possono presentare modi nuovi e univoci per l'interazione dell'utente con un'app Watch e possono essere usati anche per semplificare la navigazione delle app.

Apple suggerisce la ricerca di modi per combinare tutte le nuove funzionalità di interazione rapida aggiunte in watchos 3 per presentare interfacce di app watchos avanzate, semplici e veloci da usare.

## <a name="finishing-the-quick-interaction"></a>Completamento dell'interazione rapida

Un'esperienza di interazione rapida ben progettata conferisce all'utente la fiducia necessaria per eliminare il polso (e disattivarla con l'app) quando ha terminato l'interazione corrente.

Questo problema si verifica in modo specifico quando l'app Watch sta effettuando qualsiasi tipo di connessione di rete o condivide le informazioni con l'app iPhone complementare. Questo può spesso causare un indicatore di attesa mentre è in corso la transazione, che non è auspicabile durante un'interazione rapida. Vedere l'esempio seguente:

[![](quick-interaction-techniques-images/quick07.png "Diagramma dell'app Watch che esegue una connessione di rete e condivide le informazioni con la relativa app iPhone complementare")](quick-interaction-techniques-images/quick07.png#lightbox)

1. L'utente sceglie un elemento da acquistare nell'orologio.
2. Toccare il pulsante Acquista.
3. L'app avvia la transazione di rete e visualizza un indicatore di caricamento.
4. In un secondo momento, la transazione viene completata e l'app visualizza una conformità per l'acquisto.
5. L'utente rilascia il polso e si disinnesta con l'app.

Dal momento in cui l'utente tocca il pulsante Acquista finché la transazione non viene completata, i polsi hanno generato un indicatore di caricamento. Per risolvere questa situazione, Apple suggerisce che lo sviluppatore deve presentare un feedback istantaneo all'utente anziché visualizzare un indicatore di caricamento. 

Con il modello consigliato di Apple, esaminare di nuovo la stessa interazione rapida:

[![](quick-interaction-techniques-images/quick08.png "Diagramma del modello suggerito dalle mele")](quick-interaction-techniques-images/quick08.png#lightbox)

1. L'utente sceglie un elemento da acquistare nell'orologio.
2. Toccare il pulsante Acquista.
3. L'app avvia la transazione di rete e visualizza un messaggio che informa che l'acquisto è stato avviato correttamente.
4. L'utente rilascia il polso e si disinnesta con l'app.
5. Quando la transazione viene completata correttamente in un secondo momento, l'app visualizza una notifica locale per informare l'utente di un acquisto riuscito.

Questa volta, non appena l'utente tocca il pulsante Acquista, viene visualizzato un messaggio che informa che l'acquisto è stato avviato, in modo che possa tranquillamente eliminare il polso e terminare l'interazione rapida a questo punto. Successivamente, vengono informati dell'esito positivo o negativo della transazione in una notifica utente. In questo modo, l'utente interagisce solo con l'app durante le fasi "attive" del processo.

Per le app che eseguono la rete, possono usare uno sfondo `NSURLSession` per gestire la comunicazione di rete con un'attività di download. Ciò consentirà di riattivare l'app in background per elaborare le informazioni scaricate. Per un'app che richiede l'elaborazione in background, usare un'asserzione di attività in background per gestire l'elaborazione richiesta.

## <a name="quick-interaction-design-tips"></a>Suggerimenti per la progettazione dell'interazione rapida

Poiché la lunghezza desiderata di un'interazione rapida è di due secondi o meno, lo sviluppatore deve concentrarsi sulla progettazione delle interazioni dell'app fin dall'inizio del processo di progettazione. Individuare le aree in cui è possibile semplificare le interazioni (usando la tecnica descritta in precedenza) e usare le nuove funzionalità di watchos 3 per rendere l'app rapida e reattiva.

Apple suggerisce quanto segue:

- Concentrati sulle interazioni rapide con le funzionalità più usate dell'app in futuro.
- Usare le complicazioni e le notifiche utente per la superficie delle funzionalità e delle funzioni comuni.
- Crea un'interfaccia utente completa e accattivante con SceneKit e SpriteKit.
- Laddove possibile, semplificare la navigazione all'interno dell'app.
- Non fare mai in modo che l'utente attenda, lasciare il polso e disimpegnarsi con l'app il prima possibile.

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato le tecniche di interazione rapida che Apple ha aggiunto in watchos 3 e come implementarle in Novell. iOS per Apple Watch.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di watchOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
