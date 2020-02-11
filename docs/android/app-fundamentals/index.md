---
title: Nozioni fondamentali sull'applicazione Xamarin.Android
description: Concetti di base sulle applicazioni
ms.prod: xamarin
ms.assetid: 935B8BFE-23B7-4239-5C87-F4A503B889CB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: eb581d68f3b7e57975b6979fe1005b1fac411ec8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019227"
---
# <a name="xamarinandroid-application-fundamentals"></a>Nozioni fondamentali sull'applicazione Xamarin.Android

Questa sezione fornisce una guida su alcune delle attività più comuni o sui concetti che gli sviluppatori devono tenere presenti durante lo sviluppo di applicazioni Android.

## <a name="accessibilityandroidapp-fundamentalsaccessibilitymd"></a>[Accessibilità](~/android/app-fundamentals/accessibility.md)

Questa pagina descrive come usare le API di accessibilità Android per creare app in base all' [elenco di controllo di accessibilità](~/cross-platform/app-fundamentals/accessibility.md).

## <a name="understanding-android-api-levelsandroidapp-fundamentalsandroid-api-levelsmd"></a>[Informazioni sui livelli dell'API Android](~/android/app-fundamentals/android-api-levels.md)

Questa guida descrive come Android usa i livelli API per gestire la compatibilità delle app in versioni diverse di Android e spiega come configurare le impostazioni del progetto Xamarin.Android per distribuire questi livelli API nell'app. Questa guida illustra anche come scrivere codice di runtime che gestisce i diversi livelli API e fornisce un elenco di riferimento di tutti i livelli di API Android, i numeri di versione (ad esempio Android 8,0), i nomi di codice Android (ad esempio, Oreo) e i codici di versione di compilazione.

## <a name="resources-in-androidandroidapp-fundamentalsresources-in-androidindexmd"></a>[Risorse in Android](~/android/app-fundamentals/resources-in-android/index.md)

In questo articolo viene introdotto il concetto di risorse Android in Xamarin.Android e viene illustrato come usarle. Illustra come usare le risorse nell'applicazione Android per supportare la localizzazione dell'applicazione e più dispositivi, incluse dimensioni e densità dello schermo diverse.

## <a name="activity-lifecycleandroidapp-fundamentalsactivity-lifecycleindexmd"></a>[Ciclo di vita dell'attività](~/android/app-fundamentals/activity-lifecycle/index.md)

Le attività sono un blocco predefinito fondamentale di applicazioni Android e possono esistere in diversi Stati. Il ciclo di vita dell'attività inizia con la creazione di un'istanza e termina con la distruzione e include molti Stati. Quando un'attività modifica lo stato, viene chiamato il metodo di evento del ciclo di vita appropriato, che notifica l'attività della modifica dello stato imminente e consente di eseguire il codice per adattarsi a tale modifica. In questo articolo viene esaminato il ciclo di vita delle attività e viene illustrata la responsabilità di un'attività nel corso di ognuna di queste modifiche allo stato in modo che faccia parte di un'applicazione affidabile e ben comportata.

## <a name="localizationandroidapp-fundamentalslocalizationmd"></a>[Localizzazione](~/android/app-fundamentals/localization.md)

Questo articolo illustra come localizzare un Xamarin.Android in altre lingue traducendo le stringhe e fornendo immagini alternative.

## <a name="servicesandroidapp-fundamentalsservicesindexmd"></a>[Servizi](~/android/app-fundamentals/services/index.md)

Questo articolo illustra i servizi Android, che sono componenti Android che consentono di eseguire il lavoro in background. Vengono illustrati i diversi scenari per cui sono adatti i servizi e viene illustrato come implementarli sia per eseguire attività in background con esecuzione prolungata che per fornire un'interfaccia per le chiamate di procedure remote.

## <a name="broadcast-receiversandroidapp-fundamentalsbroadcast-receiversmd"></a>[Ricevitori di trasmissione](~/android/app-fundamentals/broadcast-receivers.md)

Questa guida illustra come creare e usare i ricevitori di trasmissioni, un componente Android che risponde alle trasmissioni a livello di sistema, in Xamarin.Android.

## <a name="permissionsandroidapp-fundamentalspermissionsmd"></a>[Autorizzazioni](~/android/app-fundamentals/permissions.md)

È possibile usare il supporto per gli strumenti integrato in Visual Studio per Mac o Visual Studio per creare e aggiungere autorizzazioni al manifesto Android. Questo documento descrive come aggiungere autorizzazioni in Visual Studio e Xamarin Studio.

## <a name="graphics-and-animationandroidapp-fundamentalsgraphics-and-animationmd"></a>[Grafica e animazione](~/android/app-fundamentals/graphics-and-animation.md)

Android offre un framework molto ricco e diversificato per supportare la grafica e le animazioni 2D. Questo documento introduce questi Framework e illustra come creare grafica e animazioni personalizzate e usarle in un'applicazione Xamarin.Android.

## <a name="cpu-architecturesandroidapp-fundamentalscpu-architecturesmd"></a>[Architetture CPU](~/android/app-fundamentals/cpu-architectures.md)

Xamarin.Android supporta diverse architetture della CPU, inclusi i dispositivi a 32 bit e a 64 bit. Questo articolo illustra come indirizzare un'app a una o più architetture CPU supportate da Android.

## <a name="handling-rotationandroidapp-fundamentalshandling-rotationmd"></a>[Gestione della rotazione](~/android/app-fundamentals/handling-rotation.md)

Questo articolo descrive come gestire le modifiche dell'orientamento del dispositivo in Xamarin.Android. Viene illustrato come usare il sistema di risorse Android per caricare automaticamente le risorse per un particolare orientamento del dispositivo e come gestire a livello di codice le modifiche dell'orientamento. Descrive quindi le tecniche per mantenere lo stato quando un dispositivo viene ruotato.

## <a name="android-audioandroidapp-fundamentalsandroid-audiomd"></a>[Audio Android](~/android/app-fundamentals/android-audio.md)

Il sistema operativo Android offre un supporto completo per i contenuti multimediali, che comprendono audio e video. Questa guida è incentrata sull'audio in Android e descrive la riproduzione e la registrazione di audio con le classi di registratore audio e registratore audio predefinite, nonché l'API audio di basso livello. Illustra anche l'uso di eventi audio trasmessi da altre applicazioni, in modo che gli sviluppatori possano compilare applicazioni ben funzionanti.

## <a name="notificationsandroidapp-fundamentalsnotificationsindexmd"></a>[Notifiche](~/android/app-fundamentals/notifications/index.md)

Questa sezione illustra come implementare le notifiche locali e remote in Xamarin.Android. Vengono descritti i vari elementi dell'interfaccia utente di una notifica Android e viene illustrata la creazione e la visualizzazione di una notifica da parte dell'API. Per le notifiche remote, vengono illustrate sia Google Cloud Messaging che la messaggistica cloud Firebase. Sono incluse procedure dettagliate ed esempi di codice.

## <a name="touchandroidapp-fundamentalstouchindexmd"></a>[Tocco](~/android/app-fundamentals/touch/index.md)

Questa sezione illustra i concetti e i dettagli relativi all'implementazione dei movimenti tocco in Android. Le API Touch vengono introdotte e spiegate seguito da un'esplorazione dei riconoscitori di movimento.

## <a name="httpclient-stack-and-ssltlsandroidapp-fundamentalshttp-stackmd"></a>[Stack HttpClient e SSL/TLS](~/android/app-fundamentals/http-stack.md)

Questa sezione illustra i selettori di implementazione di HttpClient stack e SSL/TLS per Android. Queste impostazioni determinano l'implementazione di HttpClient e SSL/TLS che verrà usata dalle app Xamarin.Android.

## <a name="writing-responsive-applicationswriting-responsive-appsmd"></a>[Scrittura di applicazioni reattive](writing-responsive-apps.md)

Questo articolo illustra come usare il threading per rendere la risposta di un'applicazione Xamarin.Android spostando attività a esecuzione prolungata in un thread in background.
