---
title: Funzionalità e le parti di RecyclerView
description: Panoramica del gestore di layout RecyclerView, adapter e titolare della visualizzazione.
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: 13678d3b1bca102e6f608ad1c11838db1f14cd08
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61309186"
---
# <a name="recyclerview-parts-and-functionality"></a>Funzionalità e le parti di RecyclerView


`RecyclerView` gli handle di alcune attività internamente (ad esempio la scorrimento e il riciclo delle visualizzazioni), ma è essenzialmente un manager che coordina le classi helper per la visualizzazione di una raccolta. `RecyclerView` delega le attività per le classi helper seguenti:

-   **`Adapter`** &ndash; Incrementa i layout di elementi (crea il contenuto di un file di layout) e associa i dati alle viste che vengono visualizzate all'interno di un `RecyclerView`. L'adapter segnala anche gli eventi click di elemento.

-   **`LayoutManager`** &ndash; Misura e posiziona le viste di elemento all'interno di un `RecyclerView` e gestisce i criteri per il riciclaggio di visualizzazione.

-   **`ViewHolder`** &ndash; Ricerca e archivia i riferimenti di visualizzazione. Il titolare della visualizzazione consente inoltre di rilevare i clic visualizzazione dell'elemento.

-   **`ItemDecoration`** &ndash; Consente a un'app aggiungere gli offset di disegno e layout speciali a visualizzazioni specifiche per il disegno di separatori tra gli elementi, evidenziazioni e i limiti di raggruppamento visivo.

-   **`ItemAnimator`** &ndash; Definisce le animazioni che si verificano durante le azioni di elemento o come le modifiche vengono apportate all'adapter.

La relazione tra il `RecyclerView`, `LayoutManager`, e `Adapter` classi è illustrata nel diagramma seguente:

![Diagramma di RecyclerView contenente LayoutManager, l'utilizzo degli Adapter per accedere ai Set di dati](parts-and-functionality-images/01-recyclerview-diagram.png)

Come illustrato nella figura, il `LayoutManager` possono essere considerati come intermediario tra il `Adapter` e il `RecyclerView`. Il `LayoutManager` effettua chiamate al `Adapter` metodi per conto del `RecyclerView`. Ad esempio, il `LayoutManager` chiamate un' `Adapter` metodo quando si è pronti per creare una nuova visualizzazione per una posizione particolare elemento nel `RecyclerView`. Il `Adapter` incrementa il layout per l'elemento e crea un `ViewHolder` istanza (non mostrato) i riferimenti alle viste in tale posizione. Quando la `LayoutManager` chiama il `Adapter` per associare un particolare elemento nel set di dati, il `Adapter` individua i dati per tale elemento, li recupera dal set di dati e lo copia alla visualizzazione elemento associato.

Quando si usa `RecyclerView` nell'app, è necessario creare tipi derivati delle classi seguenti:

-   **`RecyclerView.Adapter`** &ndash; Fornisce un'associazione dal set di dati dell'app, ovvero specifici per l'applicazione, alle viste di elemento che vengono visualizzati all'interno di `RecyclerView`. L'adapter conosca come associare ogni posizione visualizzazione dell'elemento nella `RecyclerView` in una posizione specifica nell'origine dati. Inoltre, l'adattatore gestisce il layout del contenuto all'interno di ogni singolo elemento di visualizzazione e consente di creare il contenitore di visualizzazione per ogni visualizzazione. L'adapter segnala anche gli eventi elemento clic che vengono rilevati mediante la visualizzazione elemento.

-   **`RecyclerView.ViewHolder`** &ndash; Memorizza nella cache i riferimenti alle visualizzazioni nel file di layout di elementi in modo che le ricerche di risorse non vengono ripetute inutilmente. Il contenitore di visualizzazione dispone anche essere inoltrato all'adapter quando un utente tocca la visualizzazione elemento associato del titolare visualizzazione elemento-fare clic su eventi.

-   **`RecyclerView.LayoutManager`** &ndash; Posiziona gli elementi all'interno di `RecyclerView`. È possibile usare uno dei gestori di layout predefinite diverse, oppure è possibile implementare il proprio gestore di layout personalizzati.
    `RecyclerView` i delegati i criteri di layout per la gestione di layout, quindi è possibile inserire in un gestore di layout diversi senza dover eseguire di significative modifiche all'app.

Inoltre, è facoltativamente possibile estendere le classi seguenti per modificare l'aspetto di `RecyclerView` nell'app:

-   **`RecyclerView.ItemDecoration`**
-   **`RecyclerView.ItemAnimator`**

Se non si estende `ItemDecoration` e `ItemAnimator`, `RecyclerView` utilizza le implementazioni predefinite. Questa Guida non illustra come la creazione personalizzata `ItemDecoration` e `ItemAnimator` classi; per altre informazioni su queste classi, vedere [RecyclerView.ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html) e [RecyclerView.ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).


<a name="recycling" />

### <a name="how-view-recycling-works"></a>Come visualizzare il riciclo Works

`RecyclerView` non alloca un'elemento di visualizzazione per ogni elemento nell'origine dati. Al contrario, alloca solo il numero di viste di elemento che lo schermo e riusa i layout di elementi come l'utente scorre. Quando la visualizzazione prima di tutto scorre non è più visualizzata, passano attraverso il processo di riciclo illustrato nella figura seguente:

[![Diagramma che illustra i sei passaggi del riciclo di visualizzazione](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1.  Quando una visualizzazione scorre non è più visualizzata e non viene più visualizzata, diventa un *scartare visualizzazione*.

2.  La visualizzazione frammento viene inserita in un pool e diventa un *riciclare visualizzazione*.
    Questo pool è una cache di viste che consentono di visualizzare lo stesso tipo di dati.

3.  Quando un nuovo elemento deve essere visualizzato, una vista viene preso dal pool di riciclo per il riutilizzo. Poiché in questa vista deve essere riassociata dall'adapter prima di essere visualizzato, viene chiamato un *dirty visualizzazione*.

4.  La vista dirty viene riciclata: l'adapter consente di individuare i dati per l'elemento successivo deve essere visualizzato e questi dati vengono copiati per le viste per questo elemento. Riferimenti relativi a queste visualizzazioni vengono recuperati dal titolare visualizzazione associato alla visualizzazione riciclata.

5.  La vista riciclata viene aggiunto all'elenco di elementi nel `RecyclerView` che sta tentando di passare sullo schermo.

6.  La vista riciclata va sullo schermo come l'utente scorre il `RecyclerView` all'elemento successivo nell'elenco. Nel frattempo, un'altra visualizzazione scorre non è più visualizzata e viene riciclata seguendo i passaggi precedenti.

Oltre a riutilizzo visualizzazione dell'elemento, `RecyclerView` Usa anche un'altra ottimizzazione dell'efficienza: visualizzare i titolari. Oggetto *titolare della vista* è una classe semplice che memorizza nella cache consente di visualizzare i riferimenti. Ogni volta che l'adapter ingrandisce un file di layout degli elementi, crea anche un contenitore di visualizzazione corrispondente. Il titolare della vista utilizza `FindViewById` per ottenere i riferimenti alle viste all'interno del file di layout elemento maggiori del necessario. Questi riferimenti vengono usati per caricare le visualizzazioni nuovi dati ogni volta che il layout viene riciclato per visualizzare i nuovi dati.
 


### <a name="the-layout-manager"></a>Il gestore di Layout

Layout è responsabile per il posizionamento di elementi nel `RecyclerView` visualizzare; determina il tipo di presentazione (un elenco o una griglia), l'orientamento (se gli elementi vengono visualizzati verticalmente o orizzontalmente) e quali elementi direzione devono essere visualizzati. (in ordine normale o in ordine inverso). È inoltre responsabile per il calcolo della dimensione e posizione di ogni elemento nel gestore del layout le **RecycleView** visualizzare.

La gestione di layout ha uno scopo ulteriore: determina i criteri per i casi riciclare le viste di elemento che non sono più visibili all'utente.
Poiché il gestore di layout è a conoscenza di quali visualizzazioni sono visibili (e quali non), è nella posizione ideale per decidere quando una visualizzazione può essere riciclata. Per riciclare una visualizzazione, il gestore di layout effettua chiamate all'adapter per sostituire il contenuto di una vista riciclato con dati diversi, in genere come descritto in precedenza in [come funziona il riciclo visualizzazione](#recycling).

È possibile estendere `RecyclerView.LayoutManager` per creare un proprio layout manager oppure è possibile utilizzare un gestore di layout predefinito. `RecyclerView` Fornisce i gestori di layout predefinite seguenti:

-   **`LinearLayoutManager`** &ndash; Dispone gli elementi in una colonna che è possibile scorrere in verticale o in una riga che è possibile scorrere orizzontalmente.

-   **`GridLayoutManager`** &ndash; Visualizza gli elementi in una griglia.

-   **`StaggeredGridLayoutManager`** &ndash; Visualizza gli elementi in una griglia fasi successive, in cui alcuni elementi hanno larghezze e altezze diverse.

Per specificare il gestore di layout, creare un'istanza di gestione di layout scelto e passarlo al `SetLayoutManager` (metodo). Si noti che si *devono* specificare la gestione di layout &ndash; `RecyclerView` non seleziona una gestione di layout predefinito per impostazione predefinita.

Per altre informazioni sulla gestione di layout, vedere la [riferimento alla classe RecyclerView.LayoutManager](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html).


### <a name="the-view-holder"></a>Il titolare della visualizzazione

Il titolare della visualizzazione è una classe definita per la memorizzazione nella cache i riferimenti di visualizzazione. L'adapter utilizza i riferimenti di visualizzazione per associare ogni vista in base al contenuto. Tutti gli elementi di `RecyclerView` dispone di un'istanza del titolare della visualizzazione associata che memorizza nella cache i riferimenti di visualizzazione per quell'elemento. Per creare un contenitore di visualizzazione, usare la procedura seguente per definire una classe che contenga il set esatto delle visualizzazioni per ogni elemento:

1.  Subclass `RecyclerView.ViewHolder`.
2.  Implementa un costruttore che cerca e archivia i riferimenti di visualizzazione.
3.  Implementare proprietà che l'adapter può usare per accedere a tali riferimenti.

Un esempio dettagliato di una `ViewHolder` implementazione viene presentata nella [un esempio di RecyclerView base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md).
Per altre informazioni sulle `RecyclerView.ViewHolder`, vedere la [riferimento alla classe RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="the-adapter"></a>L'Adapter

La maggior parte del "impegnativa" del `RecyclerView` integrazione del codice viene eseguita nell'adapter. `RecyclerView` è necessario fornire un adattatore derivato da `RecyclerView.Adapter` per accedere all'origine dati e popolare ogni elemento con contenuto dall'origine dati.
Poiché l'origine dati è specifico dell'app, è necessario implementare funzionalità dell'adapter che siano in grado di accedere ai dati. L'adapter estrae le informazioni dall'origine dati e li carica in ogni elemento di `RecyclerView` raccolta.

Nel disegno seguente viene illustrato come l'adapter esegue il mapping del contenuto in un'origine dati tramite i titolari di visualizzazione per singole viste all'interno di ogni elemento di riga il `RecyclerView`:

[![Diagramma che illustra la connessione origine dati a ViewHolders Adapter](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

L'adapter carica ogni `RecyclerView` riga con i dati per un elemento di riga specifico. Per la posizione della riga *P*, ad esempio, l'adapter consente di individuare i dati associati nella posizione *P* all'interno dell'origine dati e le copie di questi dati per la riga di elemento nella posizione *P* di `RecyclerView` raccolta.
Nel disegno precedente, ad esempio, l'adapter utilizza il contenitore di visualizzazione per cercare i riferimenti per il `ImageView` e `TextView` in tale posizione, in modo non è necessario chiamare ripetutamente `FindViewById` per tali visualizzazioni come l'utente scorre la raccolta e Riutilizza le visualizzazioni.

Quando si implementa un adapter, è necessario eseguire l'override seguente `RecyclerView.Adapter` metodi:

-   **`OnCreateViewHolder`** &ndash; Crea un'istanza titolare di file e Visualizza layout dell'elemento.

-   **`OnBindViewHolder`** &ndash; Carica i dati nella posizione specificata nelle viste a cui i riferimenti vengono archiviati nel contenitore di visualizzazione specificata.

-   **`ItemCount`** &ndash; Restituisce il numero di elementi nell'origine dati.

Gestore del layout chiama questi metodi anche se è posizionamento di elementi all'interno di `RecyclerView`. 



### <a name="notifying-recyclerview-of-data-changes"></a>RecyclerView notifica delle modifiche ai dati

`RecyclerView` non aggiornare automaticamente la relativa visualizzazione quando il contenuto dei dati di origine le modifiche. l'adapter deve inviare una notifica `RecyclerView` quando viene apportata una modifica nel set di dati. Il set di dati è possibile modificare in molti modi; ad esempio possibile modificare il contenuto all'interno di un elemento o la struttura generale dei dati può essere modificata.
`RecyclerView.Adapter` fornisce una serie di metodi che è possibile chiamare in modo che `RecyclerView` risponde alle modifiche dei dati in maniera più efficiente:

-  **`NotifyItemChanged`** &ndash; Segnala che è stato modificato l'elemento in corrispondenza della posizione specificata.

-  **`NotifyItemRangeChanged`** &ndash; Segnala che gli elementi nell'intervallo specificato di posizioni sono state modificate.

-  **`NotifyItemInserted`** &ndash; Segnala che l'elemento nella posizione specificata è stata appena inserita.

-  **`NotifyItemRangeInserted`** &ndash; Segnala che gli elementi nell'intervallo specificato di posizioni di cui sono stati appena inseriti.

-  **`NotifyItemRemoved`** &ndash; Segnala che l'elemento nella posizione specificata è stata rimossa.

-  **`NotifyItemRangeRemoved`** &ndash; Segnala che gli elementi nell'intervallo specificato di posizioni sono state rimosse.

-  **`NotifyDataSetChanged`** &ndash; Segnala che è stato modificato il set di dati (forza un aggiornamento completo).

Se si conosce esattamente come il set di dati è stata modificata, è possibile chiamare i metodi appropriati precedenti per aggiornare `RecyclerView` in maniera più efficiente. Se non si conosce esattamente come il set di dati è stata modificata, è possibile chiamare `NotifyDataSetChanged`, che è molto meno efficiente perché `RecyclerView` necessario Aggiorna tutte le viste che sono visibili all'utente. Per altre informazioni su questi metodi, vedere [RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

Nell'argomento successivo [un esempio di RecyclerView base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md), viene implementata un'app di esempio per illustrare gli esempi di codice reale le parti e le funzionalità descritte sopra.


## <a name="related-links"></a>Collegamenti correlati

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Un esempio di RecyclerView base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [Estendendo l'esempio di RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
