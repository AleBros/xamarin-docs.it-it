---
title: Acquisto di prodotti non utilizzabili in Novell. iOS
description: Questo documento descrive i prodotti non utilizzabili in Novell. iOS, che sono funzionalità acquistate da un utente che rimane disponibile per un periodo illimitato, indipendentemente dal dispositivo.
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: aa478636b4ab94ab000fd98860646bfa300e9fab
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291318"
---
# <a name="purchasing-non-consumable-products-in-xamarinios"></a>Acquisto di prodotti non utilizzabili in Novell. iOS

I prodotti non consumabili sono di proprietà del cliente. Si prevede che l'utente potrà sempre accedervi, anche se il dispositivo viene smarrito o rubato o se ne acquista uno nuovo. Sono utili per libri, problemi di riviste, livelli di gioco, filtri foto, funzionalità Pro e così via. Una volta che un utente ha acquistato un prodotto non utilizzabile, non è mai necessario pagarlo di nuovo. Se il codice consente di provare accidentalmente, StoreKit visualizzerà un messaggio che indica che è già stato acquistato.

## <a name="non-consumable-products-sample"></a>Esempio di prodotti non utilizzabili

Il [codice InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit) contiene un progetto denominato non *consumabile*. Nell'esempio di codice viene illustrato come implementare prodotti non utilizzabili utilizzando come esempio i filtri foto. Una volta acquistato un filtro, è possibile applicarlo alla foto più volte. Non è mai necessario riacquistarlo.   
   
   
   
 Il processo di acquisto è illustrato in questa serie di schermate: il pulsante **Acquista** diventa il pulsante di attivazione della funzionalità:   
   
   
   
 [![](purchasing-non-consumable-products-images/image34.png "Il processo di acquisto è illustrato in questa serie di schermate")](purchasing-non-consumable-products-images/image34.png#lightbox)   
   
   
   
 Il processo di acquisto è identico a quello di un prodotto utilizzabile; la differenza principale consiste nel modo in cui viene tenuta traccia dell'acquisto nel codice dell'applicazione. In questo esempio il pulsante Acquista è disponibile solo se il prodotto non è già stato acquistato. in caso contrario, il pulsante attiva la funzionalità stessa.   
   
   
   

Il diagramma seguente illustra le interazioni tra le classi e il server dell'app Store per eseguire un acquisto di prodotto non utilizzabile:   
   
   
   
 [![](purchasing-non-consumable-products-images/image35.png "Interazioni tra le classi e il server dell'app Store per eseguire un acquisto di prodotto non utilizzabile")](purchasing-non-consumable-products-images/image35.png#lightbox)   
   
   
   
 La differenza principale rispetto all'esempio utilizzabile è che, una volta completato l'acquisto, l'interfaccia utente viene aggiornata per impedire il nuovo acquisto. In questo esempio, la notifica di una transazione riuscita aggiorna l'interfaccia utente in modo che il pulsante **Acquista** venga convertito in un pulsante che attiva la funzionalità stessa.

## <a name="re-purchasing-non-consumable-products"></a>Riacquisto di prodotti non utilizzabili

Il codice dovrebbe in genere nascondere o riutilizzare un pulsante di acquisto dopo che il prodotto è stato acquistato correttamente, per evitare che l'utente tenti di acquistare di nuovo il prodotto. L'applicazione di esempio esegue questa operazione modificando il pulsante **Acquista** nel pulsante che consente di usare il filtro foto di esempio.   
   
   
   
 Esistono situazioni in cui un'applicazione non è in grado di stabilire se un prodotto non utilizzabile è già stato acquistato:

- Se un'applicazione viene eliminata e reinstallata di nuovo in un dispositivo, tutti i record di acquisto saranno finiti, a meno che l'utente non effettui un ripristino del backup. 
- Se l'utente ha installato l'applicazione in due o più dispositivi ed effettua un acquisto in uno dei dispositivi. Gli altri dispositivi continueranno a visualizzare il prodotto disponibile per l'acquisto. 
- Se un cliente tenta di riacquistare un prodotto non utilizzabile in queste situazioni, l'App Store soddisferà di nuovo il prodotto senza alcun addebito. L'interfaccia utente apparirà inizialmente per eseguire un acquisto. ad esempio, viene visualizzato un avviso di conferma e l'ID Apple sarà obbligatorio. Tuttavia, l'utente visualizzerà un messaggio che informa che il prodotto è già stato acquistato.  
   
   
   
 Il percorso del codice in questo scenario è esattamente identico a quello di un acquisto regolare, ma le uniche differenze sono:

- L'utente non viene addebitato nuovamente per il prodotto.
- L' `SKPaymentTransaction` oggetto passato all'applicazione avrà una `OriginalTransaction` proprietà che fa riferimento alla transazione generata quando il prodotto è stato inizialmente acquistato. 
- Le applicazioni che vendono prodotti non utilizzabili devono implementare anche la funzionalità di **ripristino** di StoreKit per consentire agli utenti di recuperare gli acquisti esistenti. 
