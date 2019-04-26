---
title: La registrazione di App xamarin. IOS per l'esecuzione in Background
description: Questo documento descrive come registrare un'applicazione xamarin. IOS per l'esecuzione in background. Viene descritto Audio App, VoIP le app, accessori esterni e bluetooth e altro ancora.
ms.prod: xamarin
ms.assetid: 8F89BE63-DDB5-4740-A69D-F60AEB21150D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a0a66571d0249ef6fd65ff382f14c38f48a8af37
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61393702"
---
# <a name="registering-xamarinios-apps-to-run-in-the-background"></a>La registrazione di App xamarin. IOS per l'esecuzione in Background

La registrazione di singole attività di background works privilegi per alcune applicazioni, ma cosa accade se un'applicazione viene chiamata costantemente per eseguire attività importanti e a esecuzione prolungata, ad esempio per ottenere indicazioni per l'utente attraverso GPS? Le applicazioni come i seguenti invece devono essere registrate come le applicazioni in background necessarie.

Registrazione di un'app segnala ai dispositivi iOS che l'applicazione deve essere assegnata speciali dei privilegi necessari per eseguire attività in background.

## <a name="application-registration-categories"></a>Categorie di registrazione dell'applicazione

Le app registrate possono rientrare in diverse categorie:

-  **Audio** -gli assegnatari di musica e altre applicazioni che funzionano con contenuti audio possono essere registrati per continuare la riproduzione audio anche quando l'app non è più in primo piano. Se un'app in questa categoria tenta di eseguire qualsiasi operazione diversa da riprodurre l'audio o download mentre è in background, iOS verrà terminarlo.
-  **VoIP** -applicazioni Voice Over Internet Protocol (VoIP) ottengono gli stessi privilegi concessi alle applicazioni audio per mantenere l'elaborazione di audio in background. Sono consentiti anche per rispondere come necessario per i servizi VoIP se l'alimentazione, in modo da mantenere le connessioni attivo.
-  **External accessori e Bluetooth** -riservato per le applicazioni che devono comunicare con dispositivi Bluetooth e altri accessori di hardware esterno, in queste categorie di registrazione consente all'app di rimanere connessi all'hardware.
-  **Newsstand** -Newsstand di un'applicazione può continuare a sincronizzare il contenuto in background.
-  **Percorso** - applicazioni che utilizzano di GPS o dati sulla località rete possono inviare e ricevere aggiornamenti della posizione in background.
-  **Operazione di recupero (iOS 7 +)** : un'applicazione di registrazione per i privilegi di recupero in background possono selezionare un provider per nuovo contenuto a intervalli regolari, presentazione all'utente di contenuto aggiornato quando vengono restituiti all'applicazione.
-  **Notifiche remote (iOS 7 +)** -applicazioni possono registrarsi per ricevere notifiche da un provider e usare la notifica per avviare un aggiornamento prima che l'utente apre l'applicazione. Le notifiche possono provenire sotto forma di notifiche push o consenso esplicito per riattivare l'applicazione in modo invisibile.


Le applicazioni possono essere registrate impostando il **modalità in Background obbligatorie** proprietà dell'applicazione *Info. plist*. Un'applicazione può registrare in tutte le categorie richiede:

 [![](registering-applications-to-run-in-background-images/bgmodes.png "Impostare la modalità in background")](registering-applications-to-run-in-background-images/bgmodes.png#lightbox)

Per una Guida dettagliata per la registrazione di un'applicazione per aggiornamenti della posizione in background, vedere la [procedura dettagliata di posizione in Background](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md).

## <a name="application-does-not-run-in-background-property"></a>Applicazione non viene eseguito in Background proprietà

Un'altra proprietà che può essere impostata nella *Info. plist* è la *dell'applicazione non viene eseguito in background*, o `UIApplicationExitsOnSuspend` proprietà:

 [![](registering-applications-to-run-in-background-images/plist.png "La disabilitazione in Background in esecuzione")](registering-applications-to-run-in-background-images/plist.png#lightbox)

Ciò ha lo stesso effetto esattamente come l'impostazione in Background Aggiorna App sia stata disattivata in iOS 7 +, ad eccezione del fatto può essere modificata solo sul lato per gli sviluppatori ed è disponibile per iOS 4 e versioni successive. L'applicazione verrà sospesa immediatamente dopo aver immesso il background e non sarà in grado di eseguire qualsiasi elaborazione.

Utilizzare questa proprietà se l'applicazione non è progettata per gestire l'elaborazione in background, poiché aiuta a evitare un comportamento imprevisto.

## <a name="background-fetch-and-remote-notifications"></a>Recupero in background e le notifiche Remote

Recupero in background e le notifiche remote sono categorie di registrazione speciale introdotte in iOS 7. Queste categorie consentono alle applicazioni ricevere il nuovo contenuto da un provider e aggiornare in background. Nella sezione successiva illustra fetch e notifiche remote in modo più dettagliato e introduce anche riconoscimento presenza come mezzo per l'aggiornamento di un'applicazione in background in iOS 6.
