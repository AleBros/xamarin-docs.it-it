---
title: 'Xamarin.Essentials: Gli helper File System'
description: La classe del file System in Xamarin.Essentials contiene una serie di helper per trovare la cache di applicazione e le directory dei dati e aprire i file all'interno del pacchetto dell'app.
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 13293ec05261cbdc1e70fd278002d1af18654851
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815618"
---
# <a name="xamarinessentials-file-system-helpers"></a>Xamarin.Essentials: Gli helper File System

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **FileSystem** classe contiene una serie di helper per trovare le directory della cache e i dati dell'applicazione e aprire i file all'interno del pacchetto dell'app.

## <a name="using-file-system-helpers"></a>Uso di helper di File System

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per ottenere la directory dell'applicazione per archiviare **memorizzare nella cache dati**. I dati della cache sono utilizzabile per tutti i dati che richiede più tempo rispetto a dati temporanei, ma non i dati necessari per funzionare correttamente.

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

Per ottenere la directory principale dell'applicazione per tutti i file che non sono file di dati utente. Questi file vengono eseguito il backup con il sistema operativo la sincronizzazione di framework. Visualizzare informazioni specifiche sull'implementazione della piattaforma sottostante.

```csharp
var mainDir = FileSystem.AppDataDirectory;
```

Per aprire un file che è in bundle nel pacchetto dell'applicazione:

```csharp
 using (var stream = await FileSystem.OpenAppPackageFileAsync(templateFileName))
 {
    using (var reader = new StreamReader(stream))
    {
        var fileContents = await reader.ReadToEndAsync();
    }
 }
```

## <a name="platform-implementation-specifics"></a>Funzionalità specifiche di implementazione della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

- **CacheDirectory** : restituisce il [CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir) del contesto corrente.
- **AppDataDirectory** : restituisce il [FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir) del contesto corrente e sono sottoposti a backup usando [Backup automatico](https://developer.android.com/guide/topics/data/autobackup.html) avvio in API 23 e versioni successive.

Aggiungere qualsiasi file nei **asset** cartella in Android del progetto e contrassegnare l'azione di compilazione come **AndroidAsset** a usarla con `OpenAppPackageFileAsync`.

# <a name="iostabios"></a>[iOS](#tab/ios)

- **CacheDirectory** : restituisce il [Library/cache](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) directory.
- **AppDataDirectory** : restituisce il [libreria](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) directory in cui il backup in iTunes e iCloud.

Aggiungere qualsiasi file nei **le risorse** cartella IOS del progetto e contrassegnare l'azione di compilazione come **BundledResource** a usarla con `OpenAppPackageFileAsync`.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

- **CacheDirectory** : restituisce il [LocalCacheFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder) directory...
- **AppDataDirectory** : restituisce il [LocalFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder) directory in cui viene eseguito il backup nel cloud.

Aggiungere tutti i file nella radice del progetto UWP e contrassegnare l'azione di compilazione **contenuti** a usarla con `OpenAppPackageFileAsync`.

--------------

## <a name="api"></a>API

- [Codice di origine del file System helper](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/FileSystem)
- [Documentazione dell'API di sistema di file](xref:Xamarin.Essentials.FileSystem)
