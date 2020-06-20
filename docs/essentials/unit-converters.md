---
title: Xamarin.EssentialsConvertitori di unità
description: La classe UnitConverters in Xamarin.Essentials fornisce diversi convertitori di unità per consentire agli sviluppatori di usare Xamarin.Essentials .
ms.assetid: 35DE2704-E730-4337-9476-66CD53376943
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 01/06/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 332762a83796fef24278c6685a29f7d31a10dadc
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84801855"
---
# <a name="xamarinessentials-unit-converters"></a>Xamarin.Essentials: Convertitori di unità

La classe **UnitConverters** fornisce diversi convertitori di unità per consentire agli sviluppatori di usare Xamarin.Essentials .

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-unit-converters"></a>Uso dei convertitori di unità

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Tutti i convertitori di unità sono disponibili tramite la `UnitConverters` classe statica in Xamarin.Essentials . È possibile, ad esempio, convertire facilmente i gradi Fahrenheit in Celsius.

```csharp
var celsius = UnitConverters.FahrenheitToCelsius(32.0);
```

Ecco un elenco delle conversioni disponibili:

- FahrenheitToCelsius
- CelsiusToFahrenheit
- CelsiusToKelvin
- KelvinToCelsius
- MilesToMeters
- MilesToKilometers
- KilometersToMiles
- MetersToInternationalFeet
- InternationalFeetToMeters
- DegreesToRadians
- RadiansToDegrees
- DegreesPerSecondToRadiansPerSecond
- RadiansPerSecondToDegreesPerSecond
- DegreesPerSecondToHertz
- RadiansPerSecondToHertz
- HertzToDegreesPerSecond
- HertzToRadiansPerSecond
- KilopascalsToHectopascals
- HectopascalsToKilopascals
- KilopascalsToPascals
- HectopascalsToPascals
- AtmospheresToPascals
- PascalsToAtmospheres
- CoordinatesToMiles
- CoordinatesToKilometers
- KilogramsToPounds
- PoundsToKilograms
- StonesToPounds
- PoundsToStones

## <a name="api"></a>API

- [Codice sorgente dei convertitori di unità](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Types/UnitConverters.shared.cs)
- [Documentazione delle API dei convertitori di unità](xref:Xamarin.Essentials.UnitConverters)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Unit-Conversion-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
