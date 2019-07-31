---
title: Introduzione a watchOS 3
description: Questo articolo presenta tutte le API e le funzionalità nuove e modificate disponibili in watchos 3 per gli sviluppatori Novell.
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/07/2017
ms.openlocfilehash: 8f1e77d112eae951f3441484df94772da1b0f303
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655525"
---
# <a name="introduction-to-watchos-3"></a>Introduzione a watchOS 3

_Questo articolo presenta tutte le API e le funzionalità nuove e modificate disponibili in watchos 3 per gli sviluppatori Novell._

In questo documento vengono trattati gli argomenti seguenti:

- [Novità di watchos 3](#Whats-New-in-watchOS-3)
    - [Apple pay miglioramenti](#Apple-Pay-Enhancements) aggiunge il supporto per i pagamenti in-app nel Apple Watch.
    - Le [attività in background](#Background-Tasks) offrono all'app la possibilità di aggiornare le informazioni in background, in modo che siano pronte quando l'utente la necessita.
    - Sono stati apportati [miglioramenti](#Complications-Enhancements) alle complicazioni per watchos 3 che forniscono nuove funzionalità per le app.
    - I [Framework appena disponibili](#Newly-Available-Frameworks) sono esposti per le app watchos.
    - [Suggerimenti proattivi](#Proactive-Suggestions) consente all'app di visualizzare in modo proattivo le informazioni all'utente.
    * Sono stati apportati diversi [miglioramenti alla sicurezza e alla privacy](#Security-and-Privacy-Enhancements) per watchos 3.
    - Gli [snapshot e dock](#Snapshots-and-Dock) consentono all'utente di accedere rapidamente alle app watchos.
    - [Notifiche utente](#User-Notifications) fornisce notifiche locali e remote all'utente.
    * In watchos 3 sono stati apportati diversi [miglioramenti al Framework di connettività Watch](#Watch-Connectivity-Framework-Enhancements) .
    * In watchos 3 sono stati apportati diversi [miglioramenti a WatchKit Framework](#WatchKit-Framework-Enhancements) .
    - I miglioramenti apportati alle [app di allenamento](#Workout-App-Enhancements) offrono nuove capacità per le app Apple Watch correlate all'allenamento.
- Sono state apportate [modifiche al Framework aggiuntive](#Additional-Framework-Changes) in watchos 3.
- [API deprecate](#Deprecated-APIs) in watchos 3.

<a name="Whats-New-in-watchOS-3" />

## <a name="whats-new-in-watchos-3"></a>Novità di watchos 3

Apple ha aggiunto diverse nuove API e servizi in watchos 3 insieme a numerosi miglioramenti apportati alle funzionalità esistenti, tra cui:

<a name="Apple-Pay-Enhancements" />

## <a name="apple-pay-enhancements"></a>Miglioramenti di Apple Pay

In watchos 3, il Framework PassKit è stato espanso per consentire il supporto per i pagamenti in-app sicuri (sia dei beni fisici che dei servizi) per le app in esecuzione sul Apple Watch.

Usare le nuove classi [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate) per presentare e rispondere a un'interfaccia in cui l'utente può autorizzare le richieste di pagamento.

Per ulteriori informazioni, vedere la guida per i miglioramenti apportati [Apple pay](~/ios/watchos/platform/apple-pay.md) .

<a name="Background-Tasks" />

## <a name="background-tasks"></a>Attività di background

watchos 3 introduce diverse attività in background che un'app può usare per aggiornare le informazioni assicurando che il contenuto dell'utente sia necessario prima di aprirlo.

Sono disponibili le seguenti nuove attività in background:

- **Aggiornamento dell'app in background** : l'attività [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) consente all'app di aggiornare lo stato in background. Questo include in genere un'altra attività, ad esempio il download di nuovo contenuto da Internet usando un [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Aggiornamento dello snapshot in background** : l'attività [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) consente all'app di aggiornare sia il contenuto che l'interfaccia utente prima che il sistema acquisisca uno snapshot che verrà usato per popolare il Dock.
- **Connettività di controllo in background** : l'attività [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) viene avviata per l'app quando riceve dati in background dall'iPhone abbinato.
- **Sessione URL in background** : l'attività [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) viene avviata per l'app quando un trasferimento in background richiede l'autorizzazione o il completamento (correttamente o in errore).

Per ulteriori informazioni, vedere la Guida alle [attività in background](~/ios/watchos/platform/background-tasks.md) .

<a name="Complications-Enhancements" />

## <a name="complications-enhancements"></a>Miglioramenti delle complicazioni

Le complicazioni sono elementi visivi di piccole dimensioni che forniscono informazioni utili a colpo d'occhio. A seconda del quadrante dell'orologio selezionato, l'utente ha la possibilità di personalizzare il quadrante di un'espressione di controllo con una o più complicazioni.

watchos 3 offre all'app la possibilità di creare una o più complicazioni per l'app Watch, in modo che l'utente possa accedere immediatamente alle informazioni da un quadrante.

Inoltre, le complicazioni offrono i vantaggi seguenti:

- L'utente può avviare rapidamente l'app toccando la complicazione direttamente da un quadrante dell'orologio.
- La presenza di una delle complicazioni dell'app sul quadrante dell'orologio fa sì che il sistema mantenga l'app in uno stato pronto per l'avvio, in cui tenta di avviare l'app in background, conservarla in memoria e fornire tempo aggiuntivo per l'aggiornamento.
- Le complicazioni sono garantite almeno 50 aggiornamenti push al giorno.
- Quando l'app include complicazioni, sarà disponibile nella raccolta di Apple Watch Face (vedere la pagina relativa all' [aggiunta di complicazioni alla](https://developer.apple.com/documentation/clockkit/adding_complications_to_the_gallery) documentazione della raccolta per ulteriori informazioni).

In watchos 3, il Framework ClockKit include ora diversi nuovi modelli per complicazioni molto grandi, ad esempio [CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext) e [CLKComplicationTemplateExtraLargeRingImage](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). Inoltre, per creare testo localizzabile, utilizzare i nuovi metodi della classe [CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider) .

Per ulteriori informazioni, vedere la Guida alle [tecniche di interazione rapida per watchos 3](~/ios/watchos/platform/quick-interaction-techniques.md) .

<a name="Newly-Available-Frameworks" />

## <a name="newly-available-frameworks"></a>Framework appena disponibili

watchos 3 include diversi framework Apple esistenti che in precedenza non erano disponibili, ad esempio:

- **SceneKit** : usare SceneKit per includere modelli 3D nell'interfaccia utente dell'app Watch, inclusa la maggior parte delle funzionalità disponibili su altre piattaforme come illuminazione, ombreggiatura, animazione, fisica e sistemi particellari. non sono supportati i file audio spaziali 3D, gli shader personalizzati o OpenGL, i filtri immagini di base e i materiali basati fisicamente.
- **SpriteKit** : usare SpriteKit per eseguire il rendering e animare gli sprite nell'interfaccia utente dell'app Watch Watch, inclusa la maggior parte delle funzionalità disponibili su altre piattaforme come azioni, fisica, illuminazione e sistemi particellari. i filtri di audio spaziale 3D, riproduzione video e immagine principale non sono supportati.
- **AVFoundation** : consente di gestire e riprodurre l'audio.
- **CloudKit** : per spostare i dati tra l'app Watch e i contenitori iCloud.
- **Audio principale** : per gestire i tipi di dati per la rappresentazione di flussi audio, buffer complessi e valori temporali.
- **Gamekit** : per creare giochi sociali.

<a name="Proactive-Suggestions" />

## <a name="proactive-suggestions"></a>Suggerimenti proattivi

watchos 3 consente all'app di presentare in modo proattivo le informazioni all'utente entro i contesti specificati. Per supportare questa funzionalità, [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) include ora la `MapItem` proprietà che consente all'app di fornire informazioni sulla posizione per un uso successivo da parte di altre app.

Per ulteriori informazioni, vedere la Guida [introduttiva ai suggerimenti proattivi](~/ios/watchos/platform/proactive-suggestions.md) .

<a name="Security-and-Privacy-Enhancements" />

## <a name="security-and-privacy-enhancements"></a>Miglioramenti alla sicurezza e alla privacy

Apple ha apportato diversi miglioramenti alla sicurezza e alla privacy in watchos 3, che aiuteranno lo sviluppatore a migliorare la sicurezza delle app e garantire la privacy degli utenti finali.

Di conseguenza, le app in esecuzione in watchos 3 (o versioni successive) devono dichiarare in modo statico l'intenzione di accedere a funzionalità specifiche o informazioni utente immettendo una o più `Info.plist` chiavi specifiche per la privacy nei file che spiegano all'utente il motivo per cui l'app desidera ottenere l'accesso.

Poiché watchos 3 condivide queste modifiche con iOS 10, vedere la guida ai miglioramenti per la [sicurezza e la privacy](~/ios/app-fundamentals/security-privacy.md) di iOS 10 per altre informazioni.

<a name="Snapshots-and-Dock" />

## <a name="snapshots-and-dock"></a>Snapshot e ancoraggio

In watchos 3, Apple ha aggiunto il Dock in cui gli utenti possono aggiungere le proprie app preferite e accedervi rapidamente. Quando l'utente preme il pulsante laterale nella Apple Watch, viene visualizzata una raccolta di snapshot dell'app bloccati. L'utente può scorrere rapidamente verso sinistra o verso destra per trovare l'app desiderata, quindi toccare l'app per avviarla sostituendo lo snapshot con l'interfaccia dell'app in esecuzione.

Il sistema acquisisce periodicamente gli snapshot dell'interfaccia utente dell'app e usa tali snapshot per popolare i documenti. watchos offre all'app la possibilità di aggiornare il contenuto e l'interfaccia utente prima di eseguire lo snapshot.

Per ulteriori informazioni, vedere la Guida alle [attività in background](~/ios/watchos/platform/background-tasks.md) e la Guida di riferimento per [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) di Apple.

<a name="User-Notifications" />

## <a name="user-notifications"></a>Notifiche utente

Il Framework di notifica utente introdotto in watchos 3 supporta il recapito di notifiche locali e remote al Apple Watch. Utilizzare questo Framework per pianificare le notifiche in base a condizioni specifiche, ad esempio l'ora del giorno o la posizione e la ricezione e la gestione delle notifiche.

Per ulteriori informazioni, vedere la Guida alle [tecniche di interazione rapida per watchos 3](~/ios/watchos/platform/quick-interaction-techniques.md) .

<a name="Watch-Connectivity-Framework-Enhancements" />

## <a name="watch-connectivity-framework-enhancements"></a>Guarda i miglioramenti del Framework di connettività

La nuova `HasContentPending` proprietà della classe [WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession) indica che la sessione ha ricevuto dati in background che devono essere elaborati. E la `RemainingComplicationUserInfoTransfers` proprietà restituisce le ore rimanenti in cui l'app iOS può aggiornare la sua complicazione watchos.

Per ulteriori informazioni, vedere la Guida alle [attività in background](~/ios/watchos/platform/background-tasks.md) .

<a name="WatchKit-Framework-Enhancements" />

## <a name="watchkit-framework-enhancements"></a>Miglioramenti di WatchKit Framework

watchos 3 include alcuni miglioramenti apportati al Framework WatchKit, inclusi i seguenti:

- L'app può ottenere lo stato della Digital Crown usando la nuova classe [WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer) e ricevere gli aggiornamenti quando l'utente ruota la corona usando la classe [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate) .
- La classe [WKExtension](https://developer.apple.com/reference/watchkit/wkextension) include ora il `ApplicationState` metodo e la costante [WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate) che l'app può usare per tenere traccia dello stato di runtime dell'app. `WKExtension`in sono inoltre disponibili due nuovi metodi che possono essere utilizzati per pianificare le attività in background.
- Il [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) include ora i nuovi `ApplicationWillEnterForeground` `ApplicationDidEnterBackground` metodi e `HandleBackgroundTasks` per monitorare le modifiche nello stato dell'app e gestire gli aggiornamenti delle attività in background.
- È stata aggiunta una nuova classe [WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer) per fornire i seguenti tipi di riconoscimento dei movimenti alle app Watch: [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer), [WKPanGestureRecognizer](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer), [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer) e [WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer).
- La nuova classe [WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera) fornisce un'interfaccia per qualsiasi videocamera IP collegato HomeKit.
- La nuova classe [WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie) consente all'app di visualizzare un "poster" del film che viene sostituito dal film in esecuzione quando l'utente lo tocca.
- La nuova classe [WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton) consente all'app di presentare un Apple pay pulsante nell'interfaccia utente che avvierà una richiesta di pagamento quando viene toccato.
- La nuova classe [WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene) presenta un'interfaccia per la visualizzazione di una scena SceneKit sul Apple Watch.
- La nuova classe [WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene) presenta un'interfaccia per la visualizzazione di una scena SpriteKit sul Apple Watch.

Per ulteriori informazioni, vedere la Guida alle [tecniche di interazione rapida per watchos 3](~/ios/watchos/platform/quick-interaction-techniques.md) .

<a name="Workout-App-Enhancements" />

## <a name="workout-app-enhancements"></a>Miglioramenti delle app per gli allenamenti

Nuovo per watchos 3, le app correlate all'allenamento hanno la possibilità di essere eseguite in background sul Apple Watch. Per abilitare questa funzionalità e ottenere l'accesso ai dati di HealthKit, l'app deve includere `WKBackgroundModes` la chiave `Info.plist` nel file con il valore `workout-processing`.

Inoltre, lo sviluppatore è ora in grado di avviare l'app di allenamento watchos dalla versione dell'app iOS sull'iPhone associato.

Per ulteriori informazioni, vedere la guida per i miglioramenti apportati alle [app di allenamento](~/ios/watchos/platform/workout-apps.md) .

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Modifiche aggiuntive del Framework

Oltre alle principali modifiche e aggiunte al Framework elencate in precedenza, Apple ha apportato molte modifiche di Framework secondarie aggiuntive in watchos 3.

Per ulteriori informazioni, vedere la Guida alle [modifiche aggiuntive del Framework](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md) .

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>API deprecate

Le API seguenti sono state deprecate in watchos 3:

- La `UILocalNotification` classe di UIKit è stata deprecata e deve essere sostituita con il Framework di notifica utente.

Per un elenco completo di deprecazioni e modifiche, vedere la documentazione relativa [alle differenze tra le API watchos 2,2 di Apple watchos 3,0](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html) .


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di watchOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
- [Novità di watchos 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
