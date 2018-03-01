---
title: Nozioni fondamentali sulle applicazioni
description: Concetti di base dell'applicazione
ms.topic: article
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 4abb8c823c62bc62fd2e6f717cc1b5bde9057e4e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="application-fundamentals"></a>Nozioni fondamentali sulle applicazioni

In questa sezione viene fornita una Guida su alcune delle più comuni attività di cose o concetti che gli sviluppatori devono tenere conto durante lo sviluppo di applicazioni Android.

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[Accessibilità](~/android/app-fundamentals/accessibility.md)

Questa pagina viene descritto come utilizzare le API Android di accessibilità per compilare le applicazioni in base al [elenco di controllo di accesso facilitato](~/cross-platform/app-fundamentals/accessibility.md).

##  <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[Informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md)

Questa guida descrive l'utilizzo Android livelli API per la gestione di compatibilità delle applicazioni tra le diverse versioni di Android, e viene descritto come configurare le impostazioni di progetto xamarin per distribuire questi livelli API nell'app. Inoltre, questa guida viene illustrato come scrivere il codice di runtime che interagisce con diversi livelli di API e fornisce un elenco di riferimento di tutti i livelli API Android, i numeri di versione (ad esempio Android 8.0), i nomi di codice Android (ad esempio Oreo) e i codici di versione di compilazione.



##  <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Risorse in Android](~/android/app-fundamentals/resources-in-android/index.md)

In questo articolo introduce il concetto di Android risorse nei documenti di xamarin e sul loro utilizzo. Viene illustrato come utilizzare le risorse disponibili nell'applicazione Android per supportare la localizzazione dell'applicazione e più dispositivi, tra cui densità e dimensioni dello schermo diverse.




##  <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[Ciclo di vita dell'attività](~/android/app-fundamentals/activity-lifecycle/index.md)

Le attività sono un blocco predefinito fondamentale di applicazioni Android e possono essere presenti in un numero di stati diversi. Il ciclo di vita di attività inizia con la creazione di istanze e termina con distruzione e include molti stati intermedi. Quando un'attività viene modificato, viene chiamato il metodo di evento del ciclo di vita appropriato, l'attività dell'imminente modifica lo stato di notifica e in modo che possa eseguire il codice per adattarsi a tale modifica. In questo articolo esamina il ciclo di vita delle attività e illustra la responsabilità che disponga di un'attività durante ognuna di queste modifiche di stato da parte di un'applicazione ben progettata e affidabile.

##  <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[Localizzazione](~/android/app-fundamentals/localization.md)

In questo articolo viene illustrato come localizzare un xamarin in altri linguaggi di traduzione di stringhe e fornendo immagini alternative.

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[Servizi](~/android/app-fundamentals/services/index.md)

In questo articolo vengono illustrati i servizi Android, che sono componenti di Android che consentono di lavoro da eseguire in background. Vengono illustrati i diversi scenari appropriati per i servizi e viene illustrato come implementarle entrambe per l'esecuzione di attività in background con esecuzione prolungata nonché per fornire un'interfaccia per le chiamate di procedura remota.

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[Ricevitori di broadcast](~/android/app-fundamentals/broadcast-receivers.md)

Questa guida illustra come creare e utilizzare broadcast ricevitori, un componente di Android che risponde alle trasmissioni a livello di sistema, in xamarin.



##  <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[Autorizzazioni](~/android/app-fundamentals/permissions.md)

È possibile utilizzare il supporto di strumenti integrato in Visual Studio per Mac o Visual Studio per creare e aggiungere autorizzazioni per il file manifesto Android. Questo documento descrive come aggiungere le autorizzazioni in Visual Studio e Xamarin Studio.



##  <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[Grafica e animazione](~/android/app-fundamentals/graphics-and-animation.md)

Android fornisce un framework molto avanzato e diverse per il supporto grafica 2D e le animazioni. Questo documento introduce questi Framework e viene illustrato come creare animazioni e immagini personalizzate e usarle in un'applicazione di xamarin.


##  <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[Architetture della CPU](~/android/app-fundamentals/cpu-architectures.md)

Xamarin supporta diverse architetture della CPU, inclusi i dispositivi a 32 bit e 64 bit. In questo articolo viene illustrato come destinazione un'applicazione per uno o più architetture della CPU supportata Android.




##  <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[Rotazione di gestione](~/android/app-fundamentals/handling-rotation.md)

In questo articolo viene descritto come gestire le modifiche di orientamento di dispositivo in xamarin. Viene illustrato come utilizzare con il sistema Android risorse per caricare automaticamente risorse per un orientamento del dispositivo specifico nonché come gestire a livello di programmazione orientamento cambia. Quindi vengono descritte le tecniche per mantenere lo stato quando un dispositivo viene ruotato.



##  <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Android Audio](~/android/app-fundamentals/android-audio.md)

Il sistema operativo Android fornisce supporto completo per servizi multimediali che comprende sia audio e video. Questa guida è incentrata sulla audio in Android e copre riprodurre e registrare audio tramite lettori audio incorporati e le classi di registrazione, nonché l'API audio di basso livello. Utilizzo degli eventi Audio broadcast da altre applicazioni, vengono inoltre illustrate in modo che gli sviluppatori possono compilare applicazioni ben progettate.




##  <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[Notifiche](~/android/app-fundamentals/notifications/index.md)

In questa sezione viene illustrato come implementare le notifiche locali e remote in xamarin. Vengono descritti i vari elementi dell'interfaccia utente di una notifica di Android e descrive l'API del coinvolto nella creazione e visualizzazione di una notifica. Per le comunicazioni remote, vengono spiegate di Google Cloud Messaging sia Firebase Cloud Messaging. Procedura dettagliati procedure dettagliate ed esempi di codice sono inclusi.



##  <a name="touchandroidapp-fundamentalstouchindexmd"></a>[Tocco](~/android/app-fundamentals/touch/index.md)

Questa sezione illustra che i concetti e i dettagli dell'implementazione di toccano movimenti su Android. API tocco sono introdotte e sono illustrate di seguito da un'analisi del riconoscimento di movimento.



##  <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[Stack HttpClient e SSL/TLS](~/android/app-fundamentals/http-stack.md)

In questa sezione vengono illustrati i selettori HttpClient Stack e l'implementazione di SSL/TLS per Android. Queste impostazioni determinano l'implementazione HttpClient e SSL/TLS che verrà utilizzato da app xamarin.


##  <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[Scrittura di applicazioni reattive](writing-responsive-apps.md)

In questo articolo viene illustrato come utilizzare il threading per mantenere la reattività di un'applicazione di xamarin spostando le attività a esecuzione prolungata in un thread in background.