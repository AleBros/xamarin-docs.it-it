---
title: Posizione e distanza del mapping di Novell. Forms
description: Lo spazio dei nomi Novell. Forms. Maps contiene uno struct di posizione usato in genere quando si posiziona una mappa e i relativi pin e uno struct di distanza che può essere utilizzato facoltativamente durante il posizionamento di una mappa.
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
ms.openlocfilehash: 567e1b5620378f0c1b64d17c56c8fb451f18abc3
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517439"
---
# <a name="xamarinforms-map-position-and-distance"></a>Posizione e distanza del mapping di Novell. Forms

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Lo [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) spazio dei nomi [`Position`](xref:Xamarin.Forms.Maps.Position) contiene uno struct usato in genere quando si posiziona una mappa e i relativi pin [`Distance`](xref:Xamarin.Forms.Maps.Distance) e uno struct che può essere utilizzato facoltativamente durante il posizionamento di una mappa.

## <a name="position"></a>Posizione

Lo [`Position`](xref:Xamarin.Forms.Maps.Position) struct incapsula una posizione archiviata come valori di latitudine e longitudine. Questo struct definisce due proprietà di sola lettura:

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude), di tipo `double`, che rappresenta la latitudine della posizione in gradi decimali.
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude), di tipo `double`, che rappresenta la longitudine della posizione in gradi decimali.

[`Position`](xref:Xamarin.Forms.Maps.Position)gli oggetti vengono creati con `Position` il costruttore, che richiede gli argomenti di latitudine e `double` Longitudine specificati come valori:

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

Quando si crea `Position` un oggetto, il valore di latitudine verrà fissato tra-90,0 e 90,0 e il valore della longitudine verrà fissato tra-180,0 e 180,0.

> [!NOTE]
> La `GeographyUtils` classe dispone di `ToRadians` un metodo di estensione che `double` converte un valore da gradi in radianti e `ToDegrees` un metodo di estensione che `double` converte un valore da radianti a gradi.

## <a name="distance"></a>Distance

Lo [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct incapsula una distanza archiviata come `double` valore che rappresenta la distanza in metri. Questo struct definisce tre proprietà di sola lettura:

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers), di tipo `double`, che rappresenta la distanza in chilometri occupata da `Distance`.
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters), di tipo `double`, che rappresenta la distanza in metri occupata da `Distance`.
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles), di tipo `double`, che rappresenta la distanza in miglia occupata dall'oggetto `Distance`.

[`Distance`](xref:Xamarin.Forms.Maps.Distance)gli oggetti possono essere creati con `Distance` il costruttore, che richiede un argomento contatori specificato come `double`:

```csharp
Distance distance = new Distance(1450.5);
```

In alternativa, [`Distance`](xref:Xamarin.Forms.Maps.Distance) è possibile creare oggetti con i [`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*)metodi [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*)factory [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*),, `BetweenPositions` e:

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
Distance distance4 = Distance.BetweenPositions(position1, position2);
```

## <a name="related-links"></a>Link correlati

- [Esempio di Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
