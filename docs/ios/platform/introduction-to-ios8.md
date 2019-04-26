---
title: Introduzione a iOS 8
description: Con iOS 8, Apple ha fornito una vasta gamma di API per coinvolgere maggiormente e soddisfano gli sviluppatori e nuovi Framework. In questa guida verranno introducono le nuove API e iOS 8 i vantaggi sia agli sviluppatori e utenti.
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 9299322eb20561444262c2b2ba87191d2bddcde4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61317641"
---
# <a name="introduction-to-ios-8"></a>Introduzione a iOS 8

_Con iOS 8, Apple ha fornito una vasta gamma di API per coinvolgere maggiormente e soddisfano gli sviluppatori e nuovi Framework. In questa guida verranno introducono le nuove API e iOS 8 i vantaggi sia agli sviluppatori e utenti._

iOS 7 modificare visivamente l'interfaccia utente iOS intero da quali utenti e sviluppatori avevano ormai si aspettano, direttamente dall'iPhone prima del sistema operativo. IOS 8 continua con questo, fornendo molti Framework per sviluppatori, che consente agli utenti di controllare quasi ogni aspetto della loro vita direttamente dal proprio iPhone. Ad esempio stato e adeguatezza possono essere analizzati con *HealthKit*, i passcode vengono obsoleta con l'autenticazione biometrica *LocalAuthentication*, *estensioni App*aprire un canale di comunicazione tra app di terze parti 3rd, e *HomeKit* offre la possibilità di trasformare la casa in una posizione iniziale del futuro. 

Se nient' gli utenti che incontrano il gusto iOS 7, iOS 8 è incentrato sugli sviluppatori che incontrano il gusto con un'ampia gamma di questi nuovi strumenti pis. 

Questa guida illustra le nuove API per gli sviluppatori di xamarin. IOS.  

Esistono anche alcune API che sono state deprecate in iOS 8, che si trovano alla fine di questo documento.

## <a name="requirements"></a>Requisiti

Per creare App iOS 8 in Visual Studio per Mac è necessario quanto segue:

- **Xcode 7 e iOS 8 o versione superiore** – Xcode e API iOS più recenti di Apple devono essere installati e configurati nel computer dello sviluppatore.
- **Visual Studio per Mac** : la versione più recente di Visual Studio per Mac deve essere installata e configurata sul dispositivo dell'utente.
- **iOS 8 dispositivo o simulatore** : un dispositivo iOS in esecuzione la versione più recente di iOS 8 per i test.

## <a name="home-and-leisure"></a>Ambiente domestico e tempo libero

iOS 8 ha contribuito a saldamente prevede Apple e il dispositivo iOS direttamente al cuore della propria abitazione tramite l'uso di HomeKit e HealthKit. In questa sezione si esaminerà come entrambi questi nuovi Framework di lavoro e come può essere integrati nell'applicazione xamarin. IOS.

## <a name="homekit"></a>HomeKit

Controllo le tue Appliance dall'iPhone, non è una nuova applicazione della tecnologia; molti prodotti connessi-home possono essere controllati tramite un'app per iOS. Tuttavia HomeKit ora accetta questo un ulteriore passo promuovendo un protocollo comune per i dispositivi di domotica e rendendo disponibili un'API pubblica per alcuni produttori, ad esempio, iHome Philips e Honeywell. All'utente, ciò significa che possono controllare quasi ogni aspetto di casa con facilità all'interno di un'applicazione. È irrilevante indicasse che utilizzano una lampadina Hue Philips, o un avviso di annidamento. Gli utenti possono concatenare anche numerosi processi home smart in "Background".

Con HomeKit, Siri e App di terze parti possono individuare accessori e aggiungerli al rispettivo database di configurazione di casa personali, modificare e agiscono su dati e comunicare con i propri servizi per eseguire un'azione e accessori.

### <a name="configuration"></a>Configurazione

Il diagramma seguente mostra la gerarchia di base della configurazione degli accessori HomeKit:

![](introduction-to-ios8-images/image1.png "Questo diagramma mostra la gerarchia di base della configurazione degli accessori HomeKit")
 
Per iniziare a usare HomeKit, gli sviluppatori dovranno verificare che il profilo di provisioning sia il servizio di HomeKit selezionato. Apple ha anche fornito agli sviluppatori con un componente aggiuntivo simulatore HomeKit per Xcode. Questo valore può essere trovato nel [Apple Developer Center](https://developer.apple.com/downloads/index.action), in `Hardware IO Tools for Xcode`. 

Per altre informazioni, vedere la [HomeKit](~/ios/platform/homekit.md) Guida.

## <a name="healthkit"></a>HealthKit

HealthKit è un framework introdotto in iOS 8 che fornisce un archivio dati centralizzato, coordinato e sicuro per le informazioni correlate all'integrità. Il sistema operativo garantisce la privacy e sicurezza delle informazioni sanitarie e, con l'app di integrità, un dashboard per l'utente. Con l'autorizzazione dell'utente, le applicazioni possono leggere e scrivere una vasta gamma di informazioni sull'integrità.

Per altre informazioni sull'utilizzo di questo nell'app xamarin. IOS, vedere la [Introduzione a HealthKit](~/ios/platform/healthkit.md) Guida.

## <a name="extending-iphone-functionality"></a>Estendere la funzionalità di iPhone
Con IOS 8, gli sviluppatori sono in corso ha un maggiore controllo su chi può usare le app e una maggiore capacità per la comunicazione più aperta tra le app di terze parti. Funzionalità quali selezione documento e le estensioni dell'App apre un mondo di possibilità per l'utilizzo delle applicazioni nell'ecosistema di Apple.

### <a name="app-extensions"></a>Estensioni dell'App
Le estensioni dell'App, per semplificare eccessivamente, rappresentano un modo per le app di terze parti comunicare tra loro. Per gestire standard elevati di sicurezza e per incontrarsi l'integrità delle App in modalità sandbox, questa comunicazione non si verifica direttamente tra le applicazioni. Al contrario, viene eseguito da un *estensione* al centro.

Il primo passaggio nella creazione di un'estensione di App consiste nel definire il punto di estensione corretto: questo è importante per assicurare il comportamento e la disponibilità delle API corrette. Per creare un'estensione dell'App in Visual Studio per Mac, aggiungerlo a un'applicazione esistente mediante l'aggiunta di un nuovo progetto alla soluzione.

Nel **nuovo progetto** finestra di dialogo passare alla **C#**  >  **iOS** > **API unificata**  >  **Estensioni**, come illustrato nello screenshot seguente:

![](introduction-to-ios8-images/image2.png "Creazione di una nuova estensione")
 
La finestra di dialogo Nuovo progetto fornisce sette nuovi modelli di progetto per la creazione di estensioni App e vengono descritte di seguito. Si noti che molte delle estensioni sono correlate ad altre API di nuovo in iOS, ad esempio selezione documento:

- **Azione** – ciò consente agli sviluppatori di creare pulsanti di azione personalizzata univoco che consente agli utenti eseguono determinate operazioni
- **Da tastiera personalizzato** – ciò consente agli sviluppatori di aggiungere l'intervallo di compilato in Apple tastiere aggiungendo i propri uno personalizzato. La tastiera più diffusi, Swype Usa questa a portare la tastiera per iOS.
- **Selezione del documento** : questo file contiene un Controller di visualizzazione del documento di selezione che consente agli utenti di accedere ai file all'esterno di sandbox dell'applicazione.
- **Provider di File selezione documento** – ciò offre l'archiviazione protetta per i file usando il selettore di documento.
- **Modifica foto** : questa amplia i filtri e gli strumenti già forniti da Apple nell'applicazione foto per concedere agli utenti maggiore controllo e altre opzioni quando si modificano le foto di modifica.
- **Oggi** – questo offre la possibilità di visualizzare i widget nella sezione di oggi del centro notifiche alle applicazioni.

Per altre informazioni sull'uso delle estensioni di App di Xamarin, vedere la [Introduzione alle App estensioni](~/ios/platform/extensions.md) Guida.

### <a name="touch-id"></a>ID tocco

Touch ID è stato introdotto in iOS 7 come mezzo per l'autenticazione dell'utente, simile a un passcode. Tuttavia, era limitato a sbloccare il dispositivo, tramite l'App Store, usando iTunes e l'autenticazione solo il keychain iCloud 

Esistono ora due modi per usare Touch ID come meccanismo di autenticazione nelle applicazioni iOS 8 usando l'API di autenticazione locale. Attualmente non è possibile usare l'autenticazione locale per eseguire l'autenticazione in modalità remota.

In primo luogo, assiste i servizi di Keychain esistenti tramite l'uso di nuovi Keychain Access Control Lists (ACL). È possibile sbloccarle con l'autenticazione di utenti impronta digitale Keychain dati.

In secondo luogo, LocalAuthentication fornisce due metodi per eseguire l'autenticazione dell'applicazione in locale. Gli sviluppatori devono usare `CanEvaluatePolicy` per determinare se il dispositivo è in grado di accettare Touch ID e quindi `EvaluatePolicy` per avviare l'operazione di autenticazione.

Per altre informazioni sull'ID tocco e per scoprire come integrarlo in un'applicazione xamarin. IOS, vedere la [Introduzione a TouchID](~/ios/platform/touchid.md) Guide.

### <a name="document-picker"></a>Selezione documenti

Funzionamento della selezione documento con un'unità di iCloud degli utenti per consentire all'utente di aprire i file che sono stati creati in un'altra app, importano e li modificano ed esportarli nuovamente. Verrà creato un flusso di lavoro intuitivo e pertanto migliorare notevolmente, per gli utenti. la sincronizzazione iCloud accetta ulteriormente questo passaggio, ovvero tutte le modifiche apportate in un'applicazione verrà applicata anche a essere coerente in tutti i dispositivi.

Per informazioni sulla selezione di documenti in modo più approfondito e per informazioni su come integrare un'applicazione xamarin. IOS, vedere la [Introduzione a selezione documento The](~/ios/platform/document-picker.md) Guida.

### <a name="handoff"></a>Handoff

Passaggio di consegne, che fa parte della funzionalità di continuità aziendale più grande, accetta un passo avanti verso l'integrazione di OS X e iOS. Ciò include lo sviluppo multipiattaforma AirDrop, la possibilità di rispondere alle chiamate di iPhone, SMS su iPad e Mac e i miglioramenti in tethering dall'iPhone.

Esegui handoff è compatibile con iOS 8 e Yosemite e richiede un account iCloud avere effettuato l'accesso a tutti i dispositivi di diversi da usare. Dovrebbe funzionare con le app Apple più preinstallate, tra cui Safari iWork, mappe, i calendari e contatti.

Per altre informazioni, vedere la [Handoff](~/ios/platform/handoff.md) Guida.

## <a name="unified-storyboards"></a>Storyboard unificati
iOS 8 include un nuovo più semplice usare meccanismo per la creazione dell'interfaccia utente, ovvero lo storyboard unificato. Con uno storyboard singolo per includere tutte le dimensioni dello schermo di un hardware diverso, veloce e reattive viste possono essere create in uno stile "progettare una sola volta, utilizzare molti" true.

Prima di IOS 8, gli sviluppatori utilizzavano `UIInterfaceOrientation` per distinguere tra modalità verticale e orizzontale, e `UIInterfaceIdiom` per distinguere tra i dispositivi iOS. In IOS 8 non è più necessario creare storyboard separati per i dispositivi iPhone e iPad, orientamento e dispositivo vengono determinati usando *le classi di dimensioni*.

Tutti i dispositivi sono definito da una classe di dimensioni, in verticale e l'asse orizzontale, e sono disponibili due tipi di classi di dimensioni in iOS 8:

- **Regolare** -si tratta di una dimensione di schermi di grandi dimensioni (ad esempio un iPad) o un gadget che dà l'impressione di grandi dimensioni (ad esempio un UIScrollView
- **Compact** -si tratta di dispositivi di piccole dimensioni (ad esempio iPhone). Queste dimensioni prende in considerazione l'orientamento del dispositivo.

Se i due concetti vengono utilizzati insieme, il risultato è una 2x2 griglia che definisce le diverse dimensioni possibili che possono essere usate in entrambi gli orientamenti diversi, come illustrato nel diagramma seguente:

![](introduction-to-ios8-images/image3.png "Un diagramma che rappresenta la 2x2 griglia che definisce le diverse dimensioni possibili che possono essere utilizzate in entrambi gli orientamenti diversi")
 
Per altre informazioni sulle classi di dimensioni, vedere la [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md).

## <a name="photo-kit"></a>Kit di foto
Foto Kit è un framework nuovo che consente alle applicazioni di eseguire una query nella raccolta di immagini di sistema e creare interfacce utente personalizzate per visualizzare e modificare il relativo contenuto. Include una serie di classi che rappresentano gli asset video e immagine, nonché le raccolte di risorse, ad esempio gli album e cartelle.

Per altre informazioni, vedere la [PhotoKit](~/ios/platform/photokit.md) Guida.

## <a name="games"></a>Giochi

<a name="scenekit" />

### <a name="scene-kit"></a>Scenekit

Scena Kit è un grafico della scena 3D API che semplifica l'utilizzo di grafica 3D. È stata introdotta in OS X 10.8 e ha ora iniziato a iOS 8. Con Scenekit creazione coinvolgenti visualizzazioni 3D e casual games 3D non richiede competenze in OpenGL. Sulla base di concetti comuni di graph scena, Scenekit consente di evitare le complessità correlate OpenGL e OpenGL ES, rendendo molto più semplice aggiungere 3D contenuto a un'applicazione. Tuttavia, se sei un esperto di OpenGL, Scenekit ha un notevole supporto per l'associazione di direttamente con OpenGL anche. Inoltre include numerose funzionalità che integrano quelli grafica 3D, ad esempio fisica e si integra molto bene con diversi altri framework di Apple, ad esempio Core Animation, Core immagine e Spritekit.

Per altre informazioni, vedere la [SceneKit](~/ios/platform/gaming/scenekit.md) documentazione.

<a name="spritekit" />

### <a name="sprite-kit"></a>Spritekit

Spritekit, il framework di gioco 2D da Apple, dispone di alcune nuove interessanti funzionalità in iOS 8 e OS X Yosemite. Queste includono l'integrazione con Scenekit, supporto dello shader, illuminazione, ombreggiature, vincoli, generazione di mappe normali e miglioramenti fisica. In particolare, le nuove funzionalità di effetti fisici rendono molto semplice aggiungere effetti realistici di un gioco.

Per altre informazioni, vedere la [SpriteKit](~/ios/platform/gaming/spritekit.md) documentazione.

## <a name="other-changes"></a>Altre modifiche
Nonché le modifiche principali in iOS 8 che sono descritte in precedenza, Apple ha inoltre aggiornato molti Framework esistenti. Questi sono descritti di seguito:

- **[Immagine di base](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)**  : Apple ha espanso il framework di elaborazione di immagini mediante l'aggiunta di un supporto migliore per il rilevamento di aree rettangolari e i codici a matrice all'interno di immagini. Mike Bluestein esamina questo nel suo blog post intitolato [rilevamento immagine in iOS 8](https://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>API deprecate
Con tutti i miglioramenti apportati in iOS 8, una serie di API è deprecata. Di seguito sono descritte alcune di queste.

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)**  : I metodi e proprietà usate per la registrazione delle notifiche remote sono deprecati. Si tratta registerForRemoteNotificationTypes ed enabledRemoteNotificationTypes.
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)**  – Traits e classi di dimensioni hanno sostituito i metodi e proprietà utilizzati per descrivere l'orientamento di interfaccia. Vedere le [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) per altre informazioni su come usare questi.

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)**  – questo è stato sostituito da UISearchController in IOS 8.

## <a name="summary"></a>Riepilogo
In questo articolo abbiamo esaminato alcune delle nuove funzionalità introdotte in iOS 8 da Apple.



## <a name="related-links"></a>Collegamenti correlati

- [UIKitEnhancements (esempio)](https://developer.xamarin.com/samples/monotouch/ios8/UIKitEnhancements)
- [Introduzione alle App estensioni](~/ios/platform/extensions.md)
- [Introduction to CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) (Introduzione a CloudKit)
- [Introduzione al controllo di selezione documento](~/ios/platform/document-picker.md)
- [Introduzione a HealthKit](~/ios/platform/healthkit.md)
- [Introduzione ai controlli della fotocamera manuali](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [Introduzione a touch ID](~/ios/platform/touchid.md)
- [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md)
