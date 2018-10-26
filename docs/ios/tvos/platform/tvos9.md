---
title: Introduzione a tvOS 9
description: Questo articolo illustra tutte le nuove e modificate le API e funzionalità disponibili in tvOS 9 per gli sviluppatori di xamarin. tvos.
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: dda197f71b2a2ab3e0d61a838ab85d79b7a078c7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104310"
---
# <a name="introduction-to-tvos-9"></a>Introduzione a tvOS 9

_Questo articolo illustra tutte le nuove e modificate le API e funzionalità disponibili in tvOS 9 per gli sviluppatori di xamarin. tvos._

Apple ha rilasciato il 4 ° generazione dell'hardware Apple TV con riprogettato, touch-Abilita remoto, il nuovo sistema operativo tvOS (basati su iOS 9).

Per la prima volta, apre la finestra la piattaforma di Apple TV agli sviluppatori, consentendo di creare App complete e coinvolgenti e rilasciarle attraverso la Store di App predefinite di un Apple TV in un processo simile all'esperienza di scrittura e il rilascio di App per iOS usando iTunes App tvOS Store.

Se si ha familiarità con lo sviluppo di xamarin. IOS, è necessario trovare la transizione a tvOS piuttosto semplice. La maggior parte delle funzionalità e le API sono le stesse, tuttavia, molte API comuni non sono disponibili (ad esempio WebKit). Inoltre collaborando con i con il remoti per Siri pone alcune problematiche di progettazione che non sono presenti nei dispositivi iOS touchscreen basato.

Questa guida fornirà un'introduzione a tutte le nuove e modificate le API e funzionalità disponibili in tvOS 9 per gli sviluppatori di xamarin. tvos. Per altre informazioni su tvOS, vedi di Apple [sviluppo di applicazioni per il nuovo televisore Apple](https://developer.apple.com/tvos/) documentazione.

<a name="Supported-and-Unsupported-Capabilities" />

## <a name="supported-and-unsupported-capabilities"></a>Funzionalità supportate e non supportate

App tvOS in esecuzione in Apple TV hanno le seguenti supportate funzionalità e capacità:

 - Gruppi di app
 - Modalità in background
 - Protezione dati
 - Game Center
 - Periferiche di gioco
 - iCloud
 - Acquisti in-App
 - Condivisione del keychain

Non sono supportate le funzionalità seguenti:

 - Apple Pay
 - Sandbox App
 - Domini associati
 - HealthKit
 - HomeKit
 - Audio Inter-App
 - Mappe
 - VPN personale
 - Notifiche push
 - Wallet
 - Configurazione accessori wireless

Vedere la [assembly supportati](~/ios/tvos/internals/assemblies.md) e [Framework supportati](~/ios/tvos/internals/frameworks.md) per altre informazioni.

<a name="Apple-TV-Hardware" />

## <a name="apple-tv-hardware"></a>Apple TV Hardware

Il nuovo televisore Apple presenta le specifiche hardware seguenti:

 - processore a 64 bit A8
 - 32GB o a 64GB di spazio di archiviazione
 - 2GB di RAM
 - 10 o 100 Mbps Ethernet
 - 802.11a/b/g/n/ac Wi-Fi
 - risoluzione 1080p
 - HDMI
 - Porta C USB (per gli sviluppatori e solo per uso diagnostica)
 - Remoti per Siri nuove o Apple TV remoto (basato sull'area)

### <a name="siri-remote"></a>Remoti per Siri

In base alla regione, fornito Apple TV remoto sarà disponibile in entrambe le configurazioni di uno: remoti per Siri o Apple TV remoto.

Il remoti per Siri è attualmente disponibili nei paesi seguenti:

 - Australia
 - Canada
 - Francia
 - Germania
 - Giappone
 - Spagna
 - Regno Unito
 - Stati Uniti

Tutti gli altri paesi riceverà la Apple TV remoto che sostituisce il pulsante di Siri con un pulsante di ricerca che consente di visualizzare la schermata di ricerca predefinito con input di testo per la ricerca:

[![](tvos9-images/remote02.png "Remoti per Siri")](tvos9-images/remote02.png#lightbox)

Per altre informazioni, vedere la [controller Bluetooth e remoti per Siri](~/ios/tvos/platform/remote-bluetooth.md) documentazione.

<a name="Apple-TV-Provisioning" />

## <a name="apple-tv-provisioning"></a>Provisioning Apple TV

Come lo sviluppo per iOS, tvOS il nuovo richiederà il profilo di Provisioning corretto per lo sviluppo e distribuzione basate sulle identità di firma che è già stato stabilito con Apple e l'appartenenza al Team.

È inoltre necessario per accedere alle funzionalità di tvOS, ad esempio iCloud KVS o archivi dati CloudKit provisioning corretto. Vedere la [risorse di archiviazione e dati](~/ios/tvos/app-fundamentals/resources-data-storage.md) per informazioni sul supporto iCloud nelle App xamarin. tvos.

I profili di provisioning vengono creati e installati nello stesso modo di lavorare con le app xamarin. IOS. Di conseguenza, vedi di iOS [Device Provisioning](~/ios/get-started/installation/device-provisioning/index.md) informazioni più dettagliate.

<a name="Apple-TV-Apps" />

## <a name="apple-tv-apps"></a>Apple TV App

Il nuovo hardware Apple TV e tvOS 9 supporta due tipi di App: tradizionali e le app client-server.

<a name="Traditional-Apps" />

### <a name="traditional-apps"></a>App tradizionali

App tradizionali vengono acquistate i Store di App Apple TV e vengono installate direttamente nel dispositivo. Queste App possono essere giochi, utilità o le app multimediali che vengono sviluppate utilizzando gli stessi Framework e le tecniche come le app xamarin. IOS.

Le app Apple TV hanno una dimensione massima di 200MB e possono scaricare altri 2GB di contenuto usando le risorse On Demand. Vedere la [risorse di archiviazione e dati](~/ios/tvos/app-fundamentals/resources-data-storage.md) per altre informazioni.

Vedere la [App Tvos Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md) per acquisire familiarità con gli strumenti e i concetti necessari per sviluppare App tvOS con xamarin. tvos.

<a name="Summary" />

### <a name="client-server-apps"></a>App client-Server

Oltre alle App installate tradizionali, Apple TV semplifica creare i supporti client-server basata su web App usando tecnologie web (HTTPS, XML e JavaScript) di streaming. Verrà progettata l'interfaccia utente usando il linguaggio di markup TVML di Apple e usare JavaScript per definire i comportamenti dell'app usando TVMLKit.

Per altre informazioni, vedere di Apple [riferimenti al linguaggio di Markup di Apple TV](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064), [riferimento a Framework TVJS](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076), [riferimenti Framework TVMLKit](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429), [su HTTP Live Streaming](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978) e [HLS creazione specifica per Apple TV](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf) documentazione.

<a name="User-Interface-Challenges" />

## <a name="user-interface-challenges"></a>Problematiche dell'interfaccia utente

A differenza di iOS o OS X, Apple TV non è un touchscreen o il mouse che consentono all'utente di selezionare direttamente e interagire con un'app o il relativo contenuto. Invece utente il nuovo remoti per Siri o un Controller di gioco di Bluetooth per passare un'interfaccia utente di app. Per altre informazioni, vedere la [controller Bluetooth e remoti per Siri](~/ios/tvos/platform/remote-bluetooth.md) documentazione.

Inoltre, l'esperienza utente complessiva è notevolmente diverso da quello delle App iOS o Mac che tendono a essere esperienze utente singolo. Con Apple TV, esperienze utente tendono a essere più basati su social network in natura, in cui più persone possono sedere da usare sul divano l'interazione con una singola app e tra loro. Per progettare una corretta esperienza di app Apple TV (una nuova app o il porting di uno esistente), queste modifiche devono essere presi in considerazione. 

<a name="Summary" />

### <a name="working-with-focus-and-parallax-images"></a>Uso di messa a fuoco e immagini di parallasse

Come indicato in precedenza, gli utenti dell'app xamarin. tvos verranno non interagisce con relativa interfaccia direttamente con iOS in cui vengono toccati immagini sullo schermo del dispositivo, ma è indirettamente da attraversare la stanza usando il remoti per Siri. Per presentare e gestire questa interazione dell'utente, Apple TV Usa un modello basato lo stato attivo. 

In caso di modifica dello stato attivo, le animazioni sottili ed effetti (ad esempio, l'effetto di parallasse nelle immagini) vengono utilizzati per identificare chiaramente l'elemento di interfaccia utente che ha attualmente lo stato attivo.

Se l'utente esegue un'operazione lenta, circolare nel sistema remoto Siri, l'elemento con stato attivo verrà sway in tempo reale in risposta a questo movimento. Non appena si verifica il sway, un brillantezza illuminato viene applicato per la propria immagine risulterà la superficie per gli sviluppatori. Dopo un determinato periodo di inattività, qualsiasi contenuto di fuori di fuoco attenua e l'elemento attivo aumenterà anche maggiore.

Per altre informazioni, vedere la [utilizzo di navigazione e lo stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) e [utilizzo di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md) documentazione.

<a name="The-Home-Screen" />

### <a name="the-home-screen"></a>La schermata iniziale

La schermata Home Apple TV Mostra tutte le app che vengono installate e fornisce un modo per accedere alle preferenze dell'utente:

[![](tvos9-images/home01.png "La schermata iniziale")](tvos9-images/home01.png#lightbox)

L'utente si sposta una griglia di icone dell'app usando i movimenti tocco nell'elemento remoto di Siri con lo stato attivo per selezionare un'app e avviarla. Icona dell'App è il la prima impressione ideale per l'utente potenziali di possibilità e deve comunicare scopo dell'app in modo immediato.

Ogni app deve fornire un piccolo sia una versione ingrandita della relativa icona di App. Verrà utilizzata la piccola icona nella schermata di Apple TV Home quando l'app viene installata. Viene utilizzata la versione di grandi dimensioni per l'App Store. Icona dell'App di grandi dimensioni dovrà simulare l'aspetto della versione dell'icona piccola.

Per altre informazioni, vedere la [uso di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md) documentazione.

<a name="The-Top-Shelf" />

### <a name="the-top-shelf"></a>L'evidenza

Se l'utente ha effettuato l'app xamarin. tvos sulla riga superiore nella schermata Home Apple TV, un'immagine in evidenza grande verrà visualizzata quando l'app viene selezionato dall'utente. Questa immagine deve evidenziare le funzionalità dell'app o forniscono collegamenti diretti al relativo contenuto.

[![](tvos9-images/topshelf01.png "L'evidenza")](tvos9-images/topshelf01.png#lightbox)

È possibile specificare l'immagine in evidenza come un singolo valore statico `.png` o `.lsr` file oppure possono essere creata dinamicamente in fase di esecuzione come una singola riga degli elementi con stato attivabile.

Invece di visualizzare un'immagine in evidenza statico, può contenere una riga dinamica o elementi con stato attivabile o un set dinamico di scorrimento banner. Entrambi questi stile dinamici consentono evidenziare i contenuti forniti da un'app o un salto nel relativo funzionalità più utilizzate.

Per altre informazioni, vedere la [uso di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md) documentazione e Apple [riferimento a Framework TVServices](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) per altre informazioni sull'aggiunta di un'estensione dello scaffale superiore all'App per fornire contenuto dinamico di evidenza.






## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Creazione di App per tvOS con Xamarin (video)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
