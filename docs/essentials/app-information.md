---
title: "Xamarin.Essentials: Informazioni sull'App"
description: Questo documento descrive la classe AppInfo in Xamarin.Essentials, che fornisce informazioni sull'applicazione. Ad esempio, espone il nome dell'app e la versione.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 7e79b3003f41b8de22950624e44e8c9e0e7e7e31
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831507"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Informazioni sull'App

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

La classe **AppInfo** fornisce informazioni sull'applicazione.

## <a name="using-appinfo"></a>Uso di AppInfo

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>Recupero delle informazioni dell'applicazione:

Le informazioni seguenti viene esposta tramite l'API:

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

La classe **AppInfo** può anche aprire una pagina delle impostazioni gestita dal sistema operativo relativa l'applicazione:

```csharp
// Display settings page
AppInfo.OpenSettings();
```

Questa pagina delle impostazioni consente all'utente di modificare le autorizzazioni relative all'applicazione ed eseguire altre attività specifiche della piattaforma.

## <a name="api"></a>API

- [Codice sorgente AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentazione dell'API di AppInfo](xref:Xamarin.Essentials.AppInfo)
