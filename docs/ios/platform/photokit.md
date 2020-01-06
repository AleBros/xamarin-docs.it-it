---
title: PhotoKit in Novell. iOS
description: Questo documento descrive PhotoKit, illustrando gli oggetti del modello, come eseguire query sui dati del modello e salvare le modifiche apportate alla raccolta foto.
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: def34efd1fd48cc0e7dd802a6d3e843be1e156a4
ms.sourcegitcommit: 5ddb107b0a56bef8a16fce5bc6846f9673b3b22e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75558807"
---
# <a name="photokit-in-xamarinios"></a>PhotoKit in Novell. iOS

[![scaricare l'esempio](~/media/shared/download.png) scaricare un esempio di codice](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-samplephotoapp/)

PhotoKit è un Framework che consente alle applicazioni di eseguire query nella libreria di immagini del sistema e creare interfacce utente personalizzate per visualizzarne e modificarne il contenuto. Include varie classi che rappresentano asset di immagini e video, nonché raccolte di asset come album e cartelle.

## <a name="permissions"></a>Autorizzazioni

Prima che l'app possa accedere alla raccolta foto, l'utente verrà visualizzato con una finestra di dialogo delle autorizzazioni. È necessario fornire il testo esplicativo nel file **info. plist** per spiegare in che modo l'app usa la raccolta foto, ad esempio:

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Applies filters to photos and updates the original image</string>
```

## <a name="model-objects"></a>Oggetti modello

PhotoKit rappresenta gli asset in cui vengono chiamati gli oggetti modello. Gli oggetti modello che rappresentano le foto e i video sono di tipo `PHAsset`. Un `PHAsset` contiene metadati, ad esempio il tipo di supporto e la data di creazione dell'asset.
Analogamente, le classi `PHAssetCollection` e `PHCollectionList` contengono rispettivamente i metadati relativi alle raccolte di asset e agli elenchi di raccolte. Le raccolte di asset sono gruppi di asset, ad esempio tutte le foto e i video per un determinato anno. Analogamente, gli elenchi di raccolte sono gruppi di raccolte di asset, ad esempio foto e video raggruppati in base all'anno.

## <a name="querying-model-data"></a>Esecuzione di query sui dati del modello

PhotoKit consente di eseguire query sui dati del modello in modo semplice tramite diversi metodi di recupero. Per recuperare tutte le immagini, ad esempio, è necessario chiamare `PHAsset.Fetch`, passando il tipo di supporto `PHAssetMediaType.Image`.

```csharp
PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);
```

L'istanza di `PHFetchResult` conterrebbe quindi tutte le istanze di `PHAsset` che rappresentano le immagini. Per ottenere le immagini, si usa il `PHImageManager` (o la versione di Caching `PHCachingImageManager`) per effettuare una richiesta per l'immagine chiamando `RequestImageForAsset`. Il codice seguente, ad esempio, recupera un'immagine per ogni asset in un `PHFetchResult` da visualizzare in una cella di visualizzazione raccolta:

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

![](photokit-images/image4.png "The running app displaying a grid of images")

## <a name="saving-changes-to-the-photo-library"></a>Salvataggio delle modifiche apportate alla raccolta foto

Questo è il modo di gestire l'esecuzione di query e la lettura dei dati. È anche possibile scrivere di nuovo le modifiche nella libreria. Poiché più applicazioni interessate sono in grado di interagire con la libreria foto di sistema, è possibile registrare un Observer per ricevere una notifica delle modifiche utilizzando un `PhotoLibraryObserver`. Quindi, quando vengono apportate modifiche, l'applicazione può essere aggiornata di conseguenza. Ad esempio, di seguito è riportata una semplice implementazione per ricaricare la visualizzazione della raccolta:

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

Per scrivere effettivamente le modifiche dall'applicazione, si crea una richiesta di modifica. A ognuna delle classi del modello è associata una classe di richiesta di modifica. Per modificare un `PHAsset`, ad esempio, è possibile creare una `PHAssetChangeRequest`. I passaggi per eseguire le modifiche riscritte nella raccolta foto e inviati agli osservatori come quello precedente sono:

1. Eseguire l'operazione di modifica.
2. Salvare i dati delle immagini filtrate in un'istanza di `PHContentEditingOutput`.
3. Apportare una richiesta di modifica per pubblicare le modifiche dall'output di modifica.

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

![Due esempi, che mostrano la foto prima e dopo l'applicazione del filtro](photokit-images/image5.png)

Grazie alla `PHPhotoLibraryChangeObserver`, la modifica viene riflessa nella visualizzazione della raccolta quando l'utente torna indietro:

![Visualizzazione raccolta foto che mostra la foto modificata](photokit-images/image6.png)
