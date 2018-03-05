---
title: Evidenziazione di un'Area circolare su una mappa
description: In questo articolo viene illustrato come aggiungere una sovrapposizione circolare a una mappa, per evidenziare un'area circolare della mappa.
ms.topic: article
ms.prod: xamarin
ms.assetid: 6FF8BD15-074E-4E6A-9522-F9E2BE32EF12
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 0eef31c5b9a93154b1038ffa63ee560bd738fe6b
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="highlighting-a-circular-area-on-a-map"></a>Evidenziazione di un'Area circolare su una mappa

_In questo articolo viene illustrato come aggiungere una sovrapposizione circolare a una mappa, per evidenziare un'area circolare della mappa._

## <a name="overview"></a>Panoramica

Sovrapposizione è un'immagine a più livelli in una mappa. Sovrapposizioni supportano disegno del contenuto con interfaccia grafica che consente una scalabilità con la mappa come si esegue lo zoom. Le schermate seguenti mostrano il risultato dell'aggiunta di una sovrapposizione circolare a una mappa:

![](circle-map-overlay-images/screenshots.png)

Quando un [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) rendering del controllo da un'applicazione di xamarin. Forms, in iOS il `MapRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza nativa `MKMapView` controllo. Nella piattaforma Android, il `MapRenderer` un'istanza nativa `MapView` controllo. Nel Windows piattaforma UWP (Universal), il `MapRenderer` un'istanza nativa `MapControl`. Il processo di rendering può essere sfruttato per implementare le personalizzazioni specifiche della piattaforma mappa creando un renderer personalizzato per un `Map` in ciascuna piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_Map) una mappa personalizzata xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Map) la mappa personalizzata da xamarin. Forms.
1. [Personalizzare](#Customizing_the_Map) la mappa mediante la creazione di un renderer personalizzato per la mappa in ogni piattaforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/") deve essere inizializzato e configurato prima dell'uso. Per ulteriori informazioni, vedere [`Maps Control`](~/xamarin-forms/user-interface/map.md)

Per informazioni sulla personalizzazione di una mappa utilizzando un renderer personalizzato, vedere [personalizzazione di un Pin mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Creazione della mappa personalizzata

Creare un `CustomCircle` classe che ha `Position` e `Radius` proprietà:

```csharp
public class CustomCircle
{
  public Position Position { get; set; }
  public double Radius { get; set; }
}
```

Quindi, creare una sottoclasse di [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) (classe), che aggiunge una proprietà di tipo `CustomCircle`:

```csharp
public class CustomMap : Map
{
  public CustomCircle Circle { get; set; }
}
```

<a name="Consuming_the_Custom_Map" />

### <a name="consuming-the-custom-map"></a>Utilizzo di mappa personalizzata

Utilizzare il `CustomMap` controllo mediante la dichiarazione di un'istanza dell'istanza di pagina XAML:

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

In alternativa, utilizzare il `CustomMap` controllo mediante la dichiarazione di un'istanza dell'istanza di pagina in c#:

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

Inizializzare il `CustomMap` controllare in base alle esigenze:

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

Aggiunge questa inizializzazione [ `Pin` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Pin/) e `CustomCircle` istanze per la mappa personalizzata e posiziona la visualizzazione della mappa con la [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/) metodo, che modifica la posizione e zoom livello della mappa creando un [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/) da un [ `Position` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/) e [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizzazione della mappa

Un renderer personalizzato deve ora essere aggiunto a ogni progetto di applicazione per aggiungere la sovrimpressione circolare alla mappa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Creazione di Renderer personalizzato in iOS

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` metodo per aggiungere la sovrimpressione circolare:

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

Questo metodo esegue la configurazione seguente, a condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms:

- Il `MKMapView.OverlayRenderer` è impostata su un delegato corrispondente.
- Il cerchio viene creato impostando un valore statico `MKCircle` oggetto che specifica il centro del cerchio e il raggio del cerchio in metri.
- Il cerchio viene aggiunto alla mappa chiamando il `MKMapView.AddOverlay` metodo.

Implementare quindi il `GetOverlayRenderer` metodo per personalizzare il rendering della sovrimpressione:

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

#### <a name="creating-the-custom-renderer-on-android"></a>Creazione di Renderer personalizzato in Android

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` e `OnMapReady` metodi per aggiungere la sovrimpressione circolare:

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

Il `OnElementChanged` chiamate al metodo di `MapView.GetMapAsync` metodo, che ottiene l'oggetto sottostante `GoogleMap` che è correlato alla vista, a condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms. Una volta il `GoogleMap` istanza è disponibile, il `OnMapReady` verrà richiamato metodo, in cui viene creato il cerchio creando un `CircleOptions` oggetto che specifica il centro del cerchio e il raggio del cerchio in metri. Il cerchio viene quindi aggiunto alla mappa chiamando il `NativeMap.AddCircle` metodo.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Creazione di Renderer personalizzato nella piattaforma Windows universale

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` metodo per aggiungere la sovrimpressione circolare:

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
        ...
    }
}
```

Questo metodo esegue le operazioni seguenti, a condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms:

- La posizione di cerchio e radius vengono recuperati dal `CustomMap.Circle` proprietà e passato al `GenerateCircleCoordinates` metodo, che genera l'errore latitudine e longitudine coordinate per il perimetro di circle.
- Le coordinate perimetrale cerchio vengono convertite in un `List` di `BasicGeoposition` coordinate.
- Viene creato il cerchio creando un `MapPolygon` oggetto. Il `MapPolygon` classe viene utilizzata per visualizzare una forma multipunto sulla mappa impostando il relativo `Path` proprietà per un `Geopath` oggetto che contiene le coordinate della forma.
- Il poligono viene eseguito il rendering della mappa aggiungendolo al `MapControl.MapElements` insieme.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come aggiungere una sovrapposizione circolare a una mappa, per evidenziare un'area circolare della mappa.


## <a name="related-links"></a>Collegamenti correlati

- [Circolare mappa Ovlerlay (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/circle/)
- [Personalizzazione di una puntina per la mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/)
