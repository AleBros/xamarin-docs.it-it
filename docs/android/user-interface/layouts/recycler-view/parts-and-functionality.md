---
title: "Funzionalità e le parti RecyclerView"
ms.topic: article
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: b1ddcca25fd83a806e8383a5717462b518b46d0b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="recyclerview-parts-and-functionality"></a>Funzionalità e le parti RecyclerView


`RecyclerView` gli handle di alcune attività internamente (ad esempio di scorrimento e il riciclo delle visualizzazioni), ma è essenzialmente un manager che coordina le classi di supporto per visualizzare una raccolta. `RecyclerView` attività di delegati per le classi helper seguenti:

-   **`Adapter`** &ndash; Ingrandisce l'elemento layout (crea il contenuto di un file di layout) e associa i dati alle viste che vengono visualizzate all'interno di un `RecyclerView`. La scheda report anche gli eventi click di elemento.

-   **`LayoutManager`** &ndash; Misure e posiziona l'elemento visualizzazioni all'interno di un `RecyclerView` e gestisce i criteri per il riciclo di visualizzazione.

-   **`ViewHolder`** &ndash; Ricerca e archivia i riferimenti di visualizzazione. Il titolare Vista consente inoltre di con rilevamento fa clic su visualizzazione elemento.

-   **`ItemDecoration`** &ndash; Consente a un'app aggiungere gli offset di disegno e layout speciali a visualizzazioni specifiche per la creazione di separatori tra gli elementi, caratteristiche e limiti di raggruppamento visivo.

-   **`ItemAnimator`** &ndash; Definisce le animazioni che ha luogo durante le azioni di elemento o se si modifica l'adapter.

La relazione tra il `RecyclerView`, `LayoutManager`, e `Adapter` classi è illustrato nel diagramma seguente:

![Diagramma di RecyclerView contenente LayoutManager, utilizza l'Adapter per accedere ai Set di dati](parts-and-functionality-images/01-recyclerview-diagram.png)

Come illustrato nella figura, il `LayoutManager` può essere considerato come intermediario tra la `Adapter` e `RecyclerView`. Il `LayoutManager` effettua chiamate `Adapter` metodi per conto del `RecyclerView`. Ad esempio, il `LayoutManager` chiamate un `Adapter` quando è necessario creare una nuova visualizzazione per una posizione particolare elemento nel metodo il `RecyclerView`. Il `Adapter` incrementa il layout per l'elemento e crea un `ViewHolder` istanza (non illustrato) i riferimenti alle viste in tale posizione. Quando il `LayoutManager` chiamate di `Adapter` per associare un particolare elemento al set di dati, il `Adapter` individua i dati per l'elemento, recupera dal set di dati e viene copiato per la visualizzazione elemento associato.

Quando si utilizza `RecyclerView` nell'app, è necessario creare tipi derivati delle classi seguenti:

-   **`RecyclerView.Adapter`** &ndash; Fornisce un'associazione dal set di dati dell'applicazione (ovvero specifici dell'app) alle viste di elemento che vengono visualizzati all'interno di `RecyclerView`. L'adapter conosca come associare ogni posizione di visualizzazione di elementi nella `RecyclerView` in un percorso specifico dell'origine dati. Inoltre, l'adattatore gestisce il layout del contenuto all'interno di ogni singolo elemento di visualizzazione e crea il contenitore di visualizzazione per ogni visualizzazione. L'adapter indica inoltre elemento-fare clic su eventi rilevati per la visualizzazione elemento.

-   **`RecyclerView.ViewHolder`** &ndash; Memorizza nella cache i riferimenti alle viste nel file di layout di elementi in modo che le ricerche di risorse non vengono ripetute inutilmente. Il titolare di visualizzazione dispone anche essere inoltrato all'adapter quando un utente tocca visualizzazione elemento associato del titolare visualizzazione eventi di elemento-fare clic su.

-   **`RecyclerView.LayoutManager`** &ndash; Posiziona gli elementi all'interno di `RecyclerView`. È possibile usare uno dei diversi gestori di layout predefinito o è possibile implementare la propria gestione di un layout personalizzato.
    `RecyclerView` delegati i criteri di layout per la gestione di layout, quindi è possibile inserire in un gestore di layout diversi senza dover apportare significativi modifica all'app.

Inoltre, è facoltativamente possibile estendere le classi seguenti per modificare l'aspetto di `RecyclerView` nell'app:

-   **`RecyclerView.ItemDecoration`**
-   **`RecyclerView.ItemAnimator`**

Se non si estende `ItemDecoration` e `ItemAnimator`, `RecyclerView` utilizza le implementazioni predefinite. Questa guida illustrano come creare personalizzato `ItemDecoration` e `ItemAnimator` classi; per ulteriori informazioni su queste classi, vedere [RecyclerView.ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html) e [RecyclerView.ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).


<a name="recycling" />

### <a name="how-view-recycling-works"></a>Come visualizzare il funzionamento di riciclo

`RecyclerView` non alloca un'elemento di visualizzazione per ogni elemento nell'origine dati. Invece di allocare solo il numero di viste di elemento che lo schermo e riutilizza i layout di elementi come l'utente scorre. Quando la vista prima scorre non è più visualizzata, passano attraverso il processo di riciclo illustrato nella figura seguente:

[ ![Diagramma che illustra i sei passaggi di riciclo di visualizzazione](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png)

1.  Quando una vista eseguito lo scorrimento non è più visualizzata e non viene più visualizzata, diventa un *scarto vista*.

2.  La visualizzazione di scarto viene inserita in un pool e diventa un *riciclare vista*.
    Questo pool è una cache di viste che visualizzano lo stesso tipo di dati.

3.  Quando un nuovo elemento è visualizzata, una vista viene eseguita dal pool di riciclo per il riutilizzo. Poiché in questa vista deve essere associata nuovamente dall'adapter prima di essere visualizzato, viene chiamato un *dirty vista*.

4.  La vista dirty viene riciclata: l'adapter consente di individuare i dati per l'elemento successivo deve essere visualizzato e questi dati vengono copiati alle visualizzazioni per questo elemento. Riferimenti per queste viste vengono recuperati dal titolare di visualizzazione associato alla visualizzazione riciclata.

5.  La vista riciclata viene aggiunto all'elenco di elementi nel `RecyclerView` che sta per passare sullo schermo.

6.  La vista riciclata viene visualizzato sullo schermo come l'utente scorre il `RecyclerView` all'elemento successivo nell'elenco. Nel frattempo, un'altra visualizzazione scorre non è più visualizzata e viene riciclata in base ai passaggi precedenti.

Oltre a riutilizzare visualizzazione elemento `RecyclerView` Usa anche un'altra ottimizzazione dell'efficienza: visualizzare i titolari. Oggetto *titolare visualizzazione* è una classe semplice che memorizza nella cache consente di visualizzare i riferimenti. Ogni volta che l'adapter ingrandisce un file di layout dell'elemento, viene inoltre creato un contenitore di visualizzazione corrispondente. Il titolare della vista utilizza `FindViewById` per ottenere i riferimenti alle viste all'interno del file di layout di elemento ingrandita. Questi riferimenti vengono utilizzati per caricare le viste di nuovi dati ogni volta che il layout viene riciclato per visualizzare i nuovi dati.
 

<a name="layoutmanager" />

### <a name="the-layout-manager"></a>Il gestore di Layout

Gestore del layout è responsabile per il posizionamento di elementi di `RecyclerView` visualizzare; determina il tipo di presentazione (un elenco o una griglia), l'orientamento (se gli elementi vengono visualizzati orizzontalmente o verticalmente) e gli elementi di direzione che devono essere visualizzati (in ordine normale o in ordine inverso). Gestore del layout è inoltre responsabile per il calcolo della dimensione e posizione di ciascun elemento di **RecycleView** visualizzare.

La gestione di layout ha uno scopo ulteriore: determina i criteri quando riciclare le viste di elemento che non sono più visibili all'utente.
Poiché il gestore di layout è a conoscenza di quali visualizzazioni sono visibili (e che non sono), è nella posizione ideale per decidere quando una vista può essere riutilizzata. Per eseguire il riciclo una visualizzazione, il gestore di layout effettua chiamate alla scheda di sostituire il contenuto di una vista riciclato con dati diversi, in genere come descritto in precedenza in [visualizzazione riciclo funzionamento](#recycling).

È possibile estendere `RecyclerView.LayoutManager` per creare un layout personalizzato manager, oppure è possibile utilizzare un gestore di layout predefinito. `RecyclerView` Fornisce i gestori di layout predefinite seguenti:

-   **`LinearLayoutManager`** &ndash; Dispone gli elementi in una colonna che è possibile scorrere in senso verticale o in una riga che è possibile scorrere orizzontalmente.

-   **`GridLayoutManager`** &ndash; Visualizza gli elementi in una griglia.

-   **`StaggeredGridLayoutManager`** &ndash; Visualizza gli elementi in una griglia fasi successive, in cui alcuni elementi hanno larghezze e altezze diverse.

Per specificare il gestore di layout, il gestore del layout scelto di creare un'istanza e passarlo al `SetLayoutManager` metodo. Si noti che si *deve* specificare la gestione di layout &ndash; `RecyclerView` non consente di selezionare una layout predefinito gestione per impostazione predefinita.

Per ulteriori informazioni sulla gestione di layout, vedere il [riferimento alla classe RecyclerView.LayoutManager](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html).

<a name="viewholder" />

### <a name="the-view-holder"></a>Il titolare della visualizzazione

Il titolare della vista è una classe definita per la memorizzazione nella cache i riferimenti di visualizzazione. L'adapter utilizza questi riferimenti di visualizzazione per associare ogni vista al relativo contenuto. Tutti gli elementi di `RecyclerView` dispone di un'istanza di visualizzazione associata titolare che memorizza nella cache i riferimenti di visualizzazione per l'elemento. Per creare un contenitore di visualizzazione, utilizzare la procedura seguente per definire una classe per contenere il set esatto delle visualizzazioni per ogni elemento:

1.  Sottoclasse `RecyclerView.ViewHolder`.
2.  Implementare un costruttore che esegue la ricerca e archivia i riferimenti di visualizzazione.
3.  Implementare le proprietà che la scheda è possibile utilizzare per accedere a tali riferimenti.

Un esempio dettagliato di un `ViewHolder` implementazione è presentata in [A base RecyclerView esempio](~/android/user-interface/layouts/recycler-view/recyclerview-example.md).
Per ulteriori informazioni su `RecyclerView.ViewHolder`, vedere il [riferimento alla classe RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).

<a name="adapter" />

### <a name="the-adapter"></a>L'Adapter

La maggior parte del "impegnativo" del `RecyclerView` integrazione del codice viene eseguita nell'adapter. `RecyclerView` è necessario fornire un adattatore derivato dalla `RecyclerView.Adapter` per accedere all'origine dati e popolare ogni elemento con contenuto dall'origine dati.
Poiché l'origine dati è specifico dell'applicazione, è necessario implementare funzionalità dell'adapter che siano in grado di accedere ai dati. L'adapter estrae le informazioni dall'origine dati e li carica in ogni elemento di `RecyclerView` insieme.

Nel disegno seguente viene illustrato come l'adapter esegue il mapping del contenuto in un'origine dati tramite i titolari di visualizzazione viste singole all'interno di ogni elemento di riga di `RecyclerView`:

[ ![Diagramma che illustra scheda Connessione origine dati alla ViewHolders](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png)

L'adapter carica ogni `RecyclerView` riga con dati di un elemento di riga specifico. Per la posizione riga *P*, ad esempio, l'adapter consente di individuare i dati associati nella posizione *P* all'interno dell'origine dati e le copie di questi dati alla riga di elemento nella posizione *P* nel `RecyclerView` insieme.
Nel disegno precedente, ad esempio, l'adapter utilizza il titolare della vista per cercare i riferimenti per il `ImageView` e `TextView` in tale posizione, in modo da non dover chiamare ripetutamente `FindViewById` per tali visualizzazioni come l'utente scorre la raccolta e Riutilizza viste.

Quando si implementa un adapter, è necessario eseguire l'override seguente `RecyclerView.Adapter` metodi:

-   **`OnCreateViewHolder`** &ndash; Crea un'istanza del titolare file e Visualizza elemento di layout.

-   **`OnBindViewHolder`** &ndash; Carica i dati nella posizione specificata nelle viste a cui i riferimenti vengono archiviati nel titolare della visualizzazione specificata.

-   **`ItemCount`** &ndash; Restituisce il numero di elementi nell'origine dati.

Il gestore di layout chiama questi metodi mentre è posizionamento di elementi all'interno di `RecyclerView`. 


<a name="datachanges" />

### <a name="notifying-recyclerview-of-data-changes"></a>RecyclerView notifica delle modifiche ai dati

`RecyclerView` non aggiornare automaticamente la relativa visualizzazione quando il contenuto dei dati di origine le modifiche. l'adapter deve notificare `RecyclerView` quando viene apportata una modifica nel set di dati. Il set di dati è possibile modificare in molti modi; ad esempio possibile modificare il contenuto all'interno di un elemento o la struttura generale dei dati può essere modificata.
`RecyclerView.Adapter` fornisce una serie di metodi che è possibile chiamare in modo che `RecyclerView` risponde alle modifiche dei dati in maniera più efficiente:

-  **`NotifyItemChanged`** &ndash; Segnala che è stato modificato l'elemento in corrispondenza della posizione specificata.

-  **`NotifyItemRangeChanged`** &ndash; Segnala che sono stati modificati gli elementi nell'intervallo specificato di posizioni.

-  **`NotifyItemInserted`** &ndash; Segnala che l'elemento nella posizione specificata è stata appena inserita.

-  **`NotifyItemRangeInserted`** &ndash; Segnala se gli elementi nell'intervallo specificato di posizioni sono stati inseriti nuovi.

-  **`NotifyItemRemoved`** &ndash; Segnala che l'elemento nella posizione specificata è stata rimossa.

-  **`NotifyItemRangeRemoved`** &ndash; Segnala che sono stati rimossi gli elementi nell'intervallo specificato di posizioni.

-  **`NotifyDataSetChanged`** &ndash; Segnala che è stato modificato il set di dati (forza un aggiornamento completo).

Se si conosce esattamente come il set di dati è stata modificata, è possibile chiamare i metodi appropriati per aggiornare `RecyclerView` nel modo più efficiente. Se non si conosce esattamente come il set di dati è stata modificata, è possibile chiamare `NotifyDataSetChanged`, che risulta molto meno efficiente poiché `RecyclerView` necessario aggiornare tutte le viste che sono visibili all'utente. Per ulteriori informazioni su questi metodi, vedere [RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

Nell'argomento successivo, [A base RecyclerView esempio](~/android/user-interface/layouts/recycler-view/recyclerview-example.md), viene implementata un'app di esempio per illustrare esempi di codice effettivo delle parti e delle funzionalità descritte in precedenza.


## <a name="related-links"></a>Collegamenti correlati

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Un esempio di base RecyclerView](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [Estensione dell'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
