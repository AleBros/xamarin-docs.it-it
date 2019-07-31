---
title: Eseguire una ricerca con core Spotlight in Novell. iOS
description: Questo documento descrive come usare Spotlight principale in un'applicazione Novell. iOS per fornire collegamenti al contenuto in-app. Viene illustrato come creare, ripristinare, aggiornare ed eliminare elementi disponibili per la ricerca.
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: acf065a275b28863c5133f764a7f7b1f87127887
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654206"
---
# <a name="search-with-core-spotlight-in-xamarinios"></a>Eseguire una ricerca con core Spotlight in Novell. iOS

Core Spotlight è un nuovo Framework per iOS 9 che presenta un'API simile a un database per aggiungere, modificare o eliminare collegamenti al contenuto all'interno dell'app. Gli elementi che sono stati aggiunti con la finestra Spotlight principale saranno disponibili nella ricerca Spotlight nel dispositivo iOS.

Per un esempio dei tipi di contenuto che possono essere indicizzati usando i principali Spotlight, vedere le app messaggi, posta elettronica, calendario e note di Apple. Attualmente utilizzano i principali Spotlight per fornire i risultati della ricerca.

## <a name="creating-an-item"></a>Creazione di un elemento

Di seguito è riportato un esempio di creazione di un elemento e di indicizzazione tramite Core Spotlight:

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

Queste informazioni sono simili alle seguenti nei risultati della ricerca:

[![](corespotlight-images/corespotlight01.png "Panoramica sui risultati della ricerca principale di Spotlight")](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>Ripristino di un elemento

Quando l'utente tocca un elemento aggiunto al risultato della ricerca tramite i principali Spotlight per l'app, viene `AppDelegate` chiamato `ContinueUserActivity` il metodo (questo metodo viene usato anche per `NSUserActivity`). Ad esempio:

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

Si noti che questa volta viene verificata la presenza di un' `ActivityType` attività `CSSearchableItem.ActionType`di.

## <a name="updating-an-item"></a>Aggiornamento di un elemento

In alcuni casi potrebbe essere necessario modificare un elemento dell'indice creato con il componente Spotlight principale, ad esempio una modifica nel titolo o nell'immagine di anteprima. Per apportare questa modifica, viene usato lo stesso metodo usato per creare inizialmente l'indice.
Viene creato un nuovo `CSSearchableItem` usando lo stesso ID utilizzato per creare l'elemento e alleghi un nuovo `CSSearchableItemAttributeSet` oggetto contenente gli attributi modificati:

[![](corespotlight-images/corespotlight02.png "Panoramica sull'aggiornamento di un elemento")](corespotlight-images/corespotlight02.png#lightbox)

Quando questo elemento viene scritto nell'indice ricercabile, l'elemento esistente viene aggiornato con le nuove informazioni.

## <a name="deleting-an-item"></a>Eliminazione di un elemento

Core Spotlight offre diversi modi per eliminare un elemento di indice quando non è più necessario.

In primo luogo, è possibile eliminare un elemento in base al relativo identificatore, ad esempio:

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

Infine, è possibile eliminare tutti gli elementi dell'indice con il codice seguente:

```csharp
// Delete all index items
CSSearchableIndex.DefaultSearchableIndex.DeleteAll((error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```
## <a name="additional-core-spotlight-features"></a>Funzionalità aggiuntive di base di Spotlight

In Spotlight principale sono disponibili le seguenti funzionalità che consentono di garantire la correttezza e l'aggiornamento dell'indice:

- **Supporto per aggiornamenti batch** : se l'app deve creare o modificare contemporaneamente un gruppo di indici di grandi dimensioni, l'intero batch può essere inviato al `Index` metodo della `CSSearchableIndex` classe in un'unica chiamata.
- **Rispondere alle modifiche dell'indice** : l' `CSSearchableIndexDelegate` uso dell'app può rispondere alle modifiche e alle notifiche dall'indice ricercabile.
- **Applicare la protezione dei dati** : usando le classi di protezione dei dati, è possibile implementare la sicurezza per gli elementi aggiunti all'indice in cui è possibile eseguire ricerche usando Core Spotlight.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 per sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guida alla programmazione di app Search](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
