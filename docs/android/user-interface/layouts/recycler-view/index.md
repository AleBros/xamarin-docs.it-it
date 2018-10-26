---
title: RecyclerView
description: RecyclerView è un gruppo di visualizzazione per visualizzare gli insiemi. è progettato per essere una sostituzione più flessibile per i gruppi di visualizzazione precedente, ad esempio ListView e GridView.  Questa guida illustra come usare e personalizzare RecyclerView nelle applicazioni xamarin. Android.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/03/2018
ms.openlocfilehash: 1332a7ef5b8e5bb2f1178582bcf058123f1e177c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110147"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView è un gruppo di visualizzazione per visualizzare gli insiemi. è progettato per essere una sostituzione più flessibile per i gruppi di visualizzazione precedente, ad esempio ListView e GridView.  Questa guida illustra come usare e personalizzare RecyclerView nelle applicazioni xamarin. Android._

## <a name="recyclerview"></a>RecyclerView

Molte App necessario visualizzare le raccolte dello stesso tipo (ad esempio, i messaggi, contatti, immagini o brani); spesso, questa raccolta è troppo grande per sullo schermo, in modo che la raccolta viene visualizzata in una finestra piccola di scorrere in modo uniforme tra tutti gli elementi nella raccolta.
`RecyclerView` è un widget di Android che visualizza una raccolta di elementi in un elenco o una griglia, consentendo all'utente di scorrere la raccolta. Di seguito è riportato uno screenshot di un'app di esempio che usa `RecyclerView` per visualizzare messaggio di posta elettronica nella posta in arrivo contenuto in un elenco di scorrimento verticale:

[![App di esempio usando RecyclerView da elenco dei messaggi di posta in arrivo](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` offre due funzionalità interessanti:

-  Ha un'architettura flessibile che consente di modificare il comportamento inserendo in componenti Preferiti.

-  È efficace con le raccolte di grandi dimensioni perché Riutilizza le viste di elemento e richiede l'uso di *visualizzare i titolari* Visualizza i riferimenti.

Questa guida illustra come usare `RecyclerView` nelle applicazioni xamarin. Android; viene spiegato come aggiungere i `RecyclerView` pacchetto per il progetto xamarin. Android che descrive la modalità `RecyclerView` funzioni in un'applicazione tipica. Descrive come integrare vengono forniti esempi di codice reali `RecyclerView` nell'applicazione, come implementare fare clic su visualizzazione elemento e come aggiornare `RecyclerView` quando viene modificato i dati sottostanti. Questa guida si presuppone che si abbia familiarità con lo sviluppo di xamarin. Android.


### <a name="requirements"></a>Requisiti

Sebbene `RecyclerView` è spesso associato a Android 5.0 Lollipop, viene offerto come libreria di supporto &ndash; `RecyclerView` funziona con le app a tale livello API di destinazione (Android 2.1) 7 e versioni successive. È necessario quanto segue per usare `RecyclerView` nelle applicazioni basate su Xamarin:

-  **Xamarin. Android** &ndash; xamarin. Android 4.20 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

-  Il progetto dell'app deve includere il **Xamarin.Android.Support.v7.RecyclerView** pacchetto. Per altre informazioni sull'installazione di pacchetti NuGet, vedere [procedura dettagliata: inclusi a NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


### <a name="overview"></a>Panoramica

`RecyclerView` può essere considerato una sostituzione per il `ListView` e `GridView` widget in Android. Le versioni precedenti, come `RecyclerView` è progettato per visualizzare grandi set di dati in una finestra piccola, ma `RecyclerView` offre più opzioni di layout e meglio è ottimizzato per la visualizzazione delle raccolte di grandi dimensioni. Se si ha familiarità con `ListView`, esistono alcune differenze importanti tra `ListView` e `RecyclerView`:

-   `RecyclerView` è leggermente più complessa da usare: è necessario scrivere altro codice per usare `RecyclerView` rispetto a `ListView`.

-   `RecyclerView` non è incluso un adapter predefiniti; è necessario implementare il codice dell'adattatore che accede all'origine dati. Tuttavia, Android include diversi adapter predefiniti che funzionano con `ListView` e `GridView`.

-   `RecyclerView` non offre un evento di selezione elemento quando un utente tocca un elemento. invece degli eventi clic elemento vengono gestiti dalle classi helper. Al contrario, `ListView` offre un evento di elemento-fare clic su.

-   `RecyclerView` migliora le prestazioni tramite le viste di riciclo e applicando il modello di contenitore di visualizzazione, che elimina le ricerche di risorse di layout non necessari. Utilizzo del modello view-titolare è facoltativo in `ListView`.

-   `RecyclerView` si basa su una progettazione modulare che rende più facile da personalizzare. Ad esempio, è possibile collegare un criterio di un layout diverso senza modifiche significative al codice all'app.
    Al contrario, `ListView` è relativamente monolitica nella struttura.

-   `RecyclerView` include animazioni incorporate per elemento, aggiungere e rimuovere. `ListView` le animazioni richiedono alcuni ulteriori interventi da parte dello sviluppatore di app.


### <a name="sections"></a>Sezioni

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[Funzionalità e le parti di RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

Questo argomento viene illustrato come la `Adapter`, `LayoutManager`, e `ViewHolder` interagiscono come classi di helper per supportare `RecyclerView`.
Fornisce una panoramica generale di ognuna di queste classi helper e viene spiegato come si usarli nell'app.

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[Un esempio di RecyclerView base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

Questo argomento si basa sulle informazioni fornite [RecyclerView parti e funzionalità](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) fornendo esempi di codice reale di come i vari `RecyclerView` gli elementi vengono implementati per creare un'app di consultazione delle foto reale.

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[Estendendo l'esempio di RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

In questo argomento aggiunge codice aggiuntivo per l'app di esempio presentato in [un esempio di RecyclerView base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) per illustrare come gestire gli eventi clic elemento e aggiornare `RecyclerView` quando le modifiche apportate all'origine dati sottostante.


### <a name="summary"></a>Riepilogo

Questa guida ha presentato il Android `RecyclerView` widget; spiega come aggiungere i `RecyclerView` supportano come libreria per progetti xamarin. Android, `RecyclerView` Ricicla le visualizzazioni, come viene utilizzato per applicare il modello di contenitore di visualizzazione per una maggiore efficienza e come i vari le classi helper che costituiscono `RecyclerView` collaborano per visualizzare le raccolte. Fornito codice di esempio per illustrare la modalità `RecyclerView` è integrato in un'applicazione, spiega come adattare `RecyclerView`del criterio layout collegando i gestori di layout diversi che descritto come gestire elementi fare clic su eventi e invia una notifica `RecyclerView`di origine dati vengono modificati.

Per altre informazioni sulle `RecyclerView`, vedere la [riferimento alla classe RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).


## <a name="related-links"></a>Collegamenti correlati

- [RecyclerViewer (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Introduzione al simbolo](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
