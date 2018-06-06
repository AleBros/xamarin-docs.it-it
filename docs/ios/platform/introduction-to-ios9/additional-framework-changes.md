---
title: Modifiche di Framework aggiuntive iOS 9
description: Questo documento descrive le modifiche al framework aggiuntive introdotte in iOS 9. Viene descritto AVFoundation AVKit e CloudKit.
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 15c9364cf3bdcb8c797882cc9ac76219959de439
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787708"
---
# <a name="additional-ios-9-frameworks-changes"></a>Modifiche di Framework aggiuntive iOS 9

_Questo articolo vengono illustrate le modifiche aggiuntive, secondarie o miglioramenti alle infrastrutture esistenti per iOS 9._

[![](additional-framework-changes-images/ios9-sml.png "iOS 9 Logo")](additional-framework-changes-images/ios9.png#lightbox)

Oltre alle modifiche essenziali per iOS, Apple ha apportato modifiche e miglioramenti per diversi Framework esistenti iOS 9.

## <a name="avfoundation-framework-additions"></a>Aggiunte AVFoundation Framework

In framework AVFoundation, il [AVSpeechSynthesisVoice](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechSynthesisVoice/) classe ora consente di specificare una voce dall'identificatore oltre alla lingua.

Ad esempio, il codice seguente ottiene un elenco di tutte le voci disponibili:

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

È quindi possibile utilizzare una delle voci nell'elenco impostando come il `Voice` proprietà di un'istanza di [AVSpeachUtterance](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechUtterance/) classe.

Il [AVQueuePlayer](https://developer.xamarin.com/api/type/AVFoundation.AVQueuePlayer/) classe ora supporta una combinazione di supporto di streaming e basati su file internet nella coda. Le versioni precedenti è possibile solo i supporti coda dello stesso tipo.

Per ulteriori informazioni, vedere Apple [AVSpeechSynthesisVoice riferimento](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice).

## <a name="avkit-framework-additions"></a>Framework AVKit aggiunte

Per utilizzare la nuova funzionalità di immagine in immagine (PIP), incluso il nuovo framework AVKit `AVPictureInPictureController` e [AVPlayerViewController](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) classi:

- **AVPictureInPictureController** -questa classe consente a un'app di iOS 9 rispondere all'utente che esegue la riproduzione di un video in una finestra PIP a virgola mobile e ridimensionabile in un iPad.
- **AVPlayerViewController** -gestisce un `AVPlayer` controller utilizzato per presentare un video in una finestra PIP a virgola mobile e ridimensionabile in un iPad.

Per ulteriori informazioni, vedere il nostro [Multitasking per iPad](~/ios/platform/introduction-to-ios9/index.md#multitasking) Apple e la documentazione [AVPictureInPictureController riferimento](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController) e [riferimento AVPlayerViewController](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController).

## <a name="introducing-cloudkit-web-services"></a>Introduzione a servizi Web CloudKit

Il framework CloudKit semplifica lo sviluppo di applicazioni iCloud tale accesso. Sono inclusi il recupero di dati dell'applicazione e i diritti di asset, nonché la possibilità di archiviare in modo sicuro le informazioni dell'applicazione. Questo kit offre agli utenti un livello di anonimato consentendo l'accesso alle applicazioni con i relativi ID iCloud senza condividere le informazioni personali.

Il nuovo _servizi Web CloudKit_ framework fornisce una libreria JavaScript (JS CloudKit) che può essere incorporata nel sito Web per fornire l'accesso allo stesso CloudKit basato su dati e il contenuto dell'App xamarin. IOS.

> [!IMPORTANT]
> Prima di poter accedere, presentare o aggiornare il contenuto da un database CloudKit utilizzando CloudKit JS, è necessario avere precedentemente definito lo schema del database.




Per ulteriori informazioni, vedere i seguenti documenti:

- [Introduzione a CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) -Introduzione alla utilizzando CloudKit in un'app xamarin. IOS.
- [Avvio rapido CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987) -introduzione di Apple per CloudKit.
- [Riferimento JS CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359) -documentazione JS CloudKit di Apple.
- [Riferimento per servizi Web CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240) -riferimento di Apple che descrive l'interfaccia HTTP CloudKit.
- [Catalogo CloudKit: An Introduction to CloudKit (Cocoa e JavaScript)](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599) -app di esempio di Apple usando CloudKit e CloudKit JS.

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

## <a name="foundation-framework-additions"></a>Aggiunte di Framework Foundation

In iOS 9, Apple incluse le seguenti modifiche per il framework di base:

### <a name="changes-to-nsbundle"></a>Modifiche apportate a NSBundle

Le modifiche seguenti apportate al [NSBundle](https://developer.xamarin.com/api/type/Foundation.NSBundle/) classe per iOS 9:

* `GetPreservationPriorityForTag (NSString tag)` : Ottiene la priorità di conservazione correnti per le risorse con il tag specificato. I valori validi sono compresi nell'intervallo `0.0` a `1.0`, devono essere eliminate prima delle risorse con la priorità più bassa.
* `SetPreservationPriorityForTag (double priority, NSSet tags)` -Imposta la priorità di conservazione correnti per le risorse con i tag specificati. I valori validi sono compresi nell'intervallo `0.0` a `1.0`, devono essere eliminate prima delle risorse con la priorità più bassa.

Per ulteriori informazioni, vedere Apple [NSBundle riferimento](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle).

### <a name="changes-to-nsprocessinfo"></a>Modifiche apportate a NSProcessInfo

Ogni processo in esecuzione su un dispositivo iOS ha un singolo, _processo informazioni agente_ (PIA). Utilizzare il [NSProcessInfo](https://developer.xamarin.com/api/type/Foundation.NSProcessInfo/) classe per fornire informazioni sulla gestione termica per un determinato processo e potenza di assembly di interoperabilità primario e il controllo corrente.

Per controllare la chiusura automatica di un processo, ad esempio, è possibile utilizzare il codice seguente:

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

Per ulteriori informazioni, vedere Apple [NSProcessInfo riferimento](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo).

### <a name="reacting-to-low-power-mode"></a>Reazione modalità basso consumo

Utilizzare il `LowPowerModeEnabled` proprietà del [NSProcessInfo](https://developer.xamarin.com/api/type/Foundation.NSProcessInfo/) classe per determinare se è stata attivata la modalità basso consumo nel dispositivo iOS che è in esecuzione l'app. Ad esempio:

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>Modifiche di Framework HealthKit

Apple includeva le seguenti modifiche per il [HealthKit](https://developer.xamarin.com/api/namespace/HealthKit/) framework in iOS 9:

- Supporto per l'eliminazione di massa e il rilevamento di eliminazione di voci nel database HealthKit. Vedere Apple [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject), [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery) e [riferimento alla classe HKHealthStore](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708) per ulteriori informazioni.
- Nuove categorie di rilevamento e le caratteristiche sono stati aggiunti il `HKQuantityTypeIdentifier` classe (ad esempio `UVExposure`) e al `HKCategoryTypeIdentifier` classe (ad esempio `OvulationTestResult`). Vedere Apple [HealthKit costanti riferimento](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HealthKit_Constants/index.html#//apple_ref/doc/uid/TP40014710) per ulteriori informazioni.

Consultare il nostro [Introduzione a HealthKit](~/ios/platform/healthkit.md) documentazione per ulteriori informazioni sull'utilizzo di HealthKit in xamarin. IOS.

## <a name="local-authentication-framework-changes"></a>Modifiche al Framework di autenticazione locale

Apple includeva le seguenti modifiche per il [autenticazione locale](https://developer.xamarin.com/api/namespace/LocalAuthentication/) framework in iOS 9:

- Utilizzando il `EvaluateAccessControl` e `EvaluatePolicy` metodi il [LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/) (classe), è ora possibile tenta di riutilizzare l'ID tocco corrispondenze dalla precedente lo sblocco ha esito positivo.
- La possibilità di ottenere un elenco delle dita attualmente registrate.
- Supporto per la registrazione quando un dito viene aggiunto o rimosso dal tipo di autenticazione.
- La possibilità di utilizzare _contesto di autenticazione_ nelle chiamate di portachiavi e supporto per la valutazione di controllo di accesso portachiavi elenchi.
- La possibilità di annullare un prompt utente dal codice.

Consultare il nostro [introduzione Touch ID](~/ios/platform/touchid.md) documentazione per ulteriori informazioni sull'uso di Touch ID in xamarin. IOS.

### <a name="lacontext-changes"></a>Modifiche LAContext

Le modifiche seguenti apportate al [LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/) classe per iOS 9:

- **TouchIdAuthenticationMaximumAllowableReuseDuration** -restituisce la quantità massima di tempo che può essere riutilizzata un'autenticazione ID tocco.
- **EvaluatedPolicyDomainState** : Ottiene o imposta lo stato dei criteri di valutazione.
- **MaxBiometryFailures** -è stato ridimensionato nella iOS 9.
- **TouchIdAuthenticationAllowableReuseDuration** Ottiene o imposta la quantità di tempo che può essere riutilizzata un'autenticazione ID tocco.
- **EvaluateAccessControl** : in modo asincrono valuta un criterio di autenticazione.
- **Invalidare** -invalida un'autenticazione ID tocco specificato.
- **IsCredentialSet** -restituisce `true` se le credenziali attualmente impostate.
- **SetCredentialType** imposta il tipo di credenziali specificato.

Vedere Apple [LAContext riferimento](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:) per altri dettagli.

## <a name="mapkit-framework-changes"></a>Modifiche di Framework MapKit

Apple includeva le seguenti modifiche per il [MapKit](https://developer.xamarin.com/api/namespace/MapKit/) framework in iOS 9:

- MapKit fornisce ora il supporto per l'avvio dell'app mappa direttamente in direzioni di transito e per le query di transito tempo stimato di arrivo (greco) utilizzando il [MKLaunchOptions](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/) e [MKDirections](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/) classi.
- I risultati di ricerca restituiti da MapKit e [CLGeocoder](https://developer.xamarin.com/api/type/CoreLocation.CLGeocoder/) classe può anche fornire fuso orario del risultato.
- È ora possibile personalizzare completamente annotazioni mappa presentato dall'app iOS utilizzando il `DetailCalloutAccessoryView` proprietà del [MKAnnotationView](https://developer.xamarin.com/api/type/MapKit.MKAnnotationView/) classe.

Consultare il nostro [iOS mappe](~/ios/user-interface/controls/ios-maps/index.md) e [procedura dettagliata: esplorare le annotazioni e sovrapposizioni in MapKit](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md) documentazione per ulteriori informazioni sull'utilizzo di mappe e le annotazioni in xamarin. IOS e Apple [CLGeocoder riferimento](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder) per ulteriori informazioni.

## <a name="passkit-framework-additions"></a>Framework PassKit aggiunte

Apple includeva le seguenti modifiche per il [PassKit](https://developer.xamarin.com/api/namespace/PassKit/) framework in iOS 9:

- Apple Pay supporta ora debito archivio e carte di credito insieme Discover card. Vedere il **reti a pagamento** sezione di Apple [riferimento alla classe PKPaymentRequest](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832) per ulteriori informazioni.
- Da direttamente all'interno di un'app xamarin. IOS, è possibile ora aggiungere reti a pagamento e autorità emittenti di smart card a pagamento di Apple. Vedere Apple [riferimento alla classe PKAddPaymentPassViewController](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116) per altri dettagli.

Consultare il nostro [Introduzione a PassKit](~/ios/platform/passkit.md) documentazione per ulteriori informazioni sull'utilizzo di PassKit in xamarin. IOS.

## <a name="safari-services-framework-additions"></a>Safari aggiunte Framework dei servizi

Apple includeva le seguenti modifiche per il [servizi Safari](https://developer.xamarin.com/api/namespace/SafariServices/) framework in iOS 9:

- È ora possibile usare il nuovo [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) classe per visualizzare il contenuto web in un'app xamarin. IOS. Fornisce la possibilità di condividere i dati del sito Web e i cookie con l'app Safari e include molte delle funzionalità di Safari (ad esempio lettore e riempimento automatico). [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) funzionalità un **eseguita** pulsante che verrà restituiti gli utenti all'App, al termine della visualizzazione del contenuto web.

Poiché il [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) classe personalizzata per la visualizzazione di una singola pagina del contenuto web, è consigliabile usarlo per sostituire qualsiasi [WKWebKit](https://developer.xamarin.com/api/type/WebKit.WKWebView/) o [UIWebView](https://developer.xamarin.com/api/type/UIKit.UIWebView/)controlli all'interno di App xamarin esistente.

### <a name="displaying-a-website"></a>Visualizzazione di un sito Web

Il codice riportato di seguito è riportato un esempio della chiamata al metodo un [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) all'interno di un altro controller di visualizzazione:

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>Modifiche di Framework UIKit

Apple include numerosi miglioramenti a diversi elementi di [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) framework per iOS 9. Le seguenti sezioni illustra in dettaglio le modifiche.

### <a name="3d-touch-events"></a>Eventi tocco 3D

Nuovi utenti di iOS 9 e iPhone 6s e iPhone 6s Plus, 3D Touch aggiunge i movimenti di pressione sensibili per le app iOS. Di conseguenza, se l'esecuzione dell'app in iOS 9 (o versione successiva) e il dispositivo iOS è in grado di supporto 3D Touch, le modifiche in congestione causeranno la `TouchesMoved` dell'evento. 

A causa di questa modifica nel comportamento, le app iOS devono essere preparate per il `TouchesMoved` evento da richiamare più spesso, anche se X / Y coordinate non sono stati modificati. 

Per ulteriori informazioni, vedere il nostro [introduzione 3D Touch](~/ios/platform/3d-touch.md) Guida.

### <a name="document-open-in-place-functionality"></a>Funzionalità Open-in-Place di documenti

Utilizzando il `FinishedLaunching (application, launchOptions)` o `WillFinishLaunching (Application, launchOptions)` metodi del [UIApplicationDelegate](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/) (classe), è possibile aprire un documento e modificarlo sul posto (in contrapposizione lavorando su una copia).

Per supportare le nuove funzionalità di open-in-place, aggiungere il `LSSupportsOpeningDocumentsInPlace` per app xamarin **Info. plist** file con un valore di `YES`.

Vedere Apple [UIApplicationDelegate riferimento](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) per altri dettagli.

### <a name="enhanced-touch-events"></a>Eventi tocco avanzata

Apple fornisce vari miglioramenti agli eventi tocco in iOS 9. Queste includono la possibilità di utilizzare la stima di tocco e ottenere l'accesso agli elementi intermedi tra gli aggiornamenti di visualizzazione.

Vedere Apple [Guida di gestione degli eventi per iOS](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html) per altri dettagli.

### <a name="fetching-tailored-content"></a>Recupero contenuto personalizzato

Il nuovo `NSDataAsset` classe consente di recuperare il contenuto personalizzato la memoria e le funzionalità grafiche del dispositivo iOS che è attualmente in esecuzione in un'app di xamarin. IOS.

### <a name="new-layout-anchors"></a>Nuovo Layout Anchor

Il nuovo `NSLayoutAnchor` e `NSLayoutDimension` classi di ancoraggio layout funzionano con le nuove proprietà di ancoraggio del [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/) classe (ad esempio `LeadingAnchor` e `WidthAnchor`) per rendere più semplice layout in iOS 9.

Consultare il nostro [Introduzione agli storyboard unificata](~/ios/user-interface/storyboards/unified-storyboards.md) documentazione per ulteriori informazioni sull'utilizzo di classi di dimensioni e il layout automatico in app xamarin. IOS e Apple [NSLayoutAnchor riferimento](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor), [ Riferimento NSLayoutDimension](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension) e [UIView riferimento](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) per ulteriori informazioni.

### <a name="new-readable-content-margins"></a>Margini di nuovo contenuti leggibili

Il nuovo `UILayoutGuide` classe può essere utilizzata per fornire i margini contenuti leggibili e definire le aree di disegno per il contenuto all'interno di una vista. Vedere Apple [UILayoutGuide riferimento](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide) per ulteriori informazioni.

### <a name="text-input-in-notifications-modifications"></a>Input di testo nelle notifiche modifiche

Il [UIUserNotificationAction](https://developer.xamarin.com/api/type/UIKit.UIUserNotificationAction/) classe dispone di un nuovo `Behavior` proprietà che può essere utilizzata per supportare le notifiche di input di testo.

### <a name="uiapplicationdelegate-changes"></a>Modifiche UIApplicationDelegate

Mentre non è ufficialmente deprecato da Apple, suggeriscono sostituendo tutte le chiamate al `FinishedLaunching (UIApplication application)` metodo il [UIApplicationDelegate](https://developer.xamarin.com/api/type/UIKit.UIApplicationDelegate/) classe con il `FinishedLaunching (UIApplication application, NSDictionary launchOptions)` o `WillFinishLaunching (UIApplication application, NSDictionary launchOptions)` metodi.

Vedere Apple [UIApplicationDelegate riferimento](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) per altri dettagli.

### <a name="uikit-dynamics-changes"></a>Modifiche di Dynamics UIKit

In iOS 9, Apple incluse le seguenti modifiche dinamiche UIKit:

- Dynamics fornisce ora supporto per i limiti di collisioni non rettangolari.
- Il nuovo personalizzabile `UIFieldBehavior` classe viene utilizzata per supportare vari tipi di campo.
- Tipi di attacco aggiuntivi sono stati aggiunti i `UIAttachmentBehavior` classe.

Vedere Apple [UIAttachment riferimento](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior) per altri dettagli.

### <a name="uipickerview-and-uidatepicker-changes"></a>UIPickerView e UIDatePicker modifiche

Prima di iOS 9, il [UIPickerView](https://developer.xamarin.com/api/type/UIKit.UIPickerView/) e [UIDatePicker](https://developer.xamarin.com/api/type/UIKit.UIDatePicker/) controlli sono stati non ridimensionabile e verrà automaticamente ridimensionato per riempire la larghezza del relativo contenitore (in genere la larghezza del dispositivo iOS è stata l'app in esecuzione).

In iOS 9, il ridimensionamento automatico non si verifica più e i controlli verranno visualizzati una larghezza di 320 punto su tutti i dispositivi iOS, indipendentemente dalle dimensioni dello schermo e l'orientamento.

Per risolvere questo problema, utilizzare Layout automatico e le classi di dimensioni per aggiungere la larghezza del controllo ai bordi del contenitore padre (visualizzazione) e specificare l'altezza richiesta. Consultare il nostro [Introduzione agli storyboard unificata](~/ios/user-interface/storyboards/unified-storyboards.md) documentazione per ulteriori informazioni sull'utilizzo di un'app xamarin con Layout automatico e le classi di dimensioni.

### <a name="new-uitextinputassistantitem-class"></a>Nuova classe UITextInputAssistantItem

Usare il nuovo `UITextInputAssistantItem` classe a gruppi di pulsanti di layout barra in un _barra degli strumenti_. La barra degli strumenti è una nuova area disponibile nella tastiera per fornire tipizzazione tasti di scelta rapida.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Introduzione a iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [iOS 9 per gli sviluppatori](https://developer.apple.com/ios/pre-release/)
- [Novità in iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
