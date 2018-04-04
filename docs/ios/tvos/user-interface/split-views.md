---
title: Utilizzo di controller di visualizzazione divisa
description: Questo articolo descrive la progettazione e l'utilizzo di visualizzazione controller di divisione all'interno di un'app Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 25151eb2929e2bc61dba27a9937ffdf4ee224626
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-split-view-controllers"></a>Utilizzo di controller di visualizzazione divisa

_Questo articolo descrive la progettazione e l'utilizzo di visualizzazione controller di divisione all'interno di un'app Xamarin.tvOS._


Un Controller di visualizzazione di suddivisione presenta e gestisce un Master e il Controller di visualizzazione di dettaglio side-by-side, sullo schermo contemporaneamente. Divisione visualizzazione controller sono usato per presentare contenuto persistente, attivabile nella visualizzazione Master (sezione inferiore a sinistra) e relativi dettagli nella vista di dettaglio (sezione più grande a destra).

[![](split-views-images/intro01.png "Esempio doppia visualizzazione")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>Sui controller di visualizzazione divisa

Come descritto in precedenza, una divisione View Controller gestisce un Master e un Controller di visualizzazione di dettaglio presenti side-by-side con il server principale viene la vista più limitata a sinistra, i dettagli più grande a destra. 

Inoltre, può Master View Controller è stato nascosto o visualizzato in base alle necessità: 

[![](split-views-images/intro02.png "Il Controller di visualizzazione Master nascosto")](split-views-images/intro02.png#lightbox)

Divisione viste controller sono spesso usano per presentare un elenco del contenuto filtrabile, con le categorie nella visualizzazione schema e i risultati filtrati nella vista di dettaglio. Ciò in genere viene presentato come una visualizzazione tabella a sinistra e un [visualizzazione raccolta](~/ios/tvos/user-interface/collection-views.md) a destra.

Quando si progetta un'interfaccia utente che richiede un Controller di visualizzazione di divisione, Apple suggerisce utilizzando Master e controller di visualizzazione di dettaglio che non cambiano (solo le modifiche del contenuto, non la struttura). Se è necessario swap-out, visualizzazione controller, è consigliabile utilizzare un Controller di navigazione come base di Controller di visualizzazione che è necessario modificare (Master o dettaglio).

Apple ha i seguenti suggerimenti per l'utilizzo di visualizzazione controller di suddivisione:

- **Utilizzare la corretta percentuale Split** -per impostazione predefinita il Controller di visualizzazione divisa Usa un terzo della schermata per il Controller di visualizzazione Master e due terzi per il Controller di visualizzazione di dettaglio. Facoltativamente, è possibile utilizzare una divisione 50/50. Scegliere la corretta percentuale per visualizzare il contenuto bilanciato sullo schermo.
- **Mantenere la selezione di Main** - mentre il contenuto possono la visualizzazione dei dettagli delle modifiche è una risposta alla selezione di un utente nella visualizzazione Master, è necessario correggere il contenuto della visualizzazione Master. Inoltre, è necessario chiare l'elemento attualmente selezionato nella visualizzazione Master.
- **Utilizzare una singola unificata titolo** -in genere, è opportuno utilizzare un titolo singolo centrata nella vista di dettaglio, anziché un titolo in dettagli e la visualizzazione Master.

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>Doppia visualizzazione controller e gli storyboard

Il modo più semplice per utilizzare visualizzazione controller di suddivisione in un'app Xamarin.tvOS è per aggiungerli all'interfaccia utente dell'applicazione utilizzando la finestra di progettazione iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Nel **soluzione riempimento**, fare doppio clic su di `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **Split visualizzazione controller** dal **della casella degli strumenti** e rilasciarla sulla vista: 

    [![](split-views-images/activity01.png "Un Controller di visualizzazione divisa")](split-views-images/activity01.png#lightbox)
1. Per impostazione predefinita, la finestra di progettazione iOS verrà installato un Controller di navigazione e un Controller di visualizzazione della visualizzazione Master. Se ciò non corrispondente requisiti dell'applicazione, è sufficiente eliminarli.
1. Se si rimuove il valore predefinito di visualizzazione Master, trascinare un nuovo Controller di visualizzazione nella finestra di progettazione: 

    [![](split-views-images/activity02.png "Un Controller di visualizzazione")](split-views-images/activity02.png#lightbox)
1. CTRL + clic e trascinare dal Controller di visualizzazione divisa per il nuovo Controller di visualizzazione Master. 
1. Selezionare **Master** dal **Menu Popup**: 

    [![](split-views-images/activity03.png "Selezionare Master dal Menu di scelta rapida")](split-views-images/activity03.png#lightbox)
1. Progettare il contenuto del Master e visualizzazioni Dettagli: 

    [![](split-views-images/activity04.png "Layout di esempio")](split-views-images/activity04.png#lightbox)
1. Assegnare **nomi** nel **scheda Widget** del **proprietà riempimento** per lavorare con i controlli dell'interfaccia utente in codice c#.
1. Salvare le modifiche e tornare a Visual Studio per Mac.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Nel **Esplora**, fare doppio clic su di `Main.storyboard` e aprirlo per la modifica.
1. Trascinare un **Split visualizzazione controller** dal **della casella degli strumenti** e rilasciarla sulla vista: 

    [![](split-views-images/activity01-vs.png "Un Controller di visualizzazione divisa")](split-views-images/activity01-vs.png#lightbox)
1. Per impostazione predefinita, la finestra di progettazione iOS aggiungerà un Controller di navigazione e un Controller di visualizzazione della visualizzazione Master. Se ciò non corrispondente requisiti dell'applicazione, è sufficiente eliminarli.
1. Se si rimuove il valore predefinito di visualizzazione Master, trascinare un nuovo Controller di visualizzazione nella finestra di progettazione: 

    [![](split-views-images/activity02-vs.png "Un Controller di visualizzazione")](split-views-images/activity02-vs.png#lightbox)
1. CTRL + clic e trascinare dal Controller di visualizzazione divisa per il nuovo Controller di visualizzazione Master. 
1. Selezionare **Master** dal **Menu Popup**: 

    [![](split-views-images/activity03-vs.png "Selezionare Master dal Menu di scelta rapida")](split-views-images/activity03-vs.png#lightbox)
1. Progettare il contenuto del Master e visualizzazioni Dettagli: 

    [![](split-views-images/activity04.png "Layout del contenuto")](split-views-images/activity04.png#lightbox)
1. Assegnare **nomi** nel **scheda Widget** del **Esplora proprietà** per lavorare con i controlli dell'interfaccia utente in codice c#.
1. Salvare le modifiche.
    
-----

Per ulteriori informazioni sull'uso di storyboard, consultare il nostro [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>Utilizzo di controller di visualizzazione divisa

Come descritto in precedenza, un Controller di visualizzazione di divisione viene spesso utilizzato nelle situazioni in cui vengono visualizzati i contenuti filtrati all'utente. Le categorie principali vengono visualizzate a sinistra della visualizzazione Master, e a destra nella visualizzazione dettagli risultati filtrati in base alla selezione dell'utente.

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>L'accesso a Master e dettaglio

Se è necessario accedere a livello di codice lo schema e i controller di visualizzazione di dettaglio, utilizzare il `ViewControllers ` proprietà del Controller di visualizzazione di divisione. Ad esempio:

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

Viene visualizzato sotto forma di matrice, dove il primo elemento (0) nel Controller di visualizzazione Master e il secondo elemento (1) è il dettaglio.

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>L'accesso ai dettagli dal Master

Poiché in genere per visualizzare informazioni dettagliate nella vista di dettaglio in base alla selezione dell'utente nella pagina Master, è necessario un modo per accedere ai dettagli dal Master.

Il modo più semplice per eseguire questa operazione consiste nell'esporre una proprietà della classe di visualizzazione Master Controller, ad esempio:

```csharp
public DetailViewController DetailController { get; set;}
```

Nel Controller di visualizzazione di divisione, eseguire l'override di `ViewDidLoad` metodo tie le due visualizzazioni e insieme. Ad esempio:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Gain access to master and detail view controllers
    var masterController = ViewControllers [0] as MasterViewController;
    var detailController = ViewControllers [1] as DetailViewController;

    // Wire-up views
    masterController.SplitViewController = this;
    masterController.DetailController = detailController;
    detailController.SplitViewController = this;
}
```

È possibile esporre proprietà e metodi nel Controller di visualizzazione di dettaglio che lo schema è possibile utilizzare per presentare i nuovi dati come richiesto.

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>Mostrare e nascondere Master

Facoltativamente, è possibile visualizzare e nascondere la visualizzazione Master Controller utilizzando il `PreferredDisplayMode` proprietà del Controller di visualizzazione di divisione. Ad esempio:

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

Il `UISplitViewControllerDisplayMode` enumerazione definisce la modalità Controller principale di visualizzazione verranno presentati come uno dei seguenti:

- **Automatico** -tvOS controllerà la presentazione dei database Master e visualizzazioni dettagli.
- **PrimaryHidden** -in tal modo, il Controller di visualizzazione Master.
- **AllVisible** -vengono visualizzati lo schema sia il controller di visualizzazione di dettaglio side-by-side. Si tratta della normale, presentazione predefinita.
- **PrimaryOverlay** -il Controller di visualizzazione di dettaglio si estende in ed è coperto dal Master.

Per ottenere lo stato corrente di presentazione, utilizzare il `DisplayMode` proprietà del Controller di visualizzazione di divisione.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è descritta la progettazione e l'utilizzo di visualizzazione controller di divisione all'interno di un'app Xamarin.tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
