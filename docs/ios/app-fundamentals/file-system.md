---
title: Accesso al file system in xamarin. IOS
description: Questo documento descrive come usare i file system in xamarin. IOS. Vengono illustrati le directory, la lettura di file, la serializzazione XML e JSON, sandbox dell'applicazione, la condivisione di file tramite iTunes e altro ancora.
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/12/2018
ms.openlocfilehash: 09e05fcfe10a994e14aa605b203ea67efae80d62
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672625"
---
# <a name="file-system-access-in-xamarinios"></a>Accesso al file system in xamarin. IOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/FileSystemSampleCode/)

È possibile usare xamarin. IOS e il `System.IO` le classi di *libreria di classi Base (BCL) .NET* per accedere al file system iOS. La classe `File` consente di creare, eliminare e leggere i file e la classe `Directory` consente di creare, eliminare o enumerare il contenuto delle directory. È anche possibile usare `Stream` sottoclassi, che possono fornire un livello maggiore di controllo sulle operazioni di file (ad esempio, cercare la compressione o la posizione all'interno di un file).

iOS impone alcune limitazioni sulle funzionalità di un'applicazione con il file system per mantenere la sicurezza dei dati di un'applicazione e per proteggere gli utenti dalle App maligni. Queste restrizioni fanno parte del *dell'applicazione Sandbox* : un set di regole che limita l'accesso di un'applicazione a file, le preferenze, risorse di rete, hardware, e così via. Un'applicazione è limitata alla lettura e scrittura di file all'interno di relativa home directory (percorso di installazione); non può accedere a file dell'applicazione.

iOS ha anche alcune funzionalità specifiche del sistema di file: alcune directory richiedono un trattamento speciale rispetto al backup e gli aggiornamenti e le applicazioni possono inoltre condividere file tra loro e il **file** app (da iOS 11) e tramite iTunes.

Questo articolo illustra le funzionalità e restrizioni di iOS file system e include un'applicazione di esempio che illustra come usare xamarin. IOS per eseguire alcune operazioni di sistema di file semplice:

[![Un esempio di iOS l'esecuzione di alcune operazioni di sistema di file semplice](file-system-images/01-sampleapp-sml.png)](file-system-images/01-sampleapp.png#lightbox)

## <a name="general-file-access"></a>Accesso ai file generale

Xamarin. IOS consente di usare .NET `System.IO` classi per operazioni del file system in iOS.

I frammenti di codice seguenti illustrano alcune operazioni su file comuni. Troverai esse tutto sotto il **SampleCode.cs** file, nell'applicazione di esempio per questo articolo.

### <a name="working-with-directories"></a>Lavorare con le directory

Questo codice enumera le sottodirectory nella directory corrente (specificato da di ". /" parametro), ovvero il percorso del file eseguibile dell'applicazione.
L'output sarà un elenco di tutti i file e cartelle che vengono distribuite con l'applicazione (visualizzato nella finestra della console mentre si esegue il debug).

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

### <a name="reading-files"></a>La lettura dei file

Per leggere un file di testo, è sufficiente una singola riga di codice. In questo esempio verrà visualizzato il contenuto di un file di testo nella finestra di Output dell'applicazione.

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

### <a name="xml-serialization"></a>serializzazione XML

Anche se utilizzano l'intero `System.Xml` dello spazio dei nomi non rientra nell'ambito di questo articolo, è possibile deserializzarli facilmente un documento XML dal file system usando un oggetto StreamReader come frammento di codice seguente:

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

Per altre informazioni, vedere la documentazione per [System. XML](xref:System.Xml) e [serializzazione](xref:System.Xml.Serialization). Vedere le [documentazione di xamarin. IOS](~/ios/deploy-test/linker.md) sul linker, spesso è necessario aggiungere il `[Preserve]` attributo alle classi di cui si desidera serializzare.

### <a name="creating-files-and-directories"></a>Creazione di file e directory

In questo esempio viene illustrato come utilizzare il `Environment` classe per accedere alla cartella documenti in cui è possibile creare file e directory.

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

Creazione di una directory è un processo simile:

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

Per altre informazioni vedere la [riferimento all'API di System.IO](xref:System.IO).

### <a name="serializing-json"></a>La serializzazione JSON

[Json.NET](http://www.newtonsoft.com/json) è un framework JSON a prestazioni elevate che funziona con xamarin. IOS ed è disponibile in NuGet. Aggiungere il pacchetto NuGet per l'applicazione del progetto, utilizzando **NuGet aggiungere** in Visual Studio per Mac:

[![](file-system-images/json01.png "Aggiunta del pacchetto NuGet al progetto di applicazioni")](file-system-images/json01.png#lightbox)

Successivamente, aggiungere una classe da usare come modello di dati per la serializzazione/deserializzazione (in questo caso `Account.cs`):

```csharp
using System;
using System.Collections.Generic;
using Foundation; // for Preserve attribute, which helps serialization with Linking enabled

namespace FileSystem
{
    [Preserve]
    public class Account
    {
        public string Email { get; set; }
        public bool Active { get; set; }
        public DateTime CreatedDate { get; set; }
        public List<string> Roles { get; set; }

        public Account() {
        }
    }
}
```

Infine, creare un'istanza di `Account` classe, eseguirne la serializzazione per i dati json e scriverla in un file:

```csharp
// Create a new record
var account = new Account(){
    Email = "monkey@xamarin.com",
    Active = true,
    CreatedDate = new DateTime(2015, 5, 27, 0, 0, 0, DateTimeKind.Utc),
    Roles = new List<string> {"User", "Admin"}
};

// Serialize object
var json = JsonConvert.SerializeObject(account, Newtonsoft.Json.Formatting.Indented);

// Save to file
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "account.json");
File.WriteAllText(filename, json);
```

Per altre informazioni sull'utilizzo di dati json in un'applicazione .NET, vedere la sezione Json .NET [documentazione](http://www.newtonsoft.com/json/help).

## <a name="special-considerations"></a>Considerazioni speciali

Malgrado le somiglianze tra xamarin. IOS e operazioni sui file di .NET, iOS e xamarin. IOS sono diversi da .NET alcune importanti differenze.

### <a name="making-project-files-accessible-at-runtime"></a>Rendere accessibile in fase di esecuzione i file di progetto

Per impostazione predefinita, se si aggiunge un file al progetto, non verranno incluse nell'assembly finale e pertanto non sarà disponibile per l'applicazione. Per includere un file nell'assembly, è necessario contrassegnarla con un'azione di compilazione speciale, chiamata contenuto.

Per contrassegnare un file di inclusione, fare clic sul file e scegliere **Build Action &gt; contenuto** in Visual Studio per Mac. È inoltre possibile modificare il **Build Action** del file **proprietà** foglio.

### <a name="case-sensitivity"></a>Distinzione fra maiuscole e minuscole

È importante comprendere che il file system iOS fa *distinzione maiuscole/minuscole*. Distinzione maiuscole/minuscole significa che i nomi di file e directory devono corrispondere esattamente – **Readme. txt** e **Readme. txt** devono essere considerati diversi nomi di file.

Ciò potrebbe generare confusione per gli sviluppatori .NET che hanno maggiore familiari con il file system Windows, ovvero *maiuscole e minuscole* – **file**, **file**, e  **file** tutti per fare riferimento alla stessa directory.

> [!WARNING]
> IOS Simulator non è tra maiuscole e minuscole.
> Se la distinzione tra maiuscole e filename è diverso tra il file stesso e i relativi riferimenti nel codice, il codice potrebbe comunque funzionare nel simulatore, ma non verrà completata in un dispositivo reale. Questo è uno dei motivi per cui è importante distribuire e testare su un dispositivo reale anticipato e spesso durante lo sviluppo di iOS.

### <a name="path-separator"></a>Separatore di percorso

iOS Usa la barra rovesciata '/' come separatore di percorso (che è diverso da Windows, che usa la barra rovesciata ' \').

A causa di questa differenza confusa, è consigliabile usare il `System.IO.Path.Combine` (metodo), che consente di regolare per la piattaforma corrente, anziché impostare come hardcoded un separatore di percorso specifico. Si tratta di un semplice passaggio che rende il codice più portabile da altre piattaforme.

## <a name="application-sandbox"></a>Sandbox dell'applicazione

Accesso dell'applicazione per il file system (e altre risorse, ad esempio le funzionalità di rete e hardware) è limitato per motivi di sicurezza. Questa restrizione è nota come le *dell'applicazione Sandbox*. In termini di file system, l'applicazione è limitata alla creazione e l'eliminazione di file e directory nella relativa home directory.

La home directory è un percorso univoco del file System in cui sono archiviati l'applicazione e i relativi dati. Non è possibile scegliere (o modificare) il percorso della home directory dell'applicazione. iOS e xamarin. IOS tuttavia fornire le proprietà e metodi per la gestione di file e directory all'interno.

## <a name="the-application-bundle"></a>Il bundle dell'applicazione

Il *Bundle dell'applicazione* è la cartella che contiene l'applicazione.
Si distingue dalle altre cartelle con il suffisso App aggiunto al nome della directory. Bundle dell'applicazione contiene il file eseguibile e tutto il contenuto (file, immagini, e così via) necessario per il progetto.

Quando si passa al bundle dell'applicazione in Mac OS, viene visualizzato con un'icona diversa rispetto a vedrai in altre directory (e il **file con estensione app** suffisso è nascosta); tuttavia, è solo una directory di regolare in cui viene visualizzato il sistema operativo in modo diverso.

Per visualizzare il bundle dell'applicazione per il codice di esempio, pulsante destro del mouse sul progetto in **Visual Studio per Mac** e selezionare **Visualizza in Finder**. Selezionare il **bin /** directory in cui si deve trovare un'icona dell'applicazione (simile allo screenshot seguente).

![Spostarsi nella directory bin per trovare l'icona di un'applicazione simile a questo screenshot](file-system-images/40-bundle.png)

Fare clic su questa icona e scegliere **Mostra contenuti pacchetto** per esplorare il contenuto della directory di Bundle dell'applicazione. Il contenuto viene visualizzato esattamente come il contenuto di una directory regolare, come illustrato di seguito:

[![Il contenuto del bundle dell'app](file-system-images/45-bundle-sml.png)](file-system-images/45-bundle.png#lightbox)

Il bundle dell'applicazione è ciò che viene installato in un simulatore o sul dispositivo durante il test e in definitiva è ciò che viene inviata ad Apple per l'inclusione in Store l'App.

## <a name="application-directories"></a>Directory delle applicazioni

Quando l'applicazione viene installata in un dispositivo, il sistema operativo crea una home directory per l'applicazione e crea un numero di directory all'interno della directory radice dell'applicazione che sono disponibili per l'uso. Poiché iOS 8, la directory accessibili all'utente che si trovano [non si trova](https://developer.apple.com/library/ios/technotes/tn2406/_index.html) nella radice dell'applicazione, pertanto non puoi derivare i percorsi per il bundle dell'applicazione, la directory degli utenti o viceversa.

Queste directory, di seguito sono elencati come determinare il percorso e le relative funzioni:

&nbsp;

|Directory|Descrizione|
|---|---|
|File con estensione App [ApplicationName] /|**In iOS 7 e versioni precedenti**, si tratta di `ApplicationBundle` directory in cui è archiviato il file eseguibile dell'applicazione. La struttura di directory che crea nell'app esistente in questa directory (ad esempio, immagini e altri tipi di file che è stata contrassegnata come risorse in Visual Studio per il progetto Mac).<br /><br />Se è necessario accedere ai file di contenuto all'interno del Bundle dell'applicazione, il percorso di questa directory è disponibile tramite il `NSBundle.MainBundle.BundlePath` proprietà.|
|I documenti /|Usare questa directory per archiviare i documenti dell'utente e i file di dati dell'applicazione.<br /><br />Il contenuto di questa directory può essere reso disponibile all'utente tramite iTunes file condivisione (anche se è disabilitata per impostazione predefinita). Aggiungere un `UIFileSharingEnabled` chiave booleana nel file Info. plist per consentire agli utenti di accedere a tali file.<br /><br />Anche se un'applicazione non attiva immediatamente la condivisione di file, è consigliabile evitare il posizionamento dei file che devono essere nascosto agli utenti in questa directory (ad esempio i file di database, a meno che non si intende condividere). Fino a quando i file riservati di rimangano nascosti, questi file non essere esposte (e potenzialmente spostati, modificate o eliminate dall'iTunes) se è abilitata la condivisione di file in una versione futura.<br /><br /> È possibile usare il `Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)` metodo per ottenere il percorso della directory documenti per l'applicazione.<br /><br />Il contenuto di questa directory viene eseguito il backup da iTunes.|
|Library /|La directory delle librerie è ideale per archiviare i file che non vengono creati direttamente dall'utente, ad esempio database o altri file generati dall'applicazione. Il contenuto di questa directory non è mai esposti all'utente tramite iTunes.<br /><br />È possibile creare il proprio sottodirectory nella libreria. Tuttavia, esistono già alcuni creato dal sistema le directory qui che è necessario tenere in considerazione, tra cui preferenze e le cache.<br /><br />Il contenuto di questa directory (ad eccezione della sottodirectory delle cache) viene eseguito il backup da iTunes. Le directory personalizzate create in libreria di sottoporre a backup.|
|Library/preferenze /|In questa directory vengono archiviati i file di preferenze specifiche dell'applicazione. Non creare direttamente i file. Usare invece il `NSUserDefaults` classe.<br /><br />Il contenuto di questa directory viene eseguito il backup da iTunes.|
|Library/cache /|La directory cache è ideale per archiviare i file di dati che consentono all'applicazione eseguire, ma che può essere facilmente ricreata. L'applicazione deve creare ed eliminare questi file in base alle esigenze e in grado di ricreare tali file, se necessario. iOS 5 può anche eliminare questi file (in situazioni di archiviazione ridotti), tuttavia, non ripeterà questa operazione mentre è in esecuzione l'applicazione.<br /><br />Il contenuto di questa directory non viene eseguito da iTunes, ovvero che non sarà presente se si ripristina un dispositivo e potrebbe non essere presente dopo aver installata una versione aggiornata dell'applicazione.<br /><br />Ad esempio, nel caso in cui l'applicazione non è possibile connettersi alla rete, è possibile utilizzare la directory cache per archiviare dati o file per fornire un'esperienza ottimale offline. L'applicazione è possibile salvare e recuperare rapidamente i dati durante l'attesa di risposte di rete, ma non è necessario eseguire il backup e può facilmente essere recuperato o creato nuovamente dopo un ripristino o la versione degli aggiornamenti.|
|tmp/|Le applicazioni possono archiviare i file temporanei che sono necessari solo per un breve periodo in questa directory. Per risparmiare spazio, i file devono essere eliminati quando non sono più necessari. Quando un'applicazione non è in esecuzione, il sistema operativo può anche eliminare file da questa directory.<br /><br />Il contenuto di questa directory non viene eseguito il backup da iTunes.<br /><br />Ad esempio, la directory tmp potrebbe essere utilizzata per archiviare i file temporanei che vengono scaricati da visualizzare all'utente (ad esempio gli avatar Twitter o allegati di posta elettronica), ma che può essere eliminata dopo che aver stati visualizzati (e scaricati di nuovo se sono necessarie in futuro) .|

In questo screenshot Mostra la struttura di directory in una finestra del Finder:

[![](file-system-images/08-library-directory.png "In questo screenshot Mostra la struttura di directory in una finestra del Finder")](file-system-images/08-library-directory.png#lightbox)

### <a name="accessing-other-directories-programmatically"></a>L'accesso ad altre directory a livello di codice

Gli esempi precedenti relativi a file e directory accessibili le `Documents` directory. Per scrivere in un'altra directory, è necessario costruire un percorso usando il ".." sintassi come illustrato di seguito:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

Creazione di una directory è simile:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

I percorsi per i `Caches` e `tmp` directory possono essere costruite come segue:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

## <a name="sharing-with-the-files-app"></a>La condivisione con l'app di file

iOS 11 introdotto il **file** app - un browser di file per iOS che consente all'utente di visualizzare e interagire con i relativi file in iCloud e archiviati anche da qualsiasi applicazione che lo supporta. Per consentire all'utente di accedere direttamente ai file dell'app, creare una nuova chiave booleana nel **Info. plist** file `LSSupportsOpeningDocumentsInPlace` e impostarlo su `true`, come di seguito:

![Impostare LSSupportsOpeningDocumentsInPlace nel file Info. plist](file-system-images/51-supports-opening.png)

L'app **documenti** directory saranno ora disponibile per l'esplorazione nel **file** app. Nel **file** app, passare alla **nella mio iPhone** e ogni app con i file condivisi sarà visibile. Gli screenshot seguenti mostrano cosa il [app di esempio del file System](https://developer.xamarin.com/samples/monotouch/FileSystemSampleCode/) simile:

![app di iOS 11 file](file-system-images/50-files-app-1-sml.png) ![Sfoglia file my iPhone](file-system-images/50-files-app-2-sml.png) ![File di app di esempio](file-system-images/50-files-app-3-sml.png)

## <a name="sharing-files-with-the-user-through-itunes"></a>Condivisione di file con l'utente tramite iTunes

Gli utenti possono accedere ai file nella directory dei documenti dell'applicazione modificando `Info.plist` e la creazione di un' **dell'applicazione che supporta la condivisione in iTunes** (`UIFileSharingEnabled`) voce il **origine** visualizzare, come illustrato di seguito:

[![Aggiungere l'applicazione supporta la condivisione di proprietà di iTunes](file-system-images/09-uifilesharingenabled-plist-sml.png)](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

Questi file sono accessibili in iTunes quando il dispositivo è connesso e l'utente sceglie il `Apps` scheda. Ad esempio, lo screenshot seguente mostra i file nell'app selezionata condivisi tramite iTunes:

[![Questo screenshot Mostra i file nell'app selezionata condivisi tramite iTunes](file-system-images/10-itunes-file-sharing-sml.png)](file-system-images/10-itunes-file-sharing.png#lightbox)

Gli utenti possono accedere solo gli elementi di primo livello in questa directory tramite iTunes. Non possono vedere il contenuto di tutte le sottodirectory (anche se è possibile copiarli nei propri computer o eliminarli). Ad esempio, con GoodReader, file PDF e EPUB possono essere condivisi con l'applicazione in modo che gli utenti possono leggere nei dispositivi iOS.

Agli utenti che modificano il contenuto del loro cartella documenti possono causare problemi se non si presta attenzione. L'applicazione deve tenere in considerazione questo ed essere resiliente agli aggiornamenti distruttivi della cartella documenti.

Il codice di esempio per questo articolo consente di creare un file e una cartella nella cartella documenti (in **SampleCode.cs**) e Attiva condivisione file nei **Info. plist** file. In questo screenshot illustra come questi elementi vengono visualizzati in iTunes:

[![Questo screenshot Mostra come i file vengono visualizzate in iTunes](file-system-images/15-itunes-file-sharing-example-sml.png)](file-system-images/15-itunes-file-sharing-example.png#lightbox)

Fare riferimento al [utilizzo di immagini](~/ios/app-fundamentals/images-icons/index.md) articolo per informazioni su come impostare icone per l'applicazione e per qualsiasi tipo di documento personalizzate create.

Se il `UIFileSharingEnabled` chiave è false o non è presente, quindi la condivisione di file, impostazione predefinita è disabilitato, e gli utenti non saranno in grado di interagire con le directory dei documenti.

## <a name="backup-and-restore"></a>Backup e ripristino

Quando un dispositivo viene eseguito il backup da iTunes, tutte le directory create nella directory home dell'applicazione verranno salvate tranne le seguenti directory:

- **File con estensione App [ApplicationName]** – non vengono scritte in questa directory, poiché è firmato e pertanto deve rimanere invariata dopo l'installazione. Può contenere risorse a cui accedere dal codice, ma non richiedono il backup perché è necessario eseguirne il ripristino da scaricare di nuovo l'app.
- **Library/cache** : la directory della cache è destinata a file di lavoro che non sono necessario eseguire il backup.
- **TMP** : questa directory viene usata per i file temporanei che vengono creati ed eliminati quando non sono più necessari o per i file che iOS Elimina quando ha bisogno di spazio.

Backup di una grande quantità di dati può richiedere molto tempo. Se si decide che è necessario eseguire il backup di eventuali dati o un particolare documento, l'applicazione deve usare usare documenti e le cartelle della libreria. Per i dati temporanei o i file che possono essere facilmente recuperati dalla rete, usare la cache o la directory tmp.

> [!NOTE]
> iOS verrà 'clean' nel file System quando un dispositivo viene eseguito insufficiente spazio su disco.
> Questo processo rimuoverà tutti i file dalla libreria/cache e tmp cartella delle applicazioni che non sono attualmente in esecuzione.

## <a name="complying-with-ios-5-icloud-backup-restrictions"></a>Conforme alle restrizioni sul backup di iCloud 5 iOS

> [!NOTE]
> Anche se questo criterio è stato inizialmente introdotto con iOS 5 (che sembra molto tempo fa) il materiale sussidiario è tuttora rilevante per le app oggi stesso.

Apple ha introdotto *iCloud Backup* funzionalità con iOS 5. Quando è abilitato il Backup di iCloud, tutti i file nella directory home dell'applicazione (a esclusione delle directory che non sono supportate backup, ad esempio, il bundle dell'app, in genere `Caches`, e `tmp`) vengono sottoposti a backup in iCloud server. Questa funzionalità fornisce all'utente con un backup completo nel caso in cui il dispositivo è smarrimento, furto o danneggiato.

Poiché iCloud offre solo 5 Gb di spazio "free" per ogni utente e per evitare di usare inutilmente la larghezza di banda, Apple si aspetta che le applicazioni a solo backup essenziale dati generati dall'utente. Per garantire la conformità con le linee guida sull'archiviazione dei dati iOS, è necessario limitare la quantità di dati di backup aderendo ai seguenti elementi:

- Archiviare solo dati generati dall'utente o dati che in caso contrario, non possono essere creati nuovamente, nella directory Documents (ovvero sottoposti a backup).
- Store eventuali altri dati che possono essere facilmente ricreati o scaricati nuovamente nel `Library/Caches` o `tmp` (che non viene eseguito il backup e potrebbe essere 'eliminate').
- Se si dispone di file che potrebbero essere appropriati per il `Library/Caches` o `tmp` cartella ma non si desidera 'eliminato' out, archiviarli in un' posizione (come `Library/YourData`) e applicare il ' non viene eseguito il backup ' attributo per impedire che i file con backup di iCloud Backup larghezza di banda e spazio di archiviazione. Questi dati si usano ancora spazio sul dispositivo, in modo che si deve gestirlo con attenzione ed eliminarlo quando possibile.

I ' non viene eseguito il backup ' attributo viene impostato utilizzando il `NSFileManager` classe. Verificare che la classe sia `using Foundation` e chiamare `SetSkipBackupAttribute` simile al seguente:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

Quando `SetSkipBackupAttribute` viene `true` il file non verrà eseguito il backup, indipendentemente dalla directory in cui è memorizzato (anche sul `Documents` directory). È possibile eseguire una query usando l'attributo il `GetSkipBackupAttribute` metodo ed è possibile ripristinare le impostazioni chiamando il `SetSkipBackupAttribute` metodo con `false`, simile al seguente:

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>Condivisione dei dati tra App iOS e le estensioni dell'app

Poiché le estensioni dell'App vengono eseguiti come parte di un'applicazione host (in contrapposizione le app che lo contiene), la condivisione dei dati non è automatica incluso in modo che sono necessarie ulteriori operazioni. I gruppi di App sono che utilizza il meccanismo iOS per consentire a diverse app di condividere i dati. Se le applicazioni sono state configurate correttamente con i diritti corretti e il provisioning, possono accedere una directory condivisa all'esterno di sandbox iOS normale.

### <a name="configure-an-app-group"></a>Configurare un gruppo di App

Il percorso condiviso viene configurato usando un [gruppo di App](https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), in cui viene configurato il **certificati, identificatori e profili** sezione [iOS Dev Center](https://developer.apple.com/devcenter/ios/). Questo valore è necessario fare riferimento anche in ogni progetto **entitlements. plist**.

Per informazioni sulla creazione e configurazione di un gruppo di App, vedere la [le funzionalità di gruppi di App](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) Guida.

### <a name="files"></a>File

L'app per iOS e l'estensione può anche condividere i file usando un percorso di file comuni (dato sono state correttamente configurate con i diritti corretti e di provisioning):

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```

> [!IMPORTANT]
> Se il percorso del gruppo restituito è `null`, controllare la configurazione del profilo di provisioning e i diritti e assicurarsi che siano corretti.

## <a name="application-version-updates"></a>Aggiornamenti di versione dell'applicazione

Quando viene scaricata una nuova versione dell'applicazione, iOS viene creata una nuova directory home e archivia il nuovo pacchetto di applicazione in esso. iOS sposta quindi le cartelle seguenti dalla versione precedente del Bundle dell'applicazione nella nuova home directory:

- **Documenti**
- **Libreria**

Altre directory può anche essere copiata tra e inserire nella nuova home directory, ma non sono garantiti da copiare in modo che l'applicazione non deve basarsi su questo comportamento del sistema.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato che operazioni del file system con xamarin. IOS sono simili a qualsiasi altra applicazione .NET. Anche introdotta la Sandbox dell'applicazione ed esaminato le implicazioni di sicurezza che si verifica. Successivamente, è stato spiegato il concetto di un Bundle dell'applicazione. Infine, enumerare le directory specializzate disponibile per l'applicazione e descritti i ruoli durante i backup e gli aggiornamenti dell'applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Codice di esempio del file System](https://developer.xamarin.com/samples/FileSystemSampleCode/)
- [Guida alla programmazione di file System](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [Registra il File di tipi supportata dall'App](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
