---
title: Xamarin.EssentialsConvertitori di colori
description: La classe ColorConverters in Xamarin.Essentials fornisce diversi metodi di supporto e metodi di estensione per lavorare con System. Drawing. Color.
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
ms.custom: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 57b85a909855594c70bfd529d7a794b4bd7bca7e
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84130221"
---
# <a name="xamarinessentials-color-converters"></a>Xamarin.Essentials: Convertitori di colori

La classe **ColorConverters** in Xamarin.Essentials fornisce diversi metodi helper per System. Drawing. Color.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-color-converters"></a>Uso dei convertitori di colori

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Quando si utilizza `System.Drawing.Color` , è possibile utilizzare i convertitori predefiniti di Xamarin.Forms per creare un colore da HSL, esadecimale o uint.

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

- Getcomplementari
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

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Color-Converters-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
