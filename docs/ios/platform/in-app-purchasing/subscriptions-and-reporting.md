---
title: Le sottoscrizioni e gestione rapporti in xamarin. IOS
description: Questo documento descrive non rinnovare le sottoscrizioni, le sottoscrizioni gratuite, rinnovabile automatica delle sottoscrizioni e tramite iTunes Connect per eseguire segnalazioni su questi elementi.
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 4e63894cb862db3b5b5a1e7a2bebd79160c311a9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61367384"
---
# <a name="subscriptions-and-reporting-in-xamarinios"></a>Le sottoscrizioni e gestione rapporti in xamarin. IOS

## <a name="about-non-renewing-subscriptions"></a>Su Non-rinnovo degli abbonamenti

Le sottoscrizioni con rinnovo non sono pensate per i prodotti che rappresentano le vendite di un servizio con una restrizione di tempo, ad esempio (accesso di una settimana a un'applicazione di navigazione) o accesso temporaneo a un archivio dati.   
   
Differenze principali tra le sottoscrizioni non rinnovo e altri tipi di prodotto:

-  La definizione di prodotto in iTunes Connect non include il termine. Il codice dell'applicazione deve essere in grado di dedurre il periodo di validità dall'ID del prodotto. 
-  Essi possono essere acquistati più volte (ad esempio, un prodotto può essere utilizzato). Le applicazioni sono necessarie per gestire la sottoscrizione termini/scadenza e il rinnovo e impedisce all'utente di acquisto di sottoscrizioni sovrapposte. 
-  Gli acquisti non sono supportati dalla funzione di StoreKit ripristinare. Se la sottoscrizione deve essere disponibile in tutti i dispositivi dell'utente, l'applicazione sarà necessario progettare e implementare questa funzionalità in combinazione con un server remoto. Le applicazioni sono anche responsabili backup lo stato della sottoscrizione per i casi quando un dispositivo risulta sottoposto a backup quindi ripristinato dal-backup. 
-  Panoramica dell'implementazione
-  Le sottoscrizioni con rinnovo non devono essere implementate in genere usando il flusso di lavoro fornita dal Server e gestiti, ad esempio prodotti di consumo. 


## <a name="about-free-subscriptions"></a>Sulle sottoscrizioni gratuite

Le sottoscrizioni gratuite consentono agli sviluppatori di inserire il contenuto disponibile nelle app di Newsstand (non possono essere utilizzate nelle App non Newsstand). Dopo aver avviata una sottoscrizione gratuita sarà disponibile in tutti i dispositivi dell'utente. Le sottoscrizioni gratuite scadono mai. scadono solo quando viene disinstallata l'applicazione.

### <a name="implementation-overview"></a>Panoramica dell'implementazione

Le sottoscrizioni gratuite si comportano più come rinnovabile automaticamente le sottoscrizioni. L'applicazione deve avere un prodotto di sottoscrizione gratuita disponibile per "purchase" in iTunes Connect. Quando si è acquistato dall'utente, l'acquisto dell'abbonamento gratuito deve essere convalidato, ad esempio un prodotto di sottoscrizione automatica rinnovabile. È possibile ripristinare le transazioni di sottoscrizione gratuita.


## <a name="about-auto-renewable-subscriptions"></a>Informazioni sulle sottoscrizioni rinnovabile automatico

Auto-rinnovabile sottoscrizioni vengono principalmente utilizzate nelle applicazioni Newsstand. Rappresentano un prodotto che concede all'utente l'accesso al contenuto dinamico per un determinato periodo di tempo, viene configurata in iTunes Connect (impostare periodi che variano da 7 giorni per 1 anno). Le sottoscrizioni vengono rinnovate automaticamente, l'addebito ha l'ID Apple gli utenti alla fine di ogni periodo di sottoscrizione, a meno che l'utente rifiuta esplicitamente. Questo tipo di prodotto funziona bene per le sottoscrizioni di rivista o notizie, in cui l'utente ottiene accesso a ogni problema pubblicato mentre l'abbonamento è valido.

### <a name="implementation-overview"></a>Panoramica dell'implementazione

Sottoscrizioni rinnovabile automatico deve essere implementate usando il flusso di lavoro prodotti Server-Delivered (fare riferimento al *ricezione verifica e prodotti Server-Delivered* sezione).

#### <a name="shared-secret"></a>Segreto condiviso

Il segreto condiviso acquisto In-App deve essere utilizzato nella richiesta JSON durante la verifica automatica rinnovabile sottoscrizioni nel server. Il segreto condiviso viene creato o accedervi tramite iTunes Connect.

Da iTunes Connect pagina iniziale selezionare **My Apps**:   
   
 [![](subscriptions-and-reporting-images/image2.png "Selezionare le app personali")](subscriptions-and-reporting-images/image2.png#lightbox)  
 
Selezionare un'applicazione e fare clic sui **acquisti In-App** scheda:

[![](subscriptions-and-reporting-images/image6.png "Fare clic sulla scheda gli acquisti In-App")](subscriptions-and-reporting-images/image6.png#lightbox)

Nella parte inferiore della pagina, selezionare **visualizzazione o generare una chiave privata condivisa**:
   
 [![](subscriptions-and-reporting-images/image40.png "Selezionare Vista o generare una chiave privata condivisa")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "Generare un segreto condiviso")](subscriptions-and-reporting-images/image41.png#lightbox)   
   
   
   
 Per usare il segreto condiviso, includerlo nel payload JSON che vengono inviati ai server di Apple durante la convalida di una conferma di acquisto in-app per una sottoscrizione di auto-rinnovabile, simile al seguente:

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

Campo relativo allo stato della risposta sarà zero se l'acquisto è valido, come con altri tipi di prodotto.

#### <a name="downloading-items-after-the-initial-subscription-term"></a>Download degli elementi dopo il termine sottoscrizione iniziale

Come parte della distribuzione di prodotti con sottoscrizione, il codice deve verificare spesso la ricezione nota più recente contro i server Apple. Se una sottoscrizione ha rinnovato automaticamente dopo l'ultima verifica, la risposta JSON contiene campi aggiuntivi che una notifica all'applicazione della transazione che si è verificata (che deve estendere la validità delle sottoscrizioni). Conterrà la risposta JSON:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

Se lo stato è zero la sottoscrizione sia ancora valida e gli altri campi contengono dati validi. Se lo stato è 21006 la sottoscrizione è scaduta. Vedere le [verifica una ricevuta di sottoscrizione automatica rinnovabile](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) documentazione per gli altri codici di errore.

#### <a name="restoring-auto-renewable-subscriptions"></a>Il ripristino automatico rinnovabile sottoscrizioni

Si otterrà più transazioni, la transazione di acquisto originale nonché una transazione distinta per ogni periodo di tempo che è stato rinnovato l'abbonamento. È necessario tenere traccia delle date di inizio e termini per comprendere che cos'è il periodo di validità.   
   
   
   
 L'oggetto SKPaymentTransaction non include il termine sottoscrizione, è necessario utilizzare un ID di prodotto diverso per ogni periodo di validità e scrivere codice che è possibile estrapolare il periodo di sottoscrizione dalla data di acquisto della transazione.

#### <a name="testing-auto-renewal"></a>Test di rinnovo automatico

Per renderne più semplice testare le sottoscrizioni, la loro durata viene compressi durante il test nell'ambiente sandbox. 1 settimana le sottoscrizioni vengono rinnovate ogni 3 minuti, le sottoscrizioni vengono rinnovate ogni ora 1 anno. Le sottoscrizioni verranno rinnovate automaticamente un massimo di 6 volte durante il test nell'ambiente sandbox.

## <a name="reporting"></a>Reporting

iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) fornisce:   
   
 **Vendite e le tendenze** – download Visualizza i dettagli dell'app, gli aggiornamenti e acquisti in-app.   
   
 **Pagamenti e rendiconti finanziari** – illustra in dettaglio il reddito ottenuto tramite l'App, nonché listato pagamenti che sono stati apportati per te e per quanto presa.

Seguito è riportato un esempio di report di vendite e le tendenze:   

 [![](subscriptions-and-reporting-images/image42.png "Un esempio di report di vendite e tendenze")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 È inoltre disponibile un' [ **Mobile Connect ITC**app per iOS (collegamento a iTunes)](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8).
di seguito sono riportati screenshot di iPhone per alcune delle statistiche disponibili:   
   
 [![](subscriptions-and-reporting-images/image43.png "screenshot di iPhone per alcune delle statistiche disponibili")](subscriptions-and-reporting-images/image43.png#lightbox)
