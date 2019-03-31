---
title: Introduzione a iOS 10
description: Questo articolo illustra tutte le nuove e modificate le API e funzionalità disponibili in iOS 10 per gli sviluppatori di xamarin. IOS.
ms.prod: xamarin
ms.assetid: FB91DFFE-CF5E-4253-92CB-78A6371259D9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
---

# <a name="introduction-to-ios-10"></a>Introduzione a iOS 10

_Questo articolo illustra tutte le nuove e modificate le API e funzionalità disponibili in iOS 10 per gli sviluppatori di xamarin. IOS._

## <a name="introducing-ios-10"></a>Introduzione a iOS 10

Con il nuovo iOS 10 SDK, Apple ha incluso nuove API e servizi che consentono allo sviluppatore di creare nuove categorie di App e funzionalità. Un'app per iOS è ora possibile estendere le app dei messaggi, Siri, telefono e alle mappe per offrire funzionalità avanzate e accattivanti per l'utente finale che non erano precedentemente disponibile.

Per altre informazioni in iOS 10, vedere di Apple [iOS + app](https://developer.apple.com/ios/) documentazione.

## <a name="whats-new-in-ios-10"></a>What ' s New in iOS 10

Apple ha aggiunto diverse nuove API e servizi in iOS 10 e molti miglioramenti alle funzionalità esistenti, tra cui:


## <a name="adapting-to-the-true-tone-display"></a>Adattare la visualizzazione di tono True

Tecnologia True tono visualizzare Apple Usa il sensore di luce in un dispositivo iOS per adeguare dinamicamente il colore e l'intensità dello schermo in modo che corrisponda le condizioni correnti di illuminazione. iOS 10 fornisce la nuova [UIWhitePointAdaptivityStyle](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW31) chiave che può essere aggiunta all'app `Info.plist` file e i controlli come tono True applica lo spostamento di colori standard. 

Sono disponibili i seguenti valori:

- `UIWhitePointAdaptivityStyleStandard` **Default** -usare l'adattabilità di punti vuoti standard.
- `UIWhitePointAdaptivityStyleReading` -Viene usato per le app destinate durante la lettura.
- `UIWhitePointAdaptivityStyleGame` -Viene usato per le app incentrata sul gioco.
- `UIWhitePointAdaptivityStyleVideo` -Viene usato per le app basate su video.
- `UIWhitePointAdaptivityStylePhoto` -Viene usato per le app destinate fotografia dove sono più importanti modifiche ambientali punti vuoti fedeltà dei colori.

<a name="app-extensions" />

## <a name="app-extensions"></a>Estensioni dell'App

Apple ha fornito alcuni nuovi punti di estensione di App in iOS 10:

- Directory di chiamata
- Gli Intent e interfaccia utente Intent
- Messaggi
- Contenuto della notifica
- Notification Services
- Pack adesivi

Inoltre, 3rd party tastiera App estensioni disponibili i miglioramenti seguenti:

- Il nuovo `DocumentInputMode` proprietà del `UITextDocumentProxy` classe può determinare la lingua di input di un documento e consentire l'estensione per tastiera in modo da allinearsi a tale lingua.
- Il nuovo `HandleInputModeList` metodo consente l'estensione per tastiera visualizzare dal menu di selezione della tastiera del sistema in risposta alla chiave del mondo viene toccato.

Per altre informazioni, vedere la [Introduzione alle estensioni](~/ios/platform/extensions.md), [messaggio App Integration](~/ios/platform/message-app-integration/index.md), [Introduzione ai suggerimenti proattivi](~/ios/platform/search/proactive-suggestions.md), [ Introduzione a SiriKit](~/ios/platform/sirikit/index.md), [Introduzione alle notifiche utente](~/ios/platform/user-notifications/index.md) e di Apple [Guida per programmatori dell'estensione App](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214).

## <a name="app-search-enhancements"></a>Miglioramenti della ricerca di App

Core Spotlight in iOS 10 offre numerosi miglioramenti alla ricerca di App, ad esempio:

- **Popolarità di collegamento diretto di Crowdsourcing (con privacy differenziale)** -fornisce un modo per promuovere il contenuto di app con collegamento diretto nei risultati della ricerca.
- **La ricerca in-App** -usare il nuovo `CSSearchQuery` classe per fornire capacità di ricerca Spotlight in-app simile al funzionamento delle app di posta elettronica, messaggi e note.
- **Eseguire la ricerca continuazione** : consente agli utenti di avviare una ricerca di Spotlight o Safari, quindi aprire un'app e continuare la ricerca.
- **Visualizzazione dei risultati della convalida** -Apple [lo strumento di convalida API di ricerca di App](https://search.developer.apple.com/appsearch-validation-tool) ora visualizza una rappresentazione visiva di markup e il collegamento diretto di un sito Web quando preforming test.
- **Messaggio condivisione immagine delle App** -consente di immagini in-app popolari fornite per la condivisione in messaggi (tramite un'estensione dell'App messaggi) deve essere visualizzato nella ricerca Spotlight.

Per altre informazioni, vedere la [miglioramenti della ricerca App](~/ios/platform/search/app-search-enhancements.md) Guida.

## <a name="apple-pay-enhancements"></a>Miglioramenti di Apple Pay

Apple ha apportato diversi miglioramenti a Apple Pay iOS 10 che consentono all'utente di effettuare pagamenti sicuri da siti Web e tramite l'interazione con Siri e mappe.

Con iOS 10, diverse nuove API sono state aggiunte per l'utilizzo in iOS e watchOS per supportare le reti a pagamento dinamico e un nuovo ambiente di test di sandbox.

Inoltre, il framework di PassKit è stato ampliato per supportare Apple Pay di fuori di `UIKit` e consentire emittenti di smart card presentare le schede all'interno delle app.

Per altre informazioni, vedere la [Apple pagare miglioramenti](~/ios/platform/apple-pay.md) Guida.

## <a name="alternate-app-icons"></a>Icone dell'App alternativo

Apple ha aggiunti numerosi miglioramenti a iOS 10.3 che consentono a un'app gestire la relativa icona:

 - `ApplicationIconBadgeNumber` : Ottiene o imposta il badge dell'icona dell'app in Springboard.
 - `SupportsAlternateIcons` -Se `true` l'app ha un set alternativo di icone.
 - `AlternateIconName` : Restituisce il nome dell'icona alternativa attualmente selezionata o `null` se si usa l'icona principale.
 - `SetAlternameIconName` -Usare questo metodo per cambiare l'icona dell'app per l'icona alternativo specificato.

Per altre informazioni, vedere la [alternativo icone dell'App](~/ios/app-fundamentals/images-icons/alternate-app-icons.md) Guida.


## <a name="introduction-to-callkit"></a>Introduzione a CallKit

La nuova API CallKit in iOS 10 fornisce un modo per le app VOIP da integrare con l'interfaccia utente di iPhone e fornire un'interfaccia familiare ed esperienze per l'utente finale. Con questa API utenti di visualizzare e interagire con le chiamate VOIP dalla schermata di blocco del dispositivo iOS e gestire i contatti usando l'app telefono **Preferiti** e **recenti** viste.

Inoltre, l'API CallKit offre la possibilità di creare estensioni App che è possibile associare un numero di telefono a un nome (ID chiamante) o indicare il sistema quando deve essere un numero bloccato (chiamata di blocco).

Per altre informazioni, vedere la [Introduzione a Callkit](~/ios/platform/callkit.md) Guida.

## <a name="message-app-integration"></a>Integrazione dell'App messaggi

iOS 10 consente l'inclusione di un'estensione dell'App messaggi della soluzione xamarin. IOS che si integra con il **messaggi** app e presenta nuove funzionalità per l'utente. L'estensione può inviare testo, le etichette, i file multimediali e i messaggi interattivi. Sono disponibili due tipi di estensione per App i messaggi:

- **Pack adesivo** -contiene una raccolta di adesivi che l'utente può aggiungere a un messaggio. È possibile creare pacchetti di adesivi senza scrivere alcun codice.
- **iMessage App** -possono presentare un'interfaccia utente personalizzata nell'app messaggi per la selezione di adesivi, immettendo il testo, inclusi i file multimediali (con conversioni del tipo facoltativo) e creazione, modifica e l'invio di messaggi di interazione.

Per altre informazioni, vedere la [integrazione di App messaggio](~/ios/platform/message-app-integration/index.md) Guida.

## <a name="news-publisher-enhancements"></a>Miglioramenti di server di pubblicazione di notizie

Con iOS 10, Apple verrà consentire a chiunque di principali riviste e nuove organizzazioni blogger e indipendente dal server di pubblicazione per l'iscrizione e prodotto e Distribuisci i contenuti dell'app Apple News. Per altre informazioni, vedere di Apple [notizie risorse](https://newsresources.apple.com/) documentazione.

## <a name="providing-haptic-feedback"></a>Feedback Aptico

Sull'iPhone 7 e iPhone 7 Plus, Apple ha incluso nuove risposte haptics che forniscono altri modi per fisicamente coinvolgere l'utente. Usare le nuove opzioni tattili commenti e suggerimenti per rafforzare le relative azioni e ottenere l'attenzione dell'utente.

Diversi elementi dell'interfaccia utente predefiniti forniscono già del feedback aptico quali controlli di selezione, commutatori e i dispositivi di scorrimento. iOS 10 è ora aggiunta la possibilità di generare a livello di programmazione haptics usando una sottoclasse concreta del `UIFeedbackGenerator` classe.

Per altre informazioni, vedere la [pubblicazione di Feedback Aptico](~/ios/user-interface/ios-ui/haptic-feedback.md) Guida.

## <a name="proactive-suggestions"></a>Suggerimenti proattivi

iOS 10 offre nuove modalità della Guida di engagement in un'app, consentendo al sistema in modo proattivo presentare informazioni utili automaticamente all'utente in momenti appropriati. Altrettanto iOS 9 è stato possibile aggiungere la ricerca avanzata per l'app usando Spotlight, consegne e suggerimenti di Siri con iOS 10 che un'app può esporre la funzionalità che può essere presentata all'utente dal sistema da all'interno delle posizioni seguenti:

- La selezione di App
- Schermata di blocco
- CarPlay
- Mappe
- Interazioni tra Siri
- Suggerimenti QuickType 

Un'app espone la funzionalità per il sistema utilizzando, ad esempio una raccolta di tecnologie [NSUserActivity](xref:Foundation.NSUserActivity), markup web, Core Spotlight, MapKit, Media Player e UIKit.

Per altre informazioni, vedere la [Introduzione ai suggerimenti proattivi](~/ios/platform/search/proactive-suggestions.md) Guida.

## <a name="request-app-review"></a>Richiesta di revisione dell'App

Novità di iOS 10.3, il `RequestReview()` metodo consente a un'app iOS chiedere all'utente valutare o esaminarlo. Sebbene questo metodo può essere chiamato in qualsiasi punto in cui avrebbe senso nell'esperienza dell'utente, il processo di revisione è disciplinato e gestito da criteri di App Store. Di conseguenza, questo metodo può o potrebbe non essere visualizzato un avviso e non dovrebbe mai essere chiamato in risposta a un'azione dell'utente, ad esempio se si tocca un pulsante.

Per altre informazioni, vedere la [richiesta revisione App](~/ios/platform/request-app-review.md) Guida.

## <a name="security-and-privacy-enhancements"></a>Miglioramenti della protezione e Privacy

Apple ha apportato diversi miglioramenti a sia sicurezza e privacy in iOS 10 che aiuta gli sviluppatori migliorare la sicurezza delle proprie App e garantire la privacy dell'utente finale.

Di conseguenza, le App in esecuzione in iOS 10 (o versione successiva) in modo statico devono dichiarare manifestato l'intenzione di accedere a funzionalità specifiche o informazioni utente immettendo una o più delle chiavi specifici sulla Privacy in loro `Info.plist` file in cui viene illustrato all'utente perché l'app desidera ottenere l'accesso.

Per altre informazioni, vedere la [miglioramenti della protezione e Privacy](~/ios/app-fundamentals/security-privacy.md) Guida.

## <a name="sirikit"></a>SiriKit

Nuovo ai dispositivi iOS 10, SiriKit consente a un'app xamarin. IOS fornire servizi accessibili all'utente l'uso di Siri in un dispositivo iOS. Questa funzionalità viene fornita in uno o più l'estensione dell'App usando le nuove **Intent** e **dell'interfaccia utente Intent** Framework.

SiriKit supporta i domini del servizio seguenti:

- Audio o video chiamata.
- Una questione di prenotazione.
- La gestione di allenamenti.
- Messaggistica.
- Ricerca di foto.
- Inviare o ricevere i pagamenti.

Quando l'utente effettua una richiesta di Siri con uno dei servizi dell'estensione App, SiriKit invia l'estensione di un **finalità** oggetto che descrive la richiesta dell'utente insieme a eventuali dati di supporto. l'estensione dell'App, quindi genera appropriato **risposta** dell'oggetto per il determinato **finalità**, che riporta in dettaglio come l'estensione può gestire la richiesta.

Mentre Siri gestisce in genere ogni interazione dell'utente, è possibile usare l'estensione per App i **dell'interfaccia utente Intent** framework per presentare un avanzate e personalizzate dell'interfaccia utente dotata di informazioni personalizzate distintive dell'app e informazioni aggiuntive.

Per altre informazioni, vedere la [Introduzione a SiriKit](~/ios/platform/sirikit/index.md) Guida.

## <a name="speech-recognition"></a>Riconoscimento vocale

iOS 10 include una nuova API di riconoscimento vocale che consente all'app supportare continua riconoscimento vocale e trascrizione vocale (dal vivi o registrati flussi audio) nel testo.

Poiché il riconoscimento vocale di richiede la trasmissione e l'archiviazione temporanea dei dati nei server di Apple, l'app _deve_ richiedere l'autorizzazione dell'utente per eseguire il riconoscimento, includendo il `NSSpeechRecognitionUsageDescription` chiave nel relativo `Info.plist` file e la chiamata di `SFSpeechRecognizer.RequestAutorization` (metodo).

Per altre informazioni, vedere la [Introduzione al riconoscimento vocale](~/ios/platform/speech.md) Guida.

## <a name="user-notifications"></a>Notifiche utente

Novità di iOS 10, la notifica all'utente consente a framework per il recapito e la gestione delle notifiche locali e remote. Uso di questo framework, l'app o l'estensione dell'App può pianificare il recapito di notifiche locali specificando un set di condizioni, ad esempio posizione o l'ora del giorno.

Inoltre, l'app o l'estensione può ricevere (e potenzialmente modificare) le notifiche locali e remote quando vengono recapitate al dispositivo iOS dell'utente.

Il nuovo framework di interfaccia utente di notifica utente consente all'app o l'estensione dell'App per personalizzare l'aspetto delle notifiche locali e remote quando sono presentati all'utente.

Per altre informazioni, vedere la [Framework per le notifiche utente](~/ios/platform/user-notifications/index.md) Guida.

## <a name="video-subscriber-account"></a>Account sottoscrittore video

Nuovo per iOS 10, il framework di Account sottoscrittore Video consente App tale supporto autenticato streaming o video on Demand per eseguire l'autenticazione con i rispettivi provider TV satellite o cable usando un'esperienza Single-Sign-in per l'utente finale.

## <a name="wide-color"></a>Ampia gamma di colori

iOS 10 estende il supporto per formati pixel intervalli estesi e gli spazi colore a livello di gamma in tutto il sistema, tra cui Framework quali Core Graphics, immagine di base, bare Metal e AVFoundation. Supporto per i dispositivi dotati di schermi di ampia gamma di colori viene diminuito ulteriormente fornendo questo comportamento in tutto lo stack di grafica intero.

È inoltre [UIKit](xref:UIKit) è stato modificato a funzionare nella nuova esteso **sRGB** spazio colore, rendendo più semplice combinare i colori usati nel gamme di ampia gamma di colori senza riduzione significativa delle prestazioni.

Apple offre le seguenti procedure ottimali quando si lavora con i colori wide:

- [Le proprietà UIColor](xref:UIKit.UIColor) ora USA sRGB dello spazio dei colori e non verranno più limitare i valori per il `0.0` a `1.0` intervallo. Se l'app si basa sul comportamento del clamp precedente, devono essere modificate per iOS 10.
- Verrà configurati nell'ambiente di disegno per spazio dei colori sRGB durante l'esecuzione personalizzata `UIView` disegno su un iPad Pro.
- Se l'app esegue il rendering personalizzato della `UIImages`, usare le nuove [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) classe per specificare l'utilizzo dei formati estesi-range o standard-range.
- Quando si usa un'API di basso livello, ad esempio Core Graphics o bare Metal per fornire l'elaborazione di immagini, lo sviluppatore deve usare un intervallo esteso colore pixel e spazio di formato che supporta valori a virgola mobile a 16 bit. Se necessario, lo sviluppatore dovrà limitare manualmente i valori dei componenti di colore.
- Grafica di base, immagine di base e gli shader di Metal prestazioni offrono nuovi metodi per la conversione tra gli spazi due colore.

Per altre informazioni, vedere la [Introduzione a ampia gamma di colori](~/ios/platform/wide-color.md) Guida.

## <a name="widget-enhancements"></a>Miglioramenti di widget

Apple ha introdotto numerosi miglioramenti al sistema Widget per garantire che i widget aspetto eccezionali su qualsiasi dello sfondo che esiste nel nuovo iOS 10 schermata di blocco. Il [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) proprietà è deprecata ed è stata sostituita con la nuova [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) o [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect) delle proprietà. Inoltre, i widget includono ora un [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) proprietà che consente agli sviluppatori di descrivere la quantità di contenuto è disponibile e consente all'utente di espandere e comprimere il contenuto.

Per altre informazioni, vedere la [ricerca e schermata Home i miglioramenti di Widget](~/ios/platform/search/widgets.md) Guida.

## <a name="additional-framework-changes"></a>Modifiche di altri Framework

Oltre alle modifiche principali framework e componenti aggiuntivi elencati in precedenza, Apple molti altri framework minori modifiche apportate in iOS 10.

Per altre informazioni, vedere la [modifiche aggiuntive del Framework](~/ios/platform/introduction-to-ios10/additional-framework-changes.md) Guida.

## <a name="deprecated-apis"></a>API deprecate

Le API seguenti sono state deprecate in iOS 10:

- Il `CKDiscoverAllContactsOperation`, `CKDiscoveredUserInfo`, `CKDiscoverUserInfosOperation` e `CKFetchRecordChangesOperation` classi sono state deprecate in CloudKit per iOS 10. Usare la [CKDiscoverAllUserIdentitiesOperation](https://developer.xamarin.com/api/type/CloudKit.CKDiscoverUserIdentitiesOperation/), [CKUserIdentity](https://developer.xamarin.com/api/type/CloudKit.CKUserIdentity/) e [CKFetchRecordZoneChangesOperation](https://developer.xamarin.com/api/type/CloudKit.CKFetchRecordZoneChangesOperation/) classi (che supportano la condivisione di record) alternativa.
- Numerosi [CKSubscription](https://developer.apple.com/reference/cloudkit/cksubscription) API (ad esempio una sottoscrizione basata sull'area e basati su query) sono state deprecate. Usare la [CKRecordZoneSubscription](https://developer.xamarin.com/api/type/CloudKit.CKRecordZoneSubscription/) e [CKQuerySubscription](https://developer.xamarin.com/api/type/CloudKit.CKQuerySubscription/) API invece.
- [NSPersistentStoreCoordnator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/) i simboli relativi al contenuto diffuso sono stati deprecati.
- `ADBannerView`, `ADInterstitialAd` e i simboli in relativi il [UIViewController](xref:UIKit.UIViewController) classe sono state deprecate.
- [SKUniform](https://developer.apple.com/reference/spritekit/skuniform) simboli associati a valori a virgola mobile sono stati deprecati.
- Il `UILocalNotification`, `UIMutableUserNotificationAction`, `UIMutableUserNotificationCategory`, `UIUserNotificationAction`, `UIUserNotificationCategory` e `UIUserNotificationSettings` classi di UIKit sono state deprecate. Usare la [notifiche utente](#user-notifications) framework invece.
- Il `HandleActionForLocalNotification`, `HandleActionForRemoteNotification`, `DidReceiveLocalNotification` e `DidReceiveRemoteNotification` WatchKit metodi deprecati. Usare la `HandleActionForNotification` e `DidReceiveNotification` metodi invece.
- Il `DidReceiveLocalNotification` e `DidReceiveRemoteNotification` metodi delle [WKExtensionDelegate](https://developer.apple.com/reference/watchkit/wkextensiondelegate) sono state deprecate. Creare un'istanza di [UNUserNotificationCenterDelegate](https://developer.apple.com/reference/usernotifications/unusernotificationcenterdelegate) che implementa i metodi appropriati e assegnarlo al `Delegate` proprietà della [UNUserNotificationCenter](https://developer.apple.com/reference/usernotifications/unusernotificationcenter) oggetto.
- Il **Game Center App** è deprecato ed è stato rimosso da iOS. Se l'app Usa GameKit, si _necessario_ presentare la propria interfaccia per visualizzare le funzionalità GameKit come tabelloni punteggi e così via.

Vedere di Apple [iOS 9.3 alle differenze di API iOS 10.0](https://developer.apple.com/library/prerelease/content/releasenotes/General/iOS10APIDiffs/index.html) documentazione per un elenco completo di deprecati.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Novità in iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
