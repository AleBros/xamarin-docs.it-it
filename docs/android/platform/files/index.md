---
title: Accesso ai file con Novell. Android
description: Questa guida descrive l'accesso ai file in Novell. Android
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 1bb0fae73a1e3647cdc0e3266c7b44ac04fcc1ee
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78914211"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>Archiviazione file e accesso con Novell. Android

Un requisito comune per le app Android è modificare i file &ndash; salvare immagini, scaricare documenti o esportare dati da condividere con altri programmi. Android (basato su Linux) supporta questa operazione fornendo spazio per l'archiviazione di file. Android raggruppa il file System in due tipi diversi di archiviazione:

* **Archiviazione interna** &ndash; si tratta di una parte delle file System a cui è possibile accedere solo dall'applicazione o dal sistema operativo.
* **Archiviazione esterna** &ndash; si tratta di una partizione per l'archiviazione di file accessibile da tutte le app, dall'utente e possibilmente da altri dispositivi. In alcuni dispositivi, l'archiviazione esterna può essere rimovibile (ad esempio una scheda SD).

Questi raggruppamenti sono solo concettuali e non fanno necessariamente riferimento a una singola partizione o directory sul dispositivo. Un dispositivo Android fornirà sempre la partizione per l'archiviazione interna e l'archiviazione esterna. È possibile che alcuni dispositivi dispongano di più partizioni considerate come archiviazione esterna. Indipendentemente dalla partizione, le API per la lettura, la scrittura o la creazione di file sono le stesse. Sono disponibili due set di API che possono essere usate da un'applicazione Novell. Android per l'accesso ai file:

1. **Le API .NET (fornite da mono e incapsulate da Novell. Android)** &ndash; includono gli [Helper file System](~/essentials/file-system-helpers.md?context=xamarin/android) forniti da [Novell. Essentials](~/essentials/index.md?context=xamarin/android). Le API .NET forniscono la migliore compatibilità multipiattaforma e, di conseguenza, questa guida sarà basata su queste API.
1. **Le API native di accesso ai file Java (fornite da Java e incapsulate da Novell. Android)** &ndash; Java forniscono le proprie API per la lettura e la scrittura di file. Si tratta di un'alternativa completamente accettabile alle API .NET, ma sono specifici di Android e non sono adatti per le app che sono progettate per essere multipiattaforma.

La lettura e la scrittura nei file sono quasi identiche in Novell. Android così come per qualsiasi altra applicazione .NET. L'app Novell. Android determina il percorso del file che verrà modificato, quindi usa idiomi .NET standard per l'accesso ai file. Poiché i percorsi effettivi di archiviazione interna ed esterna possono variare da dispositivo a dispositivo o dalla versione Android alla versione Android, non è consigliabile codificare in modo rigido il percorso dei file. Usare invece le API Novell. Android per determinare il percorso dei file. In questo modo, le API .NET per la lettura e la scrittura di file espongono le API Android native che consentono di determinare il percorso dei file in un archivio interno ed esterno.

Prima di illustrare le API necessarie per l'accesso ai file, è importante comprendere alcuni dei dettagli relativi all'archiviazione interna ed esterna. Questo argomento verrà illustrato nella sezione successiva.

## <a name="internal-vs-external-storage"></a>Archiviazione interna e esterna

A livello concettuale, l'archiviazione interna e l'archiviazione esterna sono molto simili &ndash; sono entrambe le posizioni in cui un'app Novell. Android può salvare i file. Questa somiglianza può comportare confusione per gli sviluppatori che non hanno familiarità con Android, perché non è chiaro quando un'app deve usare l'archiviazione interna e l'archiviazione esterna.

Archiviazione interna si riferisce alla memoria non volatile che Android alloca al sistema operativo, apk e per le singole app. Questo spazio non è accessibile ad eccezione del sistema operativo o delle app. Android alloca una directory nella partizione di archiviazione interna per ogni app. Quando l'app viene disinstallata, verranno eliminati anche tutti i file conservati nell'archivio interno di tale directory. L'archiviazione interna è più adatta per i file che sono accessibili solo all'app e che non verranno condivisi con altre app o avranno un valore minimo quando l'app viene disinstallata. In Android 6,0 o versione successiva, il backup dei file nell'archiviazione interna può essere eseguito automaticamente da Google usando la [funzionalità di backup automatico in android 6,0](https://developer.android.com/guide/topics/data/autobackup). L'archiviazione interna presenta gli svantaggi seguenti:

* I file non possono essere condivisi.
* I file verranno eliminati quando l'app viene disinstallata.
* Lo spazio disponibile nell'archiviazione interna potrebbe essere limitato.

L'archiviazione esterna si riferisce all'archiviazione di file che non è una risorsa di archiviazione interna e non è accessibile esclusivamente a un'app. Lo scopo principale dell'archiviazione esterna è fornire un posto in cui inserire i file che devono essere condivisi tra le app o che sono troppo grandi per adattarsi alla risorsa di archiviazione interna. Il vantaggio dell'archiviazione esterna è che in genere ha molto più spazio per i file rispetto all'archiviazione interna. Tuttavia, non è sempre garantito che l'archiviazione esterna sia presente in un dispositivo e potrebbe richiedere un'autorizzazione speciale da parte dell'utente per accedervi.

> [!NOTE]
> Per i dispositivi che supportano più utenti, Android fornirà a ogni utente la propria directory nell'archivio interno ed esterno. Questa directory non è accessibile ad altri utenti nel dispositivo. Questa separazione è invisibile alle app, purché non codificano i percorsi dei file in una risorsa di archiviazione interna o esterna.

Come regola generale, le app Novell. Android dovrebbero preferire il salvataggio dei file nella risorsa di archiviazione interna quando è ragionevole e si basano sull'archiviazione esterna quando i file devono essere condivisi con altre app, sono molto grandi o devono essere conservati anche se l'app viene disinstallata. Un file di configurazione, ad esempio, è più adatto per una risorsa di archiviazione interna, perché non ha importanza ad eccezione dell'app che la crea.  Al contrario, le foto sono un buon candidato per l'archiviazione esterna. Possono essere di grandi dimensioni e, in molti casi, l'utente può volerne condividerle o accedervi anche se l'app viene disinstallata.

Questa guida si concentra sull'archiviazione interna. Per informazioni dettagliate sull'uso dell'archiviazione esterna in un'applicazione Novell. Android, vedere la Guida all' [archiviazione esterna](~/android/platform/files/external-storage.md) .

## <a name="working-with-internal-storage"></a>Utilizzo dell'archiviazione interna

La directory di archiviazione interna per un'applicazione è determinata dal sistema operativo ed è esposta alle app Android dalla proprietà `Android.Content.Context.FilesDir`. Verrà restituito un oggetto `Java.IO.File` che rappresenta la directory dedicata esclusivamente da Android per l'app.  Ad esempio, un'app con il nome del pacchetto **com. CompanyName** la directory di archiviazione interna potrebbe essere:

```bash
/data/user/0/com.companyname/files
```

In questo documento verrà fatto riferimento alla directory di archiviazione interna come _archiviazione\_interna_.

> [!IMPORTANT]
> Il percorso esatto della directory di archiviazione interna può variare da dispositivo a dispositivo e da una versione all'altra di Android. Per questo motivo, le app non devono impostare come hardcoded il percorso della directory di archiviazione dei file interni e usare invece le API Novell. Android, ad esempio `System.Environment.GetFolderPath()`.

Per ottimizzare la condivisione del codice, le app Novell. Android (o le app Novell. Forms destinate a Novell. Android) devono usare il metodo [`System.Environment.GetFolderPath()`](xref:System.Environment.GetFolderPath*) . In Novell. Android questo metodo restituirà una stringa per una directory che corrisponde alla posizione `Android.Content.Context.FilesDir`. Questo metodo accetta un enum, `System.Environment.SpecialFolder`, che viene usato per identificare un set di costanti enumerate che rappresentano i percorsi di cartelle speciali usate dal sistema operativo. Non tutti i valori di `System.Environment.SpecialFolder` eseguiranno il mapping a una directory valida in Novell. Android. Nella tabella seguente viene descritto il percorso previsto per un determinato valore di `System.Environment.SpecialFolder`:

| System.Environment.SpecialFolder | Percorso  |
|----------------------|---|
| `ApplicationData` | **/.Config di _archiviazione interna\__** |
| `Desktop` | **/Desktop di _archiviazione interna\__** |
| `LocalApplicationData` | **/.Local/share di _archiviazione interna\__** |
| `MyDocuments` | **_ARCHIVIAZIONE\_interna_** |
| `MyMusic` | **/Music di _archiviazione interna\__** |
| `MyPictures` | **/Pictures di _archiviazione interna\__** |
| `MyVideos` | **/Videos di _archiviazione interna\__** |
| `Personal` | **_ARCHIVIAZIONE\_interna_** |

### <a name="reading-or-writing-to-files-on-internal-storage"></a>Lettura o scrittura su file nella risorsa di archiviazione interna

Qualsiasi [ C# API per la scrittura](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file) in un file è sufficiente. è sufficiente ottenere il percorso del file che si trova nella directory allocata all'applicazione. Si consiglia vivamente di usare le versioni asincrone delle API .NET per ridurre al minimo eventuali problemi che possono essere associati all'accesso ai file che blocca il thread principale.

Questo frammento di codice è un esempio di scrittura di un numero intero in un file di testo UTF-8 nella directory di archiviazione interna di un'applicazione:

```csharp
public async Task SaveCountAsync(int count)
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");
    using (var writer = File.CreateText(backingFile))
    {
        await writer.WriteLineAsync(count.ToString());
    }
}
```

Il frammento di codice successivo fornisce un modo per leggere un valore integer archiviato in un file di testo:

```csharp
public async Task<int> ReadCountAsync()
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");

    if (backingFile == null || !File.Exists(backingFile))
    {
        return 0;
    }

    var count = 0;
    using (var reader = new StreamReader(backingFile, true))
    {
        string line;
        while ((line = await reader.ReadLineAsync()) != null)
        {
            if (int.TryParse(line, out var newcount))
            {
                count = newcount;
            }
        }
    }

    return count;
}
```

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>Uso di Novell. Essentials &ndash; helper del file System

[Novell. Essentials](~/essentials/file-system-helpers.md?context=xamarin/android) è un set di API per la scrittura di codice compatibile multipiattaforma. Gli [helper del file System](~/essentials/file-system-helpers.md?context=xamarin/android) sono una classe che contiene una serie di helper per semplificare l'individuazione della cache e delle directory di dati dell'applicazione. Questo frammento di codice fornisce un esempio di come trovare la directory di archiviazione interna e la directory della cache per un'app:

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>Nascondere i file dal `MediaStore`

Il `MediaStore` è un componente Android che raccoglie i metadati dei file multimediali (video, musica e immagini) in un dispositivo Android. Lo scopo è semplificare la condivisione di questi file in tutte le app Android sul dispositivo.

I file privati non vengono visualizzati come supporti condivisibili. Se, ad esempio, un'app salva un'immagine nella propria archiviazione esterna privata, il file non verrà prelevato da Media Scanner (`MediaStore`).

I file pubblici verranno prelevati dal `MediaStore`. Directory con un nome di file di zero byte **. Nomedia** non verrà analizzato da `MediaStore`.

## <a name="related-links"></a>Collegamenti correlati

* [Archiviazione esterna](~/android/platform/files/external-storage.md)
* [Salvare i file nell'archiviazione del dispositivo](https://developer.android.com/training/data-storage/files)
* [Helper del file System Novell. Essentials](~/essentials/file-system-helpers.md?context=xamarin/android)
* [Backup dei dati utente con backup automatico](https://developer.android.com/guide/topics/data/autobackup)
* [Archiviazione adottabile](https://source.android.com/devices/storage/adoptable)
