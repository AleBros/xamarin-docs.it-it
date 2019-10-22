---
title: Controlli immagine watchos in Novell
description: Questo documento descrive come usare i controlli immagine in un'applicazione watchos compilata con Novell. Viene illustrato il controllo WKInterfaceImage, il metodo seimagine, aggiungendo immagini a un'estensione del controllo, animazioni e altro ancora.
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: f9367eda7651ca61a8a3cb0928ad11cb320faab6
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70769966"
---
# <a name="watchos-image-controls-in-xamarin"></a>Controlli immagine watchos in Novell

watchos fornisce un controllo [`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage) per la visualizzazione di immagini e animazioni semplici. Alcuni controlli possono anche avere un'immagine di sfondo, ad esempio pulsanti, gruppi e controller di interfaccia.

![](image-images/image-walkway.png "Apple Watch la visualizzazione dell'immagine") ![](image-images/image-animation.png "Apple Watch con animazione semplice")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Usare le immagini del catalogo asset per aggiungere immagini alle app Watch Kit.
Sono necessarie solo **@2x** versioni, dal momento che tutti i dispositivi Watch hanno schermi Retina.

![](image-images/asset-universal-sml.png "Only 2x versions are required, since all watch devices have Retina displays")

È consigliabile assicurarsi che le immagini stesse siano le dimensioni corrette per la visualizzazione del controllo. *Evitare* di usare immagini con dimensioni non corrette (in particolare quelle di grandi dimensioni) e ridimensionarle per visualizzarle nell'orologio.

È possibile usare le dimensioni del kit di controllo (38 mm e 42 mm) in un'immagine del catalogo asset per specificare immagini diverse per ogni dimensione di visualizzazione.

![](image-images/asset-watch-sml.png "You can use the Watch Kit sizes 38mm and 42mm in an asset catalog image to specify different images for each display size")

## <a name="images-on-the-watch"></a>Immagini nell'orologio

Il modo più efficiente per visualizzare le immagini consiste *nell'includerli nel progetto Watch app* e visualizzarli usando il metodo `SetImage(string imageName)`.

Ad esempio, l'esempio [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog/) include una serie di immagini aggiunte a un catalogo asset nel progetto Watch app:

![](image-images/asset-whale-sml.png "The WatchKitCatalog sample has a number of images added to an asset catalog in the watch app project")

Questi possono essere caricati e visualizzati in modo efficiente nell'espressione di controllo usando `SetImage` con il parametro nome stringa:

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Immagini di sfondo

La stessa logica si applica al `SetBackgroundImage (string imageName)` nelle classi `Button`, `Group` e `InterfaceController`. Per ottenere prestazioni ottimali, archiviare le immagini nell'app Watch.

## <a name="images-in-the-watch-extension"></a>Immagini nell'estensione Watch

Oltre a caricare immagini archiviate nell'app Watch stessa, è possibile inviare immagini dal bundle di estensione all'app Watch per la visualizzazione (oppure è possibile scaricare immagini da una posizione remota e visualizzarle).

Per caricare immagini dall'estensione Watch, creare `UIImage` istanze e quindi chiamare `SetImage` con l'oggetto `UIImage`.

Ad esempio, l'esempio [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) include un'immagine denominata **Bumblebee** nel progetto di estensione Watch:

![](image-images/asset-bumblebee-sml.png "The WatchKitCatalog sample has an image named Bumblebee in the watch extension project")

Il codice seguente comporterà:

- immagine caricata in memoria e
- visualizzato sull'orologio.

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```

## <a name="animations"></a>Animations

Per animare un set di immagini, è necessario che tutti comincino con lo stesso prefisso e abbiano un suffisso numerico.

L'esempio [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) include una serie di immagini numerate nel progetto di app Watch con il prefisso **bus** :

![](image-images/asset-bus-animation-sml.png "The WatchKitCatalog sample has a series of numbered images in the watch app project with the Bus prefix")

Per visualizzare queste immagini come un'animazione, caricare prima l'immagine usando `SetImage` con il nome del prefisso e quindi chiamare `StartAnimating`:

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

Chiamare `StopAnimating` sul controllo immagine per arrestare il ciclo di animazione:

```csharp
animatedImage.StopAnimating ();
```

<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>Appendice: memorizzazione nella cache delle immagini (watchos 1)

> [!IMPORTANT]
> le app watchos 3 vengono eseguite interamente sul dispositivo. Le informazioni seguenti sono destinate solo alle app watchos 1.

Se l'applicazione usa ripetutamente un'immagine archiviata nell'estensione (o è stata scaricata), è possibile memorizzare nella cache l'immagine nell'archivio dell'espressione di controllo per migliorare le prestazioni per le visualizzazioni successive.

Usare il metodo `WKInterfaceDevice`s `AddCachedImage` per trasferire l'immagine nell'espressione di controllo e quindi usare `SetImage` con il parametro Image Name come stringa per visualizzarla:

```csharp
var device = WKInterfaceDevice.CurrentDevice;
using (var image = UIImage.FromBundle ("Bumblebee")) {
    if (!device.AddCachedImage (image, "Bumblebee")) {
            Console.WriteLine ("Image cache full.");
        } else {
            cachedImage.SetImage ("Bumblebee");
        }
    }
}
```

È possibile eseguire una query sul contenuto della cache immagini nel codice usando `WKInterfaceDevice.CurrentDevice.WeakCachedImages`.

### <a name="managing-the-cache"></a>Gestione della cache

La cache di circa 20 MB di dimensioni. Viene mantenuta tra i riavvii dell'app e, quando si riempie, è responsabilità dell'utente cancellare i file usando `RemoveCachedImage` o `RemoveAllCachedImages` metodi sull'oggetto `WKInterfaceDevice.CurrentDevice`.

## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documento immagine Apple](https://developer.apple.com/documentation/watchkit/wkinterfaceimage)
