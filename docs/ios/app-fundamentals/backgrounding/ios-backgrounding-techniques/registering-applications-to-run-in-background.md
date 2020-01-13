---
title: Registrazione delle app Xamarin.iOS per l'esecuzione in background
description: Questo documento descrive come registrare un'applicazione Xamarin.iOS per l'esecuzione in background. Questo articolo illustra app audio, app VoIP, accessori esterni e Bluetooth e altro ancora.
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 61b7926f28253acbcc45bc204c466d76a00c72b0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010826"
---
# <a name="registering-xamarinios-apps-to-run-in-the-background"></a>Registrazione delle app Xamarin.iOS per l'esecuzione in background

La registrazione delle singole attività per i privilegi in background funziona per alcune applicazioni, ma cosa accade se un'applicazione viene costantemente chiamata per eseguire attività importanti e a esecuzione prolungata, ad esempio per ottenere indicazioni per l'utente tramite GPS? Le applicazioni, ad esempio, devono essere registrate come applicazioni necessarie in background note.

La registrazione di un'app segnala a iOS che l'applicazione deve disporre di privilegi speciali necessari per eseguire le attività in background.

## <a name="application-registration-categories"></a>Categorie di registrazione delle applicazioni

Le app registrate possono rientrare in diverse categorie:

- I lettori di musica **audio** e altre applicazioni che funzionano con contenuto audio possono essere registrati per continuare a riprodurre audio anche quando l'app non è più in primo piano. Se un'app in questa categoria tenta di eseguire operazioni diverse da riproduzione audio o download in background, iOS lo terminerà.
- Le applicazioni VoIP-voice over Internet Protocol **(VoIP)** ottengono gli stessi privilegi concessi alle applicazioni audio per la continuazione dell'elaborazione dell'audio in background. Sono inoltre autorizzati a rispondere in base alle esigenze per i servizi VoIP che li alimentano per mantenerne attive le connessioni.
- **Accessori esterni e Bluetooth** : riservati per le applicazioni che devono comunicare con dispositivi Bluetooth e altri accessori hardware esterni, la registrazione in queste categorie consente all'app di rimanere connessa all'hardware.
- **Edicola** : un'applicazione in edicola può continuare a sincronizzare il contenuto in background.
- **Località** : le applicazioni che usano i dati del percorso di rete o GPS possono inviare e ricevere gli aggiornamenti delle posizioni in background.
- **Fetch (iOS 7 +)** : un'applicazione registrata per i privilegi di recupero in background può verificare un provider per il nuovo contenuto a intervalli regolari, presentando all'utente il contenuto aggiornato quando torna all'applicazione.
- **Notifiche remote (iOS 7 +)** : le applicazioni possono registrarsi per ricevere notifiche da un provider e utilizzare la notifica per avviare un aggiornamento prima che l'applicazione venga aperta dall'utente. Le notifiche possono essere disponibili sotto forma di notifiche push o scegliere di riattivare l'applicazione in modo invisibile all'utente.

Le applicazioni possono essere registrate impostando la proprietà **modalità in background richiesta** nel file *info. plist*dell'applicazione. Un'applicazione può registrarsi nel numero di categorie necessario:

 [![](registering-applications-to-run-in-background-images/bgmodes.png "Setting the background modes")](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

Per una guida dettagliata alla registrazione di un'applicazione per gli aggiornamenti del percorso in background, vedere la [procedura dettagliata](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md)relativa alla posizione in background.

## <a name="application-does-not-run-in-background-property"></a>L'applicazione non viene eseguita in proprietà background

Un'altra proprietà che può essere impostata in *info. plist* è che l' *applicazione non viene eseguita in background*o `UIApplicationExitsOnSuspend` proprietà:

 [![](registering-applications-to-run-in-background-images/plist.png "Disabling Background Running")](registering-applications-to-run-in-background-images/plist.png#lightbox)

Questo ha lo stesso effetto dell'impostazione dell'impostazione di aggiornamento dell'app in background su off in iOS 7 +, ad eccezione del fatto che può essere modificato solo sul lato Developer ed è disponibile per iOS 4 e versioni successive. L'applicazione verrà sospesa immediatamente dopo l'immissione dello sfondo e non sarà in grado di eseguire alcuna elaborazione.

Usare questa proprietà se l'applicazione non è progettata per gestire l'elaborazione in background, in quanto consente di evitare comportamenti imprevisti.

## <a name="background-fetch-and-remote-notifications"></a>Recupero in background e notifiche remote

Il recupero in background e le notifiche remote sono categorie di registrazione speciali introdotte in iOS 7. Queste categorie consentono alle applicazioni di ricevere nuovo contenuto da un provider e di aggiornarlo in background. Nella sezione successiva vengono esaminati in modo più dettagliato il recupero e le notifiche remote e viene inoltre introdotta la consapevolezza della posizione come mezzo per l'aggiornamento di un'applicazione in background in iOS 6.
