---
title: "Funzionalità della piattaforma"
description: "Documenti in questa sezione illustrano le funzionalità specifiche per Android. Qui sono disponibili argomenti, ad esempio tramite frammenti, l'uso con le mappe e incapsulamento dei dati con i provider di contenuti."
ms.topic: article
ms.prod: xamarin
ms.assetid: DDE54082-6E2B-9ED9-05FB-D9C1D1B1258E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 2cc8121664afa5130efd2f138a8ea8567948bea5
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="platform-features"></a>Funzionalità della piattaforma

_Documenti in questa sezione illustrano le funzionalità specifiche per Android. Qui sono disponibili argomenti, ad esempio tramite frammenti, l'uso con le mappe e incapsulamento dei dati con i provider di contenuti._

## <a name="android-beamandroidplatformandroid-beammd"></a>[Android Beam](~/android/platform/android-beam.md)

Trasmetti Android sono una nuova tecnologia di comunicazione (prossimità) 4 di Android che consente alle applicazioni di condividere informazioni tramite NFC in stretta vicinanza.

## <a name="fingerprint-authenticationandroidplatformfingerprint-authenticationindexmd"></a>[Autenticazione con impronta digitale](~/android/platform/fingerprint-authentication/index.md)

In questa sezione viene illustrato come utilizzare l'autenticazione impronta digitale, introdotto inizialmente in Android 6.0, a un'applicazione di xamarin.


## <a name="firebase-job-dispatcherandroidplatformfirebase-job-dispatchermd"></a>[Firebase Job Dispatcher](~/android/platform/firebase-job-dispatcher.md)

Questa guida illustra il Dispatcher processo Firebase e come utilizzarla per semplificare i processi in background in esecuzione in un'app xamarin.



##  <a name="fragmentsandroidplatformfragmentsindexmd"></a>[Frammenti](~/android/platform/fragments/index.md)

Android 3.0 sono state introdotte frammenti, che illustra come supportare progettazioni più flessibile per le dimensioni dello schermo diverse molti presenti nei telefoni e Tablet. In questo articolo illustra come usare i frammenti di sviluppo di applicazioni di xamarin e come supportare frammenti dispositivi pre-Android 3.0 (API livello 11). 



## <a name="app-linkingandroidplatformapp-linkingmd"></a>[Collegamento App](~/android/platform/app-linking.md)

Questa Guida verrà illustrate le modalità Android 6.0 supporta _collegamento app_, una tecnica che consente di App per dispositivi mobili a rispondere a URL nei siti Web. Parlerà di come implementare il collegamento app in un'applicazione Android 6.0 e come configurare un sito Web per concedere le autorizzazioni per l'app mobile per gestire i collegamenti per il dominio applicazione.



##  <a name="android-8-oreoandroidplatformoreomd"></a>[Android 8 Oreo](~/android/platform/oreo.md)

Questo articolo fornisce una struttura di nuove funzionalità di Android Oreo, spiega come preparare xamarin per lo sviluppo di Android Oreo e vengono forniti collegamenti ad applicazioni di esempio che illustrano come utilizzare le funzionalità di Android Oreo in App xamarin.



##  <a name="android-7-nougatandroidplatformnougatmd"></a>[Android 7 Nougat](~/android/platform/nougat.md)

Questo articolo fornisce una panoramica delle nuove funzionalità introdotte in Android 7.0 Nougat.




##  <a name="android-6-marshmallowandroidplatformmarshmallowmd"></a>[Android 6 Marshmallow](~/android/platform/marshmallow.md)

Questo articolo fornisce una panoramica delle nuove funzionalità introdotte in Android Marshmallow 6.0.




##  <a name="android-5-lollipopandroidplatformlollipopmd"></a>[Android 5 Lollipop](~/android/platform/lollipop.md)

Questa guida viene fornita una panoramica delle nuove funzionalità di Android 5.0 simbolo, ad esempio il tema di materiale, CardView, RecyclerView e intestazioni delle notifiche ed è collegato a approfondita articoli che consentono di usare queste nuove funzionalità nell'app. 



##  <a name="android-44-kitkatandroidplatformkitkatmd"></a>[Android 4.4 KitKat](~/android/platform/kitkat.md)

Android 4.4 (KitKat) viene caricato con numerose funzionalità per utenti e sviluppatori. Questa guida vengono evidenziate alcune di queste funzionalità e vengono forniti esempi di codice e i dettagli di implementazione che consentono di sfruttare al meglio KitKat. 




##  <a name="android-41-jelly-beanandroidplatformjelly-beanmd"></a>[Android 4.1 Jelly Bean](~/android/platform/jelly-bean.md)

Questo documento verrà fornita una panoramica di alto livello delle nuove funzionalità per gli sviluppatori che sono stati introdotti in Android 4.1. Queste funzionalità includono: avanzata notifiche, gli aggiornamenti per Android raggio di condividere file di grandi dimensioni, gli aggiornamenti per l'individuazione di rete dei dati multimediali, peer-to-peer, animazioni, nuove autorizzazioni. 



##  <a name="android-40-ice-cream-sandwichandroidplatformice-cream-sandwichmd"></a>[Android 4.0 Ice Cream Sandwich](~/android/platform/ice-cream-sandwich.md)

In questo articolo vengono descritte molte delle nuove funzionalità disponibili per gli sviluppatori di applicazioni con il *API Android di 4 - Sandwich gelato*. Vengono illustrate diverse nuove tecnologie di interfaccia utente e quindi esamina un'ampia gamma di nuove funzionalità che offre aggiornamenti per la condivisione dei dati tra le applicazioni e tra i dispositivi Android 4. 


##  <a name="working-with-the-android-manifestandroid-manifestmd"></a>[Utilizzo con il manifesto Android](android-manifest.md)

In questo articolo introducts il file AndroidManifest.xml e come forse essere utilizzato per controllare le funzionalità e vengono descritti i requisiti di un Mono per applicazione Android.


##  <a name="introduction-to-content-providersandroidplatformcontent-providersindexmd"></a>[Introduzione ai provider di contenuti](~/android/platform/content-providers/index.md)

Un provider di contenuti incapsula un archivio di dati e fornisce un'API per accedere alla finestra. Il provider esista come parte di un'applicazione Android che in genere fornisce anche un'interfaccia utente per la visualizzazione o gestione dei dati. Il principale vantaggio dell'utilizzo di un provider di contenuti consente ad altre applicazioni di accedere facilmente i dati incapsulati utilizzando un oggetto client provider (chiamato un ContentResolver). Insieme, un provider di contenuti e il resolver di contenuto offrono un'API coerenza tra le applicazioni per l'accesso ai dati è semplice per creare e utilizzare. Questo documento viene illustrato come accedere e compilare ContentProviders con xamarin. 



##  <a name="maps-and-locationandroidplatformmaps-and-locationindexmd"></a>[Mappe e posizione](~/android/platform/maps-and-location/index.md)

In questa sezione viene illustrato come utilizzare mappe e il percorso con xamarin. Copre tutti i dati da sfruttare l'applicazione di mappe predefinite per l'utilizzo di [API Android viene eseguito il mapping di Google v2](https://developers.google.com/maps/documentation/android/) direttamente. Inoltre, viene spiegato come utilizzare una singola API per lavorare con i servizi di posizione, che usano triangolazione cellulare per consentire a un'applicazione ottenere le correzioni di percorso, la posizione di Wi-Fi e GPS. 



## <a name="android-speechandroidplatformspeechmd"></a>[Riconoscimento vocale Android](~/android/platform/speech.md)

In questa sezione viene illustrato come utilizzare il testo Android per il riconoscimento vocale e riconoscimento vocale per strutture di testo. Descrive inoltre l'installazione language pack e l'interpretazione del testo pronunciato al dispositivo. 


##  <a name="binding-a-java-librarybinding-java-libraryindexmd"></a>[Associazione di una libreria Java](binding-java-library/index.md)

Questa guida illustra come incorporare le librerie di Java in App xamarin mediante la creazione di una raccolta di associazioni.

##  <a name="java-integrationjava-integrationindexmd"></a>[Integrazione di Java](java-integration/index.md)

In questo articolo viene fornita una panoramica dei metodi che gli sviluppatori possono riutilizzare i componenti di Java esistenti in App xamarin.

##  <a name="renderscriptrenderscriptmd"></a>[Renderscript](renderscript.md)

Questa guida illustra Renderscript.