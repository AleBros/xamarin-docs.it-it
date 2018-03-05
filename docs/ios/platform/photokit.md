---
title: PhotoKit
description: Kit di foto consente alle app di eseguire una query la libreria di immagini di sistema e creare l'interfaccia utente personalizzata per visualizzare e modificare il relativo contenuto.
ms.topic: article
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 6f6858f36c30f45225417c78225926906481eb8d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="photokit"></a>PhotoKit

_Kit di foto consente alle app di eseguire una query la libreria di immagini di sistema e creare l'interfaccia utente personalizzata per visualizzare e modificare il relativo contenuto._

Foto Kit è un nuovo framework che consente di eseguire una query la libreria di immagini di sistema e creare interfacce utente personalizzate per visualizzare e modificare il relativo contenuto. Include un numero di classi che rappresentano risorse video e immagine, nonché le raccolte di risorse, ad esempio album e le cartelle.

## <a name="model-objects"></a>Oggetti del modello
Kit di foto rappresenta gli asset in ciò che chiama gli oggetti del modello. Il modello di oggetti che rappresentano le foto e i video stessi è di tipo `PHAsset`. Oggetto `PHAsset` contiene i metadati, ad esempio il tipo di supporto della risorsa e la data di creazione.
Analogamente, il `PHAssetCollection` e `PHCollectionList` le classi contengono i metadati relativi a elenchi di raccolta e raccolte di asset, rispettivamente. Le raccolte di asset sono gruppi di risorse, ad esempio le foto e i video per un determinato anno. Analogamente, gli elenchi di raccolta sono gruppi di raccolte di asset, ad esempio foto e video raggruppati in base all'anno.

## <a name="querying-model-data"></a>Esecuzione di query sui dati del modello
Kit di foto semplifica ai dati del modello di query attraverso una serie di metodi di recupero. Ad esempio, per recuperare tutte le immagini, chiamare `PFAsset.Fetch`, passando il `PHAssetMediaType.Image` tipo di supporto.

    PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);

Il `PHFetchResult` istanza conterrà tutti i `PFAsset` istanze che rappresentano le immagini. Per ottenere le immagini, utilizzare il `PHImageManager` (o la versione di memorizzazione nella cache, `PHCachingImageManager`) per effettuare una richiesta per l'immagine chiamando `RequestImageForAsset`. Ad esempio, il codice seguente recupera un'immagine per ogni asset in un `PHFetchResult` da visualizzare in una cella di visualizzazione di raccolta:


    public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
    {
              var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
            imageMgr.RequestImageForAsset ((PHAsset)fetchResults [(uint)indexPath.Item], thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (), (img, info) => {
            imageCell.ImageView.Image = img;
        });
        return imageCell;
    }

Ciò comporta una griglia di immagini, come illustrato di seguito:

![](photokit-images/image4.png "L'app in esecuzione, visualizzazione di una griglia di immagini")
 
## <a name="saving-changes-to-the-photo-library"></a>Salvataggio delle modifiche per la raccolta di foto

Che viene illustrato come gestire l'esecuzione di query e la lettura dei dati. È anche possibile scrivere nuovamente le modifiche apportate alla libreria. Poiché più applicazioni interessate sono in grado di interagire con la raccolta di foto di sistema, è possibile registrare un osservatore a ricevere una notifica delle modifiche utilizzando un PhotoLibraryObserver. Quindi, quando le modifiche sono disponibili in, l'applicazione può aggiornare di conseguenza. Ad esempio, ecco una semplice implementazione per ricaricare la visualizzazione di raccolta precedente:

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
    
Per scrivere effettivamente le modifiche nuovamente dall'applicazione, si crea una richiesta di modifica. Ognuna delle classi modello dispone di una classe di richiesta di modifica associata. Per modificare un PHAsset, ad esempio, si crea un PHAssetChangeRequest. I passaggi per eseguire le modifiche vengono scritte nella raccolta foto e inviate agli osservatori simile a quello precedente sono:

-   Eseguire l'operazione di modifica.
-   Salvare i dati di immagine filtrata a un'istanza di PHContentEditingOutput.
-   Effettuare una richiesta di modifica per pubblicare il modulo di modifiche l'output di modifica.

Di seguito è riportato un esempio che scrive una modifica a un'immagine che viene applicato un filtro asiatici immagine Core:

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
 
E grazie a PHPhotoLibraryChangeObserver, la modifica viene riflessa nella visualizzazione raccolta quando l'utente passa indietro:

![](photokit-images/image6.png "La modifica viene riflessa nella visualizzazione raccolta quando l'utente passa indietro")
