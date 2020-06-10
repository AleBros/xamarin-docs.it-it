---
title: " Xamarin.Forms mappa geocodifica" Descrizione: "in questo articolo viene illustrato come geocodificare e invertire la mappa geografica dei dati tramite Xamarin.Forms . Esegue il mapping della classe geocodificar. "
ms. prod: Novell MS. AssetID: DE7DB31A-8921-4614-8B49-DAEF1E7B03B3 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 10/22/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-map-geocoding"></a>Xamarin.FormsMappa geocodifica

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Lo [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) spazio dei nomi fornisce una [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) classe che esegue la conversione tra gli indirizzi stringa e le coordinate di latitudine e Longitudine archiviate negli [`Position`](xref:Xamarin.Forms.Maps.Position) oggetti. Per altre informazioni sullo [`Position`](xref:Xamarin.Forms.Maps.Position) struct, vedere [posizione e distanza della mappa](position-distance.md).

## <a name="geocode-an-address"></a>Geocodificare un indirizzo

Un indirizzo può essere geocodificato in coordinate di latitudine e Longitudine creando un' [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) istanza e chiamando il [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) metodo sull' `Geocoder` istanza:

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

IEnumerable<Position> approximateLocations = await geoCoder.GetPositionsForAddressAsync("Pacific Ave, San Francisco, California");
Position position = approximateLocations.FirstOrDefault();
string coordinates = $"{position.Latitude}, {position.Longitude}";
```

Il [`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync*) metodo accetta un `string` argomento che rappresenta l'indirizzo e restituisce in modo asincrono una raccolta di [`Position`](xref:Xamarin.Forms.Maps.Position) oggetti che potrebbero rappresentare l'indirizzo.

## <a name="reverse-geocode-an-address"></a>Geocodificare un indirizzo in senso inverso

Le coordinate di latitudine e longitudine possono essere geocodificate in un indirizzo via creando un' [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) istanza e chiamando il [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) metodo sull' `Geocoder` istanza:

```csharp
using Xamarin.Forms.Maps;
// ...
Geocoder geoCoder = new Geocoder();

Position position = new Position(37.8044866, -122.4324132);
IEnumerable<string> possibleAddresses = await geoCoder.GetAddressesForPositionAsync(position);
string address = possibleAddresses.FirstOrDefault();
```

Il [`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync*) metodo accetta un [`Position`](xref:Xamarin.Forms.Maps.Position) argomento costituito da coordinate di latitudine e longitudine e restituisce in modo asincrono una raccolta di stringhe che rappresentano gli indirizzi vicini alla posizione.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Xamarin.FormsPosizione e distanza della mappa](position-distance.md)
- [API geocodificatore](xref:Xamarin.Forms.Maps.Geocoder)
