---
title: Apple Pay in watchOS in Xamarin
description: Questo articolo illustra i miglioramenti di Apple ha tentato di Apple Pay in watchOS 3 e come implementarli in xamarin. IOS per Apple Watch.
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 354e03ee1e07ba99fcdeb05617bc65ed89f0e8c2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364138"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>Apple Pay in watchOS in Xamarin

Apple ha apportato diversi miglioramenti a Apple Pay watchOS 3 che aggiunge il supporto per i pagamenti In-App. Ciò consente all'utente di fornire il pagamento e di pagare beni e servizi direttamente dal Apple Watch informazioni di contatto in modo sicuro.


## <a name="about-apple-pay-enhancements"></a>Sui miglioramenti di Apple Pay

Come Stated riportato sopra, Apple ha apportati numerosi miglioramenti a Apple Pay in watchOS 3 che consentono il pagamento sicuro e i recapiti di pagare beni e servizi direttamente dal Apple Watch. Questi miglioramenti sono disponibili modifiche per il framework di PassKit.

Con iOS 10 e watchOS 3, diverse nuove API sono state aggiunte per l'utilizzo in iOS e watchOS per supportare le reti a pagamento dinamico e un nuovo ambiente di test di sandbox.

## <a name="passkit-framework-enhancements"></a>Miglioramenti apportati a Framework di PassKit

In iOS 10, il framework di PassKit è stato ampliato per supportare Apple Pay di fuori di `UIKit` e consentire emittenti di smart card presentare le schede all'interno delle app. 

### <a name="supporting-apple-pay-outside-of-uikit"></a>Supporto di Apple Pay di fuori di UIKit

Usando [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), un'app può supportare le stesse funzionalità fornite da [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) senza usare UIKit. Anche questa nuova API è obbligatorio per il supporto di Apple Pay sul Apple Watch (e anche gli Intent specifici), è facoltativo negli altri casi (ad esempio, le app esistenti). Tuttavia, suggerite da Apple spostamento appena possibile alla nuova API per fornire un ampio supporto di Apple Pay in tutte le App per gli sviluppatori con una singola codebase. Per altre informazioni sulle finalità e l'integrazione di Siri, vedere la [Introduzione a SiriKit](~/ios/platform/sirikit/index.md) documentazione.

### <a name="presenting-issuer-cards-from-within-apps"></a>Presentazione dell'autorità di certificazione le carte all'interno delle App

Con iOS 10 e watchOS 3, sono state aggiunte nuove funzionalità per il framework di PassKit che consentono di autorità emittenti di carte presentare le carte di pagamento dal entro le proprie app. Lo sviluppatore può aggiungere un `PKPaymentButtonTypeInStore` UIButton all'interfaccia utente dell'app che verrà visualizzato un pulsante di Apple Pay per una scheda.

Il `PresentPaymentPass` metodo per il [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) classe può inoltre essere utilizzata a livello di codice verrà visualizzata la scheda.

## <a name="new-payment-network-support"></a>Nuovo supporto di rete di pagamento

Nuovo in iOS 10 e watchOS 3, un'app può supportare automaticamente una nuova rete di pagamento quando diventa disponibile senza che lo sviluppatore debba modificare, ricompilare l'app e inviare di nuovo per l'App Store.

Il nuovo [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) metodo il `PKPaymentNetwork` classe consente a un'app individuare le reti disponibili nel dispositivo dell'utente in fase di esecuzione. Inoltre, il [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) proprietà è stata ampliata per richiedere il nome del provider di pagamento come argomento. Uso di questi metodi, un'app può supportare automaticamente qualsiasi rete che supporta il provider di pagamento.

Per altre informazioni, vedere nostri [configurazione Apple pagare](~/ios/platform/apple-pay.md) e di Apple [Guida di Apple pagare](https://developer.apple.com/apple-pay/).

## <a name="new-testing-environment"></a>Nuovo ambiente di Testing

Con iOS 10 e watchOS 3, Apple ha introdotto un nuovo ambiente di test che consente allo sviluppatore di eseguire il provisioning di carte di pagamento test direttamente in un dispositivo iOS. Questo nuovo ambiente di testing restituisce quindi i dati di pagamento di prova crittografata per l'app.

Per abilitare il nuovo ambiente di test, eseguire le operazioni seguenti:

1. Creare un nuovo iCloud test Account in iTunes Connect.
2. Accedere al dispositivo iOS con il nuovo account di test.
3. Impostare l'area desiderata per testare l'app in.
4. Una delle carte di pagamento di test da usare la [Guida di Apple pagare](https://developer.apple.com/apple-pay/) eseguire pagamenti.

> [!NOTE]
> Passando gli account iCloud, il dispositivo si passerà automaticamente al nuovo ambiente di testing. Tuttavia, Apple comunque **richiede** l'app da sottoporre a test con reale le schede in un ambiente di produzione prima dell'invio a iTunes App Store.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i miglioramenti di Apple ha tentato di Apple Pay in watchOS 3 e come implementarli in xamarin. IOS.
