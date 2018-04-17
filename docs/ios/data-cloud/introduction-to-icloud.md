---
title: iCloud
description: Apple ha introdotto iCloud in iOS 5 come un servizio per consentire alle applicazioni archiviare i dati nei server di Apple e fare in modo sincronizzato tra tutti i dispositivi usati dalla stessa persona (tramite i relativi ID Apple). Include anche un componente di backup, in cui i dati nei dispositivi vengano eseguito il backup per i server Apple. Questo documento viene descritto come utilizzare alcuni dei iCloud le API fornite da Apple per archiviare e recuperare dati dai loro server, con esempi di c# per archiviare coppie di dati di piccole dimensioni chiave-valore e per l'archiviazione di documenti. Illustra anche come iCloud Backup possono influenzare la progettazione dell'applicazione.
ms.prod: xamarin
ms.assetid: C6F3B87C-C195-4434-EF14-D66E63894F09
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/09/2016
ms.openlocfilehash: a62d4621a8f3ace64401d64e35c806317a591c03
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="icloud"></a>iCloud

_Apple ha introdotto iCloud in iOS 5 come un servizio per consentire alle applicazioni archiviare i dati nei server di Apple e fare in modo sincronizzato tra tutti i dispositivi usati dalla stessa persona (tramite i relativi ID Apple). Include anche un componente di backup, in cui i dati nei dispositivi vengano eseguito il backup per i server Apple. Questo documento viene descritto come utilizzare alcuni dei iCloud le API fornite da Apple per archiviare e recuperare dati dai loro server, con esempi di c# per archiviare coppie di dati di piccole dimensioni chiave-valore e per l'archiviazione di documenti. Illustra anche come iCloud Backup possono influenzare la progettazione dell'applicazione._

L'API di archiviazione iCloud in iOS 5 consente di salvare i documenti e i dati specifici dell'applicazione in una posizione centrale e accedere a tali elementi da tutti i dispositivi dell'utente.

Sono disponibili quattro tipi di archiviazione:

- **Archivio chiave-valore** : per piccole quantità di dati con l'applicazione di condivisione in altri dispositivi dell'utente.

- **Archiviazione UIDocument** : per archiviare documenti e altri dati in account iCloud dell'utente tramite una sottoclasse di UIDocument.

- **CoreData** -archiviazione del database SQLite.

- **Singoli file e directory** : per la gestione di un numero elevato di file diversi direttamente nel file system.

Questo documento illustra i primi due tipi, le coppie chiave-valore e sottoclassi UIDocument e come utilizzare le funzionalità di xamarin. IOS.

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori di gestire correttamente generale Data Protection regolamento (PILR dell'Unione europea).

## <a name="requirements"></a>Requisiti

- L'ultima versione stabile di xamarin
- Xcode 8 o versione successiva
- Visual Studio per Mac o Visual Studio 2015 e versioni successive.

## <a name="preparing-for-icloud-development"></a>Preparazione per lo sviluppo iCloud

Le applicazioni devono essere configurate per utilizzare iCloud in entrambi i [il portale di Provisioning Apple](https://developer.apple.com/account/ios/overview.action) e il progetto. Prima di sviluppo per iCloud (o provare gli esempi) attenersi alla procedura seguente.

Per configurare correttamente un'applicazione di accedere iCloud:

-   **Trovare l'ID team** -account di accesso [developer.apple.com](http://developer.apple.com) e visitare il **Member Center > l'Account > riepilogo Account sviluppatore** per ottenere l'ID Team (o ID singolo per gli sviluppatori singoli ). Sia una stringa di 10 caratteri ( **A93A5CM278** ad esempio)-questo fa parte dell'identificatore"contenitore".

-   **Creare un nuovo ID di App** : per creare un ID App, attenersi alla procedura descritta nel [Provisioning per la sezione di tecnologie di archivio della Guida di Provisioning dei dispositivi](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md)e assicurarsi di controllare **iCloud** come un servizio consentito:

 [![](introduction-to-icloud-images/icloud-sml.png "Controllo iCloud come servizio consentito")](introduction-to-icloud-images/icloud.png#lightbox)

- **Creare un nuovo profilo di Provisioning** : per creare un profilo di Provisioning, attenersi alla procedura descritta nel [Provisioning dei dispositivi Guida](~/ios/get-started/installation/device-provisioning/index.md#Provisioning_Profile) .

- **Aggiungere l'identificatore del contenitore a Entitlements.plist** -il formato di identificatore del contenitore è `TeamID.BundleID`. Per ulteriori informazioni consultare il [funziona con i diritti](~/ios/deploy-test/provisioning/entitlements.md) Guida.

- **Configurare le proprietà del progetto** - In di Info. plist file verificare il **identificatore Bundle** corrisponde la **ID Bundle** impostato quando [la creazione di un ID App ](~/ios/deploy-test/provisioning/capabilities/index.md); Firma Bundle Usa iOS un **profilo di Provisioning** che contengono un ID di App con il servizio App, iCloud e **i diritti personalizzati** file selezionato. Tutto ciò consente in Visual Studio sotto il riquadro proprietà di progetto.

- **Abilitare iCloud sul dispositivo** : passare a **Impostazioni > iCloud** e assicurarsi che il dispositivo è connesso.
Selezionare e attivare il **documenti e dati** opzione.

- **È necessario utilizzare un dispositivo per test iCloud** -non funzionerà nel simulatore.
In effetti, due o più dispositivi tutti firmati con lo stesso ID Apple per vedere in azione iCloud sia davvero necessario.


## <a name="key-value-storage"></a>Archivio chiave-valore

Archiviazione dei valori di chiave è destinata a piccole quantità di dati che un utente desideri persistenti tra i dispositivi: ad esempio l'ultima pagina che è visualizzato in un libro o magazine. Archiviazione dei valori di chiave non utilizzabile per i dati di backup.

Esistono alcune limitazioni da tenere presenti quando si utilizza l'archiviazione dei valori di chiave:

- **Dimensione massima della chiave** -i nomi delle chiavi non può essere più lungo di 64 byte.

- **Dimensioni del valore massimo** -non è possibile archiviare più di 64 KB in un singolo valore.

- **Dimensioni dell'archivio chiave-valore massimo per un'app** -applicazioni possono contenere un massimo di 64 KB di dati per valori di chiave in totale. Tenta di impostare le chiavi oltre il limite stabilito avrà esito negativo e il valore precedente verrà mantenuti.

- **Tipi di dati** : solo i tipi di base quali stringhe, numeri e possono essere archiviati valori booleani.

Il **iCloudKeyValue** riportato di seguito viene illustrato il funzionamento. Il codice di esempio crea una chiave denominata per ogni dispositivo: è possibile impostare questa chiave in un dispositivo e controllare il valore devono essere propagate ad altri utenti. Crea inoltre una chiave denominata "Shared", che possono essere modificati in qualsiasi dispositivo, se si modifica in una sola volta su diversi dispositivi, iCloud decide che il valore "wins" (utilizzo di un timestamp sulla modifica) e ottiene propagate.

Questa schermata è riportato l'esempio in uso. Quando riceve le notifiche di modifica iCloud sono stampati nella visualizzazione di testo scorrimento nella parte inferiore dello schermo e aggiornare i campi di input.



 [![](introduction-to-icloud-images/icloud-kv-arrows.png "Il flusso di messaggi tra i dispositivi")](introduction-to-icloud-images/icloud-kv-arrows.png#lightbox)

### <a name="setting-and-retrieving-data"></a>Impostazione e recupero di dati

Questo codice viene illustrato come impostare un valore stringa.

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.SetString("testkey", "VALUE IN THE CLOUD");  // key and value
store.Synchronize();
```

La chiamata di sincronizzazione assicura che il valore viene mantenuto solo l'archiviazione su disco locale. La sincronizzazione su iCloud avviene in background e non può essere "imposto" codice dell'applicazione. Connettività di rete funzioni correttamente con la sincronizzazione spesso avverrà entro 5 secondi, tuttavia, se la rete è scarsa (o disconnesse) un aggiornamento potrebbe richiedere molto più tempo.

È possibile recuperare un valore con il seguente codice:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
display.Text = store.GetString("testkey");
```

Il valore viene recuperato dall'archivio dati locale, questo metodo non tenta di contattare i server iCloud per ottenere il valore "più recente". iCloud aggiornerà l'archivio dati locale in base alla propria pianificazione.

### <a name="deleting-data"></a>Eliminazione di dati

Per rimuovere completamente una coppia chiave-valore, utilizzare il metodo Remove simile al seguente:

```csharp
var store = NSUbiquitousKeyValueStore.DefaultStore;
store.Remove("testkey");
store.Synchronize();
```

### <a name="observing-changes"></a>Osservare le modifiche

Un'applicazione può anche ricevere notifiche quando vengono modificati i valori da iCloud aggiungendo un osservatore al `NSNotificationCenter.DefaultCenter`.
Nell'esempio di codice da **KeyValueViewController.cs** `ViewWillAppear` metodo illustra come attendere le notifiche e creare un elenco di cui sono state modificate le chiavi:

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

Il codice può quindi eseguire un'azione con l'elenco di chiavi modificate, ad esempio l'aggiornamento di una copia locale o l'aggiornamento dell'interfaccia utente con i nuovi valori.

Modifica possibili motivi sono: ServerChange (0), InitialSyncChange (1) o QuotaViolationChange (2). È possibile accedere il motivo ed eseguire operazioni di elaborazione diverso, se necessario (ad esempio, potrebbe essere necessario rimuovere alcuni tasti come risultato di un *QuotaViolationChange*).

## <a name="document-storage"></a>Archiviazione di documenti

iCloud archiviazione documento è progettato per gestire i dati che sono importanti per l'app (e all'utente). E può essere utilizzato per gestire file e altri dati che l'app deve essere eseguito, mentre nello stesso momento fornendo il backup su iCloud e funzionalità di condivisione tra tutti i dispositivi dell'utente.

Questo diagramma viene illustrata la modalità tutto complessivo. Dati salvati in iCloud del sistema operativo che daemon si occupa di invio e ricezione di dati nel cloud e di archiviazione locale (il UbiquityContainer) per ogni dispositivo. Tutti gli accessi di file per il UbiquityContainer devono essere eseguito tramite FilePresenter/FileCoordinator per impedire l'accesso simultaneo. Il `UIDocument` implementa quelli automaticamente; in questo esempio viene illustrato come utilizzare UIDocument.

 [![](introduction-to-icloud-images/icloud-overview.png "Panoramica dell'archiviazione di documenti")](introduction-to-icloud-images/icloud-overview.png#lightbox)

Nell'esempio iCloudUIDoc implementa una semplice `UIDocument` sottoclasse che contiene un campo di testo singola. Il testo viene visualizzato un `UITextView` e le modifiche vengono propagate dal iCloud ad altri dispositivi con un messaggio di notifica in rosso. Il codice di esempio non si occupa funzionalità iCloud più avanzate, come la risoluzione dei conflitti.

Questa schermata mostra l'applicazione di esempio - dopo la modifica del testo e premendo **UpdateChangeCount** il documento viene sincronizzato tramite iCloud ad altri dispositivi.

 [![](introduction-to-icloud-images/iclouduidoc.png "Questa schermata mostra l'applicazione di esempio dopo la modifica del testo e premendo UpdateChangeCount")](introduction-to-icloud-images/iclouduidoc.png#lightbox)

Sono disponibili per l'esempio iCloudUIDoc composto da cinque parti:

1. **L'accesso di UbiquityContainer** -determinare se iCloud è abilitato e in tal caso, il percorso di area di archiviazione iCloud dell'applicazione.

1. **Creazione di una sottoclasse UIDocument** -creare una classe da interporre tra gli oggetti modello e di archiviazione iCloud.

1. **Ricerca e apertura dei documenti iCloud** -utilizzare `NSFileManager` e `NSPredicate` per trovare i documenti iCloud e aprirli.

1. **Visualizzazione di documenti iCloud** -esporre le proprietà del `UIDocument` in modo che sia possibile interagire con i controlli dell'interfaccia utente.

1. **Salvataggio di documenti iCloud** -verificare che le modifiche apportate nell'interfaccia utente sono persistenti su disco e in iCloud.

Tutte le operazioni iCloud eseguire (o deve essere eseguito) in modo asincrono in modo da non bloccare durante l'attesa di un intervento. Verranno visualizzati tre diversi modi per eseguire questa nell'esempio:

 **Thread** : nel `AppDelegate.FinishedLaunching` la chiamata iniziale a `GetUrlForUbiquityContainer` viene eseguita in un altro thread per evitare di bloccare il thread principale.

 **NotificationCenter** - registrazione per le notifiche quando asincrona operazioni, ad esempio `NSMetadataQuery.StartQuery` completo.

 **I gestori di completamento** - passando metodi da eseguire al completamento di operazioni asincrone come `UIDocument.Open`.

### <a name="accessing-the-ubiquitycontainer"></a>L'accesso di UbiquityContainer

Il primo passaggio nell'utilizzo iCloud documento archiviazione consiste nel determinare se iCloud è abilitato e in tal caso, il percorso del contenitore alla diffusione"" (la directory in cui sono archiviati i file abilitato iCloud nel dispositivo).

Questo codice è il `AppDelegate.FinishedLaunching` metodo dell'esempio.

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

Sebbene l'esempio in caso contrario, si consiglia Apple chiamata GetUrlForUbiquityContainer ogni volta che un'app passa in primo piano.

### <a name="creating-a-uidocument-subclass"></a>Creazione di una sottoclasse UIDocument

Tutti i file iCloud e le directory (ie. qualsiasi elemento archiviato nella directory UbiquityContainer) deve essere gestito utilizzando i metodi NSFileManager, implementazione del protocollo NSFilePresenter e la scrittura tramite un NSFileCoordinator.
Il modo più semplice per eseguire le operazioni che è non di scriverlo tuttavia sottoclasse UIDocument che fa tutto per l'utente.

Sono disponibili solo due metodi che deve essere implementata in una sottoclasse UIDocument per funzionare con iCloud:

- **LoadFromContents** -passa in NSData del contenuto del file in cui decomprimere nella classe di modello/es.

- **ContentsForType** -richiesta per poter fornire la rappresentazione di NSData della classe di modello/es salvare su disco (e nel Cloud).

Questo codice di esempio da **iCloudUIDoc\MonkeyDocument.cs** viene illustrato come implementare UIDocument.

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

In questo caso il modello di dati è molto semplice, un campo di testo singola. Il modello di dati può essere complesso come richiesto, ad esempio un documento Xml o dati binari. Il ruolo primario dell'implementazione UIDocument consiste nel convertire tra le classi di modello e la rappresentazione NSData che può essere salvati o caricati su disco.

### <a name="finding-and-opening-icloud-documents"></a>Ricerca e apertura di documenti iCloud

L'app di esempio sono disponibili solo con un singolo file - test.txt - pertanto il codice in **appdelegate. cs** crea un `NSPredicate` e `NSMetadataQuery` per la ricerca in modo specifico per questo nome file. Il `NSMetadataQuery` viene eseguito in modo asincrono e invia una notifica quando viene completato. `DidFinishGathering` viene chiamato dall'osservatore di notifica, interrompere la query e chiama LoadDocument, che utilizza il `UIDocument.Open` metodo con un gestore di completamento per tentare di caricare il file e visualizzarli in un `MonkeyDocumentViewController`.

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

Visualizzazione di un UIDocument non deve essere diversa in qualsiasi altra classe di modello
- proprietà vengono visualizzate nei controlli dell'interfaccia utente, probabilmente è stato modificato dall'utente e quindi scritta nel modello.

Nell'esempio **iCloudUIDoc\MonkeyDocumentViewController.cs** viene visualizzato il testo MonkeyDocument in un `UITextView`. `ViewDidLoad` è in ascolto per le notifiche inviate `MonkeyDocument.LoadFromContents` metodo. `LoadFromContents` viene chiamato quando iCloud con nuovi dati per il file, in modo che notifica indica che il documento è stato aggiornato.

```csharp
NSNotificationCenter.DefaultCenter.AddObserver (this,
    new Selector ("dataReloaded:"),
    new NSString ("monkeyDocumentModified"),
    null
);
```

Il gestore di notifica di codice di esempio chiama un metodo per aggiornare in questo caso l'interfaccia utente - senza il rilevamento dei conflitti o risoluzione.

```csharp
[Export ("dataReloaded:")]
void DataReloaded (NSNotification notification)
{
    doc = (MonkeyDocument)notification.Object;
    // we just overwrite whatever was being typed, no conflict resolution for now
    docText.Text = doc.DocumentString;
}
```

### <a name="saving-icloud-documents"></a>Salvare i documenti iCloud

Per aggiungere un UIDocument in iCloud è possibile chiamare `UIDocument.Save` direttamente (solo per i nuovi documenti) o spostare un file esistente usando `NSFileManager.DefaultManager.SetUbiquitious`. Nell'esempio di codice crea un nuovo documento direttamente nel contenitore alla diffusione con il codice seguente (sono disponibili due completamento gestori in questo caso, uno per il `Save` operazione e l'altro per l'apertura):

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

Le successive modifiche al documento non vengono "salvate" direttamente, ma è possibile indicare il `UIDocument` modificati con `UpdateChangeCount`, e verrà automaticamente pianificato per l'operazione di salvataggio:

```csharp
doc.UpdateChangeCount (UIDocumentChangeKind.Done);
```

### <a name="managing-icloud-documents"></a>Gestione di documenti su iCloud

Gli utenti possono gestire i documenti in iCloud il **documenti** directory del "contenitore alla diffusione" all'esterno dell'applicazione tramite le impostazioni possono visualizzare l'elenco di file e scorrere rapidamente per eliminare. Il codice dell'applicazione deve essere in grado di gestire la situazione in cui i documenti vengono eliminati dall'utente. Non archiviare dati interne dell'applicazione di **documenti** directory.

 [![](introduction-to-icloud-images/icloudstorage.png "La gestione del flusso di lavoro documenti iCloud")](introduction-to-icloud-images/icloudstorage.png#lightbox)



Gli utenti riceveranno anche diversi tipi di avvisi durante il tentativo di rimuovere un'applicazione abilitata per iCloud dal proprio dispositivo, per informarli dello stato di iCloud documenti correlati a tale applicazione.

 [![](introduction-to-icloud-images/icloud-delete1.png "Finestra di dialogo esempio quando l'utente tenta di rimuovere un'applicazione abilitata per iCloud dal proprio dispositivo")](introduction-to-icloud-images/icloud-delete1.png#lightbox)

 [![](introduction-to-icloud-images/icloud-delete2.png "Finestra di dialogo esempio quando l'utente tenta di rimuovere un'applicazione abilitata per iCloud dal proprio dispositivo")](introduction-to-icloud-images/icloud-delete2.png#lightbox)

## <a name="icloud-backup"></a>Backup iCloud

Durante l'esecuzione del backup su iCloud non è una funzionalità che si accede direttamente dagli sviluppatori, la modalità di progettazione dell'applicazione può influenzare l'esperienza utente.
Apple fornisce [iOS linee guida di archiviazione di dati](http://developer.apple.com/icloud/documentation/data-storage/) agli sviluppatori di seguire nelle proprie applicazioni iOS.

L'aspetto più importante è che l'applicazione archivia i file di grandi dimensioni che non sono generati dall'utente (ad esempio, un'applicazione di lettore rivista che archivia hundred-plus megabyte del contenuto per ogni problema). Apple preferisce non archiviare questo tipo di dati in cui verrà essere sottoposti a backup in iCloud e riempire inutilmente la quota dell'utente iCloud.

Le applicazioni che archiviano grandi quantità di dati simile al seguente devono essere archiviarlo in una delle directory utente che non viene eseguito il backup (ad es. Memorizza nella cache o tmp) oppure utilizzare `NSFileManager.SetSkipBackupAttribute` per applicare un flag a tali file in modo che li ignora iCloud durante le operazioni di backup.

## <a name="summary"></a>Riepilogo

In questo articolo è stata introdotta la nuova funzionalità iCloud inclusa in iOS 5. È stato esaminato i passaggi necessari per configurare il progetto per utilizzare iCloud e quindi forniti esempi su come implementare le funzionalità di iCloud.

Nell'esempio di archivio chiave-valore illustrati come consente di archiviare una piccola quantità di dati simile al modo in cui che sono archiviati NSUserPreferences iCloud. L'esempio di UIDocument ha mostrato come più dati complessi possono essere archiviati e sincronizzati tra più dispositivi tramite iCloud.

Infine inclusa una breve descrizione a come l'aggiunta di iCloud Backup deve influire sulla progettazione dell'applicazione.



## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a iCloud (esempio)](https://developer.xamarin.com/samples/monotouch/IntroductionToiCloud)
- [Codice di esempio seminario iCloud](https://github.com/xamarin/Seminars/tree/master/2012-03-22-iCloud)
- [iCloud seminario diapositive](http://www.slideshare.net/Xamarin/using-icloud-with-monotouch)
- [iCloud NSUbiquitousKeyValueStore](https://developer.apple.com/library/prerelease/ios/)
- [Archiviazione iCloud](http://support.apple.com/kb/HT4847)
