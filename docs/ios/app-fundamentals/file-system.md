---
title: Utilizzo di File System in xamarin. IOS
description: Questo documento descrive come utilizzare i file system in xamarin. IOS. Vengono illustrate le directory, durante la lettura di file, la serializzazione XML e JSON, sandbox dell'applicazione, la condivisione di file tramite iTunes e altro ancora.
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 5bcfac7911ac589e08cd6c5db8c8ea15a3497eca
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784241"
---
# <a name="working-with-the-file-system-in-xamarinios"></a>Utilizzo di File System in xamarin. IOS

È possibile utilizzare xamarin. IOS e `System.IO` classi il *libreria di classi Base (BCL) .NET* per accedere al file system iOS. Il `File` classe consente di creare, eliminare e leggere i file e `Directory` classe consente di creare, eliminare o enumerare il contenuto della directory. È inoltre possibile utilizzare `Stream` sottoclassi, che possono fornire un maggiore livello di controllo sulle operazioni di file (ad esempio, cercare la compressione o la posizione all'interno di un file).

iOS impone alcune limitazioni sulle operazioni che è possibile eseguire un'applicazione con il file system per mantenere la sicurezza dei dati di un'applicazione e per proteggere gli utenti da app maligni. Queste restrizioni fanno parte di *applicazione Sandbox* : un set di regole che limita l'accesso a un'applicazione a file, le preferenze, risorse di rete, hardware e così via. Un'applicazione è limitata alla lettura e scrittura di file all'interno di una directory home (percorso di installazione); non può accedere a file dell'applicazione.

iOS dispone anche di alcune funzionalità specifiche del sistema di file: alcune directory richiedono un trattamento speciale rispetto al backup e gli aggiornamenti e applicazioni possono inoltre condividere i file tramite iTunes.

In questo articolo vengono illustrate le funzionalità e restrizioni di iOS in modo dettagliato del file system e include un'applicazione di esempio che illustra come usare xamarin. IOS per eseguire alcune operazioni di sistema di file semplice:

 [![](file-system-images/05-sampleapp.png "Un esempio di iOS, l'esecuzione di alcune operazioni di sistema di file semplice")](file-system-images/05-sampleapp.png#lightbox)

 <a name="General_File_Access" />

## <a name="general-file-access"></a>Accesso ai File generale

Xamarin consente di utilizzare .NET `System.IO` classi per le operazioni di file system in iOS.

Frammenti di codice seguenti illustrano alcune operazioni di file comuni. Sono disponibili esse tutte di sotto di `SampleCode.cs` file, nell'applicazione di esempio per l'articolo.

<a name="Working_with_directories" />

### <a name="working-with-directories"></a>Utilizzo di directory

Questo codice enumera le sottodirectory nella directory corrente (specificata per il ". /" parametro), che è il percorso dell'eseguibile dell'applicazione.
L'output sarà un elenco di tutti i file e cartelle che vengono distribuite con l'applicazione (visualizzato nella finestra della console mentre si esegue il debug).

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

 <a name="Reading_files" />


### <a name="reading-files"></a>Lettura di file

Per leggere un file di testo, è sufficiente una singola riga di codice. In questo esempio, il contenuto di un file di testo verrà visualizzato nella finestra di Output dell'applicazione.

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

 <a name="XML_Serialization" />


### <a name="xml-serialization"></a>Serializzazione XML

Anche se utilizzano completo `System.Xml` dello spazio dei nomi non rientra nell'ambito di questo articolo, è possibile deserializzare facilmente un documento XML dal file system utilizzando un oggetto StreamReader simile al seguente:

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

Consultare la documentazione di MSDN per la [System](http://msdn.microsoft.com/library/system.xml.aspx) dello spazio dei nomi per ulteriori informazioni su [serializzazione](http://msdn.microsoft.com/library/system.xml.serialization.aspx). È inoltre consigliabile esaminare il [documentazione di xamarin](~/ios/deploy-test/linker.md) del linker: in genere è necessario aggiungere il `[Preserve]` attributo alle classi che si desidera serializzare.

 <a name="Creating_Files_and_Directories" />


### <a name="creating-files-and-directories"></a>Creazione di file e directory

In questo esempio viene illustrato come utilizzare la `Environment` classe per accedere alla cartella documenti in cui è possibile creare file e directory.

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

Creazione di una directory è un processo molto simile:

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

Per ulteriori informazioni sullo spazio dei nomi System.IO, vedere il [documentazione MSDN](http://msdn.microsoft.com/library/system.io.aspx).


### <a name="serializing-json"></a>La serializzazione Json

Uso di Json dati in un'applicazione di xamarin sono molto semplici tramite il [Json.NET](http://www.newtonsoft.com/json) framework JSON ad alte prestazioni per il pacchetto NuGet .NET. Il pacchetto NuGet è sufficiente aggiungere al progetto dell'applicazione: 

[![](file-system-images/json01.png "Aggiunta del pacchetto NuGet per il progetto di applicazioni")](file-system-images/json01.png#lightbox)

Successivamente, aggiungere una classe di agire come modello di dati per la serializzazione/deserializzazione (in questo caso `Account.cs`):

```csharp
using System;
using System.Collections.Generic;
using Foundation; // for Preserve attribute, which helps serialization with Linking enabled

namespace FileSystem
{
    [Preserve]
    public class Account
    {
        #region Computed Properties
        public string Email { get; set; }
        public bool Active { get; set; }
        public DateTime CreatedDate { get; set; }
        public List<string> Roles { get; set; }
        #endregion

        #region Constructors
        public Account() {

        }
        #endregion
    }
}
```

Infine, creare un'istanza di `Account` classe, eseguirne la serializzazione per i dati json e scriverli in un file:

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
Fare riferimento a Json .NET [documentazione](http://www.newtonsoft.com/json/help) per ulteriori informazioni sull'utilizzo dei dati json in un'applicazione .NET.

<a name="Special_Considerations" />

## <a name="special-considerations"></a>Considerazioni speciali

Nonostante le analogie tra le operazioni di file di xamarin. IOS e .NET, iOS e xamarin. IOS diversi da .NET alcune importanti differenze.

 <a name="runtimeaccessible" />


### <a name="making-project-files-accessible-at-runtime"></a>Rendere accessibile in fase di esecuzione i file di progetto

Per impostazione predefinita, se si aggiunge un file al progetto, non verranno incluse nell'assembly finale e pertanto non sarà disponibile per l'applicazione. Per includere un file nell'assembly, è necessario contrassegnarlo con un'azione di compilazione speciale, denominata contenuto.

Per contrassegnare un file per l'inclusione, fare clic sul file e scegliere **azione di compilazione &gt; contenuto** in Visual Studio per Mac. È inoltre possibile modificare il **azione di compilazione** del file **proprietà** foglio.

 <a name="Case_Sensitivity" />


### <a name="case-sensitivity"></a>Distinzione fra maiuscole e minuscole

È importante comprendere che il file system iOS *tra maiuscole e minuscole*. Ciò significa che devono corrispondere esattamente a nomi di file e directory e file Readme. txt e Readme ritenute diversi nomi di file.

Potrebbe trattarsi di confusione per gli sviluppatori .NET che hanno maggiore familiari con il file system di Windows, ovvero *tra maiuscole e minuscole*: "File", "File" e "file" tutti riferimento nella stessa directory.

In tal caso, anche se i dispositivi iOS sono tra maiuscole e minuscole e il codice deve essere scritto con quello presente, è simulatore iOS non maiuscole/minuscole per impostazione predefinita. Ciò significa che se maiuscole e minuscole del nome file sia diverso tra il file e i riferimenti nel codice, il codice potrebbe funzionare comunque nel simulatore, ma che non consentirebbe di in un dispositivo reale. Questo è uno dei motivi per cui è importante distribuire in un dispositivo reale anticipato e spesso durante lo sviluppo iOS.

 <a name="Path_Separator" />


### <a name="path-separator"></a>Separatore di percorso

iOS utilizza la barra rovesciata '/' come separatore di percorso (che è diverso da Windows, che utilizza la barra rovesciata ' \').

A causa di questa differenza confusione, è consigliabile utilizzare il `System.IO.Path.Combine` (metodo), che consente di regolare per la piattaforma corrente, anziché impostare come hardcoded un separatore di percorso specifica. Si tratta di un semplice passaggio che rende il codice più portabile ad altre piattaforme.

 <a name="Application_Sandbox" />


## <a name="application-sandbox"></a>Applicazione Sandbox

Accesso dell'applicazione per il file system (e altre risorse, quali le funzionalità di rete e hardware) è limitato per motivi di sicurezza. Questa restrizione è noto come il *applicazione Sandbox*. In termini di file system, l'applicazione è limitata alla creazione e all'eliminazione di file e directory nella relativa home directory.

La home directory è una posizione univoca nel file system in cui sono archiviati l'applicazione e i relativi dati. Non è possibile scegliere (o modificare) il percorso della home directory dell'applicazione. iOS e xamarin. IOS tuttavia fornire proprietà e metodi per gestire i file e le directory all'interno.

 <a name="The_Application_Bundle" />


## <a name="the-application-bundle"></a>Il pacchetto di applicazione

Il *Bundle dell'applicazione* è la cartella che contiene l'applicazione.
Si differenzia da altre cartelle con il suffisso App aggiunto al nome della directory. Bundle dell'applicazione contiene il file eseguibile e tutto il contenuto (file di testo, immagini e così via) necessario per il progetto.

Quando si passa a Bundle dell'applicazione nel sistema operativo Mac, viene visualizzato con un'icona diversa rispetto a viene visualizzato in altre directory (e il suffisso di App è nascosto); Tuttavia, è solo una directory regolare in cui il sistema operativo viene visualizzato in modo diverso.

Per visualizzare il pacchetto di applicazione per il codice di esempio, fare clic sul progetto in Visual Studio per Mac e selezionare **Apri cartella che contiene**. Passare quindi a **bin/Debug/** in cui si trova un'icona applicazione (simile alla seguente).

 [![](file-system-images/40-bundle.png "Passare a bin/Debug per trovare l'icona di un'applicazione simile a questo screenshot")](file-system-images/40-bundle.png#lightbox)

Fare clic su questa icona e scegliere **Visualizza pacchetto di contenuto** per visualizzare il contenuto della directory Bundle dell'applicazione. Il contenuto viene visualizzato come il contenuto di una directory regolari, come illustrato di seguito:

 [![](file-system-images/45-bundle.png "Il contenuto del bundle di app")](file-system-images/45-bundle.png#lightbox)

Il pacchetto di applicazione è ciò che è installato nel simulatore o sul dispositivo durante il test e infine è ciò che viene inviato a Apple per l'inclusione nell'App Store.

 <a name="Application_Directories" />


## <a name="application-directories"></a>Directory dell'applicazione

Quando l'applicazione è installata in un dispositivo, il sistema operativo crea relativa home directory e lo colloca Bundle dell'applicazione all'interno. Il codice può accedere il pacchetto di applicazione per leggere i dati, ma non deve essere scritto in tale directory radice, come è firmato e tutte le modifiche verranno invalidare l'applicazione e impedire l'avvio.

In questo caso, anche se non devono essere scritti nella directory radice, <b>in iOS 7 e versioni precedenti</b> crea un numero di directory nella directory radice dell'applicazione che sono disponibili per l'utilizzo. <b>In iOS 8 le directory accessibili all'utente sono <a href="https://developer.apple.com/library/ios/technotes/tn2406/_index.html" target="_blank">non si trova</a> nell'ambito della radice dell'applicazione</b>.

Queste directory e le relative funzioni sono elencati di seguito:

&nbsp;

|Directory|Descrizione|
|---|---|
|App [ApplicationName] /|**In iOS 7 e versioni precedenti** si tratta di `ApplicationBundle` directory in cui è archiviato nel file eseguibile dell'applicazione. La struttura di directory creati nell'applicazione esiste in questa directory (ad esempio, immagini e altri tipi di file contrassegnati come risorse di Visual Studio per il progetto Mac).<br /><br />Se si desidera accedere ai file di contenuto all'interno del pacchetto di applicazione, il percorso di questa directory è disponibile tramite il `NSBundle.MainBundle.BundlePath` proprietà.|
|Documenti /|Utilizzare questa directory per archiviare i documenti degli utenti e i file di dati dell'applicazione.<br /><br />Il contenuto di questa directory può essere offerti all'utente tramite file iTunes condivisione (anche se è disabilitato per impostazione predefinita). Aggiungere un `UIFileSharingEnabled` booleano chiave per il file Info. plist per consentire agli utenti di accedere a tali file.<br /><br />Anche se un'applicazione non attiva immediatamente la condivisione di file, è consigliabile evitare di inserire i file che devono essere nascosto agli utenti in questa directory (ad esempio i file di database, a meno che non si desidera condividerli). Finché i file riservati rimangono nascosti, questi file verranno non esposti (e potenzialmente spostati, modificati o eliminati da iTunes) se è attivata la condivisione file in una versione futura.<br /><br /> È possibile utilizzare il `Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)` metodo per ottenere il percorso della directory documenti per l'applicazione.<br /><br />Il contenuto di questa directory viene eseguito il backup da iTunes.|
|Libreria /|La directory di libreria è un ottimo strumento per archiviare i file che non vengono creati direttamente dall'utente, ad esempio database o altri file generati dall'applicazione. Il contenuto di questa directory non è mai esposte all'utente tramite iTunes.<br /><br />È possibile creare la propria sottodirectory nella libreria. Tuttavia, sono già presenti alcune creato dal sistema Directory qui che occorre conoscere, tra cui le preferenze e memorizza nella cache.<br /><br />Il contenuto della directory (ad eccezione della sottodirectory della cache) viene eseguito il backup da iTunes. Directory personalizzate creati nella raccolta verranno sottoposti a backup.|
|Preferenze/libreria /|In questa directory vengono archiviati i file di preferenze specifiche dell'applicazione. Non creare direttamente i file. Utilizzare invece la `NSUserDefaults` classe.<br /><br />Il contenuto di questa directory viene eseguito il backup da iTunes.|
|Libreria/cache /|La directory cache è un ottimo strumento per archiviare i file di dati che consentono l'applicazione eseguita, ma che può essere ricreata facilmente se necessario. L'applicazione deve creare e questi file eliminati in base alle esigenze ed essere in grado di ricreare questi file, se necessario. iOS 5 può inoltre eliminare questi file (in situazioni di memoria estremamente bassa), ma non verrà eseguita questa operazione mentre è in esecuzione l'applicazione.<br /><br />Il contenuto di questa directory non sottoposti a backup da iTunes, ovvero che non sarà presente se si ripristina un dispositivo e potrebbe non essere presente dopo avere installata una versione aggiornata dell'applicazione.<br /><br />Ad esempio, nel caso in cui l'applicazione non è possibile connettersi alla rete, è possibile utilizzare la directory cache per archiviare i file di dati o per fornire un'esperienza ottimale offline. L'applicazione è possibile salvare e recuperare rapidamente i dati durante l'attesa di risposte di rete, ma non è necessario eseguire il backup e può facilmente essere recuperato o ricreato dopo l'aggiornamento di una versione o il ripristino.|
|tmp/|Le applicazioni possono archiviare i file temporanei che sono necessari solo per un breve periodo in questa directory. Per risparmiare spazio, i file devono essere eliminati quando non sono più necessari. Quando un'applicazione non è in esecuzione, il sistema operativo può inoltre eliminare file da questa directory.<br /><br />Il contenuto di questa directory non viene eseguito il backup da iTunes.<br /><br />Ad esempio, la directory di tmp potrebbe essere utilizzata per archiviare i file temporanei che vengono scaricati da visualizzare all'utente (ad esempio Twitter avatar o allegati di posta elettronica), ma che può essere eliminato dopo che è stati visualizzati (e scaricati di nuovo se sono necessarie in futuro ).|

Questa schermata mostra la struttura di directory in una finestra di ricerca:

 [![](file-system-images/08-library-directory.png "Questa schermata viene mostrata la struttura di directory in una finestra di ricerca")](file-system-images/08-library-directory.png#lightbox)

 <a name="Accessing_Other_Directories_Programmatically" />

### <a name="accessing-other-directories-programmatically"></a>Accesso alle altre directory a livello di codice

Negli esempi precedenti di file e directory accessibili il `Documents` directory. Per scrivere in un'altra directory, è necessario creare un percorso utilizzando il "..." sintassi come illustrato di seguito:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

Creazione di una directory è molto simile:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

Percorsi di `Caches` e `tmp` directory possono essere costruite simile al seguente:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

 <a name="Sharing_Files_with_the_User_through_iTunes" />


## <a name="sharing-files-with-the-user-through-itunes"></a>Condivisione di file con l'utente tramite iTunes

Gli utenti possono accedere i file nella directory documenti dell'applicazione modificando `Info.plist` e la creazione di un **applicazione supporta la condivisione iTunes** (`UIFileSharingEnabled`) voce il **origine** visualizzazione, come illustrato di seguito:

 [![](file-system-images/09-uifilesharingenabled-plist.png "Aggiungere l'applicazione supporta la condivisione di proprietà iTunes")](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

Questi file sono accessibili in iTunes quando il dispositivo è connesso e l'utente sceglie il `Apps` scheda. Ad esempio, nella schermata seguente mostra i file nell'applicazione selezionata condiviso tramite iTunes:

 [![](file-system-images/10-itunes-file-sharing.png "Questa schermata sono visualizzati i file condivisi tramite iTunes di app selezionata")](file-system-images/10-itunes-file-sharing.png#lightbox)

Gli utenti possono accedere solo gli elementi di primo livello in questa directory tramite iTunes. Non possono vedere il contenuto di tutte le sottodirectory (anche se è possibile copiarli nel proprio computer o eliminarli). Ad esempio, con GoodReader, file PDF ed EPUB possono essere condivisi con l'applicazione in modo che gli utenti possono leggere nei dispositivi iOS.

Utenti che modificano il contenuto della propria cartella documenti possono causare problemi se non si attenzione. L'applicazione deve tenere in considerazione questo ed essere resilienti agli aggiornamenti distruttivi della cartella documenti.

Esempio di codice per questo articolo viene creato un file e una cartella nella cartella documenti (in **SampleCode.cs**) e consente la condivisione file di **Info. plist** file. Questa schermata viene illustrato come questi elementi vengono visualizzati in iTunes:

 [![](file-system-images/15-itunes-file-sharing-example.png "Questa schermata viene illustrato i file in iTunes")](file-system-images/15-itunes-file-sharing-example.png#lightbox)

Fare riferimento al [utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md) articolo per informazioni su come impostare icone per l'applicazione e per qualsiasi tipo di documento personalizzato creato.

Se il `UIFileSharingEnabled` è false o non è presente, quindi la condivisione di file è, disattivati per impostazione predefinita, e gli utenti non saranno in grado di interagire con i Documentsdirectory.

 <a name="Backup_and_Restore" />

## <a name="backup-and-restore"></a>Backup e ripristino

Quando un dispositivo di backup da iTunes, tutte le directory create nella home directory dell'applicazione saranno salvate tranne i seguenti:

-   **App [ApplicationName]** : il pacchetto di applicazione *does* effettuato il backup, ma solo quando il pacchetto è stato modificato (installando un aggiornamento dell'applicazione, ad esempio). Si deve modificare la directory, comunque, poiché è firmato e pertanto non devono essere modificate dopo l'installazione. 
-   **Libreria/cache** : la directory della cache è destinata a file di lavoro che non è necessario eseguire il backup. 
-   **TMP** : questa directory viene utilizzata per i file temporanei creati e eliminati quando non è più necessario o per i file che iOS Elimina quando è necessario recuperare spazio. 


Backup di una grande quantità di dati può richiedere molto tempo. Se si decide che è necessario eseguire il backup di eventuali dati o un particolare documento, l'applicazione deve utilizzare solo i documenti e una libreria di cartelle per questo oggetto. Per i dati temporanei o i file che possono essere facilmente recuperati dalla rete, utilizzare la cache o la directory tmp.

Tenere presente che iOS 'pulirà' nel file System quando un dispositivo esegue insufficiente spazio su disco. Questo processo rimuoverà tutti i file dalla libreria/cache e tmp cartella delle applicazioni che non sono attualmente in esecuzione.

 <a name="Complying_with_iOS5_iCloud_Backup_Restrictions" />

## <a name="complying-with-ios5-icloud-backup-restrictions"></a>Il rispetto di iOS5 iCloud restrizioni di Backup

Apple introdotto *iCloud Backup* funzionalità con iOS 5. Quando è abilitato iCloud Backup, tutti i file nella directory principale dell'applicazione (escludendo le directory di backup non viene in genere, ad esempio, il bundle dell'app, `Caches` e `tmp`) vengono sottoposti a backup in iCloud server. Questa funzionalità fornisce all'utente con un backup completo nel caso in cui il dispositivo viene perso, rubato o danneggiato.

Poiché iCloud fornisce solo 5Gb di spazio 'gratuito' per ogni utente e per evitare di utilizzare inutilmente la larghezza di banda, Apple prevede che alle applicazioni di solo backup generato dall'utente dati essenziali. Per rispettare le linee guida di archiviazione di dati di iOS è necessario limitare la quantità di dati di backup aderendo ai seguenti elementi:

-  Archiviare solo i dati generati dall'utente o dati che in caso contrario non possono essere ricreati, nella cartella documenti (ovvero sottoposti a backup). 
-  Altri dati che possono essere facilmente ricreato o scaricato nuovamente `Library/Caches` o `tmp` (che non viene eseguito il backup e può essere 'eliminato'). 
-  Se si dispone di file che potrebbero essere appropriati per il `Library/Caches` o `tmp` cartella ma non si desidera essere 'eliminato' out, archiviarli in un' posizione (ad esempio `Library/YourData` ) e applicare il ' non viene eseguito il backup ' attributo per impedire che i file utilizzando backup ba Backup iCloud ndwidth e spazio di archiviazione. Questo tipo di dati utilizza ancora di spazio nel dispositivo, pertanto si devono gestire attentamente ed eliminarlo quando possibile. 

Il ' non viene eseguito il backup di ' attributo viene impostato utilizzando la `NSFileManager` classe. Verificare che la classe sia `using Foundation` e chiamare `SetSkipBackupAttribute` come segue:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

Quando `SetSkipBackupAttribute` è `true` il file non verrà eseguito il backup, indipendentemente dalla directory in cui è memorizzato (anche il `Documents` directory). È possibile eseguire una query attributo tramite il `GetSkipBackupAttribute` metodo ed è possibile ripristinare le impostazioni chiamando il `SetSkipBackupAttribute` metodo con `false`, come segue:

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>La condivisione di dati tra iOS App e le estensioni

Poiché le estensioni App eseguita come parte di un'applicazione host (a differenza delle app che lo contiene), la condivisione dei dati non è automatica incluso in modo che sono necessarie ulteriori operazioni. Gruppi di App sono che IOS meccanismo utilizzato per consentire di diverse app di condividere i dati. Se l'applicazione è stata configurata correttamente con i diritti corretti e il provisioning, possono accedere una directory condivisa di fuori di sandbox iOS normale.

### <a name="configure-an-app-group"></a>Configurare un gruppo di App

La posizione condivisa viene configurata usando un [gruppo App](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), nel quale viene configurato il **certificati, gli identificatori e i profili** sezione [iOS Dev Center](https://developer.apple.com/devcenter/ios/). Questo valore è necessario fare riferimento anche in ogni progetto **Entitlements.plist**.

Per informazioni sulla creazione e configurazione di un gruppo di App, vedere il [le funzionalità delle App gruppo](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) Guida.

### <a name="files"></a>File

L'app iOS e l'estensione anche possibile condividere i file usando un percorso file comune (specificato sono stati correttamente configurato con i diritti corretti e provisioning):

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


<a name="Application_Version_Updates" />

## <a name="application-version-updates"></a>Aggiornamenti di versione dell'applicazione

Quando viene scaricata una nuova versione dell'applicazione, iOS crea una nuova directory home e archivia il nuovo pacchetto di applicazione al suo interno. iOS sposta quindi le cartelle seguenti dalla versione precedente del Bundle dell'applicazione della nuova home directory:

-  **Documenti**
-  **Libreria**


Altre directory possono essere copiati tra e inserire nella nuova home directory, ma non ha la certezza di essere copiato, pertanto l'applicazione non deve basarsi su questo comportamento del sistema.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato che operazioni del file system sono semplici con xamarin. IOS come con qualsiasi altra applicazione .NET. Inoltre introdotto Sandbox dell'applicazione e le implicazioni di sicurezza che verrà esaminato. Successivamente, è esplorata il concetto di un Bundle dell'applicazione. Infine, enumerare le directory specializzate disponibile per l'applicazione e illustrati i ruoli durante il backup e gli aggiornamenti dell'applicazione.


## <a name="related-links"></a>Collegamenti correlati

- [FileSystemSampleCode](https://developer.xamarin.com/samples/FileSystemSampleCode/)
- [Guida alla programmazione di file System](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [Registra il File di tipi supporta l'App](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
