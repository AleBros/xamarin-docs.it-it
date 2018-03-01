---
title: Registrazione delle applicazioni per l'esecuzione in Background
ms.topic: article
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: dddd1ad4ae70b97f17ba71a7e96b553759e35695
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="registering-applications-to-run-in-the-background"></a>Registrazione delle applicazioni per l'esecuzione in Background

Registrazione di singole attività per sfondo privilegi funziona per alcune applicazioni, ma cosa accade se un'applicazione costantemente viene chiamata per eseguire attività importanti, con esecuzione prolungata, ad esempio ottenere indicazioni per l'utente attraverso GPS? Applicazioni di questo tipo invece devono essere registrate come le applicazioni in background necessarie.

Registrazione di un'app segnala a iOS che l'applicazione deve avere privilegi speciali necessari per eseguire attività in background.

## <a name="application-registration-categories"></a>Categorie di registrazione dell'applicazione

App registrata può rientrano in diverse categorie:

-  **Audio** -lettori di musica e altre applicazioni che funzionano con contenuto audio possono essere registrati per continuare la riproduzione audio anche quando l'app non è più in primo piano. Se un'app in questa categoria tenta di eseguire alcuna operazione diverso da riprodurre l'audio o di download in background, iOS verrà chiuderla.
-  **VoIP** -applicazioni Voice Over Internet Protocol (VoIP) ottengono gli stessi privilegi concessi alle applicazioni audio per mantenere l'elaborazione di audio in background. Sono consentiti anche per rispondere alle esigenze ai servizi VoIP fondamentali in modo da mantenere attive le connessioni.
-  **Esterni accessori e Bluetooth** -riservato per le applicazioni che devono comunicare con dispositivi Bluetooth e altri accessori hardware esterno, la registrazione in queste categorie consente all'app di rimanere connessi all'hardware.
-  **Newsstand** -Newsstand di un'applicazione può continuare a sincronizzare il contenuto in background.
-  **Percorso** : applicazioni che utilizzano di GPS o dati di percorso di rete inviare e ricevere gli aggiornamenti di percorso in background.
-  **Operazione di recupero (iOS 7 +)** : un'applicazione registrato per un provider per nuovo contenuto controllare i privilegi di recupero in background a intervalli regolari, presentate all'utente con contenuto aggiornato quando viene restituita all'applicazione.
-  **Le notifiche remote (iOS 7 +)** -applicazioni possono registrarsi per ricevere notifiche da un provider e utilizzare la notifica per avviare un aggiornamento prima che l'utente apre l'applicazione. Le notifiche possono provenire sotto forma di notifiche push o consenso esplicito per riattivare l'applicazione automaticamente.


Le applicazioni possono essere registrate mediante l'impostazione di **la modalità di Background necessarie** proprietà dell'applicazione *Info. plist*. Un'applicazione può registrare in tutte le categorie richiede:

 [ ![](registering-applications-to-run-in-background-images/bgmodes.png "Impostare la modalità di background")](registering-applications-to-run-in-background-images/bgmodes.png)

Per una Guida dettagliata alla registrazione di un'applicazione per gli aggiornamenti di percorso in background, vedere il [procedura dettagliata percorso Background](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md).

## <a name="application-does-not-run-in-background-property"></a>Applicazione non viene eseguito in Background proprietà

Un'altra proprietà che può essere impostata nella *Info. plist* è il *applicazione non viene eseguito in background*, o `UIApplicationExitsOnSuspend` proprietà:

 [ ![](registering-applications-to-run-in-background-images/plist.png "La disabilitazione di Background in esecuzione")](registering-applications-to-run-in-background-images/plist.png)

Questo ha lo stesso effetto esattamente come l'impostazione aggiornamento App sfondo su off in iOS 7 +, ad eccezione del fatto che può essere modificata solo sul lato di sviluppatore ed è disponibile per iOS 4 e versioni successive. L'applicazione verrà sospeso immediatamente dopo l'immissione di background e non sarà in grado di eseguire qualsiasi elaborazione.

Utilizzare questa proprietà se l'applicazione non è progettato per gestire l'elaborazione in background, perché consente di evitare un comportamento imprevisto.

## <a name="background-fetch-and-remote-notifications"></a>Recupero in background e notifiche Remote

Recupero in background e notifiche remote sono le categorie di registrazione speciali introdotte in iOS 7. Queste categorie consentono alle applicazioni di ricevere di nuovo contenuto da un provider e l'aggiornamento in background. La sezione successiva viene esaminata fetch e notifiche remote in modo più dettagliato e introduce anche il riconoscimento presenza in come mezzo per l'aggiornamento di un'applicazione in background in iOS 6.
