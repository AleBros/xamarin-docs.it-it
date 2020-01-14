---
title: Apple Pay su watchos in Xamarin
description: Questo articolo descrive i miglioramenti apportati da Apple a Apple Pay in watchos 3 e come implementarli in Xamarin.iOS per Apple Watch.
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 372b034b7e14f3cfaadde8fe5a5370e368f161db
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030130"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>Apple Pay su watchos in Xamarin

Apple ha apportato diversi miglioramenti alla Apple Pay in watchos 3 che aggiunge il supporto per i pagamenti in-app. Questo consente all'utente di fornire in modo sicuro le informazioni di pagamento e di contatto per pagare i beni e i servizi fisici direttamente dalla Apple Watch.

## <a name="about-apple-pay-enhancements"></a>Informazioni sui miglioramenti apportati Apple Pay

Come affermato in precedenza, Apple ha apportato diversi miglioramenti a Apple Pay in watchos 3 che consentono di pagare le informazioni di contatto e il pagamento sicuro per i beni e i servizi fisici direttamente dalla Apple Watch. Questi miglioramenti sono forniti dalle modifiche apportate a PassKit Framework.

Con iOS 10 e watchos 3 sono state aggiunte diverse nuove API che funzionano con iOS e watchos per supportare le reti di pagamento dinamiche e un nuovo ambiente di test sandbox.

## <a name="passkit-framework-enhancements"></a>Miglioramenti di PassKit Framework

In iOS 10, il Framework PassKit è stato ampliato per supportare Apple Pay al di fuori della `UIKit` e consentire alle emittenti di schede di presentare le proprie schede dall'interno delle app. 

### <a name="supporting-apple-pay-outside-of-uikit"></a>Supporto di Apple Pay all'esterno di UIKit

Utilizzando [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), un'app può supportare la stessa funzionalità fornita da [PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) senza utilizzare UIKit. Anche se questa nuova API è necessaria per supportare Apple Pay sul Apple Watch (e anche in specifici Intent), è facoltativa in altre situazioni, ad esempio le app esistenti. Tuttavia, Apple suggerisce il passaggio alla nuova API il prima possibile per offrire un supporto di ampio Apple Pay in tutte le app dello sviluppatore con una singola codebase. Per ulteriori informazioni su Intent e l'integrazione Siri, vedere la documentazione [introduttiva di SiriKit](~/ios/platform/sirikit/index.md) .

### <a name="presenting-issuer-cards-from-within-apps"></a>Presentazione di schede emittenti dall'interno di app

Con iOS 10 e watchos 3 sono state aggiunte nuove funzionalità al Framework di PassKit che consentono agli emittenti di schede di presentare le proprie carte di pagamento all'interno delle proprie app. Lo sviluppatore può aggiungere un `PKPaymentButtonTypeInStore` UIButton all'interfaccia utente dell'app che visualizzerà un pulsante Apple Pay per una scheda.

Il metodo `PresentPaymentPass` della classe [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) può essere usato anche per visualizzare la scheda a livello di codice.

## <a name="new-payment-network-support"></a>Nuovo supporto per la rete di pagamento

Una novità di iOS 10 e watchos 3, un'app può supportare automaticamente una nuova rete di pagamento quando diventa disponibile senza che lo sviluppatore debba modificare, ricompilare l'app e inviarla nuovamente all'App Store.

Il nuovo metodo [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) della classe `PKPaymentNetwork` consente a un'app di individuare le reti disponibili sul dispositivo dell'utente in fase di esecuzione. Inoltre, la proprietà [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) è stata espansa in modo da assumere il nome del provider di pagamenti come argomento. Usando questi metodi, un'app può supportare automaticamente qualsiasi rete supportata dal provider di pagamenti.

Per ulteriori informazioni, vedere la [configurazione di Apple pay](~/ios/platform/apple-pay.md) e la [Guida Apple pay](https://developer.apple.com/apple-pay/)di Apple.

## <a name="new-testing-environment"></a>Nuovo ambiente di test

Con iOS 10 e watchos 3, Apple ha introdotto un nuovo ambiente di test che consente allo sviluppatore di effettuare il provisioning delle carte di pagamento dei test direttamente in un dispositivo iOS. Questo nuovo ambiente di testing restituisce quindi i dati di pagamento di test crittografati all'app.

Per abilitare il nuovo ambiente di test, eseguire le operazioni seguenti:

1. Creare un nuovo account iCloud di test in iTunes Connect.
2. Accedere al dispositivo iOS con il nuovo account di test.
3. Impostare l'area desiderata in cui testare l'app.
4. Per eseguire i pagamenti, utilizzare una delle schede di pagamento dei test della [Apple Pay guida](https://developer.apple.com/apple-pay/) .

> [!NOTE]
> Cambiando gli account iCloud, il dispositivo passa automaticamente al nuovo ambiente di test. Tuttavia, Apple **richiede** ancora che l'app venga testata con le schede reali in un ambiente di produzione prima dell'invio all'iTunes App Store.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato i miglioramenti apportati da Apple a Apple Pay in watchos 3 e come implementarli in Xamarin.iOS.
