---
title: Selezione documenti in Novell. iOS
description: Questo documento descrive la selezione dei documenti iOS e illustra come usarlo in Novell. iOS. Vengono esaminati iCloud, documenti, codice di installazione comune, estensioni del provider di documenti e altro ancora.
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: 6982f02860db2e89f83b4002d6acb5b28bae906b
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200373"
---
# <a name="document-picker-in-xamarinios"></a>Selezione documenti in Novell. iOS

Il selettore documenti consente la condivisione dei documenti tra le app. Questi documenti possono essere archiviati in iCloud o in una directory di un'altra app. I documenti vengono condivisi tramite il set di [estensioni del provider di documenti](~/ios/platform/extensions.md) installato dall'utente sul dispositivo. 

A causa della difficoltà di mantenere i documenti sincronizzati tra le app e il cloud, introducono una certa quantità di complessità necessaria.

## <a name="requirements"></a>Requisiti

Per completare i passaggi illustrati in questo articolo, è necessario quanto segue:

- **Xcode 7 e iOS 8 o versioni successive** : è necessario installare e configurare le API Xcode 7 e iOS 8 o successive di Apple nel computer dello sviluppatore.
- **Visual Studio o Visual Studio per Mac** : è necessario installare la versione più recente di Visual Studio per Mac.
- **dispositivo iOS** : un dispositivo iOS che esegue iOS 8 o versione successiva.

## <a name="changes-to-icloud"></a>Modifiche a iCloud

Per implementare le nuove funzionalità della selezione documenti, sono state apportate le modifiche seguenti al servizio iCloud di Apple:

- Il daemon iCloud è stato completamente riscritto con CloudKit.
- Le funzionalità iCloud esistenti sono state rinominate unità iCloud.
- Il supporto per il sistema operativo Microsoft Windows è stato aggiunto a iCloud.
- In Mac OS Finder è stata aggiunta una cartella iCloud.
- i dispositivi iOS possono accedere al contenuto della cartella Mac OS iCloud.

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

## <a name="what-is-a-document"></a>Che cos'è un documento?

Quando si fa riferimento a un documento in iCloud, si tratta di una singola entità autonoma che deve essere percepita dall'utente. Un utente potrebbe voler modificare il documento o condividerlo con altri utenti, ad esempio tramite posta elettronica.

Sono disponibili diversi tipi di file che verranno immediatamente riconosciuti dall'utente come documenti, ad esempio pagine, Keynote o file di numeri. Tuttavia, iCloud non è limitato a questo concetto. Ad esempio, lo stato di un gioco, ad esempio una corrispondenza degli scacchi, può essere considerato come un documento e archiviato in iCloud. Questo file può essere passato tra i dispositivi di un utente e consentire loro di scegliere un gioco da dove si è lasciato in un dispositivo diverso.

## <a name="dealing-with-documents"></a>Gestione dei documenti

Prima di approfondire il codice necessario per usare la selezione documenti con Novell, in questo articolo verranno illustrate le procedure consigliate per l'utilizzo dei documenti iCloud e alcune delle modifiche apportate alle API esistenti necessarie per supportare la selezione del documento.

### <a name="using-file-coordination"></a>Uso del coordinamento file

Poiché un file può essere modificato da diverse posizioni, è necessario usare il coordinamento per evitare la perdita di dati.

 [![](document-picker-images/image1.png "Uso del coordinamento file")](document-picker-images/image1.png#lightbox)

Diamo un'occhiata alla figura precedente:

1. Un dispositivo iOS che usa il coordinamento dei file crea un nuovo documento e lo salva nella cartella iCloud.
2. iCloud salva il file modificato nel cloud per la distribuzione in ogni dispositivo.
3. Un Mac collegato Visualizza il file modificato nella cartella iCloud e usa il coordinamento dei file per copiare le modifiche apportate al file.
4. Un dispositivo che non usa il coordinamento dei file apporta una modifica al file e lo salva nella cartella iCloud. Queste modifiche vengono replicate immediatamente negli altri dispositivi.

Si supponga che il dispositivo iOS originale o il Mac stia modificando il file, ora le modifiche andranno perse e sovrascritte con la versione del file dal dispositivo non coordinato. Per evitare la perdita di dati, il coordinamento dei file è un must quando si utilizzano documenti basati sul cloud.

### <a name="using-uidocument"></a>Uso di UIDocument

 `UIDocument`semplifica le attività (o `NSDocument` MacOS) grazie a una grande quantità di lavoro per lo sviluppatore. Fornisce il coordinamento dei file incorporato con le code in background per impedire il blocco dell'interfaccia utente dell'applicazione.

 `UIDocument`espone più API di alto livello che semplificano il lavoro di sviluppo di un'applicazione Novell per qualsiasi scopo richiesto dallo sviluppatore.

Il codice seguente crea una sottoclasse `UIDocument` di per implementare un documento generico basato su testo che può essere usato per archiviare e recuperare testo da iCloud:

```csharp
using System;
using Foundation;
using UIKit;

namespace DocPicker
{
    public class GenericTextDocument : UIDocument
    {
        #region Private Variable Storage
        private NSString _dataModel;
        #endregion

        #region Computed Properties
        public string Contents {
            get { return _dataModel.ToString (); }
            set { _dataModel = new NSString(value); }
        }
        #endregion

        #region Constructors
        public GenericTextDocument (NSUrl url) : base (url)
        {
            // Set the default document text
            this.Contents = "";
        }

        public GenericTextDocument (NSUrl url, string contents) : base (url)
        {
            // Set the default document text
            this.Contents = contents;
        }
        #endregion

        #region Override Methods
        public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Were any contents passed to the document?
            if (contents != null) {
                _dataModel = NSString.FromData( (NSData)contents, NSStringEncoding.UTF8 );
            }

            // Inform caller that the document has been modified
            RaiseDocumentModified (this);

            // Return success
            return true;
        }

        public override NSObject ContentsForType (string typeName, out NSError outError)
        {
            // Clear the error state
            outError = null;

            // Convert the contents to a NSData object and return it
            NSData docData = _dataModel.Encode(NSStringEncoding.UTF8);
            return docData;
        }
        #endregion

        #region Events
        public delegate void DocumentModifiedDelegate(GenericTextDocument document);
        public event DocumentModifiedDelegate DocumentModified;

        internal void RaiseDocumentModified(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentModified != null) {
                this.DocumentModified (document);
            }
        }
        #endregion
    }
}
```

La `GenericTextDocument` classe presentata sopra verrà usata in questo articolo quando si lavora con la selezione documenti e i documenti esterni in un'applicazione Novell. iOS 8.

## <a name="asynchronous-file-coordination"></a>Coordinamento file asincrono

iOS 8 fornisce diverse nuove funzionalità di coordinamento dei file asincrone tramite le nuove API di coordinamento dei file. Prima di iOS 8, tutte le API di coordinamento file esistenti erano completamente sincrone. Ciò significava che lo sviluppatore era responsabile dell'implementazione del proprio Accodamento in background per evitare che il coordinamento dei file bloccasse l'interfaccia utente dell'applicazione.

La nuova `NSFileAccessIntent` classe contiene un URL che punta al file e diverse opzioni per controllare il tipo di coordinamento necessario. Il codice seguente illustra lo spostamento di un file da un percorso a un altro usando gli Intent:

```csharp
// Get source options
var srcURL = NSUrl.FromFilename ("FromFile.txt");
var srcIntent = NSFileAccessIntent.CreateReadingIntent (srcURL, NSFileCoordinatorReadingOptions.ForUploading);

// Get destination options
var dstURL = NSUrl.FromFilename ("ToFile.txt");
var dstIntent = NSFileAccessIntent.CreateReadingIntent (dstURL, NSFileCoordinatorReadingOptions.ForUploading);

// Create an array
var intents = new NSFileAccessIntent[] {
    srcIntent,
    dstIntent
};

// Initialize a file coordination with intents
var queue = new NSOperationQueue ();
var fileCoordinator = new NSFileCoordinator ();
fileCoordinator.CoordinateAccess (intents, queue, (err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}",err.LocalizedDescription);
    }
});
```

## <a name="discovering-and-listing-documents"></a>Individuazione ed elenco di documenti

Per individuare ed elencare i documenti, è possibile usare le `NSMetadataQuery` API esistenti. In questa sezione vengono illustrate le nuove `NSMetadataQuery` funzionalità aggiunte a che rendono più semplice l'utilizzo dei documenti.

### <a name="existing-behavior"></a>Comportamento esistente

Prima di iOS 8, `NSMetadataQuery` era lento a prelevare le modifiche ai file locali, ad esempio: eliminazioni, creazione e ridenominazione.

 [![](document-picker-images/image2.png "Panoramica delle modifiche al file locale NSMetadataQuery")](document-picker-images/image2.png#lightbox)

Nel diagramma precedente:

1. Per i file già esistenti nel contenitore dell'applicazione, `NSMetadataQuery` dispone di `NSMetadata` record esistenti creati in precedenza e con spooling in modo che siano immediatamente disponibili per l'applicazione.
1. L'applicazione crea un nuovo file nel contenitore dell'applicazione.
1. Si verifica un ritardo prima `NSMetadataQuery` che venga visualizzata la modifica al contenitore dell'applicazione e viene `NSMetadata` creato il record necessario.


A causa del ritardo nella creazione del `NSMetadata` record, l'applicazione deve avere due origini dati aperte: una per le modifiche ai file locali e una per le modifiche basate sul cloud.

### <a name="stitching"></a>Unione

In iOS 8, `NSMetadataQuery` è più facile da usare direttamente con una nuova funzionalità denominata Unione:

 [![](document-picker-images/image3.png "NSMetadataQuery con una nuova funzionalità denominata Unione")](document-picker-images/image3.png#lightbox)

Utilizzando l'Unione nel diagramma precedente:

1. Come in precedenza, per i file già esistenti nel contenitore `NSMetadataQuery` dell'applicazione sono presenti record esistenti `NSMetadata` creati in precedenza e di cui è stato eseguito lo spooling.
1. L'applicazione crea un nuovo file nel contenitore di applicazioni usando il coordinamento dei file.
1. Un hook nel contenitore dell'applicazione Visualizza la modifica e chiama `NSMetadataQuery` per creare il record `NSMetadata` necessario.
1. Il `NSMetadata` record viene creato direttamente dopo il file e reso disponibile per l'applicazione.


Tramite l'Unione dell'applicazione non è più necessario aprire un'origine dati per monitorare le modifiche ai file locali e basati sul cloud. A questo punto l'applicazione può `NSMetadataQuery` basarsi direttamente su.

> [!IMPORTANT]
> Il ricamo funziona solo se l'applicazione usa il coordinamento dei file, come illustrato nella sezione precedente. Se il coordinamento dei file non viene usato, le API usano per impostazione predefinita il comportamento pre iOS 8 esistente.




### <a name="new-ios-8-metadata-features"></a>Nuove funzionalità dei metadati di iOS 8

Le nuove funzionalità seguenti sono state aggiunte a `NSMetadataQuery` in iOS 8:

- `NSMetatadataQuery`Ora è possibile elencare i documenti non locali archiviati nel cloud.
- Sono state aggiunte nuove API per accedere alle informazioni sui metadati sui documenti basati sul cloud. 
- È disponibile una nuova `NSUrl_PromisedItems` API che consente di accedere agli attributi dei file che possono o meno disporre di contenuto localmente.
- Utilizzare il `GetPromisedItemResourceValue` metodo per ottenere informazioni su un determinato file o utilizzare il `GetPromisedItemResourceValues` metodo per ottenere informazioni su più file alla volta.


Per la gestione dei metadati sono stati aggiunti due nuovi flag di coordinamento dei file:

- `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
- `NSFileCoordinatorWriteContentIndependentMetadataOnly` 


Con i flag precedenti, non è necessario che il contenuto del file del documento sia disponibile localmente perché venga usato.

Il segmento di codice seguente illustra come usare `NSMetadataQuery` per eseguire una query per l'esistenza di un file specifico e compilare il file se non esiste:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;


#region Static Properties
public const string TestFilename = "test.txt"; 
#endregion

#region Computed Properties
public bool HasiCloud { get; set; }
public bool CheckingForiCloud { get; set; }
public NSUrl iCloudUrl { get; set; }

public GenericTextDocument Document { get; set; }
public NSMetadataQuery Query { get; set; }
#endregion

#region Private Methods
private void FindDocument () {
    Console.WriteLine ("Finding Document...");

    // Create a new query and set it's scope
    Query = new NSMetadataQuery();
    Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

    // Build a predicate to locate the file by name and attach it to the query
    var pred = NSPredicate.FromFormat ("%K == %@"
        , new NSObject[] {
            NSMetadataQuery.ItemFSNameKey
            , new NSString(TestFilename)});
    Query.Predicate = pred;

    // Register a notification for when the query returns
    NSNotificationCenter.DefaultCenter.AddObserver (this,
            new Selector("queryDidFinishGathering:"),             NSMetadataQuery.DidFinishGatheringNotification,
            Query);

    // Start looking for the file
    Query.StartQuery ();
    Console.WriteLine ("Querying: {0}", Query.IsGathering);
}

[Export("queryDidFinishGathering:")]
public void DidFinishGathering (NSNotification notification) {
    Console.WriteLine ("Finish Gathering Documents.");

    // Access the query and stop it from running
    var query = (NSMetadataQuery)notification.Object;
    query.DisableUpdates();
    query.StopQuery();

    // Release the notification
    NSNotificationCenter.DefaultCenter.RemoveObserver (this
        , NSMetadataQuery.DidFinishGatheringNotification
        , query);

    // Load the document that the query returned
    LoadDocument(query);
}

private void LoadDocument (NSMetadataQuery query) {
    Console.WriteLine ("Loading Document...");    

    // Take action based on the returned record count
    switch (query.ResultCount) {
    case 0:
        // Create a new document
        CreateNewDocument ();
        break;
    case 1:
        // Gain access to the url and create a new document from
        // that instance
        NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

        // Load the document
        OpenDocument (url);
        break;
    default:
        // There has been an issue
        Console.WriteLine ("Issue: More than one document found...");
        break;
    }
}
#endregion

#region Public Methods
public void OpenDocument(NSUrl url) {

    Console.WriteLine ("Attempting to open: {0}", url);
    Document = new GenericTextDocument (url);

    // Open the document
    Document.Open ( (success) => {
        if (success) {
            Console.WriteLine ("Document Opened");
        } else
            Console.WriteLine ("Failed to Open Document");
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public void CreateNewDocument() {
    // Create path to new file
    // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
    var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
    var docPath = Path.Combine (docsFolder, TestFilename);
    var ubiq = new NSUrl (docPath, false);

    // Create new document at path 
    Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
    Document = new GenericTextDocument (ubiq);

    // Set the default value
    Document.Contents = "(default value)";

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
        Console.WriteLine ("Save completion:" + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
        } else {
            Console.WriteLine ("Unable to Save Document");
        }
    });

    // Inform caller
    RaiseDocumentLoaded (Document);
}

public bool SaveDocument() {
    bool successful = false;

    // Save document to path
    Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
        Console.WriteLine ("Save completion: " + saveSuccess);
        if (saveSuccess) {
            Console.WriteLine ("Document Saved");
            successful = true;
        } else {
            Console.WriteLine ("Unable to Save Document");
            successful=false;
        }
    });

    // Return results
    return successful;
}
#endregion

#region Events
public delegate void DocumentLoadedDelegate(GenericTextDocument document);
public event DocumentLoadedDelegate DocumentLoaded;

internal void RaiseDocumentLoaded(GenericTextDocument document) {
    // Inform caller
    if (this.DocumentLoaded != null) {
        this.DocumentLoaded (document);
    }
}
#endregion
```

### <a name="document-thumbnails"></a>Anteprime di documenti

Apple ritiene che la migliore esperienza utente quando si elencano i documenti per un'applicazione consiste nell'usare anteprime. In questo modo si ottiene il contesto degli utenti finali, che consente di identificare rapidamente il documento che si desidera utilizzare.

Prima di iOS 8, per visualizzare le anteprime del documento era necessaria un'implementazione personalizzata. Le novità di iOS 8 sono file system attributi che consentono allo sviluppatore di lavorare rapidamente con le anteprime dei documenti.

#### <a name="retrieving-document-thumbnails"></a>Recupero delle anteprime dei documenti 

Chiamando il metodo `GetPromisedItemResourceValue` o `GetPromisedItemResourceValues` , `NSUrl_PromisedItems` viene restituita l' `NSUrlThumbnailDictionary`API, a. L'unica chiave attualmente presente in questo dizionario è `NSThumbnial1024X1024SizeKey` l'oggetto e `UIImage`la relativa corrispondenza.

#### <a name="saving-document-thumbnails"></a>Salvataggio delle anteprime del documento

Il modo più semplice per salvare un'anteprima consiste nell' `UIDocument`usare. Chiamando il `GetFileAttributesToWrite` metodo `UIDocument` di e impostando l'anteprima, quest'operazione verrà salvata automaticamente quando il file del documento sarà. Il daemon iCloud vedrà questa modifica e la propaga a iCloud. In Mac OS X le anteprime vengono generate automaticamente per lo sviluppatore dal plug-in Quick Look.

Con le nozioni di base sull'uso dei documenti basati su iCloud, insieme alle modifiche apportate all'API esistente, è possibile implementare il controller di visualizzazione selezione documento in un'applicazione Novell iOS 8 per dispositivi mobili.


## <a name="enabling-icloud-in-xamarin"></a>Abilitazione di iCloud in Novell

Prima di poter usare la selezione documenti in un'applicazione Novell. iOS, è necessario abilitare il supporto iCloud nell'applicazione e tramite Apple. 

La procedura seguente illustra il processo di provisioning per iCloud.

1. Creare un contenitore iCloud.
2. Creare un ID app che contenga il servizio app iCloud.
3. Creare un profilo di provisioning che includa questo ID app.

La Guida [uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) illustra i primi due passaggi. Per creare un profilo di provisioning, seguire la procedura descritta nella guida del [profilo](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) di provisioning.



La procedura seguente illustra il processo di configurazione dell'applicazione per iCloud:

Seguire questa procedura:

1. Aprire il progetto in Visual Studio per Mac o Visual Studio.
2. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere Opzioni.
3. Nella finestra di dialogo Opzioni selezionare **applicazione iOS**, assicurarsi che l' **identificatore del bundle** corrisponda a quello definito nell' **ID app** creato in precedenza per l'applicazione. 
4. Selezionare **firma del bundle iOS**, selezionare l' **identità dello sviluppatore** e il **profilo** di provisioning creato in precedenza.
5. Fare clic sul pulsante **OK** per salvare le modifiche e chiudere la finestra di dialogo.
6. Fare clic con il `Entitlements.plist` pulsante destro del mouse sul **Esplora soluzioni** per aprirlo nell'editor.

    > [!IMPORTANT]
    > In Visual Studio potrebbe essere necessario aprire l'editor dei diritti facendo clic con il pulsante destro del mouse su di esso, selezionando **Apri con...** e selezionando Editor elenco proprietà

7. Selezionare **Enable iCloud** , **iCloud Documents** , **key-value storage** e **CloudKit** .
8. Verificare che il **contenitore** esista per l'applicazione, come creato in precedenza. Esempio: `iCloud.com.your-company.AppName`
9. Salvare le modifiche apportate al file.

Per ulteriori informazioni sui diritti, vedere la guida sull' [utilizzo dei diritti](~/ios/deploy-test/provisioning/entitlements.md) .

Con la configurazione precedente, l'applicazione può ora usare documenti basati sul cloud e il nuovo controller di visualizzazione selezione documento.

## <a name="common-setup-code"></a>Codice di installazione comune

Prima di iniziare a usare il controller di visualizzazione di selezione documento, è necessario un codice di installazione standard. Per iniziare, modificare il `AppDelegate.cs` file dell'applicazione e ottenere un aspetto simile al seguente:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using Foundation;
using UIKit;
using ObjCRuntime;
using System.IO;

namespace DocPicker
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Static Properties
        public const string TestFilename = "test.txt"; 
        #endregion

        #region Computed Properties
        public override UIWindow Window { get; set; }
        public bool HasiCloud { get; set; }
        public bool CheckingForiCloud { get; set; }
        public NSUrl iCloudUrl { get; set; }

        public GenericTextDocument Document { get; set; }
        public NSMetadataQuery Query { get; set; }
        public NSData Bookmark { get; set; }
        #endregion

        #region Private Methods
        private void FindDocument () {
            Console.WriteLine ("Finding Document...");

            // Create a new query and set it's scope
            Query = new NSMetadataQuery();
            Query.SearchScopes = new NSObject [] {
                NSMetadataQuery.UbiquitousDocumentsScope,
                NSMetadataQuery.UbiquitousDataScope,
                NSMetadataQuery.AccessibleUbiquitousExternalDocumentsScope
            };

            // Build a predicate to locate the file by name and attach it to the query
            var pred = NSPredicate.FromFormat ("%K == %@",
                 new NSObject[] {NSMetadataQuery.ItemFSNameKey
                , new NSString(TestFilename)});
            Query.Predicate = pred;

            // Register a notification for when the query returns
            NSNotificationCenter.DefaultCenter.AddObserver (this
                , new Selector("queryDidFinishGathering:")
                , NSMetadataQuery.DidFinishGatheringNotification
                , Query);

            // Start looking for the file
            Query.StartQuery ();
            Console.WriteLine ("Querying: {0}", Query.IsGathering);
        }


        [Export("queryDidFinishGathering:")]
        public void DidFinishGathering (NSNotification notification) {
            Console.WriteLine ("Finish Gathering Documents.");

            // Access the query and stop it from running
            var query = (NSMetadataQuery)notification.Object;
            query.DisableUpdates();
            query.StopQuery();

            // Release the notification
            NSNotificationCenter.DefaultCenter.RemoveObserver (this
                , NSMetadataQuery.DidFinishGatheringNotification
                , query);

            // Load the document that the query returned
            LoadDocument(query);
        }

        private void LoadDocument (NSMetadataQuery query) {
            Console.WriteLine ("Loading Document...");    

            // Take action based on the returned record count
            switch (query.ResultCount) {
            case 0:
                // Create a new document
                CreateNewDocument ();
                break;
            case 1:
                // Gain access to the url and create a new document from
                // that instance
                NSMetadataItem item = (NSMetadataItem)query.ResultAtIndex (0);
                var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);

                // Load the document
                OpenDocument (url);
                break;
            default:
                // There has been an issue
                Console.WriteLine ("Issue: More than one document found...");
                break;
            }
        }
        #endregion

        #region Public Methods

        public void OpenDocument(NSUrl url) {

            Console.WriteLine ("Attempting to open: {0}", url);
            Document = new GenericTextDocument (url);

            // Open the document
            Document.Open ( (success) => {
                if (success) {
                    Console.WriteLine ("Document Opened");
                } else
                    Console.WriteLine ("Failed to Open Document");
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        public void CreateNewDocument() {
            // Create path to new file
            // var docsFolder = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var docsFolder = Path.Combine(iCloudUrl.Path, "Documents");
            var docPath = Path.Combine (docsFolder, TestFilename);
            var ubiq = new NSUrl (docPath, false);

            // Create new document at path 
            Console.WriteLine ("Creating Document at:" + ubiq.AbsoluteString);
            Document = new GenericTextDocument (ubiq);

            // Set the default value
            Document.Contents = "(default value)";

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForCreating, (saveSuccess) => {
                Console.WriteLine ("Save completion:" + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                } else {
                    Console.WriteLine ("Unable to Save Document");
                }
            });

            // Inform caller
            RaiseDocumentLoaded (Document);
        }

        /// <summary>
        /// Saves the document.
        /// </summary>
        /// <returns><c>true</c>, if document was saved, <c>false</c> otherwise.</returns>
        public bool SaveDocument() {
            bool successful = false;

            // Save document to path
            Document.Save (Document.FileUrl, UIDocumentSaveOperation.ForOverwriting, (saveSuccess) => {
                Console.WriteLine ("Save completion: " + saveSuccess);
                if (saveSuccess) {
                    Console.WriteLine ("Document Saved");
                    successful = true;
                } else {
                    Console.WriteLine ("Unable to Save Document");
                    successful=false;
                }
            });

            // Return results
            return successful;
        }
        #endregion

        #region Override Methods
        public override void FinishedLaunching (UIApplication application)
        {

            // Start a new thread to check and see if the user has iCloud
            // enabled.
            new Thread(new ThreadStart(() => {
                // Inform caller that we are checking for iCloud
                CheckingForiCloud = true;

                // Checks to see if the user of this device has iCloud
                // enabled
                var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer(null);

                // Connected to iCloud?
                if (uburl == null)
                {
                    // No, inform caller
                    HasiCloud = false;
                    iCloudUrl =null;
                    Console.WriteLine("Unable to connect to iCloud");
                    InvokeOnMainThread(()=>{
                        var okAlertController = UIAlertController.Create ("iCloud Not Available", "Developer, please check your Entitlements.plist, Bundle ID and Provisioning Profiles.", UIAlertControllerStyle.Alert);
                        okAlertController.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                        Window.RootViewController.PresentViewController (okAlertController, true, null);
                    });
                }
                else
                {    
                    // Yes, inform caller and save location the Application Container
                    HasiCloud = true;
                    iCloudUrl = uburl;
                    Console.WriteLine("Connected to iCloud");

                    // If we have made the connection with iCloud, start looking for documents
                    InvokeOnMainThread(()=>{
                        // Search for the default document
                        FindDocument ();
                    });
                }

                // Inform caller that we are no longer looking for iCloud
                CheckingForiCloud = false;

            })).Start();
                
        }
        
        // This method is invoked when the application is about to move from active to inactive state.
        // OpenGL applications should use this method to pause.
        public override void OnResignActivation (UIApplication application)
        {
        }
        
        // This method should be used to release shared resources and it should store the application state.
        // If your application supports background execution this method is called instead of WillTerminate
        // when the user quits.
        public override void DidEnterBackground (UIApplication application)
        {
            // Trap all errors
            try {
                // Values to include in the bookmark packet
                var resources = new string[] {
                    NSUrl.FileSecurityKey,
                    NSUrl.ContentModificationDateKey,
                    NSUrl.FileResourceIdentifierKey,
                    NSUrl.FileResourceTypeKey,
                    NSUrl.LocalizedNameKey
                };

                // Create the bookmark
                NSError err;
                Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

                // Was there an error?
                if (err != null) {
                    // Yes, report it
                    Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
                }
            }
            catch (Exception e) {
                // Report error
                Console.WriteLine ("Error: {0}", e.Message);
            }
        }
        
        // This method is called as part of the transition from background to active state.
        public override void WillEnterForeground (UIApplication application)
        {
            // Is there any bookmark data?
            if (Bookmark != null) {
                // Trap all errors
                try {
                    // Yes, attempt to restore it
                    bool isBookmarkStale;
                    NSError err;
                    var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

                    // Was there an error?
                    if (err != null) {
                        // Yes, report it
                        Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
                    } else {
                        // Load document from bookmark
                        OpenDocument (srcUrl);
                    }
                }
                catch (Exception e) {
                    // Report error
                    Console.WriteLine ("Error: {0}", e.Message);
                }
            }

        }
        
        // This method is called when the application is about to terminate. Save data, if needed.
        public override void WillTerminate (UIApplication application)
        {
        }
        #endregion

        #region Events
        public delegate void DocumentLoadedDelegate(GenericTextDocument document);
        public event DocumentLoadedDelegate DocumentLoaded;

        internal void RaiseDocumentLoaded(GenericTextDocument document) {
            // Inform caller
            if (this.DocumentLoaded != null) {
                this.DocumentLoaded (document);
            }
        }
        #endregion
    }
}

```

> [!IMPORTANT]
> Il codice precedente include il codice della sezione relativa all'individuazione e all'elenco dei documenti riportata sopra. Viene presentato qui nell'intero modo in cui verrebbe visualizzato in un'applicazione effettiva. Per semplicità, questo esempio funziona solo con un singolo file hardcoded (`test.txt`).

Il codice precedente espone diversi collegamenti all'unità iCloud per facilitarne l'uso nel resto dell'applicazione.

Aggiungere quindi il codice seguente a qualsiasi visualizzazione o contenitore di visualizzazione che utilizzerà la selezione documenti o i documenti basati su cloud:

```csharp
using CloudKit;
...

#region Computed Properties
/// <summary>
/// Returns the delegate of the current running application
/// </summary>
/// <value>The this app.</value>
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Verrà aggiunto un collegamento per `AppDelegate` accedere al e accedere ai collegamenti iCloud creati in precedenza.

Con questo codice, verrà ora esaminata l'implementazione del controller di visualizzazione selezione documento in un'applicazione Novell iOS 8.

## <a name="using-the-document-picker-view-controller"></a>Uso del controller di visualizzazione selezione documento

Prima di iOS 8, era molto difficile accedere ai documenti da un'altra applicazione perché non era possibile individuare documenti all'esterno dell'applicazione dall'interno dell'app.

### <a name="existing-behavior"></a>Comportamento esistente

 [![](document-picker-images/image31.png "Panoramica sul comportamento esistente")](document-picker-images/image31.png#lightbox)

Diamo un'occhiata all'accesso a un documento esterno prima di iOS 8:

1. Per prima cosa, l'utente deve aprire l'applicazione che ha creato originariamente il documento.
1. Il documento è selezionato e `UIDocumentInteractionController` viene usato per inviare il documento alla nuova applicazione.
1. Infine, una copia del documento originale viene inserita nel contenitore della nuova applicazione.


Da qui è disponibile il documento per l'apertura e la modifica della seconda applicazione.

### <a name="discovering-documents-outside-of-an-apps-container"></a>Individuazione di documenti all'esterno del contenitore di un'app

In iOS 8 un'applicazione è in grado di accedere ai documenti all'esterno del proprio contenitore di applicazioni con facilità:

 [![](document-picker-images/image32.png "Individuazione di documenti all'esterno del contenitore di un'app")](document-picker-images/image32.png#lightbox)

Usando la nuova selezione documenti iCloud ( `UIDocumentPickerViewController`), un'applicazione iOS può individuare e accedere direttamente all'esterno del contenitore dell'applicazione. `UIDocumentPickerViewController` Fornisce un meccanismo che consente all'utente di concedere l'accesso e modificare i documenti individuati tramite le autorizzazioni.

Un'applicazione deve acconsentire esplicitamente alla visualizzazione dei documenti nel selettore di documenti iCloud ed essere disponibile affinché altre applicazioni possano individuarle e utilizzarle. Per fare in modo che un'applicazione Novell iOS 8 condivida il contenitore dell' `Info.plist` applicazione, modificarlo in un editor di testo standard e aggiungere le due righe seguenti alla fine del dizionario ( `<dict>...</dict>` tra i tag):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

`UIDocumentPickerViewController` Fornisce un'ottima interfaccia utente nuova che consente all'utente di scegliere i documenti. Per visualizzare il controller di visualizzazione selezione documento in un'applicazione Novell iOS 8, seguire questa procedura:

```csharp
using MobileCoreServices;
...

// Allow the Document picker to select a range of document types
        var allowedUTIs = new string[] {
            UTType.UTF8PlainText,
            UTType.PlainText,
            UTType.RTF,
            UTType.PNG,
            UTType.Text,
            UTType.PDF,
            UTType.Image
        };

        // Display the picker
        //var picker = new UIDocumentPickerViewController (allowedUTIs, UIDocumentPickerMode.Open);
        var pickerMenu = new UIDocumentMenuViewController(allowedUTIs, UIDocumentPickerMode.Open);
        pickerMenu.DidPickDocumentPicker += (sender, args) => {

            // Wireup Document Picker
            args.DocumentPicker.DidPickDocument += (sndr, pArgs) => {

                // IMPORTANT! You must lock the security scope before you can
                // access this file
                var securityEnabled = pArgs.Url.StartAccessingSecurityScopedResource();

                // Open the document
                ThisApp.OpenDocument(pArgs.Url);

                // IMPORTANT! You must release the security lock established
                // above.
                pArgs.Url.StopAccessingSecurityScopedResource();
            };

            // Display the document picker
            PresentViewController(args.DocumentPicker,true,null);
        };

pickerMenu.ModalPresentationStyle = UIModalPresentationStyle.Popover;
PresentViewController(pickerMenu,true,null);
UIPopoverPresentationController presentationPopover = pickerMenu.PopoverPresentationController;
if (presentationPopover!=null) {
    presentationPopover.SourceView = this.View;
    presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Down;
    presentationPopover.SourceRect = ((UIButton)s).Frame;
}
```

> [!IMPORTANT]
> Per poter accedere `NSUrl` a un `StartAccessingSecurityScopedResource` documento esterno, lo sviluppatore deve chiamare il metodo di. Il `StopAccessingSecurityScopedResource` metodo deve essere chiamato per rilasciare il blocco di sicurezza non appena il documento è stato caricato.

### <a name="sample-output"></a>Esempio di output

Di seguito è riportato un esempio di come il codice precedente visualizzi una selezione documenti quando viene eseguita su un dispositivo iPhone:

1. L'utente avvia l'applicazione e viene visualizzata l'interfaccia principale:   
 
    [![](document-picker-images/image33.png "Viene visualizzata l'interfaccia principale")](document-picker-images/image33.png#lightbox)
1. L'utente tocca il pulsante di **azione** nella parte superiore della schermata e viene richiesto di selezionare un **provider di documenti** dall'elenco dei provider disponibili:   
 
    [![](document-picker-images/image34.png "Selezionare un provider di documenti dall'elenco dei provider disponibili")](document-picker-images/image34.png#lightbox)
1. Il **controller di visualizzazione selezione documento** viene visualizzato per il **provider di documenti**selezionato:   
 
    [![](document-picker-images/image35.png "Viene visualizzato il controller di visualizzazione selezione documento")](document-picker-images/image35.png#lightbox)
1. L'utente tocca una **cartella del documento** per visualizzarne il contenuto:   
 
    [![](document-picker-images/image36.png "Contenuto della cartella del documento")](document-picker-images/image36.png#lightbox)
1. L'utente seleziona un **documento** e la **selezione del documento** viene chiusa.
1. L'interfaccia principale viene visualizzata nuovamente, il **documento** viene caricato dal contenitore esterno e ne viene visualizzato il contenuto.


La visualizzazione effettiva del controller di visualizzazione selezione documento dipende dai provider di documenti installati dall'utente sul dispositivo e dalla modalità di selezione dei documenti implementata. Nell'esempio precedente viene usata la modalità di apertura. gli altri tipi di modalità verranno descritti in dettaglio di seguito.

## <a name="managing-external-documents"></a>Gestione di documenti esterni

Come illustrato in precedenza, prima di iOS 8, un'applicazione poteva accedere solo ai documenti che facevano parte del contenitore dell'applicazione. In iOS 8 un'applicazione può accedere ai documenti da origini esterne:

 [![](document-picker-images/image37.png "Panoramica sulla gestione dei documenti esterni")](document-picker-images/image37.png#lightbox)

Quando l'utente seleziona un documento da un'origine esterna, viene scritto un documento di riferimento nel contenitore dell'applicazione che punta al documento originale.

Per semplificare l' `NSMetadataQuery` aggiunta di questa nuova capacità alle applicazioni esistenti, sono state aggiunte diverse nuove funzionalità all'API. In genere, un'applicazione usa l'ambito dei documenti onnipresente per elencare i documenti che risiedono all'interno del contenitore dell'applicazione. Con questo ambito, solo i documenti all'interno del contenitore dell'applicazione continueranno a essere visualizzati.

L'uso del nuovo ambito del documento esterno onnipresente restituirà i documenti che si trovano all'esterno del contenitore dell'applicazione e li restituiscono. `NSMetadataItemUrlKey` Punterà all'URL in cui si trova effettivamente il documento.

A volte un'applicazione non desidera lavorare con i documenti a cui punta il riferimento. L'app vuole invece usare direttamente il documento di riferimento. Ad esempio, è possibile che l'app desideri visualizzare il documento nella cartella dell'applicazione nell'interfaccia utente o per consentire all'utente di spostare i riferimenti all'interno di una cartella.

In iOS 8 è stato fornito `NSMetadataItemUrlInLocalContainerKey` un nuovo per accedere direttamente al documento di riferimento. Questa chiave punta al riferimento effettivo al documento esterno in un contenitore dell'applicazione.

`NSMetadataUbiquitousItemIsExternalDocumentKey` Viene utilizzato per verificare se un documento è esterno o meno al contenitore di un'applicazione. `NSMetadataUbiquitousItemContainerDisplayNameKey` Viene utilizzato per accedere al nome del contenitore che ospita la copia originale di un documento esterno.

### <a name="why-document-references-are-required"></a>Perché i riferimenti ai documenti sono obbligatori

Il motivo principale per cui iOS 8 USA i riferimenti per accedere ai documenti esterni è la sicurezza. A nessuna applicazione viene concesso l'accesso al contenitore di un'altra applicazione. Questa operazione può essere eseguita solo da selezione documenti, perché è in esecuzione out-of-process e ha accesso a livello di sistema.

L'unico modo per ottenere un documento all'esterno del contenitore dell'applicazione consiste nell'usare la selezione documenti e, se l'URL restituito dal selettore è con ambito di protezione. L'URL con ambito di protezione contiene informazioni sufficienti per selezionare il documento insieme ai diritti con ambito necessari per concedere a un'applicazione l'accesso al documento.

È importante notare che se l'URL con ambito di protezione è stato serializzato in una stringa e quindi deserializzato, le informazioni di sicurezza andranno perse e il file non sarebbe accessibile dall'URL. La funzionalità di riferimento del documento fornisce un meccanismo per tornare ai file a cui puntano tali URL.

Quindi, se l'applicazione acquisisce `NSUrl` da uno dei documenti di riferimento, dispone già dell'ambito di protezione collegato e può essere usato per accedere al file. Per questo motivo, è consigliabile che lo sviluppatore usi `UIDocument` perché gestisce tutte queste informazioni e processi.

### <a name="using-bookmarks"></a>Utilizzo dei bookmark

Non è sempre possibile enumerare i documenti di un'applicazione per tornare a un documento specifico, ad esempio quando si esegue il ripristino dello stato. iOS 8 fornisce un meccanismo per creare segnalibri destinati direttamente a un documento specifico.

Il codice seguente creerà un segnalibro dalla proprietà `UIDocument`di `FileUrl` una proprietà:

```csharp
// Trap all errors
try {
    // Values to include in the bookmark packet
    var resources = new string[] {
        NSUrl.FileSecurityKey,
        NSUrl.ContentModificationDateKey,
        NSUrl.FileResourceIdentifierKey,
        NSUrl.FileResourceTypeKey,
        NSUrl.LocalizedNameKey
    };

    // Create the bookmark
    NSError err;
    Bookmark = Document.FileUrl.CreateBookmarkData (NSUrlBookmarkCreationOptions.WithSecurityScope, resources, iCloudUrl, out err);

    // Was there an error?
    if (err != null) {
        // Yes, report it
        Console.WriteLine ("Error Creating Bookmark: {0}", err.LocalizedDescription);
    }
}
catch (Exception e) {
    // Report error
    Console.WriteLine ("Error: {0}", e.Message);
}
```

L'API segnalibro esistente viene utilizzata per creare un segnalibro per `NSUrl` un oggetto esistente che può essere salvato e caricato per fornire l'accesso diretto a un file esterno. Nel codice seguente viene ripristinato un segnalibro creato in precedenza:

```csharp
if (Bookmark != null) {
    // Trap all errors
    try {
        // Yes, attempt to restore it
        bool isBookmarkStale;
        NSError err;
        var srcUrl = new NSUrl (Bookmark, NSUrlBookmarkResolutionOptions.WithSecurityScope, iCloudUrl, out isBookmarkStale, out err);

        // Was there an error?
        if (err != null) {
            // Yes, report it
            Console.WriteLine ("Error Loading Bookmark: {0}", err.LocalizedDescription);
        } else {
            // Load document from bookmark
            OpenDocument (srcUrl);
        }
    }
    catch (Exception e) {
        // Report error
        Console.WriteLine ("Error: {0}", e.Message);
    }
}
```

## <a name="open-vs-import-mode-and-the-document-picker"></a>Apri rispetto a Modalità di importazione e selezione documenti

Il controller di visualizzazione selezione documento presenta due modalità operative diverse:

1. **Modalità di apertura** : in questa modalità, quando l'utente seleziona un documento esterno, il selettore dei documenti creerà un segnalibro con ambito di protezione nel contenitore dell'applicazione.   
 
    [![](document-picker-images/image37.png "Segnalibro con ambito di protezione nel contenitore di applicazioni")](document-picker-images/image37.png#lightbox)
1. **Modalità di importazione** : in questa modalità, quando l'utente seleziona un documento esterno, il selettore del documento non creerà un segnalibro, ma copierà il file in un percorso temporaneo e fornirà l'accesso all'applicazione al documento in questo percorso:   
 
    [![](document-picker-images/image38.png "Il selettore documenti copierà il file in un percorso temporaneo e fornirà l'accesso all'applicazione al documento in questa posizione")](document-picker-images/image38.png#lightbox)   
 Una volta terminata l'applicazione per qualsiasi motivo, il percorso temporaneo viene svuotato e il file viene rimosso. Se l'applicazione deve mantenere l'accesso al file, deve creare una copia e inserirla nel contenitore dell'applicazione.


La modalità di apertura è utile quando l'applicazione vuole collaborare con un'altra applicazione e condividere le modifiche apportate al documento con tale applicazione. La modalità di importazione viene usata quando l'applicazione non vuole condividere le modifiche apportate a un documento con altre applicazioni.

## <a name="making-a-document-external"></a>Creazione di un documento esterno

Come indicato in precedenza, un'applicazione iOS 8 non ha accesso ai contenitori all'esterno del relativo contenitore dell'applicazione. L'applicazione può scrivere nel proprio contenitore localmente o in un percorso temporaneo, quindi usare una modalità documento speciale per spostare il documento risultante all'esterno del contenitore dell'applicazione in un percorso scelto dall'utente.

Per spostare un documento in una posizione esterna, procedere come segue:

1. Creare innanzitutto un nuovo documento in un percorso locale o temporaneo.
1. Creare un `NSUrl` oggetto che punti al nuovo documento.
1. Aprire un nuovo controller di visualizzazione selezione documento e passarlo `NSUrl` con la `MoveToService` modalità. 
1. Una volta che l'utente sceglie una nuova posizione, il documento verrà spostato dalla posizione corrente alla nuova posizione.
1. Un documento di riferimento verrà scritto nel contenitore dell'applicazione dell'app in modo che l'accesso al file possa essere ancora eseguito dall'applicazione di creazione.


Il codice seguente può essere usato per spostare un documento in una posizione esterna:`var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

Il documento di riferimento restituito dal processo precedente è esattamente uguale a quello creato dalla modalità di apertura della selezione del documento. Tuttavia, in alcuni casi l'applicazione potrebbe voler spostare un documento senza conservarvi un riferimento.

Per spostare un documento senza generare un riferimento, usare la `ExportToService` modalità. Esempio: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

Quando si usa `ExportToService` la modalità, il documento viene copiato nel contenitore esterno e la copia esistente viene lasciata nella posizione originale.

## <a name="document-provider-extensions"></a>Estensioni del provider di documenti

Con iOS 8, Apple desidera che l'utente finale sia in grado di accedere ai propri documenti basati sul cloud, indipendentemente dalla loro esistenza. Per raggiungere questo obiettivo, iOS 8 fornisce un nuovo meccanismo di estensione del provider di documenti.

### <a name="what-is-a-document-provider-extension"></a>Che cos'è un'estensione del provider di documenti?

In poche parole, un'estensione del provider di documenti è un modo per uno sviluppatore, o di terze parti, di fornire a un'applicazione un'archiviazione di documenti alternativa a cui è possibile accedere nello stesso modo in cui si trova il percorso di archiviazione di iCloud esistente.

L'utente può selezionare uno di questi percorsi di archiviazione alternativi dalla selezione del documento e può utilizzare esattamente le stesse modalità di accesso (apertura, importazione, spostamento o esportazione) per lavorare con i file in tale percorso.

Questa operazione viene implementata usando due estensioni diverse:

- **Estensione di selezione documento** : fornisce `UIViewController` una sottoclasse che fornisce un'interfaccia grafica per l'utente per scegliere un documento da un percorso di archiviazione alternativo. Questa sottoclasse verrà visualizzata come parte del controller di visualizzazione selezione documento.
- **Estensione** per la fornitura di file: si tratta di un'estensione non dell'interfaccia utente che gestisce effettivamente il contenuto dei file. Queste estensioni vengono fornite tramite il coordinamento dei `NSFileCoordinator` file (). Questo è un altro caso importante in cui è necessario il coordinamento dei file.


Nel diagramma seguente viene illustrato il flusso di dati tipico quando si utilizzano le estensioni del provider di documenti:

 [![](document-picker-images/image39.png "Questo diagramma mostra il flusso di dati tipico quando si utilizzano le estensioni del provider di documenti")](document-picker-images/image39.png#lightbox)

Si verifica il seguente processo:

1. L'applicazione presenta un controller di selezione documento per consentire all'utente di selezionare un file da usare.
1. L'utente seleziona un percorso di file alternativo e l' `UIViewController` estensione personalizzata viene chiamata per visualizzare l'interfaccia utente.
1. L'utente seleziona un file da questo percorso e l'URL viene passato di nuovo al selettore del documento.
1. Il selettore documenti seleziona l'URL del file e lo restituisce all'applicazione per cui l'utente deve lavorare.
1. L'URL viene passato al coordinatore di file per restituire il contenuto dei file all'applicazione.
1. Il coordinatore file chiama l'estensione del provider di file personalizzata per recuperare il file.
1. Il contenuto del file viene restituito al coordinatore di file.
1. Il contenuto del file viene restituito all'applicazione.


### <a name="security-and-bookmarks"></a>Sicurezza e segnalibri

In questa sezione viene illustrato in modo rapido come la sicurezza e l'accesso ai file persistente tramite segnalibri funziona con le estensioni del provider di documenti. A differenza del provider di documenti iCloud, che consente di salvare automaticamente i segnalibri e la sicurezza nel contenitore di applicazioni, le estensioni del provider di documenti non fanno parte del sistema di riferimento del documento.

Ad esempio, in un'impostazione aziendale che fornisce un archivio dati protetto a livello aziendale, gli amministratori non desiderano che le informazioni aziendali riservate siano accessibili o elaborate dai server iCloud pubblici. Pertanto, non è possibile utilizzare il sistema di riferimento al documento incorporato.

Il sistema di segnalibri può comunque essere usato ed è responsabilità dell'estensione del provider di file elaborare correttamente un URL con segnalibro e restituire il contenuto del documento a cui fa riferimento.

Per motivi di sicurezza, iOS 8 dispone di un livello di isolamento che rende permanente le informazioni sull'applicazione che ha accesso a quale identificatore all'interno del provider di file. Si noti che tutti gli accessi ai file sono controllati da questo livello di isolamento.

Nel diagramma seguente viene illustrato il flusso di dati quando si utilizzano segnalibri e un'estensione del provider di documenti:

 [![](document-picker-images/image40.png "Questo diagramma mostra il flusso di dati quando si utilizzano segnalibri e un'estensione del provider di documenti")](document-picker-images/image40.png#lightbox)

Si verifica il seguente processo:

1. L'applicazione sta per entrare nello sfondo ed è necessario che lo stato sia permanente. Chiama `NSUrl` per creare un segnalibro in un file in una risorsa di archiviazione alternativa.
1. `NSUrl`chiama l'estensione del provider di file per ottenere un URL persistente per il documento. 
1. L'estensione del provider di file restituisce l'URL come stringa a `NSUrl` .
1. Il `NSUrl` aggrega l'URL in un segnalibro e lo restituisce all'applicazione.
1. Quando l'applicazione si sveglia in background e deve ripristinare lo stato, passa il segnalibro a `NSUrl` .
1. `NSUrl`chiama l'estensione del provider di file con l'URL del file.
1. Il provider di estensione di file accede al file e restituisce il percorso del file a `NSUrl` .
1. Il percorso del file viene aggregato con le informazioni di sicurezza e restituito all'applicazione.


Da qui, l'applicazione può accedere al file e usarlo come di consueto.

### <a name="writing-files"></a>Scrittura di file

In questa sezione viene illustrato rapidamente come scrivere file in un percorso alternativo con un'estensione del provider di documenti funziona. L'applicazione iOS userà il coordinamento dei file per salvare le informazioni su disco all'interno del contenitore dell'applicazione. Poco dopo che il file è stato scritto correttamente, l'estensione del provider di file riceverà una notifica della modifica.

A questo punto, l'estensione del provider di file può avviare il caricamento del file nel percorso alternativo (oppure contrassegnare il file come Dirty e richiedere il caricamento).

### <a name="creating-new-document-provider-extensions"></a>Creazione di nuove estensioni del provider di documenti

La creazione di nuove estensioni del provider di documenti esula dall'ambito di questo articolo introduttivo. Queste informazioni sono fornite qui per mostrare che, in base alle estensioni caricate da un utente nel dispositivo iOS, un'applicazione può accedere ai percorsi di archiviazione dei documenti all'esterno del percorso iCloud fornito da Apple.

Lo sviluppatore deve tenere presente questo fatto quando si utilizza il selettore documenti e si utilizzano documenti esterni. Non devono presupporre che tali documenti siano ospitati in iCloud.

Per ulteriori informazioni sulla creazione di un provider di archiviazione o di un'estensione per selezione documento, vedere il documento [Introduzione alle estensioni per le app](~/ios/platform/extensions.md) .

## <a name="migrating-to-icloud-drive"></a>Migrazione all'unità iCloud

In iOS 8 gli utenti possono scegliere di continuare a usare il sistema di documenti iCloud esistente usato in iOS 7 (e sistemi precedenti) oppure possono scegliere di eseguire la migrazione dei documenti esistenti al nuovo meccanismo di unità iCloud.

In Mac OS X Yosemite Apple non fornisce la compatibilità con le versioni precedenti, pertanto è necessario eseguire la migrazione di tutti i documenti nell'unità iCloud, altrimenti questi ultimi non verranno più aggiornati nei dispositivi.

Dopo che è stata eseguita la migrazione dell'account di un utente all'unità iCloud, solo i dispositivi che usano l'unità iCloud saranno in grado di propagare le modifiche ai documenti tra i dispositivi.

> [!IMPORTANT]
> Gli sviluppatori devono tenere presente che le nuove funzionalità descritte in questo articolo sono disponibili solo se è stata eseguita la migrazione dell'account dell'utente all'unità iCloud. 

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato le modifiche apportate alle API iCloud esistenti necessarie per supportare l'unità iCloud e il nuovo controller di visualizzazione selezione documento. Ha trattato il coordinamento dei file e il motivo per cui è importante quando si lavora con documenti basati sul cloud. È stata illustrata la configurazione necessaria per abilitare i documenti basati sul cloud in un'applicazione Novell. iOS e viene fornita un'introduzione introduttiva all'uso di documenti all'esterno di un contenitore di applicazioni dell'app usando il controller di visualizzazione di selezione documento.

Inoltre, in questo articolo sono state brevemente analizzate le estensioni dei provider di documenti e il motivo per cui lo sviluppatore ne deve essere a conoscenza durante la scrittura di applicazioni in grado di gestire documenti basati sul cloud.

## <a name="related-links"></a>Collegamenti correlati

- [DocPicker (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-docpicker)
- [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Introduzione alle estensioni dell'app](~/ios/platform/extensions.md)
