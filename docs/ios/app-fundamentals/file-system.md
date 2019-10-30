---
title: Accesso al file System in Novell. iOS
description: Questo documento descrive come usare la file system in Novell. iOS. Vengono illustrate le directory, la lettura di file, la serializzazione XML e JSON, l'applicazione sandbox, la condivisione di file tramite iTunes e altro ancora.
ms.prod: xamarin
ms.assetid: 37DF2F38-901E-8F8E-269A-5EE0CCD28C08
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/12/2018
ms.openlocfilehash: c9e5b2504fd8af8b4232eea0dcf39d9c4760b555
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010611"
---
# <a name="file-system-access-in-xamarinios"></a>Accesso al file System in Novell. iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode)

È possibile usare Novell. iOS e le classi `System.IO` nella *libreria di classi base .NET* per accedere al file System iOS. La classe `File` consente di creare, eliminare e leggere i file e la classe `Directory` consente di creare, eliminare o enumerare il contenuto delle directory. È anche possibile usare `Stream` sottoclassi, che possono fornire un maggiore livello di controllo sulle operazioni sui file, ad esempio la compressione o la ricerca nella posizione all'interno di un file.

iOS impone alcune restrizioni sulle operazioni che un'applicazione può eseguire con la file system per mantenere la sicurezza dei dati di un'applicazione e per proteggere gli utenti da app maligne. Queste restrizioni fanno parte dell' *applicazione sandbox* , ovvero un set di regole che limitano l'accesso di un'applicazione a file, preferenze, risorse di rete, hardware e così via. Un'applicazione è limitata alla lettura e alla scrittura di file all'interno della relativa home directory (percorso installato); non può accedere ai file di un'altra applicazione.

iOS dispone anche di alcune funzionalità specifiche di file system: alcune directory richiedono un trattamento speciale per quanto riguarda i backup e gli aggiornamenti e le applicazioni possono anche condividere file tra loro e l'app **file** (a partire da iOS 11) e tramite iTunes.

Questo articolo illustra le funzionalità e le restrizioni del file system iOS e include un'applicazione di esempio che illustra come usare Novell. iOS per eseguire alcune semplici operazioni di file system:

[![un esempio di iOS che esegue alcune semplici operazioni di file system](file-system-images/01-sampleapp-sml.png)](file-system-images/01-sampleapp.png#lightbox)

## <a name="general-file-access"></a>Accesso generale ai file

Novell. iOS consente di usare le classi .NET `System.IO` per file system operazioni in iOS.

I frammenti di codice seguenti illustrano alcune operazioni comuni sui file. Tutti gli elementi seguenti sono disponibili nel file **SampleCode.cs** nell'applicazione di esempio per questo articolo.

### <a name="working-with-directories"></a>Utilizzo delle directory

Questo codice enumera le sottodirectory nella directory corrente (specificata dal parametro "./"), ovvero il percorso dell'eseguibile dell'applicazione.
L'output sarà un elenco di tutti i file e le cartelle che vengono distribuiti con l'applicazione (visualizzati nella finestra della console durante il debug).

```csharp
var directories = Directory.EnumerateDirectories("./");
foreach (var directory in directories) {
      Console.WriteLine(directory);
}
```

### <a name="reading-files"></a>Lettura di file

Per leggere un file di testo, è necessaria una sola riga di codice. In questo esempio verrà visualizzato il contenuto di un file di testo nella finestra di output dell'applicazione.

```csharp
var text = File.ReadAllText("TestData/ReadMe.txt");
Console.WriteLine(text);
```

### <a name="xml-serialization"></a>serializzazione XML

Sebbene l'utilizzo dello spazio dei nomi `System.Xml` completo esula dall'ambito di questo articolo, è possibile deserializzare facilmente un documento XML dalla file system utilizzando un oggetto StreamReader come il frammento di codice seguente:

```csharp
using (TextReader reader = new StreamReader("./TestData/test.xml")) {
      XmlSerializer serializer = new XmlSerializer(typeof(MyObject));
      var xml = (MyObject)serializer.Deserialize(reader);
}
```

Per ulteriori informazioni, vedere la documentazione relativa a [System. XML](xref:System.Xml) e alla [serializzazione](xref:System.Xml.Serialization). Vedere la [documentazione di Novell. iOS](~/ios/deploy-test/linker.md) nel linker: spesso è necessario aggiungere l'attributo `[Preserve]` alle classi che si intende serializzare.

### <a name="creating-files-and-directories"></a>Creazione di file e directory

Questo esempio illustra come usare la classe `Environment` per accedere alla cartella documenti in cui è possibile creare file e directory.

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments); 
var filename = Path.Combine (documents, "Write.txt");
File.WriteAllText(filename, "Write this text into a file");
```

La creazione di una directory è un processo simile:

```csharp
var documents =
 Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var directoryname = Path.Combine (documents, "NewDirectory");
Directory.CreateDirectory(directoryname);
```

Per altre informazioni, vedere le informazioni di [riferimento sull'API System.io](xref:System.IO).

### <a name="serializing-json"></a>Serializzazione di JSON

[JSON.NET](http://www.newtonsoft.com/json) è un Framework JSON a prestazioni elevate che funziona con Novell. iOS ed è disponibile in NuGet. Aggiungere il pacchetto NuGet al progetto di applicazione, usando **Aggiungi NuGet** in Visual Studio per Mac:

[![](file-system-images/json01.png "Adding the NuGet package to the applications project")](file-system-images/json01.png#lightbox)

Successivamente, aggiungere una classe che funga da modello di dati per la serializzazione/deserializzazione (in questo caso `Account.cs`):

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

Infine, creare un'istanza della classe `Account`, serializzarla nei dati JSON e scriverla in un file:

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

Per altre informazioni sull'uso dei dati JSON in un'applicazione .NET, vedere la [documentazione](https://www.newtonsoft.com/json/help)di JSON. NET.

## <a name="special-considerations"></a>Considerazioni speciali

Nonostante le analogie tra le operazioni di file Novell. iOS e .NET, iOS e Novell. iOS differiscono da .NET in alcuni modi importanti.

### <a name="making-project-files-accessible-at-runtime"></a>Rendere accessibili i file di progetto in fase di esecuzione

Per impostazione predefinita, se si aggiunge un file al progetto, questo non verrà incluso nell'assembly finale e pertanto non sarà disponibile per l'applicazione. Per includere un file nell'assembly, è necessario contrassegnarlo con un'azione di compilazione speciale, denominata contenuto.

Per contrassegnare un file per l'inclusione, fare clic con il pulsante destro del mouse sul file e scegliere **Compila azione &gt; contenuto** nella Visual Studio per Mac. È anche possibile modificare l' **azione di compilazione** nella finestra delle **Proprietà** del file.

### <a name="case-sensitivity"></a>Distinzione fra maiuscole e minuscole

È importante comprendere che l'file system iOS fa distinzione tra *maiuscole*e minuscole. Distinzione maiuscole/minuscole indica che i nomi di file e directory devono corrispondere esattamente a. **Readme. txt** e **Readme. txt** verrebbero considerati nomi file diversi.

Questo potrebbe creare confusione per gli sviluppatori .NET che hanno familiarità con la file system di Windows, senza *distinzione tra maiuscole* e minuscole: **file**, **file**e **file** fanno tutti riferimento alla stessa directory.

> [!WARNING]
> Il simulatore iOS non distingue tra maiuscole e minuscole.
> Se la combinazione di maiuscole e minuscole del nome file è diversa dal file stesso e dai relativi riferimenti nel codice, il codice potrebbe continuare a funzionare nel simulatore, ma avrà esito negativo in un dispositivo reale. Questo è uno dei motivi per cui è importante eseguire la distribuzione e il test in un dispositivo effettivo in modo tempestivo e frequente durante lo sviluppo di iOS.

### <a name="path-separator"></a>Separatore di percorso

iOS usa la barra "/" come separatore di percorso, che è diverso da Windows, che usa la barra rovesciata "\".

A causa di questa differenza confusa, è consigliabile usare il metodo `System.IO.Path.Combine`, che si adatta alla piattaforma corrente, anziché impostare come hardcoded un separatore di percorso specifico. Si tratta di un passaggio semplice che rende il codice più portabile su altre piattaforme.

## <a name="application-sandbox"></a>Sandbox applicazione

L'accesso dell'applicazione al file system e ad altre risorse come le funzionalità di rete e hardware è limitato per motivi di sicurezza. Questa restrizione è nota come *sandbox dell'applicazione*. In termini di file system, l'applicazione è limitata alla creazione e all'eliminazione di file e directory nella relativa home directory.

La home directory è una posizione univoca nel file system in cui sono archiviati l'applicazione e tutti i relativi dati. Non è possibile scegliere (o modificare) il percorso della Home Directory per l'applicazione. Tuttavia, iOS e Novell. iOS forniscono proprietà e metodi per gestire i file e le directory all'interno di.

## <a name="the-application-bundle"></a>Il bundle dell'applicazione

Il *bundle dell'applicazione* è la cartella che contiene l'applicazione.
Si distingue dalle altre cartelle con il suffisso. app aggiunto al nome della directory. Il bundle dell'applicazione contiene il file eseguibile e tutto il contenuto (file, immagini e così via) necessario per il progetto.

Quando si seleziona il bundle dell'applicazione in Mac OS, viene visualizzato con un'icona diversa da quella visualizzata in altre directory (e il suffisso **. app** è nascosto); Tuttavia, si tratta semplicemente di una directory normale in cui il sistema operativo viene visualizzato in modo diverso.

Per visualizzare il bundle dell'applicazione per il codice di esempio, fare clic con il pulsante destro del mouse sul progetto in **Visual Studio per Mac** e selezionare **rivela in Finder**. Passare quindi alla directory **bin** in cui si trova un'icona dell'applicazione (simile alla schermata riportata di seguito).

![Spostarsi nella directory bin per trovare un'icona dell'applicazione simile a questa schermata](file-system-images/40-bundle.png)

Fare clic con il pulsante destro del mouse sull'icona e scegliere **Mostra contenuto pacchetto** per visualizzare il contenuto della directory del bundle dell'applicazione. Il contenuto viene visualizzato esattamente come il contenuto di una directory normale, come illustrato di seguito:

[![il contenuto del bundle dell'app](file-system-images/45-bundle-sml.png)](file-system-images/45-bundle.png#lightbox)

Il bundle dell'applicazione è quello che viene installato nel simulatore o nel dispositivo durante i test e, infine, viene inviato ad Apple per l'inclusione nell'App Store.

## <a name="application-directories"></a>Directory dell'applicazione

Quando l'applicazione viene installata in un dispositivo, il sistema operativo crea una home directory per l'applicazione e crea una serie di directory all'interno della directory radice dell'applicazione disponibili per l'uso. Poiché iOS 8, le directory accessibili dall'utente [non si trovano](https://developer.apple.com/library/ios/technotes/tn2406/_index.html) all'interno della radice dell'applicazione, pertanto non è possibile derivare i percorsi per il bundle dell'applicazione dalle directory utente o viceversa.

Queste directory, come determinare il percorso e i rispettivi scopi sono elencate di seguito:

&nbsp;

|Directory|Descrizione|
|---|---|
|[ApplicationName]. app/|**In iOS 7 e versioni precedenti**, si tratta della directory `ApplicationBundle` in cui è archiviato il file eseguibile dell'applicazione. La struttura di directory creata nell'app esiste in questa directory, ad esempio immagini e altri tipi di file contrassegnati come risorse nel progetto Visual Studio per Mac.<br /><br />Se è necessario accedere ai file di contenuto all'interno del bundle dell'applicazione, il percorso di questa directory è disponibile tramite la proprietà `NSBundle.MainBundle.BundlePath`.|
|Documenti|Utilizzare questa directory per archiviare documenti utente e file di dati dell'applicazione.<br /><br />Il contenuto di questa directory può essere reso disponibile all'utente tramite la condivisione di file iTunes (anche se è disabilitato per impostazione predefinita). Aggiungere una chiave booleana `UIFileSharingEnabled` al file INFO. plist per consentire agli utenti di accedere a questi file.<br /><br />Anche se un'applicazione non Abilita immediatamente la condivisione file, è consigliabile evitare di inserire i file che devono essere nascosti agli utenti in questa directory, ad esempio i file di database, a meno che non si desideri condividerli. Finché i file riservati rimangono nascosti, questi file non verranno esposti (e potenzialmente spostati, modificati o eliminati da iTunes) se la condivisione file è abilitata in una versione futura.<br /><br /> È possibile usare il metodo `Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments)` per ottenere il percorso della directory dei documenti per l'applicazione.<br /><br />Il contenuto di questa directory viene sottoposto a backup da iTunes.|
|Libreria|La directory della libreria è una posizione ideale per archiviare i file che non vengono creati direttamente dall'utente, ad esempio database o altri file generati dall'applicazione. Il contenuto di questa directory non viene mai esposto all'utente tramite iTunes.<br /><br />È possibile creare sottodirectory personalizzate nella libreria. Tuttavia, esistono già alcune directory create dal sistema che è necessario conoscere, incluse le preferenze e le cache.<br /><br />Il contenuto di questa directory (ad eccezione della sottodirectory Caches) viene sottoposto a backup da iTunes. Verrà eseguito il backup delle directory personalizzate create nella libreria.|
|Libreria/Preferenze/|I file di preferenza specifici dell'applicazione vengono archiviati in questa directory. Non creare direttamente questi file. Usare invece la classe `NSUserDefaults`.<br /><br />Il contenuto di questa directory viene sottoposto a backup da iTunes.|
|Libreria/cache/|La directory Caches rappresenta una posizione ideale per archiviare i file di dati che consentono l'esecuzione dell'applicazione, ma che possono essere ricreati facilmente. Se necessario, l'applicazione deve creare ed eliminare questi file in base alle necessità ed essere in grado di ricreare questi file. iOS 5 può anche eliminare questi file (in situazioni di archiviazione ridotte), ma questa operazione non viene eseguita durante l'esecuzione dell'applicazione.<br /><br />Il contenuto di questa directory non viene sottoposto a backup da iTunes, il che significa che non saranno presenti se l'utente ripristina un dispositivo e potrebbe non essere presente dopo l'installazione di una versione aggiornata dell'applicazione.<br /><br />Ad esempio, nel caso in cui l'applicazione non sia in grado di connettersi alla rete, è possibile utilizzare la directory cache per archiviare dati o file per offrire un'esperienza offline ottimale. L'applicazione può salvare e recuperare rapidamente questi dati in attesa di risposte di rete, ma non è necessario eseguirne il backup e può essere facilmente ripristinata o ricreata dopo un aggiornamento del ripristino o della versione.|
|tmp|Le applicazioni possono archiviare i file temporanei che sono necessari solo per un breve periodo di tempo in questa directory. Per conservare spazio, i file devono essere eliminati quando non sono più necessari. Il sistema operativo può anche eliminare i file da questa directory quando un'applicazione non è in esecuzione.<br /><br />Il contenuto di questa directory non viene sottoposto a backup da iTunes.<br /><br />Ad esempio, la directory tmp può essere usata per archiviare i file temporanei scaricati per la visualizzazione per l'utente (ad esempio, avatar di Twitter o allegati di posta elettronica), ma che potrebbero essere eliminati dopo che sono stati visualizzati e scaricati di nuovo se richiesti in futuro. .|

Questa schermata mostra la struttura di directory in una finestra di ricerca:

[![](file-system-images/08-library-directory.png "This screenshot shows the directory structure in a Finder window")](file-system-images/08-library-directory.png#lightbox)

### <a name="accessing-other-directories-programmatically"></a>Accesso a altre directory a livello di codice

La directory e gli esempi di file precedenti hanno eseguito l'accesso alla directory `Documents`. Per scrivere in un'altra directory, è necessario creare un percorso usando la sintassi "..", come illustrato di seguito:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var filename = Path.Combine (library, "WriteToLibrary.txt");
File.WriteAllText(filename, "Write this text into a file in Library");
```

La creazione di una directory è simile:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var library = Path.Combine (documents, "..", "Library");
var directoryname = Path.Combine (library, "NewLibraryDirectory");
Directory.CreateDirectory(directoryname);
```

I percorsi delle directory `Caches` e `tmp` possono essere costruiti in modo analogo al seguente:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var cache = Path.Combine (documents, "..", "Library", "Caches");
var tmp = Path.Combine (documents, "..", "tmp");
```

## <a name="sharing-with-the-files-app"></a>Condivisione con l'app files

iOS 11 ha introdotto **l'app file** , un visualizzatore di file per iOS, che consente all'utente di visualizzare e interagire con i file in iCloud e anche archiviati da qualsiasi applicazione che la supporti. Per consentire all'utente di accedere direttamente ai file nell'app, creare una nuova chiave booleana nel file **info. plist** `LSSupportsOpeningDocumentsInPlace` e impostarla su `true`, come indicato di seguito:

![Impostare LSSupportsOpeningDocumentsInPlace in info. plist](file-system-images/51-supports-opening.png)

La directory dei **documenti** dell'app sarà ora disponibile per l'esplorazione nell'app **file** . Nell'app **file** passare a **in My iPhone** e ogni app con i file condivisi sarà visibile. Gli screenshot seguenti mostrano come appare l' [app di esempio filesystem](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode) :

![app file iOS 11](file-system-images/50-files-app-1-sml.png) ![Sfogliare i file di iPhone](file-system-images/50-files-app-2-sml.png) ![File dell'app di esempio](file-system-images/50-files-app-3-sml.png)

## <a name="sharing-files-with-the-user-through-itunes"></a>Condivisione di file con l'utente tramite iTunes

Gli utenti possono accedere ai file nella directory dei documenti dell'applicazione modificando `Info.plist` e creando un' **applicazione che supporta** la voce di condivisione iTunes (`UIFileSharingEnabled`) nella visualizzazione **origine** , come illustrato di seguito:

[![l'aggiunta dell'applicazione supporta la proprietà di condivisione iTunes](file-system-images/09-uifilesharingenabled-plist-sml.png)](file-system-images/09-uifilesharingenabled-plist.png#lightbox)

È possibile accedere a questi file in iTunes quando il dispositivo è connesso e l'utente sceglie la scheda `Apps`. Ad esempio, la schermata seguente mostra i file nell'app selezionata condivisa tramite iTunes:

[![questa schermata mostra i file nell'app selezionata condivisa tramite iTunes](file-system-images/10-itunes-file-sharing-sml.png)](file-system-images/10-itunes-file-sharing.png#lightbox)

Gli utenti possono accedere solo agli elementi di livello superiore di questa directory tramite iTunes. Non possono visualizzare il contenuto delle sottodirectory (anche se possono copiarle nel computer o eliminarle). Ad esempio, con GoodReader, i file PDF e EPUB possono essere condivisi con l'applicazione in modo che gli utenti possano leggerli nei dispositivi iOS.

Gli utenti che modificano il contenuto della cartella documenti possono causare problemi se non sono attenti. L'applicazione deve prendere in considerazione questo aspetto ed essere resiliente agli aggiornamenti distruttivi della cartella documenti.

Il codice di esempio per questo articolo crea un file e una cartella nella cartella documenti (in **SampleCode.cs**) e Abilita la condivisione di file nel file **info. plist** . Questo screenshot mostra come appaiono in iTunes:

[![questo screenshot mostra come vengono visualizzati i file in iTunes](file-system-images/15-itunes-file-sharing-example-sml.png)](file-system-images/15-itunes-file-sharing-example.png#lightbox)

Per informazioni su come impostare le icone per l'applicazione e per i tipi di documenti personalizzati creati, vedere l'articolo [utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md) .

Se la chiave di `UIFileSharingEnabled` è false o non è presente, la condivisione file è, per impostazione predefinita, disabilitata e gli utenti non saranno in grado di interagire con la directory dei documenti.

## <a name="backup-and-restore"></a>Backup e ripristino

Quando si esegue il backup di un dispositivo da iTunes, tutte le directory create nella home directory dell'applicazione verranno salvate tranne le directory seguenti:

- **[ApplicationName]. app** : non scrivere in questa directory perché è firmata e pertanto deve rimanere invariata dopo l'installazione. Può contenere risorse a cui si accede dal codice, ma non è necessario eseguire il backup, dal momento che verranno ripristinate eseguendo di nuovo il download dell'app.
- **Libreria/cache** : la directory della cache è destinata ai file di lavoro di cui non è necessario eseguire il backup.
- **tmp** : questa directory viene usata per i file temporanei creati ed eliminati quando non sono più necessari o per i file eliminati da iOS quando è necessario spazio.

Il backup di una grande quantità di dati può richiedere molto tempo. Se si decide di eseguire il backup di un documento o di dati specifici, l'applicazione deve utilizzare i documenti e le cartelle della raccolta. Per i dati temporanei o i file che possono essere recuperati facilmente dalla rete, usare le cache o la directory tmp.

> [!NOTE]
> iOS "pulisce" il filesystem quando lo spazio su disco di un dispositivo viene eseguito in modo estremamente basso.
> Questo processo rimuoverà tutti i file dalla libreria/cache e dalla cartella tmp delle applicazioni attualmente non in esecuzione.

## <a name="complying-with-ios-5-icloud-backup-restrictions"></a>Conformità alle restrizioni del backup iCloud per iOS 5

> [!NOTE]
> Sebbene questo criterio è stato introdotto per la prima volta con iOS 5 (che sembra molto tempo fa), le linee guida sono ancora rilevanti per le app attuali.

Apple ha introdotto la funzionalità di *backup iCloud* con iOS 5. Quando il backup iCloud è abilitato, tutti i file nella home directory dell'applicazione (escluse le directory di cui non è stato eseguito il backup, ad esempio il bundle dell'app, `Caches`e `tmp`) vengono sottoposti a backup nei server iCloud. Questa funzionalità consente all'utente di eseguire un backup completo in caso di smarrimento, furto o danneggiamento del dispositivo.

Poiché iCloud fornisce solo 5 GB di spazio libero a ogni utente e per evitare inutilmente l'uso della larghezza di banda, Apple prevede che le applicazioni debbano solo eseguire il backup di dati essenziali generati dall'utente. Per conformarsi alle linee guida per l'archiviazione dei dati iOS, è necessario limitare la quantità di dati di cui viene eseguito il backup aderendo agli elementi seguenti:

- Archiviare solo i dati generati dall'utente o i dati che altrimenti non possono essere ricreati nella directory dei documenti (di cui viene eseguito il backup).
- Archiviare tutti gli altri dati che possono essere facilmente ricreati o scaricati di nuovo in `Library/Caches` o `tmp` (che non viene sottoposto a backup e che potrebbero essere "puliti").
- Se sono presenti file che potrebbero essere appropriati per la cartella `Library/Caches` o `tmp` ma non si vuole che vengano eliminati, archiviarli altrove (ad esempio `Library/YourData`) e applicare l'attributo ' non eseguire il backup ' per impedire che i file utilizzino la larghezza di banda dei backup iCloud e Stora spazio GE. Questi dati usano ancora spazio sul dispositivo, quindi è necessario gestirli con cautela ed eliminarli, quando possibile.

L'attributo ' non eseguire il backup ' viene impostato con la classe `NSFileManager`. Verificare che la classe sia `using Foundation` e chiamare `SetSkipBackupAttribute` come segue:

```csharp
var documents = Environment.GetFolderPath (Environment.SpecialFolder.MyDocuments);
var filename = Path.Combine (documents, "LocalOnly.txt");
File.WriteAllText(filename, "This file will never get backed-up. It would need to be re-created after a restore or re-install");
NSFileManager.SetSkipBackupAttribute (filename, true); // backup will be skipped for this file
```

Quando `SetSkipBackupAttribute` è `true` non verrà eseguito il backup del file, indipendentemente dalla directory in cui sono archiviati (anche la directory `Documents`). È possibile eseguire una query sull'attributo usando il metodo `GetSkipBackupAttribute` ed è possibile reimpostarlo chiamando il metodo `SetSkipBackupAttribute` con `false`, come segue:

```csharp
NSFileManager.SetSkipBackupAttribute (filename, false); // file will be backed-up
```

## <a name="sharing-data-between-ios-apps-and-app-extensions"></a>Condivisione di dati tra app iOS ed estensioni di app

Poiché le estensioni dell'app vengono eseguite come parte di un'applicazione host (in contrapposizione all'app contenitore), la condivisione dei dati non è automaticamente inclusa, quindi è necessario lavoro aggiuntivo. I gruppi di app sono il meccanismo usato da iOS per consentire a diverse app di condividere i dati. Se le applicazioni sono state configurate correttamente con i diritti e il provisioning corretti, possono accedere a una directory condivisa al di fuori della normale sandbox iOS.

### <a name="configure-an-app-group"></a>Configurare un gruppo di app

Il percorso condiviso viene configurato usando un [gruppo di app](https://developer.apple.com/library/archive/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), configurato nella sezione **certificati, identificatori & profili** in [iOS Dev Center](https://developer.apple.com/devcenter/ios/). È anche necessario fare riferimento a questo valore nel file **titles. plist**di ogni progetto.

Per informazioni sulla creazione e la configurazione di un gruppo di app, vedere la Guida alle [funzionalità del gruppo di app](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

### <a name="files"></a>File

L'app iOS e l'estensione possono anche condividere i file usando un percorso di file comune (dato che sono stati configurati correttamente con i diritti e il provisioning corretti):

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =FileManager.GetContainerUrl ("group.com.xamarin.WatchSettings");
var appGroupContainerPath = appGroupContainer.Path

Console.WriteLine ("Group Path: " + appGroupContainerPath);

// use the path to create and update files
...
```

> [!IMPORTANT]
> Se il percorso del gruppo restituito è `null`, controllare la configurazione dei diritti e del profilo di provisioning e verificare che siano corretti.

## <a name="application-version-updates"></a>Aggiornamenti della versione dell'applicazione

Quando viene scaricata una nuova versione dell'applicazione, iOS crea una nuova home directory e archivia il nuovo bundle dell'applicazione. iOS sposta quindi le cartelle seguenti dalla versione precedente del bundle dell'applicazione nella nuova home directory:

- **Documenti**
- **Libreria**

Le altre directory possono anche essere copiate e inserite nella nuova Home Directory, ma non sono necessariamente copiate, quindi l'applicazione non deve basarsi su questo comportamento del sistema.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato che file system operazioni con Novell. iOS sono simili a qualsiasi altra applicazione .NET. È stata inoltre introdotta l'applicazione sandbox e sono state esaminate le implicazioni di sicurezza che causa. Si è quindi esplorato il concetto di bundle dell'applicazione. Infine, è stata enumerata le directory specializzate disponibili per l'applicazione e ne sono stati descritti i ruoli durante gli aggiornamenti dell'applicazione e i backup.

## <a name="related-links"></a>Collegamenti correlati

- [Codice di esempio FileSystem](https://docs.microsoft.com/samples/xamarin/ios-samples/filesystemsamplecode)
- [Guida alla programmazione del file System](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/FileSystemProgrammingGUide/Introduction/Introduction.html)
- [Registrazione dei tipi di file supportati dall'app](https://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html#/apple_ref/doc/uid/TP40010411-SW1)
