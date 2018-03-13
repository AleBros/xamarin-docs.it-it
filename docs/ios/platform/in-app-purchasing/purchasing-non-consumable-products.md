---
title: Acquisto di prodotti Non riproducibili
ms.topic: article
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 43aceca2eb86616fd6c9f51eb10c680e8cee9800
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="purchasing-non-consumable-products"></a>Acquisto di prodotti Non riproducibili

I prodotti non riproducibile 'proprietà' dal cliente. È previsto che sempre avranno accesso a essi, anche se il dispositivo viene smarrito o rubato o acquistare uno nuovo. Sono utili per la documentazione rivista problemi, livelli di giochi, i filtri di foto, 'le funzionalità pro', e così via. Una volta che un utente ha acquistato un prodotto non può essere utilizzato, hanno mai pagare per nuovamente. Se il codice accidentalmente consente di provare, StoreKit verrà visualizzato un messaggio che è già stato acquistato.

## <a name="non-consumable-products-sample"></a>Esempio prodotti non riproducibili

Il [InAppPurchaseSample codice](https://developer.xamarin.com/samples/monotouch/StoreKit/) contiene un progetto denominato *NonConsumables*. L'esempio di codice viene illustrato come implementare i prodotti non riproducibili utilizzando i filtri di foto come esempio. Dopo aver acquistato un filtro di applicarlo alla foto in modo continuativo. Non è necessario acquistarla nuovamente.   
   
   
   
 Il processo di acquisto viene visualizzato in questa serie di schermate: il **acquistare** diventa il pulsante di attivazione delle funzionalità:   
   
   
   
 [![](purchasing-non-consumable-products-images/image34.png "Il processo di acquisto viene visualizzato in questa serie di schermate")](purchasing-non-consumable-products-images/image34.png#lightbox)   
   
   
   
 Il processo di acquisto è lo stesso come un prodotto utilizzabile; la differenza principale è in modalità di rilevamento di acquisto nel codice dell'applicazione. In questo esempio che il pulsante di acquisto è disponibile se non è già stato acquistato il prodotto, in caso contrario il pulsante Attiva la funzionalità stessa.   
   
   
   

Il diagramma seguente mostra le interazioni tra classi e il server di App Store per eseguire un acquisto prodotto non può essere utilizzato:   
   
   
   
 [![](purchasing-non-consumable-products-images/image35.png "Acquistano le interazioni tra classi e il server di App Store per eseguire un prodotto non può essere utilizzato")](purchasing-non-consumable-products-images/image35.png#lightbox)   
   
   
   
 La differenza principale dell'esempio consumo è che una volta completato l'acquisto viene aggiornata l'interfaccia utente per evitare l'acquisto di nuovo. In questo esempio, la notifica di una transazione ha esito positivo Aggiorna l'interfaccia utente in modo che il **acquistare** viene convertito in un pulsante che attiva la funzionalità stessa.

## <a name="re-purchasing-non-consumable-products"></a>Nuovo acquisto di prodotti Non riproducibili

Il codice deve essere in genere nascosto o ridefinire un pulsante di acquisto dopo che il prodotto è stato correttamente acquistato, per impedire che l'utente sta tentando di acquistare il prodotto nuovamente. L'applicazione di esempio fa modificando il **acquistare** pulsante nel pulsante che consente di attivare il filtro di foto di esempio.   
   
   
   
 Ci sono casi in cui un'applicazione non è possibile indicare se è già stato acquistato un prodotto non può essere utilizzato:

-  Se un'applicazione viene eliminata e nuovamente installata in un dispositivo, tutti i record di acquisto non saranno più presenti (a meno che non / fino a quando l'utente esegue un ripristino di backup). 
-  Se l'utente ha l'applicazione installata su dispositivi a due (o più) ed effettua un acquisto in uno dei dispositivi. Altri dispositivi continuerà a visualizzare i prodotti disponibili per l'acquisto. 
-  Se un cliente tenta nuovamente di acquistare un prodotto non può essere utilizzato in questi casi, l'archivio di App verrà usata per soddisfare il prodotto nuovamente senza alcun costo aggiuntivo. L'interfaccia utente verrà inizialmente visualizzata per eseguire un acquisto (ad esempio, viene visualizzato un avviso di conferma e l'ID Apple sarà necessario), tuttavia l'utente vedrà quindi un messaggio che il prodotto è già stato acquistato.  
   
   
   
 Il percorso del codice in questo scenario è esattamente come un normale acquisto, le uniche differenze sono:

-  L'utente non vengono addebitata nuovamente il prodotto.
-  Il `SKPaymentTransaction` oggetto passato per l'applicazione avrà un `OriginalTransaction` proprietà che fa riferimento alla transazione che inizialmente è stato acquistato il prodotto è stata generata. 
-  Le applicazioni che vendono prodotti Non riproducibile devono implementare anche del StoreKit **ripristinare** funzionalità per consentire agli utenti di recuperare gli acquisti esistenti. 
