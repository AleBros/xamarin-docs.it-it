---
title: watchOS complicazioni in Xamarin
description: Questo documento descrive come lavorare con watchOS complicazioni in Xamarin. Questo viene illustrato come aggiungere una complicazione, scrivere una complicazione, modelli e fornisce il codice di esempio.
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/03/2017
ms.openlocfilehash: 85b0c9b0688e9fb310a8f427018a02fe629404bb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61225548"
---
# <a name="watchos-complications-in-xamarin"></a>watchOS complicazioni in Xamarin

_watchOS consente agli sviluppatori di scrivere complicazioni personalizzati per i visi di espressioni di controllo_

Questa pagina illustra i diversi tipi di problemi disponibili e su come aggiungere una complicazione per l'app watchOS 3.

Si noti che ogni applicazione watchOS può avere solo una complicazione.

Iniziare leggendo [documentazione di Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html) per determinare se l'app è adatto per una complicazione. Sono disponibili 5 `CLKComplicationFamily` tipi di visualizzazione per scegliere tra:

[![](complications-images/all-complications-sml.png "I tipi CLKComplicationFamily 5 disponibili: Circolare piccola, piccola modulare, modulare piccola funzionale e di grandi dimensioni, posto grandi dimensioni")](complications-images/all-complications.png#lightbox)

Le app possono implementare solo uno stile o tutti e cinque, a seconda dei dati visualizzati.
È anche possibile supportare cronologicamente, inserendo i valori specificati per gli orari precedenti e/o futuro l'utente attiva il Crown digitale.

<a name="adding" />

## <a name="adding-a-complication"></a>Aggiunta di una complicazione

### <a name="configuration"></a>Configurazione

Complicazioni possono essere aggiunto a un'app watch durante la creazione o aggiunti manualmente a una soluzione esistente.

### <a name="add-new-project"></a>Aggiungi nuovo progetto...

Il **Aggiungi nuovo progetto...**  procedura guidata include una casella di controllo che creerà automaticamente una classe controller complicazioni e configurare il **Info. plist** file:

![](complications-images/file-new-project-sml.png "La casella di controllo Includi complicazione")

### <a name="existing-projects"></a>Progetti esistenti

Per aggiungere una complicazione a un progetto esistente:

1. Creare una nuova **ComplicationController.cs** file di classe e implementare `CLKComplicationDataSource`.
2. Configurare l'app **Info. plist** per esporre la complicazione e identità sono supportate le famiglie di complicazione.

Questi passaggi sono descritti in dettaglio più avanti.

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>Classe CLKComplicationDataSource

Il seguente C# modello include il numero minimo di metodi necessario per implementare un `CLKComplicationDataSource`.

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

Seguire le [scrittura di una complicazione](#writing) istruzioni per aggiungere codice per questa classe.

### <a name="infoplist"></a>Info. plist

L'estensione di espressioni di controllo **Info. plist** specifica il nome del file la `CLKComplicationDataSource` e le famiglie di complicazione si vuole supportare:

[![](complications-images/complications-config-sml.png "I tipi della famiglia complicazione")](complications-images/complications-config.png#lightbox)

Il **classe dell'origine dati** voce elenco verranno visualizzati i nomi delle classi tale sottoclasse `CLKComplicationDataSource` sottoclasse che include la logica complicazione.

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

Tutte le funzionalità di complicazione viene implementata in una singola classe, l'override dei metodi dal `CLKComplicationDataSource` classe astratta (che implementa il `ICLKComplicationDataSource` interface).

### <a name="required-methods"></a>Metodi richiesti

È necessario implementare i metodi seguenti per la complicazione per l'esecuzione:

- `GetPlaceholderTemplate` : Restituisce la visualizzazione statica utilizzata durante la configurazione o quando l'app non è possibile fornire un valore.
- `GetCurrentTimelineEntry` : Consente di calcolare la visualizzazione corretta quando è in esecuzione la complicazione.
- `GetSupportedTimeTravelDirections` : Restituisce le opzioni `CLKComplicationTimeTravelDirections` , ad esempio `None`, `Forward`, `Backward`, o `Forward | Backward`.

### <a name="privacy"></a>Privacy

Complicazioni che consentono di visualizzare i dati personali

* `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` o `HideOnLockScreen`

Se questo metodo restituisce `HideOnLockScreen` complicazione verrà visualizzati sia un'icona o il nome dell'applicazione (e non tutti i dati) quando l'espressione di controllo è bloccato.

### <a name="updates"></a>Aggiornamenti

- `GetNextRequestedUpdateDate` -Restituiscono l'ora quando il sistema operativo deve quindi esegue una query all'app per visualizzare i dati aggiornati complicazione.

È anche possibile forzare un aggiornamento da un'app per iOS.

### <a name="supporting-time-travel"></a>Supporto cronologicamente

Il supporto di viaggio tempo è facoltativo e viene controllato dal `GetSupportedTimeTravelDirections` (metodo). Se viene restituito `Forward`, `Backward`, o `Forward | Backward` quindi è necessario implementare i metodi seguenti

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>La scrittura di una complicazione

Intervallo complicazioni dai dati semplici visualizzare all'immagine complessa e il rendering dei dati con supporto cronologicamente. Il codice seguente illustra come compilare una complicazione semplice, single-template.

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>Codice di esempio

In questo esempio supporta solo il `UtilitarianLarge` modello, in modo che possono essere selezionate solo sulle facce di espressioni di controllo specifici che supportano tale tipo di complicazione. Quando *selezionando* complicazioni in un'espressione di controllo, viene visualizzato **COMPLICAZIONE MY** e il momento *in esecuzione* venga visualizzato il testo **minuto _ora_**   (con una parte del tempo).

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
Il **anello** i modelli consentono di visualizzare un anello di stato di avanzamento in stile intorno complicazione, che può essere utilizzato per visualizzare graficamente lo stato di avanzamento o un altro valore.

[Documentazione di Apple CLKComplicationTemplate](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>Circolare piccola

Questi nomi di classe di modello sono precedute tutte dal prefisso `CLKComplicationTemplateCircularSmall`:

- **RingImage** -visualizzare una singola immagine, con un anello di stato intorno a esso.
- **RingText** -visualizzare una singola riga di testo, con un anello di stato intorno a esso.
- **SimpleImage** -visualizzano solo una piccola immagine singola.
- **SimpleText** -visualizzano solo un piccolo frammento di testo.
- **StackImage** -visualizzare un'immagine e una riga di testo, uno sopra l'altro
- **StackText** -visualizzare due righe di testo.

### <a name="modular-small"></a>Modulare piccola

Questi nomi di classe di modello sono precedute tutte dal prefisso `CLKComplicationTemplateModularSmall`:

- **ColumnsText** -visualizzare una piccola griglia di valori di testo (2 righe e 2 colonne).
- **RingImage** -visualizzare una singola immagine, con un anello di stato intorno a esso.
- **RingText** -visualizzare una singola riga di testo, con un anello di stato intorno a esso.
- **SimpleImage** -visualizzano solo una piccola immagine singola.
- **SimpleText** -visualizzano solo un piccolo frammento di testo.
- **StackImage** -visualizzare un'immagine e una riga di testo, uno sopra l'altro
- **StackText** -visualizzare due righe di testo.

### <a name="modular-large"></a>Modulare grande

Questi nomi di classe di modello sono precedute tutte dal prefisso `CLKComplicationTemplateModularLarge`:

- **Colonne** -visualizzare una griglia di 3 righe con 2 colonne, includendo facoltativamente un'immagine a sinistra di ogni riga.
- **StandardBody** -visualizzazione di una stringa di intestazione in grassetto, con due righe di testo normale. Facoltativamente, è stato possibile visualizzare un'immagine a sinistra con l'intestazione.
- **Tabella** -visualizzazione di una stringa di intestazione in grassetto, con una 2x2 griglia di testo sottostanti. Facoltativamente, è stato possibile visualizzare un'immagine a sinistra con l'intestazione.
- **TallBody** -visualizzazione di una stringa di intestazione in grassetto, con una singola riga di dimensioni maggiori del tipo di carattere del testo di sotto.

### <a name="utilitarian-small"></a>Piccola funzionale

Questi nomi di classe di modello sono precedute tutte dal prefisso `CLKComplicationTemplateUtilitarianSmall`:

- **Flat** -Visualizza un'immagine e testo in un'unica riga (il testo deve essere breve).
- **RingImage** -visualizzare una singola immagine, con un anello di stato intorno a esso.
- **RingText** -visualizzare una singola riga di testo, con un anello di stato intorno a esso.
- **Quadrato** -visualizzare un'immagine di quadrati (40px o 44px quadrato per la 38 mm o 42mm Apple Watch rispettivamente).

### <a name="utilitarian-large"></a>Posto grandi dimensioni

È presente un solo modello per questo stile complicazione: `CLKComplicationTemplateUtilitarianLargeFlat`.
Visualizza una singola immagine e testo, tutti in un'unica riga.



## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [Video WWDC](https://developer.apple.com/videos/play/wwdc2015-209/)
