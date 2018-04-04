---
title: Introduzione a iOS 8
description: Con iOS 8, Apple ha fornito un moltissime nuovi Framework e API per eccitare e soddisfano gli sviluppatori. In questa Guida verrà introduce le nuove API e vedere come iOS 8 possibile trarre vantaggio gli sviluppatori e utenti.
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 2f57547356adcbafd01851bc54e42a14454ccd6a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-ios-8"></a>Introduzione a iOS 8

_Con iOS 8, Apple ha fornito un moltissime nuovi Framework e API per eccitare e soddisfano gli sviluppatori. In questa Guida verrà introduce le nuove API e vedere come iOS 8 possibile trarre vantaggio gli sviluppatori e utenti._

iOS 7 modificare visivamente l'interfaccia utente di iOS intero da quali utenti e sviluppatori avevano aspettano, direttamente da iPhone prima del sistema operativo. IOS 8 continua con questo fornendo molti Framework per sviluppatori, che consente agli utenti di controllare quasi ogni aspetto della vita retta da loro iPhone. Ad esempio stato e adeguatezza possono essere analizzati con *HealthKit*, passcode sono obsolete con l'autenticazione biometrica *LocalAuthentication*, *estensioni App*aprire un canale di comunicazione tra le app di terze parti 3rd, e *HomeKit* offre la possibilità di trasformare casa in una posizione iniziale del futuro. 

Se era delighting gli utenti iOS 7, iOS 8 è incentrata sulla delighting agli sviluppatori un ampio intervallo di questi nuovi strumenti pis. 

Questa guida vengono presentate le nuove API per gli sviluppatori di xamarin. IOS.  

Esistono inoltre alcune API che sono state deprecate in iOS 8, vengono descritti in dettaglio alla fine di questo documento.

## <a name="requirements"></a>Requisiti

Per creare le app iOS 8 in Visual Studio per Mac è necessario quanto segue:

- **Xcode 7 e iOS 8 o versione successiva** – Xcode e API iOS più recente di Apple devono essere installati e configurati nel computer dello sviluppatore.
- **Visual Studio per Mac** : la versione più recente di Visual Studio per Mac deve essere installata e configurata sul dispositivo dell'utente.
- **iOS 8 dispositivo o simulatore** : un dispositivo iOS in esecuzione la versione più recente di iOS 8 per il test.

## <a name="home-and-leisure"></a>Home page e tempo libero

iOS 8 ha contribuito a solidamente dello stabilimento Apple e il dispositivo iOS direttamente nel cuore della casa tramite l'utilizzo di HomeKit e HealthKit. In questa sezione verranno esaminati di lavoro come entrambi questi nuovi Framework e come può essere integrati nell'applicazione di xamarin. IOS.

## <a name="homekit"></a>HomeKit

Controllare i dispositivi da iPhone, non è una nuova applicazione di tecnologia. numero di prodotti home connessi può essere controllati tramite un'app iOS. Tuttavia HomeKit ora accetta questo ulteriormente la promozione di un protocollo comune per i dispositivi di automazione della casa e rendendo disponibile una API pubblica per alcuni produttori, ad esempio iHome, Philips e Honeywell. All'utente, ciò significa che possono controllare quasi ogni aspetto di casa perfettamente da all'interno di un'applicazione. È irrilevante per essere a conoscenza che usano una lampadina Philips tonalità o un avviso di nidificazione. Gli utenti possono inoltre concatenare numerosi processi home smart insieme in "Scene".

Con HomeKit, App di terze parti e Siri può individuare accessori e aggiungerli al rispettivo database di configurazione principale personale, modificare e agiscono su dati e comunicare con accessori e ai servizi per eseguire un'azione.

### <a name="configuration"></a>Configurazione

Il diagramma seguente mostra la gerarchia di base della configurazione degli accessori HomeKit:

![](introduction-to-ios8-images/image1.png "Questo diagramma mostra la gerarchia di base della configurazione degli accessori HomeKit")
 
Per informazioni introduttive su HomeKit, gli sviluppatori dovranno assicurarsi che il profilo di provisioning è il servizio HomeKit selezionato. Apple fornisce inoltre agli sviluppatori un componente aggiuntivo simulatore HomeKit per Xcode. Questo può essere trovato il [Apple Developer Center](https://developer.apple.com/downloads/index.action)in `Hardware IO Tools for Xcode`. 

Per ulteriori informazioni, vedere il nostro [HomeKit](~/ios/platform/homekit.md) Guida.

## <a name="healthkit"></a>HealthKit

HealthKit è un framework, introdotto in iOS 8 che fornisce un archivio dati centralizzato, coordinato e sicuro per le informazioni correlate di integrità. Il sistema operativo garantisce la privacy e protezione delle informazioni di integrità e, con l'app di integrità, un dashboard per l'utente. Con l'autorizzazione dell'utente, le applicazioni possono leggere e scrivere una vasta gamma di informazioni di integrità.

Per ulteriori informazioni sull'utilizzo di questa in app xamarin, vedere il [Introduzione a HealthKit](~/ios/platform/healthkit.md) Guida.

## <a name="extending-iphone-functionality"></a>Estensione delle funzionalità iPhone
Con 8, gli sviluppatori sono viene fornito un maggiore controllo su chi può usare l'app e capacità maggiori per la comunicazione tra applicazioni di terze parti più aperta. Funzionalità di selezione di documento e le estensioni App aprire un mondo di possibilità per come utilizzare le applicazioni nell'ecosistema di Apple.

### <a name="app-extensions"></a>Estensioni
Le estensioni, per semplificare eccessivamente, vengono utilizzati per le applicazioni di terze parti comunicare tra loro. Per mantenere gli standard di sicurezza elevato nonché l'integrità delle App in modalità sandbox, la comunicazione non si verifica direttamente tra applicazioni. In alternativa, essa viene effettuata da un *estensione* al centro.

Il primo passaggio nella creazione di un'estensione dell'App consiste nel definire il punto di estensione corretto, questo aspetto è importante nel garantire il comportamento e la disponibilità delle API corrette. Per creare un'estensione dell'App in Visual Studio per Mac, aggiungerlo a un'applicazione esistente aggiungendo un nuovo progetto alla soluzione.

Nel **nuovo progetto** finestra di dialogo passare a **c#** > **iOS** > **Unified API**  >   **Estensioni**, come illustrato nella schermata seguente:

![](introduction-to-ios8-images/image2.png "Creazione di una nuova estensione")
 
La finestra di dialogo Nuovo progetto fornisce sette nuovi modelli di progetto per la creazione di estensioni dell'App e sono descritti di seguito. Si noti che molte delle estensioni sono correlate ad altre API in iOS, ad esempio la selezione documento nuovo:

- **Azione** : ciò consente agli sviluppatori di creare pulsanti di azione personalizzata univoco che consente agli utenti di eseguono determinate operazioni
- **Tasti** – in questo modo gli sviluppatori di aggiungere l'intervallo di compilata in Apple tastiere aggiungendo i propri uno personalizzato. La tastiera più diffusa, Swype utilizza per portare la tastiera per iOS.
- **Selezione del documento** : questo file contiene un documento selezione View Controller che consente agli utenti di accedere ai file all'esterno di sandbox dell'applicazione.
- **Selezione Provider di File del documento** – fornisce archiviazione sicura per i file usando la selezione del documento.
- **Modifica di foto** : questa espande i filtri e gli strumenti già forniti da Apple nell'applicazione foto per concedere agli utenti maggiore controllo e altre opzioni quando si modifica la foto di modifica.
- **Oggi** – in questo modo le applicazioni con la possibilità di visualizzare i widget nella sezione oggi dell'area di notifica.

Per ulteriori informazioni sull'utilizzo di estensioni dell'App in Xamarin, vedere il [Introduzione alle estensioni App](~/ios/platform/extensions.md) Guida.

### <a name="touch-id"></a>ID tocco

Touch ID è stato introdotto in iOS 7 come mezzo per l'autenticazione dell'utente, simile a un passcode. Tuttavia, è limitato a sbloccare il dispositivo, utilizzando l'App Store, iTunes e l'autenticazione solo il portachiavi iCloud 

Esistono due modi per usare l'ID tocco come meccanismo di autenticazione in applicazioni iOS 8 tramite l'API di autenticazione locale. Non è attualmente possibile utilizzare l'autenticazione locale per l'autenticazione in modalità remota.

In primo luogo, assiste i servizi di portachiavi esistenti tramite l'utilizzo di nuovo portachiavi controllo elenchi di accesso (ACL). Dati Keychain possono essere sbloccati con l'autenticazione ha esito positivo di un'impronta digitale gli utenti.

In secondo luogo, LocalAuthentication fornisce due metodi per l'autenticazione dell'applicazione in locale. Gli sviluppatori devono usare `CanEvaluatePolicy` per determinare se il dispositivo è in grado di accettare Touch ID e quindi `EvaluatePolicy` per avviare l'operazione di autenticazione.

Per ulteriori informazioni sull'ID tocco e per informazioni su come integrare un'applicazione di xamarin. IOS, vedere il [Introduzione a touch ID](~/ios/platform/touchid.md) Guide.

### <a name="document-picker"></a>Selezione di documento

Documento selezione può essere utilizzata con un'unità di iCloud degli utenti per consentire all'utente di aprire i file che sono stati creati in un altro app, importano e modificarli e di esportarle nuovamente. Crea un flusso di lavoro intuitiva e pertanto migliorare notevolmente, per gli utenti. la sincronizzazione iCloud accetta ulteriormente questo passaggio, eventuali modifiche apportate a un'applicazione di essere rifletterà anche in modo coerente tra tutti i dispositivi.

Per ulteriori informazioni sulla selezione di documento in modo più approfondito e per informazioni su come integrare un'applicazione di xamarin. IOS, vedere il [introduzione per la selezione di documento](~/ios/platform/document-picker.md) Guida.

### <a name="handoff"></a>Handoff

Passaggio di consegne, che fa parte della funzionalità di continuità di dimensioni maggiori, accetta un passo avanti verso l'integrazione di OS X e iOS. Ciò include AirDrop multipiattaforma, la possibilità di eseguire chiamate di iPhone, SMS nei miglioramenti tethering dall'iPhone, iPad e Mac.

Funziona con iOS 8 e Yosemite e richiede un account iCloud avere effettuato l'accesso a tutti i dispositivi diversi che si desidera utilizzare. Dovrebbe funzionare con pre-installata più app di Apple, tra cui contatti, calendari, mappe, iWork e Safari.

Per ulteriori informazioni, vedere il nostro [Handoff](~/ios/platform/handoff.md) Guida.

## <a name="unified-storyboards"></a>Storyboard unificati
include un nuovo iOS 8 più semplice utilizzare un meccanismo per la creazione dell'interfaccia utente, ovvero lo storyboard unificato. Con un unico storyboard per includere tutte le dimensioni dello schermo di hardware diverso, è possono creare viste veloce e reattive in uno stile "progettazione di una volta, utilizzare molte" true.

Prima di 8, gli sviluppatori utilizzavano `UIInterfaceOrientation` per distinguere tra le modalità verticale e orizzontale, e `UIInterfaceIdiom` per distinguere tra i dispositivi iOS. In 8 non è più necessario creare gli storyboard separati per i dispositivi iPhone e iPad, orientamento e i dispositivi sono determinati utilizzando *classi*.

Ogni dispositivo è definito da una classe di dimensione, in verticale sia sull'asse orizzontale, e sono disponibili due tipi di classi di dimensioni in iOS 8:

- **Regolare** -si tratta di una dimensione di schermi di grandi dimensioni (ad esempio un iPad) o un gadget che dà l'impressione di grandi dimensioni (ad esempio un UIScrollView
- **Compact** -si tratta di dispositivi di piccole dimensioni (ad esempio iPhone). Questa dimensione prende in considerazione l'orientamento del dispositivo.

Se i due concetti sono utilizzati insieme, il risultato è una 2x2 griglia che definisce le diverse dimensioni possibili che possono essere usate in entrambi gli orientamenti diversi, come illustrato nel diagramma seguente:

![](introduction-to-ios8-images/image3.png "Un diagramma che rappresenta la 2x2 griglia che definisce le diverse dimensioni possibili che possono essere usate in entrambi gli orientamenti diversi")
 
Per ulteriori informazioni sulle classi di dimensioni, vedere il [Introduzione agli storyboard unificata](~/ios/user-interface/storyboards/unified-storyboards.md).

## <a name="photo-kit"></a>Kit di foto
Foto Kit è un nuovo framework che consente di eseguire una query la libreria di immagini di sistema e creare interfacce utente personalizzate per visualizzare e modificare il relativo contenuto. Include un numero di classi che rappresentano risorse video e immagine, nonché le raccolte di risorse, ad esempio album e le cartelle.

Per ulteriori informazioni, vedere il nostro [PhotoKit](~/ios/platform/photokit.md) Guida.

## <a name="games"></a>Giochi

<a name="scenekit" />

### <a name="scene-kit"></a>Kit di scena

Scena Kit è un grafico di scena 3D API che semplifica l'utilizzo con la grafica 3D. È stata introdotta in OS X è 10.8 e che è ora giunto a iOS 8. Kit di scena creare accattivanti visualizzazioni 3D e casuali giochi 3D non richiede competenze in OpenGL. Compilazione sui concetti relativi al grafico di scena comuni, i Kit di scena estrae le complessità di OpenGL e OpenGL ES, semplificando notevolmente 3D aggiungere contenuto a un'applicazione. Tuttavia, se si è esperti OpenGL, scena Kit è ottimo supporto per l'associazione di direttamente con OpenGL anche. Inoltre, include numerose funzionalità che integrano la grafica 3D, ad esempio fisica e si integra molto bene con diversi altri framework di Apple, ad esempio Core animazione, immagine di base e Sprite Kit.

Per ulteriori informazioni, vedere il nostro [SceneKit](~/ios/platform/gaming/scenekit.md) documentazione.

<a name="spritekit" />

### <a name="sprite-kit"></a>Sprite Kit

Sprite Kit, il framework di gioco 2D di Apple, include alcune nuove funzionalità interessanti in iOS 8 e OS X Yosemite. Queste includono l'integrazione con scena Kit, supporto dello shader, illuminazione, ombreggiature, vincoli, la generazione di mappe normali e miglioramenti fisica. In particolare, le nuove funzionalità di fisica rendono molto semplice aggiungere effetti realistici per un gioco.

Per ulteriori informazioni, vedere il nostro [SpriteKit](~/ios/platform/gaming/spritekit.md) documentazione.

## <a name="other-changes"></a>Altre modifiche
Nonché le principali modifiche iOS 8 descritte in precedenza, Apple ha inoltre aggiornate molti Framework esistenti. Questi sono descritte di seguito:

- **[Immagine di base](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)**  : Apple è espansa sul framework di elaborazione di immagini tramite l'aggiunta di un supporto migliore per il rilevamento di aree rettangolari e codici a matrice all'interno di immagini. Mike Bluestein si esamina nel suo blog post intitolata [rilevamento immagine in iOS 8](http://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>API deprecate
Con tutti i miglioramenti apportati in iOS 8, una serie di API è deprecata. Alcune di queste sono descritte di seguito.

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)**  : I metodi e proprietà utilizzate per la registrazione delle notifiche remote sono deprecate. Si tratta registerForRemoteNotificationTypes ed enabledRemoteNotificationTypes.
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)**  – tratti e classi di dimensione sono sostituiti i metodi e proprietà utilizzati per descrivere l'orientamento di interfaccia. Fare riferimento al [Introduzione agli storyboard unificata](~/ios/user-interface/storyboards/unified-storyboards.md) per ulteriori informazioni su come utilizzarle.

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)**  – questo è stato sostituito da UISearchController in 8.

## <a name="summary"></a>Riepilogo
In questo articolo è stato esaminato alcune delle nuove funzionalità introdotte da Apple in iOS 8.



## <a name="related-links"></a>Collegamenti correlati

- [UIKitEnhancements (esempio)](https://developer.xamarin.com/samples/monotouch/ios8/UIKitEnhancements)
- [Introduzione alle estensioni dell'App](~/ios/platform/extensions.md)
- [Introduction to CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) (Introduzione a CloudKit)
- [Introduzione al controllo di selezione di documento](~/ios/platform/document-picker.md)
- [Introduzione a HealthKit](~/ios/platform/healthkit.md)
- [Introduzione ai controlli della fotocamera manuale](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [Introduzione a touch ID](~/ios/platform/touchid.md)
- [Introduzione agli storyboard unificato](~/ios/user-interface/storyboards/unified-storyboards.md)
