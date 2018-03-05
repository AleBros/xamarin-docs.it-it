---
title: Apple Pay
description: Questa guida viene esaminato configurando l'ambiente xamarin. IOS per l'utilizzo con Apple Pay pagare merci fisiche, ad esempio alimentari, intrattenimento e appartenenze tramite l'app. Sono incluse informazioni sugli identificatori necessari, i certificati e dei diritti.
ms.topic: article
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 0ac2a19e9020113df273897a8ec2c86ee1763ec2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="apple-pay"></a>Apple Pay

_Questa guida viene esaminato configurando l'ambiente xamarin. IOS per l'utilizzo con Apple Pay pagare merci fisiche, ad esempio alimentari, intrattenimento e appartenenze tramite l'app. Sono incluse informazioni sugli identificatori necessari, i certificati e dei diritti._


Apple Pay introdotta insieme iOS 8, consentendo agli utenti di pagare merci fisiche, ad esempio alimentari, intrattenimento e appartenenze tramite i propri dispositivi iOS. È disponibile in iPhone 6 e iPhone 6 più (+) e può anche essere abbinata a di Apple Watch per gli acquisti nell'archivio. Quando utilizzato in un iPhone, come un modo per verificare e autorizzare le transazioni per un utente carta di credito Usa ID tocco.


## <a name="requirements"></a>Requisiti

Apple Pay disponibile solo all'interno di iOS 8 e versioni successive e pertanto richiede un minimo di Xcode 6.

Sono necessari per integrare Apple Pay nell'app anche gli elementi seguenti:

 - Piattaforma del processore di pagamento
 - Identificatore di carta di credito
 - Un certificato Apple Pay
 - Apple Pay diritto

Questo documento verrà esaminati più dettagliatamente questi elementi.

## <a name="differences-between-apple-pay-and-iap"></a>Differenze tra la retribuzione di Apple e IAP

La differenza principale tra Apple Pay e *acquisto In-App* (IAP), relativo ai prodotti venduti. *Fisico* venduto tramite Apple Pay; alimentari, ristorazione e svago fisica (ad esempio il ticket cinema) sono tutti esempi di questo. Al contrario, IAP vende *virtuale* beni, ad esempio premium o contenuti aggiuntivi e le sottoscrizioni: valutare altri mesi di un servizio di streaming o aggiuntivi risiede in un gioco.

I Framework utilizzati sono anche una differenza fondamentale; [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) viene utilizzata per Apple Pay, mentre [StoreKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) fornisce il framework di API per IAP.

Con Apple Pay, Apple [stati](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf) che "[] non viene addebitata utenti, aziende o gli sviluppatori di utilizzare retribuzione di Apple per i pagamenti". In confronto, IAP dispone di un addebito 30% per ogni transazione. Inoltre, con Apple Pay, la transazione non passa attraverso Apple affatto, invece, viene sottoposto a una piattaforma di pagamento.


## <a name="using-a-payment-processor-platform"></a>Utilizza una piattaforma del processore di pagamento

Una delle parti fondamentali di Apple Pay è l'elaborazione dei pagamenti. Sebbene sia possibile eseguire questa operazione manualmente, è necessaria la conoscenza significativo della crittografia
- come descritto in Apple [Guida per l'elaborazione di pagamento](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html).
Piattaforme di elaborazione dei pagamenti, d'altra parte, gestiscono queste operazioni automaticamente, consentendo di concentrarsi sulla compilazione di app.

Sono disponibili due opzioni:

- **Stripe** -iscriversi al [Stripe.com](https://stripe.com/) per accedere a tali API.

- **JudoPay** -estrae i [codice di esempio Xamarin su github](https://github.com/Judopay/Xamarin-Sample-App)e la registrazione al [JudoPay.com](https://www.judopay.com/).


## <a name="provisioning-for-apple-pay"></a>Il provisioning per il pagamento di Apple

Configurazione di un'app per l'utilizzo di Apple Pay richiede l'installazione sul portale per sviluppatori di Apple e all'interno dell'app. Esistono una serie di passaggi da seguire per correttamente il provisioning di app per il pagamento Apple:

1. Creare un ID azienda:
    - Seguire i passaggi [qui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)
2. Creare un ID di App con la possibilità di applicare pagare e aggiungervi l'azienda:
    - Seguire i passaggi [qui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)
3. Generare un certificato per l'ID azienda:
    - Seguire i passaggi [qui](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)
4. Generare un profilo di Provisioning con l'ID App appena creato:
    - Seguire i passaggi [qui](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)
5. Aggiungere i diritti di Apple Pay:
    - Selezionare i diritti di pagamento Apple come descritto in dettaglio [qui](~/ios/deploy-test/provisioning/entitlements.md), o aggiungere manualmente la coppia chiave/valore per il file da [qui](~/ios/deploy-test/provisioning/entitlements.md)


## <a name="working-with-apple-pay"></a>Utilizzo di pagamento di Apple

Apple ha apportato numerosi miglioramenti a pagamento Apple iOS 10 che consentono all'utente di effettuare i pagamenti protetti da siti Web e tramite l'interazione con Siri e mappe.

Con iOS 10, diverse nuove API sono state aggiunte per l'utilizzo in iOS e watchOS per supportare le reti a pagamento dinamico e un nuovo ambiente di test di sandbox.


### <a name="apple-pay-website-integration"></a>Integrazione di sito Web di pagamento di Apple

Nuovo in iOS 10, lo sviluppatore può incorporare Apple Pay direttamente i relativi siti Web usano **ApplePay JS**. Gli utenti che esplorano il sito Web con Safari in iOS o Mac OS possono rendere i pagamenti con Apple Pay convalidando la transazione sul proprio iPhone o Apple Watch. Per ulteriori informazioni, vedere Apple [ApplePay JP Framework riferimento](https://developer.apple.com/reference/applepayjs).

### <a name="passkit-framework-enhancements"></a>Miglioramenti apportati a Framework PassKit

In iOS 10, il framework PassKit è stato ampliato per supportare Apple Pay all'esterno di `UIKit` e per consentire di autorità emittenti di smart card presentare le proprie schede da all'interno delle app.


#### <a name="supporting-apple-pay-outside-of-uikit"></a>Supporto di pagamento in base di fuori di UIKit Apple

Utilizzando [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) e [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), un'applicazione può supportare le stesse funzionalità fornite da [ PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) senza utilizzare UIKit. Anche se questa nuova API è necessaria per supportare Apple Pay l'Apple Watch (e in specifici scopi nonché), è facoltativo negli altri casi (ad esempio le app esistenti). Tuttavia, Apple suggerisce lo spostamento alla nuova API appena possibile per fornire un ampio supporto Apple Pay in tutte le app dello sviluppatore con una sola codebase. Per ulteriori informazioni sui tipi e l'integrazione di Siri, consultare il nostro [Introduzione a SiriKit](~/ios/platform/sirikit/index.md) documentazione.

#### <a name="presenting-issuer-cards-from-within-apps"></a>Presentazione delle schede dell'autorità di certificazione da all'interno di applicazioni

Con iOS 10, sono state aggiunte nuove funzionalità del Framework PassKit che consentono di autorità emittenti di smart card presentare le proprie schede all'interno delle proprie App per le. Lo sviluppatore può aggiungere un `PKPaymentButtonTypeInStore` UIButton all'interfaccia utente dell'applicazione che verrà visualizzato un pulsante Apple Pay per una scheda.

Il `PresentPaymentPass` metodo il [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) classe può essere utilizzata anche a livello di codice verrà visualizzata la scheda.

### <a name="new-payment-network-support"></a>Nuovo supporto di rete di pagamento

Nuovo in iOS 10, un'applicazione può supportare automaticamente una nuova rete di pagamento quando diventa disponibile senza dover modificare, ricompilare l'app e inviare di nuovo all'archivio di App che lo sviluppatore.

Il nuovo [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) metodo la `PKPaymentNetwork` classe consente a un'app individuare le reti disponibili nel dispositivo dell'utente in fase di esecuzione. Inoltre, il [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) proprietà è stata ampliata per richiedere il nome del provider di pagamento come argomento. Utilizzo di questi metodi, un'applicazione può supportare automaticamente qualsiasi rete che supporta il provider di pagamento.

Per ulteriori informazioni, vedere il nostro [configurazione pagare Apple](~/ios/platform/apple-pay.md) e Apple [Guida di Apple pagare](https://developer.apple.com/apple-pay/).

### <a name="new-testing-environment"></a>Nuovo ambiente di Testing

Con iOS 10, Apple ha introdotto un nuovo ambiente di testing che consente allo sviluppatore di eseguire il provisioning di schede di pagamento di test direttamente in un dispositivo iOS. Questo nuovo ambiente di testing restituisce quindi i dati di pagamento prova crittografato all'app.

Per abilitare il nuovo ambiente di test, eseguire le operazioni seguenti:

1. Creare un nuovo iCloud test Account in iTunes Connect.
2. Accedere al dispositivo iOS con il nuovo account di test.
3. Impostare la regione desiderata per testare l'app in.
4. Utilizzare una delle schede di pagamento di test dal [Guida di Apple pagare](https://developer.apple.com/apple-pay/) per effettuare i pagamenti.

> [!IMPORTANT]
>  **Nota:** passando l'account iCloud, il dispositivo passerà automaticamente al nuovo ambiente di testing. Tuttavia, è ancora Apple **richiede** schede dell'applicazione da testare con reale in un ambiente di produzione prima dell'invio a iTunes App Store.

## <a name="summary"></a>Riepilogo

In questo articolo, è esplorare i diversi elementi, è necessari utilizzare Apple Pay all'interno dell'app. Illustra come creare un ID azienda e come utilizzarlo all'interno di **Entitlements.plist**, che deve essere modificato manualmente.


## <a name="related-links"></a>Collegamenti correlati

- [Acquisti in-app](~/ios/platform/in-app-purchasing/index.md)
- [Introduzione a PassKit](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium (esempio)](https://developer.xamarin.com/samples/monotouch/ios9/Emporium/)
