---
title: Selezione di documento in xamarin. IOS
description: Questo documento descrive iOS selettore di documento e viene illustrato come utilizzare in xamarin. IOS. Accetta un'occhiata iCloud, documenti, codice di programma di installazione comune, estensioni di provider di documento e altro ancora.
ms.prod: xamarin
ms.assetid: 89539D79-BC6E-4A3E-AEC6-69D9A6CC6818
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: efa1b589ad4afe26d8b87c3db1777660b3e27be6
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786886"
---
# <a name="document-picker-in-xamarinios"></a>Selezione di documento in xamarin. IOS

La selezione del documento consente di essere condivisi tra le app documenti. Questi documenti possono essere archiviati in iCloud o nella directory dell'applicazione diverso. I documenti vengono condivisi tramite il set di [estensioni Provider documento](~/ios/platform/extensions.md) l'utente ha installato nel dispositivo. 

A causa delle difficoltà di mantenere i documenti sincronizzati tra App e il cloud, introducono una certa complessità.

## <a name="requirements"></a>Requisiti

È necessario completare i passaggi illustrati in questo articolo:

-  **Xcode 7 e iOS 8 o versione successiva** – Xcode 7 e iOS 8 o le API più recente di Apple devono essere installato e configurato nel computer dello sviluppatore.
-  **Visual Studio o Visual Studio per Mac** : deve essere installata la versione più recente di Visual Studio per Mac.
-  **Dispositivo iOS** : un dispositivo iOS che eseguono iOS 8 o versione successiva.

## <a name="changes-to-icloud"></a>Modifiche in iCloud

Per implementare le nuove funzionalità di selezione di documento, le seguenti modifiche sono state apportate in iCloud Apple servizio:

-  Il Daemon iCloud è stata completamente riscritta utilizzando CloudKit.
-  ICloud esistenti sono state rinominate iCloud unità.
-  È stato aggiunto il supporto per sistema operativo Microsoft Windows su iCloud.
-  Una cartella iCloud è stato aggiunto il Finder.
-  i dispositivi iOS è possono accedere al contenuto della cartella del sistema operativo Mac iCloud.

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

## <a name="what-is-a-document"></a>Che cos'è un documento?

Quando si fa riferimento a un documento in iCloud, è un'entità singola, autonoma e devono essere interpretato come tali dall'utente. Un utente potrebbe voler modificare il documento o condividerlo con altri utenti (tramite posta elettronica, ad esempio).

Esistono diversi tipi di file che l'utente verrà immediatamente riconoscere file del discorso di apertura o numeri come documenti, ad esempio, le pagine. Tuttavia, iCloud non è limitato a questo concetto. Ad esempio, lo stato di un gioco (ad esempio una corrispondenza Scacchi) può considerato come un documento e archiviato in iCloud. Questo file potrebbe essere passati tra i dispositivi dell'utente e consentire loro di prelevare un gioco in cui sono stati interrotti in un altro dispositivo.

## <a name="dealing-with-documents"></a>Gestione di documenti

Prima di illustrare il codice necessario per usare il selettore di documento con Xamarin, in questo articolo verrà illustrano le procedure consigliate per l'utilizzo di documenti iCloud, alcune delle modifiche apportate alle API esistenti necessari per supportare la selezione del documento.

### <a name="using-file-coordination"></a>Utilizzando il coordinamento di File

Poiché un file può essere modificato in diverse posizioni, coordinamento deve essere utilizzato per evitare la perdita di dati.

 [![](document-picker-images/image1.png "Utilizzando il coordinamento di File")](document-picker-images/image1.png#lightbox)

Esaminiamo ora nella figura precedente:

1.  Un dispositivo iOS mediante il coordinamento di file crea un nuovo documento e salvarlo nella cartella iCloud.
2.  iCloud Salva il file modificato nel cloud per la distribuzione per ogni dispositivo.
3.  Un Mac collegato vede il file modificato nella cartella iCloud e utilizza il coordinamento di File per copiare le modifiche al file.
4.  Un dispositivo non utilizza File coordinamento apporta una modifica al file e lo salva nella cartella iCloud. Queste modifiche vengono replicate immediatamente per le altre periferiche.

Si supponga originale dispositivo iOS o Mac è stato modificato il file, ora le modifiche vengono persi e sovrascritte con la versione del file dal dispositivo non coordinato. Per evitare la perdita di dati, il coordinamento di File è necessaria quando si lavora con i documenti basati su cloud.

### <a name="using-uidocument"></a>Utilizzando UIDocument

 `UIDocument` tutto diventa semplice (o `NSDocument` su macOS) in questo modo tutte le operazioni necessarie per lo sviluppatore. Fornisce compilato in File coordinamento con le code di sfondo per evitare il blocco dell'interfaccia utente dell'applicazione.

 `UIDocument` espone più richiede le API di alto livello che semplificano l'attività di sviluppo di un'applicazione di Xamarin per qualsiasi scopo lo sviluppatore.

Il codice seguente crea una sottoclasse di `UIDocument` per implementare un documento basato su testo generico che può essere utilizzato per archiviare e recuperare testo dagli iCloud:

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

La `GenericTextDocument` classe presentato in precedenza verrà utilizzati in questo articolo quando si utilizza il selettore di documento e i documenti esterni in un'applicazione di xamarin. IOS 8.

## <a name="asynchronous-file-coordination"></a>Coordinamento di File asincrono

iOS 8 offre numerose nuove funzionalità di coordinamento File asincrona tramite le nuove API di coordinamento di File. Prima di iOS 8, tutte le API di coordinamento File esistenti sono stati completamente sincrone. In questo modo che lo sviluppatore è responsabile dell'implementazione i propri background Accodamento messaggi per impedire che il coordinamento di File di blocco dell'interfaccia utente dell'applicazione.

Il nuovo `NSFileAccessIntent` classe contiene un URL che punta al file e diverse opzioni per controllare il tipo di coordinamento necessarie. Il codice seguente viene illustrato come spostare un file da una posizione a un'altra utilizzando i tipi:

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

## <a name="discovering-and-listing-documents"></a>Individuazione e l'elenco di documenti

È il modo per individuare ed elencare i documenti utilizzando esistente `NSMetadataQuery` API. Questa sezione verranno descritte nuove funzionalità aggiunte a `NSMetadataQuery` che semplificano l'uso con i documenti anche rispetto a prima.

### <a name="existing-behavior"></a>Comportamento esistente

Prima di iOS 8, `NSMetadataQuery` è risultata lenta per le modifiche di prelievo file locale, ad esempio: Elimina, crea e si rinomina.

 [![](document-picker-images/image2.png "Panoramica delle modifiche di file locale NSMetadataQuery")](document-picker-images/image2.png#lightbox)

Nel diagramma precedente:

1.  Per i file già esistono nel contenitore dell'applicazione, `NSMetadataQuery` dispone `NSMetadata` record creato in precedenza e lo spooling in modo che siano immediatamente disponibili per l'applicazione.
1.  L'applicazione crea un nuovo file nel contenitore dell'applicazione.
1.  Si verifica un ritardo prima `NSMetadataQuery` vede la modifica al contenitore dell'applicazione e vengono creati il `NSMetadata` record.


A causa il ritardo per la creazione del `NSMetadata` record, l'applicazione deve contenere dati di due origini aprire: uno per le modifiche al file locale e uno per il cloud in base a modifiche.

### <a name="stitching"></a>Operazioni di unione

In iOS 8, `NSMetadataQuery` è più semplice usare direttamente con una nuova funzionalità denominata unione:

 [![](document-picker-images/image3.png "NSMetadataQuery con una nuova funzionalità denominata unione")](document-picker-images/image3.png#lightbox)

Utilizzo di unione nel diagramma precedente:

1.  Come in precedenza, per i file già esistono nel contenitore dell'applicazione, `NSMetadataQuery` dispone `NSMetadata` record creato in precedenza e lo spooling.
1.  L'applicazione crea un nuovo file nel contenitore dell'applicazione tramite il coordinamento di File.
1.  Una funzione hook nel contenitore applicazione rileva la modifica e chiama `NSMetadataQuery` per creare la `NSMetadata` record.
1.  Il `NSMetadata` record viene creato immediatamente dopo il file e viene resa disponibile per l'applicazione.


Unione con l'applicazione non deve aprire un'origine dati per il monitoraggio locale e le modifiche ai file basati su cloud. Dopo l'applicazione può basarsi su `NSMetadataQuery` direttamente.

> [!IMPORTANT]
> Operazioni di unione funziona solo se l'applicazione Usa File coordinamento presentato nella sezione precedente. Se non viene utilizzato il coordinamento di File, il comportamento di iOS 8 pre esistente per impostazione predefinita le API.




### <a name="new-ios-8-metadata-features"></a>Nuove funzionalità di metadati di iOS 8

Le nuove funzionalità seguenti sono stati aggiunti a `NSMetadataQuery` in iOS 8:

-   `NSMetatadataQuery` ora è possibile elencare documenti locali non archiviati nel cloud.
-  Sono state aggiunte nuove API per accedere alle informazioni di metadati nei documenti basati su cloud. 
-  È disponibile un nuovo `NSUrl_PromisedItems` API che consente di accedere gli attributi del file di file che possono o non dispone i contenuti disponibili in locale.
-  Utilizzare il `GetPromisedItemResourceValue` metodo per ottenere informazioni su un determinato file o usare il `GetPromisedItemResourceValues` metodo per ottenere informazioni su più file contemporaneamente.


Due nuovi flag di coordinamento di file sono stati aggiunti per la gestione dei metadati:

-   `NSFileCoordinatorReadImmediatelyAvailableMetadataOnly` 
-   `NSFileCoordinatorWriteContentIndependentMetadataOnly` 


Con il flag descritti in precedenza, il contenuto del file del documento non è necessario essere disponibili localmente per utilizzarli.

Il segmento di codice seguente viene illustrato come utilizzare `NSMetadataQuery` per eseguire una query per l'esistenza di un file specifico e compilare il file se non esiste:

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

Apple ritiene che l'esperienza utente ottimale quando si elencano i documenti per un'applicazione consiste nell'utilizzare le anteprime. In questo modo il contesto, gli utenti finali in modo che consente di identificare il documento che si desidera utilizzare.

Prima di iOS 8, che mostra l'anteprima dei documenti necessaria un'implementazione personalizzata. Nuovi utenti di iOS 8 sono attributi di file system che consentono agli sviluppatori di lavorare rapidamente le anteprime dei documenti.

#### <a name="retrieving-document-thumbnails"></a>Durante il recupero delle anteprime di documenti 

Chiamando il `GetPromisedItemResourceValue` o `GetPromisedItemResourceValues` metodi, `NSUrl_PromisedItems` API, un `NSUrlThumbnailDictionary`, viene restituito. È l'unica chiave attualmente nel dizionario di `NSThumbnial1024X1024SizeKey` e la relativa corrispondenza `UIImage`.

#### <a name="saving-document-thumbnails"></a>Salvare le anteprime dei documenti

Il modo più semplice per salvare un'immagine di anteprima è utilizzando `UIDocument`. Chiamando il `GetFileAttributesToWrite` metodo il `UIDocument` e impostando l'anteprima, verrà automaticamente salvata quando il file di documento. Il Daemon iCloud verrà questa modifica e propagherà su iCloud. In Mac OS X, le anteprime vengono generate automaticamente per gli sviluppatori mediante il plug-in ricerca rapida.

Con le nozioni di base dell'utilizzo di documenti iCloud basato sul posto, con le modifiche all'API esistente, si è pronti per implementare il Controller di visualizzazione documento selezione in un file Xamarin iOS 8 applicazione per dispositivi mobili.


## <a name="enabling-icloud-in-xamarin"></a>Abilitazione iCloud in Xamarin

Prima di poter utilizzare il selettore di documento in un'applicazione di xamarin. IOS, deve essere abilitato nell'applicazione e tramite Apple supporto iCloud. 

I passaggi nella procedura dettagliata seguente il processo di provisioning per iCloud.

1. Creare un contenitore iCloud.
2. Creare un ID di App che contiene il servizio App iCloud.
3. Creare un profilo di Provisioning che include questo ID. App

Il [utilizzo con funzionalità](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) Guida vengono illustrati i primi due passaggi. Per creare un profilo di provisioning, seguire i passaggi di [profilo di Provisioning](~/ios/get-started/installation/device-provisioning/index.md#Provisioning_Profile) Guida.



I passaggi nella procedura dettagliata seguente il processo di configurazione dell'applicazione per iCloud:

Seguire questa procedura:

1.  Aprire il progetto in Visual Studio per Mac o Visual Studio.
2.  Nel **Esplora**, fare clic sul progetto e selezionare le opzioni.
3.  Selezionare la finestra di dialogo Opzioni **applicazione iOS**, assicurarsi che il **identificatore Bundle** corrisponda a quella definita in precedenza **ID App** creato in precedenza per l'applicazione. 
4.  Selezionare **firma Bundle iOS**, selezionare il **identità Developer** e **profilo di Provisioning** creato in precedenza.
5.  Fare clic su di **OK** pulsante per salvare le modifiche e chiudere la finestra di dialogo.
6.  Fare clic su `Entitlements.plist` nel **Esplora** per aprirlo nell'editor.

    > [!IMPORTANT]
    > In Visual Studio potrebbe essere necessario aprire l'editor dei diritti facendo clic su di esso, selezionando **Apri con...** e selezionando Editor dell'elenco delle proprietà

7.  Controllare **abilitare iCloud** , **iCloud documenti** , **archivio chiave-valore** e **CloudKit** .
8.  Verificare che il **contenitore** esiste per l'applicazione (come creato in precedenza). Esempio: `iCloud.com.your-company.AppName`
9.  Salvare le modifiche apportate al file.

Per ulteriori informazioni sui diritti consultare il [funziona con i diritti](~/ios/deploy-test/provisioning/entitlements.md) Guida.

Con l'installazione precedente sul posto, l'applicazione ora possibile usare documenti basati su cloud e il nuovo Controller di visualizzazione di selezione dei documenti.

## <a name="common-setup-code"></a>Codice di programma di installazione comune

Prima di iniziare con il Controller di visualizzazione di selezione di documento, è il codice standard di programma di installazione necessario. Iniziare modificando l'applicazione `AppDelegate.cs` file e renderlo simile al seguente:

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
> Il codice sopra riportato include il codice dalla sezione alla scoperta e visualizzazione di un elenco di documenti. Viene visualizzato di seguito nella sua interezza, come viene visualizzata in un'applicazione effettiva. Per semplicità, in questo esempio funziona con un singolo file hardcoded (`test.txt`) solo.

Il codice sopra riportato espone diverse iCloud unità tasti di scelta rapida per rendere più facile lavorare con il resto dell'applicazione.

Successivamente, aggiungere il codice seguente per qualsiasi vista o di un contenitore di visualizzazione che verrà utilizzando il selettore di documento o gestione di documenti basati su cloud:

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

Aggiunge un collegamento per ottenere il `AppDelegate` e i collegamenti iCloud creati di sopra di accesso.

Con questo codice, esaminiamo un che implementa il Controller di visualizzazione documento selezione in un'applicazione iOS 8 Xamarin.

## <a name="using-the-document-picker-view-controller"></a>Tramite il Controller di visualizzazione di selezione di documento

Prima di iOS 8, è molto difficile accedere ai documenti da un'altra applicazione, perché si è verificato alcun modo per individuare documenti all'esterno dell'applicazione all'interno dell'app.

### <a name="existing-behavior"></a>Comportamento esistente

 [![](document-picker-images/image31.png "Panoramica del comportamento esistente")](document-picker-images/image31.png#lightbox)

Esaminiamo un accesso a un documento esterno prima di iOS 8:

1.  L'utente deve prima aprire l'applicazione di cui è stato creato il documento.
1.  Il documento è selezionato e `UIDocumentInteractionController` viene utilizzato per inviare il documento per la nuova applicazione.
1.  Infine, una copia del documento originale viene inserita nel contenitore della nuova applicazione.


Da qui è disponibile per la seconda applicazione aprire e modificare il documento.

### <a name="discovering-documents-outside-of-an-apps-container"></a>Individuazione dei documenti di fuori contenitore di un'App

In iOS 8, un'applicazione è in grado di accedere a documenti all'esterno di un proprio contenitore dell'applicazione con facilità:

 [![](document-picker-images/image32.png "Individuazione dei documenti di fuori contenitore di un'App")](document-picker-images/image32.png#lightbox)

Con il nuovo iCloud selezione documento ( `UIDocumentPickerViewController`), un'applicazione iOS direttamente può individuare e accedere all'esterno del relativo contenitore dell'applicazione. Il `UIDocumentPickerViewController` fornisce un meccanismo per l'utente concedere l'accesso a e modificare quelli individuati documenti tramite le autorizzazioni.

Un'applicazione deve acconsentire esplicitamente per disporre i documenti visualizzati nel documento selezione iCloud ed essere disponibile per altre applicazioni di individuare e utilizzarli. Per avere un'applicazione di iOS 8 Xamarin relativo contenitore dell'applicazione di condivisione, modificarlo `Info.plist` file in un editor di testo standard e aggiungere le due righe seguenti alla fine del dizionario (tra il `<dict>...</dict>` tag):

```xml
<key>NSUbiquitousContainerIsDocumentScopePublic</key>
<true/>
```

Il `UIDocumentPickerViewController` fornisce un'ottima nuova interfaccia utente che consente all'utente di scegliere i documenti. Per visualizzare il documento selezione View Controller in un'applicazione iOS 8 Xamarin, eseguire le operazioni seguenti:

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
> Lo sviluppatore deve chiamare il `StartAccessingSecurityScopedResource` metodo il `NSUrl` prima di un documento esterno accessibile. Il `StopAccessingSecurityScopedResource` metodo deve essere chiamato per rilasciare il blocco di sicurezza, non appena il documento è stato caricato.

### <a name="sample-output"></a>Esempio di output

Di seguito è riportato un esempio di come il codice precedente verrà visualizzato un documento di selezione quando viene eseguito da un dispositivo iPhone:

1.  L'utente avvia l'applicazione e viene visualizzata l'interfaccia principale:   
 
    [![](document-picker-images/image33.png "Viene visualizzata l'interfaccia principale")](document-picker-images/image33.png#lightbox)
1.  Le scelte dell'utente il **azione** pulsante nella parte superiore dello schermo e viene chiesto di selezionare un **documento Provider** dall'elenco dei provider disponibili:   
 
    [![](document-picker-images/image34.png "Selezionare un Provider di documenti dall'elenco dei provider disponibili")](document-picker-images/image34.png#lightbox)
1.  Il **documento selezione View Controller** viene visualizzato per il nodo **documento Provider**:   
 
    [![](document-picker-images/image35.png "Viene visualizzato il documento selezione View Controller")](document-picker-images/image35.png#lightbox)
1.  L'utente tocca su un **cartella documenti** per visualizzarne il contenuto:   
 
    [![](document-picker-images/image36.png "Il contenuto della cartella di documento")](document-picker-images/image36.png#lightbox)
1.  L'utente seleziona un **documento** e **selezione documento** viene chiuso.
1.  Verrà nuovamente visualizzata l'interfaccia principale, il **documento** viene caricato dal contenitore esterno e il relativo contenuto visualizzato.


La visualizzazione del documento selezione visualizzazione Controller effettiva varia a seconda che l'utente ha installato nel dispositivo e la modalità di selezione del documento è stato implementare i provider di documento. L'esempio precedente Usa la modalità di apertura, gli altri tipi di modalità verranno descritta in dettaglio di seguito.

## <a name="managing-external-documents"></a>Gestione dei documenti esterni

Come illustrato in precedenza, prima di iOS 8, un'applicazione può accedere solo ai documenti che facevano una parte del relativo contenitore dell'applicazione. In iOS 8 un'applicazione può accedere ai documenti da origini esterne:

 [![](document-picker-images/image37.png "Panoramica di gestione dei documenti esterni")](document-picker-images/image37.png#lightbox)

Quando l'utente seleziona un documento da un'origine esterna, viene scritto un documento di riferimento per il contenitore dell'applicazione che punta al documento originale.

Per semplificare l'aggiunta di questa nuova capacità nelle applicazioni esistenti, numerose nuove funzionalità sono stati aggiunti i `NSMetadataQuery` API. In genere, un'applicazione utilizza l'ambito universale di documento per elencare i documenti che risiedono all'interno del contenitore dell'applicazione. Utilizzo di questo ambito, solo i documenti all'interno del contenitore di applicazione continueranno a essere visualizzati.

Utilizzando il nuovo ambito documento esterno universale restituirà i documenti che risiedono all'esterno dell'applicazione contenitore e restituiscono i metadati per loro. Il `NSMetadataItemUrlKey` punterà all'URL in cui si trova effettivamente il documento.

Un'applicazione non desidera di lavorare con i documenti a cui fa riferimento per riferimento th. Al contrario, l'app desidera lavorare direttamente con il documento di riferimento. Ad esempio, l'applicazione potrebbe essere per visualizzare il documento nella cartella dell'applicazione nell'interfaccia utente, o per consentire all'utente di spostare i riferimenti all'interno di una cartella.

In iOS 8, un nuovo `NSMetadataItemUrlInLocalContainerKey` è stato fornito per accedere direttamente al documento di riferimento. Questa chiave fa riferimento al riferimento effettivo per il documento in un contenitore di applicazione esterno.

Il `NSMetadataUbiquitousItemIsExternalDocumentKey` viene utilizzato per verificare se un documento è esterno a contenitore un'applicazione. Il `NSMetadataUbiquitousItemContainerDisplayNameKey` viene utilizzato per accedere al nome del contenitore che è che ospita la copia originale di un documento esterno.

### <a name="why-document-references-are-required"></a>Motivo per cui sono necessari i riferimenti del documento

Il motivo principale che iOS 8 Usa i riferimenti per accedere a documenti esterni è sicurezza. Nessuna applicazione di accedere a contenitore qualsiasi altra applicazione. Solo la selezione del documento può farlo, perché è in esecuzione out-of-process e ha accesso all'intero sistema.

È l'unico modo per ottenere un documento all'esterno del contenitore dell'applicazione utilizzando il selettore di documento e se l'URL restituito per la selezione è con ambito di protezione. L'URL di un ambito di sicurezza contiene informazioni sufficienti per il documento con ambiti diritti necessari per concedere un'applicazione l'accesso al documento selezionato.

È importante notare che se l'URL di un ambito di protezione è stato serializzato in una stringa e quindi deserializzato, le informazioni di sicurezza potrebbero essere perse e il file sarebbe accessibile dall'URL. La funzionalità di riferimento del documento fornisce un meccanismo per ottenere i file a cui fa riferimento a questi URL.

Pertanto, se l'applicazione acquisisce un `NSUrl` da uno dei documenti di riferimento, dispone già dell'ambito di protezione associato e può essere utilizzato per accedere al file. Per questo motivo, è altamente consigliabile che lo sviluppatore utilizzare `UIDocument` perché tutte queste informazioni e i processi gestisce per loro.

### <a name="using-bookmarks"></a>Utilizzo dei bookmark

Non è sempre possibile enumerare i documenti di un'applicazione per tornare a un documento specifico, ad esempio, quando si esegue il ripristino dello stato. iOS 8 fornisce un meccanismo per creare segnalibri che fanno direttamente riferimento a un documento specifico.

Nel codice seguente verrà creato un segnalibro da un `UIDocument`del `FileUrl` proprietà:

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

L'API di segnalibro esistente viene utilizzato per creare un segnalibro con un oggetto esistente `NSUrl` che possono essere salvati e caricati per fornire accesso diretto a un file esterno. Il codice seguente viene eseguito un segnalibro a cui è stato creato in precedenza:

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

## <a name="open-vs-import-mode-and-the-document-picker"></a>Aprire Visual Studio. Modalità di importazione e la selezione del documento

Il Controller di visualizzazione di selezione di documento sono disponibili due modalità diverse di funzionamento:

1.  **Aprire modalità** : In questa modalità, quando l'utente seleziona e un documento esterno, la selezione del documento verrà creato un segnalibro con ambito di sicurezza nel contenitore dell'applicazione.   
 
    [![](document-picker-images/image37.png "Una protezione con ambito di segnalibro nel contenitore dell'applicazione")](document-picker-images/image37.png#lightbox)
1.  **Modalità importazione** : In questa modalità, quando l'utente seleziona e documento esterno, la selezione del documento verrà non creare un segnalibro, ma al contrario, copiare il file in un percorso temporaneo e fornire l'accesso all'applicazione al documento in questa posizione:   
 
    [![](document-picker-images/image38.png "La selezione del documento verranno copiare il file in un percorso temporaneo e fornire l'accesso all'applicazione al documento in questa posizione")](document-picker-images/image38.png#lightbox)   
 Dopo la chiusura dell'applicazione per qualsiasi motivo, il percorso temporaneo viene svuotato e rimuovere il file. Se l'applicazione deve mantenere l'accesso al file, dovrebbe eseguire una copia e posizionarlo nel relativo contenitore dell'applicazione.


La modalità di apertura è utile quando l'applicazione desidera collaborare con un'altra applicazione e condividere le modifiche apportate al documento con tale applicazione. La modalità di importazione viene utilizzata quando l'applicazione non desidera condividere le modifiche a un documento con altre applicazioni.

## <a name="making-a-document-external"></a>Rendere un documento esterno

Come indicato in precedenza, un'applicazione iOS 8 non ha accesso ai contenitori all'esterno di un proprio contenitore dell'applicazione. L'applicazione è possibile scrivere il proprio contenitore localmente o in un percorso temporaneo, quindi utilizzare una modalità speciale di documento per spostare il documento risultante di fuori del contenitore di applicazione a un utente del percorso scelto.

Per spostare un documento in una posizione esterna, eseguire le operazioni seguenti:

1.  Creare innanzitutto un nuovo documento in un percorso locale o temporaneo.
1.  Creare un `NSUrl` che punti al nuovo documento.
1.  Aprire un nuovo Controller di visualizzazione selezione documento e passare il `NSUrl` con la modalità di `MoveToService` . 
1.  Una volta che l'utente sceglie un nuovo percorso, il documento verrà spostato dalla posizione corrente alla nuova posizione.
1.  Verrà scritto un documento di riferimento al contenitore di applicazione dell'app in modo che il file può comunque accedere dall'applicazione di creazione.


Il codice seguente può essere utilizzato per spostare un documento in una posizione esterna: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.MoveToService);`

Il documento di riferimento restituito dal processo precedente è esattamente uguale a uno creato da una modalità di apertura del documento selettore. Tuttavia, esistono volte in cui l'applicazione potrebbe essere opportuno spostare un documento senza mantenere un riferimento a esso.

Per spostare un documento senza generare un riferimento, utilizzare il `ExportToService` modalità. Esempio: `var picker = new UIDocumentPickerViewController (srcURL, UIDocumentPickerMode.ExportToService);`

Quando si utilizza il `ExportToService` modalità, il documento viene copiato nel contenitore esterno e la copia esistente viene lasciata nel percorso originale.

## <a name="document-provider-extensions"></a>Estensioni di Provider di documento

Con iOS 8, Apple richiede all'utente finale in grado di accedere a uno dei propri documenti basati su cloud, indipendentemente da dove vengono effettivamente esistenti. Per raggiungere questo obiettivo, iOS 8 fornisce un meccanismo di estensione del Provider documento nuovo.

### <a name="what-is-a-document-provider-extension"></a>Che cos'è un'estensione di Provider documento?

Per un'estensione di Provider di documento è un modo per uno sviluppatore o un di terze parti, per fornire un documento alternativo nell'archiviazione di applicazioni che è possibile accedere in esattamente così come il percorso di archiviazione iCloud esistente.

L'utente può selezionare una di queste posizioni di archiviazione alternativo da Selezione tipo di documento e possono utilizzare esatta stessa modalità di accesso (Open, importazione, spostare o esportazione) per utilizzare i file in tale percorso.

Ciò viene implementato utilizzando due diverse estensioni:

-  **Selezione estensione di documenti** : fornisce un `UIViewController` sottoclasse che offre un'interfaccia grafica per l'utente di scegliere un documento da un percorso di archiviazione alternativo. Questo tipo di sottoclasse verrà visualizzato come parte del documento selezione visualizzazione Controller.
-  **Fornire estensione di file** : si tratta di un'estensione non dell'interfaccia utente che riguarda effettivamente fornendo il contenuto del file. Queste estensioni vengono fornite tramite i File di coordinamento ( `NSFileCoordinator` ). Si tratta di un'altra importante caso in cui il coordinamento di File è obbligatorio.


Nel diagramma seguente viene illustrato il flusso di dati in genere quando si utilizzano le estensioni del Provider di documento:

 [![](document-picker-images/image39.png "Questo diagramma mostra il flusso di dati in genere quando si utilizzano le estensioni del Provider di documento")](document-picker-images/image39.png#lightbox)

Si verifica quanto segue:

1.  L'applicazione presenta un Controller di selezione di documento per consentire all'utente di selezionare un file da utilizzare.
1.  L'utente seleziona un percorso alternativo e personalizzata `UIViewController` estensione viene chiamata per visualizzare l'interfaccia utente.
1.  L'utente seleziona un file da questo percorso e l'URL viene passato nuovamente al selettore di documento.
1.  La selezione del documento Seleziona URL del file e lo restituisce all'applicazione per l'utente sta lavorando.
1.  L'URL viene passato al File coordinatore per restituire il contenuto di file all'applicazione.
1.  Il coordinatore File chiama l'estensione del Provider di File personalizzati per recuperare il file.
1.  Il contenuto del file viene restituito al coordinatore File.
1.  Il contenuto del file viene restituito all'applicazione.


### <a name="security-and-bookmarks"></a>Sicurezza e i segnalibri

In questa sezione verrà esaminato un rapido come accesso al file persistente e sicurezza tramite il funzionamento di segnalibri con le estensioni di Provider di documento. A differenza dei Provider di documento, che salva automaticamente i segnalibri e sicurezza per il contenitore dell'applicazione, iCloud documento Provider di estensioni non perché non sono una parte del sistema di riferimento documento.

Ad esempio: in uno scenario aziendale che fornisce il proprio archivio di dati sicura a livello aziendale, gli amministratori non desiderano informazioni aziendale riservate accedere o elaborati dal server pubblica iCloud. Il sistema di riferimento documento predefinito non può pertanto essere utilizzato.

Il sistema di segnalibro può comunque essere utilizzato e spetta il Provider di estensione del File per elaborare correttamente un URL con segnalibro e restituire il contenuto del documento a cui fa riferimento è.

Per motivi di sicurezza, iOS 8 ha un livello di isolamento che mantiene le informazioni sui cui ha accesso alle quali identificatore quale Provider di File. Si noti che tutti gli accessi di file sono controllato da questo livello di isolamento.

Nel diagramma seguente viene illustrato il flusso di dati quando si lavora con i segnalibri e l'estensione Provider documento:

 [![](document-picker-images/image40.png "Questo diagramma mostra il flusso di dati quando si lavora con i segnalibri e l'estensione Provider documento")](document-picker-images/image40.png#lightbox)

Si verifica quanto segue:

1.  L'applicazione sta per entrare lo sfondo e deve rendere persistente lo stato. Chiama `NSUrl` per creare un segnalibro in un file nel servizio di archiviazione alternativo.
1.  `NSUrl` chiama l'estensione di File del Provider per ottenere un URL permanente nel documento. 
1.  L'estensione di File del Provider restituisce l'URL come una stringa di `NSUrl` .
1.  Il `NSUrl` aggrega l'URL in un segnalibro e lo restituisce all'applicazione.
1.  Quando l'applicazione si ripristina dalla fase in background ed è necessario ripristinare lo stato, passa il segnalibro `NSUrl` .
1.  `NSUrl` chiama l'estensione del Provider di File con l'URL del file.
1.  Il Provider dell'estensione File accede al file e restituisce il percorso del file da `NSUrl` .
1.  Il percorso del file è in bundle con informazioni di sicurezza e restituito all'applicazione.


A questo punto, l'applicazione può accedere al file e utilizzarlo come di consueto.

### <a name="writing-files"></a>Scrittura di file

In questa sezione verrà esaminato un rapido modalità scrittura in file in un percorso alternativo con l'estensione del Provider di documento può essere utilizzata. L'applicazione iOS utilizzerà il coordinamento di File per salvare le informazioni su disco all'interno del contenitore dell'applicazione. Subito dopo il file è stata scritta correttamente, il Provider di estensione riceverà la notifica della modifica.

A questo punto, l'estensione del Provider di File possibile avviare il caricamento del file in un percorso alternativo (o contrassegnare il file come dirty e la richiesta di caricamento).

### <a name="creating-new-document-provider-extensions"></a>Creazione di nuove estensioni di Provider di documento

Creazione di nuove estensioni di Provider di documento è esterno all'ambito di questo articolo introduttivo. Queste informazioni sono fornite di seguito per dimostrare che, in base alle estensioni di che un utente non è caricato nel proprio dispositivo iOS, un'applicazione potrebbe avere accesso alle posizioni di archiviazione di documenti all'esterno di Apple fornite percorso iCloud.

Lo sviluppatore è necessario essere consapevole del fatto quando si utilizza il selettore di documento e l'utilizzo di documenti esterni. Essi non devono presupporre che questi documenti vengono ospitati in iCloud.

Per ulteriori informazioni sulla creazione di un'estensione di selezione di documento o un Provider di archiviazione, vedere il [Introduzione alle estensioni App](~/ios/platform/extensions.md) documento.

## <a name="migrating-to-icloud-drive"></a>La migrazione in iCloud unità

In iOS 8, gli utenti è possono scegliere di continuare utilizzando iCloud esistente documenti sistema utilizzato nelle iOS 7 e nei sistemi precedenti, o possono scegliere di eseguire la migrazione di documenti esistenti per il nuovo meccanismo di unità iCloud.

In Mac OS X Yosemite, Apple non fornisce il con le versioni precedenti compatibilità pertanto necessario eseguire la migrazione di tutti i documenti in iCloud unità o non sarà più possibile aggiornare tra i dispositivi.

Dopo che un account utente è stato migrato in iCloud unità, solo i dispositivi con iCloud unità sarà in grado di propagare le modifiche ai documenti su tali dispositivi.

> [!IMPORTANT]
> Gli sviluppatori devono tenere presente che le nuove funzionalità illustrate in questo articolo sono disponibili solo se l'account dell'utente è stato migrato in iCloud unità. 

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati le modifiche in iCloud esistente, le API necessarie per supportare iCloud unità e il nuovo Controller di visualizzazione di selezione dei documenti. Che è trattato il coordinamento di File e perché è importante quando si lavora con i documenti basati su cloud. Dispone di cui il programma di installazione necessario per attivare i documenti basati su cloud in un'applicazione di xamarin e ha un aspetto introduttivo all'utilizzo di documenti all'esterno applicazione contenitore di un'app usando il Controller di visualizzazione documento selezione.

In questo articolo, inoltre, illustrate brevemente estensioni di Provider di documento e perché lo sviluppatore deve essere conoscenza della loro quando si scrivono applicazioni in grado di gestire documenti basati su cloud.

## <a name="related-links"></a>Collegamenti correlati

- [DocPicker (esempio)](https://developer.xamarin.com/samples/monotouch/ios8/DocPicker/)
- [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Introduzione alle estensioni dell'App](~/ios/platform/extensions.md)
