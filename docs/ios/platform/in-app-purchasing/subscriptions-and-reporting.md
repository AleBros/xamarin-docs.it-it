---
title: Le sottoscrizioni e report
ms.topic: article
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 8bf47f1edfd4256e25c7af8ce2970ad1918b8027
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="subscriptions-and-reporting"></a>Le sottoscrizioni e report

## <a name="about-non-renewing-subscriptions"></a>Le sottoscrizioni Non di rinnovo

Le sottoscrizioni di rinnovo non sono destinate i prodotti che rappresentano la vendita di un servizio con una restrizione di tempo, ad esempio (accedere della settimana a un'applicazione di navigazione) o tempo limitato a un archivio dati.   
   
   
   
 Differenze principali tra le sottoscrizioni non rinnovo e altri tipi di prodotto:

-  La definizione di prodotto in iTunes Connect non include il termine. Il codice dell'applicazione deve essere in grado di dedurre il periodo di validità dall'ID prodotto. 
-  Può essere acquistati più volte (ad esempio un prodotto supportato). Le applicazioni necessarie per gestire la sottoscrizione termine/scadenza e il rinnovo e impedire all'utente di acquisto di sottoscrizioni sovrapposte. 
-  Gli acquisti non sono supportati dalla funzione StoreKit ripristinare. Se la sottoscrizione deve essere disponibile in tutti i dispositivi dell'utente, l'applicazione sarà necessario progettare e implementare questa funzionalità in combinazione con un server remoto. Le applicazioni sono inoltre responsabili per eseguire il backup per i casi lo stato della sottoscrizione quando un dispositivo di backup quindi ripristinato dal-backup. 
-  Panoramica dell'implementazione
-  Le sottoscrizioni di rinnovo non devono essere implementate in genere tramite il flusso di lavoro recapitati al Server e gestiti come prodotto di consumo. 


## <a name="about-free-subscriptions"></a>Informazioni sulle sottoscrizioni disponibile

Le sottoscrizioni gratuite consentono agli sviluppatori di inserire contenuto disponibile in App Newsstand (non possono essere utilizzate nelle applicazioni non Newsstand). Dopo aver avviata una sottoscrizione gratuita sarà disponibile nei dispositivi dell'utente. Le sottoscrizioni gratuite scadono mai. terminare solo quando viene disinstallata l'applicazione.

### <a name="implementation-overview"></a>Panoramica dell'implementazione

Le sottoscrizioni gratuite sono molto simili rinnovabile automaticamente le sottoscrizioni: L'applicazione deve avere un prodotto di sottoscrizione gratuita disponibile per 'acquisto' in iTunes Connect. Quando è stato acquistato dall'utente, l'acquisto della sottoscrizione gratuita deve essere convalidato come un prodotto di sottoscrizione rinnovabile automaticamente. È possibile ripristinare le transazioni di iscrizione gratuita.


## <a name="about-auto-renewable-subscriptions"></a>Informazioni sulle sottoscrizioni rinnovabile automatica

Auto-rinnovabile sottoscrizioni vengono utilizzate principalmente nelle applicazioni di Newsstand. Si tratta di un prodotto che concede all'utente l'accesso a contenuto dinamico per un determinato periodo di tempo, è configurata in iTunes Connect (impostare periodi che variano da 7 giorni per 1 anno). Le sottoscrizioni vengono rinnovate automaticamente, ad addebitare i costi utenti ID Apple alla fine di ogni periodo di sottoscrizione, a meno che l'utente acconsente-out. Questo tipo di prodotto è adatta per le sottoscrizioni di rivista o notizie, in cui l'utente ottiene l'accesso a ogni problema pubblicato quando la sottoscrizione è valida.

### <a name="implementation-overview"></a>Panoramica dell'implementazione

Sottoscrizioni rinnovabile automatica deve essere implementate utilizzando il flusso di lavoro prodotti Server-Delivered (fare riferimento al *verifica ricevuta e i prodotti Server-Delivered* sezione).

#### <a name="shared-secret"></a>Segreto condiviso

Il segreto condiviso acquisto In-App deve essere utilizzato nella richiesta JSON durante la verifica automatica rinnovabile sottoscrizioni nel server. Il segreto condiviso viene creato o accedervi tramite iTunes Connect.

ITunes Connect pagina iniziale selezionare **My Apps**:   
   
 [ ![](subscriptions-and-reporting-images/image2.png "Selezionare le app personali")](subscriptions-and-reporting-images/image2.png)  
 
Selezionare un'applicazione e fare clic su di **acquisti In-App** scheda:

[ ![](subscriptions-and-reporting-images/image6.png "Fare clic sulla scheda acquisti In-App")](subscriptions-and-reporting-images/image6.png)

Nella parte inferiore della pagina, selezionare **visualizzazione o generare una chiave privata condivisa**:
   
 [ ![](subscriptions-and-reporting-images/image40.png "Selezionare una vista o generare una chiave privata condivisa")](subscriptions-and-reporting-images/image40.png)

 [ ![](subscriptions-and-reporting-images/image41.png "Generare un segreto condiviso")](subscriptions-and-reporting-images/image41.png)   
   
   
   
 Per utilizzare il segreto condiviso, includerlo nel payload JSON che viene inviato per i server Apple durante la convalida di un carico di acquisto in-app per una sottoscrizione rinnovabile automatico, simile al seguente:

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

Se l'acquisto è valido, come con altri tipi di prodotto, il campo di stato della risposta sarà zero.

#### <a name="downloading-items-after-the-initial-subscription-term"></a>Download degli elementi dopo il termine sottoscrizione iniziale

Come parte della distribuzione di prodotti di sottoscrizione, il codice deve verificare spesso la versione più recente ricezione nota contro i server Apple. Se una sottoscrizione è rinnovati automaticamente dopo l'ultima verifica, la risposta JSON conterrà campi aggiuntivi che notifica all'applicazione della transazione che si è verificato (che deve estendere la validità di sottoscrizioni). Contiene la risposta JSON:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

Se lo stato è uguale a zero la sottoscrizione è ancora valida e gli altri campi contengono dati validi. Se lo stato è 21006 la sottoscrizione è scaduta. Vedere il [verifica per determinare se un carico di sottoscrizione automatica rinnovabile](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) documentazione per altri codici di errore.

#### <a name="restoring-auto-renewable-subscriptions"></a>Ripristino automatico rinnovabile sottoscrizioni

Si otterrà più transazioni: la transazione di acquisto originale più di una transazione separata per ogni periodo di tempo, che la sottoscrizione è stata rinnovata. È necessario tenere traccia delle date di inizio e termini per comprendere cosa è il periodo di validità.   
   
   
   
 L'oggetto SKPaymentTransaction non include il termine sottoscrizione: è necessario utilizzare un ID prodotto diverso per ogni termine e scrivere codice che è possibile estrapolare il periodo di sottoscrizione dalla data di acquisto della transazione.

#### <a name="testing-auto-renewal"></a>Testing del rinnovo automatico

Per rendere più semplice testare le sottoscrizioni, la loro durata viene compressi durante il test nella sandbox. 1 settimana le sottoscrizioni vengono rinnovate ogni 3 minuti, 1 anno, le sottoscrizioni vengono rinnovate ogni ora. Le sottoscrizioni verranno rinnovati automaticamente un massimo di 6 volte durante il test nella sandbox.

## <a name="reporting"></a>Reporting

iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) fornisce:   
   
 **Vendite e le tendenze** : visualizza i dettagli dell'app Scarica gli aggiornamenti e acquisti in-app.   
   
 **Pagamenti e i rendiconti finanziari** : il reddito ottenuto tramite l'App, nonché i pagamenti di elenco che sono stati apportati all'utente e la quantità presa in dettaglio.

Un report di vendite e le tendenze di esempio è illustrato di seguito:   

 [ ![](subscriptions-and-reporting-images/image42.png "Un report di vendite e le tendenze di esempio")](subscriptions-and-reporting-images/image42.png)   
   
 È inoltre disponibile un [ **ITC connettersi Mobile**app iOS (collegamento a iTunes)](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8).
schermate di iPhone per alcune delle statistiche disponibili sono illustrate qui:   
   
 [ ![](subscriptions-and-reporting-images/image43.png "schermate di iPhone per alcune delle statistiche disponibili")](subscriptions-and-reporting-images/image43.png)
