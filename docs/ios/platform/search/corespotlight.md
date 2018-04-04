---
title: Ricerca con Core Spotlight
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: d38d90ce460c7a93f8412baf372778443eb9d9e9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="search-with-core-spotlight"></a>Ricerca con Core Spotlight

Spotlight core è un nuovo framework per iOS 9 che presenta un'API di database simile per aggiungere, modificare o eliminare i collegamenti al contenuto all'interno dell'app. Gli elementi che sono stati aggiunti mediante Spotlight Core sarà disponibili alla ricerca Spotlight nel dispositivo iOS.

Per un esempio dei tipi di contenuto che possono essere indicizzate utilizzando Spotlight Core, esaminare i messaggi, posta elettronica, calendario e note di Apple app. Tutti attualmente usano Spotlight Core per fornire i risultati della ricerca.

## <a name="creating-an-item"></a>Creazione di un elemento

Di seguito è riportato un esempio di creazione di un elemento e di indicizzazione mediante Spotlight Core:

```csharp
using CoreSpotlight;
...

// Create attributes to describe an item
var attributes = new CSSearchableItemAttributeSet();
attributes.Title = "Test Cloud";
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

Questa informazione sarà simile alla seguente nei risultati di ricerca:

[![](corespotlight-images/corespotlight01.png "Informazioni generali sul risultato di ricerca Spotlight di base")](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>Il ripristino di un elemento

Quando l'utente tocca su un elemento aggiunto al risultato di ricerca tramite Spotlight Core per l'app, il `AppDelegate` metodo `ContinueUserActivity` viene chiamato (questo metodo viene utilizzato anche per `NSUserActivity`). Ad esempio:

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

Si noti che questo tempo è controllo per l'attività con un `ActivityType` di `CSSearchableItem.ActionType`.

## <a name="updating-an-item"></a>Aggiornamento di un elemento

Talvolta potrebbe essere quando un elemento di indice viene creati con Spotlight Core è necessario modificare, ad esempio una modifica nel titolo o immagine di anteprima è obbligatoria. Per apportare questa modifica, è utilizzare lo stesso metodo utilizzato per creare l'indice.
Si crea un nuovo `CSSearchableItem` utilizzando lo stesso ID utilizzato per creare l'elemento e collegare un nuovo `CSSearchableItemAttributeSet` contenente attributi modificati:

[![](corespotlight-images/corespotlight02.png "L'aggiornamento a una panoramica di elemento")](corespotlight-images/corespotlight02.png#lightbox)

Quando questo elemento viene scritto l'indice di ricerca, l'elemento esistente viene aggiornato con le nuove informazioni.

## <a name="deleting-an-item"></a>Eliminazione di un elemento

Core Spotlight offre molti modi per eliminare una voce di indice quando non è più necessario.

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

Successivamente, è possibile eliminare un gruppo di elementi di indice in base al nome di dominio. Ad esempio:

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
## <a name="additional-core-spotlight-features"></a>Funzionalità di base aggiuntiva Spotlight

Core Spotlight presenta le seguenti funzionalità che consentono di mantenere l'indice accurate e aggiornate:

- **Il supporto dell'aggiornamento batch** : se l'app deve creare o modificare un ampio gruppo di indici allo stesso tempo, è possibile inviare l'intero batch il `Index` metodo il `CSSearchableIndex` classe in un'unica chiamata.
- **Rispondere alle modifiche di indice** -tramite il `CSSearchableIndexDelegate` app può rispondere alle modifiche e le notifiche dall'indice di ricerca.
- **Applicare la protezione dati** : utilizzo delle classi di protezione dati, è possibile implementare la sicurezza sugli elementi che aggiungono e l'indice di ricerca usando Spotlight Core.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 per gli sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida per programmatori di ricerca di App](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
