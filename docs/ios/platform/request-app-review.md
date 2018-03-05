---
title: Richiesta di revisione di App
description: Questo articolo descrive il metodo RequestReview che Apple aggiunte per iOS 10 e come implementarlo in xamarin. IOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 469a63a990b1adb108284cfb88ee54e05218a8a9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="request-app-review"></a>Richiesta di revisione di App

_Questo articolo descrive il metodo RequestReview che Apple aggiunte per iOS 10 e come implementarlo in xamarin. IOS._

Nuovi utenti di iOS 10.3, il `RequestReview()` metodo consente a un'app iOS chiedere all'utente di velocità o esaminarla. Quando questo metodo viene chiamato in un'app di spedizione che l'utente ha installato dall'App Store, iOS 10 gestirà la classificazione intera e processo di revisione per lo sviluppatore. Poiché questo processo è governato dai criteri di App Store, un avviso o potrebbe non essere visualizzato.

![](request-app-review-images/review01.png "Un esempio di richiesta revisione avviso")

## <a name="requesting-a-rating-or-review"></a>Richiede una classificazione o revisione

Mentre il `RequestReview()` metodo statico del `SKStoreReviewController` classe può essere chiamata in qualsiasi punto in cui è utile nell'esperienza dell'utente, il processo di revisione è governato e gestito da criteri di App Store. Di conseguenza, questo metodo può o non potrebbe visualizzare un avviso e non dovrebbe mai essere chiamato in risposta a un'azione dell'utente, ad esempio toccando un pulsante.

Ad esempio, un'app potrebbe richiedere una revisione dopo che è stato avviato un determinato numero di volte o un gioco potrebbe richiedere una revisione del lettore termine di un livello.

Per le richieste di una revisione come un'app xamarin al termine di avvio, apportare le seguenti modifiche per il `AppDelegate.cs` file:

```csharp
using Foundation;
using StoreKit;
using UIKit;

namespace iOSTenThree
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        ...

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Request a review from the user
            SKStoreReviewController.RequestReview ();

            return true;
        }
        
        ...
        
    }
}
```

> [!NOTE]
> **Nota:** chiamata `RequestReview()` nello sviluppo di un carattere di sottolineatura app verrà sempre visualizzare la classificazione e verificare di finestra di dialogo in modo da poterlo verificare. Questo non è applicabile alle App che sono state distribuite tramite TestFlight, in cui la chiamata al metodo verrà ignorata.

Quando il `RequestReview()` metodo viene chiamato in un'app di spedizione che l'utente ha installato dall'App Store, iOS 10 gestirà l'intero processo di classificazione e di revisione per lo sviluppatore. Nuovamente, poiché questo processo è governato dai criteri di App Store, un avviso o potrebbe non essere visualizzato.

## <a name="linking-to-an-app-store-product-page"></a>Il collegamento a una pagina del prodotto App Store 

Oltre ai nuovi `RequestReview` (metodo), lo sviluppatore può comunque specificare un collegamento diretto alla pagina del prodotto dell'app nell'App Store all'interno di un'app. Aggiungendo `action=write-review` alla fine dell'URL di pagina del prodotto, verrà aperta una pagina in cui l'utente può scrivere automaticamente una revisione dell'app. 

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati il metodo RequestReview che Apple aggiunte per iOS 10 e come implementarlo in xamarin. IOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempio iOSTenThree](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
