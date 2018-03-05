---
title: Introduzione a tvOS 9
description: "Questo articolo descrive tutte le nuove e modificate le API e le funzionalità disponibili in tvOS 9 per gli sviluppatori Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: c1fafe85fb7f5c30dca0dff6fe3dff5172e0b9aa
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-tvos-9"></a>Introduzione a tvOS 9

_Questo articolo descrive tutte le nuove e modificate le API e le funzionalità disponibili in tvOS 9 per gli sviluppatori Xamarin.tvOS._


Apple ha rilasciato la generazione di 4 dell'hardware Apple TV con riprogettato, abilitare touch remota, il nuovo sistema operativo tvOS (basati su iOS 9).

Per la prima volta, tvOS, verrà visualizzata la piattaforma Apple TV agli sviluppatori, che consente di creare applicazioni avanzate, coinvolgente e rilasciarli tramite l'Apple TV incorporato App Store in un processo simile all'esperienza di scrittura e il rilascio di App per iOS usando iTunes App Archivio.

Se si ha familiarità con lo sviluppo di xamarin. IOS, è necessario trovare la transizione a tvOS piuttosto semplice. La maggior parte delle funzionalità e API sono gli stessi, tuttavia, molte API comuni non sono disponibili (ad esempio WebKit). Inoltre, funziona con il con remoto Siri pone alcuni problemi di progettazione che non sono presenti nei dispositivi iOS touchscreen basato su.

Questa guida fornisce un'introduzione a tutte le nuove e modificate le API e le funzionalità disponibili in tvOS 9 per gli sviluppatori Xamarin.tvOS. Per ulteriori informazioni su tvOS, vedere Apple [sviluppo di applicazioni per il nuovo Apple TV](https://developer.apple.com/tvos/) documentazione.

<a name="Supported-and-Unsupported-Capabilities" />

## <a name="supported-and-unsupported-capabilities"></a>Funzionalità supportate e non supportate

App tvOS in esecuzione su Apple TV sono supportati gli elementi seguenti caratteristiche e funzionalità:

 - Gruppi di app
 - Modalità in background
 - Protezione dati
 - Game Center
 - Periferiche di gioco
 - iCloud
 - Acquisti in-App
 - Condivisione del keychain

Le funzionalità seguenti non sono supportate:

 - Apple Pay
 - App Sandbox
 - Domini associati
 - HealthKit
 - HomeKit
 - Audio Inter-App
 - Mappe
 - VPN personale
 - Notifiche push
 - Wallet
 - Configurazione accessori wireless

Vedere il nostro [assembly supportati](~/ios/tvos/internals/assemblies.md) e [Framework supportati](~/ios/tvos/internals/frameworks.md) documentazione per ulteriori informazioni.

<a name="Apple-TV-Hardware" />

## <a name="apple-tv-hardware"></a>Apple TV Hardware

Il nuovo televisore Apple presenta le specifiche hardware seguenti:

 - processore a 64 bit A8
 - 64GB di spazio di archiviazione o di 32GB
 - 2GB di RAM
 - 10/100 Mbps Ethernet
 - WiFi 802.11a/b/g/n/ac
 - risoluzione 1080p
 - HDMI
 - Porta C (per sviluppatori e solo per uso diagnostica)
 - Siri remoto nuove o Apple TV remoto (basato sull'area)

### <a name="siri-remote"></a>Siri remoto

In base all'area, fornito Apple TV remoto sono disponibili in entrambe le configurazioni di uno: Siri remoto o Apple TV remoto.

Siri remoto è attualmente disponibile in paesi seguenti:

 - Australia
 - Canada
 - Francia
 - Germania
 - Giappone
 - Spagna
 - Regno Unito
 - Stati Uniti

Tutti gli altri paesi riceverà l'Apple TV remoto che sostituisce il pulsante di Siri con un pulsante di ricerca che consente di visualizzare la schermata di ricerca predefinita con un input di testo per la ricerca:

[ ![](tvos9-images/remote02.png "Siri remoto")](tvos9-images/remote02.png)

Per ulteriori informazioni, vedere il nostro [Siri remoto e i controller Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentazione.

<a name="Apple-TV-Provisioning" />

## <a name="apple-tv-provisioning"></a>Il Provisioning di Apple TV

Come lo sviluppo per iOS, il nuovo tvOS richiederà il profilo di Provisioning appropriato per lo sviluppo e distribuzione in base all'appartenenza al Team e l'identità di firma che è già stato stabilito di Apple.

È inoltre necessario per accedere alle funzionalità tvOS, ad esempio iCloud KVS o archivi dati CloudKit provisioning corretto. Consultare il nostro [risorse e l'archiviazione dei dati](~/ios/tvos/app-fundamentals/resources-data-storage.md) per informazioni sul supporto iCloud nelle App Xamarin.tvOS.

I profili di provisioning vengono creati e installati nello stesso modo di lavorare con App xamarin. IOS. Di conseguenza, consultare il nostro iOS [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) documentazione per maggiori dettagli.

<a name="Apple-TV-Apps" />

## <a name="apple-tv-apps"></a>Apple App TV

Il nuovo hardware Apple TV e tvOS 9 supporta due tipi di App: tradizionali e applicazioni client-server.

<a name="Traditional-Apps" />

### <a name="traditional-apps"></a>Applicazioni tradizionali

Le applicazioni tradizionali vengono acquistate dall'App Store di Apple TV e vengono installate direttamente sul dispositivo. Queste App possono essere giochi, utilità o App multimediali sviluppati utilizzando gli stessi Framework e le tecniche come App xamarin. IOS.

App di Apple TV hanno una dimensione massima di 200MB e il download da 2GB di contenuto utilizzando le risorse su richiesta aggiuntiva. Consultare il nostro [risorse e l'archiviazione dei dati](~/ios/tvos/app-fundamentals/resources-data-storage.md) per ulteriori informazioni.

Vedere il nostro [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md) per acquisire familiarità con gli strumenti e i concetti necessari per sviluppare App tvOS utilizzando Xamarin.tvOS.

<a name="Summary" />

### <a name="client-server-apps"></a>Applicazioni client-Server

Oltre alle applicazioni tradizionali installate, Apple TV rende facile creare App usando tecnologie web (HTTPS, XML e JavaScript) dei flussi multimediali client-server basata sul web. Progettare l'interfaccia utente usando il linguaggio di markup TVML di Apple che usano JavaScript per definire i comportamenti dell'applicazione utilizzando TVMLKit.

Per ulteriori informazioni, vedere Apple [riferimenti al linguaggio di Markup di Apple TV](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064), [TVJS Framework riferimento](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076), [TVMLKit Framework riferimento](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429), [su HTTP Live Streaming](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978) e [HLS creazione specifica per Apple TV](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf) documentazione.

<a name="User-Interface-Challenges" />

## <a name="user-interface-challenges"></a>Problematiche dell'interfaccia utente

A differenza di iOS e OS X, Apple TV non dispone di un touchscreen o passare il mouse che consentono all'utente di selezionare direttamente e interagire con un'applicazione o il relativo contenuto. Invece utente remoto Siri nuova o un Controller di gioco Bluetooth per passare l'interfaccia utente di un'app. Per ulteriori informazioni, vedere il nostro [Siri remoto e i controller Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentazione.

Inoltre, l'esperienza utente complessiva è notevolmente diverso da quello di App iOS e Mac che tendono a essere esperienze utente singolo. Con Apple TV, esperienze utente tendono a essere più sociali in natura, in cui diverse persone possono essere seduti divano l'interazione con una singola applicazione e altro. Per progettare un'esperienza di app Apple TV ha esito positivo (una nuova app o il porting di quella esistente), queste modifiche devono essere presi in considerazione. 

<a name="Summary" />

### <a name="working-with-focus-and-parallax-images"></a>Utilizzo di immagini di parallasse e lo stato attivo

Come descritto in precedenza, gli utenti dell'app Xamarin.tvOS verranno non essere interazione con relativa interfaccia direttamente come con iOS in cui vengono toccati immagini sullo schermo del dispositivo, ma indirettamente da attraverso la chat remota Siri utilizzando. Per presentare e gestire l'interazione dell'utente, Apple TV utilizza un modello basato su attivo. 

Quando cambia lo stato attivo, le animazioni complessi ed effetti (ad esempio, l'effetto di parallasse sulle immagini) vengono utilizzati per identificare chiaramente l'elemento dell'interfaccia utente che ha attualmente lo stato attivo.

Se l'utente esegue un movimento lento, circolare in remoto Siri, l'elemento con stato attivo verrà sway in tempo reale in risposta a questo movimento. Si verifica l'oscillazione, viene applicata un' brillantezza illuminato alla propria immagine visualizzando l'area da utilizzare. Dopo un determinato periodo di inattività, l'elemento Focused aumenteranno anche offusca qualsiasi contenuto out-di-lo stato attivo.

Per ulteriori informazioni, vedere il nostro [utilizzo di navigazione e lo stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) e [utilizzo di immagini e icone](~/ios/tvos/app-fundamentals/icons-images.md) documentazione.

<a name="The-Home-Screen" />

### <a name="the-home-screen"></a>La schermata iniziale

La schermata Home Apple TV Mostra tutte le applicazioni che vengono installate e fornisce un modo per accedere alle preferenze dell'utente:

[ ![](tvos9-images/home01.png "La schermata iniziale")](tvos9-images/home01.png)

L'utente passa a una griglia delle icone di app con i movimenti tocco in remoto Siri utilizzando lo stato attivo per selezionare un'app e avviare il programma. L'icona dell'App è il la prima possibilità di effettuare un'ottima impressione potenziali utenti e deve comunicare scopo dell'applicazione a colpo d'occhio.

Ogni app è necessario specificare sia una piccola e una versione ingrandita della relativa icona di App. Nella schermata Home Apple TV verrà utilizzato l'icona di piccole dimensioni quando l'app viene installata. Dall'App Store viene utilizzata la versione di grandi dimensioni. Icona di grandi dimensioni App deve simulare l'aspetto della versione dell'icona piccola.

Per ulteriori informazioni, vedere il nostro [utilizzo di immagini e icone](~/ios/tvos/app-fundamentals/icons-images.md) documentazione.

<a name="The-Top-Shelf" />

### <a name="the-top-shelf"></a>Scaffale superiore

Se l'utente ha effettuato l'app Xamarin.tvOS sulla riga superiore nella schermata Home Apple TV, un'immagine di scaffale superiore grande verrà visualizzata quando l'app viene selezionato dall'utente. Questa immagine deve evidenziare le funzionalità dell'app o forniscono collegamenti diretti al relativo contenuto.

[ ![](tvos9-images/topshelf01.png "Scaffale superiore")](tvos9-images/topshelf01.png)

È possibile specificare l'immagine di scaffale superiore come singolo statico `.png` o `.lsr` file o può essere creato dinamicamente in fase di esecuzione come una singola riga di elementi con stato attivabile.

Anziché visualizzare un'immagine statica di scaffale superiore, può contenere una riga dinamica o elementi con stato attivabile o un set dinamico di intestazioni di scorrimento. Entrambi questi stile dinamica consente di evidenziare il contenuto fornito dall'applicazione o salto le funzionalità più utilizzate.

Per ulteriori informazioni, vedere il nostro [utilizzo di immagini e icone](~/ios/tvos/app-fundamentals/icons-images.md) Apple e la documentazione [TVServices Framework riferimento](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) per ulteriori informazioni sull'aggiunta di un'estensione scaffale superiore all'App per fornire contenuto dinamico scaffale superiore.






## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Creazione di applicazioni per tvOS con Xamarin (video)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
