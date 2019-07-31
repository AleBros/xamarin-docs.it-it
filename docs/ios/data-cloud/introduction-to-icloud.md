---
title: Uso di iCloud con Novell. iOS
description: Questo documento descrive iCloud e il relativo uso nelle applicazioni Novell. iOS. Vengono illustrati archiviazione chiave-valore, archiviazione documenti e backup iCloud.
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/09/2016
ms.openlocfilehash: e4728d14e4fdb914fbfc950ffe38abee031b4c49
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649648"
---
# <a name="using-icloud-with-xamarinios"></a>Uso di iCloud con Novell. iOS

L'API di archiviazione iCloud in iOS 5 consente alle applicazioni di salvare i documenti utente e i dati specifici dell'applicazione in una posizione centrale e di accedere a tali elementi da tutti i dispositivi dell'utente.

Sono disponibili quattro tipi di archiviazione:

- **Archiviazione chiave-valore** : per condividere piccole quantità di dati con l'applicazione negli altri dispositivi di un utente.

- **Archiviazione di UIDocument** : per archiviare documenti e altri dati nell'account iCloud dell'utente usando una sottoclasse di UIDocument.

- **CoreData** -archiviazione database SQLite.

- **Singoli file e directory** : per gestire molti file diversi direttamente nel file System.

Questo documento illustra i primi due tipi, ovvero coppie chiave-valore e sottoclassi UIDocument, e come usare tali funzionalità in Novell. iOS.

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

## <a name="requirements"></a>Requisiti

- La versione stabile più recente di Novell. iOS
- Xcode 10
- Visual Studio per Mac o Visual Studio 2019.

## <a name="preparing-for-icloud-development"></a>Preparazione per lo sviluppo iCloud

Le applicazioni devono essere configurate per usare iCloud sia nel portale di provisioning [Apple](https://developer.apple.com/account/ios/overview.action) che nel progetto stesso. Prima di sviluppare per iCloud (o provare gli esempi), seguire questa procedura.

Per configurare correttamente un'applicazione per accedere a iCloud:

-   **Trovare il TeamID** di accesso a [Developer.Apple.com](https://developer.apple.com) e visitare il **centro per i membri > il proprio account > Riepilogo account sviluppatore** per ottenere l'ID del team (o ID singolo per gli sviluppatori singoli). Si tratta di una stringa di 10 caratteri (ad esempio, **A93A5CM278** ), che fa parte dell'"identificatore del contenitore".

-   **Creare un nuovo ID app** : per creare un ID app, seguire i passaggi descritti nella [sezione provisioning for Store Technologies della Guida](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)al provisioning dei dispositivi e assicurarsi di controllare **iCloud** come servizio consentito:

 [![](introduction-to-icloud-images/icloud-sml.png "Controllare iCloud come servizio consentito")](introduction-to-icloud-images/icloud.png#lightbox)

- **Creare un nuovo profilo** di provisioning: per creare un profilo di provisioning, seguire i passaggi descritti nella Guida al provisioning dei [dispositivi](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) .

- **Aggiungere l'identificatore del contenitore a titles. plist.** il formato dell'identificatore del `TeamID.BundleID`contenitore è. Per ulteriori informazioni, vedere la guida sull' [utilizzo dei diritti](~/ios/deploy-test/provisioning/entitlements.md) .

- **Configurare le proprietà del progetto** : nel file INFO. plist verificare che l' **identificatore del bundle** corrisponda all' **ID bundle** impostato durante la [creazione di un ID app](~/ios/deploy-test/provisioning/capabilities/index.md). La firma del bundle iOS usa un **profilo** di provisioning che contiene un ID app con il servizio app iCloud e il file dei **diritti personalizzati** selezionato. Questa operazione può essere eseguita in Visual Studio nel riquadro Proprietà progetto.

- **Abilitare iCloud sul dispositivo** : passare a **Impostazioni > iCloud** e assicurarsi che il dispositivo sia connesso.
Selezionare e attivare l'opzione **documents & data** .

- È **necessario usare un dispositivo per testare iCloud** . non funzionerà sul simulatore.
Infatti, sono necessari due o più dispositivi che hanno eseguito l'accesso con lo stesso ID Apple per vedere iCloud in azione.


## <a name="key-value-storage"></a>Archiviazione chiave-valore

L'archiviazione chiave-valore è destinata a piccole quantità di dati che un utente potrebbe avere come reso permanente tra i dispositivi, ad esempio l'ultima pagina visualizzata in un libro o una rivista. L'archiviazione chiave-valore non deve essere utilizzata per il backup dei dati.

Quando si usa l'archiviazione chiave-valore, è necessario tenere presenti alcune limitazioni:

- **Dimensioni massime chiave** : i nomi di chiave non possono essere più lunghi di 64 byte.

- **Dimensione massima del valore** : non è possibile archiviare più di 64 kilobyte in un singolo valore.

- **Dimensioni massime dell'archivio valore chiave per un'app** -le applicazioni possono archiviare in totale fino a 64 kilobyte di dati chiave-valore. I tentativi di impostare le chiavi oltre tale limite avranno esito negativo e il valore precedente verrà mantenuto.

- **Tipi di dati** : è possibile archiviare solo tipi di base come stringhe, numeri e valori booleani.

L'esempio **iCloudKeyValue** ne illustra il funzionamento. Il codice di esempio crea una chiave denominata per ogni dispositivo: è possibile impostare questa chiave su un dispositivo e osservare che il valore viene propagato ad altri utenti. Crea anche una chiave denominata "Shared" che può essere modificata in qualsiasi dispositivo. Se si modifica in molti dispositivi contemporaneamente, iCloud deciderà quale valore "WINS" (usando un timestamp per la modifica) e verrà propagato.

Questa schermata mostra l'esempio in uso. Quando le notifiche di modifica vengono ricevute da iCloud, vengono stampate nella visualizzazione di testo a scorrimento nella parte inferiore della schermata e aggiornate nei campi di input.



 [![](introduction-to-icloud-images/icloud-kv-arrows.png "Flusso dei messaggi tra dispositivi")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>Impostazione e recupero di dati

Questo codice Mostra come impostare un valore stringa.

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

La chiamata di Synchronize garantisce che il valore venga salvato in modo permanente solo nell'archiviazione su disco locale. La sincronizzazione in iCloud viene eseguita in background e non può essere "forzata" dal codice dell'applicazione. Con una buona connettività di rete, la sincronizzazione si verificherà spesso entro 5 secondi. Tuttavia, se la rete è scadente (o disconnessa), un aggiornamento potrebbe richiedere molto più tempo.

È possibile recuperare un valore con il codice seguente:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

Il valore viene recuperato dall'archivio dati locale. questo metodo non tenta di contattare i server iCloud per ottenere il valore "più recente". iCloud aggiornerà l'archivio dati locale in base alla propria pianificazione.

### <a name="deleting-data"></a>Eliminazione di dati

Per rimuovere completamente una coppia chiave-valore, usare il metodo Remove come indicato di seguito:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>Osservazione delle modifiche

Un'applicazione può anche ricevere notifiche quando i valori vengono modificati da iCloud aggiungendo un Observer a `NSNotificationCenter.DefaultCenter`.
Il codice seguente dal metodo **KeyValueViewController.cs** `ViewWillAppear` Mostra come restare in ascolto delle notifiche e creare un elenco delle chiavi modificate:

```csharp
keyValueNotification =
NSNotificationCenter.DefaultCenter.AddObserver (
    NSUbiquitousKeyValueStore.DidChangeExternallyNotification, notification => {
    Console.WriteLine ("Cloud notification received");
    NSDictionary userInfo = notification.UserInfo;

    var reasonNumber = (NSNumber)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangeReasonKey);
    nint reason = reasonNumber.NIntValue;

    var changedKeys = (NSArray)userInfo.ObjectForKey (NSUbiquitousKeyValueStore.ChangedKeysKey);
    var changedKeysList = new List<string> ();
    for (uint i = 0; i < changedKeys.Count; i++) {
        var key = changedKeys.GetItem<NSString> (i); // resolve key to a string
        changedKeysList.Add (key);
    }
    // now do something with the list...
});
```

Il codice può quindi eseguire un'azione con l'elenco delle chiavi modificate, ad esempio l'aggiornamento di una copia locale o l'aggiornamento dell'interfaccia utente con i nuovi valori.

I motivi possibili per le modifiche sono: ServerChange (0), InitialSyncChange (1) o QuotaViolationChange (2). Se necessario, è possibile accedere al motivo ed eseguire un'elaborazione diversa (ad esempio, potrebbe essere necessario rimuovere alcune chiavi come risultato di un *QuotaViolationChange*).

## <a name="document-storage"></a>Archiviazione documenti

l'archiviazione di documenti iCloud è progettata per gestire i dati importanti per l'app e per l'utente. Può essere usato per gestire i file e altri dati che l'app deve eseguire, fornendo allo stesso tempo funzionalità di backup e condivisione basate su iCloud in tutti i dispositivi dell'utente.

Questo diagramma mostra il modo in cui si integra. Ogni dispositivo ha i dati salvati nell'archiviazione locale (UbiquityContainer) e il daemon iCloud del sistema operativo si occupa dell'invio e della ricezione di dati nel cloud. Per impedire l'accesso simultaneo, è necessario che l'accesso ai file di UbiquityContainer sia eseguito tramite filepresentator/Coordinator. La `UIDocument` classe implementa tali utenti. in questo esempio viene illustrato come utilizzare UIDocument.

 [![](introduction-to-icloud-images/icloud-overview.png "Panoramica dell'archiviazione dei documenti")](introduction-to-icloud-images/icloud-overview.png#lightbox)

L'esempio iCloudUIDoc implementa una semplice `UIDocument` sottoclasse che contiene un solo campo di testo. Il rendering del testo viene eseguito `UITextView` in un e le modifiche vengono propagate da iCloud ad altri dispositivi con un messaggio di notifica visualizzato in rosso. Il codice di esempio non riguarda le funzionalità iCloud più avanzate, ad esempio la risoluzione dei conflitti.

Questa schermata mostra l'applicazione di esempio: dopo aver modificato il testo e aver premuto **UpdateChangeCount** , il documento viene sincronizzato tramite iCloud con altri dispositivi.

 [![](introduction-to-icloud-images/iclouduidoc.png "Questa schermata mostra l'applicazione di esempio dopo aver modificato il testo e aver premuto UpdateChangeCount")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

Sono disponibili cinque parti per l'esempio iCloudUIDoc:

1. **Accesso a UbiquityContainer** : determinare se iCloud è abilitato e, in tal caso, il percorso dell'area di archiviazione iCloud dell'applicazione.

1. **Creazione di una sottoclasse UIDocument** : creare una classe a intermedia tra l'archivio iCloud e gli oggetti modello.

1. **Ricerca e apertura di documenti iCloud** : `NSFileManager` usare `NSPredicate` e per trovare i documenti iCloud e aprirli.

1. **Visualizzazione di documenti iCloud** : esporre le proprietà `UIDocument` da in modo che sia possibile interagire con i controlli dell'interfaccia utente.

1. **Salvataggio dei documenti iCloud** : assicurarsi che le modifiche apportate nell'interfaccia utente siano salvate in modo permanente su disco e iCloud.

Tutte le operazioni iCloud vengono eseguite (o devono essere eseguite) in modo asincrono in modo che non si blocchino durante l'attesa di un evento. Nell'esempio si vedranno tre diversi modi per eseguire questa operazione:

 **Thread** : nella `AppDelegate.FinishedLaunching` chiamata iniziale a `GetUrlForUbiquityContainer` viene eseguita su un altro thread per impedire il blocco del thread principale.

 **NotificationCenter** : registrazione per le notifiche in caso di operazioni asincrone `NSMetadataQuery.StartQuery` come complete.

 **Gestori di completamento** : passaggio di metodi da eseguire al completamento di operazioni asincrone come `UIDocument.Open`.

### <a name="accessing-the-ubiquitycontainer"></a>Accesso a UbiquityContainer

Il primo passaggio nell'uso dell'archiviazione dei documenti iCloud consiste nel determinare se iCloud è abilitato e, in tal caso, la posizione del "contenitore di ubiquità", ovvero la directory in cui vengono archiviati i file abilitati per iCloud nel dispositivo.

Questo codice è nel `AppDelegate.FinishedLaunching` metodo dell'esempio.

```csharp
// GetUrlForUbiquityContainer is blocking, Apple recommends background thread or your UI will freeze
ThreadPool.QueueUserWorkItem (_ => {
    CheckingForiCloud = true;
    Console.WriteLine ("Checking for iCloud");
    var uburl = NSFileManager.DefaultManager.GetUrlForUbiquityContainer (null);
    // OR instead of null you can specify "TEAMID.com.your-company.ApplicationName"

    if (uburl == null) {
        HasiCloud = false;
        Console.WriteLine ("Can't find iCloud container, check your provisioning profile and entitlements");

        InvokeOnMainThread (() => {
            var alertController = UIAlertController.Create ("No \uE049 available",
            "Check your Entitlements.plist, BundleId, TeamId and Provisioning Profile!", UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Destructive, null));
            viewController.PresentViewController (alertController, false, null);
        });
    } else { // iCloud enabled, store the NSURL for later use
        HasiCloud = true;
        iCloudUrl = uburl;
        Console.WriteLine ("yyy Yes iCloud! {0}", uburl.AbsoluteUrl);
    }
    CheckingForiCloud = false;
});
```

Sebbene l'esempio non esegua questa operazione, Apple consiglia di chiamare GetUrlForUbiquityContainer ogni volta che un'app viene visualizzata in primo piano.

### <a name="creating-a-uidocument-subclass"></a>Creazione di una sottoclasse UIDocument

Tutti i file e le directory iCloud, ovvero qualsiasi elemento archiviato nella directory UbiquityContainer, devono essere gestiti usando metodi NSFileManager, implementando il protocollo NSFilePresenter e scrivendo tramite un NSFileCoordinator.
Il modo più semplice per eseguire tutte queste operazioni è quello di non scriverlo autonomamente, ma sottoclasse UIDocument che esegue tutte le operazioni.

Esistono solo due metodi che è necessario implementare in una sottoclasse UIDocument per lavorare con iCloud:

- **LoadFromContents** : passa il NSData del contenuto del file per decomprimere la classe del modello/es.

- **ContentsForType** : richiede di fornire la rappresentazione NSData della classe del modello/ES per il salvataggio su disco (e il cloud).

Questo codice di esempio di **iCloudUIDoc\MonkeyDocument.cs** illustra come implementare UIDocument.

```csharp
public class MonkeyDocument : UIDocument
{
    // the 'model', just a chunk of text in this case; must easily convert to NSData
    NSString dataModel;
    // model is wrapped in a nice .NET-friendly property
    public string DocumentString {
        get {
            return dataModel.ToString ();
        }
        set {
            dataModel = new NSString (value);
        }
    }
    public MonkeyDocument (NSUrl url) : base (url)
    {
        DocumentString = "(default text)";
    }
    // contents supplied by iCloud to display, update local model and display (via notification)
    public override bool LoadFromContents (NSObject contents, string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("LoadFromContents({0})", typeName);

        if (contents != null)
            dataModel = NSString.FromData ((NSData)contents, NSStringEncoding.UTF8);

        // LoadFromContents called when an update occurs
        NSNotificationCenter.DefaultCenter.PostNotificationName ("monkeyDocumentModified", this);
        return true;
    }
    // return contents for iCloud to save (from the local model)
    public override NSObject ContentsForType (string typeName, out NSError outError)
    {
        outError = null;

        Console.WriteLine ("ContentsForType({0})", typeName);
        Console.WriteLine ("DocumentText:{0}",dataModel);

        NSData docData = dataModel.Encode (NSStringEncoding.UTF8);
        return docData;
    }
}
```

Il modello di dati in questo caso è molto semplice, ovvero un solo campo di testo. Il modello di dati può essere complesso come richiesto, ad esempio un documento XML o dati binari. Il ruolo principale dell'implementazione di UIDocument consiste nel tradurre tra le classi del modello e una rappresentazione NSData che può essere salvata/caricata su disco.

### <a name="finding-and-opening-icloud-documents"></a>Ricerca e apertura di documenti iCloud

L'app di esempio gestisce solo un file test. txt, quindi il codice in **AppDelegate.cs** crea un oggetto `NSPredicate` e `NSMetadataQuery` lo cerca in modo specifico per tale nome file. Il `NSMetadataQuery` viene eseguito in modo asincrono e invia una notifica al termine dell'operazione. `DidFinishGathering`viene chiamato dall'osservatore delle notifiche, arresta la query e chiama LoadDocument, che usa il `UIDocument.Open` metodo con un gestore di completamento per tentare di caricare il file e visualizzarlo in `MonkeyDocumentViewController`un oggetto.

```csharp
string monkeyDocFilename = "test.txt";
void FindDocument ()
{
    Console.WriteLine ("FindDocument");
    query = new NSMetadataQuery {
        SearchScopes = new NSObject [] { NSMetadataQuery.UbiquitousDocumentsScope }
    };

    var pred = NSPredicate.FromFormat ("%K == %@", new NSObject[] {
        NSMetadataQuery.ItemFSNameKey, new NSString (MonkeyDocFilename)
    });

    Console.WriteLine ("Predicate:{0}", pred.PredicateFormat);
    query.Predicate = pred;

    NSNotificationCenter.DefaultCenter.AddObserver (
        this,
        new Selector ("queryDidFinishGathering:"),
        NSMetadataQuery.DidFinishGatheringNotification,
        query
    );

    query.StartQuery ();
}

[Export ("queryDidFinishGathering:")]
void DidFinishGathering (NSNotification notification)
{
    Console.WriteLine ("DidFinishGathering");
    var metadataQuery = (NSMetadataQuery)notification.Object;
    metadataQuery.DisableUpdates ();
    metadataQuery.StopQuery ();

    NSNotificationCenter.DefaultCenter.RemoveObserver (this, NSMetadataQuery.DidFinishGatheringNotification, metadataQuery);
    LoadDocument (metadataQuery);
}

void LoadDocument (NSMetadataQuery metadataQuery)
{
    Console.WriteLine ("LoadDocument");

    if (metadataQuery.ResultCount == 1) {
        var item = (NSMetadataItem)metadataQuery.ResultAtIndex (0);
        var url = (NSUrl)item.ValueForAttribute (NSMetadataQuery.ItemURLKey);
        doc = new MonkeyDocument (url);

        doc.Open (success => {
            if (success) {
                Console.WriteLine ("iCloud document opened");
                Console.WriteLine (" -- {0}", doc.DocumentString);
                viewController.DisplayDocument (doc);
            } else {
                Console.WriteLine ("failed to open iCloud document");
            }
        });
    } // TODO: if no document, we need to create one
}
```

### <a name="displaying-icloud-documents"></a>Visualizzazione di documenti iCloud

La visualizzazione di un UIDocument non deve essere diversa da qualsiasi altra classe del modello
- le proprietà vengono visualizzate nei controlli dell'interfaccia utente, eventualmente modificati dall'utente e quindi riscritti nel modello.

Nell'esempio **iCloudUIDoc\MonkeyDocumentViewController.cs** Visualizza il testo MonkeyDocument in un oggetto `UITextView`. `ViewDidLoad`Ascolta la notifica inviata nel `MonkeyDocument.LoadFromContents` metodo. `LoadFromContents`viene chiamato quando iCloud dispone di nuovi dati per il file, in modo che la notifica indichi che il documento è stato aggiornato.

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

Il gestore di notifiche del codice di esempio chiama un metodo per aggiornare l'interfaccia utente, in questo caso senza alcuna risoluzione o rilevamento dei conflitti.

```csharp
[Export ("dataReloaded:")]
void DataReloaded (NSNotification notification)
{
    doc = (MonkeyDocument)notification.Object;
    // we just overwrite whatever was being typed, no conflict resolution for now
    docText.Text = doc.DocumentString;
}
```

### <a name="saving-icloud-documents"></a>Salvataggio di documenti iCloud

Per aggiungere un UIDocument a iCloud è possibile chiamare `UIDocument.Save` direttamente (solo per i nuovi documenti) o spostare un file esistente `NSFileManager.DefaultManager.SetUbiquitious`usando. Il codice di esempio crea un nuovo documento direttamente nel contenitore ubiquità con questo codice (sono disponibili due gestori di completamento, uno per l' `Save` operazione e l'altro per l'apertura):

```csharp
var docsFolder = Path.Combine (iCloudUrl.Path, "Documents"); // NOTE: Documents folder is user-accessible in Settings
var docPath = Path.Combine (docsFolder, MonkeyDocFilename);
var ubiq = new NSUrl (docPath, false);
var monkeyDoc = new MonkeyDocument (ubiq);
monkeyDoc.Save (monkeyDoc.FileUrl, UIDocumentSaveOperation.ForCreating, saveSuccess => {
Console.WriteLine ("Save completion:" + saveSuccess);
if (saveSuccess) {
    monkeyDoc.Open (openSuccess => {
        Console.WriteLine ("Open completion:" + openSuccess);
        if (openSuccess) {
            Console.WriteLine ("new document for iCloud");
            Console.WriteLine (" == " + monkeyDoc.DocumentString);
            viewController.DisplayDocument (monkeyDoc);
        } else {
            Console.WriteLine ("couldn't open");
        }
    });
} else {
    Console.WriteLine ("couldn't save");
}
```

Le modifiche successive apportate al documento non vengono "salvate" direttamente, ma `UIDocument` viene detto che è stato `UpdateChangeCount`modificato con e verrà automaticamente pianificata un'operazione di salvataggio su disco:

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>Gestione dei documenti iCloud

Gli utenti possono gestire i documenti iCloud nella directory dei **documenti** del "contenitore ubiquità" all'esterno dell'applicazione tramite le impostazioni. possono visualizzare l'elenco dei file e scorrere il dito per eliminare. Il codice dell'applicazione deve essere in grado di gestire la situazione in cui i documenti vengono eliminati dall'utente. Non archiviare i dati interni dell'applicazione nella directory **documenti** .

 [![](introduction-to-icloud-images/icloudstorage.png "Gestione del flusso di lavoro dei documenti iCloud")](introduction-to-icloud-images/icloudstorage.png#lightbox)



Gli utenti riceveranno anche avvisi diversi quando tenteranno di rimuovere un'applicazione abilitata per iCloud dal dispositivo, per informare lo stato dei documenti iCloud correlati a tale applicazione.

 [![](introduction-to-icloud-images/icloud-delete1.png "Finestra di dialogo di esempio quando l'utente tenta di rimuovere un'applicazione abilitata per iCloud dal dispositivo")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "Finestra di dialogo di esempio quando l'utente tenta di rimuovere un'applicazione abilitata per iCloud dal dispositivo")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>Backup iCloud

Sebbene il backup su iCloud non sia una funzionalità accessibile direttamente dagli sviluppatori, la modalità di progettazione dell'applicazione può influire sull'esperienza utente.
Apple fornisce le [linee guida per l'archiviazione dei dati iOS](https://developer.apple.com/icloud/documentation/data-storage/) che gli sviluppatori possono seguire nelle proprie applicazioni iOS.

La considerazione più importante è se l'app archivia file di grandi dimensioni che non sono generati dall'utente (ad esempio, un'applicazione Reader di riviste che archivia centinaia più di megabyte di contenuto per ogni problema). Apple preferisce che non si memorizzi questo tipo di dati in cui verrà eseguito il backup in iCloud e il riempimento inutilmente della quota iCloud dell'utente.

Le applicazioni che archiviano grandi quantità di dati come questa devono archiviarle in una delle directory utente di cui non è stato eseguito il backup (ad esempio, Cache o TMP) o usare `NSFileManager.SetSkipBackupAttribute` per applicare un flag a questi file in modo che iCloud li ignori durante le operazioni di backup.

## <a name="summary"></a>Riepilogo

In questo articolo è stata introdotta la nuova funzionalità iCloud inclusa in iOS 5. Sono stati esaminati i passaggi necessari per configurare il progetto per l'uso di iCloud e quindi sono stati forniti esempi di come implementare le funzionalità iCloud.

L'esempio di archiviazione chiave-valore ha illustrato come usare iCloud per archiviare una piccola quantità di dati simile al modo in cui vengono archiviati i NSUserPreferences. Nell'esempio UIDocument è stato illustrato il modo in cui i dati più complessi possono essere archiviati e sincronizzati tra più dispositivi tramite iCloud.

Infine, è stata inclusa una breve discussione su come l'aggiunta del backup iCloud influirà sulla progettazione dell'applicazione.



## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a iCloud (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/introductiontoicloud)
- [Codice di esempio per il seminario iCloud](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [Diapositive del seminario iCloud](https://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [Archiviazione iCloud](https://support.apple.com/kb/HT4847)
