---
title: Evidenziazione di un'area circolare su una mappa
description: Questo articolo illustra come aggiungere una sovrimpressione circolare a una mappa per evidenziare un'area circolare della mappa stessa. iOS e Android includono API per l'aggiunta della sovrimpressione circolare alla mappa, ma nella piattaforma UWP la sovrimpressione viene visualizzata come poligono.
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: ed671063aad55cb6c4730494f024a2ec2a016de1
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051931"
---
# <a name="highlighting-a-circular-area-on-a-map"></a>Evidenziazione di un'area circolare su una mappa

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)

_Questo articolo illustra come aggiungere una sovrimpressione circolare a una mappa per evidenziare un'area circolare della mappa stessa._

## <a name="overview"></a>Panoramica

Una sovrimpressione è un elemento grafico a più livelli su una mappa. Le sovrimpressioni supportano contenuto grafico di tipo disegno che viene ridimensionato con la mappa quando si esegue lo zoom. Gli screenshot seguenti mostrano il risultato dell'aggiunta di una sovrimpressione circolare a una mappa:

![](circle-map-overlay-images/screenshots.png)

Quando un'applicazione Xamarin.Forms eseguire il rendering di un controllo [`Map`](xref:Xamarin.Forms.Maps.Map), in iOS viene creata un'istanza della classe `MapRenderer`, che a sua volta crea un'istanza di un controllo `MKMapView` nativo. Nella piattaforma Android la classe `MapRenderer` crea un'istanza di un controllo `MapView` nativo. Nella piattaforma UWP (Universal Windows Platform) la classe `MapRenderer` crea un'istanza di un controllo `MapControl` nativo. È possibile sfruttare il processo di rendering per implementare personalizzazioni della mappa specifiche della piattaforma creando un renderer personalizzato per `Map` in ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#Creating_the_Custom_Map) una mappa personalizzata Xamarin.Forms.
1. [Utilizzare](#Consuming_the_Custom_Map) la mappa personalizzata da Xamarin.Forms.
1. [Personalizzare](#Customizing_the_Map) la mappa creando un renderer personalizzato per la mappa su ogni piattaforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) deve essere inizializzato e configurato prima dell'uso. Per altre informazioni, vedere [`Maps Control`](~/xamarin-forms/user-interface/map.md)

Per informazioni sulla personalizzazione di una mappa usando un renderer personalizzato, vedere [Personalizzazione di una puntina per la mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Creazione della mappa personalizzata

Creare una classe `CustomCircle` con le proprietà `Position` e `Radius`:

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

Creare quindi una sottoclasse della classe [`Map`](xref:Xamarin.Forms.Maps.Map) che aggiunge una proprietà di tipo `CustomCircle`:

```csharp
public class CustomMap : Map
{
  public CustomCircle Circle { get; set; }
}
```

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>Utilizzo della mappa personalizzata

Utilizzare il controllo `CustomMap` dichiarandone un'istanza nell'istanza della pagina XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MapOverlay;assembly=MapOverlay"
             x:Class="MapOverlay.MapPage">
    <ContentPage.Content>
        <local:CustomMap x:Name="customMap" MapType="Street" WidthRequest="{x:Static local:App.ScreenWidth}" HeightRequest="{x:Static local:App.ScreenHeight}" />
    </ContentPage.Content>
</ContentPage>
```

In alternativa, utilizzare il controllo `CustomMap` dichiarandone un'istanza nell'istanza della pagina C#:

```csharp
public class MapPageCS : ContentPage
{
    public MapPageCS ()
    {
        var customMap = new CustomMap {
            MapType = MapType.Street,
            WidthRequest = App.ScreenWidth,
            HeightRequest = App.ScreenHeight
        };
        ...
        Content = customMap;
    }
}
```

Inizializzare il controllo `CustomMap` come richiesto:

```csharp
public partial class MapPage : ContentPage
{
  public MapPage ()
  {
    ...
    var pin = new Pin {
      Type = PinType.Place,
      Position = new Position (37.79752, -122.40183),
      Label = "Xamarin San Francisco Office",
      Address = "394 Pacific Ave, San Francisco CA"
    };

    var position = new Position (37.79752, -122.40183);
    customMap.Circle = new CustomCircle {
      Position = position,
      Radius = 1000
    };

    customMap.Pins.Add (pin);
    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (position, Distance.FromMiles (1.0)));
  }
}
```

Questa inizializzazione aggiunge le istanze di [`Pin`](xref:Xamarin.Forms.Maps.Pin) e `CustomCircle` alla mappa personalizzata e posiziona la visualizzazione della mappa con il metodo [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*), che modifica la posizione e il livello di zoom della mappa mediante la creazione di [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) da [`Position`](xref:Xamarin.Forms.Maps.Position) e [`Distance`](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizzazione della mappa

È ora necessario aggiungere un renderer personalizzato a ogni progetto di applicazione per aggiungere la sovrimpressione circolare alla mappa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Creazione del renderer personalizzato in iOS

Creare una sottoclasse della classe `MapRenderer` ed eseguire l'override del metodo `OnElementChanged` per aggiungere la sovrimpressione circolare:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKCircleRenderer circleRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    circleRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                var circle = formsMap.Circle;

                nativeMap.OverlayRenderer = GetOverlayRenderer;

                var circleOverlay = MKCircle.Circle(new CoreLocation.CLLocationCoordinate2D(circle.Position.Latitude, circle.Position.Longitude), circle.Radius);
                nativeMap.AddOverlay(circleOverlay);
            }
        }
        ...
    }
}

```

Questo metodo esegue la configurazione seguente, a condizione che il renderer personalizzato sia associato a un nuovo elemento di Xamarin.Forms:

- La proprietà `MKMapView.OverlayRenderer` viene impostata su un delegato corrispondente.
- Il cerchio viene creato impostando un oggetto `MKCircle` statico che specifica il centro del cerchio e il raggio del cerchio in metri.
- Il cerchio viene aggiunto alla mappa chiamando il metodo `MKMapView.AddOverlay`.

Implementare poi il metodo `GetOverlayRenderer` per personalizzare il rendering della sovrimpressione:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKCircleRenderer circleRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (circleRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          circleRenderer = new MKCircleRenderer(overlay as MKCircle) {
              FillColor = UIColor.Red,
              Alpha = 0.4f
          };
      }
      return circleRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Creazione del renderer personalizzato in Android

Creare una sottoclasse della classe `MapRenderer` ed eseguire l'override dei metodi `OnElementChanged` e `OnMapReady` per aggiungere la sovrimpressione circolare:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        CustomCircle circle;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Xamarin.Forms.Maps.Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                circle = formsMap.Circle;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var circleOptions = new CircleOptions();
            circleOptions.InvokeCenter(new LatLng(circle.Position.Latitude, circle.Position.Longitude));
            circleOptions.InvokeRadius(circle.Radius);
            circleOptions.InvokeFillColor(0X66FF0000);
            circleOptions.InvokeStrokeColor(0X66FF0000);
            circleOptions.InvokeStrokeWidth(0);

            NativeMap.AddCircle(circleOptions);
        }
    }
}
```

Il metodo `OnElementChanged` chiama il metodo `MapView.GetMapAsync`, che ottiene l'oggetto sottostante `GoogleMap` associato alla visualizzazione, a condizione che il renderer personalizzato sia associato a un nuovo elemento di Xamarin.Forms. Quando l'istanza `GoogleMap` è disponibile, verrà richiamato il metodo `OnMapReady` con il quale viene creato il cerchio creando un'istanza di un oggetto `CircleOptions` che specifica il centro del cerchio e il raggio del cerchio in metri. Il cerchio viene quindi aggiunto alla mappa chiamando il metodo `NativeMap.AddCircle`.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Creare il renderer personalizzato sulla piattaforma UWP (Universal Windows Platform)

Creare una sottoclasse della classe `MapRenderer` ed eseguire l'override del metodo `OnElementChanged` per aggiungere la sovrimpressione circolare:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
        const int EarthRadiusInMeteres = 6371000;

        protected override void OnElementChanged(ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }
            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MapControl;
                var circle = formsMap.Circle;

                var coordinates = new List<BasicGeoposition>();
                var positions = GenerateCircleCoordinates(circle.Position, circle.Radius);
                foreach (var position in positions)
                {
                    coordinates.Add(new BasicGeoposition { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polygon = new MapPolygon();
                polygon.FillColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeThickness = 5;
                polygon.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polygon);
            }
        }
        // GenerateCircleCoordinates helper method (below)
    }
}
```

Questo metodo esegue le operazioni seguenti, a condizione che il renderer personalizzato sia associato a un nuovo elemento di Xamarin.Forms:

- La posizione e il raggio del cerchio vengono recuperati dalla proprietà `CustomMap.Circle` e passati al metodo `GenerateCircleCoordinates`, che genera le coordinate di latitudine e longitudine per il perimetro del cerchio. Il codice per questo metodo helper è riportato di seguito.
- Le coordinate del perimetro del cerchio vengono convertite in un elenco (`List`) di coordinate `BasicGeoposition`.
- Il cerchio viene creato creando un'istanza di un oggetto `MapPolygon`. La classe `MapPolygon` viene usata per visualizzare una forma multipunto sulla mappa mediante l'impostazione della relativa proprietà `Path` su un oggetto `Geopath` che contiene le coordinate della forma.
- Viene eseguito il rendering del poligono sulla mappa aggiungendolo alla raccolta `MapControl.MapElements`.


```
List<Position> GenerateCircleCoordinates(Position position, double radius)
{
    double latitude = position.Latitude.ToRadians();
    double longitude = position.Longitude.ToRadians();
    double distance = radius / EarthRadiusInMeteres;
    var positions = new List<Position>();

    for (int angle = 0; angle <=360; angle++)
    {
        double angleInRadians = ((double)angle).ToRadians();
        double latitudeInRadians = Math.Asin(Math.Sin(latitude) * Math.Cos(distance) + Math.Cos(latitude) * Math.Sin(distance) * Math.Cos(angleInRadians));
        double longitudeInRadians = longitude + Math.Atan2(Math.Sin(angleInRadians) * Math.Sin(distance) * Math.Cos(latitude), Math.Cos(distance) - Math.Sin(latitude) * Math.Sin(latitudeInRadians));

        var pos = new Position(latitudeInRadians.ToDegrees(), longitudeInRadians.ToDegrees());
        positions.Add(pos);
    }

    return positions;
}
```

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come aggiungere una sovrimpressione circolare a una mappa per evidenziare un'area circolare della mappa stessa.


## <a name="related-links"></a>Collegamenti correlati

- [Circular Map Ovlerlay (sample)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/) (Esempio di sovrimpressione circolare sulla mappa)
- [Personalizzazione di una puntina per la mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
