---
title: Android Wear
description: Creazione di App per dispositivi Android indossabile.
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/16/2018
ms.openlocfilehash: fca72291dd726d4f2a6635d26390baa103ee0d2d
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864909"
---
# <a name="android-wear"></a>Android Wear

Android Wear è una versione di Android progettata per indossabile dispositivi quali smart espressioni di controllo. In questa sezione include istruzioni su come installare e configurare gli strumenti necessari per lo sviluppo Wear, una procedura dettagliata per la creazione del primo dispositivo Wear e un elenco di esempi che è possibile fare riferimento per Wear le App per la creazione di propri.

## <a name="getting-startedandroidwearget-startedindexmd"></a>[Introduzione](~/android/wear/get-started/index.md)

Introduce Android Wear, viene descritto come installare e configurare il computer per lo sviluppo di usura e vengono descritti i passaggi che consentono di creare ed eseguire la prima app Android Wear in un emulatore o un dispositivo Wear.

## <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[Interfaccia utente](~/android/wear/user-interface/index.md)

Viene spiegato Android Wear specifici controlli e fornisce collegamenti a esempi che illustrano come usare questi controlli.

## <a name="platform-featuresandroidwearplatformindexmd"></a>[Funzionalità della piattaforma](~/android/wear/platform/index.md)

I documenti in questa sezione illustrano funzionalità specifiche di Android Wear. Qui troverai un argomento che descrive come creare un WatchFace.

## <a name="screen-sizesandroidwearscreen-sizesmd"></a>[Dimensioni dello schermo](~/android/wear/screen-sizes.md)

Visualizzare in anteprima e ottimizzare l'interfaccia utente per le dimensioni disponibile sullo schermo.

## <a name="deployment--testingandroidweardeploy-testindexmd"></a>[Distribuzione e test](~/android/wear/deploy-test/index.md)

Viene illustrato come distribuire l'app Android Wear a un dispositivo Android Wear o all'emulatore Android configurato per Wear. Include anche il debug di suggerimenti e le informazioni su come configurare una connessione Bluetooth tra il computer di sviluppo e un dispositivo Android.

## <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[Usare le API](https://developer.android.com/reference/android/support/wearable)

Il sito per sviluppatori Android fornisce informazioni dettagliate sulla chiave, usare le API, ad esempio [attività indossabile](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html), [Intent](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html), [Authentication](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html), [ Complicazioni](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html), [complicazioni Rendering](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html), [notifiche](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html), [visualizzazioni](https://developer.android.com/reference/android/support/wearable/view/package-summary.html), e [WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html).



## <a name="samples"></a>Esempi

È possibile trovare numerose [samples](https://developer.xamarin.com/samples/android/Android%20Wear/) usando Android Wear (o passare direttamente alla [github](https://github.com/xamarin/monodroid-samples/tree/master/wear)).

|Esempio|Descrizione|Schermata|
|--- |--- |--- |
|[SkeletonWear](https://developer.xamarin.com/samples/monodroid/wear/SkeletonWear/)|Un semplice esempio di base dei progetti indossabile, tra cui GridViewPager e notifiche interattive.|![Screenshot della Skeletonwear](images/skeleton.png)|
|[WatchViewStub](https://developer.xamarin.com/samples/monodroid/wear/WatchViewStub/)|Una semplice dimostrazione del controllo WatchViewStub che rileva la forma dello schermo e carica automaticamente il layout corretto. Verificare il funzionamento WatchViewStub il **Resources/layout/main_activity.xml** layout.|![Screenshot of WatchViewStub](images/watchview.png)|
|[RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/)|Dimostrazione delle pagine di notifica Wear, sotto forma di passaggi di "ricetta". Le notifiche vengono create in RecipeService.cs.|![Screenshot della RecipeAssistant](images/recipeassist.png)|
|[ElizaChat](https://developer.xamarin.com/samples/monodroid/wear/ElizaChat/)|Esempio divertente dell'interazione con un "assistente personale" chiamato Eliza, uso delle notifiche di interattiva Wear per creare una conversazione che utilizza le risposte scatola.|![Screenshot of ElizaChat](images/eliza.png)|
|[GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/)|GridViewPager implementa il modello di spostamento 2D, in cui l'utente passa verticalmente e orizzontalmente per spostarsi tra le opzioni e il contenuto.|![Screenshot della GridViewPager](images/gridviewpager.png)|
|[WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)|WatchFace è un viso di verifica personalizzato con stile analogico ora, minuto e secondo mani. Questo esempio viene illustrato come creare un servizio di volti espressioni di controllo che disegna l'ora corrente e la visibilità e la modalità ambiente gestisce gli eventi di modifica. Include un ricevitore di trasmissioni che ascolta le modifiche del fuso orario e aggiorna automaticamente il tempo di conseguenza.|![Screenshot of WatchFace](images/gridviewpager.png)|


## <a name="videos"></a>Video

Consultare questi video i collegamenti che trattano di xamarin. Android con Wear supportano:

|Descrizione|Schermata|
|--- |--- |
|[Android L e molto altro ancora](https://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; The Android L Developer Preview introdotte una vasta gamma di nuove API per gli sviluppatori a sfruttare i vantaggi, tra cui Material Design, notifiche e le nuove animazioni, per citarne alcuni.|![Schermata del video di presentazione](images/video-android-l.png)|
|[C#è nella mio orecchie e il punto di vista: Google Glass e Android Wear](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash; computing indossabile può sembrare un elemento dal futuro (o un episodio di Gadget Inspector), ma molte persone già adottano il futuro oggi stesso! C#gli sviluppatori a conoscenza e dispongono già degli strumenti e le competenze necessarie per sfruttare la potenza dei dispositivi indossabile (da variare delle 2014).|![Schermata del video di presentazione](images/video-eyes-ears.png)|
|[Novità in xamarin. Android](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; L Android, Android Wear, Android TV, Android Auto, Material Design e ART; come funziona questo mean all'utente gli sviluppatori Xamarin? da variare delle 2014.|![Schermata del video di presentazione](Images/video-whats-new.png)|


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
