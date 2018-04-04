---
title: RecyclerView
description: RecyclerView è un gruppo di visualizzazione per la visualizzazione di raccolte. è progettato per sostituire più flessibili per i gruppi di visualizzazione precedenti, ad esempio ListView e GridView.  Questa guida viene illustrato come utilizzare e personalizzare RecyclerView nelle applicazioni di xamarin.
ms.prod: xamarin
ms.assetid: 91EF0BD2-3306-47E1-9B39-627A1787762F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/03/2018
ms.openlocfilehash: 187339244d53c154cc22672a3d2ceba7e0a75bcf
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="recyclerview"></a>RecyclerView

_RecyclerView è un gruppo di visualizzazione per la visualizzazione di raccolte. è progettato per sostituire più flessibili per i gruppi di visualizzazione precedenti, ad esempio ListView e GridView.  Questa guida viene illustrato come utilizzare e personalizzare RecyclerView nelle applicazioni di xamarin._

## <a name="recyclerview"></a>RecyclerView

Molte applicazioni è necessario visualizzare raccolte dello stesso tipo (ad esempio, i messaggi, contatti, immagini o brani); spesso, la raccolta è troppo grande per sullo schermo, quindi la raccolta è presentata in una finestra piccola che può scorrere in modo uniforme in tutti gli elementi nella raccolta.
`RecyclerView` è un widget di Android che consente di visualizzare una raccolta di elementi in un elenco o una griglia, consentendo all'utente di scorrere la raccolta. Di seguito è riportato una schermata di un'applicazione di esempio che utilizza `RecyclerView` per visualizzare il contenuto di posta in arrivo tramite posta elettronica in un elenco di scorrimento verticale:

[![Applicazione di esempio utilizzando RecyclerView ai messaggi di posta in arrivo di elenco](images/01-recyclerview-example-sml.png)](images/01-recyclerview-example.png#lightbox)

`RecyclerView` offre due caratteristiche interessanti:

-  Include un'architettura flessibile che consente di modificare il comportamento collegando i componenti Preferiti.

-  È efficace con raccolte di grandi dimensioni perché riutilizza viste di elemento e richiede l'utilizzo di *visualizzare titolari* Visualizza i riferimenti.

Questa guida viene illustrato come utilizzare `RecyclerView` nelle applicazioni di xamarin; viene spiegato come aggiungere il `RecyclerView` pacchetto per il progetto xamarin e viene descritto come `RecyclerView` funzioni in un'applicazione tipica. Vengono forniti esempi di codice effettivo per viene illustrato come integrare `RecyclerView` nell'applicazione, modalità di implementazione di fare clic su visualizzazione elemento e come aggiornare `RecyclerView` quando i dati sottostanti cambiano. Questa guida presuppone che si ha familiarità con lo sviluppo di xamarin.


### <a name="requirements"></a>Requisiti

Sebbene `RecyclerView` è spesso associata con il simbolo Android 5.0, è disponibile come una libreria di supporto &ndash; `RecyclerView` funziona con App tale livello API di destinazione (Android 2.1) 7 e versioni successive. Le operazioni seguenti sono necessario utilizzare `RecyclerView` nelle applicazioni basate su Xamarin:

-  **Xamarin** &ndash; xamarin 4.20 o versioni successive deve essere installato e configurato con Visual Studio o Visual Studio per Mac.

-  Il progetto di app deve includere il **Xamarin.Android.Support.v7.RecyclerView** pacchetto. Per ulteriori informazioni sull'installazione dei pacchetti NuGet, vedere [procedura dettagliata: inclusi un NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).


### <a name="overview"></a>Panoramica

`RecyclerView` può essere considerato come una sostituzione per il `ListView` e `GridView` widget in Android. Come nelle versioni precedenti, `RecyclerView` è progettato per visualizzare un set di dati di grandi dimensioni in una finestra piccola, ma `RecyclerView` offre più opzioni di layout e prestazioni migliori è ottimizzato per la visualizzazione di raccolte di grandi dimensioni. Se si ha familiarità con `ListView`, esistono alcune differenze importanti tra `ListView` e `RecyclerView`:

-   `RecyclerView` è leggermente più complesso da utilizzare: è necessario scrivere codice più utilizzare `RecyclerView` rispetto a `ListView`.

-   `RecyclerView` non fornire una scheda predefinita; è necessario implementare il codice dell'adattatore che accede all'origine dati. Tuttavia, Android include diverse schede predefinite che funzionano con `ListView` e `GridView`.

-   `RecyclerView` non è disponibile un evento di elemento fare clic quando un utente tocca un elemento. eventi click di elemento, invece, vengono gestiti dalle classi di supporto. Al contrario, `ListView` offre un evento di elemento-fare clic su.

-   `RecyclerView` migliora le prestazioni per il riciclo delle viste e applicando il modello di visualizzazione titolare, che consente di eliminare le ricerche di risorse di layout non necessario. Utilizzo del modello di visualizzazione titolare è facoltativo in `ListView`.

-   `RecyclerView` si basa su una progettazione modulare che rende più semplice da personalizzare. Ad esempio, è possibile collegare un criterio di layout diverso senza modifiche significative al codice all'applicazione.
    Al contrario, `ListView` è relativamente monolitico nella struttura.

-   `RecyclerView` include per elemento, aggiungere e rimuovere le animazioni incorporate. `ListView` le animazioni richiedono alcune attività aggiuntive da parte dello sviluppatore di app.


### <a name="sections"></a>Sezioni

#### <a name="recyclerview-parts-and-functionalityandroiduser-interfacelayoutsrecycler-viewparts-and-functionalitymd"></a>[Funzionalità e le parti RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)

Questo argomento viene illustrato come la `Adapter`, `LayoutManager`, e `ViewHolder` lavorano insieme come classi di supporto per il supporto `RecyclerView`.
Fornisce una panoramica generale di ognuna di queste classi helper e viene illustrato come vengono utilizzati nell'applicazione.

#### <a name="a-basic-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewrecyclerview-examplemd"></a>[Un esempio di base RecyclerView](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)

Questo argomento si basa sulle informazioni fornite [RecyclerView parti e le funzionalità](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md) fornendo esempi di codice reali relativi vari `RecyclerView` elementi vengono implementati per compilare un'app di foto di esplorazione del mondo reale.

#### <a name="extending-the-recyclerview-exampleandroiduser-interfacelayoutsrecycler-viewextending-the-examplemd"></a>[Estensione dell'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)

In questo argomento aggiunge codice aggiuntivo per l'applicazione di esempio presentata in [A base RecyclerView esempio](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) per illustrare come gestire eventi click di elemento e aggiornare `RecyclerView` quando le modifiche apportate all'origine dati sottostante.


### <a name="summary"></a>Riepilogo

Questa Guida introdotto di Android `RecyclerView` widget; descritto come aggiungere il `RecyclerView` supportano come raccolta di progetti di xamarin, `RecyclerView` Ricicla visualizzazioni, come viene utilizzato per applicare il modello di visualizzazione titolare per migliorare l'efficienza e come i vari classi helper che costituiscono `RecyclerView` collaborare per visualizzare le raccolte. Fornito codice di esempio per illustrare come `RecyclerView` è integrato in un'applicazione, illustrato come personalizzare `RecyclerView`del criterio di layout per l'inserimento di gestori di layout diversi e viene descritto come gestire l'elemento fare clic su eventi e notifica `RecyclerView`origine modifiche dei dati.

Per ulteriori informazioni su `RecyclerView`, vedere il [riferimento alla classe RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html).


## <a name="related-links"></a>Collegamenti correlati

- [RecyclerViewer (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [Introduzione al simbolo](~/android/platform/lollipop.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
