---
title: CloudKit in xamarin. IOS
description: Questo documento descrive come utilizzare i CloudKit in xamarin. IOS. Fornisce una panoramica di CloudKit e viene descritto come abilitare lo, CloudKit praticità API, scalabilità, gli account utente e ambienti di sviluppo e produzione.
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/11/2016
ms.openlocfilehash: 941d39510d05c95ff06ffd3d55685c002bd4d4e5
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34785057"
---
# <a name="cloudkit-in-xamarinios"></a>CloudKit in xamarin. IOS

Il framework CloudKit semplifica lo sviluppo di applicazioni iCloud tale accesso. Sono inclusi il recupero di dati dell'applicazione e i diritti di asset, nonché la possibilità di archiviare in modo sicuro le informazioni dell'applicazione. Questo kit offre agli utenti un livello di anonimato consentendo l'accesso alle applicazioni con i relativi ID iCloud senza condividere le informazioni personali.

Gli sviluppatori possono concentrarsi sulle proprie applicazioni sul lato client e consente di eliminare la necessità di scrivere la logica dell'applicazione sul lato server iCloud. CloudKit fornisce autenticazione, database privati e pubblici e i dati strutturati e servizi di archiviazione di asset.

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

## <a name="requirements"></a>Requisiti

È necessario completare i passaggi illustrati in questo articolo:

-  **Xcode e iOS SDK** – Xcode e iOS 8 Apple API devono essere installati e configurati nel computer dello sviluppatore.
-  **Visual Studio per Mac** : la versione più recente di Visual Studio per Mac deve essere installata e configurata sul dispositivo dell'utente.
-  **iOS 8 dispositivo** : un dispositivo iOS in esecuzione la versione più recente di iOS 8 per il test.

## <a name="what-is-cloudkit"></a>Che cos'è CloudKit?

CloudKit è un modo per fornire allo sviluppatore di accedere al server iCloud. Fornisce le basi per iCloud unità sia iCloud raccolta foto. CloudKit è supportato nei dispositivi iOS sia Mac OS X e Apple.

 [![](intro-to-cloudkit-images/image1.png "Modalità CloudKit è supportato in Mac OS X sia i dispositivi iOS di Apple")](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit utilizza l'infrastruttura di Account iCloud. Se un utente è connesso a un Account nel dispositivo iCloud, CloudKit i relativi ID verrà utilizzato per identificare l'utente. Se non sono disponibili account, verrà fornito un accesso limitato di sola lettura.

CloudKit supporta sia il concetto di database pubblici e privati. Pubblici database offrono "soup" di tutti i dati che l'utente ha accesso a. Database privati sono concepiti per archiviare i dati privati associati a un utente specifico.

CloudKit supporta dati strutturati e bulk. È in grado di gestire facilmente i trasferimenti di file di grandi dimensioni. CloudKit farà in modo efficiente il trasferimento di file di grandi dimensioni da e verso i server iCloud in background, liberando lo sviluppatore di concentrarsi su altre attività.

> [!NOTE]
> È importante notare che CloudKit è un _tecnologia trasporto_. Non fornisce persistenza; consente solo di un'applicazione inviare e ricevere informazioni dai server in modo efficiente.

Redazione del presente documento, Apple inizialmente fornisce CloudKit gratuitamente con un limite elevato sia la larghezza di banda e capacità di archiviazione. Per i progetti o applicazioni con un utente di grandi dimensioni grandi dimensioni, Apple ha suggerisce che vengano fornita una scala di prezzo conveniente.


## <a name="enabling-cloudkit-in-a-xamarin-application"></a>Abilitazione CloudKit in un'applicazione di Xamarin

Prima di un'applicazione di Xamarin è possibile utilizzare il CloudKit Framework, l'applicazione deve essere corretta esecuzione del provisioning come descritto nel [utilizzo con funzionalità](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) e [funziona con i diritti](~/ios/deploy-test/provisioning/entitlements.md) Guide

1.  Aprire il progetto in Visual Studio per Mac o Visual Studio.
2.  Nel **Esplora**, aprire il **Info. plist** e assicurarsi di **identificatore Bundle** corrisponda a quella definita in precedenza **ID App**creato come parte del provisioning configurare:
 
    [![](intro-to-cloudkit-images/image26a.png "Immettere l'identificatore Bundle")](intro-to-cloudkit-images/image26a-orig.png#lightbox "Info.plist file displaying Bundle Identifier")

3.  Scorrere fino alla fine del **Info. plist** file e selezionare **la modalità di Background abilitato**, **aggiornamenti percorso** e **notifiche remoto**:

    [![](intro-to-cloudkit-images/image27a.png "Selezionare una modalità di Background abilitata, gli aggiornamenti di percorso e le notifiche Remote")](intro-to-cloudkit-images/image27a-orig.png#lightbox "Info.plist file displaying background modes")
4.  Fare clic sul progetto nella soluzione e selezionare iOS **opzioni**.
5.  Selezionare **firma Bundle iOS**, selezionare il **identità Developer** e **profilo di Provisioning** creato in precedenza.
6.  Verificare che il **Entitlements.plist** include **abilitare iCloud** , **archivio chiave-valore** e **CloudKit** .
7.  Verificare che il **contenitore alla diffusione** esiste per l'applicazione (come creato in precedenza). Esempio: `iCloud.com.your-company.CloudKitAtlas`
8.  Salvare le modifiche apportate al file.


Con queste impostazioni, l'applicazione è ora possibile accedere alle API CloudKit Framework.

## <a name="cloudkit-api-overview"></a>Panoramica dell'API CloudKit

Prima di implementare CloudKit in un'applicazione iOS Xamarin, in questo articolo verrà per coprire le nozioni di base di CloudKit Framework, che include gli argomenti seguenti:

1.  **Contenitori** – isolato silo di comunicazioni iCloud.
2.  **Database** : Public e private sono disponibili per l'applicazione.
3.  **Record** : il meccanismo in cui i dati strutturati vengono spostati verso e da CloudKit.
4.  **Le zone record** – sono gruppi di record.
5.  **Registrare gli identificatori** : vengono normalizzati completamente e rappresentano il percorso specifico del record.
6.  **Riferimento** : fornire padre-figlio le relazioni tra i record correlati all'interno di un determinato Database.
7.  **Asset** -Consenti per file di dati non strutturati e di grandi dimensioni da caricare in iCloud e associata a un determinato Record.


### <a name="containers"></a>Contenitori

Una determinata applicazione in esecuzione in un dispositivo iOS è sempre in esecuzione insieme sul lato di altre applicazioni e servizi nel dispositivo. Nel dispositivo client, l'applicazione sta per essere silo o create mediante sandbox in qualche modo. In alcuni casi, si tratta di un valore letterale sandbox e in altri, l'applicazione è semplicemente in esecuzione in esso è uno spazio di memoria propria.

Il concetto di esecuzione di un'applicazione client e in esecuzione, separati da altri client è molto potente e offre i vantaggi seguenti:

1.  **Sicurezza** : un'applicazione non può interferire con altre applicazioni client o il sistema operativo stesso.
1.  **Stabilità** : se l'applicazione client si blocca, non è possibile estrarre da altre applicazioni del sistema operativo.
1.  **Privacy** – ciascuna applicazione client ha accesso limitato alle informazioni personali archiviate all'interno del dispositivo.


CloudKit è stato progettato per fornire gli stessi vantaggi, come indicato sopra e di applicarlo all'utilizzo di informazioni basato su cloud:

 [![](intro-to-cloudkit-images/image31.png "Le applicazioni di CloudKit comunicare utilizzando i contenitori")](intro-to-cloudkit-images/image31.png#lightbox)

Come l'applicazione è in esecuzione sul dispositivo, uno di molti è così le comunicazioni dell'applicazione con uno di molti iCloud. Ognuno di questi contenitori di comunicazione diversi vengono chiamati i contenitori.

I contenitori vengono esposte in CloudKit Framework tramite la `CKContainer` classe. Per impostazione predefinita, si esprime un'applicazione a un contenitore e questo contenitore isola i dati per tale applicazione. Ciò significa che più applicazioni possono essere l'archiviazione delle informazioni per lo stesso account iCloud, ma queste informazioni non verranno mai coesistere.

La creazione dei contenitori di dati iCloud consente inoltre di CloudKit incapsulare le informazioni utente. In questo modo, l'applicazione avrà accesso limitato per l'account iCloud e le informazioni utente archiviate all'interno, tutti proteggendo al contempo la sicurezza dell'utente e la privacy.

I contenitori sono completamente gestiti dallo sviluppatore dell'applicazione tramite il portale WWDR. Lo spazio dei nomi del contenitore è globale tra tutti gli sviluppatori di Apple, pertanto il contenitore non deve solo essere univoco per le applicazioni dello sviluppatore specificato, ma per tutte le applicazioni e gli sviluppatori di Apple.

Apple consiglia di usare la notazione DNS inversa quando si crea lo spazio dei nomi per i contenitori di applicazioni. Esempio:

```csharp
iCloud.com.company-name.application-name
```

Mentre i contenitori sono, per impostazione predefinita, associato uno una determinata applicazione, può essere condivisa tra applicazioni. Consente di coordinare in modo più applicazioni in un singolo contenitore. Una singola applicazione può anche comunicare a più contenitori.

### <a name="databases"></a>Database

Una delle funzioni principali di CloudKit consiste nel disconnettere il modello di dati di un'applicazione e replica di tale modello fino a server iCloud. Alcune informazioni deve essere l'utente che lo ha creato, altre informazioni sono pubblici dati che è stato possibile creare da un utente per uso pubblico (ad esempio, una revisione del ristorante) oppure può trattarsi di informazioni che lo sviluppatore ha pubblicato per l'applicazione. In entrambi i casi, i destinatari non sono un singolo utente, ma è una comunità di utenti.

 [![](intro-to-cloudkit-images/image32.png "Diagramma di contenitore CloudKit")](intro-to-cloudkit-images/image32.png#lightbox)

All'interno di un contenitore, prima di tutto è il database public. Si tratta in tutte le informazioni pubbliche vive e mingles condiviso. Sono inoltre disponibili numerosi singoli database privati per ogni utente dell'applicazione.

Quando si esegue in un dispositivo iOS, l'applicazione avranno accesso solo alle informazioni per l'utente attualmente connesso iCloud. Pertanto la visualizzazione dell'applicazione del contenitore saranno come indicato di seguito:

 [![](intro-to-cloudkit-images/image33.png "La visualizzazione di applicazioni del contenitore")](intro-to-cloudkit-images/image33.png#lightbox)

È possibile visualizzare solo i database public e il database privato associato all'utente attualmente connesso iCloud.

I database vengono esposte in CloudKit Framework tramite la `CKDatabase` classe. Ogni applicazione dispone di accesso ai database di due: il database pubblico e quella privata.

Il contenitore è il punto di ingresso iniziale in CloudKit. Il codice seguente consente di accedere al database pubblico e privato da predefinito dell'applicazione contenitore:

```csharp
using CloudKit;
...

public CKDatabase PublicDatabase { get; set; }
public CKDatabase PrivateDatabase { get; set; }
...

// Get the default public and private databases for
// the application
PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;
```

Di seguito sono le differenze tra i tipi di database:

||Database Public|Database privato|
|---|--- |--- |
|**Tipo di dati**|Dati condivisi|Dati dell'utente corrente|
|**Quota**|Presi in considerazione in Quota dello sviluppatore|Considerare per la Quota dell'utente|
|**Autorizzazioni predefinite**|World leggibile|Utente leggibile|
|**Modifica delle autorizzazioni**|iCloud Dashboard ruoli tramite un livello di classe di record|N/D|

### <a name="records"></a>Record

I database e all'interno di database sono record di contenitori. I record sono il meccanismo in cui i dati strutturati vengono spostati verso e da CloudKit:

 [![](intro-to-cloudkit-images/image34.png "I database e all'interno di database sono record di contenitori")](intro-to-cloudkit-images/image34.png#lightbox)

I record vengono esposte in CloudKit Framework tramite il `CKRecord` (classe), che esegue il wrapping di coppie chiave-valore. Equivale a un'istanza di un oggetto in un'applicazione un `CKRecord` in CloudKit. Inoltre, ogni `CKRecord` dispone di un tipo di record, che equivale alla classe dell'oggetto.

Record dispongono di uno schema di just-in-time, pertanto i dati sono descritti per CloudKit prima di essere passato per l'elaborazione. Da questo punto, CloudKit verrà interpretare le informazioni e gestire la logistica per archiviare e recuperare il record.

La `CKRecord` classe supporta anche un'ampia gamma di metadati. Ad esempio, un record contiene informazioni sulla creazione e l'utente che li ha creati. Un record contiene inoltre informazioni dell'ultima modifica e l'utente che è stato modificato.

I record contengono la nozione di un Tag di modifica. Si tratta di una versione precedente di una revisione di un determinato record. Viene utilizzato il Tag di modifica in modo più semplice per determinare se il client e server di avere la stessa versione di un record specifico.

Come descritto in precedenza, `CKRecords` esegue il wrapping di coppie chiave-valore e di conseguenza, i tipi di dati seguenti possono essere archiviati in un record:

1.   `NSString`
1.   `NSNumber`
1.   `NSData`
1.   `NSDate`
1.   `CLLocation`
1.   `CKReferences`
1.   `CKAssets`


Oltre ai tipi di valore singolo, un record può contenere una matrice omogenea di uno dei tipi elencati sopra.

Il codice seguente consente di creare un nuovo record e archiviarla in un database:

```csharp
using CloudKit;
...

private const string ReferenceItemRecordName = "ReferenceItems";
...

var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;
await CloudManager.SaveAsync (newRecord);
```

### <a name="record-zones"></a>Le zone di record

Non esistono record autonomamente in un determinato database, gruppi di record di coesistere all'interno di una zona di Record. Zone record può essere considerate come tabelle nei database relazionali tradizionali:

 [![](intro-to-cloudkit-images/image35.png "Gruppi di record di coesistere all'interno di una zona di Record")](intro-to-cloudkit-images/image35.png#lightbox)

Possono esserci più record all'interno di una determinata zona di Record e più zone di Record all'interno di un determinato database. Ogni database contiene una zona di Record predefinito:

 [![](intro-to-cloudkit-images/image36.png "Ogni database contiene una zona Record predefinita e un'area personalizzata")](intro-to-cloudkit-images/image36.png#lightbox)

Si tratta in cui sono archiviati i record per impostazione predefinita. Inoltre, possibile creare zone Record personalizzato. Rappresentano le zone di record viene eseguita la granularità di base al quale il commit atomico e rilevamento delle modifiche.

## <a name="record-identifiers"></a>Identificatori di record

Gli identificatori di record sono rappresentati come una tupla, che contiene sia un client Record il nome specificato e la zona in cui è presente il record. Gli identificatori di record presentano le caratteristiche seguenti:

-  Vengono creati dall'applicazione client.
-  Essi vengono normalizzati completamente e rappresentano il percorso specifico del record.
-  Assegnando l'ID univoco di un record in un database esterno per il nome del record, possono essere utilizzati per collegare i database locali che non vengono memorizzati in CloudKit.


Quando gli sviluppatori creano nuovi record, è possibile scegliere di passare un identificatore di Record. Se non viene specificato un identificatore di Record, un UUID verrà automaticamente creato e assegnato al record.

Quando gli sviluppatori creano nuovi identificatori di Record, è possibile specificare l'area di Record che appartengono a ogni record. Se non viene specificato, verrà utilizzato il fuso Record predefinito.

Gli identificatori di record vengono esposte in CloudKit Framework tramite la `CKRecordID` classe. Il codice seguente consente di creare un nuovo identificatore di Record:

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>Riferimenti

Riferimenti forniscono le relazioni tra i record correlati all'interno di un Database:

 [![](intro-to-cloudkit-images/image37.png "Riferimenti forniscono le relazioni tra i record correlati all'interno di un Database")](intro-to-cloudkit-images/image37.png#lightbox)

Nell'esempio precedente, gli elementi padre disporre elementi figlio in modo che l'elemento figlio è un record figlio del record padre. La relazione esce dal record figlio al record padre e viene definita un *nuovamente riferimento*.

I riferimenti vengono esposte in CloudKit Framework tramite la `CKReference` classe. Sono un modo di lasciare che il server iCloud comprendere la relazione tra record.

Riferimenti forniscono il meccanismo alla base eliminazioni a cascata. Se un record padre viene eliminato dal database, qualsiasi record figlio (come specificato in una relazione) verrà automaticamente eliminato dal database anche.

> [!NOTE]
> I puntatori inesatti sono una possibilità quando si utilizza CloudKit. Ad esempio, quando l'applicazione ha recuperato un elenco di puntatori di record, la selezione di un record e quindi richiesto per il record, il record non esiste più nel database. Un'applicazione deve essere codificata per gestire questa situazione normalmente.

Sebbene non sia necessario, nuovamente i riferimenti sono preferibili quando si lavora con il CloudKit Framework. Apple ha ottimizzato il sistema per rendere questo tipo di riferimento più efficiente.

Quando si crea un riferimento, lo sviluppatore può fornire un record che è già in memoria oppure creare un riferimento a un identificatore di Record. Se utilizzando un identificatore del Record e il riferimento specificato non esiste nel database, viene creato un puntatore (dangling).

Di seguito è riportato un esempio di creazione di un riferimento con un Record noti:

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>Risorse

Asset consentono a un file di dati non strutturati e di grandi dimensioni da caricare in iCloud e associata a un determinato Record:

 [![](intro-to-cloudkit-images/image38.png "Asset consentano per un file di dati non strutturati e di grandi dimensioni da caricare in iCloud e associata a un determinato Record")](intro-to-cloudkit-images/image38.png#lightbox)

Nel client, un `CKRecord` creato che descrive il file che sta per essere caricate nel server iCloud. Oggetto `CKAsset` viene creato per contenere il file e il record collegato.

Quando viene caricato il file al server, il record viene inserito nel database e il file viene copiato in un database di archiviazione di massa speciale. Viene creato un collegamento tra il record di puntatore e il file caricato.

Gli asset vengono esposte in CloudKit Framework tramite la `CKAsset` classe e vengono utilizzati per archiviare i dati di grandi dimensioni, non strutturati. Perché lo sviluppatore non desidera mai dispongono di dati non strutturati e di grandi dimensioni in memoria, gli asset vengono implementati mediante i file sul disco.

Gli asset sono di proprietà per i record, che consente le risorse da recuperare dalla iCloud tramite il record come un puntatore. In questo modo il server può Garbage raccogliere asset quando viene eliminato il record che possiede l'Asset.

Poiché `CKAssets` devono gestire i file di dati di grandi dimensioni, Apple progettato CloudKit per caricare e scaricare le risorse in modo efficiente.

Il codice seguente consente di creare un Asset e associarlo al Record:

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

A questo punto sono stati illustrati tutti gli oggetti all'interno di CloudKit fondamentali. I contenitori sono associati alle applicazioni e contengono database. I database contengono record che vengono raggruppati in Record zone e a cui fa riferimento a identificatori di Record. Relazioni padre-figlio sono definite tra i record di utilizzo di riferimenti. Infine, i file di grandi dimensioni possono essere caricati ed associati ai record di utilizzo delle risorse.

## <a name="cloudkit-convenience-api"></a>API conveniente CloudKit

Apple offre due diversi set di API per l'utilizzo di CloudKit:

-  **API OPERATIONAL** – offre ogni singola funzionalità di CloudKit. Per le applicazioni più complesse, tale API fornisce un controllo accurato CloudKit.
-  **API conveniente** : offre un sottoinsieme di comune e preconfigurato di funzionalità CloudKit. Offre una soluzione di accesso semplice e pratico per includere funzionalità CloudKit in un'applicazione iOS.


L'API di praticità è in genere la scelta migliore per la maggior parte delle applicazioni iOS e Apple suggerisce partire con esso. Il resto di questa sezione illustra gli argomenti di praticità API seguenti:

-  Salvataggio di un Record.
-  Recupero di un Record.
-  Aggiornamento di un Record.


### <a name="common-setup-code"></a>Codice di programma di installazione comune

Prima di iniziare con l'API di praticità CloudKit è codice standard di programma di installazione necessario. Iniziare modificando l'applicazione `AppDelegate.cs` file e renderlo simile al seguente:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using UIKit;
using CloudKit;

namespace CloudKitAtlas
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        #region Computed Properties
        public override UIWindow Window { get; set;}
        public CKDatabase PublicDatabase { get; set; }
        public CKDatabase PrivateDatabase { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            application.RegisterForRemoteNotifications ();

            // Get the default public and private databases for
            // the application
            PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
            PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;

            return true;
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            Console.WriteLine ("Registered for Push notifications with token: {0}", deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications (UIApplication application, NSError error)
        {
            Console.WriteLine ("Push subscription failed");
        }

        public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
        {
            Console.WriteLine ("Push received");
        }
        #endregion
    }
}
```

Il codice sopra riportato espone i database CloudKit pubblici e privati come tasti di scelta rapida per renderli più semplici da utilizzare con il resto dell'applicazione.

Successivamente, aggiungere il codice seguente per qualsiasi vista o di un contenitore di visualizzazione che verrà utilizzato CloudKit:

```csharp
using CloudKit;
...

#region Computed Properties
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Aggiunge un collegamento per ottenere il `AppDelegate` e i collegamenti pubblici e privati database creati in precedenza di accesso.

Con questo codice, esaminiamo un che implementa l'API di praticità CloudKit in un'applicazione iOS 8 Xamarin.

### <a name="saving-a-record"></a>Salvataggio di un Record

Usa il modello presentato in precedenza, quando si parla di record, il codice seguente verrà creare un nuovo record e usare l'API di praticità salvarlo nel database pubbliche:

```csharp
private const string ReferenceItemRecordName = "ReferenceItems";
...

// Create a new record
var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;

// Save it to the database
ThisApp.PublicDatabase.SaveRecord(newRecord, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

Tre aspetti da tenere presenti circa il codice sopra riportato:

1.  Chiamando il `SaveRecord` metodo il `PublicDatabase`, lo sviluppatore non è necessario specificare la modalità di invio dei dati, quali zona, questa viene scritta a e così via. L'API di praticità gestisca tutti i dettagli della stessa.
1.  La chiamata asincrona e fornisce una routine di callback al completamento della chiamata, con esito positivo o negativo. Se la chiamata non riesce, verrà fornito un messaggio di errore.
1.  CloudKit non fornisce la persistenza/archiviazione locale; è solo un supporto di trasferimento. Pertanto, quando viene effettuata una richiesta di salvare un Record, viene immediatamente inviato ai server iCloud.


> [!NOTE]
> A causa della natura "perdita di dati" delle comunicazioni di rete per dispositivi mobili, in cui le connessioni vengono continuamente rilasciate o interrotta una delle considerazioni prima lo sviluppatore deve fare quando l'utilizzo con CloudKit è la gestione degli errori.

### <a name="fetching-a-record"></a>Recupero di un Record

Per recuperare il record di un Record creato e archiviato correttamente nel server iCloud, utilizzare il codice seguente:

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {
        ...
    }
});
```

Analogamente il salvataggio del Record, il codice sopra riportato è asincrona, semplice e richiede la gestione degli errori.

### <a name="updating-a-record"></a>Aggiornamento di un Record

Dopo che un Record è stato recuperato dal server iCloud, il codice seguente consente di modificare il Record e salvare le modifiche al database:

```csharp
// Create a record ID and fetch the record from the database
var recordID = new CKRecordID("MyRecordName");
ThisApp.PublicDatabase.FetchRecord(recordID, (record, err) => {
    // Was there an error?
    if (err != null) {

    } else {
        // Modify the record
        record["name"] = (NSString)"New Name";

        // Save changes to database
        ThisApp.PublicDatabase.SaveRecord(record, (r, e) => {
            // Was there an error?
            if (e != null) {
                 ...
            }
        });
    }
});
```

Il `FetchRecord` metodo il `PublicDatabase` restituisce un `CKRecord` se la chiamata ha avuto esito positivo. Quindi, l'applicazione modifica il Record e chiama `SaveRecord` per scrivere le modifiche nel database.

In questa sezione è illustrato il tipico ciclo che verranno usate dall'applicazione quando si lavora con l'API di praticità CloudKit. L'applicazione verrà salvare i dati in iCloud, recuperare i record da iCloud, modificare i record e salvare tali modifiche in iCloud.

## <a name="designing-for-scalability"></a>Progettazione ai fini della scalabilità

Finora in questo articolo ha esaminato archiviazione e recupero di un intero modello a oggetti applicazione ai server iCloud, ogni volta che sta per essere gestiti. Questo approccio funziona bene con una piccola quantità di dati e un utente molto piccolo di base, non è adatta anche quando la quantità di informazioni e/o utente base aumenta.

### <a name="big-data-tiny-device"></a>Big Data, dispositivi di piccole dimensioni

Diventa il più popolare un'applicazione, più dati nel database di e minore fattibile è disporre di una cache di tale tutti i dati nel dispositivo. Per risolvere questo problema, è possono utilizzare le tecniche seguenti:

-  **Mantenere i dati di grandi dimensioni nel Cloud** – CloudKit è stato progettato per gestire in modo efficiente i dati di grandi dimensioni.
-  **Client deve visualizzare solo una sezione di dati** : ridurre il livello minimo di dati necessari per gestire qualsiasi attività in un determinato momento.
-  **Viste del client è possono modificare** : perché ogni utente dispone di preferenze diverse, la sezione di dati visualizzati modificabili dell'utente e del singoli utente visualizzazione di tutte le sezioni specificata può essere diverso.
-  **Client utilizza le query per concentrare l'attenzione del punto di vista** : le query consentono all'utente di visualizzare un piccolo subset di un set di dati più grande presente nell'ambito del Cloud.


### <a name="queries"></a>Query

Come descritto in precedenza, le query consentono allo sviluppatore di selezionare un sottoinsieme del set di dati più grande che esiste nel Cloud. Le query vengono esposte in CloudKit Framework tramite la `CKQuery` classe.

Una query combina tre operazioni diverse: un tipo di Record ( `RecordType`), un predicato ( `NSPredicate`) e, facoltativamente, un descrittore di ordinamento ( `NSSortDescriptors`). CloudKit supporta la maggior parte delle `NSPredicate`.

#### <a name="supported-predicates"></a>Predicati supportati

CloudKit supporta i seguenti tipi di `NSPredicates` quando si lavora con le query:


1. Dove il nome è uguale a un valore archiviato in una variabile di record corrispondenti:
    
    ```
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```
   
2. Consente l'individuazione delle corrispondenze essere impostata su un valore di chiave dinamico, in modo che la chiave non deve essere noto in fase di compilazione:
    
    ```
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```
    
3. Dove il valore del Record è maggiore del valore specificato di record corrispondenti:
   
    ```
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. Dove la posizione del Record è pari a 100 metri della posizione specificata all'interno di record corrispondenti:
    
    ```
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit supporta una ricerca in formato token. Questa chiamata creerà due token, uno per `after` e un altro per `session`. Verrà restituito un Record che contiene i due token:
    
    ```
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```
    
 6. Supporta CloudKit composta predicati uniti tramite il `AND` operatore.
    
    ```
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```
    


#### <a name="creating-queries"></a>Creazione di query

Il codice seguente consente di creare un `CKQuery` in un'applicazione iOS 8 Xamarin:

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

Viene innanzitutto creato un predicato per selezionare solo i record che corrispondano al nome specificato. Quindi crea una query per la selezione di record del tipo specificato di Record che corrispondono al predicato.

#### <a name="performing-a-query"></a>Esecuzione di una Query

Dopo aver creata una Query, utilizzare il codice seguente per eseguire la query ed elaborare i record restituiti:

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = {0}", recordName));
var query = new CKQuery("CloudRecords", predicate);

ThisApp.PublicDatabase.PerformQuery(query, CKRecordZone.DefaultRecordZone().ZoneId, (NSArray results, NSError err) => {
    // Was there an error?
    if (err != null) {
       ...
    } else {
        // Process the returned records
        for(nint i = 0; i < results.Count; ++i) {
            var record = (CKRecord)results[i];
        }
    }
});
```

Il codice sopra riportato accetta la query creata in precedenza e viene eseguito sul Database Public. Poiché non è specificato alcun fuso Record, tutte le zone di ricerca vengono eseguite. Se si è verificato alcun errore, una matrice di `CKRecords` verrà restituito corrispondenti ai parametri della query.

Il modo per considerare le query è che sono sondaggi e sono utili per il sezionamento tramite grandi set di dati. Le query, tuttavia, non sono particolarmente adatte per i set di dati di grandi dimensioni, principalmente statici per i motivi seguenti:

-  Sono non valido per la durata della batteria di dispositivi.
-  Sono non valido per il traffico di rete.
-  Sono corretto per l'esperienza utente, poiché le informazioni che vedono sono limitate dalla frequenza con cui l'applicazione sta eseguendo il polling del database. Gli utenti si aspettano oggi le notifiche push quando vengono apportate modifiche.


### <a name="subscriptions"></a>Sottoscrizioni

Quando si lavora con set di dati di grandi dimensioni, soprattutto statico, la query non deve essere eseguita sul dispositivo client, deve essere eseguito nel server per conto del client. La query deve essere eseguito in background e deve essere eseguita dopo ogni singolo record salvato, anche se per il dispositivo corrente o un altro dispositivo, toccare lo stesso database.

Infine, è necessario inviata una notifica di push per ogni dispositivo collegato al database quando viene eseguita la query sul lato server.

Le sottoscrizioni vengono esposte in CloudKit Framework tramite la `CKSubscription` classe. Combinano un tipo di Record ( `RecordType`), un predicato ( `NSPredicate`) e una notifica Push Apple ( `Push`).

> [!NOTE]
> In quanto contengono un payload contenente CloudKit informazioni specifiche, ad esempio ciò che ha determinato il push al verificarsi leggermente vengono aumentati in modo CloudKit push.

#### <a name="how-subscriptions-work"></a>Funzionamento delle sottoscrizioni

Prima di implementare la sottoscrizione nel codice c#, è opportuno una rapida panoramica del funzionamento delle sottoscrizioni:

 [![](intro-to-cloudkit-images/image39.png "Una panoramica del funzionamento delle sottoscrizioni")](intro-to-cloudkit-images/image39.png#lightbox)

Il grafico precedente illustra il processo di sottoscrizione tipica come indicato di seguito:

1.  Il dispositivo client crea una nuova sottoscrizione contenente il set di condizioni che attiveranno la sottoscrizione e una notifica Push che verrà inviato quando il trigger viene attivato.
2.  La sottoscrizione viene inviata al Database in cui viene aggiunto alla raccolta di sottoscrizioni esistenti.
3.  Un secondo dispositivo crea un nuovo Record e Salva il record nel Database.
4.  Esegue la ricerca del Database tramite il relativo elenco delle sottoscrizioni per vedere se il nuovo Record corrisponde a una delle relative condizioni.
5.  Se viene trovata una corrispondenza, la notifica Push viene inviata al dispositivo registrato la sottoscrizione con informazioni sul Record che ha causato l'attivazione.


Con queste informazioni sul posto, verrà ora esaminata la creazione di sottoscrizioni in un file di Xamarin iOS 8 applicazione.

#### <a name="creating-subscriptions"></a>Creazione di sottoscrizioni

Il codice seguente può essere utilizzato per creare una sottoscrizione:

```csharp
// Create a new subscription
DateTime date;
var predicate = NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date));
var subscription = new CKSubscription("RecordType", predicate, CKSubscriptionOptions.FiresOnRecordCreation);

// Describe the type of notification
var notificationInfo = new CKNotificationInfo();
notificationInfo.AlertLocalizationKey = "LOCAL_NOTIFICATION_KEY";
notificationInfo.SoundName = "ping.aiff";
notificationInfo.ShouldBadge = true;

// Attach the notification info to the subscription
subscription.NotificationInfo = notificationInfo;
```

Viene innanzitutto creato un predicato che fornisce la condizione per attivare la sottoscrizione. Successivamente, crea la sottoscrizione da un tipo specifico di Record e imposta l'opzione di quando è sottoposto a test il trigger. Infine, definisce il tipo di notifica che si verifica quando la sottoscrizione viene attivata e viene associato alla sottoscrizione.

### <a name="saving-subscriptions"></a>Salvataggio di sottoscrizioni

Con la sottoscrizione creata, il codice seguente verrà salvato nel database:

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

Tramite l'API di praticità, la chiamata è asincrona, semplice e offre la gestione degli errori semplice.

#### <a name="handling-push-notifications"></a>Gestione delle notifiche Push

Se lo sviluppatore è usato in precedenza le notifiche Push Apple (AP), il processo di gestione di notifiche generate da CloudKit necessario conoscere.

Nel `AppDelegate.cs`, eseguire l'override di `ReceivedRemoteNotification` classe come indicato di seguito:

```csharp
public override void ReceivedRemoteNotification (UIApplication application, NSDictionary userInfo)
{
    // Parse the notification into a CloudKit Notification
    var notification = CKNotification.FromRemoteNotificationDictionary (userInfo);

    // Get the body of the message
    var alertBody = notification.AlertBody;

    // Was this a query?
    if (notification.NotificationType == CKNotificationType.Query) {
        // Yes, convert to a query notification and get the record ID
        var query = notification as CKQueryNotification;
        var recordID = query.RecordId;
    }
}
```

Il codice sopra riportato richiede CloudKit per analizzare le informazioni utente in una notifica CloudKit. Successivamente, le informazioni vengono estratte sull'avviso. Infine, viene verificato il tipo di notifica e la notifica è gestirli di conseguenza.

In questa sezione è stato illustrato come rispondere di Big Data, piccoli problemi descritti in precedenza tramite query e le sottoscrizioni. L'applicazione verrà lasciare i dati di grandi dimensioni nel cloud e usare queste tecnologie per fornire visualizzazioni in questo set di dati.

## <a name="cloudkit-user-accounts"></a>Account utente CloudKit

Come indicato all'inizio di questo articolo, CloudKit si basa sull'infrastruttura esistente iCloud. Nella sezione seguente verrà illustrate, in modo dettagliato, modo in cui vengono esposti gli account per gli sviluppatori che utilizzano l'API CloudKit.

### <a name="authentication"></a>Autenticazione

Quando si gestiscono gli account utente, la prima considerazione è l'autenticazione. CloudKit supporta l'autenticazione tramite l'utente attualmente connesso in iCloud nel dispositivo. L'autenticazione viene eseguita in background e viene gestita da iOS. In questo modo, gli sviluppatori non devono mai preoccuparsi i dettagli di implementazione dell'autenticazione. Consentono di testare solo per vedere se un utente è connesso.

### <a name="user-account-information"></a>Informazioni sull'Account utente

CloudKit fornisce le informazioni utente di seguenti allo sviluppatore:

-  **Identità** : un modo per identificare in modo univoco l'utente.
-  **Metadati** : la possibilità di salvare e recuperare informazioni sugli utenti.
-  **Privacy** : tutte le informazioni viene gestite in un manor consapevole sulla privacy. Non è esposto a meno che non ha accettato l'utente.
-  **Individuazione** : consente agli utenti la possibilità di individuare gli amici che utilizzano la stessa applicazione.


Successivamente, verranno esaminati in dettaglio questi argomenti.

#### <a name="identity"></a>identità

Come descritto in precedenza, CloudKit fornisce un modo per l'applicazione identificare in modo univoco un determinato utente:

 [![](intro-to-cloudkit-images/image40.png "Che identifica in modo univoco un utente specifico")](intro-to-cloudkit-images/image40.png#lightbox)

È un'applicazione client in esecuzione su dispositivi dell'utente e tutti i database utente privato specifico all'interno del contenitore CloudKit. L'applicazione client deve essere collegata a uno di tali utenti specifici. Si basa sull'utente che viene registrato in iCloud localmente nel dispositivo.

Poiché questo proviene da iCloud, vi è potente il backup delle informazioni dell'archivio dell'utente. E poiché iCloud effettivamente ospita il contenitore, è possibile correlare gli utenti. Nella figura precedente, l'utente il cui account iCloud `user@icloud.com` è collegata al client corrente.

In modo da contenitore, un ID utente univoco generato in modo casuale viene creato e associato con l'account iCloud dell'utente (indirizzo di posta elettronica). L'ID utente viene restituito all'applicazione e può essere utilizzato in alcun modo che lo sviluppatore maniera appropriata.

> [!NOTE]
> Diverse applicazioni in esecuzione sullo stesso dispositivo per lo stesso utente iCloud avrà ID utente diversi perché sono connessi a contenitori CloudKit diversi.

Il seguente codice ottiene l'ID utente CloudKit per attualmente connesso in iCloud utente sul dispositivo:

```csharp
public CKRecordID UserID { get; set; }
...

// Get the CloudKit User ID
CKContainer.DefaultContainer.FetchUserRecordId ((recordID, err) => {
    // Was there an error?
    if (err!=null) {
        Console.WriteLine("Error: {0}", err.LocalizedDescription);
    } else {
        // Save user ID
        UserID = recordID;
    }
});
```

Il codice sopra riportato richiede il contenitore CloudKit per fornire l'ID dell'utente attualmente connesso. Poiché queste informazioni provenienti da iCloud Server, la chiamata è asincrona e la gestione degli errori è obbligatorio.

#### <a name="metadata"></a>Metadati

Ogni utente CloudKit dispone dei metadati specifici che li descrive. Questi metadati vengono rappresentati come un CloudKit Record:

 [![](intro-to-cloudkit-images/image41.png "Ogni utente in CloudKit dispone di metadati specifici che li descrive")](intro-to-cloudkit-images/image41.png#lightbox)

Cercare un utente specifico di un contenitore non esiste all'interno del Database privato è un Record che definisce tale utente. Esistono molti record utente all'interno del Database pubblico, una per ogni utente del contenitore. Uno di questi avrà un ID record corrispondente di ID Record dell'utente attualmente connesso

Record utente nel Database pubblico sono world leggibile. Che vengono trattati, per la maggior parte, come un normale Record e un tipo di `CKRecordTypeUserRecord`. Tali record sono riservati dal sistema e non sono disponibili per le query.

Utilizzare il codice seguente per accedere a un Record utente:

```csharp
public CKRecord UserRecord { get; set; }
...

// Get the user's record
PublicDatabase.FetchRecord(UserID, (record ,er) => {
    //was there an error?
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Save the user record
        UserRecord = record;
    }
});
```

Il codice sopra riportato richiede il Database Public per restituire il Record utente per l'utente che si accede di sopra di ID. Poiché queste informazioni provenienti da iCloud Server, la chiamata è asincrona e la gestione degli errori è obbligatorio.

#### <a name="privacy"></a>Privacy

CloudKit stato di progettazione, per impostazione predefinita, per proteggere la privacy dell'utente attualmente connesso. Per impostazione predefinita, non viene esposto informazioni personali sull'utente. Vi sono casi in cui l'applicazione richiederà informazioni limitate sull'utente.

In questi casi, l'applicazione può richiedere che l'utente divulgare le informazioni. Verrà visualizzata una finestra di dialogo all'utente che richiede per acconsentire esplicitamente a esporre le informazioni degli account.

#### <a name="discovery"></a>Individuazione

Supponendo che l'utente come acconsentito esplicitamente al consentendo all'applicazione limitato l'accesso alle informazioni di account utente, possono essere individuabili ad altri utenti dell'applicazione:

 [![](intro-to-cloudkit-images/image42.png "Un utente può essere individuabile ad altri utenti dell'applicazione")](intro-to-cloudkit-images/image42.png#lightbox)

L'applicazione client comunica con un contenitore e il contenitore comunica iCloud per accedere alle informazioni utente. L'utente può specificare un indirizzo di posta elettronica e di individuazione può essere utilizzata per ottenere informazioni nuovamente l'utente. Facoltativamente, l'ID utente nonché per individuare informazioni sull'utente.

CloudKit fornisce inoltre un modo per individuare informazioni su tutti gli utenti che potrebbero essere amici di attualmente connesso iCloud utente eseguendo una query nella Rubrica intero. Il processo CloudKit verrà pull nella Rubrica di contatto dell'utente e utilizzare gli indirizzi di posta elettronica per vedere se è possibile trovare altri utenti dell'applicazione che corrispondono agli indirizzi.

In questo modo l'applicazione sfruttare i libri di contatto dell'utente senza fornire l'accesso a esso o chiedere all'utente di approvare l'accesso ai contatti. In nessun momento le informazioni di contatto diventa disponibile per l'applicazione, solo il processo CloudKit ha accesso.

Per riassumere, sono disponibili tre diversi tipi di input per l'individuazione utente:

-  **ID di Record utente** -individuazione può essere eseguita in base all'ID utente di attualmente connesso CloudKit utente.
-  **Indirizzo di posta elettronica utente** : l'utente può specificare un indirizzo di posta elettronica e può essere utilizzato per l'individuazione.
-  **Contattare il libro** : Rubrica dell'utente può essere utilizzata per individuare gli utenti dell'applicazione che hanno lo stesso indirizzo di posta elettronica, come elencato nella loro contatti.


Individuazione utente restituirà le informazioni seguenti:

-  **ID di Record utente** -l'ID univoco di un utente di Database Public.
-  **Primo e il cognome** - archiviata nel Database Public.


Questa informazione verrà restituita solo per gli utenti che hanno scelto in individuazione.

Il codice seguente verrà individuare informazioni sull'utente attualmente connesso in iCloud nel dispositivo:

```csharp
public CKDiscoveredUserInfo UserInfo { get; set; }
...

// Get the user's metadata
CKContainer.DefaultContainer.DiscoverUserInfo(UserID, (info, e) => {
    // Was there an error?
    if (e != null) {
        Console.WriteLine("Error: {0}", e.LocalizedDescription);
    } else {
        // Save the user info
        UserInfo = info;
    }
});
```

Utilizzare il codice seguente per eseguire query di tutti gli utenti nel libro contatto:

```csharp
// Ask CloudKit for all of the user's friends information
CKContainer.DefaultContainer.DiscoverAllContactUserInfos((info, er) => {
    // Was there an error
    if (er != null) {
        Console.WriteLine("Error: {0}", er.LocalizedDescription);
    } else {
        // Process all returned records
        for(int i = 0; i < info.Count(); ++i) {
            // Grab a user
            var userInfo = info[i];
        }
    }
});
```

In questa sezione sono stati illustrati quattro aree principali dell'accesso a un account utente in grado di fornire CloudKit a un'applicazione. Da cui ottenere l'identità dell'utente e i metadati, i criteri di Privacy che sono integrate in CloudKit e infine la possibilità di individuare altri utenti dell'applicazione.

## <a name="the-development-and-production-environments"></a>Lo sviluppo e ambienti di produzione

CloudKit fornisce ambienti di sviluppo e produzione distinti per i tipi di record e i dati di un'applicazione. L'ambiente di sviluppo è un ambiente più flessibile che è disponibile solo per i membri del team di sviluppo. Quando un'applicazione aggiunge un nuovo campo a un record e si salva tale record nell'ambiente di sviluppo, il server Aggiorna automaticamente le informazioni sullo schema.

Lo sviluppatore può utilizzare questa funzionalità per apportare modifiche a uno schema durante lo sviluppo, che consente di risparmiare tempo. Uno svantaggio è che dopo l'aggiunta di un campo a un record, il tipo di dati associato al campo non venga modificato a livello di codice. Per modificare il tipo del campo, lo sviluppatore deve eliminare il campo in [CloudKit Dashboard](https://icloud.developer.apple.com/dashboard/) e aggiungerlo di nuovo con il nuovo tipo.

Prima di distribuire l'applicazione, lo sviluppatore può eseguire la migrazione ai dati e dello schema per l'ambiente di produzione tramite **CloudKit Dashboard**. Durante l'esecuzione per l'ambiente di produzione, il server impedisce un'applicazione modifica dello schema a livello di codice. Lo sviluppatore può comunque apportano modifiche con **CloudKit Dashboard** , ma tenta di aggiungere campi a un record nel risultato errori in ambiente di produzione.

> [!NOTE]
> IOS simulatore funziona solo con il **ambiente di sviluppo**. Quando lo sviluppatore è pronto per testare un'applicazione in un **ambiente di produzione**, è necessario un dispositivo dei / o fisici.


## <a name="shipping-a-cloudkit-enabled-app"></a>App abilitata per la spedizione di un CloudKit

Prima del rilascio di un'applicazione che utilizza CloudKit, sarà necessario configurarle di destinazione di **ambiente di produzione CloudKit** o l'applicazione verrà rifiutato da Apple.

Seguire questa procedura:

1. In Visual Studio per l'agente di gestione, compilare l'applicazione per **versione** > **dispositivo iOS**: 

    [![](intro-to-cloudkit-images/shipping01.png "Compilare l'applicazione per versione")](intro-to-cloudkit-images/shipping01.png#lightbox)

2. Dal **compilare** dal menu **archivio**: 

    [![](intro-to-cloudkit-images/shipping02.png "Selezionare l'archivio")](intro-to-cloudkit-images/shipping02.png#lightbox)

3. Il **archivio** verrà creato e visualizzato in Visual Studio per Mac: 

    [![](intro-to-cloudkit-images/shipping03.png "L'archivio verrà creato e visualizzato")](intro-to-cloudkit-images/shipping03.png#lightbox)

4. Avviare **Xcode**.
5. Dal **finestra** dal menu **libreria**: 

    [![](intro-to-cloudkit-images/shipping04.png "Selezionare una libreria")](intro-to-cloudkit-images/shipping04.png#lightbox)

6. Selezionare l'archivio dell'applicazione e scegliere il **Esporta...**  pulsante: 

    [![](intro-to-cloudkit-images/shipping05.png "Archivio dell'applicazione")](intro-to-cloudkit-images/shipping05.png#lightbox)
    
7. Selezionare un metodo per l'esportazione e scegliere il **Avanti** pulsante: 

    [![](intro-to-cloudkit-images/shipping06.png "Selezionare un metodo per l'esportazione")](intro-to-cloudkit-images/shipping06.png#lightbox)

8. Selezionare il **Team di sviluppo** dall'elenco a discesa scegliere il **scegliere** pulsante: 

    [![](intro-to-cloudkit-images/shipping07.png "Selezionare il Team di sviluppo nell'elenco a discesa")](intro-to-cloudkit-images/shipping07.png#lightbox)

9. Selezionare **produzione** dall'elenco a discesa scegliere il **Avanti** pulsante: 

    [![](intro-to-cloudkit-images/shipping08.png "Selezionare nell'elenco a discesa di produzione")](intro-to-cloudkit-images/shipping08.png#lightbox)

10. Esaminare le impostazioni e fare clic su di **esportare** pulsante: 

    [![](intro-to-cloudkit-images/shipping09.png "Esaminare l'impostazione")](intro-to-cloudkit-images/shipping09.png#lightbox)

11. Scegliere un percorso per generare l'applicazione risulta `.ipa` file.

Il processo è simile per inviare l'applicazione direttamente in iTunes Connect, è sufficiente scegliere il **Invia...**  pulsante anziché l'esportazione... dopo la selezione di un archivio nella finestra della libreria.

## <a name="when-to-use-cloudkit"></a>Quando utilizzare CloudKit

Come abbiamo visto in questo articolo, CloudKit fornisce un modo semplice per un'applicazione archiviare e recuperare informazioni dai server iCloud. Detto questo, CloudKit non obsoleto o deprecare le Framework o strumenti esistenti.

### <a name="use-cases"></a>Casi d'uso

I seguenti casi d'uso deve consentono allo sviluppatore di decidere quando utilizzare una tecnologia o un framework specifico iCloud:

-  **Archivio chiave-valore iCloud** : mantiene aggiornate piccola quantità di dati in modo asincrono ed è ideale per l'utilizzo di preferenze per l'applicazione. Tuttavia, è vincolato per una piccola quantità di informazioni.
-  **Unità iCloud** : basato su iCloud esistente le API di documenti e fornisce una semplice API per sincronizzare i dati non strutturati dal file system. Fornisce una cache non in linea completa su Mac OS X ed è ideale per applicazioni incentrate sui documenti.
-  **Dati principali iCloud** : consente di essere replicati tra tutti i dispositivi dell'utente i dati. I dati sono ideali per mantenere privato dati strutturati sincronizzati e utente singolo.
-  **CloudKit** : fornisce entrambi una struttura di dati pubblici e di massa e sia in grado di gestione di grandi set di dati e file non strutturati di grandi dimensioni. Il relativo legati all'utente dell'account iCloud e fornisce client indirizzato il trasferimento dei dati.


Mantenere questi casi di utilizzo presente, lo sviluppatore deve scegliere la tecnologia iCloud corretto per fornire la funzionalità applicazione richiesta corrente e fornire una maggiore scalabilità per la crescita futura.

## <a name="summary"></a>Riepilogo

In questo articolo è descritta una rapida introduzione all'API CloudKit. È stato illustrato come eseguire il provisioning e configurare un'applicazione iOS Xamarin utilizzare CloudKit. Le funzionalità dell'API di praticità CloudKit sia nascosto. Ha Mostra come progettare un CloudKit abilitato applicazione per la scalabilità utilizzando query e le sottoscrizioni. E infine ha dimostrato, le informazioni dell'Account utente che viene esposto a un'applicazione da CloudKit.

## <a name="related-links"></a>Collegamenti correlati

- [CloudKitAtlas (esempio)](https://developer.xamarin.com/samples/monotouch/ios8/CloudKitAtlas/)
- [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Creazione di un profilo di provisioning](~/ios/get-started/installation/device-provisioning/index.md)
