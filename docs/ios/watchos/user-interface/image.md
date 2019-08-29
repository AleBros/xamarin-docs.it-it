---
title: Controlli immagine watchos in Novell
description: Questo documento descrive come usare i controlli immagine in un'applicazione watchos compilata con Novell. Viene illustrato il controllo WKInterfaceImage, il metodo seimagine, aggiungendo immagini a un'estensione del controllo, animazioni e altro ancora.
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: d97f914a557839a2ca1322f803894055b6156c7e
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065345"
---
# <a name="watchos-image-controls-in-xamarin"></a>Controlli immagine watchos in Novell

watchOS fornisce una [`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage) controllo per visualizzare immagini e animazioni semplici. Alcuni controlli possono anche avere un'immagine di sfondo, ad esempio pulsanti, gruppi e controller di interfaccia.

![](image-images/image-walkway.png "Immagine che mostra Apple Watch") ![](image-images/image-animation.png "Apple Watch con animazione semplice")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Usare le immagini del catalogo asset per aggiungere immagini alle app Watch Kit.
Solo **@2x** versioni sono obbligatorie, poiché tutti guardare i dispositivi hanno display Retina.

![](image-images/asset-universal-sml.png "Sono necessarie solo le versioni 2x, perché tutti i dispositivi Watch hanno schermi Retina")

È consigliabile assicurarsi che le immagini stesse siano le dimensioni corrette per la visualizzazione del controllo. *Evitare* di usare immagini con dimensioni non corrette (in particolare quelle di grandi dimensioni) e ridimensionarle per visualizzarle nell'orologio.

È possibile usare le dimensioni del kit di controllo (38 mm e 42 mm) in un'immagine del catalogo asset per specificare immagini diverse per ogni dimensione di visualizzazione.

![](image-images/asset-watch-sml.png "È possibile usare il kit Watch size 38 mm e 42 millimetri in un'immagine del catalogo asset per specificare immagini diverse per ogni dimensione di visualizzazione")


## <a name="images-on-the-watch"></a>Immagini nell'orologio

Il modo più efficiente per visualizzare le immagini consiste *nell'includerli nel progetto Watch app* e visualizzarli usando il `SetImage(string imageName)` metodo.

Ad esempio, l'esempio [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog/) include una serie di immagini aggiunte a un catalogo asset nel progetto Watch app:

![](image-images/asset-whale-sml.png "L'esempio WatchKitCatalog include una serie di immagini aggiunte a un catalogo asset nel progetto Watch app")

Questi possono essere caricati e visualizzati in modo efficiente nell'espressione `SetImage` di controllo usando con il parametro nome stringa:

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Immagini di sfondo

La stessa logica si applica per `SetBackgroundImage (string imageName)` le `Button`classi, `Group`e `InterfaceController` . Per ottenere prestazioni ottimali, archiviare le immagini nell'app Watch.


## <a name="images-in-the-watch-extension"></a>Immagini nell'estensione Watch

Oltre a caricare immagini archiviate nell'app Watch stessa, è possibile inviare immagini dal bundle di estensione all'app Watch per la visualizzazione (oppure è possibile scaricare immagini da una posizione remota e visualizzarle).

Per caricare immagini dall'estensione Watch, creare `UIImage` istanze e quindi chiamare `SetImage` con l' `UIImage` oggetto.

Ad esempio, l'esempio [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) include un'immagine denominata **Bumblebee** nel progetto di estensione Watch:

![](image-images/asset-bumblebee-sml.png "L'esempio WatchKitCatalog include un'immagine denominata Bumblebee nel progetto di estensione Watch")

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

![](image-images/asset-bus-animation-sml.png "L'esempio WatchKitCatalog include una serie di immagini numerate nel progetto di app Watch con il prefisso bus")

Per visualizzare queste immagini come un'animazione, caricare prima l'immagine usando `SetImage` con il nome del prefisso e quindi `StartAnimating`chiamare:

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

Chiamare `StopAnimating` sul controllo immagine per arrestare il ciclo di animazione:

```csharp
animatedImage.StopAnimating ();
```


<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>Appendice: Memorizzazione nella cache di immagini (watchos 1)

> [!IMPORTANT]
> le app watchos 3 vengono eseguite interamente sul dispositivo. Le informazioni seguenti sono destinate solo alle app watchos 1.

Se l'applicazione usa ripetutamente un'immagine archiviata nell'estensione (o è stata scaricata), è possibile memorizzare nella cache l'immagine nell'archivio dell'espressione di controllo per migliorare le prestazioni per le visualizzazioni successive.

Usare il `WKInterfaceDevice`metodo `AddCachedImage` s per trasferire l'immagine nell'espressione di controllo e quindi usare `SetImage` con il parametro Image Name come stringa per visualizzarla:

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

È possibile eseguire una query sul contenuto della cache immagini nel codice `WKInterfaceDevice.CurrentDevice.WeakCachedImages`usando.


### <a name="managing-the-cache"></a>Gestione della cache

La cache di circa 20 MB di dimensioni. Viene mantenuta tra i riavvii dell'app e, quando si riempie, è responsabilità dell'utente cancellare i file `RemoveCachedImage` usando `RemoveAllCachedImages` i `WKInterfaceDevice.CurrentDevice` metodi o nell'oggetto.



## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documento immagine Apple](https://developer.apple.com/documentation/watchkit/wkinterfaceimage)
