---
title: Accesso ai file con xamarin. Android
description: Questa Guida verrà descritto l'accesso ai file in xamarin. Android
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: 476f1c50a2f1a4199dfaf1996fc9c16615b40598
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116797"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>Archiviazione dei file e l'accesso con xamarin. Android

È un requisito comune per le app Android per modificare i file &ndash; il salvataggio delle immagini, scaricare i documenti o esportare dati da condividere con altri programmi. Android (che è basato su Linux) supporta questo, fornendo lo spazio per l'archiviazione file. Il file System dei gruppi di Android in due diversi tipi di archiviazione:

* **Archiviazione interna** &ndash; questa è una parte del file system che sono accessibili solo dall'applicazione o il sistema operativo.
* **Archiviazione esterna** &ndash; si tratta di una partizione per l'archiviazione dei file che sia accessibile da tutte le app, l'utente e possibilmente altri dispositivi. In alcuni dispositivi, risorsa di archiviazione esterna potrebbe essere rimovibile (ad esempio una scheda SD).

Questi raggruppamenti sono solo concettuali e non necessariamente fanno riferimento a una singola partizione o una directory nel dispositivo. Un dispositivo Android offrirà sempre partizione per l'archiviazione interna e risorsa di archiviazione esterna. È possibile che alcuni dispositivi potrebbero disporre di più partizioni che vengono considerati come risorsa di archiviazione esterna. Indipendentemente dalla partizione le API per la lettura, scrittura o la creazione di file è lo stesso. Esistono due set di API che un'applicazione xamarin. Android può usare per l'accesso al file:

1. **Le API .NET (fornito da Mono e sottoposto a wrapping da xamarin. Android)** &ndash; includono il [helper di file system](~/essentials/file-system-helpers.md?context=xamarin/android) fornita da [Xamarin.Essentials](~/essentials/index.md?context=xamarin/android). Le API .NET di fornire la migliore compatibilità multipiattaforma e di conseguenza sarà l'obiettivo di questa Guida a queste API.
1. **Accesso al file Java native API (fornito da Java e sottoposto a wrapping da xamarin. Android)** &ndash; Java fornisce la propria API per la lettura e scrittura di file. Queste rappresentano un'alternativa completamente accettabile per le API .NET, ma sono specifiche di Android e non sono adatte per le app che dovranno essere multipiattaforma.

La lettura e scrittura su file è quasi identica in xamarin. Android come succede per qualsiasi altra applicazione .NET. L'app xamarin. Android determina il percorso del file che verrà modificato, quindi Usa standard .NET idiomi per l'accesso ai file. Poiché i percorsi effettivi in un archivio interno ed esterno possono variare da un dispositivo a altro o dalla versione di Android per la versione di Android non è consigliabile a livello di codice il percorso dei file. Usare invece le APIs Xamarin.Android per determinare il percorso dei file. In questo modo, le API .NET per la lettura e scrittura di file espone le API di Android native che consentiranno di determinare il percorso dei file nell'archiviazione interna ed esterna.

Prima di discutere le API coinvolti con l'accesso ai file, è importante comprendere alcuni dettagli che circondano archiviazione interno ed esterno. Questo argomento verrà discusso nella sezione successiva.

## <a name="internal-vs-external-storage"></a>Archiviazione esterna interno di Visual Studio

Concettualmente, risorsa di archiviazione esterna e l'archiviazione interne sono molto simili &ndash; sono entrambe le posizioni in cui un'app xamarin. Android può salvare i file. Questa analogia potrebbe generare confusione per gli sviluppatori che non si ha familiari con Android non è chiaro quando un'app deve usare l'archiviazione esterna di Visual Studio archiviazione interna.

Archiviazione interna fa riferimento alla memoria non volatile Android alloca per il sistema operativo, gli Apk e per le singole app. Quest'area non è accessibile tranne che per il sistema operativo o l'app. Android verrà allocata una directory nella partizione dello spazio di archiviazione interno per ogni app. Quando si disinstalla l'app, verranno eliminati anche tutti i file che vengono mantenuti in memoria interna in tale directory. Archiviazione interna è ideale per i file che sono accessibili solo all'app e che non verrà condiviso con altre App o avrà valore minimo dopo che l'app viene disinstallata. In Android 6.0 o versione successiva, file nell'archiviazione interna possono eseguire automaticamente il backup tramite Google di [funzionalità di Backup automatico in Android 6.0](https://developer.android.com/guide/topics/data/autobackup). L'archiviazione interna presenta gli svantaggi seguenti:

* I file non possono essere condivisi.
* I file verranno eliminati quando l'app viene disinstallata.
* Lo spazio disponibile in spazio di archiviazione interno potrebbe essere limitato.

Archiviazione esterna fa riferimento all'archiviazione di file non di spazio di archiviazione interno e non è accessibile esclusivamente a un'app. Lo scopo principale di archiviazione esterno è fornire una posizione in cui inserire i file che sono concepite per essere condivisi tra le app o che sono troppo grandi per rientrare nello spazio di archiviazione interna. Il vantaggio di archiviazione esterno è che è in genere molto più spazio per i file rispetto all'archiviazione interna. Archiviazione esterna, tuttavia, non è sempre garantito a essere presente in un dispositivo e può richiedere un'autorizzazione speciale da parte dell'utente per accedervi.

> [!NOTE]
> Per i dispositivi che supportano più utenti, Android fornirà ogni utente le proprie directory sull'archiviazione interno ed esterno. Questa directory non è accessibile ad altri utenti nel dispositivo. Questa separazione è invisibile alle App, purché lo fanno non impostare come hardcoded i percorsi ai file nell'archiviazione interna o esterna.

Come regola generale, le app xamarin. Android devono preferisce salvare i propri file nell'archiviazione interna quando a diminuire è ragionevole e si basano su risorsa di archiviazione esterna quando i file devono essere condivisi con altre App, sono molto grandi o devono essere conservati anche se l'app viene disinstallata. Ad esempio, un file di configurazione è ideale per una risorsa di archiviazione interna, purché dispongano di nessuna importanza, ad eccezione per le app che lo crea.  Al contrario, le foto sono un buon candidato per l'archiviazione esterna. Possono essere molto grandi e in molti casi l'utente desideri condividere o accedervi anche se l'app viene disinstallata.

Questa guida è incentrata su archiviazione interna. Vedere la Guida [risorsa di archiviazione esterna](~/android/platform/files/external-storage.md) per informazioni dettagliate sull'uso di archiviazione esterno in un'applicazione xamarin. Android.

## <a name="working-with-internal-storage"></a>Utilizzo di memoria interna

La directory di archiviazione interna per un'applicazione è determinata dal sistema operativo e viene esposta per le app Android per la `Android.Content.Context.FilesDir` proprietà. Verrà restituito un `Java.IO.File` oggetto che rappresenta la directory che Android ha dedicato esclusivamente per l'app.  Ad esempio, un'app con il nome del pacchetto **com.companyname** nella directory di archiviazione interno potrebbe essere:

```bash
/data/user/0/com.companyname/files
```

Questo documento si farà riferimento nella directory di archiviazione interna come _INTERNAL\_archiviazione_.

> [!IMPORTANT]
> L'esatto percorso della directory di archiviazione interna può variare da dispositivo a dispositivo e tra le versioni di Android. Per questo motivo, le app devono non rigido il percorso della directory di archiviazione di file interni del codice e usare invece le APIs xamarin. Android, ad esempio `System.Environment.GetFolderPath()`.

Per ottimizzare la condivisione del codice, le app xamarin. Android (o le app xamarin. Forms destinate a xamarin. Android) devono usare la [ `System.Environment.GetFolderPath()` ](xref:System.Environment.GetFolderPath*) (metodo). In xamarin. Android, questo metodo restituirà una stringa è nello stesso percorso di directory `Android.Content.Context.FilesDir`. Questo metodo accetta un tipo enum, `System.Environment.SpecialFolder`, che consente di identificare un set di costanti enumerate che rappresentano i percorsi di cartelle speciali utilizzate dal sistema operativo. Non tutti i valori di `System.Environment.SpecialFolder` valori verranno eseguito il mapping a una directory valida in xamarin. Android. La tabella seguente descrive il percorso può essere previsto per un determinato valore di `System.Environment.SpecialFolder`:

| System.Environment.SpecialFolder | Path  |
|----------------------|---|
| `ApplicationData` | **_INTERNI\_archiviazione_/.config** |
| `Desktop` | **_INTERNI\_archiviazione_  /Desktop** |
| `LocalApplicationData` | **_INTERNI\_archiviazione_/.local/share** |
| `MyComputer` | **_INTERNI\_archiviazione_/.local/share** |
| `MyDocuments` | **_INTERNO\_ARCHIVIAZIONE_** |
| `MyMusic` | **_INTERNI\_archiviazione_/Music** |
| `MyPictures` | **_INTERNI\_archiviazione_/Music** |
| `MyVideos` | **_INTERNI\_archiviazione_/Videos** |
| `Personal` | **_INTERNO\_ARCHIVIAZIONE_** |


### <a name="reading-or-writing-to-files-on-internal-storage"></a>La lettura o scrittura su file nella memoria interna

Uno qualsiasi dei [ C# API per la scrittura](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file) in un file sono sufficienti; è sufficiente consiste nell'ottenere il percorso del file che si trova nella directory allocata all'applicazione. È consigliabile che la modalità asincrona le versioni delle API .NET consentono di ridurre al minimo eventuali problemi che potrebbero essere associare l'accesso ai file bloccare il thread principale.

Questo frammento di codice è un esempio di scrivere un numero intero in un file di testo UTF-8 nella directory di archiviazione interna di un'applicazione:

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

Il frammento di codice seguente offre un modo per leggere un valore integer che è stato archiviato in un file di testo:

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

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>Usando Xamarin.Essentials &ndash; helper di File System

[Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android) è un set di API per la scrittura di codice compatibile con lo sviluppo multipiattaforma. Il [File System helper](~/essentials/file-system-helpers.md?context=xamarin/android) è una classe che contiene una serie di helper per semplificare l'individuazione di directory della cache e i dati dell'applicazione. Questo frammento di codice viene fornito un esempio di come individuare la directory di archiviazione interna e la directory della cache per un'app:

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>I file da nascondere il `MediaStore`

Il `MediaStore` è un componente di Android che raccoglie i metadati relativi a file multimediali (video, musica, immagini) in un dispositivo Android. Il suo scopo è semplificare la condivisione di questi file in tutte le app Android nel dispositivo.

File privati non verranno visualizzata come supporto multimediale condivisibile. Ad esempio, se un'app Salva un'immagine per l'archiviazione esterna privato, quindi tale file verrà non prelevato dallo scanner di supporti (`MediaStore`).

File pubblici verranno prelevati da `MediaStore`. Le directory che hanno un nome di file pari a zero byte **. NOMEDIA** non verranno analizzati da `MediaStore`.

## <a name="related-links"></a>Collegamenti correlati

* [Archiviazione esterna](~/android/platform/files/external-storage.md)
* [Salvare i file nella memoria del dispositivo](https://developer.android.com/training/data-storage/files)
* [Helper di Xamarin.Essentials File System](~/essentials/file-system-helpers.md?context=xamarin/android)
* [Dati utente per il backup con Backup automatico](https://developer.android.com/guide/topics/data/autobackup)
* [Archiviazione di adottare la soluzione problemi](https://source.android.com/devices/storage/adoptable)
