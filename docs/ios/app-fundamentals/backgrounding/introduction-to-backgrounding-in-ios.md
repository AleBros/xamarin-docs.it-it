---
title: Introduzione all'elaborazione in background in iOS
description: "Questo documento descrive in background in iOS: Stati dell'applicazione, metodi del ciclo di vita delle applicazioni e aggiornamento delle app in background."
ms.prod: xamarin
ms.assetid: E214F2C7-E74E-46C7-B5BA-080B30D61250
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/24/2018
ms.openlocfilehash: 78751f53808ffa62589fdc57fe4cf59912849e00
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010847"
---
# <a name="introduction-to-backgrounding-in-ios"></a>Introduzione all'elaborazione in background in iOS

iOS regola in modo molto rigoroso l'elaborazione in background e offre tre approcci per implementarla:

- **Registrare un'attività in background** : se un'applicazione deve completare un'attività importante, può richiedere a iOS di non interrompere l'attività quando l'applicazione viene spostata in background. Ad esempio, un'applicazione potrebbe dover completare la registrazione di un utente o terminare il download di un file di grandi dimensioni.
- Eseguire la **registrazione come applicazione in background necessaria** : un'app può registrarsi come un tipo specifico di applicazione con requisiti di background noti e specifici, ad esempio *audio* , *VoIP* , *accessorio esterno* , *edicola* , e *posizione* . A queste applicazioni sono consentiti privilegi di elaborazione in background continui, purché eseguano attività che si trovano all'interno dei parametri del tipo di applicazione registrato.
- **Abilitare gli aggiornamenti in background** : le applicazioni possono attivare gli aggiornamenti in background con il *monitoraggio dell'area* o in attesa di *modifiche significative alla località* . A partire da iOS 7, le applicazioni possono anche registrarsi per aggiornare il contenuto in background tramite il *recupero in background* o le *notifiche remote* .

## <a name="application-states-and-application-delegate-methods"></a>Stati dell'applicazione e metodi del delegato dell'applicazione

Prima di approfondire il codice per l'elaborazione in background in iOS, è necessario comprendere come il background influisca sul ciclo di vita di un'applicazione iOS.

Il ciclo di vita dell'applicazione iOS è una raccolta di Stati dell'applicazione e metodi per lo spostamento tra di essi. Un'applicazione esegue la transizione tra gli stati in base al comportamento dell'utente e ai requisiti in background dell'applicazione. Lo spostamento viene illustrato nel diagramma seguente:

 [![](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png "Application States and Application Delegate Methods diagram")](introduction-to-backgrounding-in-ios-images/applicationlifecycle-.png#lightbox)

- **Non in esecuzione** : l'applicazione non è ancora stata avviata nel dispositivo.
- **Running/Active** : l'applicazione è sullo schermo e sta eseguendo il codice in primo piano.
- **Inattivo** : l'applicazione viene interrotta da una telefonata in arrivo, da un testo o da un'altra interruzione.
- In **background** : l'applicazione viene spostata in background e continua l'esecuzione del codice in background.
- **Suspended** : se l'applicazione non dispone di codice per l'esecuzione in background o se tutto il codice è stato completato, l'app verrà *sospesa* dal sistema operativo. Il processo di un'applicazione sospesa viene mantenuto attivo, ma l'applicazione non è in grado di eseguire il codice in questo stato.
- **Torna a non in esecuzione/terminazione (rara)** . occasionalmente, il processo dell'applicazione viene eliminato definitivamente e l'applicazione torna allo stato *non in esecuzione* . Questo problema si verifica in situazioni di memoria insufficiente o se l'utente termina manualmente l'applicazione.

Dall'introduzione del supporto multitasking, iOS raramente termina le applicazioni inattive e mantiene i processi *sospesi* in memoria. Mantenere attivo il processo di un'applicazione garantisce che l'applicazione si avvii rapidamente al successivo avvio dell'utente. Significa anche che le applicazioni possono spostarsi liberamente dallo stato di *sospensione* allo stato in *background* senza eseguire il disegno delle risorse di sistema. iOS 7 sfrutta questa funzionalità con le nuove API che consentono alle applicazioni di sospendere le attività in background quando il dispositivo passa alla modalità di sospensione, aggiorna il contenuto direttamente dallo sfondo senza interazione dell'utente e altro ancora. Si tratteranno le nuove API nelle [tecniche di background di iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/index.md).

## <a name="application-lifecycle-methods"></a>Metodi del ciclo di vita dell'applicazione

Quando si modifica lo stato di un'app, iOS invia una notifica all'applicazione tramite metodi di evento nella classe `AppDelegate`:

- `OnActivated`: viene chiamato la prima volta che l'applicazione viene avviata e ogni volta che l'app torna in primo piano. Questa è la posizione in cui inserire il codice che deve essere eseguito ogni volta che viene aperta l'app.
- `OnResignActivation`: se l'utente riceve un'interruzione, ad esempio un testo o una telefonata, questo metodo viene chiamato e l'app viene temporaneamente disattivata. Se l'utente accetta la telefonata, l'app verrà inviata in background.
- `DidEnterBackground`: quando l'app entra nello stato in background, questo metodo fornisce a un'applicazione circa cinque secondi per preparare la possibile terminazione. Usare questa volta per salvare i dati e le attività dell'utente e rimuovere le informazioni riservate dalla schermata.
- `WillEnterForeground`: quando un utente torna a un'applicazione in background o sospesa e lo avvia in primo piano, viene chiamato `WillEnterForeground`. Si tratta del tempo necessario per preparare l'app in modo da riattivare in primo piano tutti gli stati salvati durante `DidEnterBackground`.  `OnActivated` verrà chiamato immediatamente dopo il completamento di questo metodo.
- `WillTerminate`-l'applicazione viene arrestata e il processo viene eliminato definitivamente. Questo metodo viene chiamato solo se il multitasking non è disponibile nel dispositivo o nella versione del sistema operativo, se la memoria è insufficiente o se l'utente termina manualmente un'applicazione in background. Si noti che le applicazioni sospese che vengono interrotte non chiameranno `WillTerminate`.

Il diagramma seguente illustra il modo in cui si integrano gli Stati e i metodi del ciclo di vita dell'applicazione:

 [![](introduction-to-backgrounding-in-ios-images/image2.png "This diagram illustrates how the application states and lifecycle methods fit together")](introduction-to-backgrounding-in-ios-images/image2.png#lightbox)

## <a name="user-controls-for-backgrounding-in-ios"></a>Controlli utente per l'utilizzo in background in iOS

iOS 7 ha introdotto diverse funzionalità per offrire agli utenti un maggiore controllo sullo stato in background di un'applicazione. Sia lo switcher dell'app che l'impostazione di aggiornamento dell'app in background hanno effetto sul ciclo di vita dell'applicazione.

### <a name="app-switcher"></a>Switcher app

Lo strumento di selezione delle app è una funzionalità di controllo importante introdotta in iOS 7. Viene avviata con un doppio tocco del pulsante **Home** e Mostra le applicazioni i cui processi sono attivi:

 [![](introduction-to-backgrounding-in-ios-images/app-switcher-.png "Moving between apps using the App Switcher")](introduction-to-backgrounding-in-ios-images/app-switcher-.png#lightbox)

Usando lo strumento di selezione delle app, gli utenti possono scorrere gli snapshot di tutte le applicazioni in background e sospese. Toccando un'applicazione, questa viene avviata in primo piano. Se si scorre rapidamente, l'applicazione viene rimossa dallo sfondo, terminando il processo. Nella sezione successiva verrà esaminato in dettaglio lo strumento di selezione delle app nella demo del ciclo di vita [dell'applicazione iOS](~/ios/app-fundamentals/backgrounding/application-lifecycle-demo.md) .

> [!IMPORTANT]
> Lo strumento di selezione dell'app non mostra alcuna differenza tra le applicazioni in background e sospese.

### <a name="background-app-refresh-settings"></a>Impostazioni di aggiornamento dell'app in background

iOS 7 aumenta il controllo utente sul ciclo di vita dell'applicazione consentendo agli utenti di rifiutare esplicitamente le applicazioni in background [registrate per l'elaborazione in background](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md). *Ciò non impedisce alle applicazioni di eseguire attività in background*.

Gli utenti possono modificare questa impostazione passando a **impostazioni > generale > aggiornamento dell'app in background** e modificando i privilegi in background per un'applicazione selezionata. Se l'aggiornamento dell'app in background è impostato su off, l'applicazione verrà sospesa immediatamente dopo l'immissione dello sfondo e impedirà l'esecuzione di qualsiasi elaborazione in background:

 [![](introduction-to-backgrounding-in-ios-images/settings-.png "Background App Refresh Settings")](introduction-to-backgrounding-in-ios-images/settings-.png#lightbox)

Gli sviluppatori possono controllare lo stato dell'applicazione di aggiornamento in background con l'API `BackgroundRefreshStatus`. Per un esempio, vedere la [ricetta relativa all'impostazione di aggiornamento in background](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/check_background_refresh_setting).

Sono stati trattati i concetti di base del ciclo di vita delle applicazioni iOS e le funzionalità per il controllo del ciclo di vita dell'applicazione. Verrà ora visualizzato il ciclo di vita dell'applicazione iOS in azione.
