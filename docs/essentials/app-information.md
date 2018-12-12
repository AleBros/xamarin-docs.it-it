---
title: 'Xamarin.Essentials: App Information'
description: Questo documento descrive la classe AppInfo in Xamarin.Essentials, che fornisce informazioni sull'applicazione. Ad esempio, espone il nome e la versione dell'app.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 3e67b605e485b724ec11f2ac94dcf3d1aa77d5cf
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057298"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: App Information

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

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Le informazioni sull'app vengono ottenute da `AndroidManifest.xml` per i campi seguenti:

- **Build**: `android:versionCode` nel nodo `manifest`
- **Name** - `android:label` nel nodo `application`
- **PackageName**: `package` nel nodo `manifest`
- **VersionString**: `android:versionName` nel nodo `application`

# <a name="iostabios"></a>[iOS](#tab/ios)

Le informazioni sull'app vengono ottenute da `Info.plist` per i campi seguenti:

- **Build**: `CFBundleVersion`
- **Name** - `CFBundleDisplayName` se impostato. In caso contrario, `CFBundleName`.
- **PackageName**: `CFBundleIdentifier`
- **VersionString**: `CFBundleShortVersionString`

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Le informazioni sull'app vengono ottenute da `Package.appxmanifest` per i campi seguenti:

- **Build**: usa `Build` di `Version` nel nodo `Identity`
- **Name** - `DisplayName` nel nodo `Properties`
- **PackageName**: `Name` nel nodo `Identity`
- **VersionString**: `Version` nel nodo `Identity`


--------------

## <a name="api"></a>API

- [Codice sorgente di AppInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [Documentazione dell'API AppInfo](xref:Xamarin.Essentials.AppInfo)
