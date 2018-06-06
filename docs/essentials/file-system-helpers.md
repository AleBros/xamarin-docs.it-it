---
title: 'Xamarin.Essentials: Gli helper File System'
description: La classe FileSystem in Xamarin.Essentials contiene una serie di helper per trovare la cache dell'applicazione e le directory dei dati e aprire i file all'interno del pacchetto dell'app.
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 13293ec05261cbdc1e70fd278002d1af18654851
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782585"
---
# <a name="xamarinessentials-file-system-helpers"></a>Xamarin.Essentials: Gli helper File System

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **FileSystem** classe contiene una serie di helper per trovare le directory di cache e i dati dell'applicazione e aprire i file all'interno del pacchetto dell'app.

## <a name="using-file-system-helpers"></a>Utilizzo di File System helper

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per ottenere la directory dell'applicazione per archiviare **memorizzare nella cache dati**. I dati della cache è utilizzabile per tutti i dati che devono persistere più tempo rispetto a dati temporanei, ma non devono essere dati necessari per funzionare correttamente.

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

Per ottenere la directory di primo livello dell'applicazione per tutti i file che non sono file di dati utente. Questi file vengono eseguito il backup con il sistema operativo la sincronizzazione di framework. Vedere le specifiche di implementazione della piattaforma sottostante.

```csharp
var mainDir = FileSystem.AppDataDirectory;
```

Per aprire un file che è in bundle nel pacchetto di applicazione:

```csharp
 using (var stream = await FileSystem.OpenAppPackageFileAsync(templateFileName))
 {
    using (var reader = new StreamReader(stream))
    {
        var fileContents = await reader.ReadToEndAsync();
    }
 }
```

## <a name="platform-implementation-specifics"></a>Informazioni specifiche sull'implementazione della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

- **CacheDirectory** : restituisce il [CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir) del contesto corrente.
- **AppDataDirectory** : restituisce il [FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir) del contesto corrente e sono sottoposti a backup tramite [Backup automatico](https://developer.android.com/guide/topics/data/autobackup.html) avvio in API 23 e versioni successive.

Aggiungere qualsiasi file nel **asset** cartella di Android del progetto e contrassegnare l'azione di compilazione come **AndroidAsset** a usarlo con `OpenAppPackageFileAsync`.

# <a name="iostabios"></a>[iOS](#tab/ios)

- **CacheDirectory** : restituisce il [libreria/cache](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) directory.
- **AppDataDirectory** : restituisce il [libreria](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) directory in cui il backup in iTunes e iCloud.

Aggiungere qualsiasi file nel **risorse** cartella in iOS del progetto e contrassegnare l'azione di compilazione come **BundledResource** a usarlo con `OpenAppPackageFileAsync`.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

- **CacheDirectory** : restituisce il [LocalCacheFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder) directory...
- **AppDataDirectory** : restituisce il [LocalFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder) directory in cui viene eseguito il backup nel cloud.

Aggiungere tutti i file nella directory principale nel progetto UWP e contrassegnare l'azione di compilazione come **contenuto** a usarlo con `OpenAppPackageFileAsync`.

--------------

## <a name="api"></a>API

- [Codice sorgente di file System helper](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/FileSystem)
- [Documentazione dell'API di sistema di file](xref:Xamarin.Essentials.FileSystem)
