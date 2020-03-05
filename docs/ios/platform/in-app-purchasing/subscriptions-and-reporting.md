---
title: Sottoscrizioni e creazione di report in Novell. iOS
description: Questo documento descrive le sottoscrizioni senza rinnovo, le sottoscrizioni gratuite, le sottoscrizioni rinnovate automaticamente e l'uso di iTunes Connect per creare report su questi elementi.
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 5e1019417ff7ac93abfe2396a4acaa76c66d182f
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291523"
---
# <a name="subscriptions-and-reporting-in-xamarinios"></a>Sottoscrizioni e creazione di report in Novell. iOS

## <a name="about-non-renewing-subscriptions"></a>Informazioni sulle sottoscrizioni senza rinnovo

Le sottoscrizioni che non si rinnovano sono destinate ai prodotti che rappresentano la vendita di un servizio con una restrizione temporale, ad esempio l'accesso a una settimana a un'applicazione di navigazione o l'accesso limitato al tempo a un archivio dati.   

Differenze principali tra sottoscrizioni non di rinnovo e altri tipi di prodotto:

- La definizione di prodotto in iTunes Connect non include il termine. Il codice dell'applicazione deve essere in grado di dedurre il periodo di validità dall'ID prodotto.
- Possono essere acquistati più volte (ad esempio un prodotto consumabile). Le applicazioni sono necessarie per gestire i termini della sottoscrizione, la scadenza e il rinnovo e impediscono all'utente di acquistare sottoscrizioni sovrapposte.
- Gli acquisti non sono supportati dalla funzione di ripristino StoreKit. Se la sottoscrizione deve essere disponibile in tutti i dispositivi di un utente, l'applicazione dovrà progettare e implementare questa funzionalità in combinazione con un server remoto. Le applicazioni sono anche responsabili del backup dello stato della sottoscrizione nei casi in cui viene eseguito il backup di un dispositivo, quindi il ripristino da un backup.
- Panoramica dell'implementazione
- Le sottoscrizioni che non si rinnovano in genere devono essere implementate tramite il flusso di lavoro distribuito dal server e gestite come prodotti utilizzabili.

## <a name="about-free-subscriptions"></a>Informazioni sulle sottoscrizioni gratuite

Le sottoscrizioni gratuite consentono agli sviluppatori di inserire contenuti gratuiti nelle app in edicola (non possono essere usati nelle app non in edicola). Una volta avviata, una sottoscrizione gratuita sarà disponibile su tutti i dispositivi dell'utente. Le sottoscrizioni gratuite non scadono mai; terminano solo quando viene disinstallata l'applicazione.

### <a name="implementation-overview"></a>Panoramica dell'implementazione

Le sottoscrizioni gratuite si comportano come le sottoscrizioni rinnovabili automaticamente. L'applicazione deve disporre di un prodotto di sottoscrizione gratuita disponibile per l'acquisto in iTunes Connect. Quando viene acquistato dall'utente, l'acquisto di sottoscrizioni gratuite deve essere convalidato come prodotto di sottoscrizione rinnovata automaticamente. È possibile ripristinare le transazioni di sottoscrizione gratuite.

## <a name="about-auto-renewable-subscriptions"></a>Informazioni sulle sottoscrizioni rinnovate automaticamente

Le sottoscrizioni rinnovate automaticamente vengono usate principalmente nelle applicazioni edicole. Rappresentano un prodotto che concede all'utente l'accesso al contenuto dinamico per un determinato periodo di tempo, configurato in iTunes Connect (set di periodi compresi tra 7 giorni e 1 anno). Le sottoscrizioni vengono rinnovate automaticamente, addebitando l'ID Apple degli utenti alla fine di ogni periodo di sottoscrizione, a meno che l'utente non opti esplicitamente. Questo tipo di prodotto funziona bene per le sottoscrizioni di riviste o notizie, in cui l'utente ottiene l'accesso a ogni problema pubblicato mentre la sottoscrizione è valida.

### <a name="implementation-overview"></a>Panoramica dell'implementazione

Le sottoscrizioni rinnovate automaticamente devono essere implementate usando il flusso di lavoro dei prodotti distribuiti dal server (vedere la sezione *Verifica della ricezione e prodotti recapitati dal server* ).

#### <a name="shared-secret"></a>Segreto condiviso

Il segreto condiviso di acquisto in-app deve essere usato nella richiesta JSON quando si verificano sottoscrizioni rinnovabili automaticamente nel server. Il segreto condiviso viene creato/accessibile tramite iTunes Connect.

Dal home page iTunes Connect selezionare **app personali**:   

 [![](subscriptions-and-reporting-images/image2.png "Select My Apps")](subscriptions-and-reporting-images/image2.png#lightbox)  

Selezionare un'applicazione e fare clic sulla scheda **acquisti in-app** :

[![](subscriptions-and-reporting-images/image6.png "Click on the In-App Purchases tab")](subscriptions-and-reporting-images/image6.png#lightbox)

Nella parte inferiore della pagina selezionare **Visualizza o genera un segreto condiviso**:

 [![](subscriptions-and-reporting-images/image40.png "Select View or generate a shared secret")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "Generate a shared secret")](subscriptions-and-reporting-images/image41.png#lightbox)   

Per usare il segreto condiviso, includerlo nel payload JSON che viene inviato ai server Apple quando si convalida una ricevuta di acquisto in-app per una sottoscrizione rinnovabile automaticamente, come indicato di seguito:

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

Il campo di stato della risposta sarà zero se l'acquisto è valido, come per gli altri tipi di prodotto.

#### <a name="downloading-items-after-the-initial-subscription-term"></a>Download degli elementi dopo il termine iniziale della sottoscrizione

Nell'ambito della distribuzione dei prodotti di sottoscrizione, il codice deve verificare spesso l'ultima ricevuta nota rispetto ai server Apple. Se una sottoscrizione viene rinnovata automaticamente dopo l'ultima verifica, la risposta JSON conterrà campi aggiuntivi che notificano all'applicazione la transazione che si è verificata (che deve estendere la validità delle sottoscrizioni). La risposta JSON conterrà:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

Se lo stato è zero, la sottoscrizione è ancora valida e gli altri campi contengono dati validi. Se lo stato è 21006, la sottoscrizione è scaduta. Vedere la documentazione relativa alla [Verifica di una ricevuta di sottoscrizione rinnovata automaticamente](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) per altri codici di errore.

#### <a name="restoring-auto-renewable-subscriptions"></a>Ripristino di sottoscrizioni rinnovate automaticamente

Si otterranno più transazioni: la transazione di acquisto originale più una transazione separata per ogni periodo di tempo in cui la sottoscrizione è stata rinnovata. È necessario tenere traccia delle date e dei termini di inizio per comprendere il periodo di validità.   

L'oggetto SKPaymentTransaction non include il periodo di validità della sottoscrizione. è necessario utilizzare un ID prodotto diverso per ogni termine e scrivere codice in grado di estrapolare il periodo di sottoscrizione dalla data di acquisto della transazione.

#### <a name="testing-auto-renewal"></a>Test del rinnovo automatico

Per semplificare il test delle sottoscrizioni, le relative durate vengono compresse durante i test in sandbox. le sottoscrizioni di 1 settimana si rinnovano ogni 3 minuti, le sottoscrizioni di 1 anno si rinnovano ogni Le sottoscrizioni vengono rinnovate automaticamente per un massimo di 6 volte durante i test in sandbox.

## <a name="reporting"></a>Reporting

iTunes Connect ( [iTunesConnect.Apple.com](https://itunesconnect.apple.com)) fornisce:   

 **Vendite e tendenze** : Visualizza i dettagli relativi a download di app, aggiornamenti e acquisti in-app.   

 **Pagamenti e report finanziari** : dettagli sul reddito guadagnato dalle app, oltre a elencare i pagamenti effettuati per l'utente e il relativo importo.

Di seguito è riportato un esempio di report sulle vendite e le tendenze:   

 [![](subscriptions-and-reporting-images/image42.png "An example Sales and Trends report")](subscriptions-and-reporting-images/image42.png#lightbox)   

 È disponibile anche un'app per **dispositivi mobili iOS Connect** . gli screenshot di iPhone per alcune delle statistiche disponibili sono illustrati di seguito:   

 [![](subscriptions-and-reporting-images/image43.png "iPhone screenshots for some of the statistics available")](subscriptions-and-reporting-images/image43.png#lightbox)
