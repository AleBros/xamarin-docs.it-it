---
title: Android Wear
description: Creazione di applicazioni per dispositivi Android di indossabile.
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/16/2018
ms.openlocfilehash: ec8ed32eba7d8341904f800d5c174235dc25388c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="android-wear"></a>Android Wear

Usura Android è una versione di Android è progettato per i dispositivi indossabile, ad esempio le espressioni di controllo smart. In questa sezione include le istruzioni su come installare e configurare gli strumenti necessari per lo sviluppo di scadenza, una procedura dettagliata per la creazione del primo dispositivo usura e un elenco di esempi che è possibile fare riferimento per l'App, usare per la creazione di propri.

##  <a name="getting-startedandroidwearget-startedindexmd"></a>[Introduzione](~/android/wear/get-started/index.md)

Introduce accenti Android, viene descritto come installare e configurare il computer per lo sviluppo di usura e vengono descritti i passaggi che consentono di creare ed eseguire la prima app Android con accenti su un emulatore o dispositivo usura.

##  <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[Interfaccia utente](~/android/wear/user-interface/index.md)

Spiega Android usura controlla e fornisce collegamenti a esempi che illustrano come utilizzare questi controlli.

##  <a name="platform-featuresandroidwearplatformindexmd"></a>[Funzionalità della piattaforma](~/android/wear/platform/index.md)

Documenti in questa sezione riguardano le funzionalità specifiche per Android con accenti. Di seguito è disponibile un argomento che descrive come creare un WatchFace.

##  <a name="screen-sizesandroidwearscreen-sizesmd"></a>[Dimensioni dello schermo](~/android/wear/screen-sizes.md)

Visualizzare in anteprima e ottimizzare l'interfaccia utente per le dimensioni dello schermo disponibili.

##  <a name="deployment--testingandroidweardeploy-testindexmd"></a>[Distribuzione e test](~/android/wear/deploy-test/index.md)

Viene illustrato come distribuire l'app, usare Android a un dispositivo Android con accenti o all'emulatore Android configurato per la scadenza. Include inoltre il debug di suggerimenti e informazioni su come configurare una connessione Bluetooth tra il computer di sviluppo e un dispositivo Android.

##  <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[Usare le API](https://developer.android.com/reference/android/support/wearable)

Il sito per sviluppatori Android fornisce informazioni dettagliate sulla chiave, usare le API, ad esempio [attività indossabile](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html), [intenti](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html), [autenticazione](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html), [ Complicazioni](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html), [complicazioni Rendering](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html), [notifiche](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html), [viste](https://developer.android.com/reference/android/support/wearable/view/package-summary.html), e [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html).



## <a name="samples"></a>Esempi

È possibile trovare un numero di [esempi](https://developer.xamarin.com/samples/android/Android%20Wear/) utilizzando accenti Android (o passare direttamente alla [github](https://github.com/xamarin/monodroid-samples/tree/master/wear)). 

|Esempio|Descrizione|Schermata|
|--- |--- |--- |
|[SkeletonWear](https://developer.xamarin.com/samples/SkeletonWear/)|Un esempio semplice delle nozioni di base dei progetti indossabile, incluse GridViewPager e le notifiche interattive.|![Schermata di Skeletonwear](images/skeleton.png)|
|[WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/)|Una semplice demo del controllo WatchViewStub che rileva forma dello schermo e carica automaticamente il layout corretto.  Verificare il funzionamento del WatchViewStub **Resources/layout/main_actvity.xml** layout.|![Schermata di WatchViewStub](images/watchview.png)|
|[RecipeAssistant](https://developer.xamarin.com/samples/RecipeAssistant/)|Dimostrazione di pagine di notifica usura, sotto forma di passaggi recipe. Le notifiche vengono create in RecipeService.cs.|![Schermata di RecipeAssistant](images/recipeassist.png)|
|[ElizaChat](https://developer.xamarin.com/samples/ElizaChat/)|Esempio divertente dell'interazione con un Assistente"personale" chiamato Eliza, utilizzando le notifiche degli interattive usura per creare una conversazione che utilizza le risposte precompilate.|![Schermata di ElizaChat](images/eliza.png)|
|[GridViewPager](https://developer.xamarin.com/samples/GridViewPager/)|GridViewPager implementa il modello di navigazione 2D, in cui l'utente passa verticalmente e orizzontalmente per spostarsi tra le opzioni e il contenuto.|![Schermata di GridViewPager](images/gridviewpager.png)|
|[WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)|WatchFace è un tipo di carattere personalizzato espressioni di controllo con stile analogico ora, minuto e secondo mani. Questo esempio viene illustrato come creare un servizio faccia espressioni di controllo che disegna l'ora corrente e la visibilità e la modalità ambiente gestisce gli eventi di modifica. Include un ricevitore di trasmissione che ascolta i cambiamenti del fuso orario e aggiorna automaticamente il tempo di conseguenza.|![Schermata di WatchFace](images/gridviewpager.png)|


##  <a name="videos"></a>Video

Controllare i collegamenti che trattano xamarin con usura supportano i video:

|Descrizione|Schermata|
|--- |--- |
|[Android L e molto più](http://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; di Android L Developer Preview introdotto moltissime nuove API per gli sviluppatori di sfruttare, tra cui progettazione materiale, notifiche e le nuove animazioni, per citarne alcune.|![Schermata di video di presentazione](images/video-android-l.png)|
|[C# è in auguri e il punto di vista: Google Glass e accenti Android](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash; computing indossabile potrebbe sembrare un elemento da futuro (o un episodio di controllo Gadget), ma molti utenti sono già adottando futuro oggi! Gli sviluppatori c# conoscono e dispongono già di strumenti e le competenze di sfruttare le potenzialità di dispositivi indossabile (da Evolve 2014).|![Schermata di video di presentazione](images/video-eyes-ears.png)|
|[Novità di xamarin](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; L Android, usura Android, Android TV, Auto Android, materiale di progettazione e arte; ciò che avviene medio è uno sviluppatore di Xamarin? da Evolve 2014.|![Schermata di video di presentazione](Images/video-whats-new.png)|


<!--

March 18
http://blog.xamarin.com/android-wear/

August 14
http://blog.xamarin.com/android-l-developer-preview-android-wear-support/

August 27
http://blog.xamarin.com/tips-for-your-first-android-wear-app/

Watch Face
https://github.com/Redth/Xamarin.Wear.WatchFace
-->
