---
title: Accesso ai file con Xamarin.Android
description: Questa guida spiegherà l'accesso ai file in Xamarin.Android
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 1bb0fae73a1e3647cdc0e3266c7b44ac04fcc1ee
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304408"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>Archiviazione e accesso ai file con Xamarin.Android

Un requisito comune per le &ndash; app Android è quello di manipolare i file salvando immagini, scaricando documenti o esportando dati da condividere con altri programmi. Android (che si basa su Linux) supporta questo fornendo spazio per l'archiviazione di file. Android raggruppa il file system in due diversi tipi di archiviazione:

* **Archiviazione** &ndash; interna si tratta di una parte del file system a cui è possibile accedere solo dall'applicazione o dal sistema operativo.
* **Archiviazione** &ndash; esterna si tratta di una partizione per l'archiviazione di file accessibile da tutte le app, dall'utente e possibilmente da altri dispositivi. In alcuni dispositivi, la memoria esterna potrebbe essere rimovibile (ad esempio una scheda SD).

Questi raggruppamenti sono solo concettuali e non fanno necessariamente riferimento a una singola partizione o directory nel dispositivo. Un dispositivo Android fornirà sempre la partizione per l'archiviazione interna e l'archiviazione esterna. È possibile che alcuni dispositivi dispongano di più partizioni considerate di archiviazione esterna. Indipendentemente dalla partizione, le API per la lettura, la scrittura o la creazione di file sono le stesse. Esistono due set di API che un'applicazione Xamarin.Android può utilizzare per l'accesso ai file:There are two sets of APIs that a Xamarin.Android application may use for file access:

1. Le API .NET (fornite da Mono e di cui è stato eseguito il **wrapping da Xamarin.Android)** &ndash; include gli helper del [file system](~/essentials/file-system-helpers.md?context=xamarin/android) forniti da [Xamarin.Essentials.](~/essentials/index.md?context=xamarin/android) Le API .NET offrono la migliore compatibilità multipiattaforma e, di conseguenza, l'obiettivo di questa guida sarà su queste API.
1. Le API di accesso ai file Java native (fornite da Java e di cui è stato **eseguito il wrapping da Xamarin.Android)** &ndash; Fornisce le proprie API per la lettura e la scrittura di file. Si tratta di un'alternativa completamente accettabile alle API .NET, ma sono specifiche per Android e non sono adatte per le app che devono essere multipiattaforma.

La lettura e la scrittura in file è quasi identica in Xamarin.Android come lo è a qualsiasi altra applicazione .NET. L'app Xamarin.Android determina il percorso del file che verrà modificato, quindi utilizza idiomi .NET standard per l'accesso ai file. Poiché i percorsi effettivi per l'archiviazione interna ed esterna possono variare da dispositivo a dispositivo o dalla versione Android alla versione Android, non è consigliabile codificare il percorso dei file. Utilizzare invece le API Xamarin.Android per determinare il percorso dei file. In questo modo, le API .NET per la lettura e la scrittura di file espongono le API Android native che consentono di determinare il percorso dei file nell'archiviazione interna ed esterna.

Prima di discutere le API coinvolte nell'accesso ai file, è importante comprendere alcuni dei dettagli relativi all'archiviazione interna ed esterna. Questo verrà discusso nella prossima sezione.

## <a name="internal-vs-external-storage"></a>Archiviazione interna ed esterna

Concettualmente, l'archiviazione interna e &ndash; l'archiviazione esterna sono molto simili sono entrambi luoghi in cui un'app Xamarin.Android può salvare i file. Questa somiglianza può creare confusione per gli sviluppatori che non hanno familiarità con Android in quanto non è chiaro quando un'app deve usare l'archiviazione interna rispetto all'archiviazione esterna.

L'archiviazione interna si riferisce alla memoria non volatile che Android alloca al sistema operativo, agli APK e alle singole app. Questo spazio non è accessibile se non dal sistema operativo o dalle app. Android allocherà una directory nella partizione di archiviazione interna per ogni app. Quando l'app viene disinstallata, verranno eliminati anche tutti i file che vengono conservati nella memoria interna in tale directory. Lo spazio di archiviazione interno è più adatto per i file che sono accessibili solo all'app e che non saranno condivisi con altre app o avranno poco valore una volta disinstallata l'app. Su Android 6.0 o versioni successive, i file nella memoria interna possono essere automaticamente sottoposti a backup da Google utilizzando la [funzione Backup automatico in Android 6.0.](https://developer.android.com/guide/topics/data/autobackup) L'archiviazione interna presenta gli svantaggi seguenti:Internal storage has the following disadvantages:

* I file non possono essere condivisi.
* I file verranno eliminati quando l'app viene disinstallata.
* Lo spazio disponibile nella memoria interna potrebbe essere limitato.

L'archiviazione esterna si riferisce all'archiviazione di file che non è archiviazione interna e non è accessibile esclusivamente a un'app. Lo scopo principale dell'archiviazione esterna è quello di fornire una posizione in cui inserire i file che devono essere condivisi tra le app o che sono troppo grandi per essere contenuti nell'archiviazione interna. Il vantaggio dell'archiviazione esterna è che in genere ha molto più spazio per i file rispetto all'archiviazione interna. Tuttavia, l'archiviazione esterna non è sempre garantita per essere presente su un dispositivo e potrebbe richiedere un'autorizzazione speciale da parte dell'utente per accedervi.

> [!NOTE]
> Per i dispositivi che supportano più utenti, Android fornirà a ogni utente la propria directory nell'archiviazione interna ed esterna. Questa directory non è accessibile ad altri utenti del dispositivo. Questa separazione è invisibile alle app, purché non incodiceno i percorsi ai file in memoria interna o esterna.

Come regola generale, le app Xamarin.Android preferiscono salvare i propri file nello spazio di archiviazione interno quando è ragionevole e fare affidamento sull'archiviazione esterna quando i file devono essere condivisi con altre app, sono molto grandi o devono essere conservati anche se l'app viene disinstallata. Ad esempio, un file di configurazione è più adatto per uno storage interno in quanto non ha alcuna importanza se non per l'app che lo crea.  Al contrario, le foto sono un buon candidato per l'archiviazione esterna. Possono essere molto grandi e in molti casi l'utente potrebbe voler li condividono o accedervi anche se l'app viene disinstallata.

Questa guida si concentrerà sulla memoria interna. Vedere la guida Archiviazione esterna per informazioni dettagliate sull'utilizzo dell'archiviazione esterna in un'applicazione Xamarin.Android.See the guide [External storage](~/android/platform/files/external-storage.md) for details on using external storage in a Xamarin.Android application.

## <a name="working-with-internal-storage"></a>Utilizzo dell'archiviazione interna

La directory di archiviazione interna per un'applicazione è determinata dal `Android.Content.Context.FilesDir` sistema operativo ed è esposta alle app Android dalla proprietà . Verrà restituito `Java.IO.File` un oggetto che rappresenta la directory che Android ha dedicato esclusivamente per l'app.  Ad esempio, un'app con il nome del pacchetto com.companyname la directory di archiviazione interna potrebbe essere:For example, an app with the package **name com.companyname** the internal storage directory might be:

```bash
/data/user/0/com.companyname/files
```

Questo documento farà riferimento alla directory di archiviazione interna come _INTERNAL\_STORAGE_.

> [!IMPORTANT]
> Il percorso esatto della directory di archiviazione interna può variare da dispositivo a dispositivo e tra le versioni di Android. Per questo motivo, le app non devono codificare il percorso della directory di archiviazione dei file `System.Environment.GetFolderPath()`interni e usare invece le API Xamarin.Android, ad esempio .

Per ottimizzare la condivisione del codice, le app Xamarin.Android (o le app [`System.Environment.GetFolderPath()`](xref:System.Environment.GetFolderPath*) Xamarin.Forms destinate a Xamarin.Android) devono usare il metodo. In Xamarin.Android, questo metodo restituirà una stringa per `Android.Content.Context.FilesDir`una directory che è la stessa posizione di . Questo metodo accetta un'enumerazione, `System.Environment.SpecialFolder`, utilizzata per identificare un insieme di costanti enumerate che rappresentano i percorsi delle cartelle speciali utilizzate dal sistema operativo. Non tutti `System.Environment.SpecialFolder` i valori verranno mappati a una directory valida su Xamarin.Android. Nella tabella seguente viene descritto il percorso che `System.Environment.SpecialFolder`ci si può prevedere per un determinato valore di :

| System.Environment.SpecialFolder | Path  |
|----------------------|---|
| `ApplicationData` | **_Archiviazione\_INTERNA_/.config** |
| `Desktop` | **_ARCHIVI\_INTERNO_/Desktop** |
| `LocalApplicationData` | **_Archiviazione\_INTERNA_/.local/share** |
| `MyDocuments` | **_ARCHIVIAZIONE\_INTERNA_** |
| `MyMusic` | **_ARCHIVI\_INTERNO_/ Musica** |
| `MyPictures` | **_INTERNAL\_STORAGE_/Immagini** |
| `MyVideos` | **_INTERNAL\_STORAGE_/Video** |
| `Personal` | **_ARCHIVIAZIONE\_INTERNA_** |

### <a name="reading-or-writing-to-files-on-internal-storage"></a>Lettura o scrittura in file nella memoria interna

Una qualsiasi delle API di [C, per la scrittura](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file) in un file, è sufficiente. tutto ciò che è necessario è ottenere il percorso del file che si trova nella directory allocata all'applicazione. È consigliabile utilizzare le versioni asincrone delle API .NET per ridurre al minimo eventuali problemi che possono essere associati al blocco dell'accesso ai file nel thread principale.

Questo frammento di codice è un esempio di scrittura di un numero intero in un file di testo UTF-8 nella directory di archiviazione interna di un'applicazione:This code snippet is an example of writing an integer to a UTF-8 text file to the internal storage directory of an application:

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

Il frammento di codice successivo fornisce un modo per leggere un valore intero archiviato in un file di testo:The next code snippet provides one way to read an integer value that was stored in a text file:

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

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>Utilizzo degli helper del &ndash; file system Xamarin.Essentials

[Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android) è un set di API per la scrittura di codice compatibile con più piattaforme. Gli [helper del file system](~/essentials/file-system-helpers.md?context=xamarin/android) sono una classe che contiene una serie di helper per semplificare l'individuazione della cache e delle directory dei dati dell'applicazione. Questo frammento di codice fornisce un esempio di come trovare la directory di archiviazione interna e la directory della cache per un'app:This code snippet provides an example of how to find the internal storage directory and the cache directory for an app:

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>Nascondere i file dal`MediaStore`

Il `MediaStore` è un componente Android che raccoglie i metadati sui file multimediali (video, musica, immagini) su un dispositivo Android. Il suo scopo è semplificare la condivisione di questi file su tutte le applicazioni Android sul dispositivo.

I file privati non verranno visualizzati come supporti condisti. Ad esempio, se un'app salva un'immagine nella memoria esterna privata, il file`MediaStore`non verrà prelevato dallo scanner multimediale ( ).

I file pubblici saranno `MediaStore`prelevati da . Le directory con un nome file a zero byte **. NOMEDIA** non verrà `MediaStore`scansionato da .

## <a name="related-links"></a>Collegamenti correlati

* [Archiviazione esterna](~/android/platform/files/external-storage.md)
* [Salvare i file nella memoria del dispositivo](https://developer.android.com/training/data-storage/files)
* [Helper del file system Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android)
* [Backup dei dati utente con backup automatico](https://developer.android.com/guide/topics/data/autobackup)
* [Archiviazione adottabile](https://source.android.com/devices/storage/adoptable)
