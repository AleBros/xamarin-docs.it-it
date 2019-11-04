---
title: Mappa geocodifica di Novell. Forms
description: Questo articolo illustra come geocodificare e invertire la mappa geografica dei dati, usando la classe geocodificatore Novell. Forms. maps.
ms.prod: xamarin
ms.assetid: DE7DB31A-8921-4614-8B49-DAEF1E7B03B3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/22/2019
ms.openlocfilehash: 9a20618fea0091979c2ea862f417dccec565b218
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2019
ms.locfileid: "73425610"
---
# <a name="xamarinforms-map-geocoding"></a>Mappa geocodifica di Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Lo spazio dei nomi [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) fornisce una classe [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) , che esegue la conversione tra gli indirizzi stringa e le coordinate di latitudine e longitudine archiviate in [`Position`](xref:Xamarin.Forms.Maps.Position) oggetti. Per ulteriori informazioni sul [`Position`](xref:Xamarin.Forms.Maps.Position) struct, vedere [posizione e distanza della mappa](position-distance.md).

## <a name="geocode-an-address"></a>Geocodificare un indirizzo

Un indirizzo può essere geocodificato in coordinate di latitudine e Longitudine creando un'istanza di [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) e chiamando il metodo [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) sull'istanza di `Geocoder`:

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder;

IEnumerable<Position> approximateLocations = await geoCoder.GetPositionsForAddressAsync("Pacific Ave, San Francisco, California");
Position position = approximateLocations.FirstOrDefault();
string coordinates = $"{position.Latitude}, {position.Longitude}";
```

Il metodo [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) accetta un argomento `string` che rappresenta l'indirizzo e restituisce in modo asincrono una raccolta di oggetti [`Position`](xref:Xamarin.Forms.Maps.Position) che possono rappresentare l'indirizzo.

## <a name="reverse-geocode-an-address"></a>Geocodificare un indirizzo in senso inverso

Le coordinate di latitudine e longitudine possono essere geocodificate in un indirizzo via creando un'istanza di [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) e chiamando il metodo [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) sull'istanza `Geocoder`:

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder;

Position position = new Position(37.8044866, -122.4324132);
IEnumerable<string> possibleAddresses = await geoCoder.GetAddressesForPositionAsync(position);
string address = possibleAddresses.FirstOrDefault();
```

Il metodo [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) accetta un argomento [`Position`](xref:Xamarin.Forms.Maps.Position) costituito da coordinate di latitudine e longitudine e restituisce in modo asincrono una raccolta di stringhe che rappresentano gli indirizzi vicini alla posizione.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Posizione e distanza del mapping di Novell. Forms](position-distance.md)
- [API geocodificatore](xref:Xamarin.Forms.Maps.Geocoder)
