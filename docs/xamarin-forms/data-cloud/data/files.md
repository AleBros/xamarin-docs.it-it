---
title: Gestione di file in Xamarin.Forms
description: In Xamarin.Forms è possibile gestire i file usando il codice disponibile in una libreria .NET Standard o usando le risorse incorporate.
ms.prod: xamarin
ms.assetid: 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 21d25315aec5c056df90da11b8a51ee1493a45a5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656675"
---
# <a name="file-handling-in-xamarinforms"></a>Gestione di file in Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfiles)

_In Xamarin.Forms è possibile gestire i file usando il codice disponibile in una libreria .NET Standard o usando le risorse incorporate._

## <a name="overview"></a>Panoramica

Il codice Xamarin.Forms viene eseguito in più piattaforme, ognuna delle quali ha un proprio file system. In passato, questo comportava che la lettura e la scrittura di file risultasse molto semplice con le API di file native in ogni piattaforma. In alternativa, le risorse incorporate sono una soluzione più semplice per distribuire file di dati con un'app. Con .NET Standard 2.0, tuttavia, è possibile condividere il codice di accesso ai file nelle librerie .NET Standard.

Per informazioni sulla gestione dei file di immagine, vedere la pagina [Uso delle immagini](~/xamarin-forms/user-interface/images.md).

<a name="Loading_and_Saving_Files" />

## <a name="saving-and-loading-files"></a>Salvataggio e caricamento di file

In ogni piattaforma è possibile usare le classi `System.IO` per accedere al file system. La classe `File` consente di creare, eliminare e leggere i file e la classe `Directory` consente di creare, eliminare o enumerare il contenuto delle directory. È anche possibile usare le sottoclassi `Stream` che possono offrire un livello di controllo più elevato sulle operazioni sui file, ad esempio la compressione o la ricerca di una posizione all'interno di un file.

È possibile scrivere un file di testo usando il metodo `File.WriteAllText`:

```csharp
File.WriteAllText(fileName, text);
```

È possibile leggere un file di testo usando il metodo `File.ReadAllText`:

```csharp
string text = File.ReadAllText(fileName);
```

Il metodo `File.Exists`, inoltre, determina se il file specificato esiste:

```csharp
bool doesExist = File.Exists(fileName);
```

Il percorso del file in ogni piattaforma può essere determinato da una libreria .NET Standard usando un valore dell'enumerazione [`Environment.SpecialFolder`](xref:System.Environment.SpecialFolder) come primo argomento per il metodo `Environment.GetFolderPath`. Questo può quindi essere combinato con un nome di file con il metodo `Path.Combine`:

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

Queste operazioni sono illustrate nell'app di esempio che include una pagina che salva e carica il testo:

[![Salvataggio e caricamento di testo](files-images/saveandload-sml.png "Salvataggio e caricamento di file nell'app")](files-images/saveandload.png#lightbox "Salvataggio e caricamento di file nell'app")

<a name="Loading_Files_Embedded_as_Resources" />

## <a name="loading-files-embedded-as-resources"></a>Caricamento di file incorporati come risorse

Per incorporare un file in un assembly **.NET Standard**, creare o aggiungere un file e assicurarsi di selezionare **Azione compilazione: EmbeddedResource**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Configurazione azione di compilazione risorsa incorporata](files-images/vs-embeddedresource-sml.png "Impostazione azione di compilazione risorsa incorporata")](files-images/vs-embeddedresource.png#lightbox "Impostazione azione di compilazione risorsa incorporata")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![File di testo incorporato nella libreria di classi portabile, configurazione azione di compilazione risorsa incorporata](files-images/xs-embeddedresource-sml.png "Impostazione azione di compilazione risorsa incorporata")](files-images/xs-embeddedresource.png#lightbox "Impostazione azione di compilazione risorsa incorporata")

-----

`GetManifestResourceStream` viene usato per accedere al file incorporato usando il relativo **ID risorsa**. Per impostazione predefinita, l'ID della risorsa è il nome del file preceduto dallo spazio dei nomi predefinito per il progetto in cui è incorporato. In questo caso, l'assembly è **WorkingWithFiles** e il nome del file è **PCLTextResource.txt**, quindi l'ID della risorsa è `WorkingWithFiles.PCLTextResource.txt`.

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.PCLTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream)) {
    text = reader.ReadToEnd ();
}
```

La variabile `text` può quindi essere usata per visualizzare il testo oppure è possibile usarla nel codice. Questo screenshot dell'[app di esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfiles) illustra il testo visualizzabile in un controllo `Label`.

 [![File di testo incorporato nella libreria di classi portabile](files-images/pcltext-sml.png "File di testo incorporato nella libreria di classi portabile visualizzato nell'app")](files-images/pcltext.png#lightbox "File di testo incorporato nella libreria di classi portabile visualizzato nell'app")

Il caricamento e la deserializzazione di un file XML è ugualmente semplice. Il codice seguente illustra un file XML che viene caricato e deserializzato da una risorsa e quindi associato a un elemento `ListView` per la visualizzazione. Il file XML contiene una matrice di oggetti `Monkey` (la classe è definita nel codice di esempio).

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

 [![File XML incorporato nella libreria di classi portabile, visualizzato in ListView](files-images/pclxml-sml.png "File XML incorporato nella libreria di classi portabile visualizzato in ListView")](files-images/pclxml.png#lightbox "File XML incorporato nella libreria di classi portabile visualizzato in ListView")

<a name="Embedding_in_Shared_Projects" />

## <a name="embedding-in-shared-projects"></a>Incorporamento in progetti condivisi

I progetti condivisi possono anche contenere file come risorse incorporate. Tuttavia, poiché il contenuto di un progetto condiviso viene compilato nei progetti di riferimento, il prefisso usato per gli ID risorsa del file incorporato può cambiare. Ciò significa che l'ID risorsa per ogni file incorporato può essere diverso per ogni piattaforma.

Nei progetti condivisi questo problema ha due soluzioni:

-  **Sincronizzare i progetti**: modificare le proprietà del progetto per ogni piattaforma in modo da usare lo **stesso** nome di assembly e spazio dei nomi predefinito. Questo valore può quindi essere impostato come "hardcoded" e usato come prefisso per gli ID della risorsa incorporata nel progetto condiviso.
-  **Direttive #if del compilatore**: usare le direttive del compilatore per impostare il prefisso di ID risorsa corretto e usare questo valore per costruire in modo dinamico l'ID risorsa corretto.


Il codice che illustra la seconda opzione è riportato di seguito. Le direttive del compilatore vengono usate per selezionare il prefisso hardcoded della risorsa che in genere corrisponde allo spazio dei nomi predefinito per il progetto di riferimento. Viene quindi usata la variabile `resourcePrefix` per creare un ID risorsa valido concatenandolo con il nome file della risorsa incorporata.

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

Negli esempi precedenti si presuppone che il file sia incorporato nella radice del progetto libreria .NET Standard. In questo caso, il formato dell'ID risorsa è **Namespace.Filename.Extension**, ad esempio `WorkingWithFiles.PCLTextResource.txt` e `WorkingWithFiles.iOS.SharedTextResource.txt`.

È possibile organizzare le risorse incorporate in cartelle. Quando una risorsa incorporata viene inserita in una cartella, il nome di quest'ultima diventa parte dell'ID della risorsa (separato da punti), in modo tale che il formato dell'ID della risorsa diventa **Namespace.Folder.Filename.Extension**. Se si inseriscono i file usati nell'app di esempio in una cartella **MyFolder**, gli ID risorsa corrispondenti saranno `WorkingWithFiles.MyFolder.PCLTextResource.txt` e `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`.

<a name="Debugging_Embedded_Resources" />

### <a name="debugging-embedded-resources"></a>Debug delle risorse incorporate

A volte è difficile comprendere il motivo per cui una determinata risorsa non viene caricata. Per verificare che le risorse siano configurate correttamente è possibile aggiungere temporaneamente a un'applicazione il codice di debug seguente. Tutte le risorse note incorporate nell'assembly specificato verranno visualizzate nel riquadro **Errori** per facilitare il debug dei problemi di caricamento delle risorse.

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

Questo articolo ha illustrato semplici operazioni su file per il salvataggio e il caricamento di testo nel dispositivo e per il caricamento delle risorse incorporate. Con .NET Standard 2.0 è possibile condividere il codice di accesso ai file nelle librerie .NET Standard.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di file](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfiles)
- [Esempi di Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
- [Working with the File System in Xamarin.iOS](~/ios/app-fundamentals/file-system.md) (Uso del file system in Xamarin.iOS)

