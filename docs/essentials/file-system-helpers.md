---
title: 'Xamarin.Essentials: File System Helpers'
description: La classe FileSystem in Xamarin.Essentials contiene una serie di helper per trovare le directory della cache e dei dati dell'applicazione e per aprire i file all'interno del pacchetto dell'app.
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 37a4fdbdc3f7e1ee309ee9d49c7ad67374035ab4
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617527"
---
# <a name="xamarinessentials-file-system-helpers"></a>Xamarin.Essentials: File System Helpers

![NuGet in versione non definitiva](~/media/shared/pre-release.png)

La classe **FileSystem** contiene una serie di helper per trovare le directory della cache e dei dati dell'applicazione e per aprire i file all'interno del pacchetto dell'app.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-file-system-helpers"></a>Uso di File System Helpers

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Per ottenere la directory dell'applicazione in cui archiviare i **dati della cache**. I dati della cache sono utilizzabili per tutti i dati che è necessario conservare per un periodo più lungo dei dati temporanei, ma non devono essere dati necessari per il corretto funzionamento.

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

Per ottenere la directory principale dell'applicazione per tutti i file che non sono file di dati utente. Il backup di questi file viene eseguito con il framework di sincronizzazione del sistema operativo. Vedere più avanti Informazioni di implementazione specifiche della piattaforma.

```csharp
var mainDir = FileSystem.AppDataDirectory;
```

Per aprire un file in bundle nel pacchetto dell'applicazione:

```csharp
 using (var stream = await FileSystem.OpenAppPackageFileAsync(templateFileName))
 {
    using (var reader = new StreamReader(stream))
    {
        var fileContents = await reader.ReadToEndAsync();
    }
 }
```

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

- **CacheDirectory**: restituisce l'elemento [CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir) del contesto corrente.
- **AppDataDirectory**: restituisce l'elemento [FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir) del contesto corrente e il backup viene eseguito usando il [backup automatico](https://developer.android.com/guide/topics/data/autobackup.html) a partire dall'API 23 e versioni successive.

Aggiungere i file nella cartella **Assets** del progetto Android e contrassegnare l'azione di compilazione come **AndroidAsset** per usarla con `OpenAppPackageFileAsync`.

# <a name="iostabios"></a>[iOS](#tab/ios)

- **CacheDirectory**: restituisce la directory [Library/Caches](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).
- **AppDataDirectory**: restituisce la directory [Library](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) di cui viene eseguito un backup da iTunes e iCloud.

Aggiungere i file nella cartella **Resources** del progetto iOS e contrassegnare l'azione di compilazione come **BundledResource** per usarla con `OpenAppPackageFileAsync`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- **CacheDirectory**: restituisce la directory [LocalCacheFolder](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder).
- **AppDataDirectory**: restituisce la directory [LocalFolder](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder) di cui viene eseguito un backup nel cloud.

Aggiungere i file nella radice del progetto UWP e contrassegnare l'azione di compilazione come **Content** per usarla con `OpenAppPackageFileAsync`.

--------------

## <a name="api"></a>API

- [Codice sorgente di File System Helpers](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/FileSystem)
- [Documentazione dell'API File System](xref:Xamarin.Essentials.FileSystem)
