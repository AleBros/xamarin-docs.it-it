---
title: Uso di iCloud con xamarin. IOS
description: Questo documento descrive iCloud e sul relativo uso nelle applicazioni xamarin. IOS. Illustra archiviazione chiave-valore, archiviazione di documenti e iCloud Backup.
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/09/2016
ms.openlocfilehash: 009e061726f655999c08192b5839a5c962d35e24
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61091438"
---
# <a name="using-icloud-with-xamarinios"></a>Uso di iCloud con xamarin. IOS

L'API di archiviazione iCloud in iOS 5 consente di salvare i documenti dell'utente e i dati specifici dell'applicazione in una posizione centrale e accedere a tali elementi da tutti i dispositivi dell'utente.

Sono disponibili quattro tipi di archiviazione:

- **Archiviazione chiave-valore** : per condividere piccole quantità di dati con l'applicazione in altri dispositivi dell'utente.

- **Archiviazione** : per archiviare i documenti e altri dati nell'account iCloud dell'utente con una sottoclasse di UIDocument.

- **CoreData** -archiviazione nel database SQLite.

- **I singoli file e directory** - per la gestione di un numero elevato di file diversi direttamente nel file system.

Questo documento vengono illustrati i primi due tipi, coppie chiave-valore e sottoclassi UIDocument e su come usare tali funzionalità in xamarin. IOS.

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

## <a name="requirements"></a>Requisiti

- La versione stabile più recente di xamarin. IOS
- Xcode 10
- Visual Studio per Mac o Visual Studio 2019.

## <a name="preparing-for-icloud-development"></a>Preparazione per lo sviluppo di iCloud

Le applicazioni devono essere configurate per usare iCloud in entrambi i [portale di Provisioning Apple](https://developer.apple.com/account/ios/overview.action) e il progetto stesso. Prima di sviluppo per iCloud (o provare gli esempi) seguire questa procedura.

Per configurare correttamente un'applicazione per accedere a iCloud:

-   **Trovare il TeamID** -account di accesso a [developer.apple.com](https://developer.apple.com) e visitare il **Member Center > di un Account > Developer Account Summary** per ottenere l'ID Team (o l'ID individuale per i singoli sviluppatori ). Sarà una stringa di 10 caratteri ( **A93A5CM278** , ad esempio), ciò fa parte dell'identificatore del contenitore"".

-   **Creare un nuovo ID App** : per creare un ID App, seguire i passaggi descritti nel [Provisioning per la sezione di tecnologie Store della Guida di Device Provisioning](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)e assicurarsi di controllare **iCloud** come un consentiti del servizio:

 [![](introduction-to-icloud-images/icloud-sml.png "ICloud controllo come un servizio consentito")](introduction-to-icloud-images/icloud.png#lightbox)

- **Creare un nuovo profilo di Provisioning** : per creare un profilo di Provisioning, seguire i passaggi descritti nel [Guida Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md#provisioning-your-device) .

- **Aggiungere l'identificatore del contenitore a entitlements. plist** -il formato di identificatore del contenitore è `TeamID.BundleID`. Per altre informazioni vedere la [uso degli Entitlement](~/ios/deploy-test/provisioning/entitlements.md) Guida.

- **Configurare le proprietà del progetto** - In Info. plist file verificare che il **identificatore del Bundle** corrisponde alla **ID Bundle** impostata quando [la creazione di un ID App ](~/ios/deploy-test/provisioning/capabilities/index.md); IOS firma del Bundle Usa una **profilo di Provisioning** che contengono un ID App con servizio App, iCloud e le **Entitlement personalizzati** file selezionato. Questa operazione può tutte essere eseguita in Visual Studio sotto il riquadro Proprietà progetto.

- **Abilita iCloud nel dispositivo** : passare a **Impostazioni > iCloud** e assicurarsi che il dispositivo è connesso.
Selezionare e attivare i **documenti e dati** opzione.

- **È necessario usare un dispositivo per testare iCloud** -non funzionerà nel simulatore.
In realtà, ciò che occorre due o più dispositivi tutti effettuato l'accesso con lo stesso ID Apple per visualizzare iCloud in azione.


## <a name="key-value-storage"></a>Archiviazione chiave-valore

Archiviazione chiave-valore è destinata a piccole quantità di dati che un utente desideri persistenti tra i dispositivi - ad esempio l'ultima pagina che vengono visualizzati in un libro o magazine. Archiviazione chiave-valore non deve essere utilizzato per i dati di backup.

Esistono alcune limitazioni da tenere presenti quando si Usa archiviazione chiave-valore:

- **Dimensione massima della chiave** -nomi di chiave non possono superare i 64 byte.

- **Dimensioni del valore massimo** -non è possibile archiviare più di 64 KB in un singolo valore.

- **Dimensioni archivio chiave-valore massimo per un'app** -le applicazioni possono archiviare solo un massimo di 64 KB di dati chiave-valore in totale. Tenta di impostare le chiavi oltre il limite stabilito avrà esito negativo e il valore precedente verrà mantenute.

- **Tipi di dati** : solo i tipi di base, ad esempio stringhe, numeri e valori booleani possono essere archiviati.

Il **iCloudKeyValue** riportato di seguito viene illustrato come funziona. Il codice di esempio crea una chiave denominata per ogni dispositivo: è possibile impostare questa chiave in un dispositivo e l'analisi del valore vengono propagati ad altri utenti. Crea anche una chiave denominata "Condivisi", che possono essere modificati in qualsiasi dispositivo - se si modifica in una sola volta nel numero di dispositivi, iCloud verranno decidere quale valore "wins" (con un timestamp sulle modifiche) e ottiene propagate.

Questo screenshot Mostra il codice di esempio in uso. Quando le notifiche di modifica vengono ricevute da iCloud vengono stampate nella visualizzazione di testo scorrevole nella parte inferiore della schermata e aggiornati i campi di input.



 [![](introduction-to-icloud-images/icloud-kv-arrows.png "Il flusso dei messaggi tra i dispositivi")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>Impostazione e recupero di dati

Questo codice viene illustrato come impostare un valore stringa.

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

La chiamata di sincronizzazione assicura che il valore viene mantenuto solo l'archiviazione del disco locale. La sincronizzazione in iCloud avviene in background e non è possibile "forzare" dal codice dell'applicazione. Con connettività di rete funzioni correttamente la sincronizzazione si verifica spesso entro 5 secondi, tuttavia, se la rete è scarsa (o disconnesse) un aggiornamento potrebbe richiedere molto più tempo.

È possibile recuperare un valore con il seguente codice:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

Il valore viene recuperato dall'archivio dati locale, questo metodo non tenta di contattare i server iCloud per ottenere il valore "latest". iCloud aggiornerà l'archivio dati locale in base alla propria pianificazione.

### <a name="deleting-data"></a>Eliminazione di dati

Per rimuovere completamente una coppia chiave-valore, usare il metodo Remove simile al seguente:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>Osservare le modifiche

Un'applicazione può anche ricevere notifiche quando vengono modificati i valori da iCloud aggiungendo un osservatore al `NSNotificationCenter.DefaultCenter`.
Nell'esempio di codice dal **KeyValueViewController.cs** `ViewWillAppear` metodologia è in ascolto per le notifiche e creare un elenco di cui sono state modificate le chiavi:

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

Il codice può quindi eseguire un'azione con l'elenco di chiavi modificate, ad esempio l'aggiornamento di una copia locale di esse o l'interfaccia utente con i nuovi valori.

Modifica possibili motivi sono: ServerChange (0), InitialSyncChange (1), or QuotaViolationChange (2). È possibile il motivo di accesso ed eseguire l'elaborazione diversa se necessario (ad esempio, potrebbe essere necessario rimuovere alcuni tasti in seguito a un *QuotaViolationChange*).

## <a name="document-storage"></a>Archiviazione di documenti

Archiviazione di documenti iCloud è progettato per gestire i dati importanti per l'app (e all'utente). Può essere utilizzato per gestire file e altri dati che l'app deve essere eseguita, allo stesso tempo la condivisione di funzionalità tra tutti i dispositivi dell'utente e fornire backup su iCloud.

Questo diagramma mostra come tutti gli elementi di integrazione. I dati salvati nella risorsa di archiviazione locale (il UbiquityContainer) e iCloud del sistema operativo che daemon si occupa di inviare e ricevere dati nel cloud per ogni dispositivo. Tutti gli accessi di file per il UbiquityContainer devono essere eseguiti tramite FilePresenter/FileCoordinator per impedire l'accesso simultaneo. Il `UIDocument` classe implementa i; in questo esempio viene illustrato come utilizzare UIDocument.

 [![](introduction-to-icloud-images/icloud-overview.png "La panoramica di archiviazione di documenti")](introduction-to-icloud-images/icloud-overview.png#lightbox)

L'esempio iCloudUIDoc implementa una semplice `UIDocument` sottoclasse che contiene un campo di testo singola. Il testo viene eseguito il rendering un `UITextView` e le modifiche vengono propagate dal iCloud ad altri dispositivi con un messaggio di notifica visualizzato in rosso. Il codice di esempio non riguardano le funzionalità iCloud più avanzate, ad esempio la risoluzione dei conflitti.

Questa schermata mostra l'applicazione di esempio - dopo la modifica del testo e premendo **UpdateChangeCount** il documento viene sincronizzato tramite iCloud ad altri dispositivi.

 [![](introduction-to-icloud-images/iclouduidoc.png "Questa schermata mostra l'applicazione di esempio dopo la modifica del testo e premendo UpdateChangeCount")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

Esistono cinque parti all'esempio iCloudUIDoc:

1. **L'accesso al UbiquityContainer** -determinare se iCloud è abilitato e in tal caso, il percorso area di archiviazione iCloud dell'applicazione.

1. **Creazione di una sottoclasse UIDocument** -creare una classe da interporre tra archiviazione iCloud e gli oggetti modello.

1. **Ricerca e apertura dei documenti iCloud** -utilizzare `NSFileManager` e `NSPredicate` per trovare documenti iCloud e aprirli.

1. **Visualizzazione di documenti iCloud** -espone le proprietà dal `UIDocument` in modo che sia possibile interagire con i controlli dell'interfaccia utente.

1. **Salvataggio di documenti iCloud** -assicurarsi che le modifiche apportate nell'interfaccia utente vengano mantenute su disco e iCloud.

Tutte le operazioni di iCloud eseguire (o deve essere eseguito) in modo asincrono in modo da non bloccare durante l'attesa di un evento. Verranno visualizzati tre modi diversi di questa operazione nell'esempio:

 **Thread** : nel `AppDelegate.FinishedLaunching` la chiamata iniziale a `GetUrlForUbiquityContainer` avviene in un altro thread per evitare di bloccare il thread principale.

 **NotificationCenter** - registrazione per le notifiche quando asincrone operazioni, ad esempio `NSMetadataQuery.StartQuery` completo.

 **I gestori di completamento** : i metodi da eseguire al completamento delle operazioni asincrone, ad esempio passando `UIDocument.Open`.

### <a name="accessing-the-ubiquitycontainer"></a>L'accesso al UbiquityContainer

Con archiviazione di documenti iCloud il primo passaggio consiste nel determinare se è abilitato iCloud e in tal caso il percorso del contenitore di grande diffusione"" (la directory in cui sono archiviati i file abilitati iCloud nel dispositivo).

Questo codice si trova nel `AppDelegate.FinishedLaunching` metodo dell'esempio.

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

Anche se il codice di esempio in caso contrario, Apple consiglia di chiamare GetUrlForUbiquityContainer ogni volta che un'app passa in primo piano.

### <a name="creating-a-uidocument-subclass"></a>Creazione di una sottoclasse UIDocument

Tutti i file di iCloud e le directory (ad esempio. qualsiasi elemento archiviato nella directory UbiquityContainer) devono essere gestiti usando metodi NSFileManager, implementazione del protocollo NSFilePresenter e la scrittura tramite un NSFileCoordinator.
Il modo più semplice per eseguire le operazioni che non deve scrivere autonomamente, ma sottoclasse UIDocument che esegue tutto automaticamente.

Esistono solo due metodi che è necessario implementare nella sottoclasse UIDocument per lavorare con iCloud:

- **LoadFromContents** -passa in NSData del contenuto del file in cui decomprimere nella classe di modello/es.

- **ContentsForType** -richiesta per fornire la rappresentazione NSData di classe di modello/es di salvataggio su disco (e nel Cloud).

Questo codice di esempio dal **iCloudUIDoc\MonkeyDocument.cs** viene illustrato come implementare UIDocument.

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

In questo caso il modello di dati è molto semplice: un campo di testo singola. Il modello di dati può essere complesso, ad esempio richiesto, ad esempio un documento Xml o dati binari. Il ruolo primario dell'implementazione UIDocument consiste nel tradurre tra le classi del modello e una rappresentazione NSData che può essere salvati o caricati sul disco.

### <a name="finding-and-opening-icloud-documents"></a>Ricerca e apertura di documenti iCloud

L'app di esempio riguarda solo un singolo file - test. txt - pertanto il codice in **AppDelegate.cs** crea un' `NSPredicate` e `NSMetadataQuery` da cercare in modo specifico il nome del file. Il `NSMetadataQuery` viene eseguito in modo asincrono e invia una notifica quando viene completata. `DidFinishGathering` viene chiamato dall'osservatore notifica, interrompere la query e chiama LoadDocument, che usa il `UIDocument.Open` metodo con un gestore di completamento per tentare di caricare il file e visualizzarli in un `MonkeyDocumentViewController`.

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

Visualizzazione di un UIDocument non deve essere diversa a qualsiasi altra classe di modello
- proprietà vengono visualizzate nei controlli dell'interfaccia utente, possibilmente modificato dall'utente e quindi eseguito il writeback per il modello.

Nell'esempio **iCloudUIDoc\MonkeyDocumentViewController.cs** viene visualizzato il testo MonkeyDocument in un `UITextView`. `ViewDidLoad` è in ascolto per le notifiche inviate `MonkeyDocument.LoadFromContents` (metodo). `LoadFromContents` viene chiamato quando iCloud dispone di nuovi dati per il file, in modo che la notifica indica che il documento è stato aggiornato.

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

Il gestore di notifica di codice di esempio chiama un metodo per aggiornare l'interfaccia utente - in questo caso senza rilevamento dei conflitti o risoluzione.

```csharp
[Export ("dataReloaded:")]
void DataReloaded (NSNotification notification)
{
    doc = (MonkeyDocument)notification.Object;
    // we just overwrite whatever was being typed, no conflict resolution for now
    docText.Text = doc.DocumentString;
}
```

### <a name="saving-icloud-documents"></a>Salvataggio dei documenti iCloud

Per aggiungere un UIDocument in iCloud è possibile chiamare `UIDocument.Save` direttamente (solo per i nuovi documenti) o spostare un file esistente usando `NSFileManager.DefaultManager.SetUbiquitious`. Il codice di esempio crea un nuovo documento direttamente nel contenitore alla diffusione con questo codice (esistono due completamento gestori in questo caso, uno per il `Save` operazione e l'altro per l'apertura):

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

Le successive modifiche al documento non "salvate" direttamente, ma viene informato che la `UIDocument` che è stato modificato con `UpdateChangeCount`, e verrà pianificato automaticamente un salvataggio all'operazione del disco:

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>Gestione dei documenti iCloud

Gli utenti possono gestire i documenti in iCloud i **documenti** del "contenitore ubiquità" all'esterno dell'applicazione tramite le impostazioni della directory possono visualizzare l'elenco di file e scorrere rapidamente da eliminare. Il codice dell'applicazione deve essere in grado di gestire la situazione in cui i documenti vengono eliminati dall'utente. Non archiviare dati di un'applicazione interna nel **documenti** directory.

 [![](introduction-to-icloud-images/icloudstorage.png "La gestione del flusso di lavoro documenti iCloud")](introduction-to-icloud-images/icloudstorage.png#lightbox)



Gli utenti riceveranno anche diversi tipi di avvisi quando si tenta di rimuovere un'applicazione abilitata per iCloud dal proprio dispositivo, per informarli dello stato dei documenti iCloud correlati a tale applicazione.

 [![](introduction-to-icloud-images/icloud-delete1.png "Finestra di dialogo esempio quando l'utente tenta di rimuovere un'applicazione abilitata per iCloud dal proprio dispositivo")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "Finestra di dialogo esempio quando l'utente tenta di rimuovere un'applicazione abilitata per iCloud dal proprio dispositivo")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>Backup di iCloud

Durante il backup in iCloud non è una funzionalità a cui si accede direttamente dagli sviluppatori, la modalità di progettazione dell'applicazione può influenzare l'esperienza utente.
Apple offre [linee guida di archiviazione dei dati iOS](https://developer.apple.com/icloud/documentation/data-storage/) gli sviluppatori dovranno attenersi nelle proprie applicazioni iOS.

L'aspetto più importante è che l'app archivia i file di grandi dimensioni che non sono generati dall'utente (ad esempio, un'applicazione lettore magazine che archivia hundred-plus megabyte del contenuto per ogni problema). Apple si preferisce non archiviare questo tipo di dati in cui verrà essere sottoposti a backup in iCloud e riempire inutilmente la quota dell'utente iCloud.

Le applicazioni che archiviano grandi quantità di dati simile al seguente devono essere archiviarlo in una delle directory utente che non è sottoposta a backup (ad es. Le cache o tmp) o usare `NSFileManager.SetSkipBackupAttribute` per applicare un flag a tali file in modo che iCloud li ignora durante le operazioni di backup.

## <a name="summary"></a>Riepilogo

Questo articolo descrive la nuova funzionalità iCloud inclusa in iOS 5. Ha esaminato i passaggi necessari per configurare il progetto per usare iCloud e quindi forniti esempi di come implementare le funzionalità iCloud.

Nell'esempio di archiviazione chiave-valore dimostrato come iCloud può essere utilizzato per memorizzare una piccola quantità di dati in modo analogo al modo in cui che vengono archiviati NSUserPreferences. L'esempio UIDocument illustra come più i dati complessi possono essere archiviati e sincronizzati tra più dispositivi tramite iCloud.

Infine inclusa una breve descrizione sul modo in cui l'aggiunta di iCloud Backup deve influire sulla progettazione delle applicazioni.



## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a iCloud (esempio)](https://developer.xamarin.com/samples/monotouch/IntroductionToiCloud)
- [Codice di esempio seminario iCloud](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [iCloud seminario diapositive](https://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [Archiviazione iCloud](https://support.apple.com/kb/HT4847)
