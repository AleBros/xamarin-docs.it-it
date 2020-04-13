---
title: Accesso ai file su storage esterno con Xamarin.Android
description: Questa guida discuterà l'accesso ai file su archiviazione esterna in Xamarin.Android
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 96b0d6a00c7825939b1f89ed63e3e5559ca4ef59
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020478"
---
# <a name="external-storage"></a>Archiviazione esterna

L'archiviazione esterna si riferisce all'archiviazione di file che non si trova nell'archiviazione interna e non è accessibile esclusivamente all'app responsabile del file. Lo scopo principale dell'archiviazione esterna è quello di fornire una posizione in cui inserire i file che devono essere condivisi tra le app o che sono troppo grandi per essere contenuti nell'archiviazione interna.

In termini storici, l'archiviazione esterna si riferiva a una partizione del disco su supporti rimovibili come una scheda SD (era anche conosciuta come _storage portatile_). Questa distinzione non è più rilevante come i dispositivi Android si sono evoluti e molti dispositivi Android non supportano più l'archiviazione rimovibile. Invece alcuni dispositivi allocheranno parte della loro memoria interna non volatile che Android per eseguire la stessa funzione supporti rimovibili. Questa operazione è nota come archiviazione _emulata_ ed è ancora considerata archiviazione esterna. In alternativa, alcuni dispositivi Android possono avere più partizioni di archiviazione esterne. Ad esempio, un tablet Android (oltre alla memoria interna) potrebbe avere spazio di archiviazione emulato e uno o più slot per una scheda SD. Tutte queste partizioni sono trattate da Android come archiviazione esterna.

Nei dispositivi con più utenti, ogni utente diverrà una directory dedicata nella partizione di archiviazione esterna primaria per l'archiviazione esterna. Le app in esecuzione come un utente non avranno accesso ai file di un altro utente nel dispositivo. I file per tutti gli utenti sono ancora leggibili in tutto il mondo e scrivibili in tutto il mondo; tuttavia, Android sarà sandbox ogni profilo utente dagli altri.

La lettura e la scrittura in file è quasi identica in Xamarin.Android come lo è a qualsiasi altra applicazione .NET. L'app Xamarin.Android determina il percorso del file che verrà modificato, quindi utilizza idiomi .NET standard per l'accesso ai file. Poiché i percorsi effettivi per l'archiviazione interna ed esterna possono variare da dispositivo a dispositivo o dalla versione Android alla versione Android, non è consigliabile codificare il percorso dei file. Al contrario, Xamarin.Android espone le API Android native che consentono di determinare il percorso dei file nell'archiviazione interna ed esterna.

Questa guida illustra i concetti e le API in Android specifici per l'archiviazione esterna.

## <a name="public-and-private-files-on-external-storage"></a>File pubblici e privati su storage esterno

Esistono due tipi diversi di file che un'app può mantenere nell'archiviazione esterna:

* **File** &ndash; privati I file privati sono file specifici per l'applicazione (ma sono ancora leggibili in tutto il mondo e scrivibili a livello mondiale). Android prevede che i file privati vengono archiviati in una directory specifica in un archivio esterno. Anche se i file sono chiamati "privati", sono ancora visibili e accessibili da altre applicazioni sul dispositivo, non sono garantiti alcuna protezione speciale da Android.

* **File** &ndash; pubblici Si tratta di file che non sono considerati specifici per l'applicazione e sono pensati per essere liberamente condivisi.

Le differenze tra questi file sono principalmente concettuali. I file privati sono privati nel senso che sono considerati parte dell'applicazione, mentre i file pubblici sono qualsiasi altro file che esiste nell'archiviazione esterna. Android fornisce due API diverse per la risoluzione dei percorsi di file privati e pubblici, ma in caso contrario le stesse API .NET vengono utilizzate per leggere e scrivere in questi file. Queste sono le stesse API descritte nella sezione Lettura [e scrittura.](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage)

### <a name="private-external-files"></a>File esterni privati

I file esterni privati sono considerati specifici di un'applicazione (simile ai file interni) ma vengono mantenuti in memoria esterna per diversi motivi (ad esempio essere troppo grandi per l'archiviazione interna). Simile ai file interni, questi file verranno eliminati quando l'app viene disinstallata dall'utente.

Il percorso principale per i file esterni `Android.Content.Context.GetExternalFilesDir(string type)`privati viene trovato chiamando il metodo . Questo metodo restituirà un `Java.IO.File` oggetto che rappresenta la directory di archiviazione esterna privata per l'app. Il `null` passaggio a questo metodo restituirà il percorso della directory di archiviazione dell'utente per l'applicazione. Ad esempio, per un'applicazione `com.companyname.app`con il nome del pacchetto , la directory "root" dei file esterni privati sarebbe:

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

Questo documento farà riferimento alla directory di archiviazione per i file privati su storage esterno come _PRIVATE\_EXTERNAL\_STORAGE_.

Il parametro per `GetExternalFilesDir()` è una stringa che specifica una directory _dell'applicazione_. Si tratta di una directory destinata a fornire un percorso standard per un'organizzazione logica dei file. I valori stringa sono disponibili `Android.OS.Environment` tramite costanti nella classe:

| `Android.OS.Environment` | Directory |
|-|-|
| Allarmi della directory | **_PRIVATE\_\_EXTERNAL STORAGE_/Alarms** |
| DirectoryDcim (informazioni in stato di | **_PRIVATE\_\_EXTERNAL STORAGE_/DCIM** |
| DirectoryDownloads (Download di Directory) | **_PRIVATE\_\_EXTERNAL STORAGE_/Download** |
| Documenti della directory | **_PRIVATE\_\_EXTERNAL STORAGE_/Documenti** |
| Film di directory | **_PRIVATE\_\_EXTERNAL STORAGE_/Movies** |
| DirectoryMusica | **_PRIVATE\_\_EXTERNAL STORAGE_/Musica** |
| DirectoryNotifications | **_PRIVATE\_\_EXTERNAL STORAGE_/Notifications** |
| DirectoryPodcasts (podcast) | **_PRIVATE\_\_EXTERNAL STORAGE_/Podcast** |
| DirectoryRingtones | **_PRIVATE\_\_EXTERNAL STORAGE_/Ringtones** |
| Immagini di Directory | **_PRIVATE\_\_EXTERNAL STORAGE_/Immagini** |

Per i dispositivi con più partizioni di archiviazione esterne, ogni partizione disterrà una directory destinata ai file privati. Il `Android.Content.Context.GetExternalFilesDirs(string type)` metodo restituirà `Java.IO.Files`una matrice di . Ogni oggetto rappresenterà una directory privata specifica dell'applicazione su tutti i dispositivi di archiviazione condivisi/esterni in cui l'applicazione può inserire i file di cui è proprietaria.

> [!IMPORTANT]
> Il percorso esatto della directory di archiviazione esterna privata può variare da dispositivo a dispositivo e tra le versioni di Android. Per questo motivo, le app non devono codificare il percorso di questa directory e `Android.Content.Context.GetExternalFilesDir()`usare invece le API Xamarin.Android, ad esempio .

### <a name="public-external-files"></a>File esterni pubblici

I file pubblici sono file che esistono in memoria esterna che non vengono archiviati nella directory allocata da Android per i file privati. I file pubblici non verranno eliminati quando l'app viene disinstallata. Alle app Android deve essere concessa l'autorizzazione prima di poter leggere o scrivere file pubblici. È possibile che i file pubblici esistano ovunque nell'archiviazione esterna, ma per `Android.OS.Environment.ExternalStorageDirectory`convenzione Android prevede che i file pubblici esistano nella directory identificata dalla proprietà . Questa proprietà restituirà un `Java.IO.File` oggetto che rappresenta la directory di archiviazione esterna primaria. Ad esempio, `Android.OS.Environment.ExternalStorageDirectory` può fare riferimento alla seguente directory:

```bash
/storage/emulated/0/
```

Questo documento farà riferimento alla directory di archiviazione per i file pubblici su archiviazione esterna come _PUBLIC\_EXTERNAL\_STORAGE_.

Android supporta anche il concetto di directory dell'applicazione su _PUBLIC\_EXTERNAL\_STORAGE_. Queste directory sono esattamente le stesse `PRIVATE_EXTERNAL_STORAGE` delle directory dell'applicazione per e sono descritte nella tabella della sezione precedente. Il `Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)` metodo restituirà un `Java.IO.File` oggetto che corrisponde a una directory dell'applicazione pubblica. Il `directoryType` parametro è obbligatorio `null`e non può essere .

Ad esempio, `Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath` la chiamata restituirà una stringa simile al seguente:For example, calling will return a string which will resemble:

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> Il percorso esatto della directory di archiviazione esterna pubblica può variare da dispositivo a dispositivo e tra le versioni di Android. Per questo motivo, le app non devono codificare il percorso di questa directory e `Android.OS.Environment.ExternalStorageDirectory`usare invece le API Xamarin.Android, ad esempio .

## <a name="working-with-external-storage"></a>Utilizzo dell'archiviazione esterna

Una volta che un'app Xamarin.Android ha ottenuto il percorso completo di un file, deve utilizzare qualsiasi API .NET standard per la creazione, la lettura, la scrittura o l'eliminazione di file. Ciò massimizza la quantità di codice compatibile con la piattaforma incrociata per un'app. Tuttavia, prima di tentare di accedere a un file un'app Xamarin.Android deve assicurarsi che sia possibile accedere a tale file.

1. **Verificare l'archiviazione** &ndash; esterna A seconda della natura dell'archiviazione esterna, è possibile che non sia montata e utilizzabile dall'app. Tutte le app devono controllare lo stato dell'archiviazione esterna prima di tentare di usarla.
2. **Eseguire un controllo** &ndash; delle autorizzazioni di runtime Un'app Android deve richiedere l'autorizzazione all'utente per accedere all'archiviazione esterna. Ciò significa che una richiesta di autorizzazione in fase di esecuzione deve essere eseguita prima di qualsiasi accesso ai file. La guida [Autorizzazioni In Xamarin.Android](~/android/app-fundamentals/permissions.md) contiene ulteriori dettagli sulle autorizzazioni Android.

Ognuna di queste due attività verrà discussa di seguito.

### <a name="verifying-that-external-storage-is-available"></a>Verifica della disponibilità dell'archiviazione esterna

Il primo passaggio prima di scrivere in un archivio esterno consiste nel verificarne la leggibilità o la scrittura. La `Android.OS.Environment.ExternalStorageState` proprietà contiene una stringa che identifica lo stato dell'archiviazione esterna. Questa proprietà restituirà una stringa che rappresenta lo stato. Questa tabella è un `ExternalStorageState` elenco dei valori `Environment.ExternalStorageState`che potrebbero essere restituiti da :

| ExternalStorageState | Descrizione  |
|----------------------|---|
| MediaBadRemoval      | Il supporto è stato bruscamente rimosso senza essere correttamente smontato. |
| Controllo multimediale        | Il supporto è presente ma è sottoposto a un controllo del disco.  |
| MediaEjecting        | I supporti sono in fase di smontaggio ed espulsione.  |
| SupportiMontati         | Il supporto è montato e può essere letto o scritto.  |
| MediaMountedReadOnly | Il supporto è montato ma può essere letto solo da. |
| Medianof            | Media è presente ma non contiene un file system adatto per Android. |
| MediaRemoved (Elementi multimediali)         | Non ci sono media presenti. |
| MediaShared          | Il supporto è presente, ma non è montato. Viene condiviso tramite USB con un altro dispositivo.|
| MediaSconosciuto         | Lo stato del supporto non è riconosciuto da Android. |
| MediaUnmountable (MediaUnmountable)     | Il supporto è presente ma non può essere montato da Android. |
| Medianoni       | Il supporto è presente ma non è montato. |

La maggior parte delle app Android dovrà solo verificare se lo spazio di archiviazione esterno è montato. Il frammento di codice seguente mostra come verificare che l'archiviazione esterna sia montata per l'accesso in sola lettura o in lettura e scrittura:The following code snippet shows how to verify that external storage is mounted for read-only access or read-write access:

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>Autorizzazioni di archiviazione esterna

Android considera l'accesso all'archiviazione esterna come _un'autorizzazione pericolosa,_ che in genere richiede all'utente di concedere l'autorizzazione per accedere alla risorsa. L'utente può revocare questa autorizzazione in qualsiasi momento.  Ciò significa che una richiesta di autorizzazione in fase di esecuzione deve essere eseguita prima di qualsiasi accesso ai file. Alle app vengono automaticamente concesse le autorizzazioni per leggere e scrivere i propri file privati. È possibile che le app leggano e scrivano i file privati che appartengono ad altre app dopo aver [ottenuto l'autorizzazione](~/android/app-fundamentals/permissions.md) dall'utente.

Tutte le app Android devono dichiarare una delle due autorizzazioni per l'archiviazione esterna in **AndroidManifest.xml** . Per identificare le autorizzazioni, uno `uses-permission` dei due elementi seguenti deve essere aggiunto a **AndroidManifest.xml**:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> Se l'utente `WRITE_EXTERNAL_STORAGE` `READ_EXTERNAL_STORAGE` concede , viene concessa anche in modo implicito. Non è necessario richiedere entrambe le autorizzazioni in **AndroidManifest.xml**.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Le autorizzazioni possono anche essere aggiunte utilizzando la scheda **Manifesto Android** delle proprietà della **soluzione**:

![Esplora soluzioni - Autorizzazioni necessarie per Visual StudioSolution Explorer - Required Permissions for Visual Studio](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Le autorizzazioni possono anche essere aggiunte utilizzando la scheda **Manifesto Android** del **riquadro delle proprietà**della soluzione :

[![Riquadro della soluzione - Autorizzazioni necessarie per Visual Studio per Mac](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

In generale, tutte le autorizzazioni pericolose devono essere approvate dall'utente. Le autorizzazioni per l'archiviazione esterna sono un'anomalia in quanto esistono eccezioni a questa regola, a seconda della versione di Android in esecuzione nell'app:

![Diagramma di flusso dei controlli delle autorizzazioni di archiviazione esterna](./images/external-permission-check-flowchart.png)

Per ulteriori informazioni sull'esecuzione di richieste di autorizzazione di runtime, consultare la guida [Autorizzazioni in Xamarin.Android](~/android/app-fundamentals/permissions.md). Il [file LocalFiles](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles) **monodroid-sample** illustra anche un modo per eseguire controlli delle autorizzazioni di runtime.

#### <a name="granting-and-revoking-permissions-with-adb"></a>Concessione e revoca delle autorizzazioni con ADB

Nel corso dello sviluppo di un'app Android, potrebbe essere necessario concedere e revocare le autorizzazioni per testare i vari flussi di lavoro coinvolti con i controlli delle autorizzazioni di runtime. È possibile eseguire questa operazione al prompt dei comandi utilizzando ADB. I frammenti della riga di comando seguenti illustrano come concedere o revocare le autorizzazioni usando ADB per un'app Android il cui nome di pacchetto è **com.nomeazienda.app:**

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>Eliminazione di file

Per eliminare un file dall'archiviazione esterna, è possibile usare una [`System.IO.File.Delete`](xref:System.IO.File.Delete*)qualsiasi delle API standard di C, ad esempio . È anche possibile utilizzare le API Java a scapito della portabilità del codice. Ad esempio:

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>Collegamenti correlati

* [Esempio di file locali Xamarin.Android su **campioni monodroidi**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [Autorizzazioni in Xamarin.Android](~/android/app-fundamentals/permissions.md)
