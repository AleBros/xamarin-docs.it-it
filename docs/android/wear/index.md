---
title: Android Wear
description: Creazione di app per dispositivi Android indossabili.
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/16/2018
ms.openlocfilehash: 13a9a02984828781cc6402e982a4b5151c077c52
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648337"
---
# <a name="android-wear"></a>Android Wear

Android Wear è una versione di Android progettata per i dispositivi indossabili, ad esempio le Smart Watch. Questa sezione include istruzioni su come installare e configurare gli strumenti necessari per lo sviluppo di applicazioni, una procedura dettagliata per la creazione del primo dispositivo di consumo e un elenco di esempi a cui è possibile fare riferimento per creare app personalizzate.

## <a name="getting-startedandroidwearget-startedindexmd"></a>[Introduzione](~/android/wear/get-started/index.md)

Introduce Android Wear, descrive come installare e configurare il computer per lo sviluppo di usura e fornisce i passaggi per creare ed eseguire la prima app Android Wear in un emulatore o un dispositivo di consumo.

## <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[Interfaccia utente](~/android/wear/user-interface/index.md)

Vengono illustrati i controlli specifici per Android Wear e vengono forniti collegamenti a esempi che illustrano come usare questi controlli.

## <a name="platform-featuresandroidwearplatformindexmd"></a>[Funzionalità della piattaforma](~/android/wear/platform/index.md)

I documenti in questa sezione riguardano le funzionalità specifiche di Android Wear. Qui è disponibile un argomento che descrive come creare un WatchFace.

## <a name="screen-sizesandroidwearscreen-sizesmd"></a>[Dimensioni dello schermo](~/android/wear/screen-sizes.md)

Visualizzare in anteprima e ottimizzare l'interfaccia utente per le dimensioni dello schermo disponibili.

## <a name="deployment--testingandroidweardeploy-testindexmd"></a>[Distribuzione e test](~/android/wear/deploy-test/index.md)

Viene illustrato come distribuire l'app Android Wear in un dispositivo Android Wear o in un emulatore Android configurato per l'utilizzo. Include anche suggerimenti per il debug e informazioni su come configurare una connessione Bluetooth tra il computer di sviluppo e un dispositivo Android.

## <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[Usare le API](https://developer.android.com/reference/android/support/wearable)

Il sito per sviluppatori Android fornisce informazioni dettagliate sulle API di utilizzo chiave, ad esempio [attività indossabili](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html), [Intent](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html), [autenticazione](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html), [complicazioni](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html), [rendering delle complicazioni](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html), [notifiche](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html), [ Viste](https://developer.android.com/reference/android/support/wearable/view/package-summary.html) e [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html).



## <a name="samples"></a>Esempi

È possibile trovare alcuni [esempi](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Android+wear) di uso di Android Wear (oppure passare direttamente a [GitHub](https://github.com/xamarin/monodroid-samples/tree/master/wear)).

|Esempio|Descrizione|Schermata|
|--- |--- |--- |
|[SkeletonWear](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-skeletonwear)|Un semplice esempio delle nozioni di base dei progetti indossabili, tra cui GridViewPager e le notifiche interattive.|![Screenshot di Skeletonwear](images/skeleton.png)|
|[WatchViewStub](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchviewstub)|Demo semplice del controllo WatchViewStub che rileva la forma dello schermo e carica automaticamente il layout corretto. Per informazioni sul funzionamento di WatchViewStub, vedere il layout **risorse/layout/main_activity. XML** .|![Screenshot di WatchViewStub](images/watchview.png)|
|[RecipeAssistant](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-recipeassistant)|Dimostrazione di pagine di notifica di utilizzo, sotto forma di procedure per la ricetta. Le notifiche vengono create in RecipeService.cs.|![Screenshot di RecipeAssistant](images/recipeassist.png)|
|[ElizaChat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-elizachat)|Esempio divertente di interazione con un "Personal Assistant" chiamato Eliza, usando le notifiche interattive di Wear per creare una conversazione usando risposte inscatolate.|![Screenshot di ElizaChat](images/eliza.png)|
|[GridViewPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager)|GridViewPager implementa il modello di navigazione 2D, in cui l'utente scorre verticalmente e quindi orizzontalmente per spostarsi tra le opzioni e il contenuto.|![Screenshot di GridViewPager](images/gridviewpager.png)|
|[WatchFace](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchface)|WatchFace è un quadrante di orologio personalizzato con ore, minuti e secondi in stile analogico. In questo esempio viene illustrato come creare un servizio espressione di controllo che consente di disegnare l'ora corrente e gestire la modalità di ambiente e gli eventi di modifica della visibilità. Include un ricevitore di trasmissione che rimane in ascolto delle modifiche del fuso orario e aggiorna automaticamente l'ora di conseguenza.|![Screenshot di WatchFace](images/gridviewpager.png)|


## <a name="videos"></a>Video

Vedere questi collegamenti video che illustrano Novell. Android con supporto di utilizzo:

|Descrizione|Schermata|
|--- |--- |
|[Android L e molto altro ancora](https://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; Android L Developer Preview ha introdotto una pletora di nuove API che consentono agli sviluppatori di sfruttare i vantaggi offerti da, ad esempio la progettazione di materiali, le notifiche e le nuove animazioni, per citarne alcune.|![Screenshot video della presentazione](images/video-android-l.png)|
|[C#è a mio occhio: Google Glass e Android Wear](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash; wearable computing potrebbero sembrare qualcosa del futuro (o un episodio di Inspector Gadget), ma molte persone hanno già adottato il futuro. C#gli sviluppatori conoscono questo e hanno già gli strumenti e le competenze per sfruttare la potenza dei dispositivi indossabili (dall'evoluzione 2014).|![Screenshot video della presentazione](images/video-eyes-ears.png)|
|[Novità di Novell. Android](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; Android L, Android Wear, Android TV, Android auto, Material Design e Art; che cosa si intende per gli sviluppatori Novell? di evolve 2014.|![Screenshot video della presentazione](Images/video-whats-new.png)|


<!--

March 18
https://blog.xamarin.com/android-wear/

August 14
https://blog.xamarin.com/android-l-developer-preview-android-wear-support/

August 27
https://blog.xamarin.com/tips-for-your-first-android-wear-app/

Watch Face
https://github.com/Redth/Xamarin.Wear.WatchFace
-->
