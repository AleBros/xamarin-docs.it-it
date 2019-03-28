---
title: Estensioni di piattaforma Xamarin.Essentials
description: Xamarin.Essentials mette a disposizione diversi metodi di estensione piattaforma a cui ricorrere per lavorare, ad esempio, con tipi di piattaforma Rect, Size e Point.
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 740f6f4658c3e840f73261f554eb330ee02edb5d
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329065"
---
# <a name="xamarinessentials-platform-extensions"></a>Xamarin.Essentials: Estensioni di piattaforma

Xamarin.Essentials mette a disposizione diversi metodi di estensione piattaforma a cui ricorrere per lavorare, ad esempio, con tipi di piattaforma Rect, Size e Point. Ciò significa che è possibile convertire la versione `System` di questi tipi nella versione iOS, Android e UWP dei tipi specifici. 

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-platform-extensions"></a>Uso delle estensioni di piattaforma

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Tutte le estensioni di piattaforma possono essere chiamate solo dal progetto iOS, Android o UWP.

### <a name="point"></a>Punto

```csharp
var system = new System.Drawing.Point(x, y);

// Convert to CoreGraphics.CGPoint, Android.Graphics.Point, and Windows.Foundation.Point
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

### <a name="size"></a>Dimensione

```csharp
var system = new System.Drawing.Size(width, height);

// Convert to CoreGraphics.CGSize, Android.Util.Size, and Windows.Foundation.Size
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

### <a name="rectangle"></a>Rettangolo

```csharp
var system = new System.Drawing.Rectangle(x, y, width, height);

// Convert to CoreGraphics.CGRect, Android.Graphics.Rect, and Windows.Foundation.Rect
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

## <a name="api"></a>API

- [Codice sorgente dei convertitori](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/PlatformExtensions)
- [Documentazione delle API dei convertitori Point](xref:Xamarin.Essentials.PointExtensions)
- [Documentazione delle API dei convertitori Rectangle](xref:Xamarin.Essentials.RectangleExtensions)
- [Documentazione delle API dei convertitori Size](xref:Xamarin.Essentials.SizeExtensions)
