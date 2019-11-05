---
title: Introduzione a iOS 12
description: Questo documento fornisce una descrizione di alto livello di alcune API di iOS 12 per cui la versione di anteprima C# di Novell fornisce associazioni.
ms.prod: xamarin
ms.assetid: 99EA7090-315D-493C-87D3-26AB73D9E1A9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/08/2018
ms.openlocfilehash: 39d954626bc9e789446e7f1deac67e2e0fca51c8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031997"
---
# <a name="introduction-to-ios-12"></a>Introduzione a iOS 12

Questo documento fornisce una descrizione di alto livello di alcune API di iOS 12 per cui la versione di anteprima C# di Novell fornisce associazioni.

Per iniziare a creare app iOS 12 con Novell, vedere la [Guida introduttiva](get-started.md)

## <a name="arkit-2arkit2md"></a>[ARKit 2](arkit2.md)

ARKit è il Framework di realtà aumentata incluso in iOS. ARKit 2 consente a più utenti di interagire tra loro in una scena di realtà aumentata, rende possibile rendere permanente gli oggetti nello spazio e tornare a tali utenti in un secondo momento e fornisce il riconoscimento e il rilevamento di immagini 2D e il riconoscimento degli oggetti 3D. iOS 12 fornisce anche l'aspetto rapido AR, un modo per eseguire il rendering dei modelli AR usdz nelle app.

## <a name="siri-shortcutssiri-shortcutsmd"></a>[Collegamenti Siri](siri-shortcuts.md)

I collegamenti Siri consentono agli sviluppatori di integrare in modo più approfondito le proprie applicazioni con Siri. Con i collegamenti Siri, gli utenti possono usare i comandi vocali per aprire il contenuto o avviare le attività in background oppure possono avviare le stesse attività usando i collegamenti che Siri suggerisce sulla schermata di blocco.

## <a name="core-ml-2coremlmd"></a>[Core ML 2](coreml.md)

Core ML 2 riduce le dimensioni dell'applicazione attraverso la quantizzazione del modello e i modelli flessibili, migliora le prestazioni delle applicazioni con una nuova API di stima di batch e usa modelli personalizzati per supportare gli anticipi in Machine Learning.

## <a name="notification-improvementsnotificationsindexmd"></a>[Miglioramenti delle notifiche](notifications/index.md)

In iOS 12, le notifiche raggruppate consentono di presentare le notifiche utente nei raggruppamenti correlati all'app o ai thread. Il testo di riepilogo fornisce ulteriori informazioni su un gruppo di notifica.

Le estensioni di contenuto delle notifiche in iOS 12 consentono interfacce utente personalizzate e pulsanti di azione dinamica.

## <a name="natural-language-frameworknatural-languagemd"></a>[Framework del linguaggio naturale](natural-language.md)

Il Framework del linguaggio naturale consente alle applicazioni di eseguire vari tipi di analisi del linguaggio. Ad esempio, è in grado di identificare parti di sintesi vocale e di determinare la lingua rappresentata da un blocco di testo.

## <a name="vision-frameworkiosplatformintroduction-to-ios11visionmd"></a>[Framework visione artificiale](~/ios/platform/introduction-to-ios11/vision.md)

Il Framework di visione include un rilevatore viso migliorato che può rilevare visi in diversi orientamenti. Inoltre, le revisioni delle richieste possono selezionare una revisione specifica dell'algoritmo del Framework di visione.

## <a name="photo-and-video-apis"></a>API Photo e video

In iOS 12, l'API di segmentazione verticale restituisce un opaco effetti verticale, ovvero una maschera lineare che delimita il primo piano dallo sfondo di un'immagine verticale ed è utile per la creazione di vari effetti immagine. iOS 12 rende inoltre possibile l'uso di dati di profondità dalla fotocamera TrueDepth per gli effetti video in tempo reale.

## <a name="passwords"></a>Password

iOS 12 rende più semplice per gli utenti e gli sviluppatori usare le password:

- Il riempimento automatico delle password e le password complesse automatiche consentono di generare, archiviare e usare automaticamente password complesse nelle applicazioni iOS durante l'iscrizione e l'accesso a un'applicazione.
- Il riempimento del codice di sicurezza consente di usare i codici di autenticazione basati su SMS senza tagliare e incollare manualmente o memorizzare.
- La classe `ASWebAuthenticationSession` semplifica il processo di utilizzo dei servizi di autenticazione federati.
- Le estensioni del provider di credenziali di riempimento automatico consentono alle applicazioni con password di terze parti di fornire nome utente e password ai campi di accesso.

## <a name="healthkit-updates"></a>Aggiornamenti HealthKit

iOS 11,3 ha introdotto i [record di integrità](https://www.apple.com/healthcare/health-records/), che consentono agli utenti di scaricare le informazioni sui record di integrità da diversi istituti sanitari e visualizzarle sui dispositivi iOS. iOS 12 aggiunge API che consentono alle applicazioni di terze parti di accedere in modo sicuro a questi dati.

## <a name="imessage-app-presentation-contexts"></a>contesti di presentazione dell'app iMessage

In iOS 12, le app iMessage supportano i contesti di presentazione che consentono alle app di essere eseguite come un'app iMessage normale o nel contesto di un effetto foto o video.

## <a name="network-framework"></a>Framework di rete

Framework di rete, lo stack di rete sottostante le API `URLSession` comunemente usate nelle applicazioni iOS, è ora disponibile come Framework autonomo, semplificando l'uso di TCP, UDP, TLS, IPv4/IPv6 e altro ancora.

## <a name="carplay"></a>CarPlay

In iOS 12, le app di terze parti possono fornire mappe e istruzioni di navigazione turn-by-turn in CarPlay usando il nuovo Framework di CarPlay.

## <a name="deprecations"></a>Elementi deprecati

Con iOS 12, Apple ha deprecato:

- OpenGL ES, [incoraggiando gli sviluppatori](https://developer.apple.com/ios/whats-new/) ad adottare metal.
- [`UIWebView`](xref:UIKit.UIWebView), [a favore di `WKWebView`](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).
