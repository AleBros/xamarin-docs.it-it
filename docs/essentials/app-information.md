---
title: "Xamarin.Essentials: Informazioni sull'app"
description: In questo documento viene descritta la classe AppInfo in Xamarin.Essentials , che fornisce informazioni sull'applicazione. Ad esempio, espone il nome e la versione dell'app.
ms.assetid: 15924FCB-19E0-45B2-944E-E94FD7AE12FA
author: jamesmontemagno
ms.author: jamont
ms.date: 01/29/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1a8673e7c405660355e3a849e6ef1709fd2980a4
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802513"
---
# <a name="xamarinessentials-app-information"></a>Xamarin.Essentials: Informazioni sull'app

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

# <a name="android"></a>[Android](#tab/android)

Le informazioni sull'app vengono ottenute da `AndroidManifest.xml` per i campi seguenti:

- **Compilazione** - `android:versionCode` nel `manifest` nodo
- **Name**  -  Nome `android:label` nel `application` nodo
- **PackageName**: `package` nel `manifest` nodo
- **VersionString** : `android:versionName` nel `application` nodo

# <a name="ios"></a>[iOS](#tab/ios)

Le informazioni sull'app vengono ottenute da `Info.plist` per i campi seguenti:

- **Compilazione** :`CFBundleVersion`
- **Name**  -  Nome `CFBundleDisplayName` Se impostato, else`CFBundleName`
- **PackageName**:`CFBundleIdentifier`
- **VersionString** -`CFBundleShortVersionString`

# <a name="uwp"></a>[UWP](#tab/uwp)

Le informazioni sull'app vengono ottenute da `Package.appxmanifest` per i campi seguenti:

- **Build**: usa `Build` di `Version` nel nodo `Identity`
- **Name**  -  Nome `DisplayName` nel `Properties` nodo
- **PackageName**: `Name` nel `Identity` nodo
- **VersionString** : `Version` nel `Identity` nodo

--------------

## <a name="api"></a>API

- [Codice sorgente di AppInfo](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/AppInfo)
- [Documentazione dell'API AppInfo](xref:Xamarin.Essentials.AppInfo)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/App-Information-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
