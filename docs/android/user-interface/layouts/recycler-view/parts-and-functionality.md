---
title: Parti e funzionalità di RecyclerView
description: Panoramica di RecyclerView Layout Manager, adapter e View Holder.
ms.prod: xamarin
ms.assetid: 54F999BE-2732-4BC7-A466-D17373961C48
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: 823215b7cc1bac8a8f6bffc6e480400135e88ce8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028827"
---
# <a name="recyclerview-parts-and-functionality"></a>Parti e funzionalità di RecyclerView

`RecyclerView` gestisce alcune attività internamente, ad esempio lo scorrimento e il riciclo delle viste, ma è essenzialmente un responsabile che coordina le classi helper per la visualizzazione di una raccolta. `RecyclerView` delega le attività alle classi helper seguenti:

- **`Adapter`** &ndash; consente di ingrandire i layout degli elementi (creare un'istanza del contenuto di un file di layout) e di associare i dati alle visualizzazioni visualizzate all'interno di un `RecyclerView`. L'adapter segnala anche gli eventi di clic sugli elementi.

- **`LayoutManager`** &ndash; le visualizzazioni degli elementi di misure e posizioni all'interno di un `RecyclerView` e gestisce i criteri per il riciclo della visualizzazione.

- **`ViewHolder`** &ndash; Cerca e archivia i riferimenti alla visualizzazione. Il titolare della visualizzazione consente inoltre di rilevare i clic per la visualizzazione di elementi.

- **`ItemDecoration`** &ndash; consente a un'app di aggiungere offset di disegno e layout speciali a visualizzazioni specifiche per il disegno di divisori tra elementi, evidenziazioni e limiti di raggruppamento visivi.

- **`ItemAnimator`** &ndash; definisce le animazioni che si verificano durante le azioni degli elementi o quando vengono apportate modifiche all'adapter.

La relazione tra le classi `RecyclerView`, `LayoutManager`e `Adapter` è illustrata nel diagramma seguente:

![Diagramma di RecyclerView contenente LayoutManager, uso dell'adapter per accedere al set di dati](parts-and-functionality-images/01-recyclerview-diagram.png)

Come illustrato nella figura, il `LayoutManager` può essere considerato l'intermediario tra l'`Adapter` e il `RecyclerView`. Il `LayoutManager` effettua chiamate a metodi `Adapter` per conto del `RecyclerView`. Ad esempio, il `LayoutManager` chiama un metodo di `Adapter` quando è il momento di creare una nuova visualizzazione per una particolare posizione dell'elemento nel `RecyclerView`. Il `Adapter` ingrandisce il layout per tale elemento e crea un'istanza di `ViewHolder` (non mostrata) per memorizzare nella cache i riferimenti alle viste in tale posizione. Quando il `LayoutManager` chiama il `Adapter` per associare un particolare elemento al set di dati, il `Adapter` individua i dati per tale elemento, li recupera dal set di dati e li copia nella visualizzazione elemento associato.

Quando si usa `RecyclerView` nell'app, è necessario creare tipi derivati delle classi seguenti:

- **`RecyclerView.Adapter`** &ndash; fornisce un'associazione dal set di dati dell'app (specifico dell'app) alle visualizzazioni di elementi visualizzate all'interno del `RecyclerView`. L'adapter sa come associare ogni posizione di visualizzazione degli elementi nel `RecyclerView` a una posizione specifica nell'origine dati. Inoltre, l'adapter gestisce il layout del contenuto all'interno di ogni singola visualizzazione di elementi e crea il contenitore di visualizzazione per ogni visualizzazione. L'adapter segnala anche gli eventi click-through che vengono rilevati dalla visualizzazione dell'elemento.

- **`RecyclerView.ViewHolder`** &ndash; memorizza nella cache i riferimenti alle viste nel file di layout dell'elemento in modo che le ricerche di risorse non vengano ripetute inutilmente. Il titolare della visualizzazione dispone inoltre degli eventi di clic dell'elemento da inviare all'adapter quando un utente tocca la visualizzazione elemento associato del contenitore di visualizzazione.

- **`RecyclerView.LayoutManager`** &ndash; posiziona gli elementi all'interno della `RecyclerView`. È possibile utilizzare uno dei vari gestori di layout predefiniti oppure implementare un gestore di layout personalizzato.
    `RecyclerView` delega i criteri di layout a gestione layout, in modo da poter collegare un diverso gestore di layout senza dover apportare modifiche significative all'app.

Inoltre, è possibile estendere facoltativamente le classi seguenti per modificare l'aspetto di `RecyclerView` nell'app:

- **`RecyclerView.ItemDecoration`**
- **`RecyclerView.ItemAnimator`**

Se non si estendono `ItemDecoration` e `ItemAnimator`, `RecyclerView` utilizza le implementazioni predefinite. In questa guida non viene illustrato come creare classi di `ItemDecoration` e `ItemAnimator` personalizzate; Per ulteriori informazioni su queste classi, vedere [RecyclerView. ItemDecoration](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemDecoration.html) e [RecyclerView. ItemAnimator](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).

<a name="recycling" />

## <a name="how-view-recycling-works"></a>Funzionamento del riciclo della visualizzazione

`RecyclerView` non alloca una visualizzazione elemento per ogni elemento nell'origine dati. Al contrario, alloca solo il numero di visualizzazioni di elementi che si adattano allo schermo e riutilizza tali layout degli elementi quando l'utente scorre. Quando la vista scorre per la prima volta, viene eseguito il processo di riciclo illustrato nella figura seguente:

[![diagramma che illustra i sei passaggi per il riciclo delle visualizzazioni](parts-and-functionality-images/02-view-recycling-sml.png)](parts-and-functionality-images/02-view-recycling.png#lightbox)

1. Quando una visualizzazione scorre fuori vista e non è più visualizzata, diventa una *visualizzazione di scarto*.

2. La visualizzazione dei frammenti viene posizionata in un pool e diventa una *vista di riciclo*.
    Questo pool è una cache di visualizzazioni che visualizza lo stesso tipo di dati.

3. Quando viene visualizzato un nuovo elemento, una vista viene ricavata dal pool di riciclo per il riutilizzo. Poiché questa visualizzazione deve essere riassociata dall'adapter prima di essere visualizzata, viene definita *visualizzazione Dirty*.

4. La visualizzazione Dirty viene riciclata: l'adapter individua i dati per l'elemento successivo da visualizzare e copia questi dati nelle visualizzazioni per questo elemento. I riferimenti per queste viste vengono recuperati dal titolare della visualizzazione associato alla visualizzazione riciclata.

5. La vista riciclata viene aggiunta all'elenco di elementi nella `RecyclerView` che stanno per essere visualizzati sullo schermo.

6. La visualizzazione riciclata viene visualizzata sullo schermo quando l'utente scorre il `RecyclerView` all'elemento successivo nell'elenco. Nel frattempo, un'altra visualizzazione scorre fuori dalla vista e viene riciclata in base ai passaggi precedenti.

Oltre al riutilizzo della visualizzazione elementi, `RecyclerView` usa anche un'altra ottimizzazione dell'efficienza: Visualizza i titolari. Un *contenitore di visualizzazione* è una semplice classe che memorizza nella cache i riferimenti di visualizzazione. Ogni volta che l'adapter gonfia un file di layout elemento, crea anche un contenitore di visualizzazione corrispondente. Il titolare della visualizzazione USA `FindViewById` per ottenere i riferimenti alle viste all'interno del file di layout degli elementi inflat. Questi riferimenti vengono utilizzati per caricare nuovi dati nelle visualizzazioni ogni volta che il layout viene riciclato per visualizzare nuovi dati.

## <a name="the-layout-manager"></a>Gestione layout

Il gestore del layout è responsabile del posizionamento degli elementi nella visualizzazione `RecyclerView`; determina il tipo di presentazione (un elenco o una griglia), l'orientamento (se gli elementi vengono visualizzati verticalmente o orizzontalmente) e gli elementi della direzione da visualizzare (in ordine normale o in ordine inverso). Il gestore del layout è inoltre responsabile del calcolo delle dimensioni e della posizione di ogni elemento nella visualizzazione **RecycleView** .

Il gestore del layout ha uno scopo aggiuntivo: determina i criteri per il momento in cui riciclare le visualizzazioni di elementi che non sono più visibili all'utente.
Poiché il gestore del layout è in grado di riconoscere quali visualizzazioni sono visibili (e quali non sono), si trova nella posizione migliore per decidere quando una vista può essere riciclata. Per riciclare una visualizzazione, il gestore del layout esegue in genere chiamate all'adapter per sostituire il contenuto di una visualizzazione riciclata con dati diversi, come descritto in precedenza in [come funziona il riciclo della visualizzazione](#recycling).

È possibile estendere `RecyclerView.LayoutManager` per creare un proprio gestore di layout oppure è possibile usare un gestore di layout predefinito. `RecyclerView` offre i seguenti gestori di layout predefiniti:

- **`LinearLayoutManager`** &ndash; dispone gli elementi in una colonna di cui è possibile scorrere verticalmente o in una riga che può essere spostata orizzontalmente.

- **`GridLayoutManager`** &ndash; Visualizza gli elementi in una griglia.

- **`StaggeredGridLayoutManager`** &ndash; Visualizza gli elementi in una griglia sfalsata, in cui alcuni elementi hanno altezze e larghezze diverse.

Per specificare il gestore del layout, creare un'istanza del gestore del layout scelto e passarlo al metodo `SetLayoutManager`. Si noti che è *necessario* specificare il gestore del layout &ndash; `RecyclerView` non seleziona un gestore di layout predefinito per impostazione predefinita.

Per ulteriori informazioni su Gestione layout, vedere il [riferimento alla classe RecyclerView. LayoutManager](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html).

## <a name="the-view-holder"></a>Il titolare della visualizzazione

Il titolare della visualizzazione è una classe definita per la memorizzazione nella cache dei riferimenti di visualizzazione. L'adapter utilizza questi riferimenti di visualizzazione per associare ogni visualizzazione al relativo contenuto. Ogni elemento nel `RecyclerView` dispone di un'istanza del contenitore di visualizzazione associata che memorizza nella cache i riferimenti alla visualizzazione per l'elemento. Per creare un contenitore di visualizzazione, attenersi alla procedura seguente per definire una classe che contenga il set esatto di visualizzazioni per ogni elemento:

1. Sottoclasse `RecyclerView.ViewHolder`.
2. Implementare un costruttore che cerca e archivia i riferimenti alla visualizzazione.
3. Implementare le proprietà che l'adapter può utilizzare per accedere a questi riferimenti.

Un esempio dettagliato di un'implementazione di `ViewHolder` viene presentato in [un esempio di RecyclerView di base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md).
Per ulteriori informazioni su `RecyclerView.ViewHolder`, vedere la pagina relativa al [riferimento alla classe RecyclerView. ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).

## <a name="the-adapter"></a>Adapter

La maggior parte delle operazioni di "sollevamento intensivo" del codice di integrazione `RecyclerView` avviene nell'adapter. `RecyclerView` richiede la fornitura di un adapter derivato da `RecyclerView.Adapter` per accedere all'origine dati e popolare ogni elemento con contenuto dall'origine dati.
Poiché l'origine dati è specifica dell'app, è necessario implementare la funzionalità dell'adapter per comprendere come accedere ai dati. L'adapter estrae le informazioni dall'origine dati e le carica in ogni elemento della raccolta `RecyclerView`.

Nel disegno seguente viene illustrato il modo in cui l'adapter esegue il mapping del contenuto in un'origine dati tramite i titolari di visualizzazione a singole visualizzazioni all'interno di ogni elemento riga della `RecyclerView`:

[Diagramma![che illustra l'origine dati per la connessione dell'adapter a ViewHolders](parts-and-functionality-images/03-recyclerviewer-adapter-sml.png)](parts-and-functionality-images/03-recyclerviewer-adapter.png#lightbox)

L'adapter carica ogni riga di `RecyclerView` con i dati di un particolare elemento di riga. Per la posizione della riga *p*, ad esempio, l'adapter individua i dati associati nella posizione *p* all'interno dell'origine dati e copia questi dati nell'elemento riga nella posizione *P* nella raccolta di `RecyclerView`.
Nel disegno precedente, ad esempio, l'adapter usa il titolare della visualizzazione per cercare i riferimenti per il `ImageView` e `TextView` in tale posizione, in modo che non debba chiamare ripetutamente `FindViewById` per tali viste quando l'utente scorre la raccolta e riutilizza le visualizzazioni.

Quando si implementa un adapter, è necessario eseguire l'override dei seguenti metodi di `RecyclerView.Adapter`:

- **`OnCreateViewHolder`** &ndash; crea un'istanza del file di layout dell'elemento e del titolare della visualizzazione.

- **`OnBindViewHolder`** &ndash; carica i dati nella posizione specificata nelle viste i cui riferimenti sono archiviati nel contenitore di visualizzazione specificato.

- **`ItemCount`** &ndash; restituisce il numero di elementi presenti nell'origine dati.

Il gestore del layout chiama questi metodi mentre posiziona gli elementi all'interno del `RecyclerView`.

## <a name="notifying-recyclerview-of-data-changes"></a>Notifica di RecyclerView delle modifiche dei dati

`RecyclerView` non aggiorna automaticamente la visualizzazione quando il contenuto dell'origine dati cambia; l'adapter deve notificare `RecyclerView` quando viene apportata una modifica al set di dati. Il set di dati può cambiare in molti modi; ad esempio, il contenuto di un elemento può cambiare o la struttura complessiva dei dati può essere modificata.
`RecyclerView.Adapter` fornisce diversi metodi che è possibile chiamare in modo che `RecyclerView` risponda alle modifiche dei dati nel modo più efficiente:

- **`NotifyItemChanged`** &ndash; segnala che l'elemento in corrispondenza della posizione specificata è stato modificato.

- **`NotifyItemRangeChanged`** &ndash; segnala che gli elementi nell'intervallo di posizioni specificato sono stati modificati.

- **`NotifyItemInserted`** &ndash; segnala che l'elemento nella posizione specificata è stato appena inserito.

- **`NotifyItemRangeInserted`** &ndash; segnala che gli elementi nell'intervallo di posizioni specificato sono stati appena inseriti.

- **`NotifyItemRemoved`** &ndash; segnala che l'elemento nella posizione specificata è stato rimosso.

- **`NotifyItemRangeRemoved`** &ndash; segnala che gli elementi nell'intervallo di posizioni specificato sono stati rimossi.

- **`NotifyDataSetChanged`** &ndash; segnala che il set di dati è stato modificato (impone un aggiornamento completo).

Se si conosce esattamente il modo in cui il set di dati è stato modificato, è possibile chiamare i metodi appropriati sopra per aggiornare `RecyclerView` nel modo più efficiente. Se non si conosce esattamente il modo in cui il set di dati è stato modificato, è possibile chiamare `NotifyDataSetChanged`, che risulta molto meno efficiente perché `RecyclerView` necessario aggiornare tutte le visualizzazioni visibili all'utente. Per ulteriori informazioni su questi metodi, vedere [RecyclerView. Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

Nell'argomento successivo, [un esempio di RecyclerView di base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md), viene implementata un'app di esempio per illustrare esempi di codice reali delle parti e delle funzionalità descritte in precedenza.

## <a name="related-links"></a>Collegamenti correlati

- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Esempio di RecyclerView di base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [Estensione dell'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
