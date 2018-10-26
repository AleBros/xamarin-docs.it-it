---
title: PhotoKit in xamarin. IOS
description: Questo documento descrive PhotoKit, che illustrano gli oggetti modello e come eseguire query sui dati del modello e il salvataggio delle modifiche alla libreria di foto.
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: d59cac9403a244ce553d84e0590b8a9c3d4d2f30
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102711"
---
# <a name="photokit-in-xamarinios"></a>PhotoKit in xamarin. IOS

PhotoKit è un framework nuovo che consente alle applicazioni di eseguire una query nella raccolta di immagini di sistema e creare interfacce utente personalizzate per visualizzare e modificare il relativo contenuto. Include una serie di classi che rappresentano gli asset video e immagine, nonché le raccolte di risorse, ad esempio gli album e cartelle.

## <a name="model-objects"></a>Oggetti modello

PhotoKit rappresenta questi asset in ciò che chiama gli oggetti del modello. Gli oggetti modello che rappresentano le foto e video su se stessi sono di tipo `PHAsset`. Oggetto `PHAsset` contiene i metadati, ad esempio la data di creazione e il tipo di supporto dell'asset.
Analogamente, il `PHAssetCollection` e `PHCollectionList` classi contengono rispettivamente i metadati sull'asset raccolte e gli elenchi di raccolta. Le raccolte di asset sono gruppi di risorse, ad esempio tutte le foto e video per un determinato anno. Analogamente, gli elenchi di raccolta sono gruppi di raccolte di asset, ad esempio foto e video raggruppati in base all'anno.

## <a name="querying-model-data"></a>L'esecuzione di query dei dati del modello

PhotoKit rende facile eseguire query sui dati del modello tramite un'ampia gamma di metodi di recupero. Ad esempio, per recuperare tutte le immagini, chiamare `PFAsset.Fetch`, passando il `PHAssetMediaType.Image` il tipo di supporto.

    PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);

Il `PHFetchResult` istanza conterrà tutti i `PFAsset` istanze che rappresentano le immagini. Per ottenere le immagini, si utilizza il `PHImageManager` (o la versione, la memorizzazione nella cache `PHCachingImageManager`) per effettuare una richiesta per l'immagine chiamando `RequestImageForAsset`. Ad esempio, il codice seguente recupera un'immagine per ogni asset in un `PHFetchResult` da visualizzare in una cella di visualizzazione di raccolta:


    public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
    {
              var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
            imageMgr.RequestImageForAsset ((PHAsset)fetchResults [(uint)indexPath.Item], thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (), (img, info) => {
            imageCell.ImageView.Image = img;
        });
        return imageCell;
    }

Ciò comporta una griglia delle immagini, come illustrato di seguito:

![](photokit-images/image4.png "L'app in esecuzione, visualizzazione di una griglia delle immagini")
 
## <a name="saving-changes-to-the-photo-library"></a>Salvataggio delle modifiche alla libreria di foto

Questo è il modo gestire l'esecuzione di query e la lettura dei dati. È anche possibile scrivere nuovamente le modifiche apportate alla libreria. Poiché più applicazioni interessate sono in grado di interagire con la libreria foto di sistema, è possibile registrare un observer per ricevere una notifica delle modifiche usando un PhotoLibraryObserver. Quindi, quando le modifiche sono disponibili in, l'applicazione può aggiornare di conseguenza. Ad esempio, ecco una semplice implementazione di ricaricare la visualizzazione di raccolta precedente:

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
    
Per scrivere effettivamente le modifiche nuovamente dall'applicazione, si crea una richiesta di modifica. Ognuna delle classi del modello a dispone di una classe richiesta di modifica associata. Per modificare un PHAsset, ad esempio, si crea un PHAssetChangeRequest. I passaggi per eseguire modifiche di writeback per la raccolta di foto e inviate agli Observer, come quello riportato sopra sono:

-   Eseguire l'operazione di modifica.
-   Salvare i dati dell'immagine filtrato a un'istanza di PHContentEditingOutput.
-   Effettuare una richiesta di modifica per pubblicare il modulo di modifica l'output di modifica.

Di seguito è riportato un esempio che esegue il writeback di una modifica di un'immagine che viene applicato un filtro di asiatici immagine Core:

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
    
Quando l'utente seleziona il pulsante, viene applicato il filtro:

![](photokit-images/image5.png "Un esempio di applicazione del filtro")
 
E grazie a PHPhotoLibraryChangeObserver, la modifica viene riflessa nella visualizzazione raccolta quando l'utente passa nuovamente:

![](photokit-images/image6.png "La modifica viene riflessa nella visualizzazione raccolta quando l'utente passa indietro")
