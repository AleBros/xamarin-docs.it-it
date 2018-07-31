---
title: Selezione di documenti in xamarin. IOS
description: Questo documento descrive il selettore documenti iOS e illustra come usarlo in xamarin. IOS. Richiede un'occhiata a iCloud, documenti, codice di configurazione comuni, le estensioni di provider di documento e altro ancora.
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/05/2017
ms.openlocfilehash: ca0c7a6e655fdc44aa673a59be71bc83044d3085
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353334"
---
# <a name="document-picker-in-xamarinios"></a>Selezione di documenti in xamarin. IOS

La selezione di documenti consente i documenti devono essere condivisi tra app. Questi documenti possono essere archiviati in iCloud o nella directory dell'app diverse. I documenti sono condivisi tramite il set di [le estensioni di Provider documento](~/ios/platform/extensions.md) l'utente ha installato sul proprio dispositivo. 

A causa della difficoltà di mantenere sincronizzati le App e nel cloud i documenti, introducono una certa quantità di complessità necessari.

## <a name="requirements"></a>Requisiti

Di seguito è necessario per completare la procedura descritta in questo articolo:

-  **Xcode 7 e iOS 8 o versione superiore** – Apple Xcode 7 e iOS 8 o le API più recenti devono essere installati e configurati nel computer dello sviluppatore.
-  **Visual Studio o Visual Studio per Mac** : deve essere installata la versione più recente di Visual Studio per Mac.
-  **Dispositivo iOS** : un dispositivo iOS che eseguono iOS 8 o versione successiva.

## <a name="changes-to-icloud"></a>Modifiche in iCloud

Per implementare le nuove funzionalità di selezione di documenti, le modifiche seguenti sono state apportate a iCloud Apple servizio:

-  Il Daemon iCloud è stato completamente riscritto usando CloudKit.
-  ICloud esistenti sono state rinominate iCloud Drive.
-  È stato aggiunto il supporto per il sistema operativo Windows di Microsoft su iCloud.
-  È stata aggiunta una cartella di iCloud nel Finder del sistema operativo Mac.
-  i dispositivi iOS è possono accedere al contenuto della cartella del sistema operativo Mac iCloud.

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

## <a name="what-is-a-document"></a>Che cos'è un documento?

Quando si fa riferimento a un documento in iCloud, è un'entità singola, autonoma e deve essere considerata come tale dall'utente. Un utente potrebbe voler modificare il documento o condividerlo con altri utenti (tramite posta elettronica, ad esempio).

Esistono diversi tipi di file che l'utente immediatamente riconosceranno come documenti, ad esempio le pagine, file Keynote o numeri. Tuttavia, iCloud non è limitato a questo concetto. Ad esempio, lo stato di un gioco (ad esempio, una corrispondenza Chess) può essere considerato come un documento e archiviato in iCloud. Questo file può essere passato tra i dispositivi dell'utente e consentire loro di prelevare un gioco in cui si era interrotto in un altro dispositivo.

## <a name="dealing-with-documents"></a>Gestione di documenti

Prima di approfondire il codice necessario per usare il selettore di documenti con Xamarin, questo articolo verrà per coprire le procedure consigliate per l'utilizzo di documenti iCloud e alcune delle modifiche apportate alle API esistenti necessarie per supportare la selezione di documenti.

### <a name="using-file-coordination"></a>Tramite il coordinamento di File

Poiché un file può essere modificato in diverse posizioni, è necessario utilizzare coordinamento per evitare la perdita di dati.

 [![](document-picker-images/image1.png "Tramite il coordinamento di File")](document-picker-images/image1.png#lightbox)

Diamo un'occhiata nella figura precedente:

1.  Un dispositivo iOS tramite il coordinamento di file crea un nuovo documento e lo salva nella cartella iCloud.
2.  iCloud consente di salvare il file modificato nel cloud per la distribuzione in ogni dispositivo.
3.  Un Mac collegato vede il file modificato nella cartella iCloud e utilizza il coordinamento di File per copiare le modifiche apportate al file.
4.  Un dispositivo non utilizza File coordinamento apporta una modifica al file e lo salva nella cartella iCloud. Queste modifiche vengono replicate immediatamente in altri dispositivi.

Si supponga originale dispositivo iOS o Mac modificando il file, ora le modifiche vengono persi e sovrascritte con la versione del file dal dispositivo non coordinato. Per evitare la perdita di dati, il coordinamento di File è indispensabile quando si lavora con documenti basati su cloud.

### <a name="using-uidocument"></a>Usando UIDocument

 `UIDocument` tutto diventa semplice (o `NSDocument` in macOS) in questo modo tutto il lavoro sporco per gli sviluppatori. Fornisce compilato in File coordinamento con le code in background per evitare che il blocco dell'interfaccia utente dell'applicazione.

 `UIDocument` espone più, richiede l'API di alto livello che semplificano le attività di sviluppo di un'applicazione Xamarin per qualsiasi scopo lo sviluppatore.

Il codice seguente crea una sottoclasse di `UIDocument` per implementare un documento generico basati su testo che può essere utilizzato per archiviare e recuperare il testo da iCloud:

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

Il `GenericTextDocument` classe presentata nell'esempio precedente viene usata in questo articolo quando si lavora con il selettore documenti e i documenti esterni in un'applicazione xamarin. IOS 8.

## <a name="asynchronous-file-coordination"></a>Coordinamento di File asincrono

iOS 8 offre numerose nuove funzionalità di coordinamento File asincrona tramite le nuove API di coordinamento di File. Prima di iOS 8, tutte le API di coordinamento dei File esistenti sono stati completamente sincrone. In questo modo che lo sviluppatore è responsabile dell'implementazione proprio sfondo Accodamento messaggi per impedire il coordinamento di File di blocco dell'interfaccia utente dell'applicazione.

Il nuovo `NSFileAccessIntent` classe contiene un URL che punta al file e diverse opzioni per controllare il tipo di coordinamento necessario. Il codice seguente viene illustrato come spostare un file da una posizione a un'altra usando gli Intent:

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

## <a name="discovering-and-listing-documents"></a>L'individuazione e visualizzazione di un elenco documenti

Consente di individuare ed elencare documenti consiste nell'usare esistente `NSMetadataQuery` API. Questa sezione illustra le nuove funzionalità aggiunte a `NSMetadataQuery` funzionante con i documenti che rendono ancora più semplice rispetto a prima.

### <a name="existing-behavior"></a>Comportamento esistente

Prima di iOS 8, `NSMetadataQuery` sono ridotte per le modifiche di prelievo file locale, ad esempio: Elimina e crea Rinomina.

 [![](document-picker-images/image2.png "Panoramica sulle modifiche di NSMetadataQuery file locale")](document-picker-images/image2.png#lightbox)

Nel diagramma precedente:

1.  Per i file già presenti nel contenitore dell'applicazione `NSMetadataQuery` ha `NSMetadata` record creato in precedenza e lo spooling in modo che siano immediatamente disponibili per l'applicazione.
1.  L'applicazione crea un nuovo file nel contenitore dell'applicazione.
1.  Si verifica un ritardo prima `NSMetadataQuery` vede la modifica al contenitore dell'applicazione e vengono creati il `NSMetadata` record.


A causa il ritardo nella creazione del `NSMetadata` record, l'applicazione deve avere dati di due origini aprire: uno per le modifiche ai file locale e uno per il cloud in base a modifiche.

### <a name="stitching"></a>Operazioni di unione

In iOS 8, `NSMetadataQuery` è più facile da usare direttamente con una nuova funzionalità denominata unione:

 [![](document-picker-images/image3.png "NSMetadataQuery con una nuova funzionalità denominata unione")](document-picker-images/image3.png#lightbox)

Utilizzo di unione nel diagramma precedente:

1.  Come in precedenza, per i file già presenti nel contenitore dell'applicazione `NSMetadataQuery` ha `NSMetadata` record creato in precedenza e lo spooling.
1.  L'applicazione crea un nuovo file nel contenitore dell'applicazione tramite il coordinamento di File.
1.  Una funzione di hook nel contenitore di applicazione vede la modifica e chiama `NSMetadataQuery` creare i necessari `NSMetadata` record.
1.  Il `NSMetadata` record viene creato direttamente dopo il file e viene resa disponibile per l'applicazione.


Unione con l'applicazione non è più necessario aprire un'origine dati per il monitoraggio locale e le modifiche ai file basati su cloud. A questo punto l'applicazione può basarsi su `NSMetadataQuery` direttamente.

> [!IMPORTANT]
> L'unione funziona solo se l'applicazione usa il coordinamento di File come presentata nella sezione precedente. Se non è viene utilizzato il coordinamento di File, il comportamento di iOS 8 precedenti esistente per impostazione predefinita le API.




### <a name="new-ios-8-metadata-features"></a>Nuove funzionalità di metadati di iOS 8

Le nuove funzionalità seguenti sono stati aggiunti a `NSMetadataQuery` in iOS 8:

-   `NSMetatadataQuery` ora è possibile elencare i documenti non locali archiviati nel cloud.
-  Sono state aggiunte nuove API per accedere alle informazioni di metadati sui documenti basata sul cloud. 
-  C'è un nuovo `NSUrl_PromisedItems` API che consente di accedere gli attributi del file che possono o non dispone i contenuti disponibili in locale.
-  Usare la `GetPromisedItemResourceValue` metodo per ottenere informazioni su un determinato file oppure utilizzare il `GetPromisedItemResourceValues` metodo per ottenere informazioni su più di un file alla volta.


Sono stati aggiunti due nuovi flag di coordinamento di file per la gestione dei metadati:

-   `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
-   `NSFileCoordinatorWriteContentIndependentMetadataOnly` 


Con i flag precedenti, il contenuto del file del documento non è necessario essere disponibili in locale per poter essere usato.

Segmento di codice seguente viene illustrato come utilizzare `NSMetadataQuery` per eseguire una query per l'esistenza di un file specifico e compilare il file se non esiste:

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
            new Selector("queryDidFinishGathering:"),           NSMetadataQuery.DidFinishGatheringNotification,
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

### <a name="document-thumbnails"></a>Anteprime dei documenti

Apple ritiene che la migliore esperienza utente quando si elencano i documenti per un'applicazione consiste nell'usare le anteprime. In questo modo il contesto agli utenti finali, in modo che è possibile identificare rapidamente il documento che si desidera utilizzare.

Prima di iOS 8, che mostra le anteprime di documenti necessaria un'implementazione personalizzata. Novità di iOS 8 in attributi di file system che consentono agli sviluppatori di lavorare rapidamente con le anteprime dei documenti.

#### <a name="retrieving-document-thumbnails"></a>Durante il recupero delle anteprime di documenti 

Chiamando il `GetPromisedItemResourceValue` o `GetPromisedItemResourceValues` metodi `NSUrl_PromisedItems` API, un `NSUrlThumbnailDictionary`, viene restituito. L'unica chiave attualmente in questo dizionario è il `NSThumbnial1024X1024SizeKey` e la relativa corrispondenza `UIImage`.

#### <a name="saving-document-thumbnails"></a>Salvataggio delle anteprime di documenti

Il modo più semplice per salvare un'immagine di anteprima consiste nell'usare `UIDocument`. Chiamando il `GetFileAttributesToWrite` metodo del `UIDocument` e impostare l'anteprima, verrà salvato automaticamente quando il file di documento. ICloud Daemon verrà vedere questa modifica e propagarlo a iCloud. In Mac OS X, le anteprime vengono generate automaticamente per gli sviluppatori mediante il plug-in ricerca rapida.

Con le nozioni di base dell'utilizzo di documenti iCloud basato sul posto, con le modifiche apportate all'API esistente, siamo pronti implementare il Controller di visualizzazione documento selezione in un esempio di Xamarin iOS 8 dell'applicazione per dispositivi mobili.


## <a name="enabling-icloud-in-xamarin"></a>Abilitazione di iCloud in Xamarin

Prima di poter utilizzare il selettore di documento in un'applicazione xamarin. IOS, iCloud supporto deve essere abilitata nell'applicazione e tramite Apple. 

Procedura dettagliata che segue questa procedura il processo di provisioning per iCloud.

1. Creare un contenitore iCloud.
2. Creare un ID App che contiene il servizio App iCloud.
3. Creare un profilo di Provisioning che include questo ID. App

Il [uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) guida descrive in dettaglio i primi due passaggi. Per creare un profilo di provisioning, seguire i passaggi descritti nel [profilo di Provisioning](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) Guida.



Procedura dettagliata che segue i passaggi del processo di configurazione dell'applicazione per iCloud:

Seguire questa procedura:

1.  Aprire il progetto in Visual Studio per Mac o Visual Studio.
2.  Nel **Esplora soluzioni**, fare clic sul progetto e selezionare le opzioni.
3.  Nella finestra Seleziona finestra di dialogo Opzioni **applicazione iOS**, assicurarsi che il **identificatore del Bundle** corrisponda a quello definito nel **ID App** creato in precedenza per l'applicazione. 
4.  Selezionare **firma del Bundle iOS**, selezionare la **identità Developer** e il **profilo di Provisioning** creato in precedenza.
5.  Scegliere il **OK** pulsante per salvare le modifiche e chiudere la finestra di dialogo.
6.  Fare clic su `Entitlements.plist` nella **Esplora soluzioni** per aprirlo nell'editor.

    > [!IMPORTANT]
    > In Visual Studio potrebbe essere necessario aprire l'editor dei diritti facendo clic su di esso, selezionando **Apri con...** e selezionando Editor elenco proprietà

7.  Controllare **Abilita iCloud** , **documenti iCloud** , **archiviazione chiave-valore** e **CloudKit** .
8.  Verificare che il **contenitore** esiste per l'applicazione (come creato in precedenza). Esempio: `iCloud.com.your-company.AppName`
9.  Salvare le modifiche apportate al file.

Per altre informazioni su diritti consultare il [uso degli Entitlement](~/ios/deploy-test/provisioning/entitlements.md) Guida.

Con la configurazione precedente posto, l'applicazione ora possibile usare documenti basati su cloud e il nuovo Controller di visualizzazione selezione documento.

## <a name="common-setup-code"></a>Codice di configurazione comune

Prima di iniziare con il Controller di visualizzazione di selezione dei documenti, è disponibile del codice di installazione standard necessario. Iniziare modificando l'applicazione `AppDelegate.cs` file e renderlo simile al seguente:

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
                    // Yes, inform caller and save location the the Application Container
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
> Il codice riportato sopra include il codice nella sezione elenco di documenti e scoperta precedente. Viene presentato qui nella sua interezza, quanto viene visualizzato in un'applicazione effettiva. Per semplicità, questo esempio funziona con un file singolo, hardcoded (`test.txt`) solo.

Il codice sopra riportato espone diverse iCloud unità tasti di scelta rapida per renderli più facile lavorare con il resto dell'applicazione.

Successivamente, aggiungere il codice seguente per le viste e un contenitore di visualizzazione che verrà utilizzando il selettore di documento o utilizzo dei documenti basata sul cloud:

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

Ciò consente di aggiungere un collegamento per ottenere il `AppDelegate` e accedere ai collegamenti di iCloud creati in precedenza.

Con questo codice, diamo un'occhiata a implementare il Controller di visualizzazione di selezione documento in un'applicazione per Xamarin iOS 8.

## <a name="using-the-document-picker-view-controller"></a>Tramite il Controller di visualizzazione di selezione documento

Prima di iOS 8, era molto difficile accedere ai documenti da un'altra applicazione, perché si è verificato alcun modo per individuare documenti all'esterno dell'applicazione dall'interno dell'app.

### <a name="existing-behavior"></a>Comportamento esistente

 [![](document-picker-images/image31.png "Cenni preliminari sul comportamento esistente")](document-picker-images/image31.png#lightbox)

Diamo un'occhiata all'accesso a un documento esterno prima di iOS 8:

1.  Prima di tutto l'utente dovrà aprire l'applicazione che ha creato il documento.
1.  Il documento sia selezionato e `UIDocumentInteractionController` viene usato per inviare il documento per la nuova applicazione.
1.  Infine, una copia del documento originale viene inserita nel contenitore della nuova applicazione.


Da qui è disponibile per la seconda applicazione di aprire e modificare il documento.

### <a name="discovering-documents-outside-of-an-apps-container"></a>L'individuazione di documenti all'esterno contenitore di un'App

In iOS 8, un'applicazione è in grado di accedere ai documenti all'esterno di un proprio contenitore di applicazioni con facilità:

 [![](document-picker-images/image32.png "L'individuazione di documenti all'esterno contenitore di un'App")](document-picker-images/image32.png#lightbox)

Con il nuovo iCloud selezione documento ( `UIDocumentPickerViewController`), un'applicazione iOS direttamente può individuare e accedere all'esterno del relativo contenitore dell'applicazione. Il `UIDocumentPickerViewController` fornisce un meccanismo per l'utente concedere l'accesso a e modificare quelli individuati i documenti tramite le autorizzazioni.

Un'applicazione deve acconsentire esplicitamente per i relativi documenti visualizzati nella selezione documenti iCloud e disponibili per altre applicazioni individuare e lavorare con loro. Per avere un'applicazione per Xamarin iOS 8 relativo contenitore dell'applicazione di condivisione, modificarlo `Info.plist` file in un editor di testo standard e aggiungere le due righe seguenti alla fine del dizionario (tra la `<dict>...</dict>` tag):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

Il `UIDocumentPickerViewController` fornisce un'ottima nuova interfaccia utente che consente all'utente di scegliere i documenti. Per visualizzare il Controller di visualizzazione documento selezione in un'applicazione per Xamarin iOS 8, eseguire le operazioni seguenti:

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
> Lo sviluppatore deve chiamare il `StartAccessingSecurityScopedResource` metodo del `NSUrl` prima di un documento esterno è accessibile. Il `StopAccessingSecurityScopedResource` metodo deve essere chiamato per rilasciare il blocco di sicurezza, non appena il documento è stato caricato.

### <a name="sample-output"></a>Esempio di output

Di seguito è riportato un esempio di come il codice riportato sopra verrà visualizzato un selettore di documento quando eseguita in un dispositivo iPhone:

1.  L'utente avvia l'applicazione e viene visualizzata l'interfaccia principale:   
 
    [![](document-picker-images/image33.png "Viene visualizzata l'interfaccia principale")](document-picker-images/image33.png#lightbox)
1.  L'utente tocca il **azione** nella parte superiore dello schermo e viene chiesto di selezionare una **documento Provider** dall'elenco dei provider disponibili:   
 
    [![](document-picker-images/image34.png "Selezionare un Provider di documenti nell'elenco dei provider disponibili")](document-picker-images/image34.png#lightbox)
1.  Il **Controller visualizzazione selezione documento** viene visualizzato per il profilo selezionato **documento Provider**:   
 
    [![](document-picker-images/image35.png "Viene visualizzato il Controller di visualizzazione di selezione documento")](document-picker-images/image35.png#lightbox)
1.  L'utente tocca un **cartella documenti** per visualizzarne il contenuto:   
 
    [![](document-picker-images/image36.png "Il contenuto della cartella documenti")](document-picker-images/image36.png#lightbox)
1.  L'utente seleziona una **documento** e il **selezione documento** viene chiuso.
1.  L'interfaccia principale viene nuovamente visualizzato il **documento** viene caricato dal contenitore esterno e il relativo contenuto visualizzato.


La visualizzazione del Controller di visualizzazione di selezione documento effettiva dipende dai provider documento che l'utente ha installato nel dispositivo e la modalità di selezione del documento è stato implementare. Nell'esempio precedente Usa la modalità di apertura, gli altri tipi di modalità verranno descritte in dettaglio di seguito.

## <a name="managing-external-documents"></a>Gestione dei documenti esterni

Come illustrato in precedenza, prima di iOS 8, un'applicazione è stato possibile accedere solo ai documenti che facevano una parte del relativo contenitore dell'applicazione. In iOS 8 un'applicazione può accedere ai documenti da origini esterne:

 [![](document-picker-images/image37.png "Documenti esterni Cenni preliminare sulla gestione")](document-picker-images/image37.png#lightbox)

Quando l'utente seleziona un documento da un'origine esterna, viene scritto un documento di riferimento al contenitore dell'applicazione che punta al documento originale.

Per semplificare l'aggiunta di questa nuova funzionalità nelle applicazioni esistenti, numerose nuove funzionalità sono stati aggiunti per il `NSMetadataQuery` API. In genere, un'applicazione utilizza l'ambito documento universale ai documenti di elenco che si trovano all'interno del contenitore dell'applicazione. Usando questo ambito, solo i documenti all'interno del contenitore dell'applicazione continueranno a essere visualizzati.

Usando il nuovo ambito documento esterna comune restituirà i documenti in tempo reale all'esterno del contenitore dell'applicazione e restituiscono i metadati per loro. Il `NSMetadataItemUrlKey` punterà all'URL in cui è in realtà che si trova il documento.

In alcuni casi un'applicazione non desidera lavorare con i documenti in corso a cui punta il riferimento th. Al contrario, l'app desidera lavorare direttamente con il documento di riferimento. L'app potrebbe essere, ad esempio, per visualizzare il documento nella cartella dell'applicazione nell'interfaccia utente, o per consentire all'utente di spostarsi i riferimenti all'interno di una cartella.

In iOS 8, un nuovo `NSMetadataItemUrlInLocalContainerKey` per accedere direttamente al documento di riferimento è disponibile. Tale chiave punta al riferimento effettivo al documento in un contenitore di applicazioni esterno.

Il `NSMetadataUbiquitousItemIsExternalDocumentKey` viene usato per verificare se un documento è esterno al contenitore di un'applicazione o meno. Il `NSMetadataUbiquitousItemContainerDisplayNameKey` viene utilizzato per accedere al nome del contenitore in cui è che ospita la copia originale di un documento esterno.

### <a name="why-document-references-are-required"></a>Il motivo per cui sono necessari riferimenti documento

Il motivo principale per che tale iOS 8 Usa i riferimenti per accedere a documenti esterni è sicurezza. Nessuna applicazione di accedere a contenitore qualsiasi altra applicazione. Solo la selezione di documenti possono farlo, perché è in esecuzione out-of-process e ha accesso all'intero sistema.

L'unico modo per ottenere un documento all'esterno del contenitore dell'applicazione consiste nell'usare il selettore di documento e se l'URL restituito per la selezione è con ambito di protezione. L'URL con ambito di protezione contiene informazioni sufficienti per documento e con ambiti diritti necessari per concedere a un'applicazione l'accesso al documento selezionato.

È importante notare che se l'URL con ambito di protezione è stato serializzato in una stringa e quindi deserializzato, le informazioni di sicurezza potrebbero essere perse e il file sarebbe accessibile dall'URL. La funzionalità di riferimento del documento fornisce un meccanismo per tornare ai file a cui fa riferimento a questi URL.

Pertanto, se l'applicazione acquisisca un `NSUrl` da uno dei documenti di riferimento, già ha l'ambito di protezione associato ed è utilizzabile per accedere al file. Per questo motivo, è altamente consigliabile che lo sviluppatore Usa `UIDocument` perché tutte queste informazioni e i processi gestisce per loro.

### <a name="using-bookmarks"></a>Utilizzo dei bookmark

Non è sempre fattibile per enumerare i documenti di un'applicazione per tornare a uno specifico documento, ad esempio, quando si esegue il ripristino dello stato. iOS 8 fornisce un meccanismo per creare segnalibri destinati direttamente a un determinato documento.

Il codice seguente crea un segnalibro da una `UIDocument`del `FileUrl` proprietà:

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

L'API di segnalibro esistente viene usata per creare un segnalibro con un oggetto esistente `NSUrl` che può essere salvato e caricato per fornire accesso diretto a un file esterno. Il codice seguente verrà ripristinato un segnalibro a cui è stato creato in precedenza:

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

## <a name="open-vs-import-mode-and-the-document-picker"></a>Aprire Visual Studio. Modalità di importazione e la selezione documento

Il Controller di visualizzazione di selezione documento sono disponibili due modalità diverse di funzionamento:

1.  **Aprire la modalità** : In questa modalità, quando l'utente seleziona e documento esterno, la selezione di documenti verrà creato un segnalibro con ambito di sicurezza nel contenitore dell'applicazione.   
 
    [![](document-picker-images/image37.png "Una protezione con l'ambito di segnalibro nel contenitore dell'applicazione")](document-picker-images/image37.png#lightbox)
1.  **Modalità importazione** : In questa modalità, quando l'utente seleziona e documento esterno, la selezione di documenti verrà non crea un segnalibro, ma al contrario, copiare il file in un percorso temporaneo e fornire l'accesso all'applicazione al documento in questa posizione:   
 
    [![](document-picker-images/image38.png "La selezione documento verrà copiare il file in un percorso temporaneo e fornire l'accesso all'applicazione al documento in questa posizione")](document-picker-images/image38.png#lightbox)   
 Dopo la chiusura dell'applicazione per qualsiasi motivo, il percorso temporaneo viene svuotato e rimuovere il file. Se l'applicazione deve mantenere l'accesso al file, deve creare una copia e posizionarlo nel relativo contenitore dell'applicazione.


La modalità di apertura è utile quando l'applicazione desidera collaborare con un'altra applicazione e condividono tutte le modifiche apportate al documento con quell'applicazione. La modalità di importazione viene utilizzata quando l'applicazione non desidera condividere le modifiche a un documento con altre applicazioni.

## <a name="making-a-document-external"></a>Rendere un documento esterno

Come indicato in precedenza, un'applicazione iOS 8 non ha accesso ai contenitori all'esterno del proprio contenitore dell'applicazione. L'applicazione può scrivere il proprio contenitore in locale o in un percorso temporaneo, quindi utilizzare la modalità di un documento speciale per spostare il documento risultante all'esterno del contenitore dell'applicazione in un percorso di scelto dall'utente.

Per spostare un documento in una posizione esterna, eseguire le operazioni seguenti:

1.  Prima di tutto creare un nuovo documento in un percorso locale o temporaneo.
1.  Creare un `NSUrl` che punta al documento nuovo.
1.  Aprire un nuovo Controller di visualizzazione documento selezione e passare il `NSUrl` con la modalità di `MoveToService` . 
1.  Una volta che l'utente sceglie una nuova posizione, il documento sarà spostato dalla posizione corrente alla nuova posizione.
1.  Verrà scritto un documento di riferimento al contenitore dell'applicazione dell'app in modo che il file è ancora accessibile dall'applicazione di creazione.


Il codice seguente può essere utilizzato per spostare un documento in una posizione esterna: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

Il documento di riferimento restituito dal processo precedente è esattamente uguale a uno creato per la modalità di apertura della selezione documento. Tuttavia, esistono volte in cui l'applicazione può essere necessario spostare un documento senza mantenere un riferimento a esso.

Per spostare un documento senza la generazione di un riferimento, usare il `ExportToService` modalità. Esempio: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

Quando si usa il `ExportToService` modalità, il documento viene copiato nel contenitore esterno e la copia esistente viene lasciata nella posizione originale.

## <a name="document-provider-extensions"></a>Estensioni di Provider di documento

Con iOS 8, Apple richiede l'utente finale sia in grado di accedere a uno dei propri documenti basata sul cloud, indipendentemente da dove vengono effettivamente esistenti. Per raggiungere questo obiettivo, iOS 8 fornisce un meccanismo di estensione per Provider documento nuovo.

### <a name="what-is-a-document-provider-extension"></a>Che cos'è un'estensione per Provider documento?

Semplificando, un'estensione per Provider di documento è un modo per uno sviluppatore o una terza parte, per fornire un documento alternativo nell'archiviazione di applicazioni che sono accessibili nel esattamente così come percorso di archiviazione iCloud esistente.

L'utente può selezionare una di queste posizioni di archiviazione alternativo da Selezione tipo di documento e usano le esatte stessa modalità di accesso (Open, importazione, spostare o esportazione) per lavorare con i file in tale percorso.

Ciò viene implementato tramite due estensioni diverse:

-  **Estensione per selezione documento** : fornisce un `UIViewController` sottoclasse che fornisce un'interfaccia grafica per l'utente di scegliere un documento da una posizione di archiviazione alternativi. Questa sottoclasse verrà visualizzata come parte del Controller di visualizzazione di selezione documento.
-  **Fornire estensione file** – si tratta di un'estensione non di interfaccia utente che si occupasse effettivamente che fornisce il contenuto di file. Queste estensioni vengono fornite tramite il coordinamento di File ( `NSFileCoordinator` ). Si tratta di un altro importante caso in cui è necessario il coordinamento di File.


Il diagramma seguente mostra il tipico flusso di dati quando si lavora con le estensioni di Provider di documento:

 [![](document-picker-images/image39.png "Questo diagramma mostra il tipico flusso di dati quando si lavora con le estensioni di Provider di documento")](document-picker-images/image39.png#lightbox)

Si verifica quanto segue:

1.  L'applicazione presenta un Controller di selezione documento per consentire all'utente di selezionare un file da utilizzare.
1.  L'utente seleziona un percorso file alternativo e personalizzata `UIViewController` estensione viene chiamata per visualizzare l'interfaccia utente.
1.  L'utente seleziona un file da questo percorso e l'URL viene passata al selettore di documento.
1.  La selezione documento Seleziona URL del file e lo restituisce all'applicazione per l'utente a lavorarvi.
1.  L'URL viene passato per il coordinatore di File per ottenere il contenuto di file all'applicazione.
1.  Il File Coordinator chiama l'estensione del Provider di File personalizzato per recuperare il file.
1.  Il contenuto del file viene restituito per il File Coordinator.
1.  Il contenuto del file viene restituito all'applicazione.


### <a name="security-and-bookmarks"></a>Sicurezza e i segnalibri

In questa sezione richiederà un esame rapido come accedere a sicurezza e i file persistenti tramite works segnalibri con le estensioni di Provider di documento. A differenza di iCloud documento Provider, che salva automaticamente i segnalibri e sicurezza per il contenitore di applicazioni, le estensioni di Provider di documento non perché non sono una parte del sistema di riferimento documento.

Ad esempio: in uno scenario aziendale che fornisce il proprio archivio di dati sicura a livello aziendale, gli amministratori non desiderano le informazioni aziendali riservate accessibili o elaborato da iCloud pubblica i server. Pertanto, il sistema di riferimento documento predefinito non è utilizzabile.

Il sistema di segnalibro può ancora essere utilizzato ed è responsabilità dell'estensione di Provider di File per elaborare correttamente un URL con segnalibro e restituire il contenuto del documento a cui punta è.

Per motivi di sicurezza, iOS 8 dispone di un livello di isolamento che rende persistenti le informazioni su quali applicazioni può accedere a quali identificatore all'interno di quale Provider di File. Si noti che tutti gli accessi di file sono controllato da questo livello di isolamento.

Il diagramma seguente mostra il flusso di dati quando si lavora con i segnalibri e un'estensione per Provider di documento:

 [![](document-picker-images/image40.png "Questo diagramma mostra il flusso di dati quando si lavora con i segnalibri e un'estensione per Provider di documento")](document-picker-images/image40.png#lightbox)

Si verifica quanto segue:

1.  L'applicazione sta per entrare in background e deve rendere persistente lo stato. Chiama `NSUrl` per creare un segnalibro in un file di archiviazione alternativi.
1.  `NSUrl` chiama l'estensione del Provider di File per ottenere un URL persistente nel documento. 
1.  L'estensione del Provider di File restituisce l'URL sotto forma di stringa per il `NSUrl` .
1.  Il `NSUrl` aggrega l'URL in un segnalibro e lo restituisce all'applicazione.
1.  Quando l'applicazione si ripristina l'in background ed è necessario ripristinare lo stato, passa il segnalibro `NSUrl` .
1.  `NSUrl` chiama l'estensione del Provider di File con l'URL del file.
1.  Il Provider di estensioni di File accede al file e restituisce il percorso del file da `NSUrl` .
1.  Il percorso del file è in bundle con le informazioni di sicurezza e restituito all'applicazione.


A questo punto, l'applicazione può accedere al file e usarli come di consueto.

### <a name="writing-files"></a>La scrittura di file

In questa sezione richiederà un esame rapido come la scrittura di file in un percorso alternativo con un'estensione per Provider documento works. L'applicazione iOS utilizzerà il coordinamento di File per salvare le informazioni sul disco all'interno del contenitore dell'applicazione. Poco dopo il file è stata scritta correttamente, l'estensione del Provider di File riceverà una notifica della modifica.

A questo punto, l'estensione del Provider di File può iniziare a caricare il file nel percorso alternativo (o contrassegnare il file come dirty e la richiesta di caricamento).

### <a name="creating-new-document-provider-extensions"></a>Creazione di nuove estensioni di Provider di documento

Creazione di nuove estensioni di Provider di documento non è compreso nell'ambito di questo articolo introduttivo. Queste informazioni sono fornite di seguito per dimostrare che, in base alle estensioni di che un utente non è caricato nel proprio dispositivo iOS, un'applicazione potrebbe avere accesso alle posizioni di archiviazione di documenti all'esterno di Apple fornito percorso iCloud.

Lo sviluppatore di tenere presente questo fatto quando si utilizza il selettore di documento e l'utilizzo di documenti esterni. Essi non devono presupporre che questi documenti vengono ospitati in iCloud.

Per altre informazioni sulla creazione di un Provider di archiviazione o l'estensione per selezione documento, vedere la [Introduzione alle App estensioni](~/ios/platform/extensions.md) documento.

## <a name="migrating-to-icloud-drive"></a>Eseguire la migrazione a iCloud Drive

In iOS 8, gli utenti possono scegliere di continuare con iCloud esistente documenti sistema utilizzato in iOS 7 (e nei sistemi precedenti) o possono scegliere di eseguire la migrazione di documenti esistenti per il nuovo meccanismo di unità di iCloud.

In Mac OS X Yosemite, Apple non fornisce il con le versioni precedenti la compatibilità in modo che tutti i documenti devono essere migrati a iCloud Drive o non sarà più possibile aggiornare tra dispositivi.

Dopo che un account utente è stata eseguita la migrazione a iCloud Drive, solo i dispositivi con iCloud Drive sarà in grado di propagare le modifiche ai documenti in tali dispositivi.

> [!IMPORTANT]
> Gli sviluppatori devono tenere presente che le nuove funzionalità descritte in questo articolo sono disponibili solo se l'account dell'utente è stato migrato in iCloud Drive. 

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato le modifiche apportate a iCloud esistente, le API necessarie per supportare iCloud Drive e il nuovo Controller di visualizzazione selezione documento. È regolato da un coordinamento di File e il motivo per cui è importante quando si lavora con documenti basati su cloud. Ha illustrato la configurazione necessaria per abilitare documenti basati su cloud in un'applicazione xamarin. IOS e dato uno sguardo introduttivo di utilizzo di documenti all'esterno contenitore di un'app di applicazioni tramite il Controller di visualizzazione di selezione documento.

Inoltre, questo articolo ha illustrato brevemente le estensioni di Provider di documento e il motivo per cui lo sviluppatore deve essere necessario tenere in considerazione quando si scrivono applicazioni in grado di gestire documenti basati su cloud.

## <a name="related-links"></a>Collegamenti correlati

- [DocPicker (esempio)](https://developer.xamarin.com/samples/monotouch/ios8/DocPicker/)
- [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Introduzione alle App estensioni](~/ios/platform/extensions.md)
