---
title: File
description: "Utilizzo delle risorse incorporate o la scrittura sul file System native API, è possibile eseguire file di gestione con xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/22/2017
ms.openlocfilehash: 605374c0f2bfe656e564e48d14ffe18ce5b7dfe5
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="files"></a>File

_Utilizzo delle risorse incorporate o la scrittura sul file System native API, è possibile eseguire file di gestione con xamarin. Forms._

## <a name="overview"></a>Panoramica

Il codice Xamarin.Forms viene eseguito in più piattaforme, ognuna delle quali ha un proprio file system. Ciò significa che la lettura e scrittura di file è la maggior parte delle facilmente effettuata utilizzando l'API di file native in ciascuna piattaforma. In alternativa, le risorse incorporate sono una soluzione più semplice per distribuire i file di dati con un'applicazione.

Questo documento vengono illustrati il seguente file comune scenari di gestione:

-  [ **File incorporati come risorse** ](#Loading_Files_Embedded_as_Resources) -file possono essere distribuiti come parte di un'applicazione e caricati tramite l'API Reflection.
-  [ **Salvataggio e caricamento di file** ](#Loading_and_Saving_Files) -utente archiviazione accessibile in scrittura può essere implementato in modo nativo e quindi accedere tramite il `DependencyService` .


Una chiamata di terze parti componente **PCLStorage** può anche essere utilizzato per leggere e scrivere i file utente archiviazione accessibili dal codice di libreria di classi Portabile.

Per informazioni sulla gestione di file di immagine, vedere il [utilizzo delle immagini](~/xamarin-forms/user-interface/images.md) pagina.

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>Il caricamento dei file incorporati come risorse

Per incorporare un file in un **PCL** assembly, creare o aggiungere un file e verificare che **azione di compilazione: EmbeddedResource**.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[ ![Configurazione di azione di compilazione di risorse incorporato](files-images/vs-embeddedresource-sml.png "impostazione EmbeddedResource BuildAction")](files-images/vs-embeddedresource.png "impostazione EmbeddedResource BuildAction")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[ ![File di testo incorporato nella libreria di classi Portabile, la configurazione di azione di compilazione risorsa incorporata](files-images/xs-embeddedresource-sml.png "impostazione EmbeddedResource BuildAction")](files-images/xs-embeddedresource.png "impostazione EmbeddedResource BuildAction")

-----

`GetManifestResourceStream` viene utilizzato per accedere al file incorporato con il relativo **ID risorsa**. Per impostazione predefinita, l'ID risorsa è il nome del file con lo spazio dei nomi predefinito per il progetto è incorporato nella - preceduta in questo caso l'assembly è **WorkingWithFiles** e il nome del file è **PCLTextResource.txt**, pertanto è l'ID risorsa `WorkingWithFiles.PCLTextResource.txt`.

```csharp
var assembly = typeof(LoadResourceText).GetTypeInfo().Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

Il `text` variabile può quindi essere utilizzata per visualizzare il testo o in caso contrario utilizzarlo nel codice. Questa schermata del [app di esempio](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/) viene visualizzato il testo visualizzato un `Label` controllo.

 [ ![File di testo incorporato nel PCL](files-images/pcltext-sml.png "File di testo incorporato nel PCL visualizzati nell'App")](files-images/pcltext.png "File di testo incorporato nel PCL visualizzati nell'App")

Il caricamento e la deserializzazione di un file XML è altrettanto semplice. Il codice seguente viene illustrato un file XML che viene caricato e deserializzato da una risorsa, quindi associato a un `ListView` per la visualizzazione. Il file XML contiene una matrice di `Monkey` oggetti (la classe è definita nel codice di esempio).

```csharp
var assembly = typeof(LoadResourceText).GetTypeInfo().Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLXmlResource.xml");
List<Monkey> monkeys;
using (var reader = new System.IO.StreamReader (stream)) {
    var serializer = new XmlSerializer(typeof(List<Monkey>));
    monkeys = (List<Monkey>)serializer.Deserialize(reader);
}
var listView = new ListView ();
listView.ItemsSource = monkeys;
```

 [ ![File XML incorporati nella libreria di classi Portabile, visualizzata in ListView](files-images/pclxml-sml.png "File XML incorporato in PCL visualizzata in ListView")](files-images/pclxml.png "File XML incorporato in PCL visualizzata in ListView")

<a name="Embedding_in_Shared_Projects" />

### <a name="embedding-in-shared-projects"></a>L'incorporamento in progetti condivisi

Progetti condivisi possono inoltre contenere file come risorse incorporate, tuttavia, poiché il contenuto di un progetto condiviso viene compilato in progetti di riferimento, il prefisso utilizzato per incorporata possibile modificare l'ID risorsa del file. Ciò significa che l'ID di risorsa per ogni file incorporato può essere diverso per ogni piattaforma.

Sono disponibili due soluzioni al problema con i progetti condivisi:

-  **Sincronizzare i progetti** -modificare le proprietà di progetto per ogni piattaforma utilizzare il **stesso** spazio dei nomi predefinito e di nome di assembly. Questo valore può quindi essere "hardcoded" come prefisso per la risorsa incorporata ID nel progetto condiviso.
-  **direttive del compilatore #if** -utilizzare le direttive del compilatore per impostare il prefisso dell'ID della risorsa corretto e utilizzare tale valore per costruire in modo dinamico l'ID di risorsa corretto.


Codice che illustra la seconda opzione è riportato di seguito. Direttive del compilatore usate per selezionare il prefisso di risorsa hardcoded (che è in genere lo stesso spazio dei nomi predefinito per il progetto di riferimento). Il `resourcePrefix` variabile viene quindi usata per creare un ID di risorsa valido tramite la concatenazione, con il nome di file di risorse incorporato.

```csharp
#if __IOS__
var resourcePrefix = "WorkingWithFiles.iOS.";
#endif
#if __ANDROID__
var resourcePrefix = "WorkingWithFiles.Droid.";
#endif
#if WINDOWS_PHONE
var resourcePrefix = "WorkingWithFiles.WinPhone.";
#endif

Debug.WriteLine("Using this resource prefix: " + resourcePrefix);
// note that the prefix includes the trailing period '.' that is required
var assembly = typeof(SharedPage).GetTypeInfo().Assembly;
Stream stream = assembly.GetManifestResourceStream
    (resourcePrefix + "SharedTextResource.txt");
```

<a name="Organizing_Resources" />

### <a name="organizing-resources"></a>Organizzare le risorse

Gli esempi precedenti presuppongono che il file è incorporato nella radice del progetto libreria di classi Portabile, nel qual caso l'ID risorsa è nel formato **Namespace.Filename.Extension**, ad esempio `WorkingWithFiles.PCLTextResource.txt` e `WorkingWithFiles.iOS.SharedTextResource.txt`.

È possibile organizzare le risorse incorporate in cartelle. Quando una risorsa incorporata viene inserita in una cartella, il nome della cartella diventa parte dell'ID risorsa (separati da punti), in modo che diventi il formato dell'ID risorsa **Namespace.Folder.Filename.Extension**. Il posizionamento dei file utilizzati in app di esempio in una cartella **cartella** renderebbe il corrispondente ID di risorsa `WorkingWithFiles.MyFolder.PCLTextResource.txt` e `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`.

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>Debug di risorse incorporate

Poiché talvolta è difficile da comprendere perché una determinata risorsa non è caricata, il seguente codice di debug aggiungere temporaneamente a un'applicazione per verificare che le risorse siano configurate correttamente. Verranno visualizzate tutte note risorse incorporate nell'assembly specificato per il **errori** riempimento per facilitare il debug di problemi di caricamento delle risorse.

```csharp
using System.Reflection;
// ...
// use for debugging, not in released app code!
var assembly = typeof(SharedPage).GetTypeInfo().Assembly;
foreach (var res in assembly.GetManifestResourceNames()) {
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>Salvataggio e caricamento di file

Poiché xamarin. Forms viene eseguito su più piattaforme, ognuno con il proprio filesystem, non sussiste alcun approccio unico per il caricamento e salvataggio di file creati dall'utente. Per illustrare come salvare e caricare i file di testo che dell'app di esempio include una schermata che salvi e carichi di alcuni input dell'utente, la schermata termine indicata di seguito:

 [ ![Salvataggio e caricamento testo](files-images/saveandload-sml.png "salvataggio e caricamento di file nell'App")](files-images/saveandload.png "salvataggio e caricamento di file nell'App")

Ogni piattaforma presenta una struttura di directory leggermente diverso e funzionalità del file System diversi, ad esempio xamarin. IOS e xamarin supportano la maggior parte `System.IO` , ma la funzionalità di Windows Phone supporta solo `IsolatedStorage` e [ `Windows.Storage` ](http://msdn.microsoft.com/library/windowsphone/develop/jj681698(v=vs.105).aspx) API.

Per evitare questo problema, l'app di esempio definisce un'interfaccia nella libreria di classi Portabile xamarin. Forms per caricare e salvare i file. Fornisce una semplice API per caricare e salvare i file di testo che verrà archiviata nel dispositivo.

```csharp
public interface ISaveAndLoad {
    void SaveText (string filename, string text);
    string LoadText (string filename);
}
```

Libreria di classi Portabile codice Usa quindi il [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) per ottenere un riferimento a un'implementazione dell'interfaccia nativa. In questo modo il codice portabile delegare il caricamento e salvataggio di file da una classe scritta in ognuno dei progetti specifici della piattaforma. Nell'esempio di **salvare** e **carico** pulsanti vengono scritti come illustrato:

```csharp
var saveButton = new Button {Text = "Save"};
saveButton.Clicked += (sender, e) => {
    DependencyService.Get<ISaveAndLoad>().SaveText("temp.txt", input.Text);
};
var loadButton = new Button {Text = "Load"};
loadButton.Clicked += (sender, e) => {
    output.Text = DependencyService.Get<ISaveAndLoad>().LoadText("temp.txt");
};
```

Un'implementazione quindi deve essere aggiunto a ognuno dei progetti specifici della piattaforma prima i file effettivamente possono essere salvati e caricati.

### <a name="ios-and-android"></a>iOS e Android

L'implementazione dell'interfaccia per i progetti di xamarin. IOS e xamarin possa essere identico. Il codice è illustrato di seguito, inclusi il `[assembly: Dependency (typeof (SaveAndLoad))]` attributo richiesto per il `DependencyService` a funzionare.

```csharp
[assembly: Dependency (typeof (SaveAndLoad))]
namespace WorkingWithFiles {
    public class SaveAndLoad : ISaveAndLoad {
        public void SaveText (string filename, string text) {
            var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var filePath = Path.Combine (documentsPath, filename);
            System.IO.File.WriteAllText (filePath, text);
        }
        public string LoadText (string filename) {
            var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal);
            var filePath = Path.Combine (documentsPath, filename);
            return System.IO.File.ReadAllText (filePath);
        }
    }
}
```

### <a name="universal-windows-platform-uwp-windows-81-and-windows-phone-81"></a>La piattaforma Windows universale (UWP), Windows 8.1 e Windows Phone 8.1

Queste piattaforme dispongono di un file System diversi API – [ `Windows.Storage` ](/windows/uwp/files/quickstart-reading-and-writing-files/) , ovvero utilizzata per salvare e caricare i file.
Il `ISaveAndLoad` interfaccia può essere implementata come illustrato di seguito:

```csharp
using System;
using System.Threading.Tasks;
using Windows.Storage;
using WinApp;
using WorkingWithFiles;
using Xamarin.Forms;

[assembly: Dependency(typeof(SaveAndLoad_WinApp))]

namespace WindowsApp
{
    // https://msdn.microsoft.com/library/windows/apps/xaml/hh758325.aspx
    public class SaveAndLoad_WinApp : ISaveAndLoad
    {
        public async Task SaveTextAsync(string filename, string text)
        {
            StorageFolder localFolder = ApplicationData.Current.LocalFolder;
            StorageFile sampleFile = await localFolder.CreateFileAsync(filename, CreationCollisionOption.ReplaceExisting);
            await FileIO.WriteTextAsync(sampleFile, text);
        }
        public async Task<string> LoadTextAsync(string filename)
        {
            StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
            StorageFile sampleFile = await storageFolder.GetFileAsync(filename);
            string text = await Windows.Storage.FileIO.ReadTextAsync(sampleFile);
            return text;
        }
    }
}
```


<a name="Saving_and_Loading_in_Shared_Projects" />

### <a name="saving-and-loading-in-shared-projects"></a>Salvataggio e caricamento di progetti condivisi

Poiché i progetti condivisi supportano le direttive del compilatore è possibile includere tutto il codice specifico della piattaforma in un singolo file di classe all'interno del progetto condiviso (senza l'utilizzo di `DependencyService`).
Un singolo `SaveAndLoad` classe potrebbe essere scritta contenente entrambe le implementazioni precedenti, compilato in modo selettivo i progetti di riferimenti utilizzando direttive del compilatore come `#if WINDOWS_PHONE`, `#if __IOS__`, e `#if __ANDROID__`.

## <a name="additional-information"></a>Informazioni aggiuntive

Progetti basati su PCL di xamarin. Forms possono inoltre sfruttare il [PCLStorage NuGet](http://www.nuget.org/packages/pclstorage) ([codice &amp; documentazione](https://pclstorage.codeplex.com/)) per facilitare l'implementazione di operazioni sui file in modo multipiattaforma.


## <a name="summary"></a>Riepilogo

Questo documento ha illustrato alcune operazioni di file semplice per il caricamento delle risorse incorporate e salvataggio e caricamento di testo nel dispositivo. Gli sviluppatori possono implementare le proprie API file nativo tramite il `DependencyService`, rendendo più complesse, come richiesto per gestire i requisiti di modifica dei file.


## <a name="related-links"></a>Collegamenti correlati

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Creare, scrivere e leggere un file (UWP)](/windows/uwp/files/quickstart-reading-and-writing-files/)
- [Esempi di Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Cartella di lavoro di file](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/files/files.workbook)
