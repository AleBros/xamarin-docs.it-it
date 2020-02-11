---
title: Introduzione a iOS 8
description: Con iOS 8, Apple ha fornito una vasta gamma di nuovi Framework e API per entusiasmare e deliziare gli sviluppatori. In questa guida vengono introdotte le nuove API e viene illustrato in che modo iOS 8 può trarre vantaggio da sviluppatori e utenti.
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 9353cffd924688058c1495b9258cc7f0e0ce7b82
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489206"
---
# <a name="introduction-to-ios-8"></a>Introduzione a iOS 8

_Con iOS 8, Apple ha fornito una vasta gamma di nuovi Framework e API per entusiasmare e deliziare gli sviluppatori. In questa guida vengono introdotte le nuove API e viene illustrato in che modo iOS 8 può trarre vantaggio da sviluppatori e utenti._

iOS 7 ha modificato visivamente l'intera interfaccia utente iOS rispetto a quanto previsto dagli utenti e dagli sviluppatori, direttamente dal primo sistema operativo iPhone. IOS 8 continua con questa operazione fornendo molti Framework per gli sviluppatori, consentendo agli utenti di controllare quasi tutti gli aspetti della loro vita direttamente dall'iPhone. Ad esempio, l'integrità e la idoneità possono essere analizzate con *HealthKit*, i codici di accesso sono obsoleti con l'autenticazione biometrica usando *LocalAuthentication*, le *estensioni dell'app* aprono un canale di comunicazione tra le app di terze parti e *HomeKit* consentono di trasformare l'abitazione in una casa futura. 

Se iOS 7 riguardava la delucenza degli utenti, iOS 8 si concentra sul fatto che gli sviluppatori hanno una gamma completa di questi nuovi strumenti. 

Questa guida presenta le nuove API per gli sviluppatori Xamarin.iOS.  

Sono inoltre disponibili alcune API deprecate in iOS 8, che sono descritte in dettaglio alla fine di questo documento.

## <a name="requirements"></a>Requisiti di

Per creare app iOS 8 in Visual Studio per Mac, è necessario quanto segue:

- **Xcode 7 e iOS 8 o versione successiva** : le API Xcode e iOS più recenti di Apple devono essere installate e configurate nel computer dello sviluppatore.
- **Visual Studio per Mac** : la versione più recente di Visual Studio per Mac deve essere installata e configurata nel dispositivo utente.
- **dispositivo o simulatore iOS 8** : un dispositivo iOS che esegue la versione più recente di iOS 8 per il test.

## <a name="home-and-leisure"></a>Casa e tempo libero

iOS 8 ha contribuito a piantare con forza Apple e il dispositivo iOS direttamente nel cuore della casa attraverso l'uso di HomeKit e HealthKit. In questa sezione verrà esaminato il funzionamento di questi nuovi Framework e il modo in cui possono essere integrati nell'applicazione Xamarin.iOS.

## <a name="homekit"></a>HomeKit

Il controllo dei dispositivi da iPhone non è una nuova applicazione di tecnologia; molti prodotti Connected-Home possono essere controllati tramite un'app iOS. Tuttavia, HomeKit ora esegue ulteriormente questa operazione, innalzando di livello un protocollo comune per i dispositivi di automazione domestica e rendendo disponibile un'API pubblica per determinati produttori, ad esempio iHome, Philips e Honeywell. Per l'utente, ciò significa che è possibile controllare quasi tutti gli aspetti della propria abitazione dall'interno di un'applicazione. È irrilevante capire che usano una lampadina Philips Hue o un allarme di nidificazione. Gli utenti possono anche concatenare numerosi processi Home intelligenti in "scene".

Con HomeKit, le app di terze parti e Siri possono individuare accessori e aggiungerli al database di configurazione della Home page personale, modificare e agire su questi dati e comunicare con gli accessori e i relativi servizi per eseguire un'azione.

### <a name="configuration"></a>Configurazione di

Il diagramma seguente illustra la gerarchia di base della configurazione degli accessori HomeKit:

![](introduction-to-ios8-images/image1.png "This diagram shows the basic hierarchy of the configuration of HomeKit accessories")

Per iniziare a usare HomeKit, gli sviluppatori dovranno assicurarsi che il profilo di provisioning abbia il servizio HomeKit selezionato. Apple ha anche fornito agli sviluppatori un componente aggiuntivo del simulatore HomeKit per Xcode. Questa operazione si trova nel [centro per sviluppatori Apple](https://developer.apple.com/downloads/index.action), in `Hardware IO Tools for Xcode`. 

Per altre informazioni, vedere la Guida di [HomeKit](~/ios/platform/homekit.md) .

## <a name="healthkit"></a>HealthKit

HealthKit è un Framework introdotto in iOS 8 che fornisce un archivio dati centralizzato, coordinato e sicuro per le informazioni relative allo stato di integrità. Il sistema operativo garantisce la privacy e la sicurezza delle informazioni di integrità e, con l'app per l'integrità, un dashboard per l'utente. Con l'autorizzazione dell'utente, le applicazioni possono leggere e scrivere un'ampia gamma di informazioni sull'integrità.

Per altre informazioni sull'uso di questo nell'app Xamarin.iOS, vedere la Guida [introduttiva a HealthKit](~/ios/platform/healthkit.md) .

## <a name="extending-iphone-functionality"></a>Estensione della funzionalità iPhone
Con iOS 8, agli sviluppatori viene assegnato un maggiore controllo sugli utenti che possono usare la propria app ed è stata aumentata la funzionalità per la comunicazione più aperta tra app di terze parti. Funzionalità come le estensioni delle app e il selettore documenti aprono un mondo di possibilità per l'uso delle applicazioni nell'ecosistema di Apple.

### <a name="app-extensions"></a>Estensioni delle app
Le estensioni delle app, per semplificare la comunicazione, consentono alle app di terze parti di comunicare tra loro. Per mantenere gli standard di sicurezza elevati e per mantenere l'integrità delle app in modalità sandbox, questa comunicazione non avviene direttamente tra le applicazioni. Viene invece eseguita da un' *estensione* al centro.

Il primo passaggio per la creazione di un'estensione di app consiste nel definire il punto di estensione corretto, che è importante per garantire il comportamento e la disponibilità delle API corrette. Per creare un'estensione di app in Visual Studio per Mac, aggiungerla a un'applicazione esistente aggiungendo un nuovo progetto alla soluzione.

Nella finestra di dialogo **nuovo progetto** passare **C#** a > **iOS** > **API unificata** > **Extensions**, come illustrato nella schermata seguente:

![](introduction-to-ios8-images/image2.png "Creating a new extension")

Nella finestra di dialogo nuovo progetto sono disponibili sette nuovi modelli di progetto per la creazione di estensioni di app e vengono descritti di seguito. Si noti che molte delle estensioni sono correlate ad altre nuove API in iOS, ad esempio la selezione dei documenti:

- **Azione** : consente agli sviluppatori di creare pulsanti di azione personalizzati univoci che consentono agli utenti di eseguire determinate attività
- **Tastiera personalizzata** : consente agli sviluppatori di aggiungere la propria gamma di tastiere Apple integrate aggiungendone una personalizzata. La tastiera più diffusa, Swype usa questa per portare la tastiera in iOS.
- **Selezione documento** : contiene un controller di visualizzazione selezione documento che consente agli utenti di accedere ai file all'esterno della sandbox dell'applicazione.
- **Provider di file di selezione documento** : fornisce l'archiviazione sicura per i file utilizzando la selezione documenti.
- **Modifica della foto** : consente di espandere i filtri e gli strumenti di modifica già forniti da Apple nell'applicazione foto per offrire agli utenti maggiore controllo e più opzioni per la modifica delle foto.
- **Oggi** : consente alle applicazioni di visualizzare i widget nella sezione odierna del centro notifiche.

Per altre informazioni sull'uso delle estensioni delle app in Xamarin.vedere la Guida [introduttiva alle estensioni per le app](~/ios/platform/extensions.md) .

### <a name="touch-id"></a>ID tocco

Touch ID è stato introdotto in iOS 7 come mezzo per autenticare l'utente, in modo analogo a un codice di accesso. Tuttavia, era limitato allo sblocco del dispositivo, all'uso dell'app Store, all'uso di iTunes e all'autenticazione solo del keychain di iCloud 

Sono ora disponibili due modi per usare Touch ID come meccanismo di autenticazione nelle applicazioni iOS 8 che usano l'API di autenticazione locale. Attualmente non è possibile usare l'autenticazione locale per l'autenticazione in modalità remota.

In primo luogo, consente ai servizi Keychain esistenti di usare nuovi elenchi di controllo di accesso (ACL) keychain. I dati Keychain possono essere sbloccati con l'autenticazione corretta di un'impronta digitale degli utenti.

In secondo luogo, LocalAuthentication offre due metodi per autenticare l'applicazione in locale. Gli sviluppatori devono usare `CanEvaluatePolicy` per determinare se il dispositivo è in grado di accettare l'ID tocco, quindi `EvaluatePolicy` per avviare l'operazione di autenticazione.

Per altre informazioni su Touch ID e per informazioni su come integrarlo in un'applicazione Xamarin.iOS, vedere l'articolo relativo a [Touch ID e Face ID nelle guide di Xamarin.iOS](~/ios/platform/touch-id-face-id.md) .

### <a name="document-picker"></a>Selezione documenti

Selezione documenti funziona con un'unità iCloud utenti per consentire all'utente di aprire i file che sono stati creati in un'altra app, importarli e modificarli ed esportarli nuovamente. In questo modo viene creato un flusso di lavoro intuitivo e, di conseguenza, un'esperienza molto migliore per gli utenti. la sincronizzazione di iCloud riprende questo passaggio. tutte le modifiche apportate in un'applicazione verranno inoltre riflesse in modo coerente in tutti i dispositivi.

Per informazioni più dettagliate sulla selezione dei documenti e per informazioni su come integrarla in un'applicazione Xamarin.iOS, vedere la Guida [introduttiva alla selezione dei documenti](~/ios/platform/document-picker.md) .

### <a name="handoff"></a>Handoff

La consegna, che fa parte della funzionalità di continuità più ampia, esegue un ulteriore passaggio per l'integrazione di OS X e iOS. Questo include l'uso di più piattaforme, la possibilità di eseguire chiamate iPhone, SMS sull'iPad e Mac e i miglioramenti apportati al tethering dall'iPhone.

La continuità funziona con iOS 8 e Yosemite e richiede l'accesso di un account iCloud a tutti i dispositivi che si desidera utilizzare. Dovrebbe funzionare con la maggior parte delle app Apple preinstallate, tra cui Safari, iWork, Maps, calendari e contatti.

Per altre informazioni, [vedere la guida](~/ios/platform/handoff.md) alla consegna.

## <a name="unified-storyboards"></a>Storyboard unificati
iOS 8 include un nuovo meccanismo più semplice da usare per la creazione dell'interfaccia utente, lo storyboard unificato. Con un singolo storyboard per coprire tutte le diverse dimensioni dello schermo hardware, è possibile creare visualizzazioni rapide e reattive in un vero e proprio tipo di "progettazione, utilizzo di molti".

Prima di iOS 8, gli sviluppatori usavano `UIInterfaceOrientation` per distinguere le modalità verticale e orizzontale e `UIInterfaceIdiom` per distinguere i dispositivi iOS. In iOS 8 non è più necessario creare storyboard distinti per i dispositivi iPhone e iPad: l'orientamento e il dispositivo vengono determinati tramite *le classi di dimensioni*.

Ogni dispositivo è definito da una classe di dimensioni, sia nell'asse verticale che orizzontale, ed esistono due tipi di classi di dimensioni in iOS 8:

- **Normale** : si tratta di una dimensione dello schermo grande (ad esempio un iPad) o di un gadget che offre un'impressione di grandi dimensioni, ad esempio un UIScrollView
- **Compatta** : si tratta di dispositivi più piccoli, ad esempio l'iPhone. Questa dimensione prende in considerazione l'orientamento del dispositivo.

Se i due concetti vengono usati insieme, il risultato è una griglia 2 x 2 che definisce le diverse dimensioni possibili che possono essere usate in entrambi gli orientamenti diversi, come illustrato nel diagramma seguente:

![](introduction-to-ios8-images/image3.png "A diagram representing the 2 x 2 grid that defines the different possible sizes that can be used in both the differing orientations")

Per ulteriori informazioni sulle classi di dimensioni, vedere l' [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md).

## <a name="photo-kit"></a>Kit foto
Photo kit è un nuovo Framework che consente alle applicazioni di eseguire query nella libreria di immagini del sistema e creare interfacce utente personalizzate per visualizzarne e modificarne il contenuto. Include varie classi che rappresentano asset di immagini e video, nonché raccolte di asset come album e cartelle.

Per altre informazioni, vedere la Guida di [PhotoKit](~/ios/platform/photokit.md) .

## <a name="games"></a>Giochi

<a name="scenekit" />

### <a name="scene-kit"></a>Kit scene

Scene kit è un'API Graph della scena 3D che semplifica l'uso della grafica 3D. È stata introdotta per la prima volta in OS X 10,8 ed è ora disponibile in iOS 8. Con scene kit creazione di visualizzazioni 3D immersive e giochi 3D casuali non richiede esperienza in OpenGL. Basandosi sui concetti comuni relativi a Graph scene, il kit della scena astrae le complessità di OpenGL e OpenGL, semplificando l'aggiunta di contenuto 3D a un'applicazione. Tuttavia, se si è un esperto di OpenGL, il kit di scene offre un ottimo supporto per la legatura diretta con OpenGL. Include inoltre numerose funzionalità che integrano grafica 3D, ad esempio la fisica, e si integra molto bene con diversi altri framework Apple, ad esempio Core Animation, Core Image e sprite Kit.

Per ulteriori informazioni, consultare la documentazione di [SceneKit](~/ios/platform/gaming/scenekit.md) .

<a name="spritekit" />

### <a name="sprite-kit"></a>Kit sprite

Sprite Kit, il Framework di gioco 2D di Apple, presenta alcune nuove interessanti funzionalità in iOS 8 e OS X Yosemite. Sono inclusi l'integrazione con la scenografia, il supporto dello shader, l'illuminazione, le ombre, i vincoli, la generazione di mappe normali e i miglioramenti della fisica. In particolare, le nuove funzionalità di fisica facilitano l'aggiunta di effetti realistici a un gioco.

Per ulteriori informazioni, consultare la documentazione di [SpriteKit](~/ios/platform/gaming/spritekit.md) .

## <a name="other-changes"></a>Altre modifiche
Così come le principali modifiche apportate in iOS 8 descritte in precedenza, Apple ha aggiornato anche molti Framework esistenti. Queste informazioni sono descritte di seguito:

- **[Immagine di base](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)** : Apple si è espanso al suo Framework di elaborazione delle immagini aggiungendo un supporto migliore per il rilevamento di aree rettangolari e codici a matrice all'interno delle immagini. Mike Bluestein lo Esplora nel post di Blog relativo al [rilevamento delle immagini in iOS 8](https://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>API deprecate
Con tutti i miglioramenti apportati in iOS 8, diverse API sono state deprecate. Di seguito sono riportate alcune di queste informazioni.

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)** : i metodi e le proprietà usati per la registrazione delle notifiche remote sono deprecati. Si tratta di registerForRemoteNotificationTypes e enabledRemoteNotificationTypes.
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)** : le classi di tratti e dimensioni hanno sostituito i metodi e le proprietà usati per descrivere l'orientamento dell'interfaccia. Per ulteriori informazioni sull'utilizzo di questi, vedere l' [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md) .

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)** : questa operazione è stata sostituita da UISearchController in iOS 8.

## <a name="summary"></a>Riepilogo
In questo articolo sono state esaminate alcune delle nuove funzionalità introdotte da Apple in iOS 8.

## <a name="related-links"></a>Collegamenti correlati

- [UIKitEnhancements (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-uikitenhancements)
- [Introduzione alle estensioni dell'app](~/ios/platform/extensions.md)
- [Introduction to CloudKit](~/ios/data-cloud/intro-to-cloudkit.md) (Introduzione a CloudKit)
- [Introduzione a selezione documenti](~/ios/platform/document-picker.md)
- [Introduzione a HealthKit](~/ios/platform/healthkit.md)
- [Introduzione ai controlli della fotocamera manuale](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [Touch ID e Face ID con Xamarin.iOS](~/ios/platform/touch-id-face-id.md)
- [Introduzione agli storyboard unificati](~/ios/user-interface/storyboards/unified-storyboards.md)
