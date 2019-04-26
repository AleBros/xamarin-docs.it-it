---
title: watchOS controlli immagine in Xamarin
description: Questo documento descrive come usare i controlli immagine in un'applicazione watchOS compilata con Xamarin. Viene illustrato il controllo WKInterfaceImage, il metodo SetImage, aggiunta di immagini in un'estensione di espressioni di controllo, animazioni e molto altro.
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 6a2b8c99156963ae167aecd29a618d0feeffbdc7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61229038"
---
# <a name="watchos-image-controls-in-xamarin"></a>watchOS controlli immagine in Xamarin

watchOS fornisce una [`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage) controllo per visualizzare immagini e animazioni semplici. Alcuni controlli possono avere anche un'immagine di sfondo (ad esempio pulsanti, gruppi e i controller di interfaccia).

![](image-images/image-walkway.png "Immagine che mostra Apple Watch") ![](image-images/image-animation.png "Apple Watch con animazione semplice")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Usare le immagini di catalogo di asset per aggiungere immagini per le app Watch Kit.
Solo **@2x** versioni sono obbligatorie, poiché tutti guardare i dispositivi hanno display Retina.

![](image-images/asset-universal-sml.png "Le versioni x solo 2 sono necessari, poiché tutti guardare i dispositivi hanno display Retina")

È buona norma assicurarsi che le immagini sono le dimensioni corrette per la visualizzazione di espressioni di controllo. *Evitare* usando immagini ridimensionate in modo non corretto (quelli particolarmente grandi) e la scalabilità per visualizzarli all'orologio.

È possibile usare le dimensioni dei Kit Watch (38mm e 42mm) in un'immagine di catalogo di asset per specificare diverse immagini per ogni dimensione di visualizzazione.

![](image-images/asset-watch-sml.png "È possibile usare il Kit di espressioni di controllo dimensioni di 38mm 42mm in un'immagine di catalogo di asset per specificare diverse immagini per ogni dimensione di visualizzazione")


## <a name="images-on-the-watch"></a>Immagini nelle espressioni di controllo

Il modo più efficiente per visualizzare le immagini consiste *includerli nel progetto dell'app watch* e visualizzarli tramite il `SetImage(string imageName)` (metodo).

Ad esempio, il [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog/) esempio ha un numero di immagini aggiunto a un catalogo di asset nel progetto dell'app watch:

![](image-images/asset-whale-sml.png "L'esempio WatchKitCatalog ha un numero di immagini aggiunto a un catalogo di asset nel progetto dell'app watch")

Questi possono essere efficiente caricati e visualizzati sull'uso di espressioni di controllo `SetImage` con il parametro di nome di stringa:

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Immagini di sfondo

Lo stesso vale per il `SetBackgroundImage (string imageName)` nella `Button`, `Group`, e `InterfaceController` classi. Archiviando le immagini nell'app watch sono ottenere prestazioni ottimali.


## <a name="images-in-the-watch-extension"></a>Immagini nell'estensione delle espressioni di controllo

Oltre a caricare le immagini archiviate nell'app watch, è possibile inviare immagini dal bundle di estensione per l'app watch per la visualizzazione (o è possibile scaricare le immagini da una postazione remota e visualizzare quelle).

Per caricare immagini dall'estensione per l'espressione di controllo, creare `UIImage` istanze e quindi chiamare `SetImage` con il `UIImage` oggetto.

Ad esempio, il [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) campione ha un'immagine denominata **posata** nel progetto di estensione delle espressioni di controllo:

![](image-images/asset-bumblebee-sml.png "L'esempio WatchKitCatalog dispone di un'immagine denominata posata nel progetto di estensione delle espressioni di controllo")

Comporterà il codice seguente:

- l'immagine viene caricata in memoria, e
- visualizzato nella finestra di espressioni di controllo.

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```


## <a name="animations"></a>Animations

Per aggiungere un'animazione a un set di immagini, si dovrebbe iniziano tutti con lo stesso prefisso e presentano un suffisso numerico.

Il [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) esempio presenta una serie di immagini numerate nel progetto dell'app watch con la **Bus** prefisso:

![](image-images/asset-bus-animation-sml.png "L'esempio WatchKitCatalog presenta una serie di immagini numerate nel progetto dell'app watch con il prefisso Bus")

Per visualizzare queste immagini come un'animazione, innanzitutto caricare l'immagine usando `SetImage` con il prefisso del nome e quindi chiamare `StartAnimating`:

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

Chiamare `StopAnimating` sul controllo immagine per interrompere l'animazione di ciclo:

```csharp
animatedImage.StopAnimating ();
```


<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>Appendice: La memorizzazione nella cache immagini (watchOS 1)

> [!IMPORTANT]
> le app watchOS 3 eseguite interamente sul dispositivo. Le informazioni seguenti sono per solo per le app watchOS 1.

Se l'applicazione usa più volte un'immagine che viene archiviata nell'estensione (o è stata scaricata), è possibile memorizzare nella cache l'immagine nell'archivio dell'orologio, per migliorare le prestazioni per gli schermi successive.

Usare la `WKInterfaceDevice`s `AddCachedImage` metodo per trasferire l'immagine per l'espressione di controllo e quindi usare `SetImage` con il parametro di nome immagine sotto forma di stringa per visualizzarlo:

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

È possibile eseguire query sul contenuto della cache delle immagini nel codice utilizzando `WKInterfaceDevice.CurrentDevice.WeakCachedImages`.


### <a name="managing-the-cache"></a>Gestione delle Cache

La cache di circa 20 MB di dimensioni. Viene mantenuta tra i riavvii dell'app e quando riempie ha la responsabilità per cancellare i file usando `RemoveCachedImage` oppure `RemoveAllCachedImages` metodi su di `WKInterfaceDevice.CurrentDevice` oggetto.



## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento immagine di Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Images.html)
