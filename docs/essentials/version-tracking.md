---
title: 'Xamarin.Essentials: Rilevamento della versione'
description: La classe VersionTracking in Xamarin.Essentials consente di controllare la versione delle applicazioni e i numeri di build, oltre a visualizzare informazioni aggiuntive, ad esempio se è la prima volta che l'applicazione viene avviata o per la versione corrente, ottenere le informazioni di compilazione precedenti e altro ancora.
ms.assetid: 670C7E8A-E882-4AC0-97D2-A53D90ADD6A3
author: jamesmontemagno
ms.author: jamont
ms.date: 05/28/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 20819d76c23ca43f60073bcc2cd762abda280374
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802040"
---
# <a name="xamarinessentials-version-tracking"></a>Xamarin.Essentials: Rilevamento della versione

La classe **VersionTracking** consente di controllare la versione delle applicazioni e i numeri di build e di visualizzare informazioni aggiuntive, ad esempio se l'applicazione è stata avviata per la prima volta in assoluto o, per la versione corrente, di ottenere informazioni sulla build precedente e altro ancora.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-version-tracking"></a>Uso di Version Tracking

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La prima volta che viene usata, la classe **VersionTracking** inizierà a tenere traccia della versione corrente. È necessario chiamare `Track` in anticipo solo nell'applicazione ogni volta che viene caricata per assicurarsi che venga tenuta traccia delle informazioni sulla versione corrente:

```csharp
VersionTracking.Track();
```

Dopo la chiamata iniziale a `Track`, è possibile leggere le informazioni sulla versione:

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

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

Tutte le informazioni sulla versione vengono archiviate usando l'API delle [Preferenze](preferences.md) in Xamarin.Essentials e vengono archiviate con un nome file di **[your-app-Package-ID]. xamarinessentials. versiontracking** e seguono la stessa persistenza dei dati descritta nella documentazione relativa alle [Preferenze](preferences.md#persistence) .

## <a name="api"></a>API

- [Codice sorgente di Version Tracking](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/VersionTracking)
- [Documentazione dell'API Version Tracking](xref:Xamarin.Essentials.VersionTracking)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Version-Tracking-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
