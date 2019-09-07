---
title: Acquisto in-app in Novell. iOS
description: Questo documento descrive come vendere prodotti e servizi digitali usando le API di StoreKit. Sono disponibili collegamenti a guide che illustrano la configurazione, i prodotti utilizzabili, i prodotti non utilizzabili, le transazioni, le sottoscrizioni e altro ancora.
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: f0cc04896b5c6e9b620b24c84d5c9d2a09b9cf4a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752631"
---
# <a name="in-app-purchasing-in-xamarinios"></a>Acquisto in-app in Novell. iOS

le applicazioni iOS possono vendere prodotti o servizi digitali usando StoreKit, un set di API fornite da iOS che comunicano con i server Apple per condurre transazioni finanziarie con l'utente tramite il proprio ID Apple. Le API StoreKit riguardano principalmente il recupero delle informazioni sul prodotto e l'esecuzione di transazioni. non esiste alcun componente dell'interfaccia utente. Le applicazioni che implementano l'acquisto in-app devono compilare la propria interfaccia utente e tenere traccia degli elementi acquistati con codice personalizzato per fornire all'utente i prodotti o i servizi richiesti.

Per fornire funzionalità di acquisto in-app, è necessario eseguire una serie di passaggi:

- **Configurazione dell'app** : il profilo di provisioning dell'applicazione deve essere configurato correttamente.
- **Creazione di prodotti** : le descrizioni e i prezzi dei prodotti devono essere creati nel portale di iTunes Connect.
- **Implementazione di StoreKit** : l'API StoreKit deve essere implementata in base ai tipi di prodotti venduti.
- **Compilazione dell'interfaccia utente e dei prodotti stessi** : i prodotti devono essere implementati, inclusi i meccanismi per tenere traccia di ogni acquisto e backup/ripristino, se appropriato.
- **Monitoraggio delle vendite e della ricezione dei fondi** : usare le informazioni fornite da iTunes Connect per monitorare le tendenze di vendita e tenere traccia del reddito.

Questo documento illustra come eseguire tutti questi passaggi per fornire acquisti in-app con Novell. iOS.

## <a name="requirements"></a>Requisiti

Per supportare l'acquisto in-app, è necessario usare Novell. iOS 5,0 o versione successiva con Xcode 7 e versioni successive.

## <a name="contents"></a>Sommario

- [Nozioni di base e configurazione degli acquisti in-app](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

- [Panoramica di StoreKit e recupero di informazioni sul prodotto](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

- [Acquisto di prodotti di consumo](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

- [Acquisto di prodotti non di consumo](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

- [Transazioni e verifica](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

- [Sottoscrizioni e report](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto il concetto di acquisto in-app, descritto come configurare l'applicazione per sfruttarne i vantaggi ed esempi presentati con Novell. iOS. È stato analizzato:

- **portale di provisioning iOS** : linee guida per abilitare la funzionalità di acquisto in-app.
- **iTunes Connect** : configurazione dei prodotti da vendere nell'app.
- **Store Kit** : spiegazione delle classi usate per compilare le funzionalità di acquisto in-app.
- **Codifica dell'app per l'acquisto** : esempi di come creare un acquisto in-app in un'app Novell. iOS.
- **Creazione di report** : Panoramica delle statistiche disponibili tramite iTunes Connect.

## <a name="related-links"></a>Collegamenti correlati

- [InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit/)
- [Nella Guida alla programmazione dell'acquisto di app](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [iTunes Connect Developer Guide](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf) (Guida per sviluppatori di iTunes Connect)
- [Informazioni di riferimento sul Framework di Store Kit](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [Identificatori del prodotto di acquisto in-app Q & A](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [Nota tecnica di acquisto in-app](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [Invio del primo App Store](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [Centro risorse App Store](https://developer.apple.com/appstore/index.html)
- [App Store Submission Tips](https://developer.apple.com/appstore/resources/submission/tips.html) (Suggerimenti per l'invio nell'App Store)
- [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) (Linee guida sulle recensioni nell'App Store)
- [Gestione delle app](https://developer.apple.com/appstore/resources/managing/index.html)
