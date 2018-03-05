---
title: Acquisti in-app
description: "le applicazioni iOS è possono vendere digitale prodotti e servizi utilizzando le API di Kit di archivio. I prodotti vengono creati e gestiti nel portale iTunes Connect. Apple gestisce l'elaborazione delle transazioni e approva tutti i prodotti prima di potere essere venduti e una tariffa per ogni transazione (30 %) attualmente). Apple richiede che si utilizza l'acquisto in-app per tutte le vendite digitale nell'app, ma non può essere utilizzato per le vendite del fisici beni o servizi non digitale. Le applicazioni che offrono opzioni di pagamento alternativo per i servizi e prodotti digitali sono probabile che vengano rifiutate. Questo documento viene illustrato come configurare l'applicazione per usare il Kit di archivio e fornisce esempi di xamarin degli scenari di acquisto in-app più comuni."
ms.topic: article
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: af8eb556215679bab2da8f54e8231f7d7d3ed418
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="in-app-purchasing"></a>Acquisti in-app

_le applicazioni iOS è possono vendere digitale prodotti e servizi utilizzando le API di Kit di archivio. I prodotti vengono creati e gestiti nel portale iTunes Connect. Apple gestisce l'elaborazione delle transazioni e approva tutti i prodotti prima di potere essere venduti e una tariffa per ogni transazione (30 %) attualmente). Apple richiede che si utilizza l'acquisto in-app per tutte le vendite digitale nell'app, ma non può essere utilizzato per le vendite del fisici beni o servizi non digitale. Le applicazioni che offrono opzioni di pagamento alternativo per i servizi e prodotti digitali sono probabile che vengano rifiutate. Questo documento viene illustrato come configurare l'applicazione per usare il Kit di archivio e fornisce esempi di xamarin degli scenari di acquisto in-app più comuni._


le applicazioni iOS possono vendere prodotti digitali o i servizi utilizzando StoreKit: un set di API fornite da iOS che comunicano con i server Apple per condurre transazioni finanziarie con l'utente tramite i relativi ID Apple. Le APIs StoreKit sono principalmente riguarda il recupero di informazioni sul prodotto e l'esecuzione di transazioni: nessun componente dell'interfaccia utente. Le applicazioni che implementano l'acquisto in-app devono compilare la propria interfaccia utente e tenere traccia degli elementi acquistati con codice personalizzato per fornire all'utente, necessario di prodotti o servizi.

La funzionalità di acquisto in-app richiede un numero di passaggi:

-  **Configurazione della tua app** : profilo di provisioning dell'applicazione deve essere configurato correttamente.
-  **La creazione di prodotti** : le descrizioni dei prodotti e i prezzi devono essere creati nel portale iTunes Connect.
-  **Implementazione StoreKit** : l'API StoreKit deve essere implementata in base ai tipi di prodotti in vendita.
-  **La creazione dell'interfaccia utente e i prodotti stessi** : devono essere implementati i prodotti, inclusi i meccanismi per tenere traccia di ogni fornitore e backup/ripristino li se appropriato.
-  **Monitoraggio di vendite e la ricezione di fondi** : utilizzare informazioni fornite da iTunes Connect per monitorare le tendenze delle vendite e rilevare il reddito.


Questo documento viene illustrato come completare tutti questi passaggi per fornire acquisti in-app con xamarin. IOS.


## <a name="requirements"></a>Requisiti

Per supportare l'acquisto In-App è necessario utilizzare xamarin. IOS 5.0 o versioni successive con Xcode 7 e versioni successive.

## <a name="contents"></a>Sommario

 * [Nozioni di base e configurazione degli acquisti in-app](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

 * [Panoramica di StoreKit e recupero di informazioni sui prodotti](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

 * [Acquisto di prodotti di consumo](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

 * [Acquisto di prodotti non di consumo](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

 * [Transazioni e verifica](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

 * [Sottoscrizioni e report](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)


## <a name="summary"></a>Riepilogo

In questo articolo è stato introdotto il concetto di acquisto all'interno dell'applicazione, descritto come configurare l'applicazione per sfruttare i vantaggi e presentati esempi di utilizzo di xamarin. IOS. È interessati:

-  **Portale di Provisioning iOS** : linee guida per l'abilitazione in-app funzionalità di acquisto.
-  **iTunes Connect** : configurazione di prodotti da vendere nell'app.
-  **Archiviare Kit** : spiegazione delle classi utilizzate per compilare funzionalità di acquisto in-app.
-  **L'app per l'acquisto di codifica** : esempi su come integrare un'app xamarin di acquisto in-app.
-  **Reporting** : panoramica delle statistiche disponibili tramite iTunes Connect.


## <a name="related-links"></a>Collegamenti correlati

- [InAppPurchaseSample](https://developer.xamarin.com/samples/StoreKit/)
- [Nella Guida per programmatori acquisto App](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [iTunes Connect Guida per sviluppatori](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [Archiviare il riferimento Framework Kit](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [Identificatori di prodotto di acquisto in-App domande e risposte](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [Nota tecnica di acquisto in-App](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [Prima di inviare il primo App Store](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [Centro risorse di App Store](https://developer.apple.com/appstore/index.html)
- [Suggerimenti per la registrazione di App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [La gestione delle applicazioni](https://developer.apple.com/appstore/resources/managing/index.html)
