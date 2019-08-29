---
title: Convertitori di unità di Xamarin.Essentials
description: La classe UnitConverters di Xamarin.Essentials offre diversi convertitori di unità per aiutare gli sviluppatori che usano Xamarin.Essentials.
ms.assetid: 35DE2704-E730-4337-9476-66CD53376943
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: be560a156647274932265597ae5b83f22255d061
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120141"
---
# <a name="xamarinessentials-unit-converters"></a>Xamarin.Essentials: Convertitori di unità

La classe **UnitConverters** offre diversi convertitori di unità per aiutare gli sviluppatori che usano Xamarin.Essentials.

## <a name="get-started"></a>Attività iniziali

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-unit-converters"></a>Uso dei convertitori di unità

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Tutti i convertitori di unità sono disponibili usando la classe `UnitConverters` statica in Xamarin.Essentials. È possibile, ad esempio, convertire facilmente i gradi Fahrenheit in Celsius.

```csharp
var celcius = UnitConverters.FahrenheitToCelsius(32.0);
```

Ecco un elenco delle conversioni disponibili:

- FahrenheitToCelsius
- CelsiusToFahrenheit
- CelsiusToKelvin
- KelvinToCelsius
- MilesToMeters
- MilesToKilometers
- KilometersToMiles
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

## <a name="api"></a>API

- [Codice sorgente dei convertitori di unità](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/UnitConverters.shared.cs)
- [Documentazione delle API dei convertitori di unità](xref:Xamarin.Essentials.UnitConverters)
