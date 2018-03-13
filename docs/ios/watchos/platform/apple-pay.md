---
title: Apple Pay su watchOS
description: Questo articolo descrive i miglioramenti Apple ha apportato alla Apple Pay nelle watchOS 3 e come implementarli in xamarin. IOS per Apple Watch.
ms.topic: article
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 53c2c3e3866cb782eea0cc9514c0c35379e48b5c
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="apple-pay-on-watchos"></a>Apple Pay su watchOS

Apple ha apportato numerosi miglioramenti a Apple Pay watchOS 3 che aggiunge il supporto per i pagamenti In App. Ciò consente all'utente in modo sicuro pagamento forniscono informazioni di contatto e pagare fisici beni e servizi direttamente l'Apple Watch.


## <a name="about-apple-pay-enhancements"></a>Sui miglioramenti apportati alla retribuzione di Apple

Come Stated sopra, Apple ha migliorato notevolmente pagare Apple in watchOS 3 che consentono di pagamento sicuro e informazioni per pagare direttamente l'Apple Watch fisici beni e servizi di contatto. Questi miglioramenti sono forniti da modifiche al framework PassKit.

IOS 10 e watchOS 3, diverse nuove API sono state aggiunte per l'utilizzo in iOS e watchOS per supportare le reti a pagamento dinamico e un nuovo ambiente di test di sandbox.

## <a name="passkit-framework-enhancements"></a>Miglioramenti apportati a Framework PassKit

In iOS 10, il framework PassKit è stato ampliato per supportare Apple Pay all'esterno di `UIKit` e per consentire di autorità emittenti di smart card presentare le proprie schede all'interno delle app. 

### <a name="supporting-apple-pay-outside-of-uikit"></a>Supporto di pagamento in base di fuori di UIKit Apple

Utilizzando [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), un'applicazione può supportare le stesse funzionalità fornite da [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) senza utilizzare UIKit. Anche se questa nuova API è necessaria per supportare Apple Pay l'Apple Watch (e in specifici scopi nonché), è facoltativo negli altri casi (ad esempio le app esistenti). Tuttavia, Apple suggerisce lo spostamento alla nuova API appena possibile per fornire un ampio supporto Apple Pay in tutte le app dello sviluppatore con una sola codebase. Per ulteriori informazioni sui tipi e l'integrazione di Siri, consultare il nostro [Introduzione a SiriKit](~/ios/platform/sirikit/index.md) documentazione.

### <a name="presenting-issuer-cards-from-within-apps"></a>Presentazione delle schede dell'autorità di certificazione da all'interno di applicazioni

IOS 10 e watchOS 3, aggiunti nuove funzionalità del Framework PassKit che consentono di autorità emittenti di smart card presentare le proprie schede di pagamento all'interno delle proprie applicazioni. Lo sviluppatore può aggiungere un `PKPaymentButtonTypeInStore` UIButton all'interfaccia utente dell'applicazione che verrà visualizzato un pulsante Apple Pay per una scheda.

Il `PresentPaymentPass` metodo il [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) classe può essere utilizzata anche a livello di codice verrà visualizzata la scheda.

## <a name="new-payment-network-support"></a>Nuovo supporto di rete di pagamento

Nuovo iOS 10 e watchOS 3, un'applicazione può supportare automaticamente una nuova rete di pagamento quando diventa disponibile senza dover modificare, ricompilare l'app e inviare di nuovo all'archivio di App che lo sviluppatore.

Il nuovo [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) metodo la `PKPaymentNetwork` classe consente a un'app individuare le reti disponibili nel dispositivo dell'utente in fase di esecuzione. Inoltre, il [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) proprietà è stata ampliata per richiedere il nome del provider di pagamento come argomento. Utilizzo di questi metodi, un'applicazione può supportare automaticamente qualsiasi rete che supporta il provider di pagamento.

Per ulteriori informazioni, vedere il nostro [configurazione pagare Apple](~/ios/platform/apple-pay.md) e Apple [Guida di Apple pagare](https://developer.apple.com/apple-pay/).

## <a name="new-testing-environment"></a>Nuovo ambiente di Testing

IOS 10 e watchOS 3, Apple ha introdotto un nuovo ambiente di testing che consente allo sviluppatore di eseguire il provisioning di schede di pagamento di test direttamente in un dispositivo iOS. Questo nuovo ambiente di testing restituisce quindi i dati di pagamento prova crittografato all'app.

Per abilitare il nuovo ambiente di test, eseguire le operazioni seguenti:

1. Creare un nuovo iCloud test Account in iTunes Connect.
2. Accedere al dispositivo iOS con il nuovo account di test.
3. Impostare la regione desiderata per testare l'app in.
4. Utilizzare una delle schede di pagamento di test dal [Guida di Apple pagare](https://developer.apple.com/apple-pay/) per effettuare i pagamenti.

> ⚠️ **Nota:** passando l'account iCloud, il dispositivo passerà automaticamente al nuovo ambiente di testing. Tuttavia, è ancora Apple **richiede** schede dell'applicazione da testare con reale in un ambiente di produzione prima dell'invio a iTunes App Store.

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati i miglioramenti Apple ha apportato alla Apple Pay nelle watchOS 3 e come implementarli in xamarin. IOS.
