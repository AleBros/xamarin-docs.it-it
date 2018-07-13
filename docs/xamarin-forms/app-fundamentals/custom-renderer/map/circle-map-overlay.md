---
title: L'evidenziazione di un'Area circolare su una mappa
description: Questo articolo illustra come aggiungere una sovrapposizione circolare a una mappa, per evidenziare un'area circolare della mappa. IOS e Android fornire API per l'aggiunta di sovrapposizione circolare alla mappa, sulla piattaforma UWP la sovrimpressione viene visualizzata come un poligono.
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 3064296d4c78a3342fb27afc971c37a029987e5e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998557"
---
# <a name="highlighting-a-circular-area-on-a-map"></a>L'evidenziazione di un'Area circolare su una mappa

_Questo articolo illustra come aggiungere una sovrapposizione circolare a una mappa, per evidenziare un'area circolare della mappa._

## <a name="overview"></a>Panoramica

Un controllo overlay è un'immagine a più livelli in una mappa. Le sovrimpressioni supportano disegno contenuto grafico che viene ridimensionata con la mappa come si esegue lo zoom. Gli screenshot seguenti mostrano il risultato dell'aggiunta di una sovrimpressione circolare a una mappa:

![](circle-map-overlay-images/screenshots.png)

Quando un [ `Map` ](xref:Xamarin.Forms.Maps.Map) rendering del controllo da un'applicazione xamarin. Forms, in iOS il `MapRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza di nativo `MKMapView` controllo. La piattaforma Android, il `MapRenderer` un'istanza nativa `MapView` controllo. In Universal Windows Platform (UWP), il `MapRenderer` un'istanza nativa `MapControl`. Il processo di rendering possibile avvantaggiarsi di implementare le personalizzazioni specifiche della piattaforma mappe mediante la creazione di un renderer personalizzato per un `Map` in ogni piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_Map) una mappa personalizzata di xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Map) la mappa personalizzata da xamarin. Forms.
1. [Personalizzare](#Customizing_the_Map) mappa mediante la creazione di un renderer personalizzato per la mappa su ogni piattaforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) deve essere inizializzato e configurato prima dell'uso. Per altre informazioni, vedere [`Maps Control`](~/xamarin-forms/user-interface/map.md)

Per informazioni sulla personalizzazione di una mappa usando un renderer personalizzato, vedere [personalizzazione di un Pin di mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Creazione della mappa personalizzata

Creare un `CustomCircle` classe avente `Position` e `Radius` proprietà:

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

Quindi, creare una sottoclasse di [ `Map` ](xref:Xamarin.Forms.Maps.Map) (classe), che aggiunge una proprietà di tipo `CustomCircle`:

```csharp
public class CustomMap : Map
{
  public CustomCircle Circle { get; set; }
}
```

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>Utilizza la mappa personalizzata

Utilizzare il `CustomMap` controllo dichiarando un'istanza dell'istanza di pagina XAML:

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

In alternativa, utilizzare il `CustomMap` controllo dichiarando un'istanza dell'istanza di pagina in c#:

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

Inizializzare il `CustomMap` controllo in base alle esigenze:

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

Questa inizializzazione aggiunge [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) e `CustomCircle` istanze alla mappa personalizzata e posiziona la visualizzazione della mappa con il [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) metodo, che modifica la posizione e zoom livello della mappa mediante la creazione di un [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) da una [ `Position` ](xref:Xamarin.Forms.Maps.Position) e un [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizzazione della mappa

Un renderer personalizzato deve ora essere aggiunti a ogni progetto di applicazione per aggiungere l'immagine sovrapposta al circolare alla mappa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Creare il Renderer personalizzati in iOS

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` metodo per aggiungere l'immagine sovrapposta al circolare:

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

Questo metodo esegue la configurazione seguente, a condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms:

- Il `MKMapView.OverlayRenderer` è impostata su un delegato corrispondente.
- Il cerchio viene creato impostando un valore statico `MKCircle` oggetto che specifica il centro del cerchio e il raggio del cerchio in metri.
- Il cerchio viene aggiunto alla mappa chiamando il `MKMapView.AddOverlay` (metodo).

Implementare poi il `GetOverlayRenderer` metodo per personalizzare il rendering della sovrimpressione:

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

#### <a name="creating-the-custom-renderer-on-android"></a>Creare il Renderer personalizzati in Android

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` e `OnMapReady` metodi per aggiungere l'immagine sovrapposta al circolare:

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

Il `OnElementChanged` chiamate al metodo il `MapView.GetMapAsync` metodo, che ottiene l'oggetto sottostante `GoogleMap` che è associato alla visualizzazione, condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms. Una volta il `GoogleMap` istanza è disponibile, il `OnMapReady` metodo verrà richiamato, in cui il controllo circle viene creato creando un `CircleOptions` oggetto che specifica il centro del cerchio e il raggio del cerchio in metri. Il cerchio viene quindi aggiunto alla mappa chiamando il `NativeMap.AddCircle` (metodo).

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Creare il Renderer personalizzato sulla piattaforma Windows universale

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` metodo per aggiungere l'immagine sovrapposta al circolare:

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

Questo metodo esegue le operazioni seguenti, a condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms:

- La posizione di cerchio e radius vengono recuperati dal `CustomMap.Circle` proprietà e passato al `GenerateCircleCoordinates` metodo, che genera latitudine e longitudine coordinate per consentire il perimetro del cerchio. Seguito è riportato il codice per questo metodo di supporto.
- Le coordinate di perimetro del cerchio vengono convertite in un `List` di `BasicGeoposition` coordinate.
- Il cerchio viene creato creando un `MapPolygon` oggetto. Il `MapPolygon` classe viene utilizzata per visualizzare una forma di multi-punto sulla mappa mediante l'impostazione relativa `Path` proprietà su un `Geopath` oggetto che contiene le coordinate di forma.
- Il poligono viene eseguito il rendering sulla mappa, aggiungerlo al `MapControl.MapElements` raccolta.


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

Questo articolo ha illustrato come aggiungere una sovrapposizione circolare a una mappa, per evidenziare un'area circolare della mappa.


## <a name="related-links"></a>Collegamenti correlati

- [Circolare mappa Ovlerlay (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)
- [Personalizzazione di una puntina per la mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
