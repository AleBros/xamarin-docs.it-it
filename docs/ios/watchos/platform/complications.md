---
title: "Complessità"
description: watchOS consente agli sviluppatori di scrivere complicazioni personalizzati per i caratteri tipografici espressioni di controllo
ms.topic: article
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/03/2017
ms.openlocfilehash: a13de7fbb4b6e1f9fa2853ce599f3a038a5e4040
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="complications"></a>Complessità

_watchOS consente agli sviluppatori di scrivere complicazioni personalizzati per i caratteri tipografici espressioni di controllo_

Questa pagina illustra i diversi tipi di problemi disponibili e come aggiungere una complicazione all'App watchOS 3.

Si noti che ogni applicazione watchOS può avere solo una complicazione.

Iniziare leggendo [documenti Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html) per determinare se l'app è adatto per una complicazione. Sono disponibili 5 `CLKComplicationFamily` tipi di visualizzazione per scegliere tra:

[ ![](complications-images/all-complications-sml.png "I tipi di CLKComplicationFamily 5 disponibili: circolare piccola, modulare Small, modulare, funzionali piccoli e grandi dimensioni funzionali grandi")](complications-images/all-complications.png)

App è possono implementare solo uno stile o tutti e cinque, a seconda dei dati visualizzati.
È inoltre possibile supportare spostamento cronologico, inserendo i valori specificati per ultime e/o futuri volte l'utente attiva il chioma digitale.

<a name="adding" />

## <a name="adding-a-complication"></a>Aggiunta di una complicazione

### <a name="configuration"></a>Configurazione

Complicazioni aggiunto a un'applicazione di espressioni di controllo durante la creazione, o aggiunto manualmente a una soluzione esistente.

### <a name="add-new-project"></a>Aggiungi nuovo progetto...

Il **Aggiungi nuovo progetto...**  guidata include una casella di controllo che creerà automaticamente una classe controller complicazione e configurare il **Info. plist** file:

![](complications-images/file-new-project-sml.png "La casella di controllo includono complicazione")

### <a name="existing-projects"></a>Progetti esistenti

Per aggiungere una complicazione a un progetto esistente:

1. Creare un nuovo **ComplicationController.cs** file di classe e implementare `CLKComplicationDataSource`.
2. Configurare l'app **Info. plist** per esporre la complicazione e identità quali famiglie complicazione sono supportate.

Questi passaggi sono descritti in dettaglio più avanti.

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>Classe CLKComplicationDataSource

Il modello di c# seguente include i metodi minimi richiesti per implementare un `CLKComplicationDataSource`.

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
  public ComplicationController ()
  {
    }
  public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
    }
  public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
    }
  public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
    }
}
```

Seguire il [scrittura una complicazione](#writing) istruzioni per aggiungere il codice per questa classe.

### <a name="infoplist"></a>Info. plist

L'estensione di espressioni di controllo **Info. plist** specifica il nome del file di `CLKComplicationDataSource` e quali famiglie complicazione che si desidera supportare:

[ ![](complications-images/complications-config-sml.png "I tipi di famiglia complicazione")](complications-images/complications-config.png)

Il **classe dell'origine dati** voce elenco verranno visualizzati i nomi delle classi di tale sottoclasse `CLKComplicationDataSource` sottoclasse che include la logica di complicazione.

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

Tutte le funzionalità complicazione viene implementata in una singola classe, si esegue l'override di metodi di `CLKComplicationDataSource` classe astratta (che implementa il `ICLKComplicationDataSource` interfaccia).

### <a name="required-methods"></a>Metodi richiesti

È necessario implementare i metodi seguenti per la complicazione per l'esecuzione:

- `GetPlaceholderTemplate` : Restituisce la visualizzazione statica utilizzata durante la configurazione o quando l'app non è possibile fornire un valore.
- `GetCurrentTimelineEntry` : Consente di calcolare la visualizzazione corretta quando complicazione è in esecuzione.
- `GetSupportedTimeTravelDirections` : Restituisce le opzioni da `CLKComplicationTimeTravelDirections` , ad esempio `None`, `Forward`, `Backward`, o `Forward | Backward`.

### <a name="privacy"></a>Privacy

Problemi di visualizzazione dei dati personali

* `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` O `HideOnLockScreen`

Se questo metodo restituisce `HideOnLockScreen` complicazione verrà visualizzati un'icona o il nome dell'applicazione (o non tutti i dati) quando l'orologio è bloccato.

### <a name="updates"></a>Aggiornamenti

- `GetNextRequestedUpdateDate` -Restituiscono l'ora quando il sistema operativo deve quindi eseguire una query l'app per visualizzare i dati aggiornati complicazione.

È anche possibile forzare un aggiornamento dall'app iOS.

### <a name="supporting-time-travel"></a>Supporto di spostamento cronologico

Supporto di viaggio tempo è facoltativo e controllato dal `GetSupportedTimeTravelDirections` metodo. Se restituisce `Forward`, `Backward`, o `Forward | Backward` è necessario implementare i metodi seguenti

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>Scrittura di una complicazione

Intervallo complicazioni dai dati semplici la visualizzazione di immagine complicata e il rendering dei dati con supporto di spostamento cronologico. Il codice riportato di seguito viene illustrato come compilare una complicazione semplice, singolo modello.

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>Codice di esempio

In questo esempio supporta solo il `UtilitarianLarge` modello, pertanto può essere selezionato solo sulle facce di espressioni di controllo specifico che supportano tale tipo di complicazione. Quando *selezionando* complicazioni in un'espressione di controllo, viene visualizzato **COMPLICAZIONE MY** e quando *esecuzione* viene visualizzato il testo **minuto _ora_**   (con una parte del tempo).

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
    public ComplicationController ()
    {
    }
    public ComplicationController (IntPtr p) : base (p)
    {
    }
    public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
        CLKComplicationTimelineEntry entry = null;
    var complicationDisplay = "MINUTE " + DateTime.Now.Minute.ToString(); // text to display on watch face
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge)
        {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText(complicationDisplay); // dynamic display
            entry = CLKComplicationTimelineEntry.Create(NSDate.Now, textTemplate);
        } else {
            Console.WriteLine("Complication family timeline not supported (" + complication.Family + ")");
        }
        handler (entry);
    }
    public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
        CLKComplicationTemplate template = null;
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge) {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat ();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText ("MY COMPLICATION"); // static display
            template = textTemplate;
        } else {
            Console.WriteLine ("Complication family placeholder not not supported (" + complication.Family + ")");
        }
        handler (template);
    }
    public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
        handler (CLKComplicationTimeTravelDirections.None);
    }
}
```


<a name="templates" />

## <a name="complication-templates"></a>Modelli complicazione

Sono disponibili numerosi modelli diversi per ogni stile complicazione.
Il **anello** modelli consentono di visualizzare un anello di stato di avanzamento stile intorno complicazione, che consente di visualizzare graficamente lo stato di avanzamento o un altro valore.

[Documenti CLKComplicationTemplate di Apple](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>Piccola circolare

Questi nomi di classe di modello sono precedute tutte dal prefisso `CLKComplicationTemplateCircularSmall`:

- **RingImage** -visualizzare una singola immagine, con un anello di stato di avanzamento intorno a esso.
- **RingText** -visualizzare una singola riga di testo, con un anello di stato di avanzamento intorno a esso.
- **SimpleImage** -visualizzano solo una singola immagine di piccole dimensioni.
- **SimpleText** -visualizzano solo un piccolo frammento di testo.
- **StackImage** -visualizzare un'immagine e una riga di testo, uno sopra l'altro
- **StackText** -visualizzare due righe di testo.

### <a name="modular-small"></a>Piccola modulare

Questi nomi di classe di modello sono precedute tutte dal prefisso `CLKComplicationTemplateModularSmall`:

- **ColumnsText** -visualizzare una griglia di piccole dimensioni di valori di testo (2 righe e 2 colonne).
- **RingImage** -visualizzare una singola immagine, con un anello di stato di avanzamento intorno a esso.
- **RingText** -visualizzare una singola riga di testo, con un anello di stato di avanzamento intorno a esso.
- **SimpleImage** -visualizzano solo una singola immagine di piccole dimensioni.
- **SimpleText** -visualizzano solo un piccolo frammento di testo.
- **StackImage** -visualizzare un'immagine e una riga di testo, uno sopra l'altro
- **StackText** -visualizzare due righe di testo.

### <a name="modular-large"></a>Modulare di grandi dimensioni

Questi nomi di classe di modello sono precedute tutte dal prefisso `CLKComplicationTemplateModularLarge`:

- **Colonne** -visualizzare una griglia di 3 righe con 2 colonne, include facoltativamente un'immagine a sinistra di ogni riga.
- **StandardBody** -visualizzare una stringa di intestazione in grassetto, con due righe di testo normale. Facoltativamente, è stato possibile visualizzare un'immagine a sinistra con l'intestazione.
- **Tabella** -visualizzare una stringa di intestazione in grassetto, con una 2x2 griglia di testo sottostanti. Facoltativamente, è stato possibile visualizzare un'immagine a sinistra con l'intestazione.
- **TallBody** -visualizzare una stringa di intestazione in grassetto, con una singola riga di dimensioni maggiore del carattere del testo di sotto.

### <a name="utilitarian-small"></a>Piccola funzionale

Questi nomi di classe di modello sono precedute tutte dal prefisso `CLKComplicationTemplateUtilitarianSmall`:

- **Flat** -Visualizza un'immagine e testo in una singola riga (il testo deve essere breve).
- **RingImage** -visualizzare una singola immagine, con un anello di stato di avanzamento intorno a esso.
- **RingText** -visualizzare una singola riga di testo, con un anello di stato di avanzamento intorno a esso.
- **Quadrato** -Visualizza un'immagine di quadrati (40px o 44px quadrato per il 38 mm o 42 mm Apple Watch rispettivamente).

### <a name="utilitarian-large"></a>Funzionali grandi dimensioni

C'è solo un modello per questo stile complicazione: `CLKComplicationTemplateUtilitarianLargeFlat`.
Visualizza una singola immagine e testo, tutto in una singola riga.



## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [Video WWDC](https://developer.apple.com/videos/play/wwdc2015-209/)
