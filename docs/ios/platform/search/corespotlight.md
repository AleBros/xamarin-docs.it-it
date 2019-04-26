---
title: Ricerca Spotlight di Core in xamarin. IOS
description: Questo documento descrive come usare contenuti in evidenza di Core in un'applicazione xamarin. IOS per fornire i collegamenti al contenuto all'interno dell'applicazione. Illustra come creare, ripristinare, aggiornare ed eliminare gli elementi disponibili per la ricerca.
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: fb9ddcc39bd33199dc370897250cd0d74597612f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61248473"
---
# <a name="search-with-core-spotlight-in-xamarinios"></a>Ricerca Spotlight di Core in xamarin. IOS

Spotlight core è un nuovo framework per iOS 9 che presenta un'API di tipo database da aggiungere, modificare o eliminare i collegamenti al contenuto all'interno dell'app. Gli elementi che sono stati aggiunti mediante Core Spotlight saranno disponibili in ricerca di Spotlight nel dispositivo iOS.

Per un esempio dei tipi di contenuto che può essere indicizzata tramite Spotlight Core, esaminare i messaggi, posta elettronica, calendario e note di Apple app. Questi oggetti attualmente utilizzano Core Spotlight per fornire i risultati della ricerca.

## <a name="creating-an-item"></a>Creazione di un elemento

Di seguito è riportato un esempio di creazione di un elemento e indicizzando tale colonna usando Spotlight Core:

```csharp
using CoreSpotlight;
...

// Create attributes to describe an item
var attributes = new CSSearchableItemAttributeSet();
attributes.Title = "App Center Test";
attributes.ContentDescription = "Automatically test your app on 1,000 devices in the cloud.";

// Create item
var item = new CSSearchableItem ("1", "products", attributes);

// Index item
CSSearchableIndex.DefaultSearchableIndex.Index (new CSSearchableItem[]{ item }, (error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Queste informazioni sarà simile alla seguente in un risultato di ricerca:

[![](corespotlight-images/corespotlight01.png "Panoramica di risultati di ricerca di Spotlight core")](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>Il ripristino di un elemento

Quando l'utente tocca un elemento aggiunto ai risultati della ricerca tramite Core Spotlight per l'app, il `AppDelegate` metodo `ContinueUserActivity` viene chiamato (questo metodo viene usato anche per `NSUserActivity`). Ad esempio:

```csharp
public override bool ContinueUserActivity (UIApplication application,
   NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchableItem.ActionType.ToString ()) {
            // Display content for searchable item...
        }
        break;
    }

    return true;
}
```

Si noti che questa volta si verifica la presenza di attività un' `ActivityType` di `CSSearchableItem.ActionType`.

## <a name="updating-an-item"></a>Aggiornamento di un elemento

Potrebbero esserci volte quando un elemento di indice è stata creata con Spotlight Core devono essere modificati, ad esempio una modifica nel titolo o immagine di anteprima è obbligatoria. Per apportare questa modifica, utilizziamo il metodo di stesso perché è stato usato per creare inizialmente l'indice.
Creiamo una nuova `CSSearchableItem` usando lo stesso ID utilizzato per creare l'elemento e collegare un nuovo `CSSearchableItemAttributeSet` contenente gli attributi modificati:

[![](corespotlight-images/corespotlight02.png "L'aggiornamento di una panoramica di elemento")](corespotlight-images/corespotlight02.png#lightbox)

Quando questo elemento viene scritto all'indice di ricerca, l'elemento esistente viene aggiornato con le nuove informazioni.

## <a name="deleting-an-item"></a>Eliminazione di un elemento

Spotlight core offre molti modi per eliminare un indice di elemento quando non è più necessaria.

In primo luogo, è possibile eliminare un elemento dal relativo identificatore, ad esempio:

```csharp
// Delete Items by ID
CSSearchableIndex.DefaultSearchableIndex.Delete(new string[]{"1","16"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Successivamente, è possibile eliminare un gruppo di opzioni di indice in base al nome di dominio. Ad esempio:

```csharp
// Delete by Domain Name
CSSearchableIndex.DefaultSearchableIndex.DeleteWithDomain(new string[]{"domain-name"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Infine, è possibile eliminare tutte le voci di indice con il codice seguente:

```csharp
// Delete all index items
CSSearchableIndex.DefaultSearchableIndex.DeleteAll((error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```
## <a name="additional-core-spotlight-features"></a>Funzionalità principali aggiuntive in evidenza

Spotlight core presenta le seguenti funzionalità che consentono di mantenere l'indice accurate e aggiornate:

- **Supporto per gli aggiornamenti di batch** – se l'app deve creare o modificare un ampio gruppo di indici nello stesso momento, l'intero batch può essere inviato per il `Index` metodo del `CSSearchableIndex` classe in un'unica chiamata.
- **Rispondere alle modifiche di indice** -tramite il `CSSearchableIndexDelegate` l'app può rispondere alle modifiche e le notifiche dall'indice ricercabile.
- **Applicare la protezione dati** – utilizza le classi di protezione dati, è possibile implementare la sicurezza per gli elementi aggiunti all'indice ricercabile usando Spotlight Core.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [per gli sviluppatori iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida alla programmazione di ricerca nelle App](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
