---
title: Modifiche aggiuntive iOS 9 Framework
description: Questo documento vengono descritte le modifiche di altri framework introdotte in iOS 9. Viene descritto AVFoundation AVKit e CloudKit.
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: f7106c9b23fa71b32043ef8190691d798c36370d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672573"
---
# <a name="additional-ios-9-frameworks-changes"></a>Modifiche aggiuntive iOS 9 Framework

_Questo articolo illustra le modifiche aggiuntive, secondarie o miglioramenti alle infrastrutture esistenti per iOS 9._

[![](additional-framework-changes-images/ios9-sml.png "iOS 9 Logo")](additional-framework-changes-images/ios9.png#lightbox)

Oltre alle modifiche principali ai dispositivi iOS, Apple ha apportato modifiche e miglioramenti per diversi framework esistente in iOS 9.

## <a name="avfoundation-framework-additions"></a>Framework AVFoundation aggiunte

In framework AVFoundation, il [AVSpeechSynthesisVoice](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechSynthesisVoice/) classe ora consente di specificare una voce dall'identificatore oltre alla lingua.

Ad esempio, il codice seguente ottiene un elenco di tutte le voci disponibili:

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

È quindi possibile usare una delle voci nell'elenco impostandola come le `Voice` proprietà di un'istanza del [AVSpeachUtterance](https://developer.xamarin.com/api/type/AVFoundation.AVSpeechUtterance/) classe.

Il [AVQueuePlayer](https://developer.xamarin.com/api/type/AVFoundation.AVQueuePlayer/) classe ora supporta una combinazione di elemento multimediale internet basate su file e streaming nella coda. Le versioni precedenti è stato possibile solo i supporti coda dello stesso tipo.

Per altre informazioni, vedere di Apple [AVSpeechSynthesisVoice riferimento](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice).

## <a name="avkit-framework-additions"></a>Framework AVKit aggiunte

Per usare la nuova funzionalità Picture in Picture (PIP), incluso il nuovo framework AVKit `AVPictureInPictureController` e [AVPlayerViewController](https://developer.xamarin.com/api/type/AVKit.AVPlayerViewController/) classi:

- **AVPictureInPictureController** -questa classe consente a un'app per iOS 9 di rispondere all'utente l'avvio della riproduzione di un video in una finestra PIP ridimensionabile, a virgola mobile in un iPad.
- **AVPlayerViewController** -consente di gestire un `AVPlayer` controller usato per presentare un video in una finestra PIP ridimensionabile, a virgola mobile in un iPad.

Per altre informazioni, vedere la [Multitasking per iPad](~/ios/platform/introduction-to-ios9/index.md#multitasking) documentazione e Apple [riferimento AVPictureInPictureController](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController) e [AVPlayerViewController riferimento](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController).

## <a name="introducing-cloudkit-web-services"></a>Introduzione a CloudKit Web Services

Il framework CloudKit semplifica lo sviluppo di applicazioni iCloud tale accesso. Sono inclusi il recupero dei dati dell'applicazione e i diritti di asset, nonché la possibilità di archiviare in modo sicuro le informazioni sull'applicazione. Questo kit fornisce agli utenti un livello di anonimato consentendo l'accesso alle applicazioni con relativi ID iCloud senza condivisione delle informazioni personali.

Il nuovo _CloudKit Web Services_ framework fornisce una libreria JavaScript (JS CloudKit) che può essere incorporata nel sito Web per fornire l'accesso alla stessa CloudKit basati su dati e al contenuto dell'App xamarin. IOS.

> [!IMPORTANT]
> Prima di poter accedere, presentare o aggiornare il contenuto da un database CloudKit utilizzando CloudKit JS, è necessario avere precedentemente definito lo schema del database.




Per altre informazioni, vedere i documenti seguenti:

- [Introduzione a CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) -la nostra Introduzione all'uso di CloudKit in un'app xamarin. IOS.
- [Avvio rapido CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987) -introduzione di Apple a CloudKit.
- [Riferimento JS CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359) -documentazione di Apple CloudKit JS.
- [Riferimento per servizi Web CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240) -riferimento di Apple che descrive l'interfaccia HTTP a CloudKit.
- [Catalogo CloudKit: Un'introduzione a CloudKit (Cocoa e JavaScript)](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599) -app di esempio di Apple con CloudKit e CloudKit JS.

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

## <a name="foundation-framework-additions"></a>Aggiunte di Framework Foundation

Apple includeva le seguenti modifiche per il framework Foundation in iOS 9:

### <a name="changes-to-nsbundle"></a>Modifiche apportate a NSBundle

Sono state apportate le modifiche seguenti per il [NSBundle](xref:Foundation.NSBundle) classe per iOS 9:

* `GetPreservationPriorityForTag (NSString tag)` -Ottiene la priorità di conservazione corrente per le risorse con il tag specificato. I valori validi sono compresi nell'intervallo `0.0` a `1.0`, verranno eliminate prima di tutto le risorse con la priorità più bassa.
* `SetPreservationPriorityForTag (double priority, NSSet tags)` : Imposta la priorità di conservazione corrente per le risorse con i tag specificati. I valori validi sono compresi nell'intervallo `0.0` a `1.0`, verranno eliminate prima di tutto le risorse con la priorità più bassa.

Per altre informazioni, vedere di Apple [NSBundle riferimento](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle).

### <a name="changes-to-nsprocessinfo"></a>Modifiche apportate a NSProcessInfo

Ogni processo in esecuzione in un dispositivo iOS ha un'unica _processo dell'agente di informazioni_ (PIA). Usare la [NSProcessInfo](xref:Foundation.NSProcessInfo) classe per fornire informazioni sulla potenza di assembly di interoperabilità primario e il controllo corrente e la gestione termica per un determinato processo.

Per controllare la chiusura automatica di un processo, ad esempio, è possibile usare il codice seguente:

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

Per altre informazioni, vedere di Apple [NSProcessInfo riferimento](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo).

### <a name="reacting-to-low-power-mode"></a>Reazione a modalità basso consumo di Azure

Usare la `LowPowerModeEnabled` proprietà del [NSProcessInfo](xref:Foundation.NSProcessInfo) classe per determinare se la modalità basso consumo è stata abilitata nel dispositivo iOS che viene eseguita l'app. Ad esempio:

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>Modifiche del Framework HealthKit

Apple includeva le seguenti modifiche per il [HealthKit](https://developer.xamarin.com/api/namespace/HealthKit/) framework in iOS 9:

- Supporto per l'eliminazione in blocco e il rilevamento dell'eliminazione di voci nel database di HealthKit. Vedere di Apple [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject), [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery) e [riferimento alla classe HKHealthStore](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708) per altre informazioni.
- Caratteristiche e le nuove categorie di rilevamento sono stati aggiunti per il `HKQuantityTypeIdentifier` classe (, ad esempio `UVExposure`) e di ottenere il `HKCategoryTypeIdentifier` classe (, ad esempio `OvulationTestResult`). Vedere di Apple [HealthKit costanti riferimento](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HealthKit_Constants/index.html#//apple_ref/doc/uid/TP40014710) per altre informazioni.

Vedere la [Introduzione a HealthKit](~/ios/platform/healthkit.md) per altre informazioni sull'uso di HealthKit in xamarin. IOS.

## <a name="local-authentication-framework-changes"></a>Modifiche di Framework di autenticazione locale

Apple includeva le seguenti modifiche per il [l'autenticazione locale](https://developer.xamarin.com/api/namespace/LocalAuthentication/) framework in iOS 9:

- Usando il `EvaluateAccessControl` e `EvaluatePolicy` metodi delle [LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/) (classe), è ora possibile tentativi di riutilizzo Touch ID corrispondenze dalla precedente lo sblocco ha esito positivo.
- La possibilità di ottenere un elenco delle dita attualmente registrate.
- Supporto per la registrazione quando un dito viene aggiunto o rimosso dall'autenticazione.
- La possibilità di usare _contesto di autenticazione_ nelle chiamate di Keychain e supporto per la valutazione di controllo di accesso Keychain elenchi.
- La possibilità di annullare un prompt utente dal codice.

Vedere la [Introduzione a Touch ID](~/ios/platform/touchid.md) per altre informazioni sull'uso di Touch ID in xamarin. IOS.

### <a name="lacontext-changes"></a>Modifiche LAContext

Sono state apportate le modifiche seguenti per il [LAContext](https://developer.xamarin.com/api/type/LocalAuthentication.LAContext/) classe per iOS 9:

- **TouchIdAuthenticationMaximumAllowableReuseDuration** -restituisce la quantità massima di tempo che può essere riutilizzata un'autenticazione ID tocco.
- **EvaluatedPolicyDomainState** : Ottiene o imposta lo stato dei criteri di valutazione.
- **MaxBiometryFailures** -è stato rimosso in iOS 9.
- **TouchIdAuthenticationAllowableReuseDuration** Ottiene o imposta la quantità di tempo che può essere riutilizzata un'autenticazione ID tocco.
- **EvaluateAccessControl** : in modo asincrono valuta un criterio di autenticazione.
- **Invalidare** -invalida un'autenticazione ID tocco specificato.
- **IsCredentialSet** -restituisce `true` se le credenziali attualmente impostate.
- **SetCredentialType** imposta il tipo di credenziali specificato.

Vedere di Apple [LAContext riferimento](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:) per altri dettagli.

## <a name="mapkit-framework-changes"></a>Modifiche del Framework MapKit

Apple includeva le seguenti modifiche per il [MapKit](https://developer.xamarin.com/api/namespace/MapKit/) framework in iOS 9:

- MapKit fornisce ora il supporto per l'avvio dell'app mappa direttamente in direzioni di transito e per l'esecuzione di query transito tempo stimato di arrivo (ETA) usando il [MKLaunchOptions](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/) e [MKDirections](https://developer.xamarin.com/api/type/MapKit.MKLaunchOptions/) classi.
- Risultati della ricerca restituiti da MapKit e il [CLGeocoder](https://developer.xamarin.com/api/type/CoreLocation.CLGeocoder/) classe può anche fornire fuso orario del risultato.
- È ora possibile personalizzare completamente le annotazioni di mappa presentato dalla tua app iOS usando il `DetailCalloutAccessoryView` proprietà del [MKAnnotationView](https://developer.xamarin.com/api/type/MapKit.MKAnnotationView/) classe.

Vedere la [mappe di iOS](~/ios/user-interface/controls/ios-maps/index.md) e [procedura dettagliata: esplorare le annotazioni e sovrimpressioni in MapKit](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md) per altre informazioni sull'uso di mappe e le annotazioni in xamarin. IOS e Apple [CLGeocoder riferimento](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder) per altre informazioni.

## <a name="passkit-framework-additions"></a>Framework di PassKit aggiunte

Apple includeva le seguenti modifiche per il [PassKit](https://developer.xamarin.com/api/namespace/PassKit/) framework in iOS 9:

- Apple Pay ora supporta sia store debito di carte di credito e individua le schede. Vedere le **le reti a pagamento** sezione di Apple [riferimento alla classe PKPaymentRequest](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832) per altre informazioni.
- Da direttamente all'interno di un'app xamarin. IOS, è ora possibile aggiungere le reti a pagamento e autorità emittenti di carte per Apple Pay. Vedere di Apple [riferimento alla classe PKAddPaymentPassViewController](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116) per altri dettagli.

Vedere la [Introduzione a PassKit](~/ios/platform/passkit.md) per altre informazioni sull'uso di PassKit in xamarin. IOS.

## <a name="safari-services-framework-additions"></a>Aggiunte di Framework di servizi di Safari

Apple includeva le seguenti modifiche per il [Safari servizi](https://developer.xamarin.com/api/namespace/SafariServices/) framework in iOS 9:

- È ora possibile usare le nuove [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) classe per visualizzare il contenuto web in un'app xamarin. IOS. Offre la possibilità di condividere i cookie e dati del sito Web con l'app Safari e include diverse funzionalità di Safari (ad esempio lettore e il riempimento automatico). [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) funzionalità di un **pulsante** che restituirà gli utenti all'App quando hanno terminato la visualizzazione del contenuto web.

Poiché il [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) classe è concepita per la visualizzazione di una singola pagina del contenuto web, è consigliabile utilizzarlo per sostituire ciascun [WKWebKit](xref:WebKit.WKWebView) o [UIWebView](xref:UIKit.UIWebView)i controlli all'interno delle App xamarin. IOS esistente.

### <a name="displaying-a-website"></a>Visualizzazione di un sito Web

Il codice riportato di seguito è riportato un esempio della chiamata al metodo un [SFSafariViewController](https://developer.xamarin.com/api/type/SafariServices.SFSafariViewController/) all'interno di un altro controller di visualizzazione:

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>Modifiche del Framework UIKit

Apple ha incluso molti miglioramenti a diversi elementi del [UIKit](xref:UIKit) framework per iOS 9. Le sezioni seguenti in modo dettagliato le modifiche.

### <a name="3d-touch-events"></a>Eventi di tocco 3D

Novità di iOS 9 e iPhone 6s e iPhone 6s Plus, 3D Touch aggiunge i movimenti di pressione sensibili per le app iOS. Di conseguenza, se l'esecuzione dell'app in iOS 9 (o versione successiva) e il dispositivo iOS è in grado di supporto 3D Touch, le modifiche in un utilizzo elevato causa il `TouchesMoved` generazione dell'evento.

Grazie a questa modifica nel comportamento, le app iOS devono essere preparate per il `TouchesMoved` evento da richiamare più spesso, anche se X / Y coordinate non sono stati modificati.

Per altre informazioni, vedere la [Introduzione a 3D Touch](~/ios/platform/3d-touch.md) Guida.

### <a name="document-open-in-place-functionality"></a>Funzionalità Open-in-Place di documenti

Utilizzando il `FinishedLaunching (application, launchOptions)` oppure `WillFinishLaunching (Application, launchOptions)` metodi del [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) (classe), è ora possibile aprire un documento e modificarlo posto (in contrapposizione lavorando a una copia).

Per supportare la nuova funzionalità di open-in-place, aggiungere il `LSSupportsOpeningDocumentsInPlace` chiave all'app di xamarin. IOS **Info. plist** file con un valore di `YES`.

Vedere di Apple [UIApplicationDelegate riferimento](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) per altri dettagli.

### <a name="enhanced-touch-events"></a>Eventi di tocco migliorate

Apple ha fornito i vari miglioramenti agli eventi di tocco in iOS 9. Queste includono la possibilità di utilizzare la stima di tocco e per ottenere l'accesso agli elementi intermedi tra gli aggiornamenti della visualizzazione.

Vedere di Apple [Guida alla gestione degli eventi per iOS](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html) per altri dettagli.

### <a name="fetching-tailored-content"></a>Recupero contenuto personalizzato

Il nuovo `NSDataAsset` classe consente a un'app xamarin. IOS recuperare il contenuto su misura per la memoria e le funzionalità grafiche di un dispositivo iOS attualmente in esecuzione in.

### <a name="new-layout-anchors"></a>Nuovi Layout Anchor

Il nuovo `NSLayoutAnchor` e `NSLayoutDimension` layout ancoraggio classi funzionano con le nuove proprietà di ancoraggio delle [UIView](xref:UIKit.UIView) classe (ad esempio `LeadingAnchor` e `WidthAnchor`) per rendere più semplice i layout in iOS 9.

Vedere la [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) per altre informazioni sull'uso di layout automatico e le classi di dimensioni in un'app xamarin. IOS e Apple [NSLayoutAnchor riferimento](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor), [ Riferimento NSLayoutDimension](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension) e [UIView riferimento](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) per altre informazioni.

### <a name="new-readable-content-margins"></a>Nuovo dei margini del contenuto leggibili

Il nuovo `UILayoutGuide` classe può essere utilizzata per fornire dei margini del contenuto leggibili e definire le aree di disegno per il contenuto all'interno di una vista. Vedere di Apple [UILayoutGuide riferimento](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide) per altre informazioni.

### <a name="text-input-in-notifications-modifications"></a>Input di testo nelle modifiche delle notifiche

Il [UIUserNotificationAction](xref:UIKit.UIUserNotificationAction) dispone di una nuova classe `Behavior` proprietà che può essere usata per supportare le notifiche di input di testo.

### <a name="uiapplicationdelegate-changes"></a>Modifiche UIApplicationDelegate

Anche se non ufficialmente deprecato da Apple, è consigliabile sostituendo tutte le chiamate al `FinishedLaunching (UIApplication application)` metodo per il [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) classe con il `FinishedLaunching (UIApplication application, NSDictionary launchOptions)` o `WillFinishLaunching (UIApplication application, NSDictionary launchOptions)` metodi.

Vedere di Apple [UIApplicationDelegate riferimento](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) per altri dettagli.

### <a name="uikit-dynamics-changes"></a>Modifiche di Dynamics UIKit

Apple includeva le seguenti modifiche a UIKit Dynamics in iOS 9:

- Dynamics fornisce ora supporto per i limiti di collisione non rettangolari.
- Il nuovo, personalizzabile `UIFieldBehavior` classe viene utilizzata per supportare vari tipi di campo.
- Tipi di attacco aggiuntivi sono stati aggiunti per il `UIAttachmentBehavior` classe.

Vedere di Apple [UIAttachment riferimento](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior) per altri dettagli.

### <a name="uipickerview-and-uidatepicker-changes"></a>UIPickerView e modifiche UIDatePicker

Prima di iOS 9, il [UIPickerView](xref:UIKit.UIPickerView) e il [UIDatePicker](xref:UIKit.UIDatePicker) controlli sono stati non ridimensionabile e viene automaticamente ridimensionato per riempire la larghezza del relativo contenitore (in genere la larghezza di un dispositivo iOS l'app è stata in esecuzione su).

In iOS 9, il ridimensionamento automatico non si verifica più e i controlli verranno sottoposti a rendering in una larghezza di 320 punto in tutti i dispositivi iOS, indipendentemente dalle dimensioni dello schermo e l'orientamento.

Per risolvere questo problema, usare Layout automatico e le classi di dimensioni per aggiungere la larghezza del controllo ai bordi del contenitore padre (visualizzazione) e specificare l'altezza richiesta. Vedere la [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) per altre informazioni sull'uso di Layout automatico e le classi di dimensioni in un'app xamarin. IOS.

### <a name="new-uitextinputassistantitem-class"></a>Nuova classe UITextInputAssistantItem

Usare le nuove `UITextInputAssistantItem` classe a gruppi di pulsanti di layout a barre in un _barra degli strumenti_. La barra degli strumenti è una nuova area disponibile nella tastiera per fornire tipizzazione tasti di scelta rapida.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Introduzione a iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [per gli sviluppatori iOS 9](https://developer.apple.com/ios/pre-release/)
- [What ' s New in iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
