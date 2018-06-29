---
title: "Xamarin.Essentials: Le informazioni sull'App"
description: Questo documento descrive la classe AppInfo in Xamarin.Essentials, che fornisce informazioni sull'applicazione. Ad esempio, espone il nome dell'app e la versione.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 7e79b3003f41b8de22950624e44e8c9e0e7e7e31
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080274"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Le informazioni sull'App

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **AppInfo** classe fornisce informazioni sull'applicazione.

## <a name="using-appinfo"></a>Utilizzo di AppInfo

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>Recupero di informazioni sull'applicazione:

Le seguenti informazioni vengono esposte tramite l'API:

```csharp
// Application Name
var appName = AppInfo.Name;

// Package Name/Application Identifier (com.microsoft.testapp)
var packageName = AppInfo.PackageName;

// Application Version (1.0.0)
var version = AppInfo.VersionString;

// Application Build Number (1)
var build = AppInfo.BuildString;
```

## <a name="displaying-application-settings"></a>Visualizzazione delle impostazioni dell'applicazione

Il **AppInfo** classe può anche visualizzare una pagina di impostazioni gestite dal sistema operativo per l'applicazione:

```csharp
// Display settings page
AppInfo.OpenSettings();
```

Questa pagina delle impostazioni consente all'utente di modificare le autorizzazioni dell'applicazione ed eseguire altre attività specifiche della piattaforma.

## <a name="api"></a>API

- [Codice sorgente AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentazione dell'API di AppInfo](xref:Xamarin.Essentials.AppInfo)
