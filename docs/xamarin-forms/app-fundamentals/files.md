---
title: Gestione di file in xamarin. Forms
description: File di gestione con xamarin. Forms può essere ottenuto usando codice in una libreria .NET Standard, oppure usando le risorse incorporate.
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 0be441a7be9777698212e690aca95fdd75e5050f
ms.sourcegitcommit: eac092f84b603958c761df305f015ff84e0fad44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310153"
---
# <a name="file-handling-in-xamarinforms"></a>Gestione di file in xamarin. Forms

_File di gestione con xamarin. Forms può essere ottenuto usando codice in una libreria .NET Standard, oppure usando le risorse incorporate._

## <a name="overview"></a>Panoramica

Il codice Xamarin.Forms viene eseguito in più piattaforme, ognuna delle quali ha un proprio file system. In precedenza, il risultato è stata che che durante la lettura e scrittura di file non è più facilmente eseguita utilizzando l'API di file native in ciascuna piattaforma. In alternativa, le risorse incorporate sono una soluzione più semplice per distribuire i file di dati con un'applicazione. Tuttavia, con .NET 2.0 Standard è possibile condividere il codice di accesso ai file nelle librerie .NET Standard.

Per informazioni sulla gestione di file di immagine, vedere il [utilizzo delle immagini](~/xamarin-forms/user-interface/images.md) pagina.

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>Salvataggio e caricamento di file

Il `System.IO` classi possono essere utilizzate per accedere al file system in ciascuna piattaforma. Il `File` classe consente di creare, eliminare e leggere i file e `Directory` classe consente di creare, eliminare o enumerare il contenuto della directory. È anche possibile usare il `Stream` sottoclassi, che possono fornire un maggiore livello di controllo sulle operazioni di file (ad esempio, cercare la compressione o la posizione all'interno di un file).

Un file di testo può essere scritte utilizzando il `File.WriteAllText` metodo:

```csharp
File.WriteAllText(fileName, text);
```

Un file di testo può essere letto utilizzando il `File.ReadAllText` metodo:

```csharp
string text = File.ReadAllText(fileName);
```

Inoltre, il `File.Exists` metodo determina se il file specificato è presente:

```csharp
bool doesExist = File.Exists(fileName);
```

Il percorso del file in ogni piattaforma può essere determinato da una libreria .NET Standard con un valore di [ `Environment.SpecialFolder` ](xref:System.Environment.SpecialFolder) enumerazione come primo argomento per il `Environment.GetFolderPath` metodo. Questo può quindi essere combinato con un nome file con il `Path.Combine` metodo:

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

Queste operazioni sono illustrate nell'app di esempio, che include una pagina che salva e carica testo:

[![Salvataggio e caricamento testo](files-images/saveandload-sml.png "salvataggio e caricamento di file nell'App")](files-images/saveandload.png#lightbox "salvataggio e caricamento di file nell'App")

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>Il caricamento dei file incorporati come risorse

Per incorporare un file in un **.NET Standard** assembly, creare o aggiungere un file e verificare che **azione di compilazione: EmbeddedResource**.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Configurazione di azione di compilazione di risorse incorporato](files-images/vs-embeddedresource-sml.png "impostazione EmbeddedResource BuildAction")](files-images/vs-embeddedresource.png#lightbox "impostazione EmbeddedResource BuildAction")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![File di testo incorporato nella libreria di classi Portabile, la configurazione di azione di compilazione risorsa incorporata](files-images/xs-embeddedresource-sml.png "impostazione EmbeddedResource BuildAction")](files-images/xs-embeddedresource.png#lightbox "impostazione EmbeddedResource BuildAction")

-----

`GetManifestResourceStream` viene utilizzato per accedere al file incorporato con il relativo **ID risorsa**. Per impostazione predefinita, l'ID risorsa è il nome del file con lo spazio dei nomi predefinito per il progetto è incorporato nella - preceduta in questo caso l'assembly è **WorkingWithFiles** e il nome del file è **PCLTextResource.txt**, pertanto è l'ID risorsa `WorkingWithFiles.PCLTextResource.txt`.

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

Il `text` variabile può quindi essere utilizzata per visualizzare il testo o in caso contrario utilizzarlo nel codice. Questa schermata del [app di esempio](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/) viene visualizzato il testo visualizzato un `Label` controllo.

 [![File di testo incorporato nel PCL](files-images/pcltext-sml.png "File di testo incorporato nel PCL visualizzati nell'App")](files-images/pcltext.png#lightbox "File di testo incorporato nel PCL visualizzati nell'App")

Il caricamento e la deserializzazione di un file XML è altrettanto semplice. Il codice seguente viene illustrato un file XML che viene caricato e deserializzato da una risorsa, quindi associato a un `ListView` per la visualizzazione. Il file XML contiene una matrice di `Monkey` oggetti (la classe è definita nel codice di esempio).

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLXmlResource.xml");
List<Monkey> monkeys;
using (var reader = new System.IO.StreamReader (stream)) {
    var serializer = new XmlSerializer(typeof(List<Monkey>));
    monkeys = (List<Monkey>)serializer.Deserialize(reader);
}
var listView = new ListView ();
listView.ItemsSource = monkeys;
```

 [![File XML incorporati nella libreria di classi Portabile, visualizzata in ListView](files-images/pclxml-sml.png "File XML incorporato in PCL visualizzata in ListView")](files-images/pclxml.png#lightbox "File XML incorporato in PCL visualizzata in ListView")

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

Debug.WriteLine("Using this resource prefix: " + resourcePrefix);
// note that the prefix includes the trailing period '.' that is required
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
Stream stream = assembly.GetManifestResourceStream
    (resourcePrefix + "SharedTextResource.txt");
```

<a name="Organizing_Resources" />

### <a name="organizing-resources"></a>Organizzare le risorse

Gli esempi precedenti presuppongono che il file è incorporato nella radice del progetto di libreria Standard di .NET, nel qual caso l'ID della risorsa è nel formato **Namespace.Filename.Extension**, ad esempio `WorkingWithFiles.PCLTextResource.txt` e `WorkingWithFiles.iOS.SharedTextResource.txt`.

È possibile organizzare le risorse incorporate in cartelle. Quando una risorsa incorporata viene inserita in una cartella, il nome della cartella diventa parte dell'ID risorsa (separati da punti), in modo che diventi il formato dell'ID risorsa **Namespace.Folder.Filename.Extension**. Il posizionamento dei file utilizzati in app di esempio in una cartella **cartella** renderebbe il corrispondente ID di risorsa `WorkingWithFiles.MyFolder.PCLTextResource.txt` e `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`.

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>Debug di risorse incorporate

Poiché talvolta è difficile da comprendere perché una determinata risorsa non è caricata, il seguente codice di debug aggiungere temporaneamente a un'applicazione per verificare che le risorse siano configurate correttamente. Verranno visualizzate tutte note risorse incorporate nell'assembly specificato per il **errori** riempimento per facilitare il debug di problemi di caricamento delle risorse.

```csharp
using System.Reflection;
// ...
// use for debugging, not in released app code!
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
foreach (var res in assembly.GetManifestResourceNames()) {
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

## <a name="summary"></a>Riepilogo

In questo articolo ha illustrato alcune operazioni di file semplice per il salvataggio e caricamento di testo nel dispositivo e per il caricamento delle risorse incorporate. Con .NET 2.0 Standard è possibile condividere il codice di accesso ai file nelle librerie .NET Standard.

## <a name="related-links"></a>Collegamenti correlati

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Esempi di Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Utilizzo di File System in xamarin. IOS](~/ios/app-fundamentals/file-system.md)
- [Cartella di lavoro di file](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/files/files.workbook)
