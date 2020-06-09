---
title: Complicazioni watchos in Novell
description: Questo documento descrive come usare le complicazioni watchos in Novell. Viene illustrato come aggiungere una complicazione, scrivendo una complicazione, modelli e viene fornito codice di esempio.
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/03/2017
ms.openlocfilehash: 139b58fd1953924d5a848fc79c3a1706afb760b0
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84565643"
---
# <a name="watchos-complications-in-xamarin"></a>Complicazioni watchos in Novell

_watchos consente agli sviluppatori di scrivere complicazioni personalizzate per i visi di controllo_

In questa pagina vengono illustrati i diversi tipi di complicazioni disponibili e viene illustrato come aggiungere una complicazione all'app watchos 3.

Si noti che ogni applicazione watchos può avere una sola complicazione.

Per iniziare, leggere la [documentazione di Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html) per determinare se l'app è adatta per una complicazione. È possibile `CLKComplicationFamily` scegliere tra 5 tipi di visualizzazione:

[![](complications-images/all-complications-sml.png "The 5 CLKComplicationFamily types available: Circular Small, Modular Small, Modular Large, Utilitarian Small, Utilitarian Large")](complications-images/all-complications.png#lightbox)

Le app possono implementare solo uno stile o tutte e cinque, a seconda dei dati visualizzati.
È anche possibile supportare i viaggi temporali, fornendo valori per le ore passate e/o future quando l'utente attiva il Digital Crown.

<a name="adding"></a>

## <a name="adding-a-complication"></a>Aggiunta di una complicazione

### <a name="configuration"></a>Configurazione

Le complicazioni possono essere aggiunte a un'app Watch durante la creazione o aggiunte manualmente a una soluzione esistente.

### <a name="add-new-project"></a>Aggiungi nuovo progetto...

La procedura guidata **Aggiungi nuovo progetto...** include una casella di controllo che creerà automaticamente una classe del controller di complicazione e configurerà il file **info. plist** :

![](complications-images/file-new-project-sml.png "The Include Complication checkbox")

### <a name="existing-projects"></a>Progetti esistenti

Per aggiungere una complicazione a un progetto esistente:

1. Creare un nuovo file di classe **ComplicationController.cs** e implementare `CLKComplicationDataSource` .
2. Configurare il file **info. plist** dell'app per esporre la complicazione e l'identità quali famiglie di complicazione sono supportate.

Questi passaggi sono descritti in dettaglio di seguito.

<a name="clkcomplicationcontroller"></a>

### <a name="clkcomplicationdatasource-class"></a>Classe CLKComplicationDataSource

Il modello C# seguente include i metodi minimi necessari per implementare un `CLKComplicationDataSource` .

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

Seguire le istruzioni per la [creazione di una complicazione](#writing) per aggiungere codice a questa classe.

### <a name="infoplist"></a>Info. plist

Il file **info. plist** dell'estensione Watch deve specificare il nome e le `CLKComplicationDataSource` famiglie di complicazioni che si desidera supportare:

[![](complications-images/complications-config-sml.png "The complication family types")](complications-images/complications-config.png#lightbox)

L'elenco di voci della **classe dell'origine dati** mostrerà i nomi di classe che sottoclasse sottoclasse `CLKComplicationDataSource` che include la logica di complicazione.

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

Tutte le funzionalità di complicazione sono implementate in una singola classe, eseguendo l'override dei metodi dalla `CLKComplicationDataSource` classe astratta, che implementa l' `ICLKComplicationDataSource` interfaccia.

### <a name="required-methods"></a>Metodi obbligatori

Per eseguire la complicazione, è necessario implementare i metodi seguenti:

- `GetPlaceholderTemplate`-Restituisce la visualizzazione statica usata durante la configurazione o quando l'app non può fornire un valore.
- `GetCurrentTimelineEntry`: Consente di calcolare la visualizzazione corretta quando la complicazione è in esecuzione.
- `GetSupportedTimeTravelDirections`: Restituisce le opzioni da `CLKComplicationTimeTravelDirections` , ad esempio `None` ,, `Forward` `Backward` o `Forward | Backward` .

### <a name="privacy"></a>Privacy

Complicazioni che visualizzano i dati personali

- `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` oppure `HideOnLockScreen`

Se questo metodo restituisce, `HideOnLockScreen` la complicazione Visualizza un'icona o il nome dell'applicazione (e non i dati) quando l'espressione di controllo è bloccata.

### <a name="updates"></a>Aggiornamenti

- `GetNextRequestedUpdateDate`-Restituisce un'ora in cui il sistema operativo deve eseguire una query sull'app per visualizzare i dati di complicazione aggiornati.

È anche possibile forzare un aggiornamento dall'app iOS.

### <a name="supporting-time-travel"></a>Supporto del viaggio in tempo

Il supporto del viaggio temporale è facoltativo e controllato dal `GetSupportedTimeTravelDirections` metodo. Se restituisce `Forward` , `Backward` o, `Forward | Backward` è necessario implementare i metodi seguenti

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing"></a>

## <a name="writing-a-complication"></a>Creazione di una complicazione

Le complicazioni variano dalla semplice visualizzazione dei dati al rendering di immagini e dati complessi con supporto del viaggio in tempo. Il codice seguente illustra come creare una semplice complicazione a modello singolo.

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>Codice di esempio

Questo esempio supporta solo il `UtilitarianLarge` modello, pertanto può essere selezionato solo su visi di controllo specifici che supportano tale tipo di complicazione. Quando si *selezionano* le complicazioni in un'espressione di controllo **, viene visualizzata** la complicazione e durante l' *esecuzione* viene visualizzata l' ** _ora_ del minuto** di testo (con una parte dell'ora).

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

<a name="templates"></a>

## <a name="complication-templates"></a>Modelli di complicazione

Sono disponibili diversi modelli per ogni tipo di complicazione.
I modelli **Ring** consentono di visualizzare un anello dello stato di avanzamento attorno alla complicazione, che può essere usato per visualizzare lo stato di avanzamento o un altro valore graficamente.

[Documentazione di Apple CLKComplicationTemplate](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>Circolare piccola

Questi nomi di classi modello hanno tutti il prefisso `CLKComplicationTemplateCircularSmall` :

- **RingImage** : Visualizza una singola immagine, con un anello di avanzamento.
- **RingText** : Visualizza una sola riga di testo, con un anello di avanzamento.
- **SimpleImage** : è sufficiente visualizzare una piccola immagine singola.
- **SimpleText** : è sufficiente visualizzare un piccolo frammento di testo.
- **StackImage** -Visualizza un'immagine e una riga di testo, una sopra l'altra
- **StackText** -Visualizza due righe di testo.

### <a name="modular-small"></a>Modulare piccola

Questi nomi di classi modello hanno tutti il prefisso `CLKComplicationTemplateModularSmall` :

- **ColumnsText** : Visualizza una piccola griglia di valori di testo (2 righe e 2 colonne).
- **RingImage** : Visualizza una singola immagine, con un anello di avanzamento.
- **RingText** : Visualizza una sola riga di testo, con un anello di avanzamento.
- **SimpleImage** : è sufficiente visualizzare una piccola immagine singola.
- **SimpleText** : è sufficiente visualizzare un piccolo frammento di testo.
- **StackImage** -Visualizza un'immagine e una riga di testo, una sopra l'altra
- **StackText** -Visualizza due righe di testo.

### <a name="modular-large"></a>Modulare grande

Questi nomi di classi modello hanno tutti il prefisso `CLKComplicationTemplateModularLarge` :

- **Colonne** : consente di visualizzare una griglia di 3 righe con due colonne, includendo facoltativamente un'immagine a sinistra di ogni riga.
- **StandardBody** : Visualizza una stringa di intestazione in grassetto, con due righe di testo normale. L'intestazione può facoltativamente visualizzare un'immagine a sinistra.
- **Table** -Visualizza una stringa di intestazione in grassetto con una griglia di testo 2x2 sotto di essa. L'intestazione può facoltativamente visualizzare un'immagine a sinistra.
- **TallBody** : Visualizza una stringa di intestazione in grassetto con una singola riga di testo di tipo carattere più grande sotto.

### <a name="utilitarian-small"></a>Utilitaristica piccola

Questi nomi di classi modello hanno tutti il prefisso `CLKComplicationTemplateUtilitarianSmall` :

- **Flat** : Visualizza un'immagine e un testo su una sola riga (il testo deve essere breve).
- **RingImage** : Visualizza una singola immagine, con un anello di avanzamento.
- **RingText** : Visualizza una sola riga di testo, con un anello di avanzamento.
- **Square** -Visualizza un'immagine quadrata (40px o 44px Square rispettivamente per il 38 mm o Apple Watch 42 mm).

### <a name="utilitarian-large"></a>Utilitaristica grande

È disponibile un solo modello per questo stile di complicazione: `CLKComplicationTemplateUtilitarianLargeFlat` .
Viene visualizzata una singola immagine e un testo, tutto su una sola riga.

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione di Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [Video di WWDC](https://developer.apple.com/videos/play/wwdc2015-209/)
