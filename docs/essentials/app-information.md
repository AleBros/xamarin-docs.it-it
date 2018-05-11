---
title: Informazioni sull'App Xamarin.Essentials
description: La classe AppInfo fornisce informazioni sull'applicazione.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 32e3eb8fab719540e4c9ffec4e57f5510c10e3f5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="xamarinessentials-app-information"></a>Informazioni sull'App Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **AppInfo** classe fornisce informazioni sull'applicazione.

## <a name="using-appinfo"></a>Utilizzo di AppInfo

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

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

## <a name="api"></a>API

- [Codice sorgente AppInfo](https://github.com/xamarin/Essentials/tree/master/Essentials/AppInfo)
- [Documentazione dell'API di AppInfo](xref:Xamarin.Essentials.AppInfo)
