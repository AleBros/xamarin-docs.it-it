---
title: Introduzione a iOS 12
description: Questo documento fornisce una descrizione dettagliata di alcune API di iOS 12 per l'anteprima di Xamarin, quale versione fornisce le associazioni C#.
ms.prod: xamarin
ms.assetid: 99EA7090-315D-493C-87D3-26AB73D9E1A9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/08/2018
ms.openlocfilehash: 99f2b98614c2b8d558dd8744b31a62b787fc955c
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233835"
---
# <a name="introduction-to-ios-12"></a>Introduzione a iOS 12

Questo documento fornisce una descrizione dettagliata di alcune API di iOS 12 per l'anteprima di Xamarin, quale versione fornisce le associazioni C#.

Per iniziare a creare App iOS 12 con Xamarin, vedere il [Guida introduttiva](get-started.md)

## <a name="arkit-2arkit2md"></a>[ARKit 2](arkit2.md)

ARKit è il framework di realtà aumentata incluso con iOS. ARKit 2 consente a più utenti di interagire tra loro in una scena di realtà aumentata, consente di rendere persistenti gli oggetti nello spazio e tornare alla loro in un secondo momento e il riconoscimento di immagini 2D e 3D e rilevamento di riconoscimento dell'oggetto. iOS 12 fornisce inoltre AR Quick Look, un modo per eseguire il rendering usdz modelli AR nelle tue app.

## <a name="siri-shortcutssiri-shortcutsmd"></a>[Tasti di scelta rapida Siri](siri-shortcuts.md)

Tasti di scelta rapida Siri consentono agli sviluppatori di integrare in modo più approfondito le proprie applicazioni con Siri. Gli utenti possono usare i comandi vocali per aprire il contenuto o avviare le attività in background con tasti di scelta rapida di Siri, oppure può avviare queste attività tramite i tasti di scelta rapida che viene suggerito Siri nella schermata di blocco.

## <a name="core-ml-2coremlmd"></a>[Core ML 2](coreml.md)

ML Core 2 consente di ridurre le dimensioni dell'applicazione tramite la quantizzazione di modello e i modelli flessibile, migliora le prestazioni dell'applicazione con una nuova API di stima batch e utilizza i modelli personalizzati per supportare i progressi nell'apprendimento automatico.

## <a name="notification-improvementsnotificationsindexmd"></a>[Miglioramenti delle notifiche](notifications/index.md)

In iOS 12, notifiche raggruppate rendono possibile per le notifiche utente presente nell'app o raggruppamenti correlati al thread. Testo di riepilogo fornisce ulteriori informazioni su un gruppo di notifica.

Le estensioni di contenuto di notifica in iOS 12 consentono le interfacce utente personalizzate e i pulsanti di azione dinamici.

## <a name="natural-language-frameworknatural-languagemd"></a>[Framework di linguaggio naturale](natural-language.md)

Il framework del linguaggio naturale consente alle applicazioni di eseguire vari tipi di analisi della lingua. Ad esempio, può identificare le parti del discorso e determinare la lingua rappresentata da un blocco di testo.

## <a name="vision-framework"></a>Framework Vision

Il framework Vision include un rilevamento volti migliorate che consentono di rilevare i visi in vari orientamenti. Inoltre, le revisioni di richiesta possono selezionare specifici visione framework algoritmo revisione.

## <a name="photo-and-video-apis"></a>API video e foto

In iOS 12, la segmentazione verticale API restituisce un alone di effetti verticale – una maschera lineare che delinea il primo piano dallo sfondo di un'immagine verticale ed è utile per creare vari effetti delle immagini. iOS 12 rende inoltre è possibile usare i dati di profondità tra la camera TrueDepth per gli effetti video in tempo reale.

## <a name="passwords"></a>Password

iOS 12 rende più semplice per gli utenti e agli sviluppatori di lavorare con le password:

- Riempimento automatico di password complesse e automatici rendono possibile la automaticamente generare, archiviare e utilizzare password complesse nelle applicazioni iOS quando si effettua l'iscrizione e accesso a un'applicazione.
- Riempimento automatico di codice di sicurezza rende possibile utilizzare i codici di autenticazione basata su SMS senza il cutting manuale e incollare oppure la memorizzazione.
- Il `ASWebAuthenticationSession` classe semplifica il processo di uso dei servizi di autenticazione federata.
- Le estensioni di Provider di credenziali di riempimento automatico rendono possibili per le applicazioni di terze parti password fornire nome utente e password per i campi di accesso.

## <a name="healthkit-updates"></a>Aggiornamenti di HealthKit

iOS 11.3 introdotte [cliniche](https://www.apple.com/healthcare/health-records/), che consente agli utenti di scaricare l'integrità di registrare le informazioni da diverse istituzioni di assistenza sanitarie e visualizzarlo nei dispositivi iOS. iOS 12 aggiunge le API che consentono alle applicazioni di terze parti di accedere in modo sicuro i dati.

## <a name="imessage-app-presentation-contexts"></a>per IMessage per contesti di presentazione di App

In iOS 12, le App per iMessage supportano contesti di presentazione, che consentono l'esecuzione come app iMessage normale o nel contesto di una foto o video effetto delle app.

## <a name="network-framework"></a>Framework di rete

Framework di rete, nella rete stack sottostante il `URLSession` API comunemente usate nelle applicazioni iOS, è ora disponibile come framework autonomi, rendendo più facile lavorare con TCP, UDP, TLS, IPv4 e IPv6 e altro ancora.

## <a name="carplay"></a>CarPlay

In iOS 12, le app di terze parti possono fornire mappe e le istruzioni di navigazione da attiva-attiva in CarPlay usando il nuovo framework CarPlay.

## <a name="deprecations"></a>Deprecati

Con 12 iOS, Apple è deprecato:

- OpenGL ES [incoraggiano gli sviluppatori](https://developer.apple.com/ios/whats-new/) adottare bare Metal.
- [`UIWebView`](xref:UIKit.UIWebView), [favore del `WKWebView` ](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

## <a name="related-links"></a>Collegamenti correlati

- [Get Ready per iOS 12 (Apple)](https://developer.apple.com/ios/)
