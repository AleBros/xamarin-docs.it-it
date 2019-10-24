---
title: Poligoni e polilinee del mapping di Novell. Forms
description: Questo articolo illustra come creare poligoni e polilinee in un'istanza di mappa Novell. Forms.
ms.prod: xamarin
ms.assetid: CDAF0B02-1AA8-4AD6-94A7-ABFC18006A2D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/20/2019
ms.openlocfilehash: 43826fbcd0c0c0b062611a5fc8e1f0511d4e8dda
ms.sourcegitcommit: e4c23187874488ff55794d0e81a9bba30d2c2cd6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72776702"
---
# <a name="xamarinforms-map-polygons-and-polylines"></a>Poligoni e polilinee del mapping di Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

[![ "demo di poligono e polilinea in iOS e Android"](polygons-images/polygon-app-cropped.png)](polygons-images/polygon-app.png#lightbox)

gli elementi `Polygon` e `Polyline` consentono di evidenziare aree specifiche su una mappa. Un `Polygon` è una forma completamente racchiusa che può avere un tratto e un colore di riempimento. Una `Polyline` è una riga che non racchiude completamente un'area.

> [!NOTE]
> Esempi di `Polygon` e `Polyline` si trovano in **PolygonsPage** nel progetto di esempio.

Le classi `Polygon` e `Polyline` derivano da `MapElement`, che espone le proprietà [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) seguenti:

- `StrokeColor` è una proprietà `Color` che determina il colore della linea.
- `StrokeWidth` è una proprietà `float` che determina la lunghezza della linea.
- `Geopath` viene definito sia in `Polygon` che in `Polyline` ed è un elenco di oggetti [`Position`](xref:Xamarin.Forms.Maps.Position) che specificano i punti della forma.

La classe `Polygon` definisce una proprietà aggiuntiva:

- `FillColor` è una proprietà `Color` che determina il colore di sfondo del poligono.

> [!NOTE]
> Se la proprietà `StrokeColor` non è specificata, per impostazione predefinita il tratto sarà nero. Se la proprietà `FillColor` non è specificata, per impostazione predefinita il riempimento verrà impostato su Transparent. Pertanto, se nessuna delle due proprietà è specificata, la forma avrà un contorno nero senza riempimento.

## <a name="create-a-polygon"></a>Creare un poligono

È possibile aggiungere un oggetto `Polygon` a una mappa creandone un'istanza e aggiungendolo alla raccolta di `MapElements` della mappa:

```csharp
// instantiate a polygon
Polygon polygon = new Polygon
{
    StrokeWidth = 8,
    StrokeColor = Color.FromHex("#1BA1E2"),
    FillColor = Color.FromHex("#881BA1E2"),
    Geopath =
    {
        new Position(47.6368678, -122.137305),
        new Position(47.6368894, -122.134655),
        new Position(47.6359424, -122.134655),
        new Position(47.6359496, -122.1325521),
        new Position(47.6424124, -122.1325199),
        new Position(47.642463,  -122.1338932),
        new Position(47.6406414, -122.1344833),
        new Position(47.6384943, -122.1361248),
        new Position(47.6372943, -122.1376912)
    }
}

// add the polygon to the map's MapElements collection
map.MapElements.Add(polygon);
```

In XAML è inoltre possibile creare un `Polygon`:

```xaml
<maps:Map x:Name="map">
    <maps:Map.MapElements>
        <maps:Polygon StrokeColor="#FF9900"
                      StrokeWidth="8"
                      FillColor="#88FF9900">
            <maps:Polygon.Geopath>
                <maps:Position>
                    <x:Arguments>
                        <x:Double>47.6368678</x:Double>
                        <x:Double>-122.137305</x:Double>
                    </x:Arguments>
                </maps:Position>
                ...
            </maps:Polygon.Geopath>
        </maps:Polygon>
    </maps:Map.MapElements>
</maps:Map>
```

Le proprietà `StrokeColor` e `StrokeWidth` vengono specificate per personalizzare il contorno del poligono. Il valore della proprietà `FillColor` corrisponde al valore della proprietà `StrokeColor` ma è stato specificato un valore alfa per renderlo trasparente, consentendo alla mappa sottostante di essere visibile attraverso la forma. La proprietà `GeoPath` contiene un elenco di oggetti `Position` che definiscono le coordinate geografiche dei punti del poligono. Viene eseguito il rendering di un oggetto `Polygon` sulla mappa dopo che è stato aggiunto alla raccolta `MapElements` dell'`Map`.

> [!NOTE]
> Una `Polygon` è una forma completamente racchiusa. Il primo e l'ultimo punto verranno connessi automaticamente se non corrispondono.

## <a name="create-a-polyline"></a>Creare una polilinea

È possibile aggiungere un oggetto `Polyline` a una mappa creandone un'istanza e aggiungendolo alla raccolta di `MapElements` della mappa:

```csharp
// instantiate a polyline
Polyline polyline = new Polyline
{
    StrokeColor = Color.Blue,
    StrokeWidth = 12,
    Geopath =
    {
        new Position(47.6381401, -122.1317367),
        new Position(47.6381473, -122.1350841),
        new Position(47.6382847, -122.1353094),
        new Position(47.6384582, -122.1354703),
        new Position(47.6401136, -122.1360819),
        new Position(47.6403883, -122.1364681),
        new Position(47.6407426, -122.1377019),
        new Position(47.6412558, -122.1404056),
        new Position(47.6414148, -122.1418647),
        new Position(47.6414654, -122.1432702)
    }
};

// add the polyline to the map's MapElements collection
map.MapElements.Add(polyline);
```

In XAML è inoltre possibile creare un `Polyline`:

```xaml
<maps:Map x:Name="map">
    <maps:Map.MapElements>
        <maps:Polyline StrokeColor="Blue"
                       StrokeWidth="12">
            <maps:Polyline.Geopath>
                <maps:Position>
                    <x:Arguments>
                        <x:Double>47.6381401</x:Double>
                        <x:Double>-122.1317367</x:Double>
                    </x:Arguments>
                </maps:Position>
                ...
            </maps:Polyline.Geopath>
        </maps:Polyline>
    </maps:Map.MapElements>
</maps:Map>
```

Le proprietà `StrokeColor` e `StrokeWidth` vengono specificate per personalizzare la riga. La proprietà `GeoPath` contiene un elenco di oggetti `Position` che definiscono le coordinate geografiche dei punti della polilinea. Viene eseguito il rendering di un oggetto `Polyline` sulla mappa dopo che è stato aggiunto alla raccolta `MapElements` dell'`Map`.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
