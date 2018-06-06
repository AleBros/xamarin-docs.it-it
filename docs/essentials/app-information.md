---
title: "Xamarin.Essentials: Le informazioni sull'App"
description: Questo documento descrive la classe AppInfo in Xamarin.Essentials, che fornisce informazioni sull'applicazione. Ad esempio, espone il nome dell'app e la versione.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 25765fbbcbd2475bfcbc72ca5c4feefa8ef19d08
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782105"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Le informazioni sull'App

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

- [Codice sorgente AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentazione dell'API di AppInfo](xref:Xamarin.Essentials.AppInfo)
