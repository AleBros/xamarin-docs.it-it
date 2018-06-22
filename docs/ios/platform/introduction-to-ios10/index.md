---
title: Introduzione a iOS 10
description: Questo articolo descrive tutte le nuove e modificate le API e le funzionalità disponibili in iOS 10 per gli sviluppatori di xamarin. IOS.
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: c3bee0f15016394005a67e98cd8435e6d63b3ac6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30786472"
---
# <a name="introduction-to-ios-10"></a>Introduzione a iOS 10

_Questo articolo descrive tutte le nuove e modificate le API e le funzionalità disponibili in iOS 10 per gli sviluppatori di xamarin. IOS._

## <a name="introducing-ios-10"></a>Introduzione a iOS 10

Con il nuovo iOS 10 SDK, Apple ha incluso nuove API e servizi che consentono agli sviluppatori di creare nuove categorie di App e funzionalità. Un'app per iOS è ora possibile estendere le app di messaggi, Siri, telefono e mappe per fornire una funzionalità avanzata, coinvolgere all'utente finale che precedentemente non disponibili.

Per ulteriori informazioni su iOS, 10, vedere Apple [iOS app +](https://developer.apple.com/ios/) documentazione.

## <a name="whats-new-in-ios-10"></a>Novità in iOS 10

Apple iOS 10 insieme a molti miglioramenti alle funzionalità esistenti, tra cui è aggiunto diverse nuove API e servizi:


## <a name="adapting-to-the-true-tone-display"></a>Adattamento per la visualizzazione di tono True

La tecnologia True tono visualizzare Apple utilizza il sensore di luminosità in un dispositivo iOS per modificare dinamicamente il colore e l'intensità dello schermo per corrispondono alle condizioni di illuminazione corrente. fornisce la nuova iOS 10 [UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31) chiave che può essere aggiunto all'app `Info.plist` file e controlla il tono True si applica lo spostamento di colori standard. 

Sono disponibili i seguenti valori:

- `UIWhitePointAdaptivityStyleStandard` **Predefinito** -utilizzare l'adattabilità punto bianco standard.
- `UIWhitePointAdaptivityStyleReading` -Viene usato per le applicazioni con stato attivo di lettura.
- `UIWhitePointAdaptivityStyleGame` -Viene usato per le applicazioni con stato attivo di gioco.
- `UIWhitePointAdaptivityStyleVideo` -Viene usato per le applicazioni con stato attivo di video.
- `UIWhitePointAdaptivityStylePhoto` -Viene usato per le applicazioni con stato attivo fotografia dove fedeltà dei colori è più importante modifiche ambientali punti vuoti.

<a name="app-extensions" />

## <a name="app-extensions"></a>Estensioni

Apple ha fornito diversi nuovi punti di estensione di App in iOS 10:

- Directory di chiamata
- Tipi e i tipi di interfaccia utente
- Messages
- Contenuto di notifica
- Notification Services
- Adesivo Pack

Inoltre, 3rd party tastiera le estensioni sono i seguenti miglioramenti:

- Il nuovo `DocumentInputMode` proprietà la `UITextDocumentProxy` classe può determinare la lingua di input di un documento e consentire l'estensione di tastiera per allinearlo con tale lingua.
- Il nuovo `HandleInputModeList` metodo consente l'estensione della tastiera di visualizzare menu Selettore di tasti del sistema in risposta alla chiave del globo vengano utilizzate.

Per ulteriori informazioni, vedere il nostro [Introduzione alle estensioni](~/ios/platform/extensions.md), [messaggio App integrazione](~/ios/platform/message-app-integration/index.md), [Introduzione ai suggerimenti proattiva](~/ios/platform/search/proactive-suggestions.md), [ Introduzione a SiriKit](~/ios/platform/sirikit/index.md), [Introduzione alle notifiche utente](~/ios/platform/user-notifications/index.md) e Apple [Guida per programmatori dell'estensione App](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

## <a name="app-search-enhancements"></a>Miglioramenti della ricerca di App

Core Spotlight in iOS 10 offre vari miglioramenti per la ricerca di App, ad esempio:

- **Crowdsourced con collegamento diretto popolarità (con privacy differenziale)** -consente di promuovere i contenuti dell'app con collegamento diretto nei risultati della ricerca.
- **La ricerca in-App** -usare il nuovo `CSSearchQuery` classe per fornire capacità di ricerca Spotlight nell'applicazione simile al funzionano delle app di posta elettronica, messaggi e le note.
- **Ricerca di continuazione** : consente di avviare una ricerca Spotlight o Safari, quindi aprire un'app e continuare la ricerca.
- **Visualizzazione dei risultati della convalida** -Apple [lo strumento di convalida API di ricerca di App](https://search.developer.apple.com/appsearch-validation-tool) viene visualizzata una rappresentazione visiva del markup di un sito Web e il collegamento diretto quando preforming test.
- **Messaggio App immagine condivisione** -consente comuni delle immagini nell'applicazione previsti nelle ricerche in evidenza la condivisione in messaggi (tramite un messaggio dell'estensione App).

Per ulteriori dettagli, consultare il nostro [miglioramenti della ricerca di App](~/ios/platform/search/app-search-enhancements.md) Guida.

## <a name="apple-pay-enhancements"></a>Miglioramenti di pagamento di Apple

Apple ha apportato numerosi miglioramenti a pagamento Apple iOS 10 che consentono all'utente di effettuare i pagamenti protetti da siti Web e tramite l'interazione con Siri e mappe.

Con iOS 10, diverse nuove API sono state aggiunte per l'utilizzo in iOS e watchOS per supportare le reti a pagamento dinamico e un nuovo ambiente di test di sandbox.

Inoltre, il framework PassKit è stato espanso per supportare Apple Pay all'esterno di `UIKit` e per consentire di autorità emittenti di smart card presentare le proprie schede all'interno delle app.

Per ulteriori dettagli, vedere il nostro [miglioramenti pagare Apple](~/ios/platform/apple-pay.md) Guida.

## <a name="alternate-app-icons"></a>Icone App alternativo

Apple ha aggiunto numerosi miglioramenti a iOS 10.3 che consentono di gestire la relativa icona di un'app:

 - `ApplicationIconBadgeNumber` -Ottiene o imposta la chiave per il badge dell'icona di app.
 - `SupportsAlternateIcons` -Se `true` l'applicazione dispone di un set alternativo di icone.
 - `AlternateIconName` : Restituisce il nome dell'icona alternativo attualmente selezionata o `null` se si utilizza l'icona primario.
 - `SetAlternameIconName` : Utilizzare questo metodo per passare l'icona dell'app dell'icona alternativo specificato.

Per ulteriori dettagli, vedere il nostro [alternativo icone App](~/ios/app-fundamentals/images-icons/alternate-app-icons.md) Guida.


## <a name="introduction-to-callkit"></a>Introduzione a CallKit

La nuova API CallKit in iOS 10 fornisce un modo per le app VOIP da integrare con l'iPhone dell'interfaccia utente e fornire un'interfaccia familiare ed esperienza per l'utente finale. Con questa API può visualizzare e interagire con le chiamate VOIP dalla schermata di blocco del dispositivo iOS e gestire i contatti con l'app di Phone **Preferiti** e **recenti** viste.

Inoltre, l'API di CallKit fornisce la possibilità di creare estensioni per App che è possibile associare un numero di telefono con un nome (ID chiamante) o indicare il sistema quando deve essere un numero bloccato (chiamata di blocco).

Per ulteriori dettagli, consultare il nostro [Introduzione a Callkit](~/ios/platform/callkit.md) Guida.

## <a name="message-app-integration"></a>Integrazione di App di messaggio

iOS 10 consente l'inclusione di un messaggio dell'estensione dell'App nella soluzione xamarin. IOS che si integra con il **messaggi** nuove funzionalità app e visualizza all'utente. L'estensione può inviare il testo, adesivi, file multimediali e messaggi interattivi. Sono disponibili due tipi di messaggio dell'estensione dell'App:

- **Pack adesivo** -contiene una raccolta di adesivi che l'utente può aggiungere a un messaggio. È possibile creare pacchetti di codice senza scrivere alcun codice.
- **iMessage App** -possono presentare un'interfaccia utente personalizzata all'interno dell'app di messaggi per la selezione adesivi, immissione di testo, inclusi i file di supporto (con le conversioni di tipo facoltativi) e creazione, modifica e l'invio di messaggi di interazione.

Per ulteriori dettagli, vedere il nostro [messaggio App integrazione](~/ios/platform/message-app-integration/index.md) Guida.

## <a name="news-publisher-enhancements"></a>Miglioramenti di server di pubblicazione di notizie

Con iOS 10, Apple verrà consentono a chiunque di riviste principali e nuove organizzazioni blogger e indipendente dal server di pubblicazione per effettuare l'iscrizione e prodotto e fornire contenuto per l'app di notizie di Apple. Per ulteriori informazioni, vedere Apple [risorse notizie](https://newsresources.apple.com/) documentazione.

## <a name="providing-haptic-feedback"></a>Inviare commenti e suggerimenti tattili

L'iPhone 7 e iPhone 7 Plus, Apple ha incluso nuove risposte haptics che forniscono altri modi di stabilire fisicamente l'utente. Utilizzare le nuove opzioni commenti e suggerimenti tattili per ottenere l'attenzione dell'utente e consolidare le relative azioni.

Alcuni elementi dell'interfaccia utente predefiniti già commenti tattili, ad esempio le selezioni, commutatori e i dispositivi di scorrimento. iOS 10 verrà aggiunta la possibilità di trigger a livello di programmazione haptics utilizzando una sottoclasse concreta la `UIFeedbackGenerator` classe.

Per ulteriori dettagli, vedere il nostro [fornire Feedback tattili](~/ios/user-interface/ios-ui/haptic-feedback.md) Guida.

## <a name="proactive-suggestions"></a>Suggerimenti attiva

iOS 10 presenta nuove modalità della Guida del progetto per un'app per consentire al sistema in modo proattivo presentare informazioni utili automaticamente all'utente in momenti appropriati. Come iOS 9 fornita la possibilità di aggiungere una ricerca approfondita all'app usando Spotlight, uniforme e i suggerimenti Siri con iOS 10 che un'app è possibile esporre funzionalità che possono essere presentate all'utente dal sistema da all'interno delle posizioni seguenti:

- La selezione di App
- Schermata di blocco
- CarPlay
- Mappe
- Interazioni di Siri
- Suggerimenti QuickType 

Un'app espone questa funzionalità per il sistema utilizzando una raccolta di tecnologie, ad esempio [NSUserActivity](https://developer.xamarin.com/api/type/Foundation.NSUserActivity/), markup web, componenti di base Spotlight, MapKit, Media Player e UIKit.

Per ulteriori dettagli, vedere il nostro [Introduzione ai suggerimenti proattiva](~/ios/platform/search/proactive-suggestions.md) Guida.

## <a name="request-app-review"></a>Richiesta di revisione di App

Nuovi utenti di iOS 10.3, il `RequestReview()` metodo consente a un'app iOS chiedere all'utente di velocità o esaminarla. Durante questo metodo può essere chiamato in qualsiasi punto in cui è utile nell'esperienza dell'utente, il processo di revisione è governato e gestito da criteri di App Store. Di conseguenza, questo metodo può o non potrebbe visualizzare un avviso e non dovrebbe mai essere chiamato in risposta a un'azione dell'utente, ad esempio toccando un pulsante.

Per ulteriori dettagli, consultare il nostro [richiesta di revisione di App](~/ios/platform/request-app-review.md) Guida.

## <a name="security-and-privacy-enhancements"></a>Miglioramenti di Privacy e sicurezza

Apple ha migliorato notevolmente sia sicurezza e privacy in iOS 10 che lo sviluppatore consente di migliorare la sicurezza delle proprie App e garantire la privacy dell'utente finale.

Di conseguenza, App in esecuzione su iOS 10 (o versioni successive) dichiarare in modo statico l'intenzione di accedere a funzionalità specifiche o informazioni utente immettendo una o più Privacy chiavi specifiche nella loro `Info.plist` file segnalare all'utente perché l'app desidera accedere.

Per ulteriori dettagli, consultare il nostro [miglioramenti di Privacy e sicurezza](~/ios/app-fundamentals/security-privacy.md) Guida.

## <a name="sirikit"></a>SiriKit

Nuovo in iOS 10, SiriKit consente un'app xamarin di fornire servizi sono accessibili all'utente l'utilizzo di Siri nel dispositivo iOS. Questa funzionalità viene fornita in uno o più estensioni di App utilizzando il nuovo **intenti** e **dell'interfaccia utente intenti** Framework.

SiriKit supporta i domini di servizio seguenti:

- Audio o video chiamata.
- Prenotazione di un interscambio.
- Gestione allenamenti.
- Messaggistica.
- Ricerca foto.
- Inviare o ricevere i pagamenti.

Quando l'utente effettua una richiesta di Siri che include uno dei servizi dell'estensione App, SiriKit invia l'estensione un **finalità** oggetto che descrive la richiesta dell'utente insieme a eventuali dati di supporto. L'estensione dell'App, quindi genera appropriata **risposta** dell'oggetto per il determinato **finalità**, che riporta in dettaglio come l'estensione può gestire la richiesta.

Mentre Siri gestisce in genere tutte le interazioni utente, è possibile utilizzare l'estensione dell'App di **dell'interfaccia utente con finalità di** framework per presentare un completo e personalizzata interfaccia utente dotato di branding l'app e informazioni aggiuntive.

Per ulteriori dettagli, consultare il nostro [Introduzione a SiriKit](~/ios/platform/sirikit/index.md) Guida.

## <a name="speech-recognition"></a>Riconoscimento vocale

iOS 10 include una nuova API riconoscimento vocale che consente all'app supportare il riconoscimento vocale continua e trascrivere vocale (da flussi audio in tempo reale o registrati) nel testo.

Poiché il riconoscimento vocale richiede la trasmissione e l'archiviazione temporanea dei dati nei server di Apple, l'app _deve_ richiedere l'autorizzazione dell'utente per eseguire il riconoscimento includendo il `NSSpeechRecognitionUsageDescription` chiave nel relativo `Info.plist` file e la chiamata di `SFSpeechRecognizer.RequestAutorization` metodo.

Per ulteriori dettagli, consultare il nostro [Introduzione al riconoscimento vocale](~/ios/platform/speech.md) Guida.

## <a name="user-notifications"></a>Notifiche utente

Nuovo a 10, la notifica all'utente consente di framework per la gestione delle notifiche locali e remote e il recapito di iOS. Usando questo framework, l'app o l'estensione dell'App può pianificare il recapito delle notifiche locale specificando un set di condizioni, ad esempio percorso o l'ora del giorno.

Inoltre, l'app o l'estensione può ricevere (e potenzialmente modificare) le notifiche locali e remote come vengano recapitati a dispositivo iOS dell'utente.

Il nuovo framework di interfaccia utente di notifica utente consente l'app o l'estensione dell'App per personalizzare l'aspetto delle notifiche locali e remote quando sono presentati all'utente.

Per ulteriori dettagli, consultare il nostro [Framework notifiche utente](~/ios/platform/user-notifications/index.md) Guida.

## <a name="video-subscriber-account"></a>Account del sottoscrittore video

Nuovo per iOS 10, il framework di Video sottoscrittore Account consente alle App tale supporto autenticato streaming o video on Demand per l'autenticazione con il provider via cavo o satellite TV con un'esperienza Single Sign-in per l'utente finale.

## <a name="wide-color"></a>Colore Wide

iOS 10 estende il supporto per i formati di pixel di intervallo esteso e spazi colore ampia in tutto il sistema incluso Framework, ad esempio grafica di base, l'immagine di base, Metal e AVFoundation. Supporto per i dispositivi dotati di schermi di colori ampia ulteriormente è agevolato fornendo questo comportamento in tutto lo stack dell'intero grafico.

Inoltre, [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) è stato modificato per funzionare nel nuovo estesi **sRGB** spazio colore, rendendo più semplice la combinazione di colori in gamme di colori ampia senza perdita significativa delle prestazioni.

Apple offre le seguenti procedure consigliate quando si lavora con colori wide:

- [UIColor](https://developer.xamarin.com/api/type/UIKit.UIColor/) ora utilizza sRGB spazio colore e non verranno più clamp valori per il `0.0` a `1.0` intervallo. Se l'applicazione si basa sul comportamento clamp precedente, dovrà essere modificato per iOS 10.
- Nell'ambiente di disegno verrà configurato per lo spazio colore sRGB durante l'esecuzione di personalizzato `UIView` disegno in un iPad Pro.
- Se l'app esegue il rendering personalizzato di `UIImages`, usare il nuovo [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) classe per specificare l'utilizzo dei formati di intervallo esteso o standard-range.
- Quando si utilizza un'API di basso livello, ad esempio grafica di base o Metal per fornire l'elaborazione di immagini, lo sviluppatore deve utilizzare un intervallo esteso colore pixel e spazio di formato che supporta valori a virgola mobile a 16 bit. Se necessario, lo sviluppatore dovrà clamp manualmente i valori dei componenti di colore.
- Grafica di base, immagine di Core e Metal prestazioni shader fornire nuovi metodi per la conversione tra gli spazi di due colori.

Per ulteriori dettagli, vedere il nostro [Introduzione ai colori ampia](~/ios/platform/wide-color.md) Guida.

## <a name="widget-enhancements"></a>Miglioramenti di widget

Apple ha introdotto numerosi miglioramenti al sistema di Widget per assicurarti che il widget eccezionale su eventuali sfondo che esiste nel nuovo iOS 10 schermata di blocco. Il [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) proprietà è deprecata ed è stata sostituita con il nuovo [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) o [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect) proprietà. Inoltre, ora contengono widget un [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) proprietà che consente agli sviluppatori di descrivere la quantità di contenuto è disponibile e consente all'utente di espandere e comprimere il contenuto.

Per ulteriori dettagli, vedere il nostro [ricerca e schermata Home i miglioramenti di Widget](~/ios/platform/search/widgets.md) Guida.

## <a name="additional-framework-changes"></a>Modifiche di .NET Framework

Oltre alle modifiche framework principali e le aggiunte elencate in precedenza, Apple ha molte modifiche aggiuntive framework secondaria iOS 10.

Per ulteriori dettagli, consultare il nostro [ulteriori modifiche di Framework](~/ios/platform/introduction-to-ios10/additional-framework-changes.md) Guida.

## <a name="deprecated-apis"></a>API deprecate

Le seguenti API sono state deprecate in iOS 10:

- Il `CKDiscoverAllContactsOperation`, `CKDiscoveredUserInfo`, `CKDiscoverUserInfosOperation` e `CKFetchRecordChangesOperation` classi sono state deprecate in CloudKit per iOS 10. Utilizzare il [CKDiscoverAllUserIdentitiesOperation](https://developer.xamarin.com/api/type/CloudKit.CKDiscoverUserIdentitiesOperation/), [CKUserIdentity](https://developer.xamarin.com/api/type/CloudKit.CKUserIdentity/) e [CKFetchRecordZoneChangesOperation](https://developer.xamarin.com/api/type/CloudKit.CKFetchRecordZoneChangesOperation/) classi (che supportano la condivisione dei record) invece.
- Diversi [CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) API (ad esempio le sottoscrizioni basate su query e zona) sono state deprecate. Utilizzare il [CKRecordZoneSubscription](https://developer.xamarin.com/api/type/CloudKit.CKRecordZoneSubscription/) e [CKQuerySubscription](https://developer.xamarin.com/api/type/CloudKit.CKQuerySubscription/) API alternativa.
- [NSPersistentStoreCoordnator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/) simboli relativi al contenuto diffuso sono stati deprecati.
- `ADBannerView`, `ADInterstitialAd` e relativi simboli di [UIViewController](https://developer.xamarin.com/api/type/UIKit.UIViewController/) classe sono state deprecate.
- [SKUniform](https://developer.apple.com/reference/spritekit/skuniform) simboli associati a valori a virgola mobile sono stati deprecati.
- Il `UILocalNotification`, `UIMutableUserNotificationAction`, `UIMutableUserNotificationCategory`, `UIUserNotificationAction`, `UIUserNotificationCategory` e `UIUserNotificationSettings` classi di UIKit sono state deprecate. Utilizzare il [notifiche utente](#User-Notifications) framework invece.
- Il `HandleActionForLocalNotification`, `HandleActionForRemoteNotification`, `DidReceiveLocalNotification` e `DidReceiveRemoteNotification` WatchKit metodi deprecati. Utilizzare il `HandleActionForNotification` e `DidReceiveNotification` metodi invece.
- Il `DidReceiveLocalNotification` e `DidReceiveRemoteNotification` metodi di [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) sono state deprecate. Creare un'istanza di [UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) che implementa i metodi appropriati e assegnarlo al `Delegate` proprietà del [UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter) oggetto.
- Il **Game Center App** è stato deprecato e rimosso da iOS. Se l'app Usa GameKit, si _deve_ presentare la propria interfaccia per visualizzare le caratteristiche di GameKit come classifiche e così via.

Vedere Apple [iOS 9.3 a differenze tra API iOS 10.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html) documentazione relativa a un elenco completo di deprecations.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Novità di iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
