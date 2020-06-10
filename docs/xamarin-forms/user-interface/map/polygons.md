---
title: " Xamarin.Forms mappa poligoni, polilinee e cerchi" Descrizione: "in questo articolo viene illustrato come creare poligoni, polilinee e cerchi su un' Xamarin.Forms istanza della mappa".
ms. prod: Novell MS. AssetID: CDAF0B02-1AA8-4AD6-94A7-ABFC18006A2D ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 03/10/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-map-polygons-and-polylines"></a>Xamarin.FormsPoligoni e polilinee della mappa

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

`Polygon``Polyline` `Circle` gli elementi, e consentono di evidenziare aree specifiche su una mappa. Un oggetto `Polygon` è una forma completamente racchiusa che può presentare un tratto e un colore di riempimento. Un oggetto `Polyline` è una riga che non racchiude completamente un'area. Un oggetto `Circle` evidenzia un'area circolare della mappa:

[!["Screenshot del poligono e della polilinea della mappa in iOS e Android"](polygons-images/polygon-polyline.png "Poligono e polilinea su una mappa")](polygons-images/polygon-polyline-large.png#lightbox "Poligono e polilinea su una mappa") 
 [ !["Screenshot di un cerchio mappa, in iOS e Android"](polygons-images/circle.png "Cerchio su una mappa")](polygons-images/circle-large.png#lightbox "Cerchio su una mappa")

Le `Polygon` `Polyline` classi, e `Circle` derivano dalla `MapElement` classe, che espone le proprietà associabili seguenti:

- `StrokeColor``Color`oggetto che determina il colore della linea.
- `StrokeWidth``float`oggetto che determina lo spessore della linea.

La `Polygon` classe definisce una proprietà associabile aggiuntiva:

- `FillColor`è un `Color` oggetto che determina il colore di sfondo del poligono.

Inoltre, le `Polygon` classi e `Polyline` definiscono entrambe una `GeoPath` proprietà, ovvero un elenco di [`Position`](xref:Xamarin.Forms.Maps.Position) oggetti che specificano i punti della forma.

La `Circle` classe definisce le proprietà associabili seguenti:

- `Center`è un [`Position`](xref:Xamarin.Forms.Maps.Position) oggetto che definisce il centro del cerchio, in latitudine e longitudine.
- `Radius`è un [`Distance`](xref:Xamarin.Forms.Maps.Distance) oggetto che definisce il raggio del cerchio in metri, chilometri o miglia.
- `FillColor`è una `Color` proprietà che determina il colore all'interno del perimetro del cerchio.

> [!NOTE]
> Se la `StrokeColor` proprietà non è specificata, l'impostazione predefinita del tratto sarà nero. Se la `FillColor` proprietà non è specificata, per impostazione predefinita il riempimento verrà impostato su Transparent. Pertanto, se nessuna delle due proprietà è specificata, la forma avrà un contorno nero senza riempimento.

## <a name="create-a-polygon"></a>Creare un poligono

È `Polygon` possibile aggiungere un oggetto a una mappa creandone un'istanza e aggiungendolo alla raccolta della mappa `MapElements` . Per ottenere questo risultato, è possibile procedere come segue in XAML:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map>
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
</ContentPage>
```

Il codice C# equivalente è il seguente:

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};

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
};

// add the polygon to the map's MapElements collection
map.MapElements.Add(polygon);
```

Le `StrokeColor` `StrokeWidth` proprietà e vengono specificate per personalizzare il contorno del poligono. Il `FillColor` valore della proprietà corrisponde al `StrokeColor` valore della proprietà ma ha un valore alfa specificato per renderlo trasparente, consentendo la visualizzazione della mappa sottostante attraverso la forma. La `GeoPath` proprietà contiene un elenco di `Position` oggetti che definiscono le coordinate geografiche dei punti del poligono. Un `Polygon` oggetto viene sottoposto a rendering sulla mappa dopo che è stato aggiunto alla `MapElements` raccolta di `Map` .

> [!NOTE]
> Un oggetto `Polygon` è una forma completamente racchiusa. Il primo e l'ultimo punto verranno connessi automaticamente se non corrispondono.

## <a name="create-a-polyline"></a>Creare una polilinea

È `Polyline` possibile aggiungere un oggetto a una mappa creandone un'istanza e aggiungendolo alla raccolta della mappa `MapElements` . Per ottenere questo risultato, è possibile procedere come segue in XAML:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map>
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
</ContentPage>
```

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};
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

Le `StrokeColor` `StrokeWidth` proprietà e vengono specificate per personalizzare la riga. La `GeoPath` proprietà contiene un elenco di `Position` oggetti che definiscono le coordinate geografiche dei punti della polilinea. Un `Polyline` oggetto viene sottoposto a rendering sulla mappa dopo che è stato aggiunto alla `MapElements` raccolta di `Map` .

## <a name="create-a-circle"></a>Creare un cerchio

È `Circle` possibile aggiungere un oggetto a una mappa creandone un'istanza e aggiungendolo alla raccolta della mappa `MapElements` . Per ottenere questo risultato, è possibile procedere come segue in XAML:

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
      <maps:Map>
          <maps:Map.MapElements>
              <maps:Circle StrokeColor="#88FF0000"
                           StrokeWidth="8"
                           FillColor="#88FFC0CB">
                  <maps:Circle.Center>
                      <maps:Position>
                          <x:Arguments>
                              <x:Double>37.79752</x:Double>
                              <x:Double>-122.40183</x:Double>
                          </x:Arguments>
                      </maps:Position>
                  </maps:Circle.Center>
                  <maps:Circle.Radius>
                      <maps:Distance>
                          <x:Arguments>
                              <x:Double>250</x:Double>
                          </x:Arguments>
                      </maps:Distance>
                  </maps:Circle.Radius>
              </maps:Circle>             
          </maps:Map.MapElements>
          ...
      </maps:Map>
</ContentPage>
```

Il codice C# equivalente è il seguente:

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map();

// Instantiate a Circle
Circle circle = new Circle
{
    Center = new Position(37.79752, -122.40183),
    Radius = new Distance(250),
    StrokeColor = Color.FromHex("#88FF0000"),
    StrokeWidth = 8,
    FillColor = Color.FromHex("#88FFC0CB")
};

// Add the Circle to the map's MapElements collection
map.MapElements.Add(circle);
```

La posizione dell'oggetto `Circle` sulla mappa è determinata dal valore delle `Center` `Radius` proprietà e. La `Center` proprietà definisce il centro del cerchio, in latitudine e longitudine, mentre la `Radius` proprietà definisce il raggio del cerchio in metri. Le `StrokeColor` `StrokeWidth` proprietà e vengono specificate per personalizzare il contorno del cerchio. Il `FillColor` valore della proprietà specifica il colore all'interno del perimetro del cerchio. Entrambi i valori dei colori specificano un canale alfa, consentendo alla mappa sottostante di essere visibile attraverso il cerchio. `Circle`Viene eseguito il rendering dell'oggetto sulla mappa dopo che è stato aggiunto alla `MapElements` raccolta di `Map` .

> [!NOTE]
> La `GeographyUtils` classe dispone di un `ToCircumferencePositions` metodo di estensione che converte un `Circle` oggetto (che definisce `Center` `Radius` i valori di proprietà e) in un elenco di `Position` oggetti che costituiscono le coordinate di latitudine e Longitudine del perimetro del cerchio.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
