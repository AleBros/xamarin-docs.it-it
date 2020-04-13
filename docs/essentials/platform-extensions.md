---
title: Estensioni di piattaforma Xamarin.Essentials
description: Xamarin.Essentials mette a disposizione diversi metodi di estensione piattaforma a cui ricorrere per lavorare, ad esempio, con tipi di piattaforma Rect, Size e Point.
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 4e43159fb9cae6646be54d8efc24c334bc071477
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "77545156"
---
# <a name="xamarinessentials-platform-extensions"></a>Xamarin.Essentials: Estensioni della piattaforma

Xamarin.Essentials mette a disposizione diversi metodi di estensione piattaforma a cui ricorrere per lavorare, ad esempio, con tipi di piattaforma Rect, Size e Point. Ciò significa che è possibile convertire la versione `System` di questi tipi nella versione iOS, Android e UWP dei tipi specifici. 

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-platform-extensions"></a>Uso delle estensioni di piattaforma

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Tutte le estensioni di piattaforma possono essere chiamate solo dal progetto iOS, Android o UWP.

## <a name="android-extensions"></a>Estensioni Android

Queste estensioni sono accessibili solo da un progetto Android.These extensions can only be accessed from an Android project.

### <a name="application-context--activity"></a>Contesto applicazione & attivitàApplication Context & Activity

Usando le estensioni `Platform` della piattaforma nella classe `Context` `Activity` puoi ottenere l'accesso all'app corrente o per l'app in esecuzione.

```csharp

var context = Platform.AppContext;

// Current Activity or null if not initialized or not started.
var activity = Platform.CurrentActivity;
```

Se c'è una `Activity` situazione in cui è necessario il, `WaitForActivityAsync` ma l'applicazione non è stata completamente avviata, il metodo deve essere utilizzato.

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

Gli stati di attività sono i seguenti:

* Data di creazione
* Resumed
* Paused
* Eliminata
* SaveInstanceState (Stato di salvataggio)
* Started
* Arrestato

Per ulteriori informazioni, leggere la documentazione relativa al [ciclo di vita](https://docs.microsoft.com/xamarin/android/app-fundamentals/activity-lifecycle/) delle attività.

## <a name="ios-extensions"></a>Estensioni iOS

Queste estensioni sono accessibili solo da un progetto iOS.These extensions can only be accessed from an iOS project.

### <a name="current-uiviewcontroller"></a>UIViewController corrente

Ottenere l'accesso `UIViewController`al visibile:

```csharp
var vc = Platform.GetCurrentUIViewController();
```

Questo metodo `null` restituirà se `UIViewController`non è possibile rilevare un oggetto .

## <a name="cross-platform-extensions"></a>Estensioni multipiattaforma

Queste estensioni esistono in ogni piattaforma.

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

- [Codice sorgente dei convertitori](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/PlatformExtensions)
- [Documentazione delle API dei convertitori Point](xref:Xamarin.Essentials.PointExtensions)
- [Documentazione delle API dei convertitori Rectangle](xref:Xamarin.Essentials.RectangleExtensions)
- [Documentazione delle API dei convertitori Size](xref:Xamarin.Essentials.SizeExtensions)
