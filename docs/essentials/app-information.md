---
title: 'Xamarin.Essentials: App Information'
description: Questo documento descrive la classe AppInfo in Xamarin.Essentials, che fornisce informazioni sull'applicazione. Ad esempio, espone il nome e la versione dell'app.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 62ecf890ba6b3276db89e93c2699e0406dbe4d45
ms.sourcegitcommit: a635312ffec816ba357a92b66c8c5221c8d9044c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50965617"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: App Information

![NuGet in versione non definitiva](~/media/shared/pre-release.png)

La classe **AppInfo** fornisce informazioni sull'applicazione.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-appinfo"></a>Uso di AppInfo

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-application-information"></a>Acquisizione delle informazioni sull'applicazione:

Tramite l'API vengono esposte le informazioni seguenti:

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

## <a name="displaying-application-settings"></a>Accesso alle impostazione applicazione

La classe **AppInfo** può anche visualizzare una pagina di impostazioni gestite dal sistema operativo per l'applicazione:

```csharp
// Display settings page
AppInfo.ShowSettingsUI();
```

Questa pagina di impostazioni consente all'utente di modificare le autorizzazioni dell'applicazione e di eseguire altre attività specifiche della piattaforma.

## <a name="api"></a>API

- [Codice sorgente di AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentazione dell'API AppInfo](xref:Xamarin.Essentials.AppInfo)
