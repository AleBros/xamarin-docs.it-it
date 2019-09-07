---
title: Evidenziazione di un percorso su una mappa
description: Questo articolo illustra come aggiungere una sovrimpressione polilinea a una mappa. Una sovrimpressione polilinea è una serie di segmenti di linea collegati che sono in genere usati per visualizzare un percorso su una mappa o per definire una qualsiasi forma richiesta.
ms.prod: xamarin
ms.assetid: FBFDC715-1654-4188-82A0-FC522548BCFF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: af6e491725ac3dad843238c1adb547ac76a9c9ea
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771861"
---
# <a name="highlighting-a-route-on-a-map"></a>Evidenziazione di un percorso su una mappa

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-polyline)

_Questo articolo illustra come aggiungere una sovrimpressione polilinea a una mappa. Una sovrimpressione polilinea è una serie di segmenti di linea collegati che sono in genere usati per visualizzare un percorso su una mappa o per definire una qualsiasi forma richiesta._

## <a name="overview"></a>Panoramica

Una sovrimpressione è un elemento grafico a più livelli su una mappa. Le sovrimpressioni supportano contenuto grafico di tipo disegno che viene ridimensionato con la mappa quando si esegue lo zoom. Gli screenshot seguenti mostrano il risultato dell'aggiunta di una sovrimpressione polilinea a una mappa:

![](polyline-map-overlay-images/screenshots.png)

Quando un'applicazione Xamarin.Forms eseguire il rendering di un controllo [`Map`](xref:Xamarin.Forms.Maps.Map), in iOS viene creata un'istanza della classe `MapRenderer`, che a sua volta crea un'istanza di un controllo `MKMapView` nativo. Nella piattaforma Android la classe `MapRenderer` crea un'istanza di un controllo `MapView` nativo. Nella piattaforma UWP (Universal Windows Platform) la classe `MapRenderer` crea un'istanza di un controllo `MapControl` nativo. È possibile sfruttare il processo di rendering per implementare personalizzazioni della mappa specifiche della piattaforma creando un renderer personalizzato per `Map` in ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#Creating_the_Custom_Map) una mappa personalizzata Xamarin.Forms.
1. [Utilizzare](#Consuming_the_Custom_Map) la mappa personalizzata da Xamarin.Forms.
1. [Personalizzare](#Customizing_the_Map) la mappa creando un renderer personalizzato per la mappa su ogni piattaforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) deve essere inizializzato e configurato prima di poter essere utilizzato. Per altre informazioni, vedere [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Per informazioni sulla personalizzazione di una mappa usando un renderer personalizzato, vedere [Customizing a Map Pin](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md) (Personalizzazione di una puntina per la mappa).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Creazione della mappa personalizzata

Creare una sottoclasse della classe [`Map`](xref:Xamarin.Forms.Maps.Map) che aggiunge una proprietà `RouteCoordinates`:

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

La proprietà `RouteCoordinates` archivierà una raccolta di coordinate che definiscono il percorso da evidenziare.

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
    customMap.RouteCoordinates.Add (new Position (37.785559, -122.396728));
    customMap.RouteCoordinates.Add (new Position (37.780624, -122.390541));
    customMap.RouteCoordinates.Add (new Position (37.777113, -122.394983));
    customMap.RouteCoordinates.Add (new Position (37.776831, -122.394627));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (1.0)));
  }
}
```

Questa inizializzazione specifica una serie di coordinate di latitudine e longitudine, per definire il percorso sulla mappa da evidenziare. Quindi posiziona la visualizzazione della mappa con il metodo [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*), che modifica la posizione e livello di zoom della mappa mediante la creazione di [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) da [`Position`](xref:Xamarin.Forms.Maps.Position) e [`Distance`](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizzazione della mappa

È ora necessario aggiungere un renderer personalizzato a ogni progetto di applicazione per aggiungere la sovrimpressione polilinea alla mappa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Creazione del renderer personalizzato in iOS

Creare una sottoclasse della classe `MapRenderer` ed eseguire l'override del metodo `OnElementChanged` per aggiungere la sovrimpressione polilinea:

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

Questo metodo esegue la configurazione seguente, a condizione che il renderer personalizzato sia associato a un nuovo elemento di Xamarin.Forms:

- La proprietà `MKMapView.OverlayRenderer` viene impostata su un delegato corrispondente.
- La raccolta di coordinate di latitudine e longitudine vengono recuperate dalla proprietà `CustomMap.RouteCoordinates` e archiviate come matrice di istanze di `CLLocationCoordinate2D`.
- La polilinea viene creata chiamando il metodo statico `MKPolyline.FromCoordinates`, che specifica la latitudine e longitudine di ogni punto.
- La polilinea viene aggiunta alla mappa chiamando il metodo `MKMapView.AddOverlay`.

Implementare poi il metodo `GetOverlayRenderer` per personalizzare il rendering della sovrimpressione:

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

#### <a name="creating-the-custom-renderer-on-android"></a>Creazione del renderer personalizzato in Android

Creare una sottoclasse della classe `MapRenderer` ed eseguire l'override dei metodi `OnElementChanged` e `OnMapReady` per aggiungere la sovrimpressione polilinea:

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

Il metodo `OnElementChanged` recupera la raccolta delle coordinate di latitudine e longitudine dalla proprietà `CustomMap.RouteCoordinates` e le archivia in una variabile membro. Il metodo chiama poi il metodo `MapView.GetMapAsync`, che ottiene l'oggetto sottostante `GoogleMap` associato alla visualizzazione, a condizione che il renderer personalizzato sia associato a un nuovo elemento di Xamarin.Forms. Quando l'istanza di `GoogleMap` è disponibile, verrà richiamato il metodo `OnMapReady`, in cui viene creata la polilinea creando un'istanza dell'oggetto `PolylineOptions` che specifica la latitudine e longitudine di ogni punto. La polilinea viene poi aggiunta alla mappa chiamando il metodo `NativeMap.AddPolyline`.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Creare il renderer personalizzato sulla piattaforma UWP (Universal Windows Platform)

Creare una sottoclasse della classe `MapRenderer` ed eseguire l'override del metodo `OnElementChanged` per aggiungere la sovrimpressione polilinea:

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

Questo metodo esegue le operazioni seguenti, a condizione che il renderer personalizzato sia associato a un nuovo elemento di Xamarin.Forms:

- La raccolta di coordinate di latitudine e longitudine viene recuperata dalla proprietà `CustomMap.RouteCoordinates` e convertita in un elenco (`List`) di coordinate `BasicGeoposition`.
- La polilinea viene creata creando un'istanza di un oggetto `MapPolyline`. La classe `MapPolygon` viene usata per visualizzare una linea sulla mappa mediante l'impostazione della relativa proprietà `Path` su un oggetto `Geopath` che contiene le coordinate della linea.
- Viene eseguito il rendering della polilinea sulla mappa aggiungendola alla raccolta `MapControl.MapElements`.

## <a name="summary"></a>Riepilogo

Questo articolo ha spiegato come aggiungere una sovrimpressione polilinea a una mappa, come visualizzare un percorso su una mappa o definire qualsiasi forma richiesta.

## <a name="related-links"></a>Collegamenti correlati

- [Polyline Map Ovlerlay (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-polyline) (Esempio di sovrimpressione polilinea sulla mappa)
- [Personalizzazione di una puntina per la mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
