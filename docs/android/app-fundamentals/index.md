---
title: Nozioni fondamentali dell'applicazione xamarin. Android
description: Concetti di base dell'applicazione
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1d3bd071eeffb77f94a1b5f35f1df59f2d8c7a8a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105558"
---
# <a name="xamarinandroid-application-fundamentals"></a>Nozioni fondamentali dell'applicazione xamarin. Android

In questa sezione fornisce una Guida su alcune delle più comuni attività di cose o concetti che gli sviluppatori devono tenere presenti quando si sviluppano applicazioni Android.

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[Accessibilità](~/android/app-fundamentals/accessibility.md)

Questa pagina descrive come usare le API di accessibilità di Android per compilare le App in base al [elenco di controllo di accesso facilitato](~/cross-platform/app-fundamentals/accessibility.md).

##  <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[Informazioni sui livelli API Android](~/android/app-fundamentals/android-api-levels.md)

Questa guida viene descritto come Android Usa i livelli di API per gestire la compatibilità delle app tra versioni diverse di Android e viene descritto come configurare le impostazioni di progetto xamarin. Android per distribuire questi livelli di API nell'app. Inoltre, questa guida illustra come scrivere il codice di runtime che interagisce con diversi livelli di API e fornisce un elenco di riferimento di tutti i livelli API Android, i numeri di versione (ad esempio Android 8.0), nomi di codice Android (ad esempio Oreo) e codici versione build.



##  <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Risorse in Android](~/android/app-fundamentals/resources-in-android/index.md)

Questo articolo introduce il concetto di risorse Android nei documenti di xamarin. Android e come usarli. Viene illustrato come usare le risorse nell'applicazione Android per supportare la localizzazione dell'applicazione e più dispositivi tra cui densità e dimensioni dello schermo diverse.




##  <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[Ciclo di vita dell'attività](~/android/app-fundamentals/activity-lifecycle/index.md)

Le attività sono un blocco predefinito fondamentale di applicazioni Android e possono essere presenti in un numero di stati diversi. Il ciclo di vita di attività inizia con la creazione di istanze e termina con la distruzione e include molti stati intermedi. Quando un'attività Cambia stato, viene chiamato il metodo di eventi del ciclo di vita appropriata, notificando l'attività dell'imminente modifica dello stato e in modo che possa eseguire il codice per adattarsi a tale modifica. Questo articolo esamina il ciclo di vita delle attività e spiega le responsabilità che un'attività ha durante ognuna di queste modifiche di stato da parte di un'applicazione affidabile, ben progettata.

##  <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[Localizzazione](~/android/app-fundamentals/localization.md)

Questo articolo illustra come localizzare xamarin. Android in altre lingue mediante la traduzione di stringhe e fornire immagini alternative.

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[Servizi](~/android/app-fundamentals/services/index.md)

Questo articolo illustra i servizi Android, che sono componenti di Android che consentono di lavorare da eseguire in background. Illustra i diversi scenari appropriati per i servizi e viene illustrato come implementarli sia per eseguire le attività in background a esecuzione prolungata, nonché per fornire un'interfaccia per le chiamate di procedura remota.

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[Ricevitori di trasmissione](~/android/app-fundamentals/broadcast-receivers.md)

Questa guida illustra come creare e usare ricevitori di trasmissioni, un componente di Android che risponde a trasmissioni a livello di sistema, in xamarin. Android.



##  <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[Autorizzazioni](~/android/app-fundamentals/permissions.md)

È possibile usare il supporto di strumenti integrato in Visual Studio per Mac o Visual Studio per creare e aggiungere le autorizzazioni per il manifesto Android. Questo documento descrive come aggiungere autorizzazioni in Visual Studio e Xamarin Studio.



##  <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[Grafica e animazione](~/android/app-fundamentals/graphics-and-animation.md)

Android fornisce un framework molto avanzato e diverse per supportare le animazioni ed elementi grafici 2D. Questo documento introduce questi Framework e illustra come creare le animazioni e grafica personalizzata e usarli in un'applicazione xamarin. Android.


##  <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[Architetture CPU](~/android/app-fundamentals/cpu-architectures.md)

Xamarin. Android supporta diverse architetture della CPU, inclusi i dispositivi a 32 e 64 bit. Questo articolo illustra come specificare come destinazione di un'app a uno o più architetture della CPU Android è supportata.




##  <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[Gestione della rotazione](~/android/app-fundamentals/handling-rotation.md)

Questo articolo descrive come gestire le modifiche apportate orientamento di dispositivo in xamarin. Android. Viene illustrato come lavorare con il sistema risorse Android per caricare automaticamente le risorse per un orientamento del dispositivo specifico nonché come gestire a livello di codice l'orientamento viene modificato. Quindi vengono descritte le tecniche per mantenere lo stato quando un dispositivo viene ruotato.



##  <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Audio Android](~/android/app-fundamentals/android-audio.md)

Il sistema operativo Android offre ampio supporto per funzionalità multimediali, che comprende sia audio e video. Questa guida è incentrata sul audio in Android e la riproduzione e la registrazione tramite il lettore audio predefinito e le classi di registrazione, nonché l'API di basso livello audio dell'audio. Utilizzo degli eventi Audio broadcast da altre applicazioni, vengono inoltre descritti in modo che gli sviluppatori possono compilare applicazioni ben progettate.




##  <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[Notifiche](~/android/app-fundamentals/notifications/index.md)

Questa sezione viene illustrato come implementare le notifiche locali e remote in xamarin. Android. Vengono descritti i vari elementi dell'interfaccia utente di una notifica di Android e descrive l'API di coinvolti con la creazione e visualizzazione di una notifica. Per le notifiche remote, vengono illustrati entrambi Google Cloud Messaging e Firebase Cloud Messaging. Sono inclusi esempi dettagliati, procedure dettagliate e il codice.



##  <a name="touchandroidapp-fundamentalstouchindexmd"></a>[Tocco](~/android/app-fundamentals/touch/index.md)

Questa sezione illustra che i concetti e i dettagli dell'implementazione di movimenti di tocco in Android. API di tocco sono introdotte e spiegate seguite da un'analisi di riconoscitori di movimento.



##  <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[Stack HttpClient e SSL/TLS](~/android/app-fundamentals/http-stack.md)

In questa sezione vengono illustrati i selettori di HttpClient Stack e implementazione di SSL/TLS per Android. Queste impostazioni determinano l'implementazione di HttpClient e SSL/TLS che verrà usato dalle App xamarin. Android.


##  <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[Scrittura di applicazioni reattive](writing-responsive-apps.md)

Questo articolo illustra come usare il threading per mantenere reattiva un'applicazione xamarin. Android spostando le attività a esecuzione prolungata in un thread in background.