---
title: Accesso ai file nella risorsa di archiviazione esterna con xamarin. Android
description: Questa guida illustra l'accesso ai file nella risorsa di archiviazione esterna in xamarin. Android
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: 78051fce44239eea86948988a4d19ac37c5ea0d5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953222"
---
# <a name="external-storage"></a>Archiviazione esterna

Archiviazione esterna fa riferimento a spazio di archiviazione file è non in memoria interna e non è accessibile esclusivamente all'app che è responsabile per il file. Lo scopo principale di archiviazione esterno è fornire una posizione in cui inserire i file che sono concepite per essere condivisi tra le app o che sono troppo grandi per rientrare nello spazio di archiviazione interna.

Archiviazione esterna, storicamente pronuncia definita una partizione disco su supporti rimovibili, ad esempio una scheda SD (nota anche come _dispositivo di archiviazione portatile_). Questa distinzione non è più importante come dispositivi Android si sono evolute e numero di dispositivi Android non supporterà più archivi rimovibili. Ma alcuni dispositivi allocherà alcune delle loro memoria non volatile interna quali Android per eseguire lo stesso supporto rimovibile (funzione). Questo è noto come _emulato_ archiviazione ed è ancora considerato come risorsa di archiviazione esterna. In alternativa, alcuni dispositivi Android possono avere più partizioni di archiviazione esterna. Ad esempio, un Android tablet (oltre alla memoria interna) potrebbe avere emulati archiviazione e uno o più degli slot per una scheda SD. Tutte le partizioni vengono considerate da Android come risorsa di archiviazione esterna.

Sui dispositivi che hanno più utenti, ogni utente disporrà di una directory dedicata nella partizione primaria di archiviazione esterna per la loro archiviazione esterna. Le App in esecuzione con un account utente non saranno possibile accedere ai file da un altro utente nel dispositivo. I file per tutti gli utenti sono ancora world leggibile e scrivibile; mondo Tuttavia, ogni profilo utente dagli altri Android verrà sandbox.

La lettura e scrittura su file è quasi identica in xamarin. Android come succede per qualsiasi altra applicazione .NET. L'app xamarin. Android determina il percorso del file che verrà modificato, quindi Usa standard .NET idiomi per l'accesso ai file. Poiché i percorsi effettivi in un archivio interno ed esterno possono variare da un dispositivo a altro o dalla versione di Android per la versione di Android non è consigliabile a livello di codice il percorso dei file. Al contrario, xamarin. Android espone le API di Android native che consentiranno di determinare il percorso dei file nell'archiviazione interna ed esterna.

Questa guida illustra i concetti e le API di Android che sono specifici di archiviazione esterna.

## <a name="public-and-private-files-on-external-storage"></a>File pubblici e privati nella risorsa di archiviazione esterna

Esistono due diversi tipi di file che un'app può mantenere su risorsa di archiviazione esterna:

* **Privato** file &ndash; file privati sono file che sono specifici per l'applicazione (ma sono comunque world leggibile e modificabile). Android si aspetta che i file privati vengono archiviati in una directory specifica nella risorsa di archiviazione esterna. Anche se i file vengono denominati "privati", sono ancora visibili e accessibili da altre App sul dispositivo, non sono previsti alcuna protezione speciale da Android.

* **Pubblica** file &ndash; questi sono file che non sono considerati a essere specifiche dell'applicazione e sono concepiti per essere condivisi liberamente.

Le differenze tra questi file è principalmente concettuale. File privati sono privati nel senso che sono considerati a far parte dell'applicazione, mentre i file pubblici sono tutti gli altri file presente nella risorsa di archiviazione esterno. Android offre due API diverse per la risoluzione di percorsi ai file privati e pubblici, ma in caso contrario, le stesse API di .NET consentono di leggere e scrivere in questi file. Queste sono le stesse API che sono descritte nella sezione sul [la lettura e scrittura](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage).

### <a name="private-external-files"></a>File esterni privati

Privato file esterni sono considerati specifici di un'applicazione, simile a file interni, ma vengono mantenuti nella risorsa di archiviazione esterna per diversi motivi (ad esempio si trova troppo grande per l'archiviazione interna). Analogamente ai file interni, questi file verranno eliminati quando l'app venga disinstallata dall'utente.

Viene trovata la posizione primaria per i file esterni privati chiamando il metodo `Android.Content.Context.GetExternalFilesDir(string type)`. Questo metodo restituirà un `Java.IO.File` oggetto che rappresenta la directory di archiviazione esterno privato per l'app. Il passaggio `null` a questo metodo restituirà il percorso alla directory di archiviazione dell'utente per l'applicazione. Ad esempio, per un'applicazione con il nome del pacchetto `com.companyname.app`, la directory "root" dei file esterni privati sarebbe:

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

Questo documento si farà riferimento alla directory di archiviazione per i file privati nella risorsa di archiviazione esterna come _privati\_esterni\_archiviazione_.


Il parametro per `GetExternalFilesDir()` è una stringa che specifica un' _directory dell'applicazione_. Questa è una directory lo scopo di fornire una posizione standard per un'organizzazione logica dei file. I valori stringa sono disponibili tramite le costanti nel `Android.OS.Environment` classe:

| `Android.OS.Environment` | Directory |
|-|-|
| DirectoryAlarms | **_PRIVATE\_EXTERNAL\_STORAGE_/Alarms** |
| DirectoryDcim | **_PRIVATE\_EXTERNAL\_STORAGE_/DCIM** |
| DirectoryDownloads | **_PRIVATE\_EXTERNAL\_STORAGE_/Download** |
| DirectoryDocuments | **_PRIVATE\_EXTERNAL\_STORAGE_/Documents** |
| DirectoryMovies | **_PRIVATE\_EXTERNAL\_STORAGE_/Movies** |
| DirectoryMusic | **_PRIVATE\_EXTERNAL\_STORAGE_/Music** |
| DirectoryNotifications | **_PRIVATE\_EXTERNAL\_STORAGE_/Notifications** |
| DirectoryPodcasts | **_PRIVATE\_EXTERNAL\_STORAGE_/Podcasts** |
| DirectoryRingtones | **_PRIVATE\_EXTERNAL\_STORAGE_/Ringtones** |
| DirectoryPictures | **_PRIVATE\_EXTERNAL\_STORAGE_/Pictures** |

Per i dispositivi con più partizioni di archiviazione esterna, ogni partizione avrà una directory in cui è destinata a file privati. Il metodo `Android.Content.Context.GetExternalFilesDirs(string type)` restituirà una matrice di `Java.IO.Files`. Ogni oggetto rappresenta una directory specifiche dell'applicazione privata in tutti i dispositivi di archiviazione condiviso ed esterni in cui l'applicazione può inserire i file di sua proprietà.

> [!IMPORTANT]
> Nella directory di archiviazione esterno privato il percorso esatto può variare da dispositivo a dispositivo e tra le versioni di Android. Per questo motivo, le app devono non hard il percorso alla directory del codice e usare invece le APIs xamarin. Android, ad esempio `Android.Content.Context.GetExternalFilesDir()`.

### <a name="public-external-files"></a>File esterni pubblici

File pubblici sono i file esistenti nella risorsa di archiviazione esterna che non vengono archiviati nella directory che consente di allocare Android per i file privati. File pubblici non verranno eliminati quando l'app viene disinstallata. Le app Android devono essere concessa l'autorizzazione prima che possano leggere o scrivere i file pubblici. È possibile visualizzare i file pubblici devono trovarsi in qualsiasi punto su risorsa di archiviazione esterna, ma per convenzione Android prevede che i file pubblici esista nella directory identificata dalla proprietà `Android.OS.Environment.ExternalStorageDirectory`. Questa proprietà restituirà un `Java.IO.File` oggetto che rappresenta la directory di archiviazione esterna primario. Ad esempio, `Android.OS.Environment.ExternalStorageDirectory` può fare riferimento alla directory seguente:

```bash
/storage/emulated/0/
```

Questo documento si farà riferimento alla directory di archiviazione per i file pubblici nell'archiviazione esterna come _pubbliche\_esterni\_archiviazione_.


Android supporta inoltre il concetto di directory applicative nei _pubbliche\_esterni\_archiviazione_. Tali directory sono esattamente le stesse directory delle applicazioni per `_PRIVATE\_EXTERNAL\_STORAGE_` e sono descritte nella tabella nella sezione precedente. Il metodo `Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)` restituirà un `Java.IO.File` oggetti che corrispondono a una directory pubblico dell'applicazione. Il `directoryType` parametro è un parametro obbligatorio e non può essere `null`.

Ad esempio, la chiamata `Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath` restituirà una stringa che sarà simile a:

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> L'esatto percorso della directory di archiviazione esterna pubblica può variare da dispositivo a dispositivo e tra le versioni di Android. Per questo motivo, le app devono non hard il percorso alla directory del codice e usare invece le APIs xamarin. Android, ad esempio `Android.OS.Environment.ExternalStorageDirectory`.

## <a name="working-with-external-storage"></a>Uso di archiviazione esterna

Una volta un'app xamarin. Android ha ottenuto il percorso completo in un file, consigliabile utilizzare una delle API .NET standard per la creazione, lettura, scrittura o eliminazione dei file. Questo ottimizza la quantità di cross-platform codice compatibile per un'app. Tuttavia, prima di tentare di accedere a un file di un'app xamarin. Android necessario assicurarsi che è possibile accedere a tale file.

1. **Verificare l'archiviazione esterna** &ndash; a seconda della natura della risorsa di archiviazione esterna, è possibile che si potrebbe non essere montato e utilizzabili dall'app. Tutte le app devono controllare lo stato della risorsa di archiviazione esterna prima di provare a usarlo.
2. **Eseguire una verifica delle autorizzazioni di runtime** &ndash; An Android app deve richiedere l'autorizzazione da parte dell'utente per accedere a risorsa di archiviazione esterna. Ciò significa che una fase di esecuzione richiesta di autorizzazione deve essere eseguita prima di accedere ai file. La Guida [le autorizzazioni In xamarin. Android](~/android/app-fundamentals/permissions.md) contiene altri dettagli sulle autorizzazioni di Android.

Ognuna di queste due attività verrà illustrata di seguito.

### <a name="verifying-that-external-storage-is-available"></a>Verifica che l'archiviazione esterna è disponibile

Il primo passaggio prima della scrittura in un archivio esterno è che sia leggibile o scrivibile. Il `Android.OS.Environment.ExternalStorageState` proprietà contiene una stringa che identifica lo stato della risorsa di archiviazione esterna. Questa proprietà restituirà una stringa che rappresenta lo stato. Questa tabella è un elenco del `ExternalStorageState` i valori che potrebbero essere restituiti da `Environment.ExternalStorageState`:

| ExternalStorageState | Descrizione  |
|----------------------|---|
| MediaBadRemoval      | Il supporto è stato rimosso in modo anomalo senza correttamente non montato. |
| MediaChecking        | È presente il supporto, ma in fase di un disco di controllo.  |
| MediaEjecting        | Media è in corso da smontare ed espulso.  |
| MediaMounted         | Media viene montato e possono essere letti o scritti.  |
| MediaMountedReadOnly | Supporti sono montato, ma possono essere letti solo dal. |
| MediaNofs            | Supporti sono presenti ma non contiene un file System adatto per Android. |
| MediaRemoved         | Non è presentano alcun supporto. |
| MediaShared          | Supporti sono presenti, ma non sono montato. Si è condiviso tramite USB in un altro dispositivo.|
| MediaUnknown         | Lo stato del contenuto multimediale è non riconosciuto da Android. |
| MediaUnmountable     | Il supporto è presente ma non è possibile montarli su Android. |
| MediaUnmounted       | Il supporto è presente ma non è montato. |


La maggior parte delle App per Android necessiterà soltanto di verificare se l'archiviazione esterna viene montato. Il frammento di codice seguente viene illustrato come verificare che l'archiviazione esterna sia montato per l'accesso di sola lettura o accesso in lettura / scrittura:

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>Autorizzazioni di archiviazione esterna

Android prende in considerazione l'accesso all'archiviazione esterna per essere un' _autorizzazioni pericolose_, che in genere richiede all'utente di concedere l'autorizzazione ad accedere alla risorsa. L'utente può revocare questa autorizzazione in qualsiasi momento.  Ciò significa che una fase di esecuzione richiesta di autorizzazione deve essere eseguita prima di accedere ai file. Le app vengono automaticamente concesse autorizzazioni per leggere e scrivere i propri file privati. È possibile che le app di leggere e scrivere i file privati che appartengono ad altre App dopo essere stati [concessa l'autorizzazione](~/android/app-fundamentals/permissions.md) dall'utente.

Tutte le app Android devono dichiarare una delle due autorizzazioni per l'archiviazione esterna nella **androidmanifest. XML** . Per identificare le autorizzazioni, uno dei due seguenti `uses-permission` è necessario aggiungere elementi al **androidmanifest. XML**:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> Se l'utente concede `WRITE_EXTERNAL_STORAGE`, quindi `READ_EXTERNAL_STORAGE` è inoltre in modo implicito concesso. Non è necessario richiedere entrambe le autorizzazioni in **androidmanifest. XML**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Le autorizzazioni possono essere aggiunti anche tramite il **manifesto Android** scheda della finestra di **proprietà della soluzione**:

![Esplora soluzioni - delle autorizzazioni necessarie per Visual Studio](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Le autorizzazioni possono essere aggiunti anche tramite il **manifesto Android** scheda della finestra di **riquadro delle proprietà di soluzione**:

[![Riquadro della soluzione - delle autorizzazioni necessarie per Visual Studio per Mac](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

In generale, tutte le autorizzazioni pericolose devono essere approvate dall'utente. Le autorizzazioni per l'archiviazione esterna sono un'anomalia in quanto esistono eccezioni a questa regola, a seconda della versione di Android che viene eseguita l'app:

![Diagramma di flusso dei controlli delle autorizzazioni di archiviazione esterna](./images/external-permission-check-flowchart.png)

Per altre informazioni su come eseguire le richieste di autorizzazione di runtime, consultare la Guida [le autorizzazioni In xamarin. Android](~/android/app-fundamentals/permissions.md). Il **monodroid-sample** [LocalFiles](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles) viene inoltre illustrato come eseguire controlli delle autorizzazioni di runtime.

#### <a name="granting-and-revoking-permissions-with-adb"></a>La concessione e la revoca delle autorizzazioni con ADB

Durante lo sviluppo di un'app per Android, è necessario concedere e revocare le autorizzazioni per testare i vari flussi di lavoro coinvolti con i controlli di autorizzazione di runtime. È possibile eseguire questa operazione al prompt dei comandi utilizzando ADB. I frammenti di riga di comando seguenti illustrano come concedere o revocare autorizzazioni utilizzando ADB per un'app per Android è il cui nome pacchetto **com.companyname.app**:

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>L'eliminazione di file

Qualsiasi dello standard C# API possono essere usate per eliminare un file dalla risorsa di archiviazione esterna, ad esempio [ `System.IO.File.Delete` ](xref:System.IO.File.Delete*). È anche possibile usare le API Java a scapito della portabilità del codice. Ad esempio:

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>Collegamenti correlati

* [File locali di xamarin. Android di esempio su **monodroid-samples**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [Permissions In Xamarin.Android](~/android/app-fundamentals/permissions.md)
