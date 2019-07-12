---
title: CloudKit in xamarin. IOS
description: Questo documento descrive come usare CloudKit in xamarin. IOS. Fornisce una panoramica di CloudKit e viene descritto come abilitare lo, CloudKit comodità API, scalabilità, gli account utente e ambienti di sviluppo e produzione.
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/11/2016
ms.openlocfilehash: 8ef12c8b0822f3d0486f584878f572a266b0d44e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831872"
---
# <a name="cloudkit-in-xamarinios"></a>CloudKit in xamarin. IOS

Il framework CloudKit semplifica lo sviluppo di applicazioni iCloud tale accesso. Sono inclusi il recupero dei dati dell'applicazione e i diritti di asset, nonché la possibilità di archiviare in modo sicuro le informazioni sull'applicazione. Questo kit fornisce agli utenti un livello di anonimato consentendo l'accesso alle applicazioni con relativi ID iCloud senza condivisione delle informazioni personali.

Gli sviluppatori possono concentrare l'attenzione sulle applicazioni client-side e consentire a iCloud eliminano la necessità di scrivere logica dell'applicazione lato server. CloudKit fornisce autenticazione, i database privati e pubblici e dati strutturati e i servizi archiviazione di risorse.

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

## <a name="requirements"></a>Requisiti

Di seguito è necessario per completare la procedura descritta in questo articolo:

-  **Xcode e iOS SDK** – Apple Xcode e iOS 8 API devono essere installati e configurati nel computer dello sviluppatore.
-  **Visual Studio per Mac** : la versione più recente di Visual Studio per Mac deve essere installata e configurata sul dispositivo dell'utente.
-  **iOS 8 dispositivo** : un dispositivo iOS in esecuzione la versione più recente di iOS 8 per i test.

## <a name="what-is-cloudkit"></a>Che cos'è CloudKit?

CloudKit è un modo per fornire l'accesso per gli sviluppatori su iCloud i server. Fornisce le basi per iCloud Drive sia libreria foto di iCloud. CloudKit è supportato nei dispositivi iOS sia Mac OS X e Apple.

 [![](intro-to-cloudkit-images/image1.png "Modalità di supporto di CloudKit su Mac OS X sia i dispositivi iOS di Apple")](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit Usa l'infrastruttura di Account iCloud. Se è presente un utente registrato in un Account nel dispositivo iCloud, CloudKit userà il relativo ID per identificare l'utente. Se non è disponibile alcun account, verrà fornito l'accesso di sola lettura limitato.

CloudKit supporta sia il concetto di database pubblici e privati. Pubblici database offrono un "completo" di tutti i dati che l'utente può accedere a. Database privati sono pensati per archiviare i dati privati associati a un utente specifico.

CloudKit supporta dati sia strutturati e in blocco. È in grado di gestire i trasferimenti di file di grandi dimensioni senza problemi. CloudKit automaticamente in modo efficiente il trasferimento dei file di grandi dimensioni da e verso i server iCloud in background, liberando allo sviluppatore di concentrarsi su altre attività.

> [!NOTE]
> È importante notare che CloudKit è un _trasporto tecnologia_. Non fornisce persistenza; consente solo un'applicazione inviare e ricevere informazioni dai server in modo efficiente.

Al momento della stesura di questo documento, Apple inizialmente fornisce CloudKit gratuitamente con un limite massimo per la capacità di larghezza di banda e archiviazione. Per i progetti o le applicazioni con una base utente di grandi dimensioni grandi dimensioni, Apple ha JSP che verrà fornita una scala di prezzo conveniente.


## <a name="enabling-cloudkit-in-a-xamarin-application"></a>Abilitazione CloudKit in un'applicazione Xamarin

Prima di un'applicazione Xamarin possa usare il CloudKit Framework, è necessario eseguirne correttamente l'applicazione come descritto in dettaglio nel [uso delle funzionalità](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) e [uso degli Entitlement](~/ios/deploy-test/provisioning/entitlements.md) Guide

1.  Aprire il progetto in Visual Studio per Mac o Visual Studio.
2.  Nel **Esplora soluzioni**, aprire il **Info. plist** del file e verificare che il **identificatore del Bundle** corrisponda a quello che è stata definita in **ID App**creato come parte del provisioning imposta:
 
    [![](intro-to-cloudkit-images/image26a.png "Immettere l'identificatore del Bundle")](intro-to-cloudkit-images/image26a-orig.png#lightbox "Info.plist file displaying Bundle Identifier")

3.  Scorrere fino alla parte inferiore della **Info. plist** del file e selezionare **modalità in Background abilitata**, **aggiornamenti della posizione** e **notifiche Remote**:

    [![](intro-to-cloudkit-images/image27a.png "Selezionare le modalità in Background abilitata, aggiornamenti della posizione e le notifiche Remote")](intro-to-cloudkit-images/image27a-orig.png#lightbox "Info.plist file displaying background modes")
4.  Fare clic sul progetto iOS nella soluzione e selezionare **opzioni**.
5.  Selezionare **firma del Bundle iOS**, selezionare la **identità Developer** e **profilo di Provisioning** creato in precedenza.
6.  Verificare che il **entitlements. plist** include **Abilita iCloud** , **archiviazione chiave-valore** e **CloudKit** .
7.  Verificare che il **ubiquità contenitore** esiste per l'applicazione (come creato in precedenza). Esempio: `iCloud.com.your-company.CloudKitAtlas`
8.  Salvare le modifiche apportate al file.


Con queste impostazioni posto, l'applicazione è ora pronta per accedere alle API di Framework CloudKit.

## <a name="cloudkit-api-overview"></a>Panoramica dell'API CloudKit

Prima di implementare CloudKit in un'applicazione per Xamarin iOS, in questo articolo si intende coprire le nozioni di base del CloudKit Framework, che include gli argomenti seguenti:

1.  **I contenitori** – Isolated silo di comunicazioni iCloud.
2.  **Database** – Public e private sono disponibili per l'applicazione.
3.  **I record** – il meccanismo in cui i dati strutturati vengono spostati da e verso CloudKit.
4.  **Le zone record** – sono gruppi di record.
5.  **Gli identificatori di record** : completamente vengono normalizzate e rappresentano il percorso specifico del record.
6.  **Riferimento** – forniscono le relazioni tra record correlati all'interno di un Database padre-figlio.
7.  **Gli asset** -attendere un file di dati di grandi dimensioni, non strutturati da caricare in iCloud e associata a un determinato Record.


### <a name="containers"></a>Contenitori

Una determinata applicazione in esecuzione in un dispositivo iOS è sempre in esecuzione insieme interno di altre applicazioni e servizi in tale dispositivo. Nel dispositivo client, l'applicazione deve essere creata mediante sandbox in qualche modo o silo. In alcuni casi, si tratta di una sandbox di valore letterale e in altri casi, l'applicazione è semplicemente in esecuzione in esso è lo spazio di memoria propria.

Il concetto di eseguire un'applicazione client e l'esecuzione separati da altri client è molto potente e offre i vantaggi seguenti:

1.  **Sicurezza** : un'applicazione non può interferire con altre App client o del sistema operativo stesso.
1.  **Stabilità** : se l'arresto anomalo dell'applicazione client non è possibile estrarre le altre App del sistema operativo.
1.  **Privacy** – ogni applicazione client ha limitato l'accesso ai dati personali archiviati all'interno del dispositivo.


CloudKit è stato progettato per fornire gli stessi vantaggi, come indicato sopra e applicarli all'utilizzo di informazioni basato sul cloud:

 [![](intro-to-cloudkit-images/image31.png "Le app CloudKit comunicare con i contenitori")](intro-to-cloudkit-images/image31.png#lightbox)

Proprio come l'applicazione è in esecuzione nel dispositivo, uno dei molti è così le comunicazioni dell'applicazione con uno dei molti iCloud. Ognuna di queste barriere della comunicazione diverse vengono chiamati i contenitori.

I contenitori sono esposte in CloudKit Framework tramite il `CKContainer` classe. Per impostazione predefinita, un contenitore e questo contenitore comunica con una sola applicazione segrega i dati per l'applicazione. Ciò significa che molte applicazioni possono essere l'archiviazione delle informazioni allo stesso account iCloud, ma queste informazioni non verranno mai coesistere.

L'utilizzo dei contenitori di dati iCloud consente inoltre di CloudKit incapsulare le informazioni utente. In questo modo, l'applicazione avrà alcuni accesso limitato all'account iCloud e le informazioni dell'utente archiviati all'interno, tutto continuando comunque a proteggere la privacy e sicurezza dell'utente.

I contenitori sono completamente gestiti dallo sviluppatore dell'applicazione tramite il portale WWDR. Lo spazio dei nomi del contenitore è globale tra tutti gli sviluppatori di Apple, in modo che il contenitore non deve solo essere univoco per le applicazioni dello sviluppatore specificato, ma per tutte le applicazioni e agli sviluppatori di Apple.

Suggerite da Apple usando la notazione DNS inversa quando si crea lo spazio dei nomi per i contenitori di applicazioni. Esempio:

```csharp
iCloud.com.company-name.application-name
```

Mentre i contenitori sono, per impostazione predefinita, associata uno una determinata applicazione, possono essere condivisi tra le applicazioni. In modo che consente di coordinare più applicazioni in un singolo contenitore. Una singola applicazione può anche comunicare con più contenitori.

### <a name="databases"></a>Database

Una delle funzioni principali di CloudKit deve eseguire il modello di dati di un'applicazione e la replica tale modello fino a server iCloud. Alcune informazioni sono destinate per l'utente che lo ha creato, altre informazioni sono dati pubblici che è stati creati da un utente per uso pubblico (ad esempio, una revisione del ristorante) o può trattarsi di informazioni che lo sviluppatore ha pubblicato per l'applicazione. In entrambi i casi, il destinatario non è solo un singolo utente, ma è una community di utenti.

 [![](intro-to-cloudkit-images/image32.png "Diagramma del contenitore CloudKit")](intro-to-cloudkit-images/image32.png#lightbox)

All'interno di un contenitore, prima di tutto è il database pubblico. Si tratta in cui tutte le informazioni pubbliche vive e CO-mingles. Inoltre, esistono numerosi singoli database privati per ogni utente dell'applicazione.

Durante l'esecuzione in un dispositivo iOS, l'applicazione solo avrà accesso alle informazioni dell'utente attualmente connesso iCloud. Pertanto, la visualizzazione dell'applicazione del contenitore sarà come segue:

 [![](intro-to-cloudkit-images/image33.png "La visualizzazione delle applicazioni del contenitore")](intro-to-cloudkit-images/image33.png#lightbox)

È possibile visualizzare solo il database pubblico e privato database associato all'utente attualmente connesso iCloud.

I database vengono esposte in CloudKit Framework tramite il `CKDatabase` classe. Ogni applicazione può accedere a due database: il database pubblico e quella privata.

Il contenitore è il punto di ingresso iniziale in CloudKit. Il codice seguente può essere utilizzato per accedere al database pubblico e privato dal contenitore predefinito dell'applicazione:

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

||Pubblica Database|Database privato|
|---|--- |--- |
|**Tipo di dati**|Dati condivisi|Dati dell'utente corrente|
|**Quota**|Conteggiato nella Quota per gli sviluppatori|Conteggiato nella Quota dell'utente|
|**Autorizzazioni predefinite**|World leggibile|Utente leggibile|
|**Modifica autorizzazioni**|iCloud ruoli del Dashboard tramite un livello di classe di record|N/D|

### <a name="records"></a>Record

I contenitori di contengono i database e all'interno di database sono i record. I record sono il meccanismo in cui i dati strutturati vengono spostati da e verso CloudKit:

 [![](intro-to-cloudkit-images/image34.png "I contenitori di contengono i database e all'interno di database sono record")](intro-to-cloudkit-images/image34.png#lightbox)

I record vengono esposte in CloudKit Framework tramite il `CKRecord` classe, che esegue il wrapping di coppie chiave-valore. Equivale a un'istanza di un oggetto in un'applicazione un `CKRecord` in CloudKit. Inoltre, ogni `CKRecord` possiede un tipo di record, che equivale alla classe di un oggetto.

I record con uno schema di just-in-time, in modo che i dati viene descritto a CloudKit prima viene passato per l'elaborazione. Da quel punto, CloudKit verrà interpretare le informazioni e gestire la logistica per archiviare e recuperare il record.

Il `CKRecord` classe supporta anche un'ampia gamma di metadati. Ad esempio, un record contiene informazioni su quando è stato creato e l'utente che l'ha creata. Un record contiene anche informazioni su quando l'ultima modifica e l'utente che è stato modificato.

I record contengono la nozione di un Tag di modifica. Si tratta di una versione precedente di una revisione di un determinato record. Il Tag di modifica viene utilizzato come un modo più semplice per determinare se il client e server hanno la stessa versione di un determinato record.

Come indicato in precedenza, `CKRecords` eseguire il wrapping di coppie chiave-valore e di conseguenza, i tipi di dati seguenti possono essere archiviati in un record:

1.   `NSString`
1.   `NSNumber`
1.   `NSData`
1.   `NSDate`
1.   `CLLocation`
1.   `CKReferences`
1.   `CKAssets`


Oltre ai tipi di valore singolo, un record può contenere una matrice omogenea di uno dei tipi elencati sopra.

Il codice seguente è utilizzabile per creare un nuovo record e archiviarlo in un database:

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

Non esistono record di per sé all'interno di un database: gruppi di record esistono tra loro all'interno di una zona di Record. Zone record possono essere considerate come tabelle in un database relazionali tradizionali:

 [![](intro-to-cloudkit-images/image35.png "Gruppi di record esistono tra loro all'interno di una zona di Record")](intro-to-cloudkit-images/image35.png#lightbox)

Possono esistere più record all'interno di una determinata zona di Record e più zone di Record all'interno di un database. Ogni database contiene una zona di Record predefinite:

 [![](intro-to-cloudkit-images/image36.png "Ogni database contiene una zona di Record di predefinito e l'area personalizzata")](intro-to-cloudkit-images/image36.png#lightbox)

Si tratta in cui i record vengono archiviati per impostazione predefinita. Inoltre, è possibile creare le zone di Record personalizzato. Rappresentano le zone di record viene eseguita la granularità di base in cui rilevamento modifiche e commit atomici.

## <a name="record-identifiers"></a>Identificatori di record

Identificatori di record vengono rappresentati come una tupla, che contiene sia un client fornito il nome di Record e l'area in cui è presente il record. Gli identificatori di record hanno le caratteristiche seguenti:

-  Vengono create dall'applicazione client.
-  Si sono normalizzati completamente e rappresenta il percorso specifico del record.
-  Assegnando l'ID univoco di un record in un database esterno per il nome del record, possono essere utilizzati per collegare i database locali che non vengono archiviati all'interno di CloudKit.


Quando gli sviluppatori di creano nuovi record, è possibile passare un identificatore di Record. Se non viene specificato un identificatore di Record, un valore UUID verrà automaticamente creato e assegnato al record.

Quando gli sviluppatori creano nuovi identificatori di Record, è possibile specificare la zona di Record che appartengono a ogni record. Se non viene specificato, verrà utilizzato il fuso Record predefinito.

Gli identificatori di record vengono esposte in CloudKit Framework tramite il `CKRecordID` classe. Il codice seguente è utilizzabile per creare un nuovo identificatore di Record:

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>Riferimenti

Riferimenti forniscono le relazioni tra record correlati all'interno di un Database:

 [![](intro-to-cloudkit-images/image37.png "Riferimenti forniscono le relazioni tra record correlati all'interno di un Database")](intro-to-cloudkit-images/image37.png#lightbox)

Nell'esempio precedente, gli elementi padre proprietario di elementi figlio in modo che l'elemento figlio è un record figlio del record padre. La relazione va dal record figlio al record padre e fa riferimento come un *nuovamente riferimento*.

I riferimenti vengono esposte in CloudKit Framework tramite il `CKReference` classe. Sono un modo per consentire al server di iCloud di comprendere la relazione tra record.

Riferimenti forniscono il meccanismo alla base di eliminazioni a cascata. Se un record padre viene eliminato dal database, qualsiasi record figlio (come specificato in una relazione) verranno automaticamente eliminati dal database anche.

> [!NOTE]
> I puntatori inesatti sono una possibilità quando si usa CloudKit. Ad esempio, una volta l'applicazione ha recuperato un elenco di puntatori di record, la selezione di un record e quindi richiesto per il record, il record non esiste più nel database. Un'applicazione deve essere codificata per gestire questa situazione normale.

Sebbene non obbligatorio, nuovamente i riferimenti sono preferiti quando si lavora con il CloudKit Framework. Apple ha ottimizzato il sistema per rendere questo tipo di riferimento più efficiente.

Quando si crea un riferimento, lo sviluppatore può specificare un record che è già in memoria o creare un riferimento a un identificatore di Record. Se tramite un identificatore del Record e il riferimento specificato non esiste nel database, viene creato un puntatore (dangling).

Di seguito è riportato un esempio di creazione di un riferimento con un Record noto:

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>Asset

Gli asset consentono di un file di dati di grandi dimensioni, non strutturati da caricare in iCloud e associata a un determinato Record:

 [![](intro-to-cloudkit-images/image38.png "Gli asset consentano per un file di dati di grandi dimensioni, non strutturati da caricare in iCloud e associata a un determinato Record")](intro-to-cloudkit-images/image38.png#lightbox)

Sul client, un `CKRecord` creato che descrive il file che sta per essere caricato nel server iCloud. Oggetto `CKAsset` creato per contenere il file e viene collegato al record che descrive.

Quando il file viene caricato nel server, il record viene inserito nel database e il file viene copiato in un database di archiviazione di massa speciale. Viene creato un collegamento tra il puntatore di record e il file caricato.

Gli asset vengono esposte in CloudKit Framework tramite il `CKAsset` classe e vengono usati per archiviare i dati di grandi dimensioni, non strutturati. Poiché lo sviluppatore non desidera mai presenti dati non strutturati e di grandi dimensioni in memoria, gli asset vengono implementati mediante i file su disco.

Gli asset di proprietà di record, che consente l'asset da recuperare dalla iCloud tramite il record come indicatore di misura. In questo modo il server può Garbage raccogliere asset quando viene eliminato il record che è proprietario dell'Asset.

Poiché `CKAssets` devono gestire i file di dati di grandi dimensioni, Apple progettata CloudKit caricare e scaricare gli asset in modo efficiente.

Il codice seguente è utilizzabile per creare un Asset e associarlo al Record:

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

A questo punto sono stati trattati tutti gli oggetti all'interno di CloudKit fondamentali. I contenitori sono associati alle applicazioni e contengono database. I database contengono record che vengono raggruppati in Record zone e a cui puntano gli identificatori di Record. Relazioni padre-figlio sono definite tra i record di utilizzo di riferimenti. Infine, i file di grandi dimensioni possono essere caricati ed associati ai record di uso degli asset.

## <a name="cloudkit-convenience-api"></a>API conveniente CloudKit

Apple offre due diversi set di API per l'uso di CloudKit:

-  **API OPERATIONAL** – offre ogni singola funzionalità di CloudKit. Per le applicazioni più complesse, questo API fornisce un controllo accurato CloudKit.
-  **API conveniente** : offre un subset di funzionalità CloudKit comuni, configurati in precedenza. Fornisce una soluzione di accesso semplice e pratico per includere funzionalità CloudKit in un'applicazione iOS.


L'API di praticità è in genere la scelta migliore per la maggior parte delle applicazioni iOS e avvio con esso suggerite da Apple. La parte restante di questa sezione verrà trattati i seguenti argomenti di praticità API:

-  Salvataggio di un Record.
-  Durante il recupero di un Record.
-  Aggiornamento di un Record.


### <a name="common-setup-code"></a>Codice di configurazione comune

Prima di iniziare con l'API di praticità CloudKit, è disponibile del codice di installazione standard necessario. Iniziare modificando l'applicazione `AppDelegate.cs` file e renderlo simile al seguente:

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

Il codice sopra riportato espone i database di CloudKit pubblici e privati come tasti di scelta rapida per renderli più facile lavorare con il resto dell'applicazione.

Per le viste e un contenitore di visualizzazione che useranno CloudKit successivamente, aggiungere il codice seguente:

```csharp
using CloudKit;
...

#region Computed Properties
public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
#endregion
```

Ciò consente di aggiungere un collegamento per ottenere il `AppDelegate` e accedere ai collegamenti di database pubbliche e private creati in precedenza.

Con questo codice, diamo un'occhiata a implementa l'API di praticità CloudKit in un'applicazione per Xamarin iOS 8.

### <a name="saving-a-record"></a>Salvataggio di un Record

Usando il modello presentato in precedenza, quando si parla di record, il codice seguente verrà crea un nuovo record e usare l'API di praticità per salvarlo nel database pubblico:

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

Tre aspetti da considerare nel codice precedente:

1.  Chiamando il `SaveRecord` metodo di `PublicDatabase`, lo sviluppatore non deve specificare la modalità di invio dei dati, da quale area viene scritto e così via. L'API di praticità gestisca tutti questi dettagli di se stesso.
1.  La chiamata è asincrona e fornisce una routine di callback al completamento della chiamata, con esito positivo o negativo. Se la chiamata non riesce, verrà fornito un messaggio di errore.
1.  CloudKit non fornisce archiviazione locale/persistenza; è solo un supporto di trasferimento. Pertanto, quando viene effettuata una richiesta per salvare un Record, viene immediatamente inviata ai server iCloud.


> [!NOTE]
> A causa della natura "perdita di dati" di comunicazioni di rete per dispositivi mobili, in cui le connessioni vengono costantemente eliminate o interrotto, una delle considerazioni prima lo sviluppatore deve apportare quando l'utilizzo di CloudKit è la gestione degli errori.

### <a name="fetching-a-record"></a>Il recupero di un Record

Con un Record creato e archiviato correttamente nel server di iCloud, usare il codice seguente per recuperare il record:

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

Analogamente il salvataggio del Record, il codice sopra riportato è asincrona, semplice e richiede la gestione degli errori eccezionali.

### <a name="updating-a-record"></a>Aggiornamento di un Record

Dopo che un Record è stato recuperato dai server iCloud, il codice seguente è utilizzabile per modificare il Record e salvare le modifiche nel database:

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

Il `FetchRecord` metodo per il `PublicDatabase` restituisce un `CKRecord` se la chiamata ha avuto esito positivo. Quindi l'applicazione modifica il Record e le chiamate `SaveRecord` nuovamente da scrivere nuovamente le modifiche al database.

In questa sezione ha illustrato il tipico ciclo che verranno usate dall'applicazione quando si lavora con l'API di praticità CloudKit. L'applicazione verrà salvare i dati in iCloud, recuperare i record da iCloud, modificare i record e salvare tali modifiche in iCloud.

## <a name="designing-for-scalability"></a>Progettazione per la scalabilità

Finora in questo articolo ha esaminato l'archiviazione e recupero di un intero modello a oggetti applicazione dai server iCloud, ogni volta che sta per essere gestiti. Sebbene questo approccio funziona bene con una piccola quantità di dati e una base utenti molto piccola, non è adatta anche quando la quantità di informazioni e/o utente basare aumenta.

### <a name="big-data-tiny-device"></a>I big data, dispositivi di piccole dimensioni

Diventa più popolari un'applicazione, i dati nel database di più e meno fattibile deve disporre di una cache di tale tutti i dati nel dispositivo. Per risolvere questo problema, è possono utilizzare le tecniche seguenti:

-  **Mantenere i dati di grandi dimensioni nel Cloud** – CloudKit è stato progettato per gestire in modo efficiente grandi quantità di dati.
-  **Client deve visualizzare solo una sezione di quei dati** – causi l'arresto viene ridotta al minimo dei dati necessari per gestire tutte le attività in un determinato momento.
-  **Possono modificare visualizzazioni client** – perché ogni utente dispone di differenti preferenze, la sezione di dati visualizzati possa modificare da utente a utente e dell'utente singoli visualizzazione di tutte le sezioni specificata può essere diverso.
-  **Client usa le query per concentrare l'attenzione del punto di vista** – query consentono all'utente visualizzare un piccolo subset di un set di dati più grande presente nell'ambito del Cloud.


### <a name="queries"></a>Query

Come indicato in precedenza, le query consentono allo sviluppatore di selezionare un piccolo subset del set di dati più grande presente nel Cloud. Le query vengono esposte in CloudKit Framework tramite il `CKQuery` classe.

Una query combina tre aspetti: un tipo di Record ( `RecordType`), un predicato ( `NSPredicate`) e, facoltativamente, un descrittore di ordinamento ( `NSSortDescriptors`). CloudKit supporta la maggior parte delle `NSPredicate`.

#### <a name="supported-predicates"></a>Predicati supportati

CloudKit supporta i tipi seguenti di `NSPredicates` quando si lavora con le query:


1. Corrispondenza record in cui il nome è uguale a un valore archiviato in una variabile:
    
    ```
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```
   
2. Consente a ricerca per basarsi su un valore di chiave dinamico, in modo che la chiave non deve essere noto in fase di compilazione:
    
    ```
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```
    
3. Corrispondenza record in cui il valore del Record è superiore al valore specificato:
   
    ```
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. Dove la posizione del Record è pari a 100 metri della posizione specificata all'interno di record corrispondenti:
    
    ```
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit supporta una ricerca in formato token. Questa chiamata creerà due token, uno per `after` e l'altro per `session`. Verrà restituito un Record che contiene i due token:
    
    ```
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```
    
6. Supporta CloudKit composta predicati uniti tramite il `AND` operatore.
    
    ```
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```
    


#### <a name="creating-queries"></a>Creazione di query

Il codice seguente può essere utilizzato per creare un `CKQuery` in un'applicazione per Xamarin iOS 8:

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

Innanzitutto, crea un predicato per selezionare solo i record che soddisfano un determinato nome. Quindi crea una query che verrà selezionati i record del tipo di Record specificato che corrispondono al predicato.

#### <a name="performing-a-query"></a>Esegue una Query

Dopo aver creata una Query, usare il codice seguente per eseguire la query ed elaborare i record restituiti:

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

Il codice precedente accetta la query creata in precedenza e lo esegue sul Database pubblico. Poiché è specificato alcun fuso Record, tutte le zone di ricerca. Se si è verificato alcun errore, una matrice di `CKRecords` verrà restituito corrispondente ai parametri della query.

Il metodo per descrivere le query è che sono sondaggi e sono molto utili per il sezionamento attraverso grandi set di dati. Le query, tuttavia, non sono adatte per i set di dati di grandi dimensioni ed essenzialmente statico i motivi seguenti:

-  Sono errate per la durata della batteria di dispositivi.
-  Sono errate per il traffico di rete.
-  Sono un impatto negativo sulle piacevole perché le informazioni che vedono sono limitate dalla frequenza dell'applicazione esegue il polling del database. Oggi gli utenti si aspettano notifiche push quando vengono apportate modifiche.


### <a name="subscriptions"></a>Sottoscrizioni

Quando si lavora con set di dati di grandi dimensioni ed essenzialmente statico, la query non deve essere eseguita nel dispositivo client, deve essere eseguito nel server per conto del client. La query deve essere eseguito in background e deve essere eseguita dopo ogni singolo record salvato, per il dispositivo corrente o un altro dispositivo toccando lo stesso database.

Infine, una notifica push deve essere inviata a tutti i dispositivi collegati al database quando viene eseguita la query sul lato server.

Le sottoscrizioni vengono esposte in CloudKit Framework tramite il `CKSubscription` classe. Queste foreste combinano un tipo di Record ( `RecordType`), un predicato ( `NSPredicate`) e un account Apple Push Notification ( `Push`).

> [!NOTE]
> Push CloudKit leggermente sono aumentati in modo che contengono un payload contenente CloudKit informazioni specifiche, ad esempio ciò che ha causato il push a verificarsi.

#### <a name="how-subscriptions-work"></a>Funzionano delle sottoscrizioni

Prima di implementare la sottoscrizione in C# il codice, diamo una rapida panoramica del funzionano delle sottoscrizioni:

 [![](intro-to-cloudkit-images/image39.png "Una panoramica del funzionano delle sottoscrizioni")](intro-to-cloudkit-images/image39.png#lightbox)

Il grafico precedente mostra il processo di sottoscrizione tipico come indicato di seguito:

1.  Il dispositivo client crea una nuova sottoscrizione che contiene il set di condizioni che attivano una notifica Push che verrà inviata quando viene generato il trigger e la sottoscrizione.
2.  La sottoscrizione viene inviata al Database in cui viene aggiunto alla raccolta di sottoscrizioni esistenti.
3.  Un secondo dispositivo crea un nuovo Record e salva tale record nel Database.
4.  Esegue la ricerca del Database tramite l'elenco di sottoscrizioni se il nuovo Record corrisponde a una delle rispettive condizioni.
5.  Se viene trovata una corrispondenza, la notifica Push viene inviata al dispositivo registrato la sottoscrizione con informazioni sul Record che ha causato l'attivazione.


Con queste informazioni posto, esaminiamo la creazione di sottoscrizioni in un file di Xamarin iOS 8 dell'applicazione.

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

Innanzitutto, crea un predicato che fornisce la condizione per attivare la sottoscrizione. Successivamente, crea la sottoscrizione con un tipo di Record specifici e imposta l'opzione di quando è sottoposto a test il trigger. Infine, definisce il tipo di notifica che si verifica quando viene attivata la sottoscrizione e la collega la sottoscrizione.

### <a name="saving-subscriptions"></a>Salvataggio delle sottoscrizioni

Con la sottoscrizione creata, il codice seguente viene salvato di nuovo il Database:

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

Usando l'API di praticità, la chiamata è asincrona, semplice e offre la gestione degli errori semplice.

#### <a name="handling-push-notifications"></a>La gestione delle notifiche Push

Se le notifiche Push Apple (AP) ha precedenza usato dallo sviluppatore, il processo di gestione di notifiche generate da CloudKit deve essere familiare.

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

Il codice sopra riportato richiede CloudKit per analizzare le informazioni utente in una notifica CloudKit. Successivamente, viene estratto informazioni sull'avviso. Infine, il tipo di notifica viene testato e la notifica è gestirli di conseguenza.

In questa sezione ha illustrato come rispondere i Big Data, problema piccolo dispositivo presentato in precedenza tramite query e le sottoscrizioni. L'applicazione verrà lasciare i dati di grandi dimensioni nel cloud e usare queste tecnologie per fornire visualizzazioni in questo set di dati.

## <a name="cloudkit-user-accounts"></a>Account utente di CloudKit

Come indicato all'inizio di questo articolo, CloudKit si basa sull'infrastruttura iCloud esistente. La sezione seguente verrà trattate in dettaglio, come gli account sono esposti a uno sviluppatore che usa l'API CloudKit.

### <a name="authentication"></a>Authentication

Quando si lavora con gli account utente, la prima considerazione è l'autenticazione. CloudKit supporta l'autenticazione tramite l'utente attualmente connesso in iCloud nel dispositivo. L'autenticazione viene eseguita in background e viene gestita da iOS. In questo modo, gli sviluppatori non devono mai preoccuparsi dei dettagli di implementazione dell'autenticazione. Questi test solo per verificare se un utente è connesso.

### <a name="user-account-information"></a>Informazioni sull'Account utente

CloudKit offre le seguenti informazioni utente per gli sviluppatori:

-  **Identità** -un modo per identificare in modo univoco l'utente.
-  **Metadati** : la possibilità di salvare e recuperare informazioni sugli utenti.
-  **Privacy** – tutte le informazioni viene gestite in un manor consapevole sulla privacy. Non viene esposto a meno che l'utente ha accettato ad esso.
-  **Individuazione** – offre agli utenti la possibilità di individuare gli amici che utilizzano la stessa applicazione.


Successivamente, si esaminerà in dettaglio questi argomenti.

#### <a name="identity"></a>identità

Come indicato in precedenza, CloudKit fornisce un modo per l'applicazione identificare in modo univoco un utente specifico:

 [![](intro-to-cloudkit-images/image40.png "In modo univoco che identifica un determinato utente")](intro-to-cloudkit-images/image40.png#lightbox)

È un'applicazione client in esecuzione su dispositivi dell'utente e tutti i database utente privato specifico all'interno del contenitore CloudKit. L'applicazione client deve essere collegato a uno di questi utenti specifici. Si basa sull'utente connesso a iCloud in locale nel dispositivo.

Poiché questo provengono da iCloud, è presente un rich archivio di informazioni utente di backup. E poiché iCloud effettivamente ospita il contenitore, è possibile correlare gli utenti. Nella figura precedente, l'utente il cui account iCloud `user@icloud.com` è collegato il client corrente.

In base a ciascun contenitore dal contenitore, un ID utente univoco, generato in modo casuale viene creato e associato account iCloud dell'utente (indirizzo di posta elettronica). L'ID utente viene restituito all'applicazione e può essere usato in alcun modo che lo sviluppatore adatti.

> [!NOTE]
> Diverse applicazioni in esecuzione sullo stesso dispositivo per lo stesso utente iCloud avrà ID utente diverso perché sono connessi a contenitori CloudKit diversi.

Il seguente codice recupera l'ID utente di CloudKit per attualmente connesso in utente iCloud nel dispositivo:

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

Il codice sopra riportato viene chiesto il contenitore di CloudKit per fornire l'ID dell'utente attualmente connesso. Poiché queste informazioni provengono da iCloud Server, la chiamata è asincrona e la gestione degli errori è obbligatorio.

#### <a name="metadata"></a>Metadata

Ogni utente in CloudKit dispone i metadati specifici che descrivono tali. Questi metadati vengano rappresentato come un CloudKit Record:

 [![](intro-to-cloudkit-images/image41.png "Ogni utente in CloudKit dispone di metadati specifici che descrivono tali")](intro-to-cloudkit-images/image41.png#lightbox)

La ricerca all'interno del Database privati per un utente specifico di un contenitore non esiste è un Record che definisce tale utente. Esistono molti record utente all'interno del Database pubblico, una per ogni utente del contenitore. Uno dei seguenti avranno un ID record che corrisponde l'ID del Record. dell'utente attualmente connesso

Record utente nel Database pubblico sono world leggibile. Che vengono trattati, nella maggior parte, come un normale Record e sono di tipo `CKRecordTypeUserRecord`. Questi record sono riservati dal sistema e non sono disponibili per le query.

Usare il codice seguente per accedere a un Record utente:

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

Il codice sopra riportato viene chiesto di Database Public per restituire il Record utente per l'utente che è l'ID è accessibile di sopra. Poiché queste informazioni provengono da iCloud Server, la chiamata è asincrona e la gestione degli errori è obbligatorio.

#### <a name="privacy"></a>Privacy

CloudKit era progettazione, per impostazione predefinita, per proteggere la privacy dell'utente attualmente connesso. Per impostazione predefinita non viene esposta alcuna informazioni personali sull'utente. Esistono alcuni casi in cui l'applicazione richiederà alcune informazioni relative all'utente.

In questi casi, l'applicazione può richiedere che l'utente divulgare tali informazioni. Verrà visualizzata una finestra di dialogo per l'utente chiedendogli di acconsentire esplicitamente a esporre le informazioni degli account.

#### <a name="discovery"></a>Individuazione

Supponendo che l'utente ha scelto aggiuntivo per consentire all'applicazione un accesso alle informazioni di account utente limitato, possono essere individuabile da altri utenti dell'applicazione:

 [![](intro-to-cloudkit-images/image42.png "Un utente può essere individuabile da altri utenti dell'applicazione")](intro-to-cloudkit-images/image42.png#lightbox)

L'applicazione client sta comunicando con un contenitore e il contenitore sta comunicando con iCloud per accedere alle informazioni utente. L'utente può fornire un indirizzo di posta elettronica e individuazione può essere utilizzato per ottenere informazioni nuovamente l'utente. Facoltativamente, l'ID utente è anche utilizzabile per individuare informazioni sull'utente.

CloudKit fornisce inoltre un modo per individuare le informazioni su tutti gli utenti che potrebbero essere gli amici di attualmente connesso in iCloud utente eseguendo una query nella Rubrica intero. Il processo di CloudKit pull manuale di contatto dell'utente e usare gli indirizzi di posta elettronica per vedere se è possibile trovare altri utenti dell'applicazione che corrispondono a questi indirizzi.

In questo modo l'applicazione sfruttare il libro di contatto dell'utente senza fornire l'accesso ad esso o chiedere all'utente di approvare l'accesso ai contatti. In nessun momento le informazioni di contatto sarà disponibile per l'applicazione, solo il processo di CloudKit ha accesso.

Ricapitolando, sono disponibili tre diversi tipi di input per l'individuazione utente:

-  **ID del Record utente** – individuazione può essere eseguita in base all'ID utente di attualmente connesso in CloudKit utente.
-  **Indirizzo di posta elettronica utente** : l'utente può fornire un indirizzo di posta elettronica e può essere utilizzato per l'individuazione.
-  **Contattare il libro** – rubrica dell'utente può essere usata per individuare gli utenti dell'applicazione che hanno lo stesso indirizzo di posta elettronica come elencato nella loro contatti.


Individuazione utente restituirà le informazioni seguenti:

-  **ID del Record utente** -l'ID univoco di un utente del database Public.
-  **Primo e il cognome** : come archiviato nel Database pubblico.


Queste informazioni restituirà solo per gli utenti che hanno scelto nell'individuazione.

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

Usare il codice seguente per eseguire query di tutti gli utenti del libro di contatto:

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

In questa sezione sono stati trattati le quattro aree principali di accesso a un account utente che CloudKit può fornire a un'applicazione. Uso di identità dell'utente e i metadati, per i criteri di Privacy integrate nelle CloudKit e infine la possibilità di individuare altri utenti dell'applicazione.

## <a name="the-development-and-production-environments"></a>Lo sviluppo e ambienti di produzione

CloudKit offre ambienti di sviluppo e produzione separati per i tipi di record e i dati di un'applicazione. L'ambiente di sviluppo è un ambiente più flessibile è disponibile solo per i membri del team di sviluppo. Quando un'applicazione aggiunge un nuovo campo a un record e salva tale record nell'ambiente di sviluppo, il server Aggiorna automaticamente le informazioni sullo schema.

Lo sviluppatore può utilizzare questa funzionalità per apportare modifiche a uno schema durante lo sviluppo, che consente di risparmiare tempo. Un'avvertenza è che dopo l'aggiunta di un campo a un record, il tipo di dati associato a tale campo non venga modificato a livello di codice. Per modificare un tipo di campo, lo sviluppatore deve eliminare il campo [CloudKit Dashboard](https://icloud.developer.apple.com/dashboard/) e aggiungerlo nuovamente con il nuovo tipo.

Prima di distribuire l'applicazione, lo sviluppatore può eseguire la migrazione i dati e lo schema per l'ambiente di produzione usando **CloudKit Dashboard**. Durante l'esecuzione per l'ambiente di produzione, il server impedisce la modifica dello schema a livello di codice di un'applicazione. Lo sviluppatore puoi ancora apportare modifiche con **CloudKit Dashboard** ma tenta di aggiungere campi a un record in risultato l'ambiente di produzione errori.

> [!NOTE]
> IOS Simulator funziona solo con il **ambiente di sviluppo**. Quando lo sviluppatore è pronto per testare un'applicazione in un **ambiente di produzione**, è necessario un dispositivo iOS fisico.


## <a name="shipping-a-cloudkit-enabled-app"></a>App abilitata per la spedizione di un CloudKit

Prima di spedire un'applicazione che usa CloudKit, dovrà essere configurato per destinazione il **ambiente di produzione CloudKit** o l'applicazione verrà rifiutata da Apple.

Seguire questa procedura:

1. In Visual Studio per agente di gestione, compilare l'applicazione per **Release** > **dispositivo iOS**: 

    [![](intro-to-cloudkit-images/shipping01.png "Compilare l'applicazione per il rilascio")](intro-to-cloudkit-images/shipping01.png#lightbox)

2. Dal **compilare** dal menu **Archive**: 

    [![](intro-to-cloudkit-images/shipping02.png "Seleziona archivio")](intro-to-cloudkit-images/shipping02.png#lightbox)

3. Il **Archive** verrà creato e visualizzato in Visual Studio per Mac: 

    [![](intro-to-cloudkit-images/shipping03.png "L'archivio verrà creato e visualizzato")](intro-to-cloudkit-images/shipping03.png#lightbox)

4. Avviare **Xcode**.
5. Dal **finestra** dal menu **organizzatore**: 

    [![](intro-to-cloudkit-images/shipping04.png "Selezionare l'organizzatore")](intro-to-cloudkit-images/shipping04.png#lightbox)

6. Selezionare l'archivio dell'applicazione e fare clic il **Esporta...**  pulsante: 

    [![](intro-to-cloudkit-images/shipping05.png "Archivio dell'applicazione")](intro-to-cloudkit-images/shipping05.png#lightbox)
    
7. Selezionare un metodo per l'esportazione e scegliere il **successivo** pulsante: 

    [![](intro-to-cloudkit-images/shipping06.png "Selezionare un metodo per l'esportazione")](intro-to-cloudkit-images/shipping06.png#lightbox)

8. Selezionare il **Team di sviluppo** dall'elenco a discesa scegliere il **Scegli** pulsante: 

    [![](intro-to-cloudkit-images/shipping07.png "Selezionare il Team di sviluppo nell'elenco a discesa")](intro-to-cloudkit-images/shipping07.png#lightbox)

9. Selezionare **produzione** dall'elenco a discesa scegliere il **successivo** pulsante: 

    [![](intro-to-cloudkit-images/shipping08.png "Selezionare l'ambiente di produzione nell'elenco a discesa")](intro-to-cloudkit-images/shipping08.png#lightbox)

10. Esaminare le impostazioni e fare clic sui **esportare** pulsante: 

    [![](intro-to-cloudkit-images/shipping09.png "Esaminare l'impostazione")](intro-to-cloudkit-images/shipping09.png#lightbox)

11. Scegliere un percorso in cui generare l'applicazione risultante `.ipa` file.

Il processo è simile per inviare l'applicazione direttamente in iTunes Connect, è sufficiente scegliere il **Submit...**  pulsante anziché l'esportazione... dopo aver selezionato un archivio nella finestra di libreria.

## <a name="when-to-use-cloudkit"></a>Quando usare CloudKit

Come abbiamo visto in questo articolo, CloudKit fornisce un modo semplice per un'applicazione archiviare e recuperare informazioni dai server iCloud. Premesso questo, CloudKit non superate o deprecare uno qualsiasi degli strumenti esistenti o Framework.

### <a name="use-cases"></a>Casi d'uso

I casi d'uso seguenti devono consentono allo sviluppatore decidere quando usare una tecnologia o un framework specifico iCloud:

-  **Store chiave-valore iCloud** : mantiene aggiornate piccola quantità di dati in modo asincrono ed è ideale per operare con le preferenze dell'applicazione. Tuttavia, questo è vincolato per una piccola quantità di informazioni.
-  **iCloud Drive** : basato su iCloud esistente le API di documenti e fornisce un'API semplice per eseguire la sincronizzazione dati non strutturati dal file system. Fornisce una cache non in linea completa su Mac OS X ed è ideale per applicazioni incentrate sui documenti.
-  **iCloud Core Data** : consente i dati vengono replicati tra tutti i dispositivi dell'utente. I dati sono single user e molto utile per mantenere privato, strutturata dei dati sincronizzati.
-  **CloudKit** : fornisce i dati pubblici entrambi struttura e di massa e sia in grado di gestire grandi set di dati sia file di dati non strutturati di grandi dimensioni. Relativo legati all'utente dell'account iCloud e fornisce client indirizzato il trasferimento dei dati.


Mantenere questi casi di utilizzo presente, lo sviluppatore deve scegliere la tecnologia di iCloud corrette per assicurare la funzionalità richiesta dell'applicazione corrente e fornire una scalabilità ottimale per la crescita futura.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato una rapida introduzione all'API di CloudKit. Ha illustrato come effettuare il provisioning e configurare un'applicazione iOS di Xamarin per usare CloudKit. Le funzionalità dell'API di praticità CloudKit è regolato. Ha Mostra come progettare un CloudKit abilitato dell'applicazione per la scalabilità utilizzando query e le sottoscrizioni. E infine ha dimostrato, le informazioni dell'Account utente che viene esposto a un'applicazione dal CloudKit.

## <a name="related-links"></a>Collegamenti correlati

- [CloudKitAtlas (sample)](https://developer.xamarin.com/samples/monotouch/ios8/CloudKitAtlas/)
- [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Creazione di un profilo di provisioning](~/ios/get-started/installation/device-provisioning/index.md)
