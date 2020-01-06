---
title: Modifiche aggiuntive ai Framework iOS 9
description: Questo documento descrive le modifiche aggiuntive del Framework introdotte in iOS 9. Vengono illustrati AVFoundation, AVKit e CloudKit.
ms.prod: xamarin
ms.assetid: CFDE1FC4-9327-402B-95A0-581D4AA0E9D5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: d9d47e750580bb9e4a0f4a2283cbd9e8c6a44c93
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489089"
---
# <a name="additional-ios-9-frameworks-changes"></a>Modifiche aggiuntive ai Framework iOS 9

_Questo articolo illustra ulteriori modifiche o miglioramenti secondari ai Framework esistenti per iOS 9._

[![](additional-framework-changes-images/ios9-sml.png "iOS 9 Logo")](additional-framework-changes-images/ios9.png#lightbox)

Oltre alle principali modifiche apportate a iOS, Apple ha apportato modifiche e miglioramenti a diversi Framework esistenti in iOS 9.

## <a name="avfoundation-framework-additions"></a>Aggiunte a AVFoundation Framework

In AVFoundation Framework la classe [AVSpeechSynthesisVoice](xref:AVFoundation.AVSpeechSynthesisVoice) consente ora di specificare una voce per identificatore oltre al linguaggio.

Il codice seguente, ad esempio, ottiene un elenco di tutte le voci disponibili:

```csharp
var voices = AVSpeechSynthesisVoice.GetSpeechVoices ();
```

È quindi possibile utilizzare una delle voci dell'elenco impostando il valore come `Voice` proprietà di un'istanza della classe [AVSpeachUtterance](xref:AVFoundation.AVSpeechUtterance) .

La classe [AVQueuePlayer](xref:AVFoundation.AVQueuePlayer) supporta ora una combinazione di flussi Internet e supporti basati su file nella coda. Le versioni precedenti potevano solo accodare supporti dello stesso tipo.

Per altre informazioni, vedere la Guida di [riferimento per AVSpeechSynthesisVoice](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVSpeechSynthesisVoice_Ref/index.html#//apple_ref/occ/cl/AVSpeechSynthesisVoice)di Apple.

## <a name="avkit-framework-additions"></a>Aggiunte a AVKit Framework

Per usare la nuova funzionalità Picture-in-Picture (PIP), il Framework AVKit include le nuove classi `AVPictureInPictureController` e [AVPlayerViewController](xref:AVKit.AVPlayerViewController) :

- **AVPictureInPictureController** : questa classe consente a un'app iOS 9 di rispondere all'utente che avvia la riproduzione di un video in una finestra PIP mobile e ridimensionabile su un iPad.
- **AVPlayerViewController** : gestisce un controller `AVPlayer` usato per presentare un video in una finestra PIP mobile e ridimensionabile in un iPad.

Per ulteriori informazioni, vedere la documentazione relativa [al multitasking per iPad](~/ios/platform/introduction-to-ios9/index.md#multitasking) e il riferimento [AVPictureInPictureController](https://developer.apple.com/library/prerelease/ios/documentation/AVKit/Reference/AVPictureInPictureController_Class/index.html#//apple_ref/occ/cl/AVPictureInPictureController) e [AVPlayerViewController](https://developer.apple.com/library/prerelease/ios/documentation/AVFoundation/Reference/AVPlayerViewController_Class/index.html#//apple_ref/occ/cl/AVPlayerViewController)di Apple.

## <a name="introducing-cloudkit-web-services"></a>Introduzione ai servizi Web di CloudKit

Il Framework CloudKit semplifica lo sviluppo di applicazioni che accedono a iCloud. Questo include il recupero dei dati delle applicazioni e dei diritti degli asset, nonché la possibilità di archiviare in modo sicuro le informazioni sull'applicazione. Questo kit offre agli utenti un livello di anonimità consentendo l'accesso alle applicazioni con gli ID iCloud senza condividere le informazioni personali.

Il nuovo Framework _dei servizi Web di CloudKit_ fornisce una libreria JavaScript (CloudKit js) che può essere incorporata nel sito Web per consentire l'accesso agli stessi dati e contenuti basati su CloudKit dell'app Novell. iOS.

> [!IMPORTANT]
> Prima di poter accedere, presentare o aggiornare il contenuto da un database CloudKit usando CloudKit JS, è necessario aver definito in precedenza lo schema del database.

Per ulteriori informazioni, consultare i documenti seguenti:

- [Introduzione a CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) : Introduzione all'uso di CloudKit in un'app Novell. iOS.
- [CloudKit avvio rapido](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987) -Introduzione a CloudKit di Apple.
- Informazioni di [riferimento su CLOUDKIT JS](https://developer.apple.com/library/prerelease/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html#//apple_ref/doc/uid/TP40015359) -documentazione di Apple CloudKit js.
- [Riferimento ai servizi Web di CloudKit](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240) -riferimento di Apple che descrive l'interfaccia http a CloudKit.
- [Catalogo di CloudKit: Introduzione a CloudKit (Cocoa e JavaScript)](https://developer.apple.com/library/prerelease/ios/samplecode/CloudAtlas/Introduction/Intro.html#//apple_ref/doc/uid/TP40014599) -app di esempio Apple con CloudKit e CloudKit js.

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

## <a name="foundation-framework-additions"></a>Aggiunte di Foundation Framework

Apple ha incluso le seguenti modifiche al Framework di base in iOS 9:

### <a name="changes-to-nsbundle"></a>Modifiche a NSBundle

Sono state apportate le modifiche seguenti alla classe [NSBundle](xref:Foundation.NSBundle) per iOS 9:

- `GetPreservationPriorityForTag (NSString tag)`: ottiene la priorità di conservazione corrente per le risorse con il tag specificato. I valori validi sono compresi nell'intervallo `0.0` `1.0`, le risorse con la priorità più bassa verranno eliminate per prime.
- `SetPreservationPriorityForTag (double priority, NSSet tags)`: imposta la priorità di conservazione corrente per le risorse con i tag specificati. I valori validi sono compresi nell'intervallo `0.0` `1.0`, le risorse con la priorità più bassa verranno eliminate per prime.

Per altre informazioni, vedere la Guida di [riferimento per NSBundle](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSBundle_Class/index.html#//apple_ref/occ/cl/NSBundle)di Apple.

### <a name="changes-to-nsprocessinfo"></a>Modifiche a NSProcessInfo

Ogni processo in esecuzione in un dispositivo iOS dispone di un singolo _agente di elaborazione delle informazioni di processo_ (PIA). Usare la classe [NSProcessInfo](xref:Foundation.NSProcessInfo) per fornire informazioni sull'assembly di interoperabilità primario e sulla gestione termica correnti per un determinato processo.

Ad esempio, per controllare la chiusura automatica di un processo, è possibile usare il codice seguente:

```csharp
// Disable automatic termination
var activity = NSProcessInfo.ProcessInfo.BeginActivity(NSActivityOptions.AutomaticTerminationDisabled, "Define reason for change here...");

// Perform the required task
...

// Return to normal operation
NSProcessInfo.ProcessInfo.EndActivity(activity);
```

Per altre informazioni, vedere la Guida di [riferimento per NSProcessInfo](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSProcessInfo_Class/index.html#//apple_ref/occ/cl/NSProcessInfo)di Apple.

### <a name="reacting-to-low-power-mode"></a>Reazione a una modalità a basso consumo

Usare la proprietà `LowPowerModeEnabled` della classe [NSProcessInfo](xref:Foundation.NSProcessInfo) per determinare se la modalità a basso consumo è stata abilitata nel dispositivo iOS in cui è in esecuzione l'app. Ad esempio:

```csharp
// Is the device in low power mode?
if (NSProcessInfo.ProcessInfo.LowPowerModeEnabled) {
    // Reduce activity to conserve energy...
} else {
    // Return to normal activity...
}
```

## <a name="healthkit-framework-changes"></a>Modifiche al Framework di HealthKit

Apple ha incluso le seguenti modifiche al Framework [HealthKit](xref:HealthKit) in iOS 9:

- Supporto per l'eliminazione in blocco e il rilevamento dell'eliminazione di voci nel database HealthKit. Per ulteriori informazioni, vedere le informazioni di [riferimento sulle classi](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKHealthStore_Class/index.html#//apple_ref/doc/uid/TP40014708) [HKDeletedObject](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKDeletedObject_ClassReference/index.html#//apple_ref/occ/cl/HKDeletedObject), [HKAnchoredObjectQuery](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HKAnchoredObjectQuery_Class/index.html#//apple_ref/occ/cl/HKAnchoredObjectQuery) e HKHealthStore di Apple.
- Sono state aggiunte nuove categorie e caratteristiche di rilevamento alla classe `HKQuantityTypeIdentifier` (ad esempio `UVExposure`) e alla classe `HKCategoryTypeIdentifier` (ad esempio `OvulationTestResult`). Per ulteriori informazioni, vedere le informazioni di [riferimento sulle costanti HealthKit](https://developer.apple.com/library/prerelease/ios/documentation/HealthKit/Reference/HealthKit_Constants/index.html#//apple_ref/doc/uid/TP40014710) di Apple.

Per altre informazioni sull'uso di HealthKit in Novell. iOS, vedere la documentazione [introduttiva di HealthKit](~/ios/platform/healthkit.md) .

## <a name="local-authentication-framework-changes"></a>Modifiche al Framework di autenticazione locale

Apple ha incluso le seguenti modifiche al Framework di [autenticazione locale](xref:LocalAuthentication) in iOS 9:

- Utilizzando i metodi `EvaluateAccessControl` e `EvaluatePolicy` della classe [LAContext](xref:LocalAuthentication.LAContext) , è ora possibile riutilizzare le corrispondenze di ID tocco dai precedenti tentativi di sblocco riusciti.
- Possibilità di ottenere un elenco di dita attualmente registrate.
- Supporto per il rilevamento quando un dito viene aggiunto o rimosso dall'autenticazione.
- Possibilità di usare il _contesto di autenticazione_ nelle chiamate keychain e il supporto per la valutazione degli elenchi di controllo di accesso keychain.
- Possibilità di annullare un prompt utente dal codice.

Per altre informazioni, vedere [Touch ID e Face ID con Novell. iOS](~/ios/platform/touch-id-face-id.md).

### <a name="lacontext-changes"></a>LAContext modifiche

Sono state apportate le modifiche seguenti alla classe [LAContext](xref:LocalAuthentication.LAContext) per iOS 9:

- **TouchIdAuthenticationMaximumAllowableReuseDuration** : restituisce l'intervallo di tempo massimo in cui è possibile riutilizzare l'autenticazione con ID tocco.
- **EvaluatedPolicyDomainState** : Ottiene o imposta lo stato di un criterio valutato.
- **MaxBiometryFailures** -è stato ammortizzato in iOS 9.
- **TouchIdAuthenticationAllowableReuseDuration** Ottiene o imposta la quantità di tempo per cui è possibile riutilizzare l'autenticazione con ID tocco.
- **EvaluateAccessControl** : valuta in modo asincrono i criteri di autenticazione.
- **Invalidate** : invalida un'autenticazione con ID tocco specificata.
- Set di **credenziali** : restituisce `true` se le credenziali sono attualmente impostate.
- **SetCredentialType** Imposta il tipo di credenziale specificato.

Per ulteriori informazioni, vedere la Guida di [riferimento a LAContext](https://developer.apple.com/library/prerelease/ios/documentation/LocalAuthentication/Reference/LAContext_Class/index.html#//apple_ref/occ/instm/LAContext/evaluatePolicy:localizedReason:reply:) di Apple.

## <a name="mapkit-framework-changes"></a>Modifiche al Framework di MapKit

Apple ha incluso le seguenti modifiche al Framework [MapKit](xref:MapKit) in iOS 9:

- MapKit offre ora il supporto per l'avvio dell'app map direttamente nelle direzioni di transito e per l'esecuzione di query sul tempo di arrivo stimato di transito (ETA) tramite le classi [MKLaunchOptions](xref:MapKit.MKLaunchOptions) e [MKDirections](xref:MapKit.MKLaunchOptions) .
- I risultati della ricerca restituiti da MapKit e dalla classe [CLGeocoder](xref:CoreLocation.CLGeocoder) possono fornire anche il fuso orario del risultato.
- È ora possibile personalizzare completamente le annotazioni della mappa presentate dall'app iOS usando la proprietà `DetailCalloutAccessoryView` della classe [MKAnnotationView](xref:MapKit.MKAnnotationView) .

Per altre informazioni sull'uso delle mappe e delle annotazioni in Novell. iOS e sul [riferimento CLGeocoder](https://developer.apple.com/library/prerelease/ios/documentation/CoreLocation/Reference/CLGeocoder_class/index.html#//apple_ref/occ/cl/CLGeocoder) di Apple, vedere l'argomento relativo alle [mappe iOS](~/ios/user-interface/controls/ios-maps/index.md) e alla [procedura dettagliata-esplorazione delle annotazioni e sovrimpressioni nella](~/ios/user-interface/controls/ios-maps/ios-maps-walkthrough.md) documentazione di MapKit.

## <a name="passkit-framework-additions"></a>Aggiunte a PassKit Framework

Apple ha incluso le seguenti modifiche al Framework [PassKit](xref:PassKit) in iOS 9:

- Apple Pay supporta ora sia le carte di credito sia le carte di credito, insieme alle schede di individuazione. Per ulteriori informazioni, vedere la sezione **reti di pagamento** del [riferimento alla classe PKPaymentRequest](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKPaymentRequest_Ref/index.html#//apple_ref/doc/uid/TP40014832) di Apple.
- Direttamente all'interno di un'app Novell. iOS, è ora possibile aggiungere reti di pagamento e emittenti di schede a Apple Pay. Per altri dettagli, vedere [riferimento alla classe PKAddPaymentPassViewController](https://developer.apple.com/library/prerelease/ios/documentation/PassKit/Reference/PKAddPaymentPassViewController_Class/index.html#//apple_ref/doc/uid/TP40016116) di Apple.

Per altre informazioni sull'uso di PassKit in Novell. iOS, vedere la documentazione [introduttiva di PassKit](~/ios/platform/passkit.md) .

## <a name="safari-services-framework-additions"></a>Aggiunte di Framework per i Servizi Safari

Apple ha incluso le seguenti modifiche al Framework dei [Servizi Safari](xref:SafariServices) in iOS 9:

- È ora possibile usare la nuova classe [SFSafariViewController](xref:SafariServices.SFSafariViewController) per visualizzare il contenuto Web all'interno di un'app Novell. iOS. Offre la possibilità di condividere i dati e i cookie dei siti Web con l'app Safari e include diverse funzionalità di Safari, ad esempio lettore e riempimento automatico. [SFSafariViewController](xref:SafariServices.SFSafariViewController) funzionalità di un pulsante **Fine** che restituirà gli utenti all'App quando hanno terminato la visualizzazione del contenuto web.

Poiché la classe [SFSafariViewController](xref:SafariServices.SFSafariViewController) è adatta per la visualizzazione di una singola pagina di contenuto Web, è consigliabile usarla per sostituire tutti i controlli [WKWebKit](xref:WebKit.WKWebView) o [UIWebView](xref:UIKit.UIWebView) all'interno delle app Novell. iOS esistenti.

### <a name="displaying-a-website"></a>Visualizzazione di un sito Web

Il codice seguente è un esempio di chiamata di un [SFSafariViewController](xref:SafariServices.SFSafariViewController) dall'interno di un altro controller di visualizzazione:

```csharp
// Create an instance of the Safari Services View Controller
var controller = new SFSafariViewController(new NSUrl("http://www.xamarin.com"));

// Display website
PresentViewController(controller, true, null);
```

## <a name="uikit-framework-changes"></a>Modifiche al Framework di UIKit

Apple ha incluso molti miglioramenti per diversi elementi di [UIKit](xref:UIKit) Framework per iOS 9. Le sezioni seguenti illustrano in dettaglio le modifiche.

### <a name="3d-touch-events"></a>Eventi tocco 3D

Una novità di iOS 9 e di iPhone 6s e iPhone 6s Plus, 3D touch aggiunge movimenti sensibili alla pressione alle app iOS. Di conseguenza, se l'app è in esecuzione in iOS 9 o versione successiva e il dispositivo iOS è in grado di supportare il tocco 3D, le variazioni di pressione comporteranno la generazione dell'evento `TouchesMoved`.

A causa di questa modifica del comportamento, le app iOS devono essere preparate affinché l'evento `TouchesMoved` venga richiamato più spesso, anche se le coordinate X/Y non sono state modificate.

Per altre informazioni, vedere la Guida [introduttiva a 3D Touch](~/ios/platform/3d-touch.md) .

### <a name="document-open-in-place-functionality"></a>Funzionalità di apertura sul posto del documento

Usando i metodi `FinishedLaunching (application, launchOptions)` o `WillFinishLaunching (Application, launchOptions)` della classe [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) , è ora possibile aprire un documento e modificarlo sul posto (anziché lavorare a una copia).

Per supportare la nuova funzionalità di apertura sul posto, aggiungere la chiave di `LSSupportsOpeningDocumentsInPlace` al file **info. plist** dell'app Novell. iOS con un valore di `YES`.

Per ulteriori informazioni, vedere la Guida di [riferimento a UIApplicationDelegate](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) di Apple.

### <a name="enhanced-touch-events"></a>Eventi tocco avanzati

Apple ha fornito diversi miglioramenti per gli eventi di tocco in iOS 9. Queste includono la possibilità di utilizzare la stima del tocco e di ottenere l'accesso ai tocchi intermedi tra gli aggiornamenti della visualizzazione.

Per altri dettagli, vedere la [Guida alla gestione degli eventi di Apple per iOS](https://developer.apple.com/library/ios/documentation/EventHandling/Conceptual/EventHandlingiPhoneOS/Introduction/Introduction.html) .

### <a name="fetching-tailored-content"></a>Recupero di contenuto personalizzato

La nuova classe `NSDataAsset` consente a un'app Novell. iOS di recuperare contenuti personalizzati per la memoria e le funzionalità grafiche del dispositivo iOS in cui è attualmente in esecuzione.

### <a name="new-layout-anchors"></a>Nuovi ancoraggi del layout

Le nuove classi di ancoraggio del layout `NSLayoutAnchor` e `NSLayoutDimension` funzionano con le nuove proprietà di ancoraggio della classe [UIView](xref:UIKit.UIView) (ad esempio `LeadingAnchor` e `WidthAnchor`) per semplificare il layout in iOS 9.

Per altre informazioni sull'uso delle classi di layout automatico e di dimensioni in un'app Novell. iOS e [riferimento NSLayoutAnchor](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutAnchor_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutAnchor)di Apple, riferimento a [NSLayoutDimension](https://developer.apple.com/library/prerelease/ios/documentation/AppKit/Reference/NSLayoutDimension_ClassReference/index.html#//apple_ref/occ/cl/NSLayoutDimension) e [riferimento a UIView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIView_Class/index.html#//apple_ref/occ/cl/UIView) per altre informazioni, vedere la documentazione [introduttiva per gli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) .

### <a name="new-readable-content-margins"></a>Nuovi margini di contenuto leggibili

La nuova classe `UILayoutGuide` può essere utilizzata per fornire margini di contenuto leggibili e definire le aree di creazione per il contenuto all'interno di una visualizzazione. Per ulteriori informazioni, vedere la Guida di [riferimento a UILayoutGuide](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UILayoutGuide_Class_Reference/index.html#//apple_ref/occ/cl/UILayoutGuide) di Apple.

### <a name="text-input-in-notifications-modifications"></a>Input di testo nelle modifiche alle notifiche

La classe [UIUserNotificationAction](xref:UIKit.UIUserNotificationAction) dispone di una nuova proprietà `Behavior` che può essere utilizzata per supportare l'input di testo dalle notifiche.

### <a name="uiapplicationdelegate-changes"></a>UIApplicationDelegate modifiche

Sebbene non sia formalmente deprecato da Apple, suggerisce di sostituire tutte le chiamate al metodo `FinishedLaunching (UIApplication application)` della classe [UIApplicationDelegate](xref:UIKit.UIApplicationDelegate) con i metodi `FinishedLaunching (UIApplication application, NSDictionary launchOptions)` o `WillFinishLaunching (UIApplication application, NSDictionary launchOptions)`.

Per ulteriori informazioni, vedere la Guida di [riferimento a UIApplicationDelegate](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intf/UIApplicationDelegate) di Apple.

### <a name="uikit-dynamics-changes"></a>Modifiche di UIKit Dynamics

Apple ha incluso le seguenti modifiche apportate a UIKit Dynamics in iOS 9:

- Dynamics offre ora il supporto per i limiti di collisione non rettangolari.
- La nuova classe di `UIFieldBehavior` personalizzabile viene utilizzata per supportare diversi tipi di campo.
- Alla classe `UIAttachmentBehavior` sono stati aggiunti altri tipi di allegati.

Per ulteriori informazioni, vedere la Guida di [riferimento a UIAttachment](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIAttachmentBehavior_Class/index.html#//apple_ref/occ/cl/UIAttachmentBehavior) di Apple.

### <a name="uipickerview-and-uidatepicker-changes"></a>Modifiche a UIPickerView e UIDatePicker

Prima di iOS 9, i controlli [UIPickerView](xref:UIKit.UIPickerView) e [UIDatePicker](xref:UIKit.UIDatePicker) erano non ridimensionabili e verrebbero ridimensionati automaticamente per riempire la larghezza del contenitore (in genere la larghezza del dispositivo iOS in cui era in esecuzione l'app).

In iOS 9 questo ridimensionamento automatico non si verifica più e viene eseguito il rendering dei controlli a una larghezza di 320 punti su tutti i dispositivi iOS, indipendentemente dalle dimensioni dello schermo e dall'orientamento.

Per correggere questa situazione, utilizzare le classi layout automatico e dimensioni per aggiungere la larghezza del controllo ai bordi del contenitore padre (visualizzazione) e specificare l'altezza richiesta. Per altre informazioni sull'uso del layout automatico e delle classi di dimensioni in un'app Novell. iOS, vedere la documentazione [introduttiva per gli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) .

### <a name="new-uitextinputassistantitem-class"></a>Nuova classe UITextInputAssistantItem

Utilizzare la nuova classe `UITextInputAssistantItem` per i gruppi di pulsanti della barra di layout in una _barra di scelta rapida_. La barra dei collegamenti è una nuova area disponibile nella tastiera morbida per fornire collegamenti di tipizzazione.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Introduzione a iOS 9](~/ios/platform/introduction-to-ios9/index.md)
- [iOS 9 per sviluppatori](https://developer.apple.com/ios/pre-release/)
- [Novità di iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
