---
title: Xamarin.FormsPosizione e distanza della mappa
description: Oggetto Xamarin.Forms . Lo spazio dei nomi Maps contiene uno struct di posizione usato in genere quando si posiziona una mappa e i relativi pin e uno struct di distanza che può essere utilizzato facoltativamente durante il posizionamento di una mappa.
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2b1613789029d59e46a6d0431bfa9da1a53082e8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138398"
---
# <a name="xamarinforms-map-position-and-distance"></a>Xamarin.FormsPosizione e distanza della mappa

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Lo [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) spazio dei nomi contiene uno [`Position`](xref:Xamarin.Forms.Maps.Position) struct usato in genere quando si posiziona una mappa e i relativi pin e uno [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct che può essere utilizzato facoltativamente durante il posizionamento di una mappa.

## <a name="position"></a>Posizione

Lo [`Position`](xref:Xamarin.Forms.Maps.Position) struct incapsula una posizione archiviata come valori di latitudine e longitudine. Questo struct definisce due proprietà di sola lettura:

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude), di tipo `double` , che rappresenta la latitudine della posizione in gradi decimali.
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude), di tipo `double` , che rappresenta la longitudine della posizione in gradi decimali.

[`Position`](xref:Xamarin.Forms.Maps.Position)gli oggetti vengono creati con il `Position` costruttore, che richiede gli argomenti di latitudine e Longitudine specificati come `double` valori:

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

Quando si crea un `Position` oggetto, il valore di latitudine verrà fissato tra-90,0 e 90,0 e il valore della longitudine verrà fissato tra-180,0 e 180,0.

> [!NOTE]
> La `GeographyUtils` classe dispone di un `ToRadians` metodo di estensione che converte un `double` valore da gradi in radianti e un `ToDegrees` metodo di estensione che converte un `double` valore da radianti a gradi.

## <a name="distance"></a>Distance

Lo [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct incapsula una distanza archiviata come `double` valore che rappresenta la distanza in metri. Questo struct definisce tre proprietà di sola lettura:

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers), di tipo `double` , che rappresenta la distanza in chilometri occupata da `Distance` .
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters), di tipo `double` , che rappresenta la distanza in metri occupata da `Distance` .
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles), di tipo `double` , che rappresenta la distanza in miglia occupata dall'oggetto `Distance` .

[`Distance`](xref:Xamarin.Forms.Maps.Distance)gli oggetti possono essere creati con il `Distance` costruttore, che richiede un argomento contatori specificato come `double` :

```csharp
Distance distance = new Distance(1450.5);
```

In alternativa, [`Distance`](xref:Xamarin.Forms.Maps.Distance) è possibile creare oggetti con i [`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*) [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*) [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*) metodi factory,, e `BetweenPositions` :

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
Distance distance4 = Distance.BetweenPositions(position1, position2);
```

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
