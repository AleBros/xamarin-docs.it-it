---
title: Richiedi la revisione dell'app in Novell. iOS
description: Questo articolo descrive il metodo RequestReview aggiunto da Apple in iOS 10 e illustra come implementarlo in Novell. iOS.
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: c65e29a5499c33fd8d36dfa39bda05ec3b300148
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656297"
---
# <a name="request-app-review-in-xamarinios"></a>Richiedi la revisione dell'app in Novell. iOS

_Questo articolo illustra il metodo RequestReview aggiunto da Apple in iOS 10 e come implementarlo in Novell. iOS._

Una novità di iOS 10,3, `RequestReview()` il metodo consente a un'app per iOS di richiedere all'utente di valutarla o esaminarla. Quando questo metodo viene chiamato in un'app di spedizione che l'utente ha installato dall'App Store, iOS 10 gestirà l'intero processo di valutazione e revisione per lo sviluppatore. Poiché questo processo è regolato dai criteri dell'app Store, è possibile che venga visualizzato un avviso.

![](request-app-review-images/review01.png "Avviso di richiesta di revisione di esempio")

## <a name="requesting-a-rating-or-review"></a>Richiesta di valutazione o Revisione

Sebbene il `RequestReview()` metodo statico `SKStoreReviewController` della classe possa essere chiamato in qualsiasi punto in cui è sensato nell'esperienza utente, il processo di revisione è governato e gestito dai criteri dell'app Store. Di conseguenza, questo metodo può o meno visualizzare un avviso e non deve mai essere chiamato in risposta a un'azione dell'utente, ad esempio il tocco di un pulsante.

Ad esempio, un'app potrebbe richiedere una verifica dopo che è stata avviata un determinato numero di volte o una partita potrebbe richiedere una verifica dopo che il giocatore ha completato un livello.

Per richiedere una revisione non appena viene completata l'avvio di un'app Novell. iOS, apportare le modifiche seguenti `AppDelegate.cs` al file:

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
> Se `RequestReview()` si chiama in un'app in fase di sviluppo, verrà sempre visualizzata la finestra di dialogo valutazione e verifica per poterla testare. Questa operazione non si applica alle app che sono state distribuite tramite TestFlight, in cui la chiamata al metodo verrà ignorata.

Quando il `RequestReview()` metodo viene chiamato in un'app di spedizione che l'utente ha installato dall'App Store, iOS 10 gestirà l'intero processo di valutazione e revisione per lo sviluppatore. Anche in questo caso, poiché questo processo è regolato dai criteri dell'app Store, è possibile che venga visualizzato un avviso.

## <a name="linking-to-an-app-store-product-page"></a>Collegamento a una pagina del prodotto App Store 

Oltre al nuovo `RequestReview` metodo, lo sviluppatore può comunque fornire un collegamento approfondito alla pagina del prodotto dell'app nell'App Store dall'interno di un'app. `action=write-review` Aggiungendo alla fine dell'URL della pagina del prodotto, viene aperta una pagina in cui l'utente può scrivere automaticamente una recensione dell'app. 

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato il metodo RequestReview aggiunto da Apple in iOS 10 e come implementarlo in Novell. iOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempio iOSTenThree](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree/)
