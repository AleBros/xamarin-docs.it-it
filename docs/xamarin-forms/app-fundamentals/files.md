---
title: Gestione di file in xamarin. Forms
description: File di gestione delle eccezioni con xamarin. Forms può essere ottenuto tramite codice in una libreria .NET Standard, o utilizzando le risorse incorporate.
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 87084a0ccc2970f56e7ef7a6d2f4c59c49032aa0
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527365"
---
# <a name="file-handling-in-xamarinforms"></a>Gestione di file in xamarin. Forms

_File di gestione delle eccezioni con xamarin. Forms può essere ottenuto tramite codice in una libreria .NET Standard, o utilizzando le risorse incorporate._

## <a name="overview"></a>Panoramica

Il codice Xamarin.Forms viene eseguito in più piattaforme, ognuna delle quali ha un proprio file system. In precedenza, questo significava che la lettura e scrittura di file è stata più semplice eseguita tramite l'API di file native in ogni piattaforma. In alternativa, le risorse incorporate sono una soluzione più semplice per distribuire i file di dati con un'app. Tuttavia, con .NET Standard 2.0 è possibile condividere il codice di accesso ai file nelle librerie .NET Standard.

Per informazioni sulla gestione di file di immagine, vedere la [utilizzo di immagini](~/xamarin-forms/user-interface/images.md) pagina.

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>Salvataggio e caricamento di file

Il `System.IO` classi possono essere utilizzate per accedere al file system in ogni piattaforma. Il `File` classe consente di creare, eliminare e leggere i file e il `Directory` classe consente di creare, eliminare o enumerare il contenuto delle directory. È anche possibile usare il `Stream` sottoclassi, che possono fornire un livello maggiore di controllo sulle operazioni di file (ad esempio, cercare la compressione o la posizione all'interno di un file).

Un file di testo può essere scritte utilizzando la `File.WriteAllText` metodo:

```csharp
File.WriteAllText(fileName, text);
```

Un file di testo può essere letto utilizzando i `File.ReadAllText` metodo:

```csharp
string text = File.ReadAllText(fileName);
```

Inoltre, il `File.Exists` metodo determina se il file specificato è presente:

```csharp
bool doesExist = File.Exists(fileName);
```

Il percorso del file in ogni piattaforma può essere determinato da una libreria .NET Standard con un valore dei [ `Environment.SpecialFolder` ](xref:System.Environment.SpecialFolder) enumerazione come primo argomento per il `Environment.GetFolderPath` (metodo). Ciò può quindi essere combinato con un nome di file con il `Path.Combine` metodo:

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

Queste operazioni sono illustrate nell'app di esempio, che include una pagina che salva e carica il testo:

[![Salvataggio e caricamento di testo](files-images/saveandload-sml.png "salvataggio e caricamento di file nell'App")](files-images/saveandload.png#lightbox "salvataggio e caricamento di file nell'App")

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>Il caricamento dei file incorporati come risorse

Per incorporare un file in una **.NET Standard** assembly, creare o aggiungere un file e assicurarsi che **azione di compilazione: EmbeddedResource**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Configurazione di azione di compilazione risorsa incorporata](files-images/vs-embeddedresource-sml.png "impostazione EmbeddedResource BuildAction")](files-images/vs-embeddedresource.png#lightbox "EmbeddedResource BuildAction impostazione")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![File di testo incorporato nella libreria di classi Portabile, la configurazione di azione di compilazione risorsa incorporata](files-images/xs-embeddedresource-sml.png "impostazione EmbeddedResource BuildAction")](files-images/xs-embeddedresource.png#lightbox "EmbeddedResource BuildAction impostazione")

-----

`GetManifestResourceStream` Consente di accedere ai file incorporati utilizzando relativi **ID risorsa**. Per impostazione predefinita, l'ID risorsa è il nome del file con lo spazio dei nomi predefinito per il progetto è incorporato nella - preceduta in questo caso l'assembly viene **WorkingWithFiles** e il nome del file viene **PCLTextResource.txt**, in modo che l'ID risorsa è `WorkingWithFiles.PCLTextResource.txt`.

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

Il `text` variabile può quindi essere utilizzata per visualizzare il testo o in caso contrario, usarla nel codice. Questo screenshot del [app di esempio](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/) viene visualizzato il testo viene eseguito il rendering un `Label` controllo.

 [![File di testo incorporato nella libreria di classi Portabile](files-images/pcltext-sml.png "File di testo incorporato nella libreria di classi Portabile visualizzati nell'App")](files-images/pcltext.png#lightbox "File di testo incorporato nella libreria di classi Portabile visualizzati nell'App")

Il caricamento e la deserializzazione di un file XML è ugualmente semplice. Il codice seguente illustra un file XML che viene caricata e deserializzata da una risorsa, quindi associato a un `ListView` per la visualizzazione. Il file XML contiene una matrice di `Monkey` oggetti (la classe è definita nel codice di esempio).

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

 [![File XML incorporato nella libreria di classi Portabile, visualizzato in ListView](files-images/pclxml-sml.png "File XML incorporato in libreria di classi Portabile visualizzata in ListView")](files-images/pclxml.png#lightbox "File XML incorporato in libreria di classi Portabile visualizzata in ListView")

<a name="Embedding_in_Shared_Projects" />

## <a name="embedding-in-shared-projects"></a>Incorporamento in progetti condivisi

Progetti condivisi possono anche contenere file come risorse incorporate, tuttavia, poiché il contenuto di un progetto condiviso viene compilato in progetti di riferimento, il prefisso usato per incorporati possono modificare gli ID risorsa del file. Ciò significa che l'ID risorsa per ogni file incorporato può essere diverso per ogni piattaforma.

Esistono due soluzioni a questo problema con i progetti condivisi:

-  **Sincronizzare i progetti** -modificare le proprietà di progetto per ogni piattaforma per usare il **stesso** spazio dei nomi predefinito e nome di assembly. Questo valore può quindi essere "hardcoded" come prefisso di risorsa incorporata ID nel progetto condiviso.
-  **le direttive del compilatore #if** -usare le direttive del compilatore per impostare il prefisso dell'ID risorsa corretto e usare tale valore per costruire in modo dinamico l'ID risorsa corretto.


Il codice che illustra che la seconda opzione è illustrata di seguito. Le direttive del compilatore vengono utilizzate per selezionare il prefisso della risorsa hardcoded (che è in genere lo stesso come spazio dei nomi predefinito per il progetto di riferimento). Il `resourcePrefix` variabile viene quindi usata per creare un ID di risorsa valida concatenando il file con il nome di risorsa incorporata.

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

### <a name="organizing-resources"></a>Organizzazione delle risorse

Gli esempi precedenti presuppongono che il file è incorporato nella radice del progetto di libreria .NET Standard, nel qual caso l'ID risorsa è nel formato **Namespace.Filename.Extension**, ad esempio `WorkingWithFiles.PCLTextResource.txt` e `WorkingWithFiles.iOS.SharedTextResource.txt`.

È possibile organizzare le risorse incorporate nelle cartelle. Se una risorsa incorporata viene inserita in una cartella, il nome della cartella diventa parte dell'ID risorsa (separati da punti), in modo che il formato dell'ID risorsa diventi **Namespace.Folder.Filename.Extension**. Il posizionamento dei file usati nell'app di esempio in una cartella **MyFolder** renderebbero l'ID di risorsa corrispondente `WorkingWithFiles.MyFolder.PCLTextResource.txt` e `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`.

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>Debug di risorse incorporate

Poiché a volte è difficile da comprendere il motivo per cui una determinata risorsa non viene caricata, il seguente codice di debug è aggiungere temporaneamente a un'applicazione per verificare che le risorse siano configurate correttamente. Verranno visualizzate tutte le note risorse incorporate nell'assembly specificato per il **errori** pad per facilitare il debug dei problemi di caricamento delle risorse.

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

Questo articolo ha illustrato alcune operazioni di file semplice per salvare e caricare il testo nel dispositivo e per il caricamento delle risorse incorporate. Con .NET Standard 2.0 è possibile condividere il codice di accesso ai file nelle librerie .NET Standard.

## <a name="related-links"></a>Collegamenti correlati

- [FilesSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFiles/)
- [Esempi di Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Utilizzo di File System in xamarin. IOS](~/ios/app-fundamentals/file-system.md)

