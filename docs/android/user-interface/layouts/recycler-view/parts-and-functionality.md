---
title: Parti e funzionalità di RecyclerView
description: Panoramica di RecyclerView Layout Manager, adapter e View Holder.
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: 89b7f70ae69987edbd465d669f1bac17ddebc7c8
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522452"
---
# <a name="recyclerview-parts-and-functionality"></a>Parti e funzionalità di RecyclerView


`RecyclerView`gestisce alcune attività internamente, ad esempio lo scorrimento e il riciclo delle viste, ma è essenzialmente un responsabile che coordina le classi helper per la visualizzazione di una raccolta. `RecyclerView`delega le attività alle classi helper seguenti:

- **`Adapter`** Consente di ingrandire i layout degli elementi (creare un'istanza del contenuto di un file di layout) e di associare i dati alle visualizzazioni visualizzate all'interno di un oggetto `RecyclerView`. &ndash; L'adapter segnala anche gli eventi di clic sugli elementi.

- **`LayoutManager`** Misura e posiziona le visualizzazioni di elementi `RecyclerView` all'interno di un e gestisce i criteri per il riciclo della visualizzazione. &ndash;

- **`ViewHolder`** &ndash; Cerca e archivia i riferimenti alla visualizzazione. Il titolare della visualizzazione consente inoltre di rilevare i clic per la visualizzazione di elementi.

- **`ItemDecoration`** &ndash; Consente a un'app di aggiungere offset di disegno e layout speciali a visualizzazioni specifiche per il disegno di divisori tra gli elementi, le evidenziazioni e i limiti di raggruppamento visivo.

- **`ItemAnimator`** &ndash; Definisce le animazioni che si verificano durante le azioni dell'elemento o quando vengono apportate modifiche all'adapter.

La relazione tra le `RecyclerView`classi `LayoutManager`, e `Adapter` è illustrata nel diagramma seguente:

![Diagramma di RecyclerView contenente LayoutManager, uso dell'adapter per accedere al set di dati](parts-and-functionality-images/01-recyclerview-diagram.png)

Come illustrato nella figura, il `LayoutManager` può essere considerato come l'intermediario `Adapter` tra e `RecyclerView`. Esegue `LayoutManager` le `Adapter` chiamate ai metodi per conto dell'oggetto `RecyclerView`. Ad esempio, chiama `LayoutManager` un `Adapter` metodo quando è il momento di creare una nuova visualizzazione per una posizione `RecyclerView`particolare dell'elemento in. Consente di ingrandire il layout per l'elemento e di `ViewHolder` creare un'istanza (non visualizzata) per memorizzare nella cache i riferimenti alle viste in tale posizione. `Adapter` `LayoutManager` Quando `Adapter` chiama per associare un particolare elemento al set di dati, il individua i dati per tale elemento, li recupera dal set di dati e li copia nella visualizzazione elemento associato. `Adapter`

Quando si `RecyclerView` USA nell'app, è necessario creare tipi derivati delle classi seguenti:

- **`RecyclerView.Adapter`** Fornisce un'associazione dal set di dati dell'app (specifico dell'app) alle visualizzazioni di elementi visualizzate `RecyclerView`all'interno di. &ndash; L'adapter sa come associare ogni posizione di visualizzazione degli `RecyclerView` elementi in a una posizione specifica nell'origine dati. Inoltre, l'adapter gestisce il layout del contenuto all'interno di ogni singola visualizzazione di elementi e crea il contenitore di visualizzazione per ogni visualizzazione. L'adapter segnala anche gli eventi click-through che vengono rilevati dalla visualizzazione dell'elemento.

- **`RecyclerView.ViewHolder`** &ndash; Memorizza nella cache i riferimenti alle viste nel file di layout dell'elemento in modo che le ricerche di risorse non vengano ripetute inutilmente. Il titolare della visualizzazione dispone inoltre degli eventi di clic dell'elemento da inviare all'adapter quando un utente tocca la visualizzazione elemento associato del contenitore di visualizzazione.

- **`RecyclerView.LayoutManager`** Posiziona gli elementi all' `RecyclerView`interno di. &ndash; È possibile utilizzare uno dei vari gestori di layout predefiniti oppure implementare un gestore di layout personalizzato.
    `RecyclerView`delega i criteri di layout a gestione layout, in modo da poter collegare un diverso gestore di layout senza dover apportare modifiche significative all'app.

Inoltre, è possibile estendere facoltativamente le classi seguenti per modificare l'aspetto di `RecyclerView` nell'app:

- **`RecyclerView.ItemDecoration`**
- **`RecyclerView.ItemAnimator`**

Se non si estende `ItemDecoration` e, `ItemAnimator` `RecyclerView` usa le implementazioni predefinite. In questa guida non viene illustrato come creare classi `ItemDecoration` e `ItemAnimator` personalizzate. per ulteriori informazioni su queste classi, vedere [RecyclerView. ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html) e [RecyclerView. ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).


<a name="recycling" />

### <a name="how-view-recycling-works"></a>Funzionamento del riciclo della visualizzazione

`RecyclerView`non alloca una visualizzazione elemento per ogni elemento nell'origine dati. Al contrario, alloca solo il numero di visualizzazioni di elementi che si adattano allo schermo e riutilizza tali layout degli elementi quando l'utente scorre. Quando la vista scorre per la prima volta, viene eseguito il processo di riciclo illustrato nella figura seguente:

[![Diagramma che illustra i sei passaggi per il riciclo delle visualizzazioni](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1. Quando una visualizzazione scorre fuori vista e non è più visualizzata, diventa una *visualizzazione di scarto*.

2. La visualizzazione dei frammenti viene posizionata in un pool e diventa una *vista*di riciclo.
    Questo pool è una cache di visualizzazioni che visualizza lo stesso tipo di dati.

3. Quando viene visualizzato un nuovo elemento, una vista viene ricavata dal pool di riciclo per il riutilizzo. Poiché questa visualizzazione deve essere riassociata dall'adapter prima di essere visualizzata, viene definita *visualizzazione Dirty*.

4. La visualizzazione Dirty viene riciclata: l'adapter individua i dati per l'elemento successivo da visualizzare e copia questi dati nelle visualizzazioni per questo elemento. I riferimenti per queste viste vengono recuperati dal titolare della visualizzazione associato alla visualizzazione riciclata.

5. La vista riciclata viene aggiunta all'elenco di elementi in `RecyclerView` che stanno per essere visualizzati sullo schermo.

6. La visualizzazione riciclata viene visualizzata sullo schermo quando l'utente scorre l' `RecyclerView` oggetto all'elemento successivo nell'elenco. Nel frattempo, un'altra visualizzazione scorre fuori dalla vista e viene riciclata in base ai passaggi precedenti.

Oltre al riutilizzo di visualizzazione elementi, `RecyclerView` usa anche un'altra ottimizzazione dell'efficienza: Visualizza i titolari. Un *contenitore di visualizzazione* è una semplice classe che memorizza nella cache i riferimenti di visualizzazione. Ogni volta che l'adapter gonfia un file di layout elemento, crea anche un contenitore di visualizzazione corrispondente. Il titolare della visualizzazione `FindViewById` USA per ottenere i riferimenti alle viste all'interno del file di layout degli elementi inflat. Questi riferimenti vengono utilizzati per caricare nuovi dati nelle visualizzazioni ogni volta che il layout viene riciclato per visualizzare nuovi dati.
 


### <a name="the-layout-manager"></a>Gestione layout

Il gestore del layout è responsabile del posizionamento degli elementi `RecyclerView` nella visualizzazione. determina il tipo di presentazione (un elenco o una griglia), l'orientamento (se gli elementi vengono visualizzati verticalmente o orizzontalmente) e gli elementi della direzione da visualizzare (in ordine normale o in ordine inverso). Il gestore del layout è inoltre responsabile del calcolo delle dimensioni e della posizione di ogni elemento nella visualizzazione **RecycleView** .

Il gestore del layout ha uno scopo aggiuntivo: determina i criteri per il momento in cui riciclare le visualizzazioni di elementi che non sono più visibili all'utente.
Poiché il gestore del layout è in grado di riconoscere quali visualizzazioni sono visibili (e quali non sono), si trova nella posizione migliore per decidere quando una vista può essere riciclata. Per riciclare una visualizzazione, il gestore del layout esegue in genere chiamate all'adapter per sostituire il contenuto di una visualizzazione riciclata con dati diversi, come descritto in precedenza in [come funziona](#recycling)il riciclo della visualizzazione.

È possibile estendere `RecyclerView.LayoutManager` per creare il proprio gestore di layout oppure è possibile usare un gestore di layout predefinito. `RecyclerView`in sono disponibili i gestori di layout predefiniti seguenti:

- **`LinearLayoutManager`** &ndash; Dispone gli elementi di una colonna di cui è possibile scorrere verticalmente o in una riga che può essere spostata orizzontalmente.

- **`GridLayoutManager`** &ndash; Visualizza gli elementi in una griglia.

- **`StaggeredGridLayoutManager`** &ndash; Visualizza gli elementi in una griglia sfalsata, in cui alcuni elementi hanno altezze e larghezze diverse.

Per specificare il gestore del layout, creare un'istanza del gestore del layout scelto e passarlo al `SetLayoutManager` metodo. Si noti che per impostazione predefinita, è &ndash; necessario specificare che gestione `RecyclerView` layout non selezioni un gestore di layout predefinito.

Per ulteriori informazioni su Gestione layout, vedere il [riferimento alla classe RecyclerView. LayoutManager](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html).


### <a name="the-view-holder"></a>Il titolare della visualizzazione

Il titolare della visualizzazione è una classe definita per la memorizzazione nella cache dei riferimenti di visualizzazione. L'adapter utilizza questi riferimenti di visualizzazione per associare ogni visualizzazione al relativo contenuto. Ogni elemento in `RecyclerView` dispone di un'istanza del contenitore di visualizzazione associata che memorizza nella cache i riferimenti alla visualizzazione per l'elemento. Per creare un contenitore di visualizzazione, attenersi alla procedura seguente per definire una classe che contenga il set esatto di visualizzazioni per ogni elemento:

1. Sottoclasse `RecyclerView.ViewHolder`.
2. Implementare un costruttore che cerca e archivia i riferimenti alla visualizzazione.
3. Implementare le proprietà che l'adapter può utilizzare per accedere a questi riferimenti.

Un esempio dettagliato di `ViewHolder` implementazione viene presentato in [un esempio di RecyclerView di base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md).
Per ulteriori informazioni su `RecyclerView.ViewHolder`, vedere il [riferimento alla classe RecyclerView. ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="the-adapter"></a>Adapter

La maggior parte delle operazioni di "sollevamento intensivo `RecyclerView` " del codice di integrazione si verifica nell'adapter. `RecyclerView`richiede la fornitura di un adapter derivato da `RecyclerView.Adapter` per accedere all'origine dati e popolare ogni elemento con contenuto dall'origine dati.
Poiché l'origine dati è specifica dell'app, è necessario implementare la funzionalità dell'adapter per comprendere come accedere ai dati. L'adapter estrae le informazioni dall'origine dati e le carica in ogni elemento `RecyclerView` della raccolta.

Nel disegno seguente viene illustrato il modo in cui l'adapter esegue il mapping del contenuto in un'origine dati tramite i titolari di visualizzazione a `RecyclerView`singole visualizzazioni all'interno di ogni elemento riga in:

[![Diagramma che illustra l'origine dati per la connessione dell'adapter a ViewHolders](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

L'adapter carica ogni `RecyclerView` riga con i dati di un particolare elemento di riga. Per la posizione della riga *p*, ad esempio, l'adapter individua i dati associati nella posizione *p* all'interno dell'origine dati e copia questi dati nell'elemento riga nella `RecyclerView` posizione p della raccolta.
Nel disegno precedente, ad esempio, l'adapter usa il titolare della visualizzazione per cercare i riferimenti per `ImageView` e `TextView` in tale posizione, in modo che non debba chiamare `FindViewById` ripetutamente per tali viste quando l'utente scorre la raccolta e Riutilizza le visualizzazioni.

Quando si implementa un adapter, è necessario eseguire l'override `RecyclerView.Adapter` dei metodi seguenti:

- **`OnCreateViewHolder`** &ndash; Crea un'istanza del file di layout dell'elemento e del titolare della visualizzazione.

- **`OnBindViewHolder`** &ndash; Carica i dati nella posizione specificata nelle viste i cui riferimenti sono archiviati nel contenitore di visualizzazione specificato.

- **`ItemCount`** &ndash; Restituisce il numero di elementi nell'origine dati.

Il gestore del layout chiama questi metodi mentre posiziona gli elementi all'interno `RecyclerView`di. 



### <a name="notifying-recyclerview-of-data-changes"></a>Notifica di RecyclerView delle modifiche dei dati

`RecyclerView`non aggiorna automaticamente la visualizzazione quando il contenuto dell'origine dati cambia; l'adapter deve inviare `RecyclerView` una notifica quando viene apportata una modifica al set di dati. Il set di dati può cambiare in molti modi; ad esempio, il contenuto di un elemento può cambiare o la struttura complessiva dei dati può essere modificata.
`RecyclerView.Adapter`in sono disponibili diversi metodi che è possibile chiamare in modo `RecyclerView` che risponda alle modifiche dei dati nel modo più efficiente:

- **`NotifyItemChanged`** &ndash; Segnala che l'elemento in corrispondenza della posizione specificata è stato modificato.

- **`NotifyItemRangeChanged`** &ndash; Segnala che gli elementi nell'intervallo di posizioni specificato sono stati modificati.

- **`NotifyItemInserted`** &ndash; Segnala che l'elemento nella posizione specificata è stato appena inserito.

- **`NotifyItemRangeInserted`** &ndash; Segnala che gli elementi nell'intervallo di posizioni specificato sono stati appena inseriti.

- **`NotifyItemRemoved`** &ndash; Segnala che l'elemento nella posizione specificata è stato rimosso.

- **`NotifyItemRangeRemoved`** &ndash; Segnala che gli elementi nell'intervallo di posizioni specificato sono stati rimossi.

- **`NotifyDataSetChanged`** &ndash; Segnala che il set di dati è stato modificato (impone un aggiornamento completo).

Se si conosce esattamente il modo in cui il set di dati è stato modificato, è possibile chiamare i `RecyclerView` metodi appropriati sopra per eseguire l'aggiornamento nel modo più efficiente. Se non si conosce esattamente il modo in cui il set di dati è stato modificato `NotifyDataSetChanged`, è possibile chiamare, che è `RecyclerView` molto meno efficiente perché deve aggiornare tutte le visualizzazioni visibili all'utente. Per ulteriori informazioni su questi metodi, vedere [RecyclerView. Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

Nell'argomento successivo, [un esempio di RecyclerView di base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md), viene implementata un'app di esempio per illustrare esempi di codice reali delle parti e delle funzionalità descritte in precedenza.


## <a name="related-links"></a>Collegamenti correlati

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Esempio di RecyclerView di base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [Estensione dell'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
