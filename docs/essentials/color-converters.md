---
title: Convertitori di colori di Xamarin.Essentials
description: La classe ColorConverters di Xamarin.Essentials offre diversi metodi helper e metodi di estensione per usare System.Drawing.Color.
ms.assetid: B10428D6-89E2-4714-A39F-7E6E626391B2
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 5d64967dfaa6ce7ef746a97f739cac67f5102fc2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "77545158"
---
# <a name="xamarinessentials-color-converters"></a>Xamarin.Essentials: Convertitori di colori

La classe **ColorConverters** di Xamarin.Essentials offre diversi metodi helper per System.Drawing.Color.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-color-converters"></a>Uso dei convertitori di colori

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Quando si lavora con `System.Drawing.Color` è possibile usare i convertitori incorporati di Xamarin.Forms per creare un colore da Hsl, Hex o UInt.

```csharp
var blueHex = ColorConverters.FromHex("#3498db");
var blueHsl = ColorConverters.FromHsl(204, 70, 53);
var blueUInt = ColorConverters.FromUInt(3447003);
```

## <a name="using-color-extensions"></a>Uso delle estensioni di colore

I metodi di estensione su `System.Drawing.Color` consentono di applicare proprietà diverse:

```csharp
var blue = ColorConverters.FromHex("#3498db");

// Multiplies the current alpha by 50%
var blueWithAlpha = blue.MultiplyAlpha(.5f);
```

Esistono diversi altri metodi di estensione tra cui:

- GetComplementary
- MultiplyAlpha
- ToUInt
- WithAlpha
- WithHue
- WithLuminosity
- WithSaturation

## <a name="using-platform-extensions"></a>Uso delle estensioni di piattaforma

È inoltre possibile convertire System.Drawing.Color alla struttura di colore specifica della piattaforma. Questi metodi possono essere chiamati solo dai progetti iOS, Android e UWP.

```csharp
var system = System.Drawing.Color.FromArgb(255, 52, 152, 219);

// Extension to convert to Android.Graphics.Color, UIKit.UIColor, or Windows.UI.Color
var platform = system.ToPlatformColor();
```

```csharp
var platform = new Android.Graphics.Color(52, 152, 219, 255);

// Back to System.Drawing.Color
var system = platform.ToSystemColor();
```

Il metodo `ToSystemColor` si applica a Android.Graphics.Color, UIKit.UIColor e Windows.UI.Color.

## <a name="api"></a>API

- [Codice sorgente dei convertitori di colore](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/ColorConverters.shared.cs)
- [Documentazione delle API dei convertitori di colore](xref:Xamarin.Essentials.ColorConverters)
- [Codice sorgente delle estensioni di colore](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/ColorConverters.shared.cs)
- [Documentazione delle API delle estensioni di colore](xref:Xamarin.Essentials.ColorExtensions)
