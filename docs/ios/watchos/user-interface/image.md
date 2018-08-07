---
title: watchOS controlli immagine in Xamarin
description: Questo documento descrive come usare i controlli immagine in un'applicazione watchOS compilata con Xamarin. Viene descritto il controllo WKInterfaceImage, il metodo SetImage, aggiunta di immagini in un'estensione di espressioni di controllo, animazioni e molto altro.
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: eb58c587f737a5991a21f0efe9964353a8ab0399
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791251"
---
# <a name="watchos-image-controls-in-xamarin"></a>watchOS controlli immagine in Xamarin

watchOS fornisce un [ `WKInterfaceImage` ](https://developer.xamarin.com/api/type/WatchKit.WKInterfaceImage/) controllo per visualizzare le immagini e le animazioni semplice. Alcuni controlli possono anche avere un'immagine di sfondo (ad esempio pulsanti, gruppi e i controller di interfaccia).

![](image-images/image-walkway.png "Immagine che mostra Apple Watch") ![](image-images/image-animation.png "Apple Watch con animazione semplice")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Per aggiungere immagini al Kit di espressioni di controllo App, usare le immagini di catalogo di asset.
Solo **@2x** versioni sono necessarie, perché tutti guardare i dispositivi hanno display Retina.

![](image-images/asset-universal-sml.png "Le versioni x solo 2 sono necessarie, perché tutti guardare i dispositivi hanno display Retina")

È consigliabile verificare che le immagini siano di dimensioni corrette per la visualizzazione di espressioni di controllo. *Evitare* l'utilizzo di immagini ridimensionate in modo non corretto (quelli particolarmente grande) e la scalabilità per visualizzarli nelle espressioni di controllo.

È possibile utilizzare le dimensioni del Kit di espressioni di controllo (38mm e 42mm) in un'immagine di catalogo di asset per specificare immagini diverse per ogni dimensione di visualizzazione.

![](image-images/asset-watch-sml.png "È possibile utilizzare le dimensioni delle espressioni di controllo Kit 38 e mm 42 in un'immagine di catalogo di asset per specificare immagini diverse per ogni dimensione di visualizzazione")


## <a name="images-on-the-watch"></a>Immagini di espressioni di controllo

Il modo più efficiente per visualizzare le immagini è *includerli nel progetto di applicazione delle espressioni di controllo* e visualizzarli utilizzando il `SetImage(string imageName)` metodo.

Ad esempio, il [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog/) esempio dispone di numerose immagini aggiunti a un catalogo di asset nel progetto di applicazione delle espressioni di controllo:

![](image-images/asset-whale-sml.png "L'esempio WatchKitCatalog ha un numero di immagini aggiunti a un catalogo di asset nel progetto di applicazione delle espressioni di controllo")

Questi possono essere in modo efficiente caricati e visualizzati sull'utilizzo di espressioni di controllo `SetImage` con il parametro di nome di stringa:

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Immagini di sfondo

Si applica la stessa logica per il `SetBackgroundImage (string imageName)` sul `Button`, `Group`, e `InterfaceController` classi. Ottimizzare le prestazioni è necessario archiviare le immagini nell'app stessa espressione di controllo.


## <a name="images-in-the-watch-extension"></a>Immagini nell'estensione di espressioni di controllo

Oltre a caricare immagini archiviati nell'app stessa espressione di controllo, è possibile inviare immagini dal bundle di estensione per l'applicazione di espressioni di controllo per la visualizzazione oppure è possibile scaricare le immagini da una postazione remota e visualizzare tali.

Per caricare immagini dall'estensione per l'espressione di controllo, creare `UIImage` istanze e quindi chiamare `SetImage` con il `UIImage` oggetto.

Ad esempio, il [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) esempio dispone di un'immagine denominata **posata** nel progetto di estensione delle espressioni di controllo:

![](image-images/asset-bumblebee-sml.png "L'esempio WatchKitCatalog dispone di un'immagine denominata posata nel progetto di estensione delle espressioni di controllo")

Comporterà il codice seguente:

- l'immagine viene caricata in memoria, e
- visualizzata nella finestra di controllo.

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```


## <a name="animations"></a>Animations

Per aggiungere un'animazione a un set di immagini, che devono effettivamente iniziano tutti con lo stesso prefisso e avere un suffisso numerico.

Il [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) esempio dispone di una serie di immagini numerate nel progetto dell'app di espressioni di controllo con il **Bus** prefisso:

![](image-images/asset-bus-animation-sml.png "L'esempio WatchKitCatalog dispone di una serie di immagini numerate nel progetto dell'app di espressioni di controllo con il prefisso Bus")

Per visualizzare queste immagini come un'animazione, innanzitutto caricare l'immagine usando `SetImage` con il prefisso del nome e quindi chiamare `StartAnimating`:

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

Chiamare `StopAnimating` sul controllo immagine per interrompere il ciclo di animazione:

```csharp
animatedImage.StopAnimating ();
```


<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>Appendice: La memorizzazione nella cache di immagini (watchOS 1)

> [!IMPORTANT]
> le app watchOS 3 eseguite interamente nel dispositivo. Le informazioni seguenti sono watchOS 1 solo per le app.

Se l'applicazione usa più volte un'immagine che viene archiviata nell'estensione (o è stata scaricata), è possibile memorizzare nella cache l'immagine nel servizio di archiviazione dell'orologio, per migliorare le prestazioni per gli schermi successive.

Utilizzare il `WKInterfaceDevice`s `AddCachedImage` per trasferire l'immagine per le espressioni di controllo e quindi utilizzare `SetImage` con il parametro di nome immagine sotto forma di stringa per visualizzarlo:

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

È possibile eseguire una query il contenuto della cache delle immagini nel codice utilizzando `WKInterfaceDevice.CurrentDevice.WeakCachedImages`.


### <a name="managing-the-cache"></a>Gestione delle Cache

La cache di circa 20 MB di dimensioni. Viene mantenuto tra i vari riavvi app e quando riempie ha la responsabilità per cancellare i file usando `RemoveCachedImage` o `RemoveAllCachedImages` metodi di `WKInterfaceDevice.CurrentDevice` oggetto.



## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Documento di immagine di Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Images.html)
