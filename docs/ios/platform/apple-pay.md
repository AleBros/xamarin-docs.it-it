---
title: Apple Pay in xamarin. IOS
description: Questa guida illustra l'impostazione di configurare l'ambiente xamarin. IOS per l'uso con Apple Pay di pagare beni, ad esempio food, svago e le appartenenze tramite l'app. Sono incluse informazioni sugli identificatori necessari, i certificati e i diritti.
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/05/2017
ms.openlocfilehash: f2a38a4305aa11c78f3e4e35265a86dc71642777
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351665"
---
# <a name="apple-pay-in-xamarinios"></a>Apple Pay in xamarin. IOS

_Questa guida illustra l'impostazione di configurare l'ambiente xamarin. IOS per l'uso con Apple Pay di pagare beni, ad esempio food, svago e le appartenenze tramite l'app. Sono incluse informazioni sugli identificatori necessari, i certificati e i diritti._

Apple Pay è stata introdotta con iOS 8, consentendo agli utenti di pagare beni, ad esempio food, svago e le appartenenze tramite i propri dispositivi iOS. È disponibile su iPhone 6 e iPhone 6 più (+) e può anche essere abbinato l'Apple Watch per gli acquisti in negozio. Quando usato in un iPhone, Usa ID tocco come un modo per confermare e autorizzare le transazioni per una carta di debito o credito dell'utente.

## <a name="requirements"></a>Requisiti

Apple Pay è solo disponibile in iOS 8 e versioni successive e pertanto richiede un minimo di Xcode 6.

Sono necessari per integrare Apple Pay nell'app anche gli elementi seguenti:

 - Piattaforma del processore di pagamento
 - Identificatore esercente
 - Un certificato Apple Pay
 - Entitlement Apple Pay

Questo documento verrà esaminati questi elementi in modo più dettagliato.

## <a name="differences-between-apple-pay-and-iap"></a>Differenze tra Apple Pay e IAP

La differenza principale tra Apple Pay e *acquisti In-App* (IAP), si riferisce ai prodotti vengono venduti. *Fisico* venduto tramite Apple Pay; cibo, ristorazione e intrattenimento fisico (ad esempio, i ticket cinema) sono tutti esempi di questo. Al contrario, IAP vende *virtuale* dei beni di consumo, ad esempio premium o contenuti aggiuntivi e le sottoscrizioni: interazione utente mesi aggiuntivi di un servizio di streaming o extra risiede in un gioco.

Il Framework usati sono anche la differenza principale tra; [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) viene utilizzato per Apple Pay, mentre [StoreKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) fornisce il framework API per IAP.

Con Apple Pay, Apple [stati](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf) che "[] prevede un addebito gli utenti, ai commercianti o agli sviluppatori di usare Apple Pay per i pagamenti". In confronto, IAP prevede l'addebito del 30% per ogni transazione. Inoltre, con Apple Pay, la transazione non passa attraverso Apple affatto, invece, la chiamata passa attraverso una piattaforma di pagamento.

## <a name="using-a-payment-processor-platform"></a>Usando una piattaforma di processore di pagamento

Una delle parti fondamentali di Apple Pay è l'elaborazione dei pagamenti. Sebbene sia possibile farlo da soli, richiede notevoli conoscenze della crittografia
- come descritto nell'Apple [Guida di elaborazione pagamenti](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html).
Piattaforme di elaborazione pagamenti, d'altra parte, gestiscono queste operazioni per l'utente, consentendo di concentrarsi sulla compilazione dell'app.

Sono disponibili due opzioni:

- **Stripe** -iscriversi all'indirizzo [Stripe.com](https://stripe.com/) per accedere alle loro API.

- **JudoPay** -check-out loro [codice di esempio Xamarin su github](https://github.com/Judopay/Xamarin-Sample-App)e la registrazione nella [JudoPay.com](https://www.judopay.com/).

## <a name="provisioning-for-apple-pay"></a>Il provisioning per Apple Pay

Configurazione di un'app per usare Apple Pay richiede l'installazione nel portale Apple Developer e all'interno dell'app. Esistono una serie di passaggi da seguire per correttamente il provisioning dell'app per Apple pay:

1. Creare un ID esercente:
    - Seguire i passaggi [qui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)
2. Creare un ID App con la funzionalità si applicano pagare e aggiungervi l'esercente:
    - Seguire i passaggi [qui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)
3. Generare un certificato per l'ID esercente:
    - Seguire i passaggi [qui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)
4. Generare un profilo di Provisioning con l'ID App appena creato:
    - Seguire i passaggi [qui](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)
5. Aggiungi gli Entitlement Apple Pay:
    - Selezionare il diritto di retribuzione Apple come descritto in dettaglio [Ecco](~/ios/deploy-test/provisioning/entitlements.md), o aggiungere manualmente la coppia chiave/valore per il file da [qui](~/ios/deploy-test/provisioning/entitlements.md)

## <a name="working-with-apple-pay"></a>Uso di Apple Pay

Apple ha apportato diversi miglioramenti a Apple Pay iOS 10 che consentono all'utente di effettuare pagamenti sicuri da siti Web e tramite l'interazione con Siri e mappe.

Con iOS 10, diverse nuove API sono state aggiunte per l'utilizzo in iOS e watchOS per supportare le reti a pagamento dinamico e un nuovo ambiente di test di sandbox.

### <a name="apple-pay-website-integration"></a>Integrazione di sito Web di Apple Pay

Nuovo ai dispositivi iOS 10, lo sviluppatore può incorporare Apple Pay direttamente tramite i siti Web **ApplePay JS**. Gli utenti che accedono il sito Web con Safari in iOS o macOS possono effettuare pagamenti con Apple Pay convalidando la transazione iPhone o Apple Watch. Per altre informazioni, vedere di Apple [riferimento a Framework ApplePay JP](https://developer.apple.com/reference/applepayjs).

### <a name="passkit-framework-enhancements"></a>Miglioramenti apportati a Framework di PassKit

In iOS 10, il framework di PassKit è stato ampliato per supportare Apple Pay di fuori di `UIKit` e consentire emittenti di smart card presentare le proprie schede all'interno delle app.


#### <a name="supporting-apple-pay-outside-of-uikit"></a>Supporto di Apple Pay di fuori di UIKit

Usando [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), un'app può supportare le stesse funzionalità fornite da [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) senza usare UIKit. Anche questa nuova API è obbligatorio per il supporto di Apple Pay sul Apple Watch (e anche gli Intent specifici), è facoltativo negli altri casi (ad esempio, le app esistenti). Tuttavia, suggerite da Apple spostamento appena possibile alla nuova API per fornire un ampio supporto di Apple Pay in tutte le App per gli sviluppatori con una singola codebase. Per altre informazioni sulle finalità e l'integrazione di Siri, vedere la [Introduzione a SiriKit](~/ios/platform/sirikit/index.md) documentazione.

#### <a name="presenting-issuer-cards-from-within-apps"></a>Presentazione dell'autorità di certificazione le carte all'interno delle App

Con iOS 10, sono state aggiunte nuove funzionalità per il framework di PassKit che consentono di autorità emittenti di carte presentare le schede da entro le proprie app. Lo sviluppatore può aggiungere un `PKPaymentButtonTypeInStore` UIButton all'interfaccia utente dell'app che verrà visualizzato un pulsante di Apple Pay per una scheda.

Il `PresentPaymentPass` metodo per il [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) classe può inoltre essere utilizzata a livello di codice verrà visualizzata la scheda.

### <a name="new-payment-network-support"></a>Nuovo supporto di rete di pagamento

Nuovo ai dispositivi iOS 10, un'app può supportare automaticamente una nuova rete di pagamento quando diventa disponibile senza che lo sviluppatore debba modificare, ricompilare l'app e inviare di nuovo per l'App Store.

Il nuovo [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) metodo il `PKPaymentNetwork` classe consente a un'app individuare le reti disponibili nel dispositivo dell'utente in fase di esecuzione. Inoltre, il [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) proprietà è stata ampliata per richiedere il nome del provider di pagamento come argomento. Uso di questi metodi, un'app può supportare automaticamente qualsiasi rete che supporta il provider di pagamento.

Per altre informazioni, vedere nostri [configurazione Apple pagare](~/ios/platform/apple-pay.md) e di Apple [Guida di Apple pagare](https://developer.apple.com/apple-pay/).

### <a name="new-testing-environment"></a>Nuovo ambiente di Testing

Con iOS 10, Apple ha introdotto un nuovo ambiente di test che consente allo sviluppatore di eseguire il provisioning di carte di pagamento test direttamente in un dispositivo iOS. Questo nuovo ambiente di testing restituisce quindi i dati di pagamento di prova crittografata per l'app.

Per abilitare il nuovo ambiente di test, eseguire le operazioni seguenti:

1. Creare un nuovo iCloud test Account in iTunes Connect.
2. Accedere al dispositivo iOS con il nuovo account di test.
3. Impostare l'area desiderata per testare l'app in.
4. Una delle carte di pagamento di test da usare la [Guida di Apple pagare](https://developer.apple.com/apple-pay/) eseguire pagamenti.

> [!IMPORTANT]
> Passando gli account iCloud, il dispositivo si passerà automaticamente al nuovo ambiente di testing. Tuttavia, Apple comunque **richiede** l'app da sottoporre a test con reale le schede in un ambiente di produzione prima dell'invio a iTunes App Store.

## <a name="summary"></a>Riepilogo

In questo articolo, sono stati presentati i diversi elementi necessari per usare Apple Pay all'interno dell'app. Abbiamo visto come creare un ID esercente e modalità di utilizzo all'interno di **entitlements. plist**, che deve essere modificato manualmente.

## <a name="related-links"></a>Collegamenti correlati

- [Acquisti in-app](~/ios/platform/in-app-purchasing/index.md)
- [Introduzione a PassKit](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium (esempio)](https://developer.xamarin.com/samples/monotouch/ios9/Emporium/)
