---
title: PhotoKit in Novell. iOS
description: Questo documento descrive PhotoKit, illustrando gli oggetti del modello, come eseguire query sui dati del modello e salvare le modifiche apportate alla raccolta foto.
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 5e5cc20e9fbeaf2b00e022ccdbf67286aed6d5ef
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528820"
---
# <a name="photokit-in-xamarinios"></a>PhotoKit in Novell. iOS

PhotoKit è un nuovo Framework che consente alle applicazioni di eseguire query nella libreria di immagini del sistema e creare interfacce utente personalizzate per visualizzarne e modificarne il contenuto. Include varie classi che rappresentano asset di immagini e video, nonché raccolte di asset come album e cartelle.

## <a name="model-objects"></a>Oggetti modello

PhotoKit rappresenta gli asset in cui vengono chiamati gli oggetti modello. Gli oggetti modello che rappresentano le foto e i video sono di tipo `PHAsset`. Un `PHAsset` oggetto contiene i metadati, ad esempio il tipo di supporto e la data di creazione dell'asset.
Analogamente, `PHAssetCollection` le `PHCollectionList` classi e contengono i metadati relativi alle raccolte di asset e agli elenchi di raccolte rispettivamente. Le raccolte di asset sono gruppi di asset, ad esempio tutte le foto e i video per un determinato anno. Analogamente, gli elenchi di raccolte sono gruppi di raccolte di asset, ad esempio foto e video raggruppati in base all'anno.

## <a name="querying-model-data"></a>Esecuzione di query sui dati del modello

PhotoKit consente di eseguire query sui dati del modello in modo semplice tramite diversi metodi di recupero. Per recuperare tutte le immagini, ad esempio, è necessario `PFAsset.Fetch`chiamare, passando `PHAssetMediaType.Image` il tipo di supporto.

```csharp
PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);
```

L' `PHFetchResult` istanza conterrà quindi tutte le `PFAsset` istanze che rappresentano immagini. Per ottenere le immagini, usare `PHImageManager` (o la `PHCachingImageManager`versione di Caching) per effettuare una richiesta `RequestImageForAsset`per l'immagine chiamando. Il codice seguente, ad esempio, recupera un'immagine per ogni asset in `PHFetchResult` un oggetto da visualizzare in una cella di visualizzazione raccolta:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
    imageMgr.RequestImageForAsset (
        (PHAsset)fetchResults [(uint)indexPath.Item],
        thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (),
        (img, info) => {
            imageCell.ImageView.Image = img;
        }
    );
    return imageCell;
}
```

In questo modo si ottiene una griglia di immagini, come illustrato di seguito:

![](photokit-images/image4.png "L'app in esecuzione che visualizza una griglia di immagini")

## <a name="saving-changes-to-the-photo-library"></a>Salvataggio delle modifiche apportate alla raccolta foto

Questo è il modo di gestire l'esecuzione di query e la lettura dei dati. È anche possibile scrivere di nuovo le modifiche nella libreria. Poiché più applicazioni interessate sono in grado di interagire con la libreria foto di sistema, è possibile registrare un Observer per ricevere una notifica delle modifiche tramite un PhotoLibraryObserver. Quindi, quando vengono apportate modifiche, l'applicazione può essere aggiornata di conseguenza. Ad esempio, di seguito è riportata una semplice implementazione per ricaricare la visualizzazione della raccolta:

```csharp
class PhotoLibraryObserver : PHPhotoLibraryChangeObserver
{
    readonly PhotosViewController controller;
    public PhotoLibraryObserver (PhotosViewController controller)

    {
        this.controller = controller;
    }

    public override void PhotoLibraryDidChange (PHChange changeInstance)
    {
        DispatchQueue.MainQueue.DispatchAsync (() => {
        var changes = changeInstance.GetFetchResultChangeDetails (controller.fetchResults);
        controller.fetchResults = changes.FetchResultAfterChanges;
        controller.CollectionView.ReloadData ();
        });
    }
}
```

Per scrivere effettivamente le modifiche dall'applicazione, si crea una richiesta di modifica. A ognuna delle classi del modello è associata una classe di richiesta di modifica. Per modificare, ad esempio, un PHAsset, è necessario creare un PHAssetChangeRequest. I passaggi per eseguire le modifiche riscritte nella raccolta foto e inviati agli osservatori come quello precedente sono:

- Eseguire l'operazione di modifica.
- Salvare i dati delle immagini filtrate in un'istanza di PHContentEditingOutput.
- Apportare una richiesta di modifica per pubblicare le modifiche nell'output di modifica.

Di seguito è riportato un esempio che scrive una modifica in un'immagine che applica un filtro di base dell'immagine Noir:

```csharp
void ApplyNoirFilter (object sender, EventArgs e)
{

    Asset.RequestContentEditingInput (new PHContentEditingInputRequestOptions (), (input, options) => {

        // perform the editing operation, which applies a noir filter in this case
        var image = CIImage.FromUrl (input.FullSizeImageUrl);
        image = image.CreateWithOrientation((CIImageOrientation)input.FullSizeImageOrientation);
        var noir = new CIPhotoEffectNoir {
            Image = image
        };
        var ciContext = CIContext.FromOptions (null);
        var output = noir.OutputImage;
        var uiImage = UIImage.FromImage (ciContext.CreateCGImage (output, output.Extent));
        imageView.Image = uiImage;
        //
        // save the filtered image data to a PHContentEditingOutput instance
        var editingOutput = new PHContentEditingOutput(input);
        var adjustmentData = new PHAdjustmentData();
        var data = uiImage.AsJPEG();
        NSError error;
        data.Save(editingOutput.RenderedContentUrl, false, out error);
        editingOutput.AdjustmentData = adjustmentData;
        //
        // make a change request to publish the changes form the editing output
        PHPhotoLibrary.GetSharedPhotoLibrary.PerformChanges (() => {
            PHAssetChangeRequest request = PHAssetChangeRequest.ChangeRequest(Asset);
            request.ContentEditingOutput = editingOutput;
        },
        (ok, err) => Console.WriteLine ("photo updated successfully: {0}", ok));
    });
}
```

Quando l'utente seleziona il pulsante, viene applicato il filtro:

![](photokit-images/image5.png "Esempio di filtro applicato")

Grazie al PHPhotoLibraryChangeObserver, la modifica viene riflessa nella visualizzazione della raccolta quando l'utente torna indietro:

![](photokit-images/image6.png "La modifica viene riflessa nella visualizzazione della raccolta quando l'utente si sposta indietro")
