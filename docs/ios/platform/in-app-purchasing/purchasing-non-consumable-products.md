---
title: Acquisto di prodotti Non di consumo in xamarin. IOS
description: Questo documento descrive i prodotti non di consumo in xamarin. IOS, ovvero funzionalità acquistate da un utente che rimangono disponibili per un periodo illimitato, indipendentemente dal dispositivo.
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 060403baf8ac28b9b160632a01471b9828735069
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118500"
---
# <a name="purchasing-non-consumable-products-in-xamarinios"></a>Acquisto di prodotti Non di consumo in xamarin. IOS

I prodotti non riproducibile 'proprietà' da parte del cliente. L'aspettativa è che sono sempre avranno accesso a essi, anche se il dispositivo viene smarrito o rubato o si acquista uno nuovo. Sono utili per la documentazione, problemi rivista, livelli di gioco, i filtri di foto, '-le funzionalità pro' e così via. Una volta che un utente ha acquistato un prodotto non di consumo, hanno mai pagarlo nuovamente. Se il codice accidentalmente permetta di prova, StoreKit mostrerà un messaggio in cui si è già stato acquistato.

## <a name="non-consumable-products-sample"></a>Esempio prodotti non di consumo

Il [codice InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) contiene un progetto chiamato *NonConsumables*. Nell'esempio di codice viene illustrato come implementare non di consumo prodotti con foto viene applicato il filtro come esempio. Dopo aver acquistato un filtro è possibile applicarlo alla foto ripetutamente. È mai necessario acquistarla nuovamente.   
   
   
   
 Il processo di acquisto viene visualizzato in questa serie di schermate: il **acquistare** pulsante diventa l'attivazione delle funzionalità:   
   
   
   
 [![](purchasing-non-consumable-products-images/image34.png "Il processo di acquisto viene visualizzato in questa serie di schermate")](purchasing-non-consumable-products-images/image34.png#lightbox)   
   
   
   
 Il processo di acquisto è lo stesso come prodotto utilizzabile; la differenza principale è in modalità di rilevamento l'acquisto nel codice dell'applicazione. In questo esempio che il pulsante di acquisto è solo disponibile se non è già stato acquistato il prodotto, in caso contrario, il pulsante Attiva la funzionalità stessa.   
   
   
   

Il diagramma seguente mostra le interazioni tra classi e il server di App Store per eseguire un acquisto di prodotti non di consumo:   
   
   
   
 [![](purchasing-non-consumable-products-images/image35.png "Acquistare le interazioni tra classi e il server di App Store per eseguire un prodotto non di consumo")](purchasing-non-consumable-products-images/image35.png#lightbox)   
   
   
   
 La differenza principale dell'esempio può essere utilizzato è che, dopo aver completato l'acquisto l'interfaccia utente viene aggiornato per evitare l'acquisto di nuovo. In questo esempio, la notifica di una transazione ha esito positivo Aggiorna l'interfaccia utente in modo che il **acquistare** pulsante viene convertito in un pulsante che consente di attivare la funzionalità stessa.

## <a name="re-purchasing-non-consumable-products"></a>Nuovo acquisto di prodotti Non di consumo

Il codice deve essere in genere nascosto o reimpiegare un pulsante di acquisto dopo che il prodotto è stato acquistato, per impedire che l'utente tenta di ripetere l'acquisto del prodotto. L'applicazione di esempio fa modificando il **acquistare** pulsante nel pulsante che consente di attivare il filtro di foto di esempio.   
   
   
   
 Esistono situazioni in cui un'applicazione non è possibile stabilire se è già stato acquistato un prodotto non di consumo:

-  Se un'applicazione viene eliminata e reinstallata in un dispositivo, tutti i record di acquisto non saranno più presenti (a meno che non / fino a quando l'utente esegue un ripristino di backup). 
-  Se l'utente ha l'applicazione installata nei dispositivi due (o più) ed effettui un acquisto in uno dei dispositivi. Gli altri dispositivi continueranno a visualizzare i prodotti disponibili per l'acquisto. 
-  Se un cliente tenta di eseguire nuovamente acquisteranno un prodotto non può essere utilizzato in questi casi, l'App Store verrà usata per soddisfare il prodotto nuovamente senza alcun costo aggiuntivo. L'interfaccia utente verrà inizialmente visualizzato per eseguire un acquisto (ad esempio, viene visualizzato un avviso di conferma e verrà richiesto l'ID Apple), tuttavia l'utente visualizzerà quindi un messaggio che li informa che il prodotto è già stato acquistato.  
   
   
   
 Il percorso del codice in questo scenario è esattamente lo stesso come un acquisto regolare, le uniche differenze sono:

-  L'utente non ricevono un addebito anche in questo caso per il prodotto.
-  Il `SKPaymentTransaction` oggetto passato all'applicazione avrà un `OriginalTransaction` proprietà che fa riferimento alla transazione che è stata generata quando è stato inizialmente acquistato il prodotto. 
-  Le applicazioni che vendono prodotti di consumo Non devono implementare anche di StoreKit **ripristinare** funzionalità per consentire agli utenti di recuperare gli acquisti di esistenti. 
