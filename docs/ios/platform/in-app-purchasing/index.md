---
title: In xamarin. IOS di acquisto in-App
description: Questo documento descrive come vendere prodotti digitali e i servizi usando le APIs StoreKit. Collega alle guide che illustrano configuration, prodotti di consumo, prodotti non di consumo, le transazioni, sottoscrizioni e altro ancora.
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 4b301c18ea0e69c818cf65b3b7df1cc8351350f5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119332"
---
# <a name="in-app-purchasing-in-xamarinios"></a>In xamarin. IOS di acquisto in-App

le applicazioni iOS è possono vendere prodotti digitali o servizi che usano StoreKit: un set di API fornite da iOS che comunicano con i server Apple per condurre transazioni finanziarie con l'utente tramite rispettivi ID Apple. APIs StoreKit sono principalmente interessati con il recupero di informazioni sui prodotti e condurre transazioni: nessun componente dell'interfaccia utente. Le applicazioni che implementano acquisti in-app è necessario compilare la propria interfaccia utente e tenere traccia di elementi acquistati con codice personalizzato per fornire all'utente i prodotti necessari o i servizi.

La funzionalità di acquisto in-app richiede diversi passaggi:

-  **Configurazione dell'app** : profilo di provisioning dell'applicazione deve essere configurato correttamente.
-  **Creazione di prodotti** – le descrizioni dei prodotti e i prezzi indicati devono essere creati nel portale di Connect di iTunes.
-  **Implementazione di StoreKit** : l'API di StoreKit deve essere implementata in base ai tipi di prodotti in vendita.
-  **Creazione dell'interfaccia utente e i prodotti autonomamente** : devono essere implementati i prodotti, tra cui meccanismi per tenere traccia di ogni acquisto e backup/ripristino li se appropriato.
-  **Vendite di monitoraggio e la ricezione di fondi** : usare le informazioni fornite da iTunes Connect per monitorare le tendenze di vendita e tenere traccia del reddito.

Questo documento illustra come completare tutti questi passaggi per garantire acquisti in-app con xamarin. IOS.

## <a name="requirements"></a>Requisiti

Per supportare acquisti In-App è necessario usare xamarin. IOS 5.0 o versione successiva con Xcode 7 e versioni successive.

## <a name="contents"></a>Sommario

 * [Nozioni di base e configurazione degli acquisti in-app](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

 * [Panoramica di StoreKit e durante il recupero delle informazioni sul prodotto](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

 * [Acquisto di prodotti di consumo](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

 * [Acquisto di prodotti non di consumo](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

 * [Transazioni e verifica](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

 * [Sottoscrizioni e report](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto il concetto di acquisti in-app, ha indicato come configurare l'applicazione possa sfruttare i vantaggi di esso e presentati esempi di uso di xamarin. IOS. È regolato da:

-  **Portale di Provisioning iOS** : linee guida per l'abilitazione di in-app acquistare funzionalità.
-  **iTunes Connect** – configurazione di prodotti per la vendita nell'app.
-  **Store Kit** : spiegazione delle classi usate per compilare le funzionalità di acquisto in-app.
-  **Creare la propria applicazione per l'acquisto** : esempi di come compilare acquisto in-app in un'app xamarin. IOS.
-  **Creazione di report** : panoramica delle statistiche disponibili tramite iTunes Connect.


## <a name="related-links"></a>Collegamenti correlati

- [InAppPurchaseSample](https://developer.xamarin.com/samples/StoreKit/)
- [Nella Guida alla programmazione di acquisto di App](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [iTunes Connect Developer Guide](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf) (Guida per sviluppatori di iTunes Connect)
- [Riferimento a Framework Kit Store](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [Identificatori di prodotto di acquisto in-App domande e risposte](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [Nota tecnica di acquisto in-App](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [L'invio della prima App Store](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [Centro risorse di App Store](https://developer.apple.com/appstore/index.html)
- [App Store Submission Tips](https://developer.apple.com/appstore/resources/submission/tips.html) (Suggerimenti per l'invio nell'App Store)
- [App Store Review Guidelines](https://developer.apple.com/appstore/resources/approval/guidelines.html) (Linee guida sulle recensioni nell'App Store)
- [La gestione delle App](https://developer.apple.com/appstore/resources/managing/index.html)
