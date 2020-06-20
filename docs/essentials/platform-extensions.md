---
title: Xamarin.EssentialsEstensioni della piattaforma
description: Xamarin.Essentialsin sono disponibili diversi metodi di estensione della piattaforma quando è necessario utilizzare i tipi di piattaforma, ad esempio Rect, Size e Point.
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 56cb9619a4132f6568cee8fbf590965934024639
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84801923"
---
# <a name="xamarinessentials-platform-extensions"></a>Xamarin.Essentials: Estensioni della piattaforma

Xamarin.Essentialsin sono disponibili diversi metodi di estensione della piattaforma quando è necessario utilizzare i tipi di piattaforma, ad esempio Rect, Size e Point. Ciò significa che è possibile convertire la versione `System` di questi tipi nella versione iOS, Android e UWP dei tipi specifici.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-platform-extensions"></a>Uso delle estensioni di piattaforma

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Tutte le estensioni di piattaforma possono essere chiamate solo dal progetto iOS, Android o UWP.

## <a name="android-extensions"></a>Estensioni Android

È possibile accedere a queste estensioni solo da un progetto Android.

### <a name="application-context--activity"></a>Attività & del contesto dell'applicazione

Usando le estensioni della piattaforma nella `Platform` classe è possibile ottenere l'accesso al corrente `Context` o `Activity` per l'app in esecuzione.

```csharp

var context = Platform.AppContext;

// Current Activity or null if not initialized or not started.
var activity = Platform.CurrentActivity;
```

Se si verifica una situazione in cui `Activity` è necessario, ma l'applicazione non è stata avviata completamente, è `WaitForActivityAsync` necessario usare il metodo.

```csharp
var activity = await Platform.WaitForActivityAsync();
```

### <a name="activity-lifecycle"></a>Ciclo di vita dell'attività

Oltre a ottenere l'attività corrente, è anche possibile registrarsi per gli eventi del ciclo di vita.

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);

    Xamarin.Essentials.Platform.Init(this, bundle);

    Xamarin.Essentials.Platform.ActivityStateChanged += Platform_ActivityStateChanged;
}

protected override void OnDestroy()
{
    base.OnDestroy();
    Xamarin.Essentials.Platform.ActivityStateChanged -= Platform_ActivityStateChanged;
}

void Platform_ActivityStateChanged(object sender, Xamarin.Essentials.ActivityStateChangedEventArgs e) =>
    Toast.MakeText(this, e.State.ToString(), ToastLength.Short).Show();
```

Gli Stati dell'attività sono i seguenti:

* Data di creazione
* Resumed
* Paused
* Eliminata
* SaveInstanceState
* Avviato
* Arrestato

Per altre informazioni, vedere la documentazione del ciclo di vita delle [attività](https://docs.microsoft.com/xamarin/android/app-fundamentals/activity-lifecycle/) .

## <a name="ios-extensions"></a>Estensioni iOS

È possibile accedere a queste estensioni solo da un progetto iOS.

### <a name="current-uiviewcontroller"></a>UIViewController corrente

Ottenere l'accesso al attualmente visibile `UIViewController` :

```csharp
var vc = Platform.GetCurrentUIViewController();
```

Questo metodo restituisce `null` se non è in grado di rilevare un oggetto `UIViewController` .

## <a name="cross-platform-extensions"></a>Estensioni multipiattaforma

Queste estensioni sono disponibili in ogni piattaforma.

### <a name="point"></a>Point

```csharp
var system = new System.Drawing.Point(x, y);

// Convert to CoreGraphics.CGPoint, Android.Graphics.Point, and Windows.Foundation.Point
var platform = system.ToPlatformPoint();

// Back to System.Drawing.Point
var system2 = platform.ToSystemPoint();
```

### <a name="size"></a>Dimensione

```csharp
var system = new System.Drawing.Size(width, height);

// Convert to CoreGraphics.CGSize, Android.Util.Size, and Windows.Foundation.Size
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

### <a name="rectangle"></a>Rectangle

```csharp
var system = new System.Drawing.Rectangle(x, y, width, height);

// Convert to CoreGraphics.CGRect, Android.Graphics.Rect, and Windows.Foundation.Rect
var platform = system.ToPlatformRectangle();

// Back to System.Drawing.Rectangle
var system2 = platform.ToSystemRectangle();
```

## <a name="api"></a>API

- [Codice sorgente dei convertitori](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Types/PlatformExtensions)
- [Documentazione delle API dei convertitori Point](xref:Xamarin.Essentials.PointExtensions)
- [Documentazione delle API dei convertitori Rectangle](xref:Xamarin.Essentials.RectangleExtensions)
- [Documentazione delle API dei convertitori Size](xref:Xamarin.Essentials.SizeExtensions)
