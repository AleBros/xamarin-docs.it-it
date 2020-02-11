---
title: Apple Pay in Xamarin.iOS
description: Questa guida illustra la configurazione dell'ambiente Xamarin.iOS da usare con Apple Pay per pagare i beni fisici, ad esempio cibo, intrattenimento e appartenenze tramite l'app. Sono incluse informazioni sugli identificatori, i certificati e i diritti necessari.
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 87f81f96e51b6744e37a80819c3c88d1abb644f1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022240"
---
# <a name="apple-pay-in-xamarinios"></a>Apple Pay in Xamarin.iOS

_Questa guida illustra la configurazione dell'ambiente Xamarin.iOS da usare con Apple Pay per pagare i beni fisici, ad esempio cibo, intrattenimento e appartenenze tramite l'app. Sono incluse informazioni sugli identificatori, i certificati e i diritti necessari._

Apple Pay è stato introdotto insieme a iOS 8, consentendo agli utenti di pagare beni fisici, ad esempio cibo, intrattenimento e appartenenze tramite i dispositivi iOS. È disponibile in iPhone 6 e iPhone 6 Plus e può anche essere abbinato al Apple Watch per gli acquisti in-Store. Quando viene usato in un iPhone, USA Touch ID per confermare e autorizzare le transazioni sulla carta di credito o debito di un utente.

## <a name="requirements"></a>Requisiti

Apple Pay è disponibile solo in iOS 8 e versioni successive e richiede quindi almeno Xcode 6.

Per integrare Apple Pay nell'app sono necessari anche gli elementi seguenti:

- Piattaforma del processore di pagamento
- Identificatore Merchant
- Un certificato Apple Pay
- Diritto Apple Pay

In questo documento vengono esaminati in modo più dettagliato questi elementi.

## <a name="differences-between-apple-pay-and-iap"></a>Differenze tra Apple Pay e IAP

La differenza principale tra Apple Pay e l' *acquisto in-app* (IAP) riguarda i prodotti che vendono. I beni *fisici* vengono venduti tramite Apple pay; il cibo, l'alloggio e l'intrattenimento fisico (ad esempio, i biglietti per i cinema) sono tutti esempi di questo. Il servizio IAP, invece, vende le merci *virtuali* , ad esempio il contenuto Premium o extra, e le sottoscrizioni, pensa a mesi aggiuntivi di un servizio di streaming o a una vita aggiuntiva in un gioco.

I Framework usati rappresentano anche una differenza principale; [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) viene usato per Apple Pay, mentre [STOREKIT](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) fornisce l'API del Framework per IAP.

Con Apple Pay, Apple [dichiara](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf) che "[does] non addebitare a utenti, commercianti o sviluppatori di usare Apple pay per i pagamenti". In confronto, per ogni transazione viene addebitato il 30%. Inoltre, con Apple Pay, la transazione non passa attraverso Apple, bensì passa attraverso una piattaforma di pagamento.

## <a name="using-a-payment-processor-platform"></a>Utilizzo di una piattaforma di elaborazione pagamenti

Uno dei componenti fondamentali di Apple Pay è l'elaborazione dei pagamenti. Sebbene sia possibile eseguire questa operazione, è necessaria una conoscenza significativa della crittografia, come illustrato in dettaglio nella Guida all' [elaborazione dei pagamenti](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html)di Apple.
Le piattaforme di elaborazione dei pagamenti, d'altra parte, gestiscono queste operazioni per l'utente, consentendo di concentrarsi sulla creazione dell'app.

Sono disponibili due opzioni:

- **Stripe** : iscriversi a [stripe.com](https://stripe.com/) per accedere alle API.

- **JudoPay** : vedere il [codice di esempio Xamarin su GitHub](https://github.com/Judopay/Xamarin-Sample-App)e registrarsi in [JudoPay.com](https://www.judopay.com/).

## <a name="provisioning-for-apple-pay"></a>Provisioning per Apple Pay

La configurazione di un'app per l'uso di Apple Pay richiede la configurazione nel portale Apple Developer e nell'app. Per eseguire correttamente il provisioning dell'app per Apple Pay, è necessario seguire una serie di passaggi:

1. Creare un ID esercente:
    - Seguire [questa procedura](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)
2. Creare un ID app con la funzionalità applica pagamento e aggiungervi il Merchant:
    - Seguire [questa procedura](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)
3. Generare un certificato per l'ID esercente:
    - Seguire [questa procedura](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)
4. Generare un profilo di provisioning con l'ID app appena creato:
    - Seguire [questa procedura](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)
5. Aggiungere Apple Pay diritti:
    - Selezionare il diritto Apple pay come descritto [qui](~/ios/deploy-test/provisioning/entitlements.md)o aggiungere manualmente la coppia chiave/valore al file da [qui](~/ios/deploy-test/provisioning/entitlements.md)

## <a name="working-with-apple-pay"></a>Utilizzo di Apple Pay

Apple ha apportato diversi miglioramenti a Apple Pay in iOS 10 che consentono all'utente di effettuare pagamenti sicuri da siti Web e di interagire con Siri e Maps.

Con iOS 10 sono state aggiunte diverse nuove API che funzionano con iOS e watchos per supportare le reti di pagamento dinamiche e un nuovo ambiente di test sandbox.

### <a name="apple-pay-website-integration"></a>Integrazione di Apple Pay sito Web

Novità di iOS 10, lo sviluppatore può incorporare Apple Pay direttamente nei propri siti Web usando **APPLEPAY JS**. Gli utenti che accedono al sito Web con Safari in iOS o macOS possono effettuare pagamenti con Apple Pay convalidando la transazione sull'iPhone o Apple Watch. Per altre informazioni, vedere le informazioni di [riferimento sul Framework APPLEPAY JP](https://developer.apple.com/reference/applepayjs)di Apple.

### <a name="passkit-framework-enhancements"></a>Miglioramenti di PassKit Framework

In iOS 10, il Framework PassKit è stato ampliato per supportare Apple Pay al di fuori della `UIKit` e consentire alle emittenti di schede di presentare le proprie schede dall'interno delle app.

#### <a name="supporting-apple-pay-outside-of-uikit"></a>Supporto di Apple Pay all'esterno di UIKit

Utilizzando [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), un'app può supportare la stessa funzionalità fornita da [PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) senza utilizzare UIKit. Anche se questa nuova API è necessaria per supportare Apple Pay sul Apple Watch (e anche in specifici Intent), è facoltativa in altre situazioni, ad esempio le app esistenti. Tuttavia, Apple suggerisce il passaggio alla nuova API il prima possibile per offrire un supporto di ampio Apple Pay in tutte le app dello sviluppatore con una singola codebase. Per ulteriori informazioni su Intent e l'integrazione Siri, vedere la documentazione [introduttiva di SiriKit](~/ios/platform/sirikit/index.md) .

#### <a name="presenting-issuer-cards-from-within-apps"></a>Presentazione di schede emittenti dall'interno di app

Con iOS 10 sono state aggiunte nuove funzionalità al Framework PassKit che consentono agli emittenti di schede di presentare le proprie schede dalle proprie app. Lo sviluppatore può aggiungere un `PKPaymentButtonTypeInStore` UIButton all'interfaccia utente dell'app che visualizzerà un pulsante Apple Pay per una scheda.

Il metodo `PresentPaymentPass` della classe [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) può essere usato anche per visualizzare la scheda a livello di codice.

### <a name="new-payment-network-support"></a>Nuovo supporto per la rete di pagamento

Una novità di iOS 10, un'app può supportare automaticamente una nuova rete di pagamento quando diventa disponibile senza che lo sviluppatore debba modificare, ricompilare l'app e inviarla nuovamente all'App Store.

Il nuovo metodo [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) della classe `PKPaymentNetwork` consente a un'app di individuare le reti disponibili sul dispositivo dell'utente in fase di esecuzione. Inoltre, la proprietà [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) è stata espansa in modo da assumere il nome del provider di pagamenti come argomento. Usando questi metodi, un'app può supportare automaticamente qualsiasi rete supportata dal provider di pagamenti.

Per ulteriori informazioni, vedere la [configurazione di Apple pay](~/ios/platform/apple-pay.md) e la [Guida Apple pay](https://developer.apple.com/apple-pay/)di Apple.

### <a name="new-testing-environment"></a>Nuovo ambiente di test

Con iOS 10, Apple ha introdotto un nuovo ambiente di test che consente allo sviluppatore di effettuare il provisioning delle carte di pagamento dei test direttamente in un dispositivo iOS. Questo nuovo ambiente di testing restituisce quindi i dati di pagamento di test crittografati all'app.

Per abilitare il nuovo ambiente di test, eseguire le operazioni seguenti:

1. Creare un nuovo account iCloud di test in iTunes Connect.
2. Accedere al dispositivo iOS con il nuovo account di test.
3. Impostare l'area desiderata in cui testare l'app.
4. Per eseguire i pagamenti, utilizzare una delle schede di pagamento dei test della [Apple Pay guida](https://developer.apple.com/apple-pay/) .

> [!IMPORTANT]
> Cambiando gli account iCloud, il dispositivo passa automaticamente al nuovo ambiente di test. Tuttavia, Apple **richiede** ancora che l'app venga testata con le schede reali in un ambiente di produzione prima dell'invio all'iTunes App Store.

## <a name="summary"></a>Riepilogo

In questo articolo sono stati esaminati i diversi elementi necessari per usare Apple Pay all'interno dell'app. Si è appreso come creare un ID esercente e come viene usato all'interno di **titles. plist**, che deve essere modificato manualmente.

## <a name="related-links"></a>Collegamenti correlati

- [Acquisti in-app](~/ios/platform/in-app-purchasing/index.md)
- [Introduzione a PassKit](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-emporium)
