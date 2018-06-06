---
title: Introduzione a Backgrounding in iOS
description: "Questo documento descrive backgrounding in iOS: stati dell'applicazione, i metodi del ciclo di vita dell'applicazione e all'aggiornamento di app in background."
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: c8084d8e218ba8e3468529795aaa5fd4eae30947
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783639"
---
# <a name="introduction-to-backgrounding-in-ios"></a>Introduzione a Backgrounding in iOS

iOS regola strettamente l'elaborazione in background e offre tre approcci per la sua implementazione:

-  **Registrare un'attività in Background** -se un'applicazione deve completare un'attività importante, è possibile chiedere iOS non interrompere l'attività quando l'applicazione viene spostata in background. Ad esempio, un'applicazione potrebbe essere necessario completare l'accesso a un utente o completare il download di un file di grandi dimensioni.
-  **Registra come applicazione in Background necessarie** -un'app può registrare come un tipo specifico di applicazione che è noto, requisiti backgrounding specifici, ad esempio *Audio* , *VoIP* ,  *Accessori esterno* , *Newsstand* , e *percorso* . Queste applicazioni sono consentite fino a quando sono in esecuzione attività all'interno dei parametri del tipo di applicazione registrata, privilegi di elaborazione in background continua.
-  **Abilitare gli aggiornamenti in Background** -applicazioni possono attivare gli aggiornamenti in background con *area monitoraggio* o in attesa degli *modifiche significative di percorso* . A partire da iOS 7, le applicazioni possono inoltre registrare per aggiornare il contenuto in background utilizzando *Background recuperare* o *notifiche remoto* .


## <a name="application-states-and-application-delegate-methods"></a>Stati dell'applicazione e i metodi delegati di applicazione

Esaminiamo il codice per iOS di elaborazione in background, è necessario comprendere come backgrounding influisce sul ciclo di vita di un'applicazione iOS.

Il ciclo di vita dell'applicazione iOS è una raccolta di stati dell'applicazione e i metodi per lo spostamento tra di essi. Un'applicazione esegue la transizione tra stati in base a quello dell'utente e i requisiti backgrounding dell'applicazione. Lo spostamento è illustrato nel diagramma seguente:

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "Diagramma di stati dell'applicazione e i metodi delegati di applicazione")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

-  **Non è in esecuzione** -l'applicazione non è ancora stata avviata sul dispositivo.
-  **Esecuzione/attiva** -l'applicazione è visualizzato sullo schermo ed esegue codice in primo piano.
-  **Inattivo** -l'applicazione è stato interrotto da una chiamata telefonica in ingresso, text o altre interruzioni.
-  **Backgrounded** -l'applicazione viene spostata in background e continua l'esecuzione di codice in background.
-  **Suspended** : se l'applicazione non dispone di qualsiasi codice da eseguire in background, o se tutto il codice è stata completata, l'app sarà *Suspended* dal sistema operativo. Processo dell'applicazione sospesa viene mantenuta attiva, ma l'applicazione è in grado di eseguire qualsiasi codice in questo stato.
-  **Tornare a non in esecuzione/terminazione (Rare)** : in alcuni casi, viene eliminato definitivamente il processo dell'applicazione e l'applicazione viene restituito il *non è in esecuzione* stato. Ciò si verifica nelle situazioni di memoria insufficiente, oppure se l'utente termina manualmente l'applicazione.


Fin dall'introduzione del supporto multitasking, raramente termina le applicazioni inattive, iOS e ma viene invece mantenuta processi *Suspended* in memoria. Mantiene attivo un processo di applicazione assicura che l'applicazione viene avviata rapidamente alla successiva apertura parte dell'utente. Significa anche applicazioni è possono spostare liberamente dal *Suspended* dello stato nel *Backgrounded* stato senza disegnare alle risorse di sistema. iOS 7 sfrutta questa funzionalità con nuove API che consentono alle applicazioni di sospendere le attività in background quando il dispositivo passa alla modalità sospensione, il contenuto di aggiornamento direttamente da background senza interazione dell'utente e altro ancora. Verranno illustrate le nuove API nel [iOS Backgrounding tecniche](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md).

## <a name="application-lifecycle-methods"></a>Metodi del ciclo di vita dell'applicazione

Quando un'app cambia lo stato, iOS notifica all'applicazione tramite metodi di eventi di `AppDelegate` classe:

-  `OnActivated` -Si tratta la prima volta che viene avviata l'applicazione, e ogni volta che l'applicazione torna in primo piano. Questo è il luogo per inserire il codice che deve essere eseguito ogni volta che viene aperto l'app.
-  `OnResignActivation` -Se l'utente riceve un'interruzione, ad esempio un testo o di una chiamata telefonica, questo metodo viene chiamato e l'app è temporaneamente disattivato. L'utente deve accettare la telefonata, l'app verrà inviata allo sfondo.
-  `DidEnterBackground` -Chiamato quando l'app passa allo stato backgrounded, questo metodo fornisce a un'applicazione circa cinque secondi per preparare per la terminazione possibili. Utilizzare questo tempo per salvare i dati utente e attività e rimuovere informazioni riservate dalla schermata.
-  `WillEnterForeground` -Quando un utente restituisce a un'applicazione backgrounded o sospesa e viene avviato in primo piano, `WillEnterForeground` viene chiamato. Questo è il tempo necessario per preparare l'app da eseguire in primo piano riattivando qualsiasi stato salvato durante `DidEnterBackground` .  `OnActivated` verrà chiamato immediatamente dopo il completamento del metodo.
-  `WillTerminate` -L'applicazione è stato arrestato e il processo viene eliminato definitivamente. Questo metodo viene chiamato solo se il multitasking non è disponibile nel dispositivo o la versione del sistema operativo, se la memoria è insufficiente oppure se l'utente termina manualmente un'applicazione backgrounded. Si noti che applicazioni sospese terminate non chiamerà `WillTerminate` .


Il diagramma seguente illustra come gli Stati dell'applicazione e i metodi del ciclo di vita del formato:

 [![](introduction-to-backgrounding-in-ios-images/image2.png "Questo diagramma viene illustrato come gli Stati dell'applicazione e i metodi del ciclo di vita del formato")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>Controlli utente per Backgrounding in iOS

iOS 7 sono state introdotte diverse funzionalità per consentire agli utenti maggiore controllo sulla backgrounded stato di un'applicazione. Sia la selezione di App e l'impostazione di aggiornamento in Background App influenzano il ciclo di vita dell'applicazione.

### <a name="app-switcher"></a>Selezione del tipo di App

La selezione di App è una funzionalità di controllo importante introdotta in iOS 7. Viene avviata da doppio tocco di **Home** pulsante e Mostra le applicazioni cui processi sono attivi:

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "Lo spostamento tra le applicazioni utilizzando la selezione di App")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

Utilizza la selezione di App, agli utenti di scorrere gli snapshot di tutte le applicazioni backgrounded e sospese. Se si tocca un'applicazione, viene avviato in primo piano. Passaggio di backup rimuove l'applicazione dallo sfondo, il processo verrà terminato. Verrà usato da vicino la selezione di App nel [Demo del ciclo di vita dell'applicazione iOS](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md) nella sezione successiva.

> [!IMPORTANT]
> La selezione del tipo di App non mostra una differenza tra le applicazioni backgrounded o sospese.



### <a name="background-app-refresh-settings"></a>Impostazioni di aggiornamento di App in background

iOS 7 aumenta il controllo utente al ciclo di vita dell'applicazione consentendo agli utenti di rifiutare esplicitamente backgrounding Applications [registrato per l'elaborazione in background](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md). *Ciò non impedisce l'esecuzione di attività in background di applicazioni*.

Gli utenti possono modificare questa impostazione, passare a <span class="uiitem">Impostazioni > generale > Aggiorna App di Background</span> e la modifica dei privilegi backgrounding per un'applicazione selezionata. Se l'aggiornamento di App in Background è impostata su off, l'applicazione verrà sospesa immediatamente durante l'immissione di sfondo e impedito di eseguire l'elaborazione in background:

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "Impostazioni di aggiornamento di App in background")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

Gli sviluppatori possono controllare lo stato di applicazione di aggiornamento in Background con il `BackgroundRefreshStatus` API. Per un esempio, vedere il [recipe controllare impostazione di aggiornamento in Background](https://developer.xamarin.com/recipes/ios/multitasking/check_background_refresh_setting/).

Abbiamo trattato le nozioni di base del ciclo di vita dell'applicazione iOS e le funzionalità per il controllo del ciclo di vita dell'applicazione. Successivamente, di seguito viene illustrato il ciclo di vita dell'applicazione iOS in azione.

