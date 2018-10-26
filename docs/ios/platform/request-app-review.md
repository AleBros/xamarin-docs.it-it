---
title: Richiedere la revisione di App in xamarin. IOS
description: Questo articolo descrive il metodo RequestReview di Apple aggiunto in iOS 10 e viene illustrato come implementare questo metodo in xamarin. IOS.
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: f72aaa781b0712e206cf02725cfc434594287f41
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109783"
---
# <a name="request-app-review-in-xamarinios"></a>Richiedere la revisione di App in xamarin. IOS

_Questo articolo descrive il metodo RequestReview di Apple aggiunto in iOS 10 e come implementarlo in xamarin. IOS._

Novità di iOS 10.3, il `RequestReview()` metodo consente a un'app iOS chiedere all'utente valutare o esaminarlo. Quando questo metodo viene chiamato in un'app di spedizione che l'utente ha installato dall'App Store, iOS 10 gestirà la classificazione intera e processo di revisione per gli sviluppatori. Poiché questo processo è regolato dai criteri di App Store, un avviso o potrebbe non essere visualizzato.

![](request-app-review-images/review01.png "Un avviso di richiesta di revisione di esempio")

## <a name="requesting-a-rating-or-review"></a>Richiede una valutazione o una recensione

Mentre il `RequestReview()` metodo statico del `SKStoreReviewController` classe può essere chiamata in qualsiasi punto in cui avrebbe senso nell'esperienza dell'utente, il processo di revisione è disciplinato e gestito da criteri di App Store. Di conseguenza, questo metodo può o potrebbe non essere visualizzato un avviso e non dovrebbe mai essere chiamato in risposta a un'azione dell'utente, ad esempio se si tocca un pulsante.

Ad esempio, un'app potrebbe richiedere una verifica dopo che è stato avviato un determinato numero di volte oppure un gioco potrebbe richiedere una verifica dopo che il giocatore termina un livello.

Per le richieste una revisione, non appena un'app xamarin. IOS di completamento dell'avvio, apportare le modifiche seguenti per il `AppDelegate.cs` file:

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
> La chiamata `RequestReview()` nello sviluppo di un difetto app visualizzerà sempre la classificazione e rivedere finestra di dialogo in modo che è possibile testarlo. Ciò non è applicabile alle App che sono state distribuite con TestFlight, in cui verrà ignorata la chiamata al metodo.

Quando il `RequestReview()` metodo viene chiamato in un'app di spedizione che l'utente ha installato dall'App Store, iOS 10 verrà gestito l'intero processo di valutazione e revisione per gli sviluppatori. Anche in questo caso, poiché questo processo è regolato dai criteri di App Store, un avviso o potrebbe non essere visualizzato.

## <a name="linking-to-an-app-store-product-page"></a>Collegamento a una pagina del prodotto App Store 

Oltre ai nuovi `RequestReview` (metodo), lo sviluppatore può comunque fornire un collegamento diretto alla pagina del prodotto dell'app di Store l'App dall'interno di un'app. Aggiungendo `action=write-review` alla fine dell'URL della pagina del prodotto, verrà aperta una pagina in cui l'utente può scrivere automaticamente una revisione dell'app. 

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato il metodo RequestReview di Apple aggiunto in iOS 10 e come implementarlo in xamarin. IOS.



## <a name="related-links"></a>Collegamenti correlati

- [iOSTenThree esempio](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
