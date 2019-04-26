---
title: Introduzione a watchOS 3
description: Questo articolo illustra tutte le nuove e modificate le API e funzionalità disponibile in watchOS 3 per gli sviluppatori di Xamarin.
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/07/2017
ms.openlocfilehash: 0428a0df157e359ab34a6a71dbba31bdeb6962fa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224062"
---
# <a name="introduction-to-watchos-3"></a>Introduzione a watchOS 3

_Questo articolo illustra tutte le nuove e modificate le API e funzionalità disponibile in watchOS 3 per gli sviluppatori di Xamarin._

Questo documento illustra gli argomenti seguenti:

- [What ' s New in watchOS 3](#Whats-New-in-watchOS-3)
    - [Miglioramenti di pagare Apple](#Apple-Pay-Enhancements) aggiunge il supporto per i pagamenti nell'app nel Apple Watch.
    - [Le attività in background](#Background-Tasks) assegnare all'app la possibilità di aggiornare le informazioni in background in modo che è pronto quando l'utente ne ha bisogno.
    - [Miglioramenti di complicazioni](#Complications-Enhancements) sono state apportate per watchOS 3 che forniscono nuove funzionalità per le app.
    - [Nuovo Framework disponibile](#Newly-Available-Frameworks) sono esposte per le app watchOS.
    - [Suggerimenti proattivi](#Proactive-Suggestions) consente all'app mostrare in modo proattivo informazioni all'utente.
    * Numerosi [miglioramenti della protezione e Privacy](#Security-and-Privacy-Enhancements) sono state apportate per watchOS 3.
    - [Gli snapshot e ancoraggio](#Snapshots-and-Dock) consentire all'utente di accedere rapidamente alle App watchOS app.
    - [Notifiche utente](#User-Notifications) fornisce notifiche locali e remote per l'utente.
    * Numerosi [espressioni di controllo della connettività Framework miglioramenti](#Watch-Connectivity-Framework-Enhancements) in watchOS 3 sono state apportate.
    * Numerosi [i miglioramenti apportati Framework WatchKit](#WatchKit-Framework-Enhancements) in watchOS 3 sono state apportate.
    - [Miglioramenti delle App per gli allenamenti](#Workout-App-Enhancements) offre nuove funzionalità per il per gli allenamenti correlati App Apple Watch.
- [Modifiche aggiuntive del Framework](#Additional-Framework-Changes) sono state apportate in tutto watchOS 3.
- [API deprecate](#Deprecated-APIs) in watchOS 3.

<a name="Whats-New-in-watchOS-3" />

## <a name="whats-new-in-watchos-3"></a>What ' s New in watchOS 3

Apple ha aggiunto diverse nuove API e servizi in watchOS 3 insieme a molti miglioramenti alle funzionalità esistenti, tra cui:

<a name="Apple-Pay-Enhancements" />

## <a name="apple-pay-enhancements"></a>Miglioramenti di Apple Pay

In watchOS 3, il framework di PassKit è stato ampliato per consentire al supporto per i pagamenti sicuri, in-app (di beni e servizi) per le App in esecuzione sul Apple Watch.

Usare le nuove [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate) classi per presentare e rispondere a un'interfaccia in cui l'utente può autorizzare le richieste di pagamento.

Per altre informazioni, vedere la [Apple pagare miglioramenti](~/ios/watchos/platform/apple-pay.md) Guida.

<a name="Background-Tasks" />

## <a name="background-tasks"></a>Attività di background

watchOS 3 introduce diverse attività in background che un'app può usare per aggiornare le proprie informazioni assicurando che sia il contenuto, l'utente deve prima di aprirlo.

Sono disponibili le seguenti nuove attività in background:

- **Aggiornamento di App di sfondo** - il [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) attività consente all'app per aggiornarne lo stato in background. In genere includerà un'altra attività, ad esempio download di nuovi contenuti da internet usando un [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **In background aggiorna Snapshot** - il [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) attività consente all'app di aggiornare il contenuto e l'interfaccia utente prima che il sistema accetta uno snapshot che verrà usato per popolare l'ancoraggio.
- **Espressioni di controllo della connettività di sfondo** - il [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) attività viene avviata per l'app alla ricezione dei dati in background da iPhone associato.
- **URL di sessione di sfondo** - il [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) attività viene avviata per l'app quando un trasferimento in background richiede l'autorizzazione o completato (con esito positivo o errore).

Per altre informazioni, vedere la [le attività in Background](~/ios/watchos/platform/background-tasks.md) Guida.

<a name="Complications-Enhancements" />

## <a name="complications-enhancements"></a>Miglioramenti di complicazioni

Le complicazioni sono piccoli elementi visivi che forniscono informazioni utili a colpo. A seconda del quadrante dell'orologio selezionata, l'utente ha la possibilità di personalizzare una watch face con uno o più complicazione.

watchOS 3 fornisce all'app la possibilità di creare uno o più complicazione per le app watch, in modo che l'utente può accedere le informazioni in immediatamente un quadrante dell'orologio.

Inoltre, complicazioni offrono i vantaggi seguenti:

- L'utente può avviare rapidamente l'app toccando la complicazione direttamente da una watch face.
- Presenza di uno dei problemi dell'app watch face cause al sistema di mantenere l'app in uno stato di pronto-a-avvio in cui tenta di avviare l'app in background, mantenerla in memoria e fornisce tempo extra per aggiornare.
- Complicazioni sono garantite almeno 50 aggiornamenti di push al giorno.
- Quando l'app include complicazioni, verrà presentata nella raccolta di Apple Watch Face (vedere di Apple [complicazioni aggiunta alla raccolta](https://developer.apple.com/documentation/clockkit/adding_complications_to_the_gallery) per altre informazioni).

In watchOS 3, il framework ClockKit ora include diversi nuovi modelli per complicazioni molto grande, ad esempio [CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext) e [CLKComplicationTemplateExtraLargeRingImage ](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). Inoltre, per creare testo localizzabile, usare nuovi metodi del [CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider) classe.

Per altre informazioni, vedere la [tecniche per interazioni rapide per watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) Guida.

<a name="Newly-Available-Frameworks" />

## <a name="newly-available-frameworks"></a>Framework appena disponibili

watchOS 3 include diversi Framework Apple esistente che non erano disponibili, ad esempio:

- **SceneKit** -Usa SceneKit per includere 3D modelli nell'interfaccia utente dell'app per le espressioni di controllo, tra cui la maggior parte delle funzionalità disponibili in altre piattaforme, ad esempio illuminazione, ombreggiatura, animazione, di fisica e sistemi particellari. 3D spaziale audio, gli shader di Metal o OpenGL personalizzati, filtri immagini di base e fisicamente in base al materiale non sono supportati.
- **SpriteKit** -Usa SpriteKit per il rendering e animare sprite nell'interfaccia utente dell'app watch app tra cui la maggior parte delle funzionalità disponibili in altre piattaforme, ad esempio i sistemi di azioni, physics, illuminazione e particella. 3D spaziale audio, la riproduzione di video e filtri di immagini di base non sono supportati.
- **AVFoundation** : per gestire e riprodurre l'audio.
- **CloudKit** : per spostare dati tra i contenitori iCloud e app watch.
- **Core Audio** : per gestire i tipi di dati per rappresentare flussi audio, buffer complesso e i valori di ora.
- **GameKit** : per creare giochi basati su social network.

<a name="Proactive-Suggestions" />

## <a name="proactive-suggestions"></a>Suggerimenti proattivi

watchOS 3 consente all'app di presentare in modo proattivo informazioni all'utente all'interno di contesti di base. Per supportare questa funzionalità, il [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) include ora il `MapItem` proprietà che consente all'app di fornire informazioni sul percorso per un utilizzo successivo da altre app.

Per altre informazioni, vedere la [Introduzione ai suggerimenti proattivi](~/ios/watchos/platform/proactive-suggestions.md) Guida.

<a name="Security-and-Privacy-Enhancements" />

## <a name="security-and-privacy-enhancements"></a>Miglioramenti della protezione e Privacy

Apple ha apportato diversi miglioramenti a sia sicurezza e privacy in watchOS 3 che aiuta gli sviluppatori migliorare la sicurezza delle proprie App e garantire la privacy dell'utente finale.

Di conseguenza, le App in esecuzione in watchOS 3 (o versioni successive) in modo statico devono dichiarare manifestato l'intenzione di accedere a funzionalità specifiche o informazioni utente immettendo una o più delle chiavi specifici sulla Privacy in loro `Info.plist` file in cui viene illustrato all'utente perché l'app desidera ottenere l'accesso.

Poiché watchOS 3 condivide queste modifiche con iOS 10, vedere di iOS 10 [miglioramenti della protezione e Privacy](~/ios/app-fundamentals/security-privacy.md) Guida per altre informazioni.

<a name="Snapshots-and-Dock" />

## <a name="snapshots-and-dock"></a>Gli snapshot e ancoraggio

In watchOS 3, Apple ha aggiunto l'ancoraggio in cui gli utenti possono bloccare le app preferite e accedervi rapidamente. Quando l'utente preme il pulsante sul lato l'Apple Watch, verrà visualizzata una raccolta di snapshot delle app bloccate. L'utente può scorrere verso destra o sinistra per trovare l'app desiderata, quindi toccare l'app per avviare il programma sostituendo lo snapshot con l'interfaccia dell'app in esecuzione.

Periodicamente, il sistema acquisisce snapshot dell'interfaccia utente dell'app e Usa tali snapshot per popolare i documenti. watchOS fornisce all'app l'opportunità di aggiornare il contenuto e dell'interfaccia utente prima di questo snapshot viene acquisito.

Per altre informazioni, vedere la [le attività in Background](~/ios/watchos/platform/background-tasks.md) Guida e Apple [WKSnapshotRefreshBackgroundTask riferimento](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) .

<a name="User-Notifications" />

## <a name="user-notifications"></a>Notifiche utente

Il framework di notifica all'utente introdotto in watchOS 3 supporta il recapito delle notifiche locali e remoti per l'Apple Watch. Usare questo framework per pianificare le notifiche in base a condizioni specifiche, ad esempio l'ora del giorno o posizione e per ricevere e gestire le notifiche.

Per altre informazioni, vedere la [tecniche per interazioni rapide per watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) Guida.

<a name="Watch-Connectivity-Framework-Enhancements" />

## <a name="watch-connectivity-framework-enhancements"></a>Guarda i miglioramenti apportati ai Framework di connettività

Il nuovo `HasContentPending` proprietà del [WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession) classe indica che la sessione ha ricevuto i dati in background che deve essere elaborato. E `RemainingComplicationUserInfoTransfers` proprietà restituisce il rimanente gli orari in cui l'app per iOS è possibile aggiornare relativo watchOS complicazione.

Per altre informazioni, vedere la [le attività in Background](~/ios/watchos/platform/background-tasks.md) Guida.

<a name="WatchKit-Framework-Enhancements" />

## <a name="watchkit-framework-enhancements"></a>Miglioramenti apportati Framework WatchKit

watchOS 3 include numerosi miglioramenti al framework WatchKit incluse le seguenti:

- L'app può ottenere lo stato del Crown digitali usando le nuove [WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer) classe e ricevere gli aggiornamenti quando l'utente ruota il crown usando la [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate) classe.
- Il [WKExtension](https://developer.apple.com/reference/watchkit/wkextension) classe include ora le `ApplicationState` (metodo) e [WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate) costante che l'app può usare per tenere traccia dello stato di runtime dell'app. `WKExtension` fornisce anche due nuovi metodi che possono essere usati per pianificare le attività in background.
- Il [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) include ora il nuovo `ApplicationWillEnterForeground`, `ApplicationDidEnterBackground` e `HandleBackgroundTasks` metodi per monitorare le modifiche nello stato dell'app e gestire gli aggiornamenti delle attività in background.
- Una nuova [WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer) classe è stata aggiunta per fornire i seguenti tipi di riconoscimento del movimento per le app watch: [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer), [WKPanGestureRecognizer](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer), [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer) e [WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer).
- Il nuovo [WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera) classe fornisce un'interfaccia per qualsiasi HomeKit collegati fotocamera IP.
- Il nuovo [WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie) classe consente all'app per la visualizzazione di un film "poster" che viene sostituita con il film in esecuzione quando l'utente tocca il.
- Il nuovo [WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton) classe consente all'app presentare un pulsante di Apple Pay nell'interfaccia utente che avvia una richiesta di pagamento quando si tocca.
- Il nuovo [WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene) classe presenta un'interfaccia per la visualizzazione di una scena SceneKit sul Apple Watch.
- Il nuovo [WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene) classe presenta un'interfaccia per la visualizzazione di una scena SpriteKit sul Apple Watch.

Per altre informazioni, vedere la [tecniche per interazioni rapide per watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) Guida.

<a name="Workout-App-Enhancements" />

## <a name="workout-app-enhancements"></a>Miglioramenti delle app per gli allenamenti

Nuovo per watchOS 3, per gli allenamenti correlati App hanno la possibilità di eseguire in background nel Apple Watch. Per abilitare questa funzionalità (e ottenere l'accesso ai dati di HealthKit), l'app deve includere il `WKBackgroundModes` chiavi nel `Info.plist` file con il valore `workout-processing`.

Inoltre, lo sviluppatore ha ora la possibilità di avviare l'app per gli allenamenti watchOS rispetto alla versione di app iOS su iPhone associato.

Per altre informazioni, vedere la [miglioramenti delle App per gli allenamenti](~/ios/watchos/platform/workout-apps.md) Guida.

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Modifiche di altri Framework

Oltre alle modifiche principali framework e componenti aggiuntivi elencati in precedenza, Apple molti altri framework minori modifiche apportate in watchOS 3.

Per altre informazioni, vedere la [modifiche aggiuntive del Framework](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md) Guida.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>API deprecate

Le API seguenti sono state deprecate in watchOS 3:

- Il `UILocalNotification` classe di UIKit è stato deprecato e deve essere sostituito con il framework di notifica all'utente.

Vedere di Apple [watchOS 2.2 per differenze tra API watchOS 3.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html) documentazione per un elenco completo di modifiche e deprecati.


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Novità in watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
