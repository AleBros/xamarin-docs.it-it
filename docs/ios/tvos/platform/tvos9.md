---
title: Introduzione a tvOS 9
description: Questo articolo presenta tutte le API e le funzionalità nuove e modificate disponibili in tvOS 9 per gli sviluppatori Novell. tvOS.
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/07/2016
ms.openlocfilehash: ecf5a7cabb03cea92075127d4d5e87350f45619e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769113"
---
# <a name="introduction-to-tvos-9"></a>Introduzione a tvOS 9

_Questo articolo presenta tutte le API e le funzionalità nuove e modificate disponibili in tvOS 9 per gli sviluppatori Novell. tvOS._

Apple ha rilasciato la quarta generazione dell'hardware Apple TV con una riprogettata, abilitata per il tocco in remoto, che esegue il nuovo sistema operativo tvOS (basato su iOS 9).

Per la prima volta, tvOS apre la piattaforma Apple TV allo sviluppatore, consentendo di creare app complete e immersive e di rilasciarle tramite l'App Store di Apple TV incorporata in un processo simile all'esperienza di scrittura e rilascio di app per iOS con l'app iTunes Negozio.

Se si ha familiarità con lo sviluppo di Novell. iOS, si dovrebbe trovare la transizione a tvOS abbastanza semplice. La maggior parte delle API e delle funzionalità sono le stesse, ma molte API comuni non sono disponibili, ad esempio WebKit. Inoltre, l'utilizzo del con Siri Remote pone alcune difficoltà di progettazione che non sono presenti nei dispositivi iOS basati su touchscreen.

Questa guida offre un'introduzione a tutte le API nuove e modificate disponibili in tvOS 9 per gli sviluppatori Novell. tvOS. Per altre informazioni su tvOS, vedere la pagina relativa allo sviluppo di Apple [per la nuova documentazione di Apple TV](https://developer.apple.com/tvos/) .

<a name="Supported-and-Unsupported-Capabilities" />

## <a name="supported-and-unsupported-capabilities"></a>Funzionalità supportate e non supportate

le app tvOS in esecuzione in Apple TV hanno le funzionalità e le funzionalità supportate seguenti:

- Gruppi di app
- Modalità in background
- Protezione dati
- Game Center
- Controller di gioco
- iCloud
- Acquisti in-app
- Condivisione del keychain

Le funzionalità e le funzionalità seguenti non sono supportate:

- Apple Pay
- Sandbox app
- Domini associati
- HealthKit
- HomeKit
- Audio Inter-App
- Mappe
- VPN personale
- Notifiche push
- Wallet
- Configurazione accessori wireless

Per ulteriori informazioni, vedere la documentazione degli [assembly supportati](~/ios/tvos/internals/assemblies.md) e dei [Framework supportati](~/ios/tvos/internals/frameworks.md) .

<a name="Apple-TV-Hardware" />

## <a name="apple-tv-hardware"></a>Hardware Apple TV

Il nuovo Apple TV presenta le specifiche hardware seguenti:

- processore A8 a 64 bit
- 32GB o 64 GB di spazio di archiviazione
- 2 GB di RAM
- Ethernet 10/100 Mbps
- Wi-Fi 802.11 a/b/g/n/AC
- risoluzione 1080p
- HDMI
- Porta USB C (solo per uso di sviluppatori e diagnostica)
- Nuovo Siri remote o Apple TV Remote (in base all'area geografica)

### <a name="siri-remote"></a>Siri remoto

In base all'area, il controllo remoto Apple TV disponibile verrà fornito in una delle due configurazioni seguenti: Siri remoto o Apple TV remoto.

Siri Remote è attualmente disponibile nei paesi seguenti:

- Australia
- Canada
- Francia
- Germania
- Giappone
- Spagna
- Regno Unito
- Stati Uniti

Tutti gli altri paesi riceveranno il telecomando Apple TV che sostituisce il pulsante Siri con un pulsante di ricerca che visualizza la schermata di ricerca predefinita con input di testo per la ricerca:

[![](tvos9-images/remote02.png "Siri remoto")](tvos9-images/remote02.png#lightbox)

Per ulteriori informazioni, vedere la documentazione di [Siri Remote and Bluetooth Controllers](~/ios/tvos/platform/remote-bluetooth.md) .

<a name="Apple-TV-Provisioning" />

## <a name="apple-tv-provisioning"></a>Provisioning di Apple TV

Proprio come lo sviluppo per iOS, la nuova tvOS richiederà il profilo di provisioning appropriato sia per lo sviluppo che per la distribuzione, in base all'appartenenza al team e alle identità di firma già stabilite con Apple.

Il provisioning appropriato è necessario anche per accedere alle funzionalità di tvOS, ad esempio gli archivi dati iCloud KVS o CloudKit. Per informazioni sul supporto di iCloud nelle app Novell. tvOS, vedere le [risorse e l'archivio dati](~/ios/tvos/app-fundamentals/resources-data-storage.md) .

I profili di provisioning vengono creati e installati nello stesso modo in cui funzionano con le app Novell. iOS. Per ulteriori informazioni, vedere la documentazione relativa al [provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) iOS.

<a name="Apple-TV-Apps" />

## <a name="apple-tv-apps"></a>App Apple TV

Il nuovo hardware Apple TV e tvOS 9 supportano due tipi di app: app tradizionali e client-server.

<a name="Traditional-Apps" />

### <a name="traditional-apps"></a>App tradizionali

Le app tradizionali vengono acquistate dall'App Store di Apple TV e sono installate direttamente nel dispositivo. Queste app possono essere giochi, utilità o app multimediali sviluppate usando gli stessi Framework e tecniche delle app Novell. iOS.

Le app Apple TV hanno una dimensione massima di 200 MB e possono scaricare altri 2 GB di contenuti usando risorse su richiesta. Per ulteriori informazioni, vedere le [risorse e l'archivio dati](~/ios/tvos/app-fundamentals/resources-data-storage.md) .

Vedere il [Guida introduttiva Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md) per acquisire familiarità con gli strumenti e i concetti necessari per sviluppare app TvOS con Novell. tvOS.

<a name="Summary" />

### <a name="client-server-apps"></a>App client-server

Oltre alle app tradizionali installate, Apple TV semplifica la creazione di app di streaming multimediale client-server basate sul Web usando le tecnologie Web (HTTPS, XML e JavaScript). L'interfaccia utente viene progettata usando il linguaggio di markup TVML di Apple e viene usato JavaScript per definire i comportamenti dell'app usando TVMLKit.

Per altre informazioni, vedere le informazioni di riferimento sul [linguaggio di markup Apple TV](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064), informazioni di riferimento sul [Framework di TVJS](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076), informazioni di riferimento sul [framework TVMLKit](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429), [informazioni sulle http live streaming](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978) e [la specifica di authoring HLS per Apple TV](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf) documentazione.

<a name="User-Interface-Challenges" />

## <a name="user-interface-challenges"></a>Problemi relativi all'interfaccia utente

A differenza di iOS o OS X, Apple TV non dispone di touchscreen o mouse che consentono all'utente di selezionare e interagire direttamente con un'app o il relativo contenuto. Per spostarsi nell'interfaccia utente di un'app è invece necessario usare il nuovo controller di gioco di Siri remote o di Bluetooth. Per ulteriori informazioni, vedere la documentazione di [Siri Remote and Bluetooth Controllers](~/ios/tvos/platform/remote-bluetooth.md) .

Inoltre, l'esperienza utente complessiva è notevolmente diversa rispetto alle app iOS o Mac che tendono a essere esperienze utente singole. Con la TV Apple, le esperienze degli utenti tendono a essere più sociali, in cui è possibile che più persone si trovino sul divano che interagisce con una singola app. Per progettare un'esperienza di app Apple TV corretta, ovvero una nuova app o il porting di un'app esistente, è necessario prendere in considerazione queste modifiche. 

<a name="Summary" />

### <a name="working-with-focus-and-parallax-images"></a>Uso delle immagini di stato attivo e parallasse

Come indicato in precedenza, gli utenti dell'app Novell. tvOS non interagiranno con l'interfaccia direttamente come con iOS, in cui toccano le immagini sullo schermo del dispositivo, ma indirettamente da tutta la stanza usando Siri remote. Per presentare e gestire questa interazione con l'utente, Apple TV usa un modello basato sullo stato attivo. 

Quando si modifica lo stato attivo, vengono usate animazioni e effetti delicati, ad esempio l'effetto di parallasse sulle immagini, per identificare chiaramente l'elemento dell'interfaccia utente che attualmente ha lo stato attivo.

Se l'utente esegue un movimento lento e circolare sul Siri remoto, l'elemento con lo stato attivo verrà influenzato in tempo reale in risposta a questo movimento. Quando si verifica il Sway, viene applicata una lucentezza illuminata all'immagine che rende visibile la superficie. Dopo un determinato periodo di inattività, qualsiasi contenuto fuori dallo stato di inattività viene offuscato e l'elemento con lo stato attivo aumenta ulteriormente.

Per altre informazioni, vedere l' [argomento relativo all'uso di navigazione e messa a fuoco](~/ios/tvos/app-fundamentals/navigation-focus.md) e all'uso della documentazione relativa a [icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md) .

<a name="The-Home-Screen" />

### <a name="the-home-screen"></a>Schermata iniziale

La schermata iniziale di Apple TV Mostra tutte le app installate e fornisce un modo per accedere alle preferenze dell'utente:

[![](tvos9-images/home01.png "Schermata iniziale")](tvos9-images/home01.png#lightbox)

L'utente Esplora una griglia di icone dell'app usando i movimenti tocco sulla finestra remota Siri con lo stato attivo per selezionare un'app e avviarla. L'icona dell'app è la prima possibilità di creare una grande impressione sul potenziale utente e deve comunicare a colpo d'occhio lo scopo dell'app.

Ogni app deve fornire una versione di piccole e grandi dimensioni dell'icona dell'app. L'icona piccola verrà usata nella schermata iniziale di Apple TV quando viene installata l'app. La versione di grandi dimensioni viene usata dall'App Store. L'icona dell'app di grandi dimensioni dovrebbe simulare l'aspetto della versione dell'icona piccola.

Per ulteriori informazioni, vedere la documentazione relativa [all'utilizzo di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md) .

<a name="The-Top-Shelf" />

### <a name="the-top-shelf"></a>Scaffale superiore

Se l'utente ha inserito l'app Novell. tvOS nella parte superiore della schermata iniziale di Apple TV, viene visualizzata un'immagine di grandi dimensioni superiore quando l'utente seleziona l'app. Questa immagine dovrebbe evidenziare le funzionalità dell'app o fornire collegamenti diretti al relativo contenuto.

[![](tvos9-images/topshelf01.png "Scaffale superiore")](tvos9-images/topshelf01.png#lightbox)

L'immagine in primo piano può essere specificata come un singolo file `.png` o `.lsr` statico oppure può essere creata dinamicamente in fase di esecuzione come una singola riga di elementi attivabili.

Invece di visualizzare un'immagine in primo piano statica, può contenere una riga dinamica o elementi attivabili o un set dinamico di banner di scorrimento. Entrambi questi stili dinamici consentono di evidenziare il contenuto fornito dall'app o di passare alle funzionalità più usate.

Per altre informazioni, vedere la documentazione relativa all' [uso di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md) e informazioni di [riferimento su TVServices Framework](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) di Apple per altre informazioni sull'aggiunta di un'estensione di scaffale principale all'app per fornire contenuti dinamici di primo piano.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Creazione di app per tvOS con Novell (video)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
