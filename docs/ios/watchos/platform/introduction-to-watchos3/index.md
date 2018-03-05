---
title: Introduzione a watchOS 3
description: "Questo articolo descrive tutte le nuove e modificate le API e le funzionalità disponibili in watchOS 3 per gli sviluppatori di Xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: B8ABE1E1-8688-4262-BE66-A16813C2D671
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/07/2017
ms.openlocfilehash: f11db7496d132b742cb57b86ddea240712b09e34
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-watchos-3"></a>Introduzione a watchOS 3

_Questo articolo descrive tutte le nuove e modificate le API e le funzionalità disponibili in watchOS 3 per gli sviluppatori di Xamarin._

Questo documento illustra gli argomenti seguenti:

- [Novità in watchOS 3](#Whats-New-in-watchOS-3)
    - [Miglioramenti di pagare Apple](#Apple-Pay-Enhancements) aggiunge il supporto per i pagamenti nell'applicazione nel Apple Watch.
    - [Attività in background](#Background-Tasks) assegnare all'app la possibilità di aggiornare le informazioni in background, pertanto è pronto quando l'utente ha bisogno.
    - [Miglioramenti di complicazioni](#Complications-Enhancements) watchOS 3 che forniscono nuove funzionalità per le app sono state apportate.
    - [Appena disponibile Framework](#Newly-Available-Frameworks) sono esposti per le app watchOS.
    - [Suggerimenti proattivi](#Proactive-Suggestions) consente all'app da visualizzare in modo proattivo informazioni all'utente.
    * Diversi [miglioramenti di Privacy e sicurezza](#Security-and-Privacy-Enhancements) sono state apportate per watchOS 3.
    - [Gli snapshot e ancoraggio](#Snapshots-and-Dock) forniscono accesso rapido alle App watchOS app all'utente.
    - [Notifiche utente](#User-Notifications) fornisce locali e remote notifiche all'utente.
    * Diversi [Framework miglioramenti alla connettività di espressioni di controllo](#Watch-Connectivity-Framework-Enhancements) watchOS 3 sono state apportate.
    * Diversi [WatchKit Framework miglioramenti](#WatchKit-Framework-Enhancements) watchOS 3 sono state apportate.
    - [Miglioramenti di App allenamenti](#Workout-App-Enhancements) offre capacità di nuovo per l'allenamenti relative applicazioni Apple Watch.
- [Altre modifiche Framework](#Additional-Framework-Changes) sono state apportate in tutto watchOS 3.
- [API deprecate](#Deprecated-APIs) in watchOS 3.

<a name="Whats-New-in-watchOS-3" />

## <a name="whats-new-in-watchos-3"></a>Novità in watchOS 3

Apple ha aggiunto diversi servizi e le API di nuovo watchOS 3 e molti miglioramenti alle funzionalità esistenti, tra cui:

<a name="Apple-Pay-Enhancements" />

## <a name="apple-pay-enhancements"></a>Miglioramenti di pagamento di Apple

In watchOS 3, il framework PassKit è stata espansa per consentire il supporto per i pagamenti protetto, nell'applicazione (del fisici beni e servizi) per le App in esecuzione su di Apple Watch.

Usare il nuovo [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorizationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate) classi per consentire di rispondere a un'interfaccia in cui l'utente può autorizzare le richieste di pagamento.

Per ulteriori dettagli, vedere il nostro [miglioramenti pagare Apple](~/ios/watchos/platform/apple-pay.md) Guida.

<a name="Background-Tasks" />

## <a name="background-tasks"></a>Attività di background

watchOS 3 presenta diverse attività di background che un'app è possibile utilizzare per aggiornare le informazioni assicurandosi che include il contenuto, l'utente deve prima di aprirlo.

Le seguenti nuove attività in background sono disponibili:

- **Aggiornamento di App in background** - [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask) attività consente all'app di aggiornare il proprio stato in background. In genere ciò comprende un'altra attività, ad esempio download di nuovo contenuto da internet utilizzando un [NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession).
- **Snapshot di aggiornamento in background** - [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) attività consente all'app di aggiornare il contenuto e l'interfaccia utente prima che il sistema accetta uno snapshot che verrà utilizzato per popolare l'ancoraggio.
- **Connettività di espressioni di controllo in background** - [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask) attività viene avviata per l'app quando riceve i dati in background da iPhone associati.
- **URL di sessione in background** - [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask) attività viene avviata per l'app quando un trasferimento in background richiede l'autorizzazione o completato (esito positivo o errore).

Per ulteriori dettagli, consultare il nostro [attività in Background](~/ios/watchos/platform/background-tasks.md) Guida.

<a name="Complications-Enhancements" />

## <a name="complications-enhancements"></a>Miglioramenti di complessità

Le complicazioni sono piccoli elementi visivi che forniscono informazioni utili a colpo d'occhio. A seconda della faccia di espressioni di controllo selezionata, l'utente ha la possibilità di personalizzare un tipo di carattere di controllo con uno o più complicazione.

watchOS 3 offre l'app la possibilità di creare uno o più complicazione per l'applicazione di espressioni di controllo in modo che l'utente può accedere relative informazioni a immediatamente da un tipo di carattere di controllo.

Inoltre, complicazioni offrono i vantaggi seguenti:

- L'utente può rapidamente avviare l'app toccando nella complicazione direttamente da un tipo di carattere di controllo.
- Con uno dei problemi dell'applicazione nel cause faccia espressioni di controllo per mantenere l'app in uno stato di pronto-a-avvio in cui tenta di avviare l'applicazione in background, il sistema mantenerla in memoria e fornisce tempo extra per aggiornare.
- Complicazioni sono garantiti almeno 50 aggiornamenti push al giorno.
- Quando l'applicazione include complicazioni, verranno presentato nella raccolta faccia Apple Watch (vedere Apple [complicazioni aggiunta alla raccolta](https://developer.apple.com/documentation/clockkit/adding_complications_to_the_gallery) documentazione per ulteriori informazioni).

In watchOS 3, il framework ClockKit ora include diversi nuovi modelli per complicazioni molto grandi, ad esempio [CLKComplicationTemplateExtraLargeColumnsText](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargecolumnstext) e [CLKComplicationTemplateExtraLargeRingImage ](https://developer.apple.com/reference/clockkit/clkcomplicationtemplateextralargeringimage). Inoltre, per creare il testo localizzabile, usare nuovi metodi del [CLKTextProvider](https://developer.apple.com/reference/clockkit/clktextprovider) classe.

Per ulteriori dettagli, vedere il nostro [rapido tecniche di interazione per watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) Guida.

<a name="Newly-Available-Frameworks" />

## <a name="newly-available-frameworks"></a>Framework appena disponibili.

watchOS 3 include diversi Framework Apple esistenti che non sono disponibili in precedenza, ad esempio:

- **SceneKit** -SceneKit utilizzare 3D di includere modelli nell'interfaccia utente dell'applicazione di espressioni di controllo tra cui la maggior parte dei sistemi di particelle e le funzionalità disponibili in altre piattaforme come illuminazione, ombreggiatura, animazione, fisica. Audio spaziale 3D, shader Metal o OpenGL personalizzati, filtri immagini di base e materiali fisicamente basata non sono supportati.
- **SpriteKit** -SpriteKit utilizzare per eseguire il rendering e animazione sprite nell'interfaccia utente dell'app app espressioni di controllo, tra cui la maggior parte delle funzionalità disponibili in altre piattaforme, ad esempio i sistemi di azioni, fisica, l'illuminazione e particella. Audio spaziale 3D, la riproduzione di video e filtri di immagini di base non sono supportati.
- **AVFoundation** : per gestire e riprodurre l'audio.
- **CloudKit** : per spostare i dati tra i contenitori di app e iCloud espressioni di controllo.
- **Core Audio** : per gestire i tipi di dati per rappresentare flussi audio, buffer complesso e i valori di ora.
- **GameKit** : per creare giochi.

<a name="Proactive-Suggestions" />

## <a name="proactive-suggestions"></a>Suggerimenti attiva

watchOS 3 consente all'app in modo proattivo presenti informazioni all'utente all'interno di contesti di base. Per supportare questa funzionalità, il [NSUserActivity](https://developer.apple.com/reference/foundation/nsuseractivity) include ora il `MapItem` proprietà che consente all'app di fornire le informazioni sul percorso per un utilizzo successivo da altre app.

Per ulteriori dettagli, vedere il nostro [Introduzione ai suggerimenti proattiva](~/ios/watchos/platform/proactive-suggestions.md) Guida.

<a name="Security-and-Privacy-Enhancements" />

## <a name="security-and-privacy-enhancements"></a>Miglioramenti di Privacy e sicurezza

Apple ha migliorato notevolmente sia sicurezza e privacy in watchOS 3 che lo sviluppatore consente di migliorare la sicurezza delle proprie App e garantire la privacy dell'utente finale.

Di conseguenza, App in esecuzione su watchOS 3 (o versione successiva) dichiarare in modo statico l'intenzione di accedere a funzionalità specifiche o informazioni utente immettendo una o più Privacy chiavi specifiche nella loro `Info.plist` file segnalare all'utente perché l'app desidera accedere.

Poiché watchOS 3 condivide queste modifiche con iOS 10, consultare il nostro iOS 10 [miglioramenti di Privacy e sicurezza](~/ios/app-fundamentals/security-privacy.md) Guida per ulteriori informazioni.

<a name="Snapshots-and-Dock" />

## <a name="snapshots-and-dock"></a>Gli snapshot e ancoraggio

In watchOS 3, Apple ha aggiunto ancoraggio in cui gli utenti possono aggiungere le proprie App preferite e accedervi rapidamente. Quando l'utente preme il pulsante sul lato nel Apple Watch, verrà visualizzata una raccolta di snapshot di app bloccate. L'utente può scorrere verso sinistra o destra per cercare l'app desiderata, quindi toccare l'app per avviarla sostituendo lo snapshot con l'interfaccia dell'app in esecuzione.

Periodicamente, il sistema accetta gli snapshot dell'interfaccia utente dell'applicazione e utilizza questi snapshot per popolare i documenti. watchOS consente all'app l'opportunità di aggiornare il contenuto e dell'interfaccia utente prima di questo snapshot è stato creato.

Per ulteriori informazioni, vedere il nostro [attività in Background](~/ios/watchos/platform/background-tasks.md) Guida e Apple [WKSnapshotRefreshBackgroundTask riferimento](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask) .

<a name="User-Notifications" />

## <a name="user-notifications"></a>Notifiche utente

Il framework di notifica utente introdotto in watchOS 3 supporta il recapito delle notifiche locali e remoti di Apple Watch. Utilizzare questo framework di pianificare le notifiche in base a condizioni specifiche, ad esempio l'ora del giorno o il percorso e di ricevere e gestire le notifiche.

Per ulteriori dettagli, vedere il nostro [rapido tecniche di interazione per watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) Guida.

<a name="Watch-Connectivity-Framework-Enhancements" />

## <a name="watch-connectivity-framework-enhancements"></a>Controllare la connettività Framework miglioramenti

Il nuovo `HasContentPending` proprietà del [WCSession](https://developer.apple.com/reference/watchconnectivity/wcsession) classe indica che la sessione ha ricevuto i dati in background che deve essere elaborato. E `RemainingComplicationUserInfoTransfers` proprietà restituisce il rimanente gli orari in cui l'app per iOS è possibile aggiornare relativo watchOS complicazione.

Per ulteriori dettagli, consultare il nostro [attività in Background](~/ios/watchos/platform/background-tasks.md) Guida.

<a name="WatchKit-Framework-Enhancements" />

## <a name="watchkit-framework-enhancements"></a>Miglioramenti apportati a Framework WatchKit

watchOS 3 include vari miglioramenti per il framework WatchKit, inclusi i seguenti:

- L'app può ottenere lo stato di parte digitali usando la nuova [WKCrownSequencer](https://developer.apple.com/reference/watchkit/wkcrownsequencer) classe e di ricevere gli aggiornamenti quando l'utente ruota il chioma usando il [WKCrownDelegate](https://developer.apple.com/reference/watchkit/wkcrowndelegate) classe.
- Il [WKExtension](https://developer.apple.com/reference/watchkit/wkextension) classe include ora il `ApplicationState` (metodo) e [WKApplicationState](https://developer.apple.com/reference/watchkit/wkapplicationstate) (costante) che l'app è possibile utilizzare per tenere traccia dello stato di runtime dell'applicazione. `WKExtension` fornisce anche due nuovi metodi che consentono di pianificare le attività in background.
- Il [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) include ora il nuovo `ApplicationWillEnterForeground`, `ApplicationDidEnterBackground` e `HandleBackgroundTasks` metodi per monitorare le modifiche nello stato dell'app e gestire gli aggiornamenti delle attività in background.
- Un nuovo [WKGestureRecognizer](https://developer.apple.com/reference/watchkit/wkgesturerecognizer) classe è stata aggiunta per fornire i seguenti tipi di riconoscimento di movimento per le app di espressioni di controllo: [WKLongPressGestureRecognizer](https://developer.apple.com/reference/watchkit/wklongpressgesturerecognizer), [WKPanGestureRecognizer ](https://developer.apple.com/reference/watchkit/wkpangesturerecognizer), [WKSwipeGestureRecognizer](https://developer.apple.com/reference/watchkit/wkswipegesturerecognizer) e [WKTapGestureRecognizer](https://developer.apple.com/reference/watchkit/wktapgesturerecognizer).
- Il nuovo [WKinterfaceHMCamera](https://developer.apple.com/reference/watchkit/wkinterfacehmcamera) classe fornisce un'interfaccia per qualsiasi HomeKit collegato fotocamera IP.
- Il nuovo [WKInterfaceInlineMovie](https://developer.apple.com/reference/watchkit/wkinterfaceinlinemovie) classe consente all'app di visualizzare un filmato "poster" che viene sostituita con il film in esecuzione quando l'utente tocca il.
- Il nuovo [WKInterfacePaymentButton](https://developer.apple.com/reference/watchkit/wkinterfacepaymentbutton) classe consente all'app di presentare un pulsante Apple Pay nell'interfaccia utente che avvia una richiesta di pagamento quando si tocca.
- Il nuovo [WKInterfaceSCNScene](https://developer.apple.com/reference/watchkit/wkinterfacescnscene) classe presenta un'interfaccia per visualizzare una scena SceneKit di Apple Watch.
- Il nuovo [WKInterfaceSKScene](https://developer.apple.com/reference/watchkit/wkinterfaceskscene) classe presenta un'interfaccia per visualizzare una scena SpriteKit di Apple Watch.

Per ulteriori dettagli, vedere il nostro [rapido tecniche di interazione per watchOS 3](~/ios/watchos/platform/quick-interaction-techniques.md) Guida.

<a name="Workout-App-Enhancements" />

## <a name="workout-app-enhancements"></a>Miglioramenti di App allenamenti

Nuovo per watchOS 3, allenamenti correlati App hanno la possibilità di eseguire in background nel Apple Watch. Per abilitare questa funzionalità (e accedere ai dati HealthKit), l'app deve includere il `WKBackgroundModes` chiave nel `Info.plist` file con il valore `workout-processing`.

Inoltre, lo sviluppatore ha ora la possibilità di avviare l'app allenamenti watchOS dalla versione di app iOS su iPhone associati.

Per ulteriori dettagli, consultare il nostro [allenamenti App miglioramenti](~/ios/watchos/platform/workout-apps.md) Guida.

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Modifiche di .NET Framework

Oltre alle modifiche framework principali e le aggiunte elencate in precedenza, Apple ha molte modifiche aggiuntive framework secondaria watchOS 3.

Per ulteriori dettagli, consultare il nostro [ulteriori modifiche di Framework](~/ios/watchos/platform/introduction-to-watchos3/additional-framework-changes.md) Guida.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>API deprecate

Le seguenti API sono state deprecate in watchOS 3:

- La `UILocalNotification` classe di UIKit è stato deprecato e deve essere sostituito con il framework di notifica all'utente.

Vedere Apple [watchOS 2.2 a differenze tra API watchOS 3.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/watchOS30APIDiffs/index.html) documentazione per un elenco completo delle deprecations e delle modifiche.


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di watchOS](https://developer.xamarin.com/samples/watchos/all/)
- [Novità di watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
