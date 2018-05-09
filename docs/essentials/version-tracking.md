---
title: Rilevamento delle versioni Xamarin.Essentials
description: La classe VersionTracking consente di controllare la versione di applicazioni e i numeri di build e visualizzare informazioni aggiuntive quali come se fosse la prima volta che l'applicazione avviata mai o per la versione corrente, ottengono le informazioni sulla compilazione precedente e altro ancora.
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ec9d62589ddfb270d5c8a5321b3bc733fc597e4b
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-version-tracking"></a>Rilevamento delle versioni Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **VersionTracking** classe consente di controllare la versione di applicazioni e i numeri di build e visualizzare informazioni aggiuntive quali come se fosse la prima volta che l'applicazione avviata mai o per la versione corrente, ottengono precedente informazioni sulla compilazione e altro ancora.

## <a name="using-version-tracking"></a>Utilizzo del rilevamento di versione

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La prima volta che utilizza il **VersionTracking** classe verrà avviata la versione corrente di rilevamento. È necessario chiamare `Track` anticipata solo nell'applicazione ogni volta che viene caricato per verificare le informazioni sulla versione corrente viene tenuta traccia:

```csharp
VersionTracking.Track();
```

Dopo l'iniziale `Track` viene chiamato può leggere le informazioni sulla versione:

```csharp

// First time ever launched application
var firstLaunch = VersionTracking.IsFirstLaunchEver;

// First time launching current version
var firstLaunchCurrent = VersionTracking.IsFirstLaunchForCurrentVersion;

// First time launching current build
var firstLaunchBuild = VersionTracking.IsFirstLaunchForCurrentBuild;

// Current app version (2.0.0)
var currentVersion = VersionTracking.CurrentVersion;

// Current build (2)
var currentBuild = VersionTracking.CurrentBuild;

// Previous app version (1.0.0)
var previousVersion = VersionTracking.PreviousVersion;

// Previous app build (1)
var previousBuild = VersionTracking.PreviousBuild;

// First version of app installed (1.0.0)
var firstVersion = VersionTracking.FirstInstalledVersion;

// First build of app installed (1)
var firstBuild = VersionTracking.FirstInstalledBuild;

// List of versions installed (1.0.0, 2.0.0)
var versionHistory = VersionTracking.VersionHistory;

// List of builds installed (1, 2)
var buildHistory = VersionTracking.BuildHistory;
```

## <a name="platform-implementation-specifics"></a>Informazioni specifiche sull'implementazione della piattaforma

Tutte le informazioni sulla versione vengono archiviati utilizzando il [preferenze](preferences.md) API in Xamarin.Essentials e viene archiviato con il nome della **.xamarinessentials [YOUR-APP-PACKAGE-ID]**.

L'applicazione se si disinstalla il _LocalSettings_e la versione di tutte le informazioni di riferimento deve essere rimosso.

## <a name="api"></a>API

- [Codice sorgente rilevamento versione](https://github.com/xamarin/Essentials/tree/master/Essentials/VersionTracking)
- [Documentazione dell'API di rilevamento versione](xref:Xamarin.Essentials.VersionTracking)
