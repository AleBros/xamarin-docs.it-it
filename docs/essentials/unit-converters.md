---
title: Convertitori di unità di Xamarin.Essentials
description: La classe UnitConverters di Xamarin.Essentials offre diversi convertitori di unità per aiutare gli sviluppatori che usano Xamarin.Essentials.
ms.assetid: 35DE2704-E730-4337-9476-66CD53376943
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: c07e0c7d9645c22f0d70c75fd7d8dffdec8cde04
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77545028"
---
# <a name="xamarinessentials-unit-converters"></a>Xamarin.Essentials: Convertitori di unità

La classe **UnitConverters** offre diversi convertitori di unità per aiutare gli sviluppatori che usano Xamarin.Essentials.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-unit-converters"></a>Uso dei convertitori di unità

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Tutti i convertitori di unità sono disponibili usando la classe `UnitConverters` statica in Xamarin.Essentials. È possibile, ad esempio, convertire facilmente i gradi Fahrenheit in Celsius.

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

- [Codice sorgente dei convertitori di unità](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/UnitConverters.shared.cs)
- [Documentazione delle API dei convertitori di unità](xref:Xamarin.Essentials.UnitConverters)
