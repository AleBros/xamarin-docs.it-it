---
title: RecyclerView
description: RecyclerView è un gruppo di visualizzazione per la visualizzazione delle raccolte. è progettato per essere una sostituzione più flessibile per i gruppi di viste precedenti, ad esempio ListView e GridView.  Questa guida illustra come usare e personalizzare RecyclerView in applicazioni Novell. Android.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/03/2018
ms.openlocfilehash: e6c5f6e19599624899f74b99dcaaae734d098b3a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764200"
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView è un gruppo di visualizzazione per la visualizzazione delle raccolte. è progettato per essere una sostituzione più flessibile per i gruppi di viste precedenti, ad esempio ListView e GridView.  Questa guida illustra come usare e personalizzare RecyclerView in applicazioni Novell. Android._

## <a name="recyclerview"></a>RecyclerView

Molte app devono visualizzare le raccolte dello stesso tipo (ad esempio messaggi, contatti, immagini o canzoni); spesso, questa raccolta è troppo grande per essere adattata allo schermo, quindi la raccolta viene visualizzata in una piccola finestra che consente di scorrere agevolmente tutti gli elementi della raccolta.
`RecyclerView`è un widget Android che visualizza una raccolta di elementi in un elenco o una griglia, consentendo all'utente di scorrere la raccolta. Di seguito è riportata una schermata di un'app di esempio `RecyclerView` che usa per visualizzare il contenuto della posta in arrivo in un elenco a scorrimento verticale:

[![App di esempio con RecyclerView per elencare i messaggi della posta in arrivo](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView`in sono disponibili due funzionalità interessanti:

- Dispone di un'architettura flessibile che consente di modificarne il comportamento tramite l'inserimento di componenti preferiti.

- È efficiente con le raccolte di grandi dimensioni perché riutilizza le visualizzazioni di elementi e richiede l'uso di *titolari di visualizzazione* per memorizzare nella cache i riferimenti alla visualizzazione.

Questa guida illustra come usare `RecyclerView` nelle applicazioni Novell. Android. viene illustrato come aggiungere il `RecyclerView` pacchetto al progetto Novell. Android e viene descritto come `RecyclerView` funzioni in un'applicazione tipica. Sono disponibili esempi di codice reali che illustrano come integrare `RecyclerView` nell'applicazione, come implementare la visualizzazione di elementi e come aggiornarli `RecyclerView` quando cambiano i dati sottostanti. Questa guida presuppone che l'utente abbia familiarità con lo sviluppo di Novell. Android.

### <a name="requirements"></a>Requisiti

Sebbene `RecyclerView` sia spesso associato al Lollipop di Android 5,0, viene offerto come libreria &ndash; `RecyclerView` di supporto funziona con le app che hanno come destinazione API Level 7 (Android 2,1) e versioni successive. Per usare `RecyclerView` le applicazioni basate su Novell, è necessario quanto segue:

- **Novell. Android** &ndash; Novell. Android 4,20 o versione successiva deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

- Il progetto dell'app deve includere il pacchetto **Novell. Android. support. V7. RecyclerView** . Per ulteriori informazioni sull'installazione di pacchetti NuGet, [vedere Procedura dettagliata: Inclusione di NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

### <a name="overview"></a>Panoramica

`RecyclerView`può essere considerato come una sostituzione per i `ListView` widget e `GridView` in Android. Analogamente ai predecessori `RecyclerView` , è progettato per visualizzare un set di dati di grandi dimensioni in una `RecyclerView` piccola finestra, ma offre più opzioni di layout ed è ottimizzato per la visualizzazione di raccolte di grandi dimensioni. Se si ha familiarità `ListView`con, esistono alcune differenze importanti tra `ListView` e `RecyclerView`:

- `RecyclerView`è leggermente più complesso da usare: è necessario scrivere altro codice da usare `RecyclerView` rispetto a. `ListView`

- `RecyclerView`non fornisce un adapter predefinito; è necessario implementare il codice dell'adattatore che accede all'origine dati. Tuttavia, Android include diversi adapter predefiniti che funzionano con `ListView` e. `GridView`

- `RecyclerView`non offre un evento di clic dell'elemento quando un utente tocca un elemento; gli eventi di clic sugli elementi vengono invece gestiti da classi helper. Al contrario, `ListView` offre un evento click-through.

- `RecyclerView`consente di migliorare le prestazioni tramite il riciclo delle visualizzazioni e l'applicazione del modello del supporto di visualizzazione, che elimina le ricerche non necessarie sulle risorse di layout. L'uso del modello del supporto di visualizzazione è facoltativo `ListView`in.

- `RecyclerView`si basa su una progettazione modulare che ne semplifica la personalizzazione. Ad esempio, è possibile inserire un criterio di layout diverso senza modifiche significative al codice per l'app.
    Al contrario, `ListView` è relativamente monolitico nella struttura.

- `RecyclerView`include animazioni predefinite per l'aggiunta e la rimozione di elementi. `ListView`le animazioni richiedono alcuni sforzi aggiuntivi da parte dello sviluppatore di app.

### <a name="sections"></a>Sezioni

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[Parti e funzionalità di RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

In questo argomento viene illustrata `LayoutManager`la modalità di interazione tra, e `ViewHolder` come classi helper `RecyclerView`per il `Adapter`supporto di.
Viene fornita una panoramica di alto livello di ognuna di queste classi helper e viene illustrato come usarle nell'app.

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[Esempio di RecyclerView di base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

Questo argomento si basa sulle informazioni fornite nelle [parti e funzionalità di RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) fornendo esempi di codice reale su come vengono `RecyclerView` implementati i vari elementi per creare un'app di esplorazione foto reale.

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[Estensione dell'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

Questo argomento aggiunge codice aggiuntivo all'app di esempio presentata in [un esempio di RecyclerView di base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) per illustrare come gestire gli eventi Click- `RecyclerView` through e aggiornarli quando viene modificata l'origine dati sottostante.

### <a name="summary"></a>Riepilogo

In questa guida è stato `RecyclerView` introdotto il widget Android. è stato illustrato `RecyclerView` come aggiungere la libreria di supporto ai progetti Novell `RecyclerView` . Android, come riciclare le visualizzazioni, come applicare il modello del supporto di visualizzazione per l'efficienza e come i vari classi helper che compongono la `RecyclerView` collaborazione per la visualizzazione delle raccolte. Il codice di esempio per illustrare `RecyclerView` come è integrato in un'applicazione ha illustrato come personalizzare `RecyclerView`i criteri di layout inserendo in diversi gestori di layout e descritto come gestire gli eventi click degli elementi e inviare notifiche `RecyclerView`delle modifiche all'origine dati.

Per ulteriori informazioni su `RecyclerView`, vedere la pagina relativa al [riferimento alla classe RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).

## <a name="related-links"></a>Collegamenti correlati

- [RecyclerViewer (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [Introduzione a lollipop](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
