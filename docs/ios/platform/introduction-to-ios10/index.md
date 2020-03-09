---
title: Introduzione a iOS 10
description: Questo articolo presenta tutte le API e le funzionalità nuove e modificate disponibili in iOS 10 per gli sviluppatori Novell. iOS.
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: ce262faf2d79e6a2cc969df582446fdc2ec29bde
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910857"
---
# <a name="introduction-to-ios-10"></a>Introduzione a iOS 10

Con il nuovo SDK per iOS 10, Apple ha incluso nuove API e servizi che consentono allo sviluppatore di creare nuove categorie di app e funzionalità. Un'app iOS ora può estendere le app messaggi, Siri, telefono e mappe per fornire funzionalità avanzate e accattivanti all'utente finale che in precedenza non era disponibile.

Per altre informazioni su iOS 10, vedere la documentazione di Apple [iOS + Apps](https://developer.apple.com/ios/) .

## <a name="whats-new-in-ios-10"></a>Novità di iOS 10

Apple ha aggiunto diverse nuove API e servizi in iOS 10, oltre a molti miglioramenti alle funzionalità esistenti, tra cui:

## <a name="adapting-to-the-true-tone-display"></a>Adattamento alla visualizzazione del tono reale

La tecnologia di visualizzazione del vero tono di Apple usa il sensore luce di ambiente in un dispositivo iOS per modificare dinamicamente il colore e l'intensità dello schermo in modo che corrispondano alle condizioni di illuminazione correnti. iOS 10 fornisce la nuova chiave [UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31) che può essere aggiunta al file di `Info.plist` dell'app e controlla il modo in cui il valore True Tone applica lo spostamento dei colori standard. 

Sono disponibili i valori seguenti.

- `UIWhitePointAdaptivityStyleStandard` **predefinita** : usare il punto di adattività bianco standard.
- `UIWhitePointAdaptivityStyleReading`: usato per le app con lo stato attivo per la lettura.
- `UIWhitePointAdaptivityStyleGame`: usato per le app incentrate sul gioco.
- `UIWhitePointAdaptivityStyleVideo`: usato per le app orientate ai video.
- `UIWhitePointAdaptivityStylePhoto`: usato per le app incentrate sulla fotografia, in cui la fedeltà dei colori è più importante rispetto alle rettifiche di punti bianco ambientali.

## <a name="app-extensions"></a>Estensioni delle app

Apple ha fornito diversi nuovi punti di estensione dell'app in iOS 10:

- Directory di chiamata
- Interfaccia utente Intent e Intent
- Messaggi
- Contenuto della notifica
- Notification Services
- Sticker Pack

Inoltre, le estensioni dell'app tastiera di terze parti presentano i miglioramenti seguenti:

- La nuova proprietà `DocumentInputMode` della classe `UITextDocumentProxy` può determinare la lingua di input di un documento e consentire all'estensione della tastiera di allinearsi a tale lingua.
- Il nuovo metodo di `HandleInputModeList` consente all'estensione della tastiera di visualizzare il menu di selezione della tastiera del sistema in risposta alla chiave globo toccata.

Per altre informazioni, vedere l' [Introduzione alle estensioni](~/ios/platform/extensions.md), l' [integrazione di app](~/ios/platform/message-app-integration/index.md)per i messaggi, l'introduzione [ai suggerimenti proattivi](~/ios/platform/search/proactive-suggestions.md), l' [Introduzione a SiriKit](~/ios/platform/sirikit/index.md), l' [Introduzione alle notifiche utente](~/ios/platform/user-notifications/index.md) e la [Guida alla programmazione dell'estensione app](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)di Apple.

## <a name="app-search-enhancements"></a>Miglioramenti per la ricerca nelle app

I principali Spotlight in iOS 10 forniscono diversi miglioramenti alla ricerca di app, ad esempio:

- **Popolarità del collegamento di crowdsourcing (con privacy differenziale)** : offre un modo per promuovere il contenuto delle app con collegamento profondo nei risultati della ricerca.
- **Ricerca in-app** : usare la nuova classe `CSSearchQuery` per fornire funzionalità di ricerca Spotlight in-app simili a quelle del funzionamento delle app di posta elettronica, messaggi e note.
- **Continuazione ricerca** : consente a un utente di avviare una ricerca in Spotlight o Safari, quindi aprire un'app e continuare la ricerca.
- **Visualizzazione dei risultati della convalida** : [lo strumento di convalida dell'API ricerca app](https://search.developer.apple.com/appsearch-validation-tool) di Apple ora Visualizza una rappresentazione visiva del markup di un sito Web e il collegamento profondo quando si preformano i test.
- **Condivisione immagini app messaggio** : consente di visualizzare le immagini in-app più diffuse fornite per la condivisione nei messaggi (tramite un'estensione di app per i messaggi) nelle ricerche Spotlight.

Per ulteriori informazioni, vedere la guida ai [miglioramenti](~/ios/platform/search/app-search-enhancements.md) per la ricerca di app.

## <a name="apple-pay-enhancements"></a>Miglioramenti di Apple Pay

Apple ha apportato diversi miglioramenti a Apple Pay in iOS 10 che consentono all'utente di effettuare pagamenti sicuri da siti Web e di interagire con Siri e Maps.

Con iOS 10 sono state aggiunte diverse nuove API che funzionano con iOS e watchos per supportare le reti di pagamento dinamiche e un nuovo ambiente di test sandbox.

Inoltre, il Framework PassKit è stato ampliato per supportare Apple Pay al di fuori della `UIKit` e consentire alle emittenti di schede di presentare le proprie schede dall'interno delle app.

Per ulteriori informazioni, vedere la guida per i miglioramenti apportati [Apple pay](~/ios/platform/apple-pay.md) .

## <a name="alternate-app-icons"></a>Icone alternative per le app

Apple ha aggiunto diversi miglioramenti a iOS 10,3 che consentono a un'app di gestire la relativa icona:

- `ApplicationIconBadgeNumber`: Ottiene o imposta la notifica dell'icona dell'app nel Springboard.
- `SupportsAlternateIcons`-se `true` l'app dispone di un set alternativo di icone.
- `AlternateIconName`: restituisce il nome dell'icona alternativa attualmente selezionata o `null` se si utilizza l'icona primaria.
- `SetAlternameIconName`: usare questo metodo per impostare l'icona dell'app sull'icona alternativa specificata.

Per ulteriori informazioni, vedere la Guida alle [icone alternative](~/ios/app-fundamentals/images-icons/alternate-app-icons.md) per le app.

## <a name="introduction-to-callkit"></a>Introduzione a CallKit

La nuova API CallKit in iOS 10 consente alle app VOIP di integrarsi con l'interfaccia utente di iPhone e fornire all'utente finale un'interfaccia familiare ed esperienza. Con questa API, gli utenti possono visualizzare e interagire con le chiamate VOIP dalla schermata di blocco del dispositivo iOS e gestire i contatti usando le visualizzazioni **Preferiti** e **recenti** dell'app Phone.

Inoltre, l'API di CallKit offre la possibilità di creare estensioni di app che possono associare un numero di telefono a un nome (ID chiamante) o indicare al sistema quando un numero deve essere bloccato (blocco delle chiamate).

Per ulteriori informazioni, vedere la Guida [introduttiva a Callkit](~/ios/platform/callkit.md) .

## <a name="message-app-integration"></a>Integrazione dell'app Messaggi

iOS 10 consente l'inclusione di un'estensione di app per i messaggi nella soluzione Novell. iOS che si integra con l'app **messages** e presenta nuove funzionalità all'utente. L'estensione può inviare testo, adesivi, file multimediali e messaggi interattivi. Sono disponibili due tipi di estensione dell'app per i messaggi:

- **Sticker pack** : contiene una raccolta di adesivi che l'utente può aggiungere a un messaggio. È possibile creare pacchetti di adesivi senza scrivere codice.
- **app iMessage** : può presentare un'interfaccia utente personalizzata all'interno dell'app Messages per la selezione di adesivi, l'immissione di testo, inclusi i file multimediali (con conversioni di tipi facoltative) e la creazione, la modifica e l'invio di messaggi di interazione.

Per ulteriori informazioni, vedere la Guida all' [integrazione dell'app Message](~/ios/platform/message-app-integration/index.md) .

## <a name="news-publisher-enhancements"></a>Miglioramenti di News Publisher

Con iOS 10, Apple consentirà a tutti i blogger e i publisher indipendenti di iscriversi e fornire contenuti all'app Apple News. Per altre informazioni, vedere la documentazione [sulle risorse](https://newsresources.apple.com/) di Apple.

## <a name="providing-haptic-feedback"></a>Implementazione del feedback aptico

Con iPhone 7 e iPhone 7 Plus, Apple ha incluso nuove risposte haptics che offrono ulteriori modalità di coinvolgimento fisico dell'utente. Usare le nuove opzioni di feedback tattile per attirare l'attenzione dell'utente e rafforzare le azioni.

Molti elementi dell'interfaccia utente incorporati forniscono già Commenti tattili, ad esempio Picker, commutatori e dispositivi di scorrimento. iOS 10 aggiunge ora la possibilità di attivare haptics a livello di codice usando una sottoclasse concreta della classe `UIFeedbackGenerator`.

Per ulteriori informazioni, vedere la guida per il [feedback tattile](~/ios/user-interface/ios-ui/haptic-feedback.md) .

## <a name="proactive-suggestions"></a>Suggerimenti proattivi

iOS 10 presenta nuovi modi per guidare l'engagement a un'app consentendo al sistema di presentare in modo proattivo le informazioni utili automaticamente all'utente in momenti appropriati. Così come iOS 9 ha fornito la possibilità di aggiungere una ricerca completa all'app usando i suggerimenti Spotlight, Componi e Siri, con iOS 10 un'app può esporre la funzionalità che può essere presentata all'utente dal sistema all'interno delle seguenti posizioni:

- Switcher dell'app
- Schermata di blocco
- CarPlay
- Mappe
- Interazioni Siri
- Suggerimenti QuickType

Un'app espone questa funzionalità al sistema usando una raccolta di tecnologie come [NSUserActivity](xref:Foundation.NSUserActivity), markup web, Core Spotlight, MapKit, Media Player e UIKit.

Per ulteriori informazioni, vedere la Guida [introduttiva ai suggerimenti proattivi](~/ios/platform/search/proactive-suggestions.md) .

## <a name="request-app-review"></a>Richiedere una recensione per un'app

Una novità di iOS 10,3, il metodo `RequestReview()` consente a un'app per iOS di richiedere all'utente di valutarla o esaminarla. Sebbene questo metodo possa essere chiamato in qualsiasi momento in cui è sensato per l'esperienza utente, il processo di revisione è governato e gestito dai criteri dell'app Store. Di conseguenza, questo metodo può o meno visualizzare un avviso e non deve mai essere chiamato in risposta a un'azione dell'utente, ad esempio il tocco di un pulsante.

Per ulteriori informazioni, vedere la guida alla [revisione dell'app request](~/ios/platform/request-app-review.md) .

## <a name="security-and-privacy-enhancements"></a>Miglioramenti alla sicurezza e alla privacy

Apple ha apportato diversi miglioramenti alla sicurezza e alla privacy in iOS 10 che aiuteranno lo sviluppatore a migliorare la sicurezza delle app e garantire la privacy degli utenti finali.

Di conseguenza, le app in esecuzione in iOS 10 (o versioni successive) devono dichiarare in modo statico l'intenzione di accedere a funzionalità specifiche o a informazioni utente immettendo una o più chiavi specifiche per la privacy nei file `Info.plist` che spieghino all'utente il motivo per cui l'app desidera ottenere l'accesso.

Per ulteriori informazioni, vedere la guida ai [miglioramenti per la sicurezza e la privacy](~/ios/app-fundamentals/security-privacy.md) .

## <a name="sirikit"></a>SiriKit

Una novità di iOS 10, SiriKit consente a un'app Novell. iOS di fornire servizi accessibili all'utente tramite Siri in un dispositivo iOS. Questa funzionalità è disponibile in una o più estensioni di app usando i nuovi framework **dell'interfaccia utente** **Intent** e Intent.

SiriKit supporta i seguenti domini del servizio:

- Chiamata audio o video.
- Prenotare una corsa.
- Gestione degli allenamenti.
- Messaggistica.
- Ricerca di foto.
- Invio o ricezione dei pagamenti.

Quando l'utente effettua una richiesta di Siri che interessa uno dei servizi dell'estensione dell'app, SiriKit invia all'estensione un oggetto **preventivo** che descrive la richiesta dell'utente insieme a tutti i dati di supporto. L'estensione dell'app genera quindi l'oggetto **Response** appropriato per la **finalità**specificata, in cui viene illustrato in dettaglio il modo in cui l'estensione può gestire la richiesta.

Mentre Siri gestisce in genere tutte le interazioni dell'utente, l'estensione dell'app può usare il Framework dell' **interfaccia utente Intent** per presentare un'interfaccia utente personalizzata avanzata con la personalizzazione dell'app e informazioni aggiuntive.

Per ulteriori informazioni, vedere la Guida [introduttiva a SiriKit](~/ios/platform/sirikit/index.md) .

## <a name="speech-recognition"></a>Riconoscimento vocale

iOS 10 include un nuovo Speech API che consente all'app di supportare il riconoscimento vocale continuo e la traduzione vocale (da flussi audio in tempo reale o registrato).

Poiché il riconoscimento vocale richiede la trasmissione e l'archiviazione temporanea dei dati nei server Apple, l'app _deve_ richiedere l'autorizzazione dell'utente per eseguire il riconoscimento includendo la chiave di `NSSpeechRecognitionUsageDescription` nel file di `Info.plist` e chiamando il metodo di `SFSpeechRecognizer.RequestAutorization`.

Per ulteriori informazioni, vedere la Guida [introduttiva al riconoscimento vocale](~/ios/platform/speech.md) .

## <a name="user-notifications"></a>Notifiche utente

Una novità di iOS 10, il Framework di notifica utente consente la distribuzione e la gestione di notifiche locali e remote. Con questo Framework, l'estensione app o app può pianificare il recapito delle notifiche locali specificando un set di condizioni, ad esempio la posizione o l'ora del giorno.

Inoltre, l'app o l'estensione può ricevere e potenzialmente modificare le notifiche locali e remote quando vengono recapitate al dispositivo iOS dell'utente.

Il nuovo Framework dell'interfaccia utente di notifica utente consente all'app o all'estensione dell'app di personalizzare l'aspetto delle notifiche locali e remote quando vengono presentate all'utente.

Per ulteriori informazioni, vedere la guida del [Framework per le notifiche utente](~/ios/platform/user-notifications/index.md) .

## <a name="video-subscriber-account"></a>Account Sottoscrittore video

Novità per iOS 10, il Framework di account Sottoscrittore video consente alle app che supportano flussi autenticati o video su richiesta di eseguire l'autenticazione con il provider TV via cavo o satellite usando un'esperienza di accesso Single Sign-on per l'utente finale.

## <a name="wide-color"></a>Wide Color

iOS 10 estende il supporto per i formati di pixel estesi e gli spazi dei colori a gamma ampia in tutto il sistema, inclusi i Framework come la grafica principale, le immagini di base, i metal e AVFoundation. Il supporto per i dispositivi con schermi Wide Color è ulteriormente facilitato fornendo questo comportamento nell'intero stack di grafica.

Inoltre, [UIKit](xref:UIKit) è stato modificato in modo da funzionare nel nuovo spazio dei colori di **sRGB** esteso, semplificando la combinazione di colori in ampie gamme di colori senza perdite di prestazioni significative.

Apple offre le seguenti procedure consigliate per l'utilizzo di colori estesi:

- [UIColor](xref:UIKit.UIColor) USA ora lo spazio dei colori sRGB e non blocca più i valori per il `0.0` `1.0` intervallo. Se l'app si basa sul comportamento del morsetto precedente, sarà necessario modificarlo per iOS 10.
- L'ambiente di disegno verrà configurato per lo spazio colore sRGB quando si esegue un disegno personalizzato di `UIView` su un iPad Pro.
- Se l'app esegue il rendering personalizzato di `UIImages`, usare la nuova classe [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) per specificare l'uso dei formati di intervallo esteso o standard.
- Quando si usa un'API di basso livello, ad esempio grafica di base o metal, per fornire l'elaborazione di immagini, lo sviluppatore deve usare uno spazio dei colori di intervallo esteso e un formato pixel che supporti i valori a virgola mobile a 16 bit. Se necessario, lo sviluppatore dovrà bloccare manualmente i valori dei componenti del colore.
- I componenti grafici di base, le immagini di base e gli shader con prestazioni Metal forniscono tutti i nuovi metodi per la conversione tra i due spazi dei colori.

Per saperne di più, vedere la Guida [introduttiva a wide color](~/ios/platform/wide-color.md) .

## <a name="widget-enhancements"></a>Miglioramenti del widget

Apple ha introdotto diversi miglioramenti al sistema widget per garantire che i widget risultino ottimali in qualsiasi sfondo presente nella nuova schermata di blocco di iOS 10. La proprietà [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) è stata deprecata ed è stata sostituita con le nuove proprietà [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) o [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect) . Inoltre, i widget contengono ora una proprietà [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) che consente allo sviluppatore di descrivere la quantità di contenuto disponibile e consente all'utente di espandere e comprimere il contenuto.

Per ulteriori informazioni, vedere la guida ai [miglioramenti del widget per la schermata di ricerca e la schermata iniziale](~/ios/platform/search/widgets.md) .

## <a name="additional-framework-changes"></a>Modifiche aggiuntive del Framework

Oltre alle principali modifiche e aggiunte al Framework elencate in precedenza, Apple ha apportato molte modifiche di Framework secondarie aggiuntive in iOS 10.

Per ulteriori informazioni, vedere la Guida alle [modifiche aggiuntive del Framework](~/ios/platform/introduction-to-ios10/additional-framework-changes.md) .

## <a name="deprecated-apis"></a>API deprecate

Le API seguenti sono state deprecate in iOS 10:

- Le classi `CKDiscoverAllContactsOperation`, `CKDiscoveredUserInfo`, `CKDiscoverUserInfosOperation` e `CKFetchRecordChangesOperation` sono state deprecate in CloudKit per iOS 10. Usare invece le classi [CKDiscoverAllUserIdentitiesOperation](xref:CloudKit.CKDiscoverUserIdentitiesOperation), [CKUserIdentity](xref:CloudKit.CKUserIdentity) e [CKFetchRecordZoneChangesOperation](xref:CloudKit.CKFetchRecordZoneChangesOperation) (che supportano la condivisione dei record).
- Diverse API [CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) (ad esempio sottoscrizioni basate su zone e basate su query) sono state deprecate. Usare invece le API [CKRecordZoneSubscription](xref:CloudKit.CKRecordZoneSubscription) e [CKQuerySubscription](xref:CloudKit.CKQuerySubscription) .
- I simboli [NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator) correlati al contenuto onnipresente sono stati deprecati.
- `ADBannerView`, `ADInterstitialAd` e i simboli correlati nella classe [UIViewController](xref:UIKit.UIViewController) sono stati deprecati.
- I simboli [SKUniform](https://developer.apple.com/reference/spritekit/skuniform) correlati ai valori a virgola mobile sono stati deprecati.
- Le classi `UILocalNotification`, `UIMutableUserNotificationAction`, `UIMutableUserNotificationCategory`, `UIUserNotificationAction`, `UIUserNotificationCategory` e `UIUserNotificationSettings` di UIKit sono state deprecate. Usare invece il framework delle [notifiche utente](#user-notifications) .
- I metodi `HandleActionForLocalNotification`, `HandleActionForRemoteNotification`, `DidReceiveLocalNotification` e `DidReceiveRemoteNotification` WatchKit sono stati deprecati. Usare invece i metodi `HandleActionForNotification` e `DidReceiveNotification`.
- I metodi `DidReceiveLocalNotification` e `DidReceiveRemoteNotification` di [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) sono stati deprecati. Creare un'istanza di [UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) che implementi i metodi appropriati e assegnarla alla proprietà `Delegate` dell'oggetto [UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter) .
- L' **App Game Center** è stata deprecata e rimossa da iOS. Se l'app usa GameKit, _deve_ presentare una propria interfaccia per visualizzare le funzionalità di gamekit, ad esempio le classifiche e così via.

Per un elenco completo delle funzionalità deprecate, vedere la documentazione relativa [alle differenze tra l'API ios 9,3 e ios 10,0](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html) di Apple.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
