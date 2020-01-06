---
title: CloudKit in Novell. iOS
description: Questo documento descrive come usare CloudKit in Novell. iOS. Viene fornita una panoramica di CloudKit e viene illustrato come abilitarla, l'API CloudKit praticità, la scalabilità, gli account utente e gli ambienti di sviluppo e produzione.
ms.prod: xamarin
ms.assetid: 66B207F2-FAA0-4551-B43B-3DB9F620C397
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/11/2016
ms.openlocfilehash: 29ccb919f68a45212bff3b66b4bc3fbdebd24faf
ms.sourcegitcommit: bad1ab3f78d7f94d48511666626b54f8ba155689
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663460"
---
# <a name="cloudkit-in-xamarinios"></a>CloudKit in Novell. iOS

Il Framework CloudKit semplifica lo sviluppo di applicazioni che accedono a iCloud. Questo include il recupero dei dati delle applicazioni e dei diritti degli asset, nonché la possibilità di archiviare in modo sicuro le informazioni sull'applicazione. Questo kit offre agli utenti un livello di anonimità consentendo l'accesso alle applicazioni con gli ID iCloud senza condividere le informazioni personali.

Gli sviluppatori possono concentrarsi sulle applicazioni lato client e lasciare che iCloud elimini la necessità di scrivere logica dell'applicazione sul lato server. CloudKit fornisce l'autenticazione, i database privati e pubblici, nonché i dati strutturati e i servizi di archiviazione degli asset.

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

## <a name="requirements"></a>Requisiti di

Per completare i passaggi illustrati in questo articolo, è necessario quanto segue:

- **Xcode e iOS SDK** : le API Xcode e iOS 8 di Apple devono essere installate e configurate nel computer dello sviluppatore.
- **Visual Studio per Mac** : la versione più recente di Visual Studio per Mac deve essere installata e configurata nel dispositivo utente.
- **dispositivo iOS 8** : un dispositivo iOS che esegue la versione più recente di iOS 8 per il test.

## <a name="what-is-cloudkit"></a>Che cos'è CloudKit?

CloudKit è un modo per consentire agli sviluppatori di accedere ai server iCloud. Fornisce le basi per l'unità iCloud e la libreria foto iCloud. CloudKit è supportato nei dispositivi macOS e iOS.

[![il modo in cui CloudKit è supportato nei dispositivi macOS e iOS](intro-to-cloudkit-images/image1.png)](intro-to-cloudkit-images/image1.png#lightbox)

CloudKit usa l'infrastruttura di account iCloud. Se un utente ha effettuato l'accesso a un account iCloud sul dispositivo, CloudKit utilizzerà il proprio ID per identificare l'utente. Se non è disponibile alcun account, verrà fornito un accesso di sola lettura limitato.

CloudKit supporta sia il concetto di database pubblico che quello privato. I database pubblici forniscono una "zuppa" di tutti i dati a cui l'utente ha accesso. I database privati sono destinati a archiviare i dati privati associati a un utente specifico.

CloudKit supporta sia i dati strutturati che quelli in blocco. È in grado di gestire facilmente i trasferimenti di file di grandi dimensioni. CloudKit esegue in modo efficiente il trasferimento di file di grandi dimensioni da e verso i server iCloud in background, consentendo allo sviluppatore di concentrarsi su altre attività.

> [!NOTE]
> È importante notare che CloudKit è una tecnologia di _trasporto_. Non fornisce alcuna persistenza. consente solo a un'applicazione di inviare e ricevere informazioni dai server in modo efficiente.

Al momento della stesura di questo articolo, Apple fornisce inizialmente CloudKit con un limite massimo per la larghezza di banda e la capacità di archiviazione. Per progetti di grandi dimensioni o applicazioni con una base di utenti di grandi dimensioni, Apple ha suggerito che verrà fornita una scala di prezzo conveniente.

## <a name="enabling-cloudkit-in-a-xamarin-application"></a>Abilitazione di CloudKit in un'applicazione Novell

Prima che un'applicazione Novell possa usare il Framework CloudKit, è necessario eseguire correttamente il provisioning dell'applicazione come illustrato in dettaglio nelle guide [utilizzo delle funzionalità](~/ios/deploy-test/provisioning/capabilities/icloud-capabilities.md) e [utilizzo di diritti](~/ios/deploy-test/provisioning/entitlements.md) .

Per accedere a CloudKit, il file con **estensione plist dei diritti** deve includere **Abilita iCloud**, **archiviazione chiave-valore**e autorizzazioni **CloudKit** .

### <a name="sample-app"></a>App di esempio

L' [esempio CloudKitAtlas](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-cloudkitatlas) illustra come usare CloudKit con Novell. I passaggi seguenti illustrano come configurare l'esempio, per cui sono necessarie impostazioni aggiuntive oltre a quelle richieste solo per CloudKit:

1. Aprire il progetto in Visual Studio per Mac o Visual Studio.
2. Nel **Esplora soluzioni**aprire il file **info. plist** e verificare che l' **identificatore del bundle** corrisponda a quello definito nell' **ID app** creato durante la configurazione del provisioning.
3. Scorrere fino alla fine del file **info. plist** e selezionare le **modalità di sfondo abilitate**, **gli aggiornamenti della posizione**e le **notifiche remote**.
4. Fare clic con il pulsante destro del mouse sul progetto iOS nella soluzione e scegliere **Opzioni**.
5. Selezionare **firma del bundle iOS**, selezionare l' **identità dello sviluppatore** e il **profilo di provisioning** creato in precedenza.
6. Assicurarsi che i **diritti. plist** includa **iCloud**, **archiviazione chiave-valore**e **CloudKit**.
7. Verificare che il **contenitore di connettività** esista per l'applicazione. Esempio: `iCloud.com.your-company.CloudKitAtlas`
8. Salvare le modifiche apportate al file.

Con queste impostazioni, l'app di esempio è ora pronta ad accedere alle API del Framework CloudKit, oltre che ai servizi di sfondo, posizione e notifica.

## <a name="cloudkit-api-overview"></a>Panoramica dell'API CloudKit

Prima di implementare CloudKit in un'applicazione Novell iOS, in questo articolo verranno illustrate le nozioni di base di CloudKit Framework, che include gli argomenti seguenti:

1. **Contenitori** : silo isolati di comunicazioni iCloud.
2. **Database** : Public e private sono disponibili per l'applicazione.
3. **Record** : meccanismo in cui i dati strutturati vengono spostati da e verso CloudKit.
4. **Zone di record** : sono gruppi di record.
5. **Identificatori di record** : sono completamente normalizzati e rappresentano la posizione specifica del record.
6. **Riferimento** : fornire relazioni padre-figlio tra i record correlati all'interno di un determinato database.
7. **Asset** : consente il caricamento di file di grandi dimensioni, non strutturati in iCloud e associati a un record specificato.

### <a name="containers"></a>Contenitori

Una determinata applicazione in esecuzione su un dispositivo iOS è sempre in esecuzione accanto ad altre applicazioni e servizi su tale dispositivo. Sul dispositivo client, l'applicazione verrà sottoposto a silo o in modalità sandbox. In alcuni casi, si tratta di un sandbox letterale e, in altri, l'applicazione è semplicemente in esecuzione nello spazio di memoria.

Il concetto di acquisizione di un'applicazione client e di esecuzione separato da altri client è molto potente e offre i vantaggi seguenti:

1. **Sicurezza** : un'applicazione non può interferire con altre app client o il sistema operativo stesso.
1. **Stabilità** : se l'applicazione client si arresta in modo anomalo, non può estrarre altre app del sistema operativo.
1. **Privacy** : ogni applicazione client ha accesso limitato alle informazioni personali archiviate nel dispositivo.

CloudKit è stato progettato per fornire gli stessi vantaggi indicati in precedenza e applicarli all'uso delle informazioni basate sul cloud:

 [![](intro-to-cloudkit-images/image31.png "CloudKit apps communicate using containers")](intro-to-cloudkit-images/image31.png#lightbox)

Analogamente a quanto avviene per l'applicazione uno-a-molti in esecuzione sul dispositivo, le comunicazioni dell'applicazione con iCloud uno-a-molti. Ognuno di questi diversi silo di comunicazione è denominato contenitori.

I contenitori vengono esposti nel Framework CloudKit tramite la classe `CKContainer`. Per impostazione predefinita, un'applicazione comunica con un contenitore e il contenitore separa i dati per l'applicazione. Questo significa che diverse applicazioni possono archiviare le informazioni nello stesso account iCloud, ma queste informazioni non verranno mai rimescolate.

Il contenitore dei dati iCloud consente anche a CloudKit di incapsulare le informazioni utente. In questo modo, l'applicazione avrà un accesso limitato all'account iCloud e le informazioni utente archiviate in, garantendo al tempo stesso la protezione della privacy e della sicurezza dell'utente.

I contenitori sono completamente gestiti dallo sviluppatore dell'applicazione tramite il portale WWDR. Lo spazio dei nomi del contenitore è globale in tutti gli sviluppatori Apple, pertanto il contenitore non può essere univoco solo per le applicazioni di uno sviluppatore specifico, ma per tutti gli sviluppatori e le applicazioni Apple.

Apple suggerisce di usare la notazione DNS inversa quando si crea lo spazio dei nomi per i contenitori di applicazioni. Esempio: `iCloud.com.company-name.application-name`

Mentre i contenitori sono, per impostazione predefinita, associati uno-a-uno a una determinata applicazione, possono essere condivisi tra le applicazioni. Quindi, più applicazioni possono coordinare in un singolo contenitore. Una singola applicazione può anche comunicare con più contenitori.

### <a name="databases"></a>database

Una delle funzioni principali di CloudKit consiste nell'adottare il modello di dati e la replica di un'applicazione che si modellano sui server iCloud. Alcune informazioni sono destinate all'utente che l'ha creata, altre informazioni sono dati pubblici che possono essere creati da un utente per l'uso pubblico (ad esempio una revisione del ristorante) o informazioni che lo sviluppatore ha pubblicato per l'applicazione. In entrambi i casi, il gruppo di destinatari non è solo un singolo utente, ma è una community di persone.

 [![](intro-to-cloudkit-images/image32.png "CloudKit Container Diagram")](intro-to-cloudkit-images/image32.png#lightbox)

All'interno di un contenitore, prima di tutto è il database pubblico. Questo è il punto in cui si trovano tutte le informazioni pubbliche e si confonde. Sono inoltre disponibili diversi database privati singoli per ogni utente dell'applicazione.

Quando è in esecuzione in un dispositivo iOS, l'applicazione avrà accesso solo alle informazioni per l'utente iCloud attualmente connesso. Quindi, la visualizzazione dell'applicazione del contenitore sarà la seguente:

 [![](intro-to-cloudkit-images/image33.png "The applications view of the container")](intro-to-cloudkit-images/image33.png#lightbox)

Può visualizzare solo il database pubblico e il database privato associato all'utente iCloud attualmente connesso.

I database vengono esposti nel Framework CloudKit tramite la classe `CKDatabase`. Ogni applicazione può accedere a due database: il database pubblico e quello privato.

Il contenitore è il punto di ingresso iniziale in CloudKit. Il codice seguente può essere usato per accedere al database pubblico e privato dal contenitore predefinito dell'applicazione:

```csharp
using CloudKit;
//...

public CKDatabase PublicDatabase { get; set; }
public CKDatabase PrivateDatabase { get; set; }
//...

// Get the default public and private databases for
// the application
PublicDatabase = CKContainer.DefaultContainer.PublicCloudDatabase;
PrivateDatabase = CKContainer.DefaultContainer.PrivateCloudDatabase;
```

Di seguito sono riportate le differenze tra i tipi di database:

||Database pubblico|Database privato|
|---|--- |--- |
|**Tipo di dati**|Dati condivisi|Dati dell'utente corrente|
|**Quota**|Conto nella quota dello sviluppatore|Conto nella quota dell'utente|
|**Autorizzazioni predefinite**|Leggibile in tutto il mondo|Leggibile dall'utente|
|**Modifica di autorizzazioni**|Ruoli del dashboard iCloud tramite un livello di classe di record|N/D|

### <a name="records"></a>Record

I contenitori contengono database e all'interno di database sono record. I record sono il meccanismo in cui i dati strutturati vengono spostati da e verso CloudKit:

 [![](intro-to-cloudkit-images/image34.png "Containers hold databases, and inside databases are records")](intro-to-cloudkit-images/image34.png#lightbox)

I record vengono esposti nel Framework CloudKit tramite la classe `CKRecord`, che esegue il wrapping delle coppie chiave-valore. Un'istanza di un oggetto in un'applicazione equivale a un `CKRecord` in CloudKit. Ogni `CKRecord` dispone inoltre di un tipo di record, equivalente alla classe di un oggetto.

I record hanno uno schema JIT, quindi i dati vengono descritti in CloudKit prima di essere passati per l'elaborazione. A questo punto, CloudKit interpreterà le informazioni e gestirà la logistica dell'archiviazione e del recupero del record.

La classe `CKRecord` supporta inoltre un'ampia gamma di metadati. Ad esempio, un record contiene informazioni sul momento in cui è stato creato e l'utente che l'ha creata. Un record contiene anche informazioni sul momento dell'Ultima modifica e sull'utente che lo ha modificato.

I record contengono la nozione di tag di modifica. Si tratta di una versione precedente di una revisione di un determinato record. Il tag di modifica viene usato come metodo leggero per determinare se il client e il server hanno la stessa versione di un record specificato.

Come indicato in precedenza, `CKRecords` eseguire il wrapping delle coppie chiave-valore e, di conseguenza, i tipi di dati seguenti possono essere archiviati in un record:

1. `NSString`
1. `NSNumber`
1. `NSData`
1. `NSDate`
1. `CLLocation`
1. `CKReferences`
1. `CKAssets`

Oltre ai tipi valore singolo, un record può contenere una matrice omogenea di uno qualsiasi dei tipi elencati in precedenza.

Il codice seguente può essere usato per creare un nuovo record e archiviarlo in un database:

```csharp
using CloudKit;
//...

private const string ReferenceItemRecordName = "ReferenceItems";
//...

var newRecord = new CKRecord (ReferenceItemRecordName);
newRecord ["name"] = (NSString)nameTextField.Text;
await CloudManager.SaveAsync (newRecord);
```

### <a name="record-zones"></a>Zone di record

I record non esistono autonomamente in un determinato database: i gruppi di record esistono insieme all'interno di una zona di record. Le zone di record possono essere considerate come tabelle in un database relazionale tradizionale:

 [![](intro-to-cloudkit-images/image35.png "Groups of records exist together inside a Record Zone")](intro-to-cloudkit-images/image35.png#lightbox)

Possono essere presenti più record in una determinata area di record e più zone di record in un determinato database. Ogni database contiene una zona di record predefinita:

 [![](intro-to-cloudkit-images/image36.png "Every database contains a Default Record Zone and Custom Zone")](intro-to-cloudkit-images/image36.png#lightbox)

Questo è il punto in cui i record vengono archiviati per impostazione predefinita. È inoltre possibile creare zone di record personalizzate. Le zone di record rappresentano la granularità di base a cui vengono eseguiti i commit atomici e Rilevamento modifiche.

## <a name="record-identifiers"></a>Identificatori di record

Gli identificatori di record sono rappresentati come tupla, che contiene sia il nome del record fornito dal client che la zona in cui è presente il record. Gli identificatori di record hanno le seguenti caratteristiche:

- Vengono creati dall'applicazione client.
- Sono completamente normalizzate e rappresentano la posizione specifica del record.
- Assegnando l'ID univoco di un record in un database esterno al nome del record, è possibile usare per eseguire il bridging dei database locali che non sono archiviati all'interno di CloudKit.

Quando gli sviluppatori creano nuovi record, possono scegliere di passare un identificatore di record. Se non si specifica un identificatore di record, viene creato automaticamente un UUID che verrà assegnato al record.

Quando gli sviluppatori creano nuovi identificatori di record, possono scegliere di specificare la zona di record a cui apparterrà ogni record. Se non viene specificato alcun valore, verrà utilizzata la zona di record predefinita.

Gli identificatori di record vengono esposti nel Framework CloudKit tramite la classe `CKRecordID`. Il codice seguente può essere usato per creare un nuovo identificatore di record:

```csharp
var recordID =  new CKRecordID("My Record");
```

### <a name="references"></a>Riferimenti

I riferimenti forniscono relazioni tra record correlati all'interno di un database specifico:

 [![](intro-to-cloudkit-images/image37.png "References provide relationships between related Records within a given Database")](intro-to-cloudkit-images/image37.png#lightbox)

Nell'esempio precedente, i genitori sono proprietari dei figli in modo che l'elemento figlio sia un record figlio del record padre. La relazione passa dal record figlio al record padre e viene definita *back reference*.

I riferimenti vengono esposti nel Framework CloudKit tramite la classe `CKReference`. Sono un modo per consentire al server iCloud di comprendere la relazione tra i record.

I riferimenti forniscono il meccanismo alla base delle eliminazioni a cascata. Se un record padre viene eliminato dal database, tutti i record figlio (come specificato in una relazione) verranno eliminati automaticamente anche dal database.

> [!NOTE]
> I puntatori penzoloni sono una possibilità quando si usa CloudKit. Ad esempio, nel momento in cui l'applicazione ha recuperato un elenco di puntatori di record, ha selezionato un record e quindi ha richiesto il record, il record potrebbe non esistere più nel database. Un'applicazione deve essere codificata per gestire correttamente questa situazione.

Sebbene non sia obbligatorio, i riferimenti indietro sono preferiti quando si utilizza il Framework CloudKit. Apple ha ottimizzato il sistema per rendere questo il tipo di riferimento più efficiente.

Quando si crea un riferimento, lo sviluppatore può fornire un record già in memoria o creare un riferimento a un identificatore di record. Se si usa un identificatore di record e il riferimento specificato non esiste nel database, verrà creato un puntatore in sospeso.

Di seguito è riportato un esempio di creazione di un riferimento a un record noto:

```csharp
var reference = new CKReference(newRecord, new CKReferenceAction());
```

### <a name="assets"></a>Asset

Gli asset consentono il caricamento di un file di dati non strutturati di grandi dimensioni in iCloud e associati a un record specificato:

 [![](intro-to-cloudkit-images/image38.png "Assets allow for a file of large, unstructured data to be uploaded to iCloud and associated with a given Record")](intro-to-cloudkit-images/image38.png#lightbox)

Sul client viene creato un `CKRecord` che descrive il file che verrà caricato nel server iCloud. Viene creato un `CKAsset` per contenere il file ed è collegato al record che lo descrive.

Quando il file viene caricato nel server, il record viene inserito nel database e il file viene copiato in un database di archiviazione bulk speciale. Viene creato un collegamento tra il puntatore del record e il file caricato.

Gli asset vengono esposti nel Framework CloudKit tramite la classe `CKAsset` e vengono usati per archiviare dati di grandi dimensioni e non strutturati. Poiché lo sviluppatore non desidera mai avere dati di grandi dimensioni, non strutturati in memoria, le risorse vengono implementate usando file su disco.

Gli asset sono di proprietà dei record, che consente di recuperare le risorse da iCloud usando il record come puntatore. In questo modo il server può raccogliere gli asset quando il record proprietario dell'asset viene eliminato.

Poiché `CKAssets` sono destinati alla gestione di file di dati di grandi dimensioni, Apple ha progettato CloudKit per caricare e scaricare in modo efficiente le risorse.

Il codice seguente può essere usato per creare un asset e associarlo al record:

```csharp
var fileUrl = new NSUrl("LargeFile.mov");
var asset = new CKAsset(fileUrl);
newRecord ["name"] = asset;
```

Sono stati ora trattati tutti gli oggetti fondamentali all'interno di CloudKit. I contenitori sono associati alle applicazioni e contengono database. I database contengono record raggruppati in zone di record a cui puntano gli identificatori di record. Le relazioni padre-figlio vengono definite tra i record utilizzando i riferimenti. Infine, i file di grandi dimensioni possono essere caricati e associati ai record tramite asset.

## <a name="cloudkit-convenience-api"></a>API CloudKit convenience

Apple offre due diversi set di API per l'uso di CloudKit:

- **API operativa** : offre ogni singola funzionalità di CloudKit. Per le applicazioni più complesse, questa API offre un controllo granulare sui CloudKit.
- **API pratici** : offre un subset comune preconfigurato di funzionalità CloudKit. Offre una soluzione comoda e facile da accedere per includere la funzionalità CloudKit in un'applicazione iOS.

L'API convenienza è in genere la scelta migliore per la maggior parte delle applicazioni iOS e Apple suggerisce di iniziare. Nella parte restante di questa sezione vengono illustrati gli argomenti pratici dell'API seguenti:

- Salvataggio di un record.
- Recupero di un record.
- Aggiornamento di un record.

### <a name="common-setup-code"></a>Codice di installazione comune

Prima di iniziare a usare l'API CloudKit convenience, è necessario un codice di installazione standard. Per iniziare, modificare il file di `AppDelegate.cs` dell'applicazione e ottenere un aspetto simile al seguente:

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
        public override UIWindow Window { get; set;}
        public CKDatabase PublicDatabase { get; set; }
        public CKDatabase PrivateDatabase { get; set; }

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
    }
}
```

Il codice precedente espone i database CloudKit pubblici e privati come collegamenti per facilitarne l'uso nel resto dell'applicazione.

Aggiungere quindi il codice seguente a qualsiasi visualizzazione o contenitore di visualizzazione che utilizzerà CloudKit:

```csharp
using CloudKit;
//...

public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

Verrà aggiunto un collegamento per accedere al `AppDelegate` e accedere ai collegamenti al database pubblico e privato creati in precedenza.

Con questo codice, verrà ora esaminata l'implementazione dell'API CloudKit convenience in un'applicazione Novell iOS 8.

### <a name="saving-a-record"></a>Salvataggio di un record

Usando il modello illustrato in precedenza quando si discutono i record, il codice seguente crea un nuovo record e usa l'API di praticità per salvarlo nel database pubblico:

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

Tre aspetti da considerare sul codice sopra riportato:

1. Chiamando il metodo `SaveRecord` della `PublicDatabase`, lo sviluppatore non deve specificare come vengono inviati i dati, in quale zona viene scritta e così via. L'API di convenienza sta prendendo in considerazione tutti i dettagli.
1. La chiamata è asincrona e fornisce una routine di callback al completamento della chiamata, in caso di esito positivo o negativo. Se la chiamata ha esito negativo, verrà visualizzato un messaggio di errore.
1. CloudKit non fornisce archiviazione/persistenza locale; si tratta solo di un supporto di trasferimento. Quindi, quando viene effettuata una richiesta di salvataggio di un record, quest'operazione viene inviata immediatamente ai server iCloud.

> [!NOTE]
> A causa della natura "lossale" delle comunicazioni di rete mobile, in cui le connessioni vengono costantemente eliminate o interrotte, una delle prime considerazioni che lo sviluppatore deve apportare quando si lavora con CloudKit è la gestione degli errori.

### <a name="fetching-a-record"></a>Recupero di un record

Con un record creato e archiviato correttamente nel server iCloud, usare il codice seguente per recuperare il record:

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

Proprio come nel salvataggio del record, il codice precedente è asincrono, semplice e richiede una gestione degli errori eccezionale.

### <a name="updating-a-record"></a>Aggiornamento di un record

Dopo che un record è stato recuperato dai server iCloud, è possibile usare il codice seguente per modificare il record e salvare di nuovo le modifiche nel database:

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

Il metodo `FetchRecord` della `PublicDatabase` restituisce un `CKRecord` se la chiamata ha avuto esito positivo. L'applicazione modifica quindi il record e chiama di nuovo `SaveRecord` per scrivere nuovamente le modifiche nel database.

Questa sezione ha illustrato il ciclo tipico che un'applicazione userà quando si lavora con l'API CloudKit convenience. L'applicazione salverà i record in iCloud, recupererà i record da iCloud, modificherà i record e salverà le modifiche in iCloud.

## <a name="designing-for-scalability"></a>Progettazione per la scalabilità

Finora questo articolo ha esaminato l'archiviazione e il recupero di un intero modello a oggetti di un'applicazione dai server iCloud, ogni volta che verrà usato. Sebbene questo approccio funzioni bene con una piccola quantità di dati e una base utente molto piccola, non viene ridimensionata correttamente quando la quantità di informazioni e/o di base utente aumenta.

### <a name="big-data-tiny-device"></a>Big data, piccolo dispositivo

Maggiore è la popolarità di un'applicazione, maggiore è il numero di dati nel database e meno fattibile è avere una cache di tutti i dati nel dispositivo. Per risolvere il problema, è possibile usare le tecniche seguenti:

- **Mantieni i dati di grandi dimensioni nel cloud** : CloudKit è stato progettato per gestire in modo efficiente i dati di grandi dimensioni.
- Il **client deve visualizzare solo una sezione di tali dati** , ovvero ridurre il minimo dei dati necessari per gestire le attività in un determinato momento.
- Le **visualizzazioni client possono cambiare** : poiché ogni utente dispone di preferenze diverse, la sezione dei dati visualizzati può variare da utente a utente e la visualizzazione singola di una determinata sezione può essere diversa.
- **Il client usa le query per concentrare il punto di vista** : le query consentono all'utente di visualizzare un piccolo subset di un set di dati più grande esistente all'interno del cloud.

### <a name="queries"></a>Query

Come indicato in precedenza, le query consentono allo sviluppatore di selezionare un piccolo subset del set di dati più grande esistente nel cloud. Le query vengono esposte nel Framework CloudKit tramite la classe `CKQuery`.

Una query combina tre elementi diversi: un tipo di record (`RecordType`), un predicato (`NSPredicate`) e, facoltativamente, un descrittore di ordinamento (`NSSortDescriptors`). CloudKit supporta la maggior parte dei `NSPredicate`.

#### <a name="supported-predicates"></a>Predicati supportati

CloudKit supporta i tipi di `NSPredicates` seguenti quando si utilizzano le query:

1. Record corrispondenti in cui il nome è uguale a un valore archiviato in una variabile:

    ```csharp
    NSPredicate.FromFormat(string.Format("name = '{0}'", recordName))
    ```

2. Consente la corrispondenza in base a un valore di chiave dinamica, in modo che non sia necessario che la chiave sia in fase di compilazione:

    ```csharp
    NSPredicate.FromFormat(string.Format("{0} = '{1}'", key, value))
    ```

3. Record corrispondenti in cui il valore del record è maggiore del valore specificato:

    ```csharp
    NSPredicate.FromFormat(string.Format("start > {0}", (NSDate)date))
    ```

4. Record corrispondenti in cui la posizione del record è entro 100 metri dalla località specificata:

    ```csharp
    var location = new CLLocation(37.783,-122.404);
    var predicate = NSPredicate.FromFormat(string.Format("distanceToLocation:fromLocation(Location,{0}) < 100", location));
    ```

5. CloudKit supporta una ricerca in formato token. Questa chiamata creerà due token, uno per `after` e un altro per `session`. Verrà restituito un record che contiene i due token seguenti:

    ```csharp
    NSPredicate.FromFormat(string.Format("ALL tokenize({0}, 'Cdl') IN allTokens", "after session"))
    ```

6. CloudKit supporta i predicati composti Uniti usando l'operatore `AND`.

    ```csharp
    NSPredicate.FromFormat(string.Format("start > {0} AND name = '{1}'", (NSDate)date, recordName))
    ```

#### <a name="creating-queries"></a>Creazione di query

Il codice seguente può essere usato per creare un `CKQuery` in un'applicazione Novell iOS 8:

```csharp
var recordName = "MyRec";
var predicate = NSPredicate.FromFormat(string.Format("name = '{0}'", recordName));
var query = new CKQuery("CloudRecords", predicate);
```

Innanzitutto, viene creato un predicato per selezionare solo i record che corrispondono a un determinato nome. Viene quindi creata una query che consente di selezionare i record del tipo di record specificato che corrispondono al predicato.

#### <a name="performing-a-query"></a>Esecuzione di una query

Dopo aver creato una query, usare il codice seguente per eseguire la query ed elaborare i record restituiti:

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

Il codice precedente prende la query creata in precedenza e la esegue sul database pubblico. Poiché non viene specificata alcuna zona di record, viene eseguita la ricerca in tutte le zone. Se non si sono verificati errori, verrà restituita una matrice di `CKRecords` corrispondenti ai parametri della query.

Il modo in cui si pensa alle query è che si tratta di polling e sono molto interessanti per il sezionamento di set di impostazioni di grandi dimensioni. Le query, tuttavia, non sono adatte per i set di impostazioni di grandi dimensioni e per lo più statico a causa dei motivi seguenti:

- Non sono validi per la durata della batteria del dispositivo.
- Non sono valide per il traffico di rete.
- L'esperienza utente non è valida perché le informazioni visualizzate sono limitate dalla frequenza con cui l'applicazione esegue il polling del database. Attualmente gli utenti prevedono notifiche push quando qualcosa viene modificato.

### <a name="subscriptions"></a>Sottoscrizioni

Quando si gestiscono set di impostazioni di grandi dimensioni, per la maggior parte statici, la query non deve essere eseguita sul dispositivo client, ma deve essere eseguita sul server per conto del client. La query deve essere eseguita in background e deve essere eseguita dopo il salvataggio di ogni singolo record, indipendentemente dal fatto che il dispositivo corrente o un altro dispositivo tocchi lo stesso database.

Infine, è necessario inviare una notifica push a ogni dispositivo collegato al database durante l'esecuzione della query sul lato server.

Le sottoscrizioni vengono esposte in CloudKit Framework tramite la classe `CKSubscription`. Combinano un tipo di record (`RecordType`), un predicato (`NSPredicate`) e una notifica push Apple (`Push`).

> [!NOTE]
> I push CloudKit sono leggermente aumentati perché contengono un payload contenente informazioni specifiche di CloudKit, ad esempio la causa del push.

#### <a name="how-subscriptions-work"></a>Funzionamento delle sottoscrizioni

Prima di implementare la C# sottoscrizione nel codice, è opportuno eseguire una rapida panoramica del funzionamento delle sottoscrizioni:

 [![](intro-to-cloudkit-images/image39.png "An overview of how subscriptions work")](intro-to-cloudkit-images/image39.png#lightbox)

Il grafico precedente Mostra il processo di sottoscrizione tipico come segue:

1. Il dispositivo client crea una nuova sottoscrizione contenente il set di condizioni che attiverà la sottoscrizione e una notifica push che verrà inviata quando si verifica il trigger.
2. La sottoscrizione viene inviata al database in cui viene aggiunta alla raccolta di sottoscrizioni esistenti.
3. Un secondo dispositivo crea un nuovo record e lo salva nel database.
4. Il database esegue la ricerca nell'elenco di sottoscrizioni per verificare se il nuovo record corrisponde a una delle condizioni.
5. Se viene rilevata una corrispondenza, la notifica push viene inviata al dispositivo che ha registrato la sottoscrizione con le informazioni sul record che ne ha causato l'attivazione.

Con queste informazioni, verrà ora esaminata la creazione di sottoscrizioni in un'applicazione Novell iOS 8.

#### <a name="creating-subscriptions"></a>Creazione di sottoscrizioni

Per creare una sottoscrizione, è possibile usare il codice seguente:

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

Viene innanzitutto creato un predicato che fornisce la condizione per attivare la sottoscrizione. Successivamente, crea la sottoscrizione in base a un tipo di record specifico e imposta l'opzione di quando il trigger viene testato. Infine, definisce il tipo di notifica che si verificherà quando la sottoscrizione viene attivata e la connette alla sottoscrizione.

### <a name="saving-subscriptions"></a>Salvataggio di sottoscrizioni

Con la sottoscrizione creata, il codice seguente lo salva nel database:

```csharp
// Save the subscription to the database
ThisApp.PublicDatabase.SaveSubscription(subscription, (s, err) => {
    // Was there an error?
    if (err != null) {

    }
});
```

Utilizzando l'API di praticità, la chiamata è asincrona, semplice e fornisce una gestione semplice degli errori.

#### <a name="handling-push-notifications"></a>Gestione delle notifiche push

Se lo sviluppatore ha usato in precedenza le notifiche push di Apple (APS), il processo di gestione delle notifiche generate da CloudKit dovrebbe essere familiare.

Nella `AppDelegate.cs`eseguire l'override della classe `ReceivedRemoteNotification` come indicato di seguito:

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

Il codice precedente richiede a CloudKit di analizzare userInfo in una notifica CloudKit. Successivamente, le informazioni vengono estratte sull'avviso. Infine, il tipo di notifica viene testato e la notifica viene gestita di conseguenza.

In questa sezione è stato illustrato come rispondere ai Big data, un piccolo problema di dispositivo presentato sopra usando query e sottoscrizioni. L'applicazione lascerà i dati di grandi dimensioni nel cloud e utilizzerà tali tecnologie per fornire visualizzazioni a questo set di dati.

## <a name="cloudkit-user-accounts"></a>Account utente CloudKit

Come indicato all'inizio di questo articolo, CloudKit si basa sull'infrastruttura iCloud esistente. La sezione seguente illustra in dettaglio come gli account vengono esposti a uno sviluppatore usando l'API CloudKit.

### <a name="authentication"></a>Autenticazione

Quando si gestiscono gli account utente, la prima considerazione è l'autenticazione. CloudKit supporta l'autenticazione tramite l'utente iCloud attualmente connesso sul dispositivo. L'autenticazione viene eseguita dietro le quinte e viene gestita da iOS. In questo modo, gli sviluppatori non devono preoccuparsi dei dettagli dell'implementazione dell'autenticazione. Eseguono solo test per verificare se un utente è connesso.

### <a name="user-account-information"></a>Informazioni sull'account utente

CloudKit fornisce le informazioni utente seguenti allo sviluppatore:

- **Identity** : un modo per identificare l'utente in modo univoco.
- **Metadati** : la possibilità di salvare e recuperare informazioni sugli utenti.
- **Privacy** : tutte le informazioni sono gestite in una residenza privata consapevole. Nessun elemento viene esposto, a meno che l'utente non lo abbia accettato.
- **Individuazione** : consente agli utenti di individuare i propri amici che usano la stessa applicazione.

Successivamente, questi argomenti vengono esaminati in dettaglio.

#### <a name="identity"></a>Identity

Come indicato in precedenza, CloudKit consente all'applicazione di identificare in modo univoco un determinato utente:

 [![](intro-to-cloudkit-images/image40.png "Uniquely identifing a given user")](intro-to-cloudkit-images/image40.png#lightbox)

È presente un'applicazione client in esecuzione nei dispositivi di un utente e in tutti i database privati utente specifici all'interno del contenitore CloudKit. L'applicazione client sarà collegata a uno di questi utenti specifici. Questa operazione è basata sull'utente che ha eseguito l'accesso a iCloud localmente sul dispositivo.

Dal momento che provengono da iCloud, è disponibile un archivio di backup completo delle informazioni sugli utenti. Poiché iCloud ospita effettivamente il contenitore, può correlare gli utenti. Nell'immagine precedente, l'utente il cui account iCloud `user@icloud.com` è collegato al client corrente.

In un contenitore in base al contenitore viene creato un ID utente univoco, generato in modo casuale, associato all'account iCloud dell'utente (indirizzo di posta elettronica). Questo ID utente viene restituito all'applicazione e può essere usato in qualsiasi modo lo sviluppatore ritiene appropriato.

> [!NOTE]
> Diverse applicazioni in esecuzione sullo stesso dispositivo per lo stesso utente iCloud avranno ID utente diversi perché sono connesse a contenitori CloudKit diversi.

Il codice seguente ottiene l'ID utente CloudKit per l'utente iCloud attualmente connesso sul dispositivo:

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

Il codice precedente chiede al contenitore CloudKit di fornire l'ID dell'utente attualmente connesso. Poiché queste informazioni provengono dal server iCloud, la chiamata è asincrona e la gestione degli errori è obbligatoria.

#### <a name="metadata"></a>Metadati

Ogni utente di CloudKit dispone di metadati specifici che li descrivono. Questi metadati sono rappresentati come record CloudKit:

 [![](intro-to-cloudkit-images/image41.png "Each user in CloudKit has specific Metadata that describes them")](intro-to-cloudkit-images/image41.png#lightbox)

Ricerca all'interno del database privato per un utente specifico di un contenitore è presente un record che definisce tale utente. All'interno del database pubblico sono presenti molti record utente, uno per ogni utente del contenitore. Uno di questi avrà un ID record corrispondente all'ID record dell'utente attualmente connesso.

I record utente nel database pubblico sono leggibili in tutto il mondo. Sono trattati, nella maggior parte dei casi, come record ordinari e hanno un tipo di `CKRecordTypeUserRecord`. Questi record sono riservati dal sistema e non sono disponibili per le query.

Per accedere a un record utente, usare il codice seguente:

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

Il codice precedente chiede al database pubblico di restituire il record utente per l'ID utente a cui è stato effettuato l'accesso. Poiché queste informazioni provengono dal server iCloud, la chiamata è asincrona e la gestione degli errori è obbligatoria.

#### <a name="privacy"></a>Privacy

Per impostazione predefinita, CloudKit è stato progettato per proteggere la privacy dell'utente attualmente connesso. Per impostazione predefinita, non vengono esposte informazioni personali sull'utente. In alcuni casi l'applicazione richiederà informazioni limitate sull'utente.

In questi casi, l'applicazione può richiedere che l'utente divulga le informazioni. Verrà visualizzata una finestra di dialogo che chiede all'utente di acconsentire esplicitamente all'esposizione delle informazioni relative all'account.

#### <a name="discovery"></a>Individuazione

Supponendo che l'utente come acconsentito a consentire all'applicazione di accedere in modo limitato alle informazioni dell'account utente, può essere individuato da altri utenti dell'applicazione:

 [![](intro-to-cloudkit-images/image42.png "A user can be discoverable to other users of the application")](intro-to-cloudkit-images/image42.png#lightbox)

L'applicazione client sta comunicando con un contenitore e il contenitore sta comunicando iCloud per accedere alle informazioni dell'utente. L'utente può specificare un indirizzo di posta elettronica e l'individuazione può essere usata per ottenere informazioni sull'utente. Facoltativamente, l'ID utente può essere utilizzato anche per individuare le informazioni relative all'utente.

CloudKit consente inoltre di individuare informazioni sugli utenti che potrebbero essere amici dell'utente iCloud attualmente connesso eseguendo una query sull'intera Rubrica. Il processo CloudKit eseguirà il pull del libro di contatto dell'utente e userà gli indirizzi di posta elettronica per verificare se è possibile trovare altri utenti dell'applicazione che corrispondono a tali indirizzi.

Ciò consente all'applicazione di sfruttare il libro di contatto dell'utente senza fornire l'accesso o chiedere all'utente di approvare l'accesso ai contatti. In nessun momento le informazioni di contatto rese disponibili per l'applicazione sono accessibili solo al processo CloudKit.

Per riepilogare, sono disponibili tre tipi diversi di input per l'individuazione utente:

- **ID record utente** : l'individuazione può essere eseguita con l'ID utente dell'utente attualmente connesso CloudKit.
- **Indirizzo di posta elettronica dell'utente** : l'utente può specificare un indirizzo di posta elettronica e può essere usato per l'individuazione.
- **Contact Book** : la rubrica dell'utente può essere usata per individuare gli utenti dell'applicazione che hanno lo stesso indirizzo di posta elettronica elencato nei contatti.

L'individuazione utente restituirà le informazioni seguenti:

- **ID record utente** : ID univoco di un utente nel database pubblico.
- **Nome e cognome** : archiviati nel database pubblico.

Queste informazioni verranno restituite solo per gli utenti che hanno optato per l'individuazione.

Il codice seguente rileverà informazioni sull'utente attualmente connesso a iCloud sul dispositivo:

```csharp
public CKDiscoveredUserInfo UserInfo { get; set; }
//...

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

Usare il codice seguente per eseguire una query su tutti gli utenti del libro contatti:

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

In questa sezione sono state descritte le quattro principali aree di accesso all'account di un utente che CloudKit può fornire a un'applicazione. Da ottenere l'identità e i metadati dell'utente, i criteri di privacy incorporati in CloudKit e infine la possibilità di individuare altri utenti dell'applicazione.

## <a name="the-development-and-production-environments"></a>Ambienti di sviluppo e produzione

CloudKit fornisce ambienti di sviluppo e produzione distinti per i tipi di record e i dati di un'applicazione. L'ambiente di sviluppo è un ambiente più flessibile, disponibile solo per i membri di un team di sviluppo. Quando un'applicazione aggiunge un nuovo campo a un record e salva tale record nell'ambiente di sviluppo, il server aggiorna automaticamente le informazioni sullo schema.

Lo sviluppatore può usare questa funzionalità per apportare modifiche a uno schema durante lo sviluppo, per risparmiare tempo. Un avvertimento è che dopo l'aggiunta di un campo a un record, il tipo di dati associato a tale campo non può essere modificato a livello di codice. Per modificare il tipo di un campo, lo sviluppatore deve eliminare il campo nel [dashboard di CloudKit](https://icloud.developer.apple.com/dashboard/) e aggiungerlo di nuovo con il nuovo tipo.

Prima di distribuire l'applicazione, lo sviluppatore può eseguire la migrazione dello schema e dei dati nell'ambiente di produzione usando il **dashboard di CloudKit**. Quando si esegue l'ambiente di produzione, il server impedisce a un'applicazione di modificare lo schema a livello di codice. Lo sviluppatore può comunque apportare modifiche con il **dashboard di CloudKit** , ma i tentativi di aggiungere campi a un record nell'ambiente di produzione generano errori.

> [!NOTE]
> Il simulatore iOS funziona solo con l' **ambiente di sviluppo**. Quando lo sviluppatore è pronto per testare un'applicazione in un **ambiente di produzione**, è necessario un dispositivo iOS fisico.

## <a name="shipping-a-cloudkit-enabled-app"></a>Spedizione di un'app abilitata per CloudKit

Prima di distribuire un'applicazione che usa CloudKit, è necessario configurarla in modo che abbia come destinazione l' **ambiente CloudKit di produzione** o l'applicazione verrà rifiutata da Apple.

eseguire le operazioni descritte di seguito.

1. In Visual Studio per ma compilare l'applicazione per la **versione** > **dispositivo iOS**:

    [![](intro-to-cloudkit-images/shipping01.png "Compile the application for Release")](intro-to-cloudkit-images/shipping01.png#lightbox)

2. Scegliere **Archivia**dal menu **Compila** :

    [![](intro-to-cloudkit-images/shipping02.png "Select Archive")](intro-to-cloudkit-images/shipping02.png#lightbox)

3. L' **Archivio** verrà creato e visualizzato in Visual Studio per Mac:

    [![](intro-to-cloudkit-images/shipping03.png "The Archive will be created and displayed")](intro-to-cloudkit-images/shipping03.png#lightbox)

4. Avviare **Xcode**.
5. Scegliere **libreria**dal menu **finestra** :

    [![](intro-to-cloudkit-images/shipping04.png "Select Organizer")](intro-to-cloudkit-images/shipping04.png#lightbox)

6. Selezionare l'archivio dell'applicazione e fare clic sul pulsante **Esporta...** :

    [![](intro-to-cloudkit-images/shipping05.png "The application's archive")](intro-to-cloudkit-images/shipping05.png#lightbox)

7. Selezionare un metodo per l'esportazione e fare clic sul pulsante **Avanti** :

    [![](intro-to-cloudkit-images/shipping06.png "Select a method for export")](intro-to-cloudkit-images/shipping06.png#lightbox)

8. Selezionare il **team di sviluppo** dall'elenco a discesa e fare clic sul pulsante **Choose (Scegli** ):

    [![](intro-to-cloudkit-images/shipping07.png "Select the Development Team from the dropdown list")](intro-to-cloudkit-images/shipping07.png#lightbox)

9. Selezionare **produzione** nell'elenco a discesa e fare clic sul pulsante **Avanti** :

    [![](intro-to-cloudkit-images/shipping08.png "Select Production from the dropdown list")](intro-to-cloudkit-images/shipping08.png#lightbox)

10. Verificare l'impostazione e fare clic sul pulsante **Esporta** :

    [![](intro-to-cloudkit-images/shipping09.png "Review the setting")](intro-to-cloudkit-images/shipping09.png#lightbox)

11. Scegliere un percorso per generare il file di `.ipa` dell'applicazione risultante.

Il processo è simile per l'invio dell'applicazione direttamente a iTunes Connect. è sufficiente fare clic sul pulsante **Submit (Invia** ) invece di Esporta... Dopo aver selezionato un archivio nella finestra libreria.

## <a name="when-to-use-cloudkit"></a>Quando usare CloudKit

Come illustrato in questo articolo, CloudKit consente a un'applicazione di archiviare e recuperare informazioni dai server iCloud in modo semplice. Detto questo, CloudKit non è obsoleto o depreca uno degli strumenti o dei Framework esistenti.

### <a name="use-cases"></a>Casi d'uso

I casi d'uso seguenti dovrebbero aiutare gli sviluppatori a decidere quando usare una tecnologia o un Framework iCloud specifico:

- **Archivio chiave-valore iCloud** : mantiene in modo asincrono una piccola quantità di dati aggiornata ed è ideale per l'uso delle preferenze dell'applicazione. Tuttavia, è vincolato per una piccola quantità di informazioni.
- **unità iCloud** : basata sulle API dei documenti iCloud esistenti e fornisce un'API semplice per sincronizzare i dati non strutturati dal file System. Fornisce una cache offline completa su Mac OS X ed è ideale per le applicazioni incentrate sui documenti.
- **iCloud Core Data** : consente la replica dei dati tra tutti i dispositivi dell'utente. I dati sono un utente singolo ed è ideale per mantenere sincronizzati i dati strutturati e privati.
- **CloudKit** : fornisce dati pubblici sia struttura che bulk ed è in grado di gestire sia set di dati di grandi dimensioni che file non strutturati di grandi dimensioni. Il relativo oggetto è associato all'account iCloud dell'utente e fornisce il trasferimento dati diretto del client.

Tenendo presenti questi casi d'uso, lo sviluppatore deve scegliere la tecnologia iCloud corretta per fornire la funzionalità dell'applicazione richiesta corrente e garantire una scalabilità adeguata per la crescita futura.

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato una rapida introduzione all'API CloudKit. È stato illustrato come eseguire il provisioning e configurare un'applicazione Novell iOS per l'uso di CloudKit. Ha trattato le funzionalità dell'API CloudKit convenience. Mostra come progettare un'applicazione abilitata per CloudKit per la scalabilità usando query e sottoscrizioni. Infine, ha mostrato le informazioni sull'account utente esposte a un'applicazione da CloudKit.

## <a name="related-links"></a>Collegamenti correlati

- [CloudKit (Apple)](https://developer.apple.com/icloud/cloudkit/)
- [CloudKitAtlas (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-cloudkitatlas)
- [Creazione di un profilo di provisioning](~/ios/get-started/installation/device-provisioning/index.md)
