---
title: Evidenziazione di una Route su una mappa
description: In questo articolo viene illustrato come aggiungere una sovrapposizione polilinea a una mappa. Una sovrapposizione polilinea è una serie di segmenti di linea collegati che sono in genere utilizzato per mostrare una route su una mappa, o qualsiasi forma che ha richiesto.
ms.prod: xamarin
ms.assetid: FBFDC715-1654-4188-82A0-FC522548BCFF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: f781a472a63d97c8859aff36b28e0fd4fa0c7756
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="highlighting-a-route-on-a-map"></a>Evidenziazione di una Route su una mappa

_In questo articolo viene illustrato come aggiungere una sovrapposizione polilinea a una mappa. Una sovrapposizione polilinea è una serie di segmenti di linea collegati che sono in genere utilizzato per mostrare una route su una mappa, o qualsiasi forma che ha richiesto._

## <a name="overview"></a>Panoramica

Sovrapposizione è un'immagine a più livelli in una mappa. Sovrapposizioni supportano disegno del contenuto con interfaccia grafica che consente una scalabilità con la mappa come si esegue lo zoom. Le schermate seguenti mostrano il risultato dell'aggiunta di una sovrapposizione polilinea a una mappa:

![](polyline-map-overlay-images/screenshots.png)

Quando un [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) rendering del controllo da un'applicazione di xamarin. Forms, in iOS il `MapRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza nativa `MKMapView` controllo. Nella piattaforma Android, il `MapRenderer` un'istanza nativa `MapView` controllo. Nel Windows piattaforma UWP (Universal), il `MapRenderer` un'istanza nativa `MapControl`. Il processo di rendering può essere sfruttato per implementare le personalizzazioni specifiche della piattaforma mappa creando un renderer personalizzato per un `Map` in ciascuna piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_Map) una mappa personalizzata xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Map) la mappa personalizzata da xamarin. Forms.
1. [Personalizzare](#Customizing_the_Map) la mappa mediante la creazione di un renderer personalizzato per la mappa in ogni piattaforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) deve essere inizializzato e configurato prima dell'uso. Per altre informazioni, vedere [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Per informazioni sulla personalizzazione di una mappa utilizzando un renderer personalizzato, vedere [personalizzazione di un Pin mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Creazione della mappa personalizzata

Creare una sottoclasse di [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) (classe), che aggiunge un `RouteCoordinates` proprietà:

```csharp
public class CustomMap : Map
{
  public List<Position> RouteCoordinates { get; set; }

  public CustomMap ()
  {
    RouteCoordinates = new List<Position> ();
  }
}
```

Il `RouteCoordinates` proprietà archivierà una raccolta di coordinate che definiscono la route viene evidenziata.

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
    customMap.RouteCoordinates.Add (new Position (37.785559, -122.396728));
    customMap.RouteCoordinates.Add (new Position (37.780624, -122.390541));
    customMap.RouteCoordinates.Add (new Position (37.777113, -122.394983));
    customMap.RouteCoordinates.Add (new Position (37.776831, -122.394627));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
  }
}
```

L'inizializzazione specifica una serie di coordinate di latitudine e longitudine, per definire la route nella mappa viene evidenziata. Viene quindi posizionato visualizzazione della mappa con la [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/) (metodo), che modifica la posizione e il livello di zoom della mappa creando un [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/) da un [ `Position` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/) e [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizzazione della mappa

Un renderer personalizzato deve ora essere aggiunto a ogni progetto di applicazione per aggiungere la sovrimpressione polilinea alla mappa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Creazione di Renderer personalizzato in iOS

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` metodo per aggiungere la sovrimpressione polilinea:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKPolylineRenderer polylineRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    polylineRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;
                nativeMap.OverlayRenderer = GetOverlayRenderer;

                CLLocationCoordinate2D[] coords = new CLLocationCoordinate2D[formsMap.RouteCoordinates.Count];
                int index = 0;
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coords[index] = new CLLocationCoordinate2D(position.Latitude, position.Longitude);
                    index++;
                }

                var routeOverlay = MKPolyline.FromCoordinates(coords);
                nativeMap.AddOverlay(routeOverlay);
            }
        }
        ...
    }
}

```

Questo metodo esegue la configurazione seguente, a condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms:

- Il `MKMapView.OverlayRenderer` è impostata su un delegato corrispondente.
- La raccolta di coordinate di latitudine e longitudine vengono recuperati i `CustomMap.RouteCoordinates` proprietà e archiviata come una matrice di `CLLocationCoordinate2D` istanze.
- La polilinea viene creata chiamando il metodo statico `MKPolyline.FromCoordinates` metodo, che specifica la latitudine e longitudine di ogni punto.
- La polilinea viene aggiunto alla mappa chiamando il `MKMapView.AddOverlay` metodo.

Implementare quindi il `GetOverlayRenderer` metodo per personalizzare il rendering della sovrimpressione:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKPolylineRenderer polylineRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (polylineRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          polylineRenderer = new MKPolylineRenderer(overlay as MKPolyline) {
              FillColor = UIColor.Blue,
              StrokeColor = UIColor.Red,
              LineWidth = 3,
              Alpha = 0.4f
          };
      }
      return polylineRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Creazione di Renderer personalizzato in Android

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` e `OnMapReady` metodi per aggiungere la sovrimpressione polilinea:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        List<Position> routeCoordinates;

        public CustomMapRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(Xamarin.Forms.Platform.Android.ElementChangedEventArgs<Map> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                // Unsubscribe
            }

            if (e.NewElement != null)
            {
                var formsMap = (CustomMap)e.NewElement;
                routeCoordinates = formsMap.RouteCoordinates;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var polylineOptions = new PolylineOptions();
            polylineOptions.InvokeColor(0x66FF0000);

            foreach (var position in routeCoordinates)
            {
                polylineOptions.Add(new LatLng(position.Latitude, position.Longitude));
            }

            NativeMap.AddPolyline(polylineOptions);
        }
    }
}
```

Il `OnElementChanged` consente di recuperare la raccolta delle coordinate di latitudine e longitudine dal `CustomMap.RouteCoordinates` proprietà e li archivia in una variabile membro. Chiama quindi il `MapView.GetMapAsync` metodo, che ottiene l'oggetto sottostante `GoogleMap` che è correlato alla vista, a condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms. Una volta il `GoogleMap` istanza è disponibile, il `OnMapReady` verrà richiamato metodo, in cui viene creata la polilinea creando un `PolylineOptions` oggetto che specifica la latitudine e longitudine di ogni punto. La polilinea viene quindi aggiunto alla mappa chiamando il `NativeMap.AddPolyline` metodo.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Creazione di Renderer personalizzato nella piattaforma Windows universale

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` metodo per aggiungere la sovrimpressione polilinea:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.UWP
{
    public class CustomMapRenderer : MapRenderer
    {
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

                var coordinates = new List<BasicGeoposition>();
                foreach (var position in formsMap.RouteCoordinates)
                {
                    coordinates.Add(new BasicGeoposition() { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polyline = new MapPolyline();
                polyline.StrokeColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polyline.StrokeThickness = 5;
                polyline.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polyline);
            }
        }
    }
}
```

Questo metodo esegue le operazioni seguenti, a condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms:

- La raccolta di coordinate di latitudine e longitudine vengono recuperati il `CustomMap.RouteCoordinates` proprietà e convertito in un `List` di `BasicGeoposition` coordinate.
- Viene creata la polilinea creando un `MapPolyline` oggetto. Il `MapPolygon` classe viene utilizzata per visualizzare una linea della mappa impostando il relativo `Path` proprietà per un `Geopath` oggetto che contiene le coordinate di riga.
- La polilinea viene eseguito il rendering della mappa, aggiungerlo al `MapControl.MapElements` insieme.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come aggiungere una sovrapposizione polilinea a una mappa, per visualizzare una route su una mappa, o qualsiasi forma che ha richiesto.


## <a name="related-links"></a>Collegamenti correlati

- [Polilinea mappa Ovlerlay (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polyline/)
- [Personalizzazione di una puntina per la mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/)
