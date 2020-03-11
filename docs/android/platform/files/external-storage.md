---
title: Accesso ai file in un archivio esterno con Novell. Android
description: Questa guida illustra l'accesso ai file nell'archiviazione esterna in Novell. Android
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 96b0d6a00c7825939b1f89ed63e3e5559ca4ef59
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "73020478"
---
# <a name="external-storage"></a>Archiviazione esterna

L'archiviazione esterna si riferisce all'archiviazione di file che non si trova nella risorsa di archiviazione interna e non è accessibile esclusivamente all'app responsabile del file. Lo scopo principale dell'archiviazione esterna è fornire un posto in cui inserire i file che devono essere condivisi tra le app o che sono troppo grandi per adattarsi alla risorsa di archiviazione interna.

In passato, lo spazio di archiviazione esterno faceva riferimento a una partizione disco su un supporto rimovibile, ad esempio una scheda SD (nota anche come _archiviazione portatile_). Questa distinzione non è più pertinente perché i dispositivi Android si sono evoluti e molti dispositivi Android non supportano più l'archiviazione rimovibile. Alcuni dispositivi, invece, allocano parte della memoria interna non volatile, che Android deve eseguire lo stesso supporto rimovibile della funzione. Questa operazione è nota come archiviazione _emulata_ e viene comunque considerata come archiviazione esterna. In alternativa, alcuni dispositivi Android possono avere più partizioni di archiviazione esterne. Ad esempio, un Tablet Android (oltre alla relativa archiviazione interna) potrebbe avere l'archiviazione emulata e uno o più slot per una scheda SD. Tutte queste partizioni vengono gestite da Android come archiviazione esterna.

Nei dispositivi che hanno più utenti, ogni utente avrà una directory dedicata sulla partizione di archiviazione esterna primaria per l'archiviazione esterna. Le app in esecuzione come un utente non avranno accesso ai file di un altro utente nel dispositivo. I file per tutti gli utenti sono ancora leggibili in tutto il mondo e possono essere scritti in tutto il mondo. Tuttavia, Android effettuerà il sandboxing di ogni profilo utente dagli altri.

La lettura e la scrittura nei file sono quasi identiche in Novell. Android così come per qualsiasi altra applicazione .NET. L'app Novell. Android determina il percorso del file che verrà modificato, quindi usa idiomi .NET standard per l'accesso ai file. Poiché i percorsi effettivi di archiviazione interna ed esterna possono variare da dispositivo a dispositivo o dalla versione Android alla versione Android, non è consigliabile codificare in modo rigido il percorso dei file. Al contrario, Novell. Android espone le API native di Android che consentono di determinare il percorso dei file in un archivio interno ed esterno.

Questa guida illustra i concetti e le API in Android specifici per l'archiviazione esterna.

## <a name="public-and-private-files-on-external-storage"></a>File pubblici e privati in una risorsa di archiviazione esterna

Esistono due tipi diversi di file che possono essere conservati da un'app sull'archiviazione esterna:

* I file **privati** &ndash; file privati sono file specifici dell'applicazione (ma sono ancora leggibili e accessibili dal mondo). Android prevede che i file privati siano archiviati in una directory specifica in una risorsa di archiviazione esterna. Anche se i file sono denominati "privati", sono ancora visibili e accessibili da altre app nel dispositivo, non sono consentiti per la protezione speciale da Android.

* I file **pubblici** &ndash; si tratta di file che non sono considerati specifici dell'applicazione e che devono essere condivisi liberamente.

Le differenze tra questi file sono principalmente concettuali. I file privati sono privati nel senso che sono considerati parte dell'applicazione, mentre i file pubblici sono tutti gli altri file presenti nella risorsa di archiviazione esterna. Android offre due API diverse per la risoluzione dei percorsi di file privati e pubblici, ma in caso contrario vengono usate le stesse API .NET per leggere e scrivere in questi file. Si tratta delle stesse API illustrate nella sezione relativa alla [lettura e alla scrittura](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage).

### <a name="private-external-files"></a>File esterni privati

I file esterni privati sono considerati specifici di un'applicazione (analogamente ai file interni), ma vengono mantenuti nella risorsa di archiviazione esterna per diversi motivi, ad esempio se sono troppo grandi per l'archiviazione interna. Analogamente ai file interni, questi file verranno eliminati quando l'app viene disinstallata dall'utente.

La posizione primaria per i file esterni privati viene rilevata chiamando il metodo `Android.Content.Context.GetExternalFilesDir(string type)`. Questo metodo restituirà un oggetto `Java.IO.File` che rappresenta la directory di archiviazione esterna privata per l'app. Il passaggio di `null` a questo metodo restituirà il percorso della directory di archiviazione dell'utente per l'applicazione. Per un'applicazione con il nome del pacchetto `com.companyname.app`, ad esempio, la directory radice dei file esterni privati è:

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

In questo documento si fa riferimento alla directory di archiviazione per i file privati nell'archiviazione esterna come _privata\_\_di archiviazione esterna_.

Il parametro per `GetExternalFilesDir()` è una stringa che specifica una _directory dell'applicazione_. Si tratta di una directory progettata per fornire una posizione standard per un'organizzazione logica di file. I valori stringa sono disponibili tramite costanti nella classe `Android.OS.Environment`:

| `Android.OS.Environment` | Directory |
|-|-|
| DirectoryAlarms | **/Alarms _privato\_archiviazione esterna\__** |
| DirectoryDcim | **/DCIM _privato\_archiviazione esterna\__** |
| DirectoryDownloads | **/Download _privato\_archiviazione esterna\__** |
| DirectoryDocuments | **/Documents _privato\_archiviazione esterna\__** |
| DirectoryMovies | **/Movies _privato\_archiviazione esterna\__** |
| DirectoryMusic | **/Music _privato\_archiviazione esterna\__** |
| DirectoryNotifications | **/Notifications _privato\_archiviazione esterna\__** |
| DirectoryPodcasts | **/Podcasts _privato\_archiviazione esterna\__** |
| DirectoryRingtones | **/Ringtones _privato\_archiviazione esterna\__** |
| DirectoryPictures | **/Pictures _privato\_archiviazione esterna\__** |

Per i dispositivi che dispongono di più partizioni di archiviazione esterne, ogni partizione avrà una directory destinata ai file privati. Il metodo `Android.Content.Context.GetExternalFilesDirs(string type)` restituirà una matrice di `Java.IO.Files`. Ogni oggetto rappresenterà una directory privata specifica dell'applicazione in tutti i dispositivi di archiviazione condivisi/esterni in cui l'applicazione può inserire i file di sua proprietà.

> [!IMPORTANT]
> Il percorso esatto della directory di archiviazione esterna privata può variare da dispositivo a dispositivo e da una versione all'altra di Android. Per questo motivo, le app non devono impostare come hardcoded il percorso di questa directory e usare invece le API Novell. Android, ad esempio `Android.Content.Context.GetExternalFilesDir()`.

### <a name="public-external-files"></a>File esterni pubblici

I file pubblici sono file presenti in una risorsa di archiviazione esterna che non sono archiviati nella directory allocata da Android per i file privati. I file pubblici non verranno eliminati quando l'app viene disinstallata. È necessario concedere all'app Android le autorizzazioni prima di poter leggere o scrivere file pubblici. È possibile che i file pubblici esistano ovunque in una risorsa di archiviazione esterna, ma per convenzione Android prevede che i file pubblici siano presenti nella directory identificata dalla proprietà `Android.OS.Environment.ExternalStorageDirectory`. Questa proprietà restituirà un oggetto `Java.IO.File` che rappresenta la directory di archiviazione esterna primaria. Ad esempio, `Android.OS.Environment.ExternalStorageDirectory` possibile fare riferimento alla directory seguente:

```bash
/storage/emulated/0/
```

Questo documento fa riferimento alla directory di archiviazione per i file pubblici nell'archiviazione esterna come _public\_archiviazione\_esterna_.

Android supporta inoltre il concetto di directory applicative in un _\_pubblico esterno\_archiviazione_. Queste directory corrispondono esattamente alle directory dell'applicazione per `PRIVATE_EXTERNAL_STORAGE` e sono descritte nella tabella della sezione precedente. Il metodo `Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)` restituirà un oggetto `Java.IO.File` che corrisponde a una directory dell'applicazione pubblica. Il parametro `directoryType` è un parametro obbligatorio e non può essere `null`.

Se ad esempio si chiama `Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath`, viene restituita una stringa simile alla seguente:

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> Il percorso esatto della directory di archiviazione esterna pubblica può variare da dispositivo a dispositivo e da una versione all'altra di Android. Per questo motivo, le app non devono impostare come hardcoded il percorso di questa directory e usare invece le API Novell. Android, ad esempio `Android.OS.Environment.ExternalStorageDirectory`.

## <a name="working-with-external-storage"></a>Utilizzo dell'archiviazione esterna

Una volta che un'app Novell. Android ha ottenuto il percorso completo di un file, deve usare una delle API .NET standard per la creazione, la lettura, la scrittura o l'eliminazione di file. Questa operazione massimizza la quantità di codice compatibile multipiattaforma per un'app. Tuttavia, prima di tentare di accedere a un file, un'app Novell. Android deve garantire che sia possibile accedere a tale file.

1. **Verificare &ndash; di archiviazione esterna** a seconda della natura dell'archiviazione esterna, è possibile che potrebbe non essere montata e utilizzabile dall'app. Tutte le app devono controllare lo stato della risorsa di archiviazione esterna prima di provare a usarla.
2. **Eseguire un controllo delle autorizzazioni di runtime** &ndash; un'app Android deve richiedere l'autorizzazione all'utente per accedere all'archiviazione esterna. Ciò significa che è necessario eseguire una richiesta di autorizzazione in fase di esecuzione prima di qualsiasi accesso ai file. Le [autorizzazioni per la Guida in Novell. Android](~/android/app-fundamentals/permissions.md) contengono altre informazioni sulle autorizzazioni per Android.

Ognuna di queste due attività verrà illustrata di seguito.

### <a name="verifying-that-external-storage-is-available"></a>Verifica della disponibilità dell'archiviazione esterna

Il primo passaggio prima della scrittura nella risorsa di archiviazione esterna consiste nel verificare che sia leggibile o scrivibile. La proprietà `Android.OS.Environment.ExternalStorageState` include una stringa che identifica lo stato dell'archiviazione esterna. Questa proprietà restituirà una stringa che rappresenta lo stato. Questa tabella è un elenco dei valori `ExternalStorageState` che potrebbero essere restituiti da `Environment.ExternalStorageState`:

| ExternalStorageState | Descrizione  |
|----------------------|---|
| MediaBadRemoval      | Il supporto è stato rimosso improvvisamente senza essere stato smontato correttamente. |
| MediaChecking        | Il supporto è presente, ma è in corso un controllo del disco.  |
| MediaEjecting        | È in corso la disinstallazione e la rimozione dei supporti.  |
| MediaMounted         | I supporti sono montati e possono essere letti o scritti.  |
| MediaMountedReadOnly | I supporti sono montati ma possono essere letti solo da. |
| MediaNofs            | Il supporto è presente, ma non contiene un file System adatto per Android. |
| MediaRemoved         | Non è presente alcun supporto. |
| MediaShared          | Il supporto è presente, ma non è montato. Viene condiviso tramite USB con un altro dispositivo.|
| MediaUnknown         | Lo stato del supporto non è riconosciuto da Android. |
| MediaUnmountable     | Il supporto è presente, ma non può essere montato da Android. |
| MediaUnmounted       | Il supporto è presente, ma non è montato. |

La maggior parte delle app Android deve verificare solo se è montata l'archiviazione esterna. Il frammento di codice seguente illustra come verificare che l'archiviazione esterna sia montata per l'accesso in sola lettura o in lettura/scrittura:

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>Autorizzazioni di archiviazione esterne

Android considera l'accesso a una risorsa di archiviazione esterna come un' _autorizzazione pericolosa_, che in genere richiede all'utente di concedere l'autorizzazione per accedere alla risorsa. L'utente può revocare l'autorizzazione in qualsiasi momento.  Ciò significa che è necessario eseguire una richiesta di autorizzazione in fase di esecuzione prima di qualsiasi accesso ai file. Alle app vengono concesse automaticamente le autorizzazioni per la lettura e la scrittura dei propri file privati. È possibile che le app leggano e scrivano i file privati che appartengono ad altre app dopo aver [ottenuto l'autorizzazione](~/android/app-fundamentals/permissions.md) da parte dell'utente.

Tutte le app Android devono dichiarare una delle due autorizzazioni per l'archiviazione esterna in **file AndroidManifest. XML** . Per identificare le autorizzazioni, è necessario aggiungere uno dei due elementi `uses-permission` seguenti a **file AndroidManifest. XML**:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> Se l'utente concede `WRITE_EXTERNAL_STORAGE`, anche `READ_EXTERNAL_STORAGE` viene concesso in modo implicito. Non è necessario richiedere entrambe le autorizzazioni in **file AndroidManifest. XML**.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

È possibile aggiungere le autorizzazioni anche usando la scheda **manifesto Android** delle **proprietà della soluzione**:

![Esplora soluzioni le autorizzazioni necessarie per Visual Studio](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

È possibile aggiungere le autorizzazioni anche usando la scheda **manifesto Android** del **riquadro proprietà soluzione**:

[![riquadro della soluzione le autorizzazioni necessarie per Visual Studio per Mac](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

In generale, tutte le autorizzazioni pericolose devono essere approvate dall'utente. Le autorizzazioni per l'archiviazione esterna sono un'anomalia in quanto vi sono eccezioni a questa regola, a seconda della versione di Android in esecuzione nell'app:

![Diagramma di flusso dei controlli delle autorizzazioni di archiviazione esterni](./images/external-permission-check-flowchart.png)

Per ulteriori informazioni sull'esecuzione delle richieste di autorizzazione di runtime, consultare le autorizzazioni della Guida [in Novell. Android](~/android/app-fundamentals/permissions.md). Il [LocalFiles](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles) di **esempio monodroid** Mostra anche un modo per eseguire i controlli delle autorizzazioni di Runtime.

#### <a name="granting-and-revoking-permissions-with-adb"></a>Concessione e revoca delle autorizzazioni con ADB

Nel corso dello sviluppo di un'app Android, potrebbe essere necessario concedere e revocare le autorizzazioni per testare i vari flussi di lavoro necessari per i controlli delle autorizzazioni di Runtime. È possibile eseguire questa operazione dal prompt dei comandi usando ADB. I frammenti di codice seguenti illustrano come concedere o revocare le autorizzazioni usando ADB per un'app Android il cui nome del pacchetto è **com. CompanyName. app**:

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>Eliminazione di file

È possibile usare qualsiasi C# API standard per eliminare un file dalla risorsa di archiviazione esterna, ad esempio [`System.IO.File.Delete`](xref:System.IO.File.Delete*). È anche possibile usare le API Java a scapito della portabilità del codice. Ad esempio,

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>Collegamenti correlati

* [Esempio di file locali Novell. Android su **monodroid-esempi**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [Autorizzazioni in Novell. Android](~/android/app-fundamentals/permissions.md)
