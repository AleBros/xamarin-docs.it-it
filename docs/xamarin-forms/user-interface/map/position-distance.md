---
title: Posizione e distanza del mapping di Xamarin.Forms
description: Lo spazio dei nomi Xamarin.Forms. Maps contiene uno struct di posizione usato in genere quando si posiziona una mappa e i relativi pin e uno struct di distanza che può essere utilizzato facoltativamente durante il posizionamento di una mappa.
ms.prod: xamarin
ms.assetid: 2F4EA3D2-1351-40AD-A71D-CF7F1F18F1E8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/30/2019
ms.openlocfilehash: a68eab7bb3e6da764f5a35af4461d6af2be50ebe
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426153"
---
# <a name="xamarinforms-map-position-and-distance"></a>Posizione e distanza del mapping di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Lo spazio dei nomi [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) contiene uno struct [`Position`](xref:Xamarin.Forms.Maps.Position) usato in genere quando si posiziona una mappa e i relativi pin e una [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct che può essere utilizzata facoltativamente durante il posizionamento di una mappa.

## <a name="position"></a>Posizione

Lo struct [`Position`](xref:Xamarin.Forms.Maps.Position) incapsula una posizione archiviata come valori di latitudine e longitudine. Questo struct definisce due proprietà di sola lettura:

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude), di tipo `double`, che rappresenta la latitudine della posizione in gradi decimali.
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude), di tipo `double`, che rappresenta la longitudine della posizione in gradi decimali.

[`Position`](xref:Xamarin.Forms.Maps.Position) oggetti vengono creati con il costruttore `Position`, che richiede gli argomenti di latitudine e Longitudine specificati come `double` valori:

```csharp
Position position = new Position(36.9628066, -122.0194722);
```

> [!NOTE]
> Quando si crea un oggetto `Position`, il valore di latitudine verrà fissato tra-90,0 e 90,0 e il valore della longitudine verrà fissato tra-180,0 e 180,0.

## <a name="distance"></a>Distanza

Lo struct [`Distance`](xref:Xamarin.Forms.Maps.Distance) incapsula una distanza archiviata come valore `double`, che rappresenta la distanza in metri. Questo struct definisce tre proprietà di sola lettura:

- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers), di tipo `double`, che rappresenta la distanza in chilometri occupata dal `Distance`.
- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters), di tipo `double`, che rappresenta la distanza in metri occupata dal `Distance`.
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles), di tipo `double`, che rappresenta la distanza in miglia occupata dal `Distance`.

[`Distance`](xref:Xamarin.Forms.Maps.Distance) oggetti possono essere creati con il costruttore `Distance`, che richiede un argomento contatori specificato come `double`:

```csharp
Distance distance = new Distance(1450.5);
```

In alternativa, è possibile creare oggetti [`Distance`](xref:Xamarin.Forms.Maps.Distance) con i metodi factory [`FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers*), [`FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters*)e [`FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles*) :

```csharp
Distance distance1 = Distance.FromKilometers(1.45); // argument represents the number of kilometers
Distance distance2 = Distance.FromMeters(1450.5);   // argument represents the number of meters
Distance distance3 = Distance.FromMiles(0.969);     // argument represents the number of miles
```

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
