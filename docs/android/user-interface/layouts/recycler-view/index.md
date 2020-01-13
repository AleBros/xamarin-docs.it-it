---
title: RecyclerView
description: RecyclerView è un gruppo di visualizzazione per la visualizzazione delle raccolte. è progettato per essere una sostituzione più flessibile per i gruppi di viste precedenti, ad esempio ListView e GridView.  Questa guida illustra come usare e personalizzare RecyclerView in applicazioni Xamarin.Android.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/03/2018
ms.openlocfilehash: bce89be8bec512ac70ca40015521c7d56f3460d3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028817"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView è un gruppo di visualizzazione per la visualizzazione delle raccolte. è progettato per essere una sostituzione più flessibile per i gruppi di viste precedenti, ad esempio ListView e GridView.  Questa guida illustra come usare e personalizzare RecyclerView in applicazioni Xamarin.Android._

## <a name="recyclerview"></a>RecyclerView

Molte app devono visualizzare le raccolte dello stesso tipo (ad esempio messaggi, contatti, immagini o canzoni); spesso, questa raccolta è troppo grande per essere adattata allo schermo, quindi la raccolta viene visualizzata in una piccola finestra che consente di scorrere agevolmente tutti gli elementi della raccolta.
`RecyclerView` è un widget Android che visualizza una raccolta di elementi in un elenco o in una griglia, consentendo all'utente di scorrere la raccolta. Di seguito è riportata una schermata di un'app di esempio che usa `RecyclerView` per visualizzare il contenuto della posta in arrivo in un elenco a scorrimento verticale:

[app di esempio![uso di RecyclerView per elencare i messaggi della posta in arrivo](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` offre due funzionalità interessanti:

- Dispone di un'architettura flessibile che consente di modificarne il comportamento tramite l'inserimento di componenti preferiti.

- È efficiente con le raccolte di grandi dimensioni perché riutilizza le visualizzazioni di elementi e richiede l'uso di *titolari di visualizzazione* per memorizzare nella cache i riferimenti alla visualizzazione.

Questa guida illustra come usare `RecyclerView` nelle applicazioni Xamarin.Android; viene illustrato come aggiungere il pacchetto di `RecyclerView` al progetto Xamarin.Android e viene descritto come `RecyclerView` funzioni in un'applicazione tipica. Sono disponibili esempi di codice reali per illustrare come integrare `RecyclerView` nell'applicazione, come implementare la visualizzazione di elementi e come aggiornare `RecyclerView` quando cambiano i dati sottostanti. Questa guida presuppone che l'utente abbia familiarità con lo sviluppo di Xamarin.Android.

### <a name="requirements"></a>Requisiti

Anche se `RecyclerView` è spesso associato a un Lollipop Android 5,0, viene offerto come libreria di supporto &ndash; `RecyclerView` funziona con le app che hanno come destinazione API Level 7 (Android 2,1) e versioni successive. Per usare `RecyclerView` nelle applicazioni basate su Novell, è necessario quanto segue:

- **Xamarin.android** &ndash; Xamarin.Android 4,20 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

- Il progetto dell'app deve includere il pacchetto **Xamarin.Android. support. V7. RecyclerView** . Per altre informazioni sull'installazione di pacchetti NuGet, vedere [procedura dettagliata: inclusione di NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

### <a name="overview"></a>Panoramica

`RecyclerView` possono essere considerati sostitutivi per i widget `ListView` e `GridView` in Android. Analogamente ai predecessori, `RecyclerView` è progettato per visualizzare un set di dati di grandi dimensioni in una piccola finestra, ma `RecyclerView` offre più opzioni di layout ed è ottimizzato per la visualizzazione di raccolte di grandi dimensioni. Se si ha familiarità con `ListView`, esistono alcune differenze importanti tra `ListView` e `RecyclerView`:

- `RecyclerView` è leggermente più complesso da usare: è necessario scrivere altro codice per usare `RecyclerView` rispetto a `ListView`.

- `RecyclerView` non fornisce un adapter predefinito; è necessario implementare il codice dell'adattatore che accede all'origine dati. Tuttavia, Android include diversi adapter predefiniti che funzionano con `ListView` e `GridView`.

- `RecyclerView` non offre un evento di clic dell'elemento quando un utente tocca un elemento; gli eventi di clic sugli elementi vengono invece gestiti da classi helper. Al contrario, `ListView` offre un evento di clic sull'elemento.

- `RecyclerView` migliora le prestazioni tramite il riciclo delle visualizzazioni e applicando il modello di supporto di visualizzazione, che elimina le ricerche non necessarie sulle risorse di layout. L'uso del modello del supporto di visualizzazione è facoltativo in `ListView`.

- `RecyclerView` si basa su una progettazione modulare che ne semplifica la personalizzazione. Ad esempio, è possibile inserire un criterio di layout diverso senza modifiche significative al codice per l'app.
    Al contrario, `ListView` è relativamente monolitico nella struttura.

- `RecyclerView` include animazioni predefinite per l'aggiunta e la rimozione di elementi. `ListView` animazioni richiedono un lavoro aggiuntivo da parte dello sviluppatore di app.

### <a name="sections"></a>Sezioni

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[Parti e funzionalità di RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

In questo argomento viene illustrato il modo in cui il `Adapter`, `LayoutManager`e `ViewHolder` collaborano come classi helper per supportare `RecyclerView`.
Viene fornita una panoramica di alto livello di ognuna di queste classi helper e viene illustrato come usarle nell'app.

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[Esempio di RecyclerView di base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

Questo argomento si basa sulle informazioni fornite nelle [parti e funzionalità di RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) fornendo esempi di codice reale su come vengono implementati i vari elementi di `RecyclerView` per creare un'app di esplorazione foto reale.

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[Estensione dell'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

Questo argomento aggiunge codice aggiuntivo all'app di esempio presentata in [un esempio di RecyclerView di base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) per illustrare come gestire gli eventi di clic di elemento e aggiornare `RecyclerView` quando viene modificata l'origine dati sottostante.

### <a name="summary"></a>Riepilogo

Questa guida ha presentato il widget Android `RecyclerView`; è stato illustrato come aggiungere la libreria di supporto `RecyclerView` ai progetti Xamarin.Android, come `RecyclerView` ricicla le visualizzazioni, come impone il modello di supporto per l'efficienza e in che modo le varie classi helper che compongono `RecyclerView` collaborano per visualizzare le raccolte. Viene fornito codice di esempio per illustrare il modo in cui `RecyclerView` è integrato in un'applicazione, spiega come adattare i criteri di layout di `RecyclerView`tramite l'inserimento in diversi gestori di layout e come gestire gli eventi click degli elementi e inviare notifiche `RecyclerView` di dati modifiche all'origine.

Per ulteriori informazioni su `RecyclerView`, vedere la pagina relativa al [riferimento alla classe RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).

## <a name="related-links"></a>Collegamenti correlati

- [RecyclerViewer (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [Introduzione a lollipop](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
