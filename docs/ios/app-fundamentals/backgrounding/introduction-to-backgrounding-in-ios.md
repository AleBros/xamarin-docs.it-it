---
title: Introduzione a background in iOS
description: "Questo documento descrive l'elaborazione in background in iOS: stati dell'applicazione, i metodi del ciclo di vita dell'applicazione e all'aggiornamento di app in background."
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/24/2018
ms.openlocfilehash: 621a2d25a8b800ad95be2c8b71a5e6cdf6abca21
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351203"
---
# <a name="introduction-to-backgrounding-in-ios"></a>Introduzione a background in iOS

iOS regola l'elaborazione attentamente in background e sono disponibili tre metodi per la relativa implementazione:

-  **Registrare un'attività in Background** -se un'applicazione deve effettuare un'attività importante, è possibile chiedere iOS non interrompere l'attività quando l'applicazione viene spostata in background. Ad esempio, un'applicazione potrebbe essere necessario completare la registrazione di un utente o terminarne scaricando un file di grandi dimensioni.
-  **Registra come applicazione in Background necessarie** -un'app può registrare come un tipo specifico di applicazione che è noto, i requisiti di elaborazione in background in specifici, ad esempio *Audio* , *VoIP* ,  *Accessori esterni* , *Newsstand* , e *percorso* . Queste applicazioni sono consentite in background continua l'elaborazione dei privilegi, purché che eseguono attività che sono all'interno dei parametri del tipo di applicazione registrata.
-  **Abilitare gli aggiornamenti in Background** -le applicazioni possono attivare gli aggiornamenti in background con *area monitoraggio* o in attesa degli *modifiche significative percorso* . A partire da iOS 7, le applicazioni possono anche registrarsi per aggiornare il contenuto in background usando *recupero in Background* oppure *notifiche Remote* .


## <a name="application-states-and-application-delegate-methods"></a>Stati dell'applicazione e i metodi delegato dell'applicazione

Prima di approfondire il codice per l'elaborazione in iOS in background, è necessario comprendere la modalità elaborazione in background in effetti il ciclo di vita di un'applicazione iOS.

Il ciclo di vita dell'applicazione iOS è una raccolta di stati dell'applicazione e i metodi per lo spostamento tra di essi. Un'applicazione esegue la transizione tra stati in base il backgrounding requisiti dell'applicazione e il comportamento dell'utente. Lo spostamento è illustrato nel diagramma seguente:

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "Diagramma di stati dell'applicazione e i metodi delegato dell'applicazione")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

-  **Non è in esecuzione** -l'applicazione non è ancora stata avviata nel dispositivo.
-  **Esecuzione/attivo** -l'applicazione è sullo schermo ed è l'esecuzione di codice in primo piano.
-  **Inattivo** -l'applicazione verrà interrotto da una chiamata in arrivo, text o altre interruzioni.
-  **Supportata da un background** -l'applicazione viene spostata in background e continua l'esecuzione di codice in background.
-  **Suspended** : se l'applicazione è associato alcun codice per l'esecuzione in background o se tutto il codice è stata completata, l'app sarà *Suspended* dal sistema operativo. Processo di un'applicazione sospesa viene mantenuto attivo, ma l'applicazione è in grado di eseguire qualsiasi codice in questo stato.
-  **Tornare alla non esecuzione/terminazione (Rare)** : in alcuni casi, il processo dell'applicazione viene eliminata definitivamente e l'applicazione torna al *non è in esecuzione* dello stato. Si verifica nelle situazioni di memoria insufficiente, o se l'utente termina manualmente l'applicazione.


Dall'introduzione del supporto per il multitasking, raramente termina inattività delle applicazioni iOS e mantiene invece dei processi *Suspended* in memoria. Mantiene attivo un processo di applicazione assicura che l'applicazione viene avviata rapidamente alla successiva che l'utente lo apre. Significa anche le applicazioni è possono spostare liberamente dal *degli elementi sospesi* dello stato nel *Backgrounded* stato senza disegno delle risorse di sistema. iOS 7 sfrutta questa funzionalità con nuove API che consentono alle applicazioni di sospendere le attività in background quando il dispositivo va in sospensione, il contenuto di aggiornamento direttamente da background senza interazione dell'utente e altro ancora. Verranno esaminate le nuove API nel [tecniche di elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md).

## <a name="application-lifecycle-methods"></a>Metodi del ciclo di vita delle applicazioni

Quando un'app viene modificato lo stato, iOS notifica all'applicazione tramite i metodi di eventi nel `AppDelegate` classe:

-  `OnActivated` -Si tratta la prima volta viene avviata l'applicazione, e ogni volta che l'app torna in primo piano. Questa è la posizione in cui inserire il codice che deve essere eseguita ogni volta che l'app viene aperta.
-  `OnResignActivation` -Se l'utente riceve un'interruzione, ad esempio un testo o una chiamata telefonica, questo metodo viene chiamato e l'app viene temporaneamente disattivato. L'utente deve accettare la telefonata, l'app verrà inviata allo sfondo.
-  `DidEnterBackground` -Chiamata quando l'app passa allo stato backgrounded, questo metodo fornisce a un'applicazione circa cinque secondi per la preparazione per la terminazione possibili. Utilizzare questa volta per salvare le attività e dati utente e rimuovere informazioni riservate nella schermata.
-  `WillEnterForeground` -Quando un utente restituisce a un'applicazione supportata da un background o sospesa e lo avvia in primo piano, `WillEnterForeground` venga chiamato. Questo è il tempo necessario per preparare l'app da eseguire in primo piano da qualsiasi stato salvato durante la riattivazione `DidEnterBackground` .  `OnActivated` verrà chiamato immediatamente dopo il completamento del metodo.
-  `WillTerminate` -L'applicazione viene arrestata e il processo viene eliminato definitivamente. Questo metodo viene chiamato solo se il multitasking non è disponibile nel dispositivo o per la versione del sistema operativo, se la memoria è insufficiente o se l'utente termina manualmente un'applicazione backgrounded. Si noti che sospese le applicazioni che venire terminate non chiamerà `WillTerminate` .


Il diagramma seguente illustra come gli Stati dell'applicazione e i metodi del ciclo di vita si integrino:

 [![](introduction-to-backgrounding-in-ios-images/image2.png "Questo diagramma illustra come gli Stati dell'applicazione e i metodi del ciclo di vita si integrino")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>Controlli utente di background in iOS

iOS 7 ha introdotto diverse funzionalità per offrire agli utenti maggiore controllo sulla stato backgrounded di un'applicazione. Sia il cambio modalità per App e l'impostazione di aggiornamento di App in Background influenzano il ciclo di vita dell'applicazione.

### <a name="app-switcher"></a>Cambio modalità per App

Cambio modalità per l'App è una caratteristica importante del controllo introdotta in iOS 7. Viene avviata tramite doppio tocco il **Home** pulsante e Mostra le applicazioni cui processi sono attivi:

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "Lo spostamento tra le app tramite il cambio modalità per App")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

Usa la selezione di App, agli utenti di scorrere gli snapshot di tutte le applicazioni supportata da un background o sospesi. Se si tocca un'applicazione, lo avvia in primo piano. Scorrendo rapidamente backup rimuove l'applicazione di background, il processo verrà terminato. Verrà usato da vicino la selezione di App nel [Demo del ciclo di vita dell'applicazione iOS](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md) nella sezione successiva.

> [!IMPORTANT]
> La selezione di App non include una differenza tra le applicazioni supportata da un background e sospese.



### <a name="background-app-refresh-settings"></a>Impostazioni di aggiornamento di App in background

iOS 7 aumenta il controllo utente al ciclo di vita dell'applicazione, consentendo agli utenti di rifiutare esplicitamente l'elaborazione in background in Applications [registrato per l'elaborazione in background](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md). *Ciò non impedisce l'esecuzione di attività in background delle applicazioni*.

Gli utenti possono modificare questa impostazione, passare a <span class="uiitem">Impostazioni > generali > Aggiorna App in Background</span> e modifica i privilegi backgrounding per un'applicazione selezionata. Se l'aggiornamento di App in Background è impostata su off, l'applicazione verrà sospesi immediatamente al momento dell'immissione di background e impedito di eseguire qualsiasi elaborazione in background:

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "Impostazioni di aggiornamento di App in background")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

Gli sviluppatori possono controllare lo stato in Background Aggiorna l'applicazione con il `BackgroundRefreshStatus` API. Per un esempio, vedere la [recipe controlla impostazione di aggiornamento in Background](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/check_background_refresh_setting).

Che avremo illustrato le nozioni di base del ciclo di vita dell'applicazione iOS e le funzionalità per controllare il ciclo di vita dell'applicazione. Successivamente, di seguito viene illustrato il ciclo di vita dell'applicazione iOS in azione.

