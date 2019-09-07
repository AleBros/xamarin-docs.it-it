---
title: Evidenziazione di una regione su una mappa
description: Questo articolo illustra come aggiungere una sovrimpressione poligonale a una mappa per evidenziare un'area della mappa stessa. I poligoni sono forme chiuse con riempimento.
ms.prod: xamarin
ms.assetid: E79EB2CF-8DD6-44A8-B47D-5F0A94FB0A63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 103d4f40a1c368f576276c4cdcbdc585d2a1536a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771895"
---
# <a name="highlighting-a-region-on-a-map"></a>Evidenziazione di una regione su una mappa

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-polygon)

_Questo articolo ha illustrato come aggiungere una sovrimpressione poligonale a una mappa per evidenziare un'area della mappa stessa. I poligoni sono forme chiuse con riempimento._

## <a name="overview"></a>Panoramica

Una sovrimpressione è un elemento grafico a più livelli su una mappa. Le sovrimpressioni supportano contenuto grafico di tipo disegno che viene ridimensionato con la mappa quando si esegue lo zoom. Gli screenshot seguenti mostrano il risultato dell'aggiunta di una sovrimpressione poligonale su una mappa:

![](polygon-map-overlay-images/screenshots.png)

Quando un'applicazione Xamarin.Forms esegue il rendering di un controllo [`Map`](xref:Xamarin.Forms.Maps.Map), in iOS viene creata un'istanza della classe `MapRenderer`, che a sua volta crea un'istanza di un controllo `MKMapView` nativo. Nella piattaforma Android la classe `MapRenderer` crea un'istanza di un controllo `MapView` nativo. Nella piattaforma UWP (Universal Windows Platform) la classe `MapRenderer` crea un'istanza di un controllo `MapControl` nativo. È possibile sfruttare il processo di rendering per implementare personalizzazioni della mappa specifiche della piattaforma creando un renderer personalizzato per `Map` in ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#Creating_the_Custom_Map) una mappa personalizzata Xamarin.Forms.
1. [Utilizzare](#Consuming_the_Custom_Map) la mappa personalizzata da Xamarin.Forms.
1. [Personalizzare](#Customizing_the_Map) la mappa creando un renderer personalizzato per la mappa su ogni piattaforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) deve essere inizializzato e configurato prima di poter essere utilizzato. Per altre informazioni, vedere [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Per informazioni sulla personalizzazione di una mappa usando un renderer personalizzato, vedere [Customizing a Map Pin](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md) (Personalizzazione di una puntina per la mappa).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Creazione della mappa personalizzata

Creare una sottoclasse della classe [`Map`](xref:Xamarin.Forms.Maps.Map) che aggiunge una proprietà `ShapeCoordinates`:

```csharp
public class CustomMap : Map
{
  public List<Position> ShapeCoordinates { get; set; }

  public CustomMap ()
  {
    ShapeCoordinates = new List<Position> ();
  }
}
```

La proprietà `ShapeCoordinates` archivierà una raccolta di coordinate che definiscono l'area da evidenziare.

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
    customMap.ShapeCoordinates.Add (new Position (37.797513, -122.402058));
    customMap.ShapeCoordinates.Add (new Position (37.798433, -122.402256));
    customMap.ShapeCoordinates.Add (new Position (37.798582, -122.401071));
    customMap.ShapeCoordinates.Add (new Position (37.797658, -122.400888));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (0.1)));
  }
}
```

Questa inizializzazione specifica una serie di coordinate di latitudine e longitudine, per definire l'area della mappa da evidenziare. Quindi posiziona la visualizzazione della mappa con il metodo [`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion*), che modifica la posizione e livello di zoom della mappa mediante la creazione di [`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan) da [`Position`](xref:Xamarin.Forms.Maps.Position) e [`Distance`](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizzazione della mappa

È ora necessario aggiungere un renderer personalizzato a ogni progetto di applicazione per aggiungere la sovrimpressione poligona alla mappa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Creazione del renderer personalizzato in iOS

Creare una sottoclasse della classe `MapRenderer` ed eseguire l'override del metodo `OnElementChanged` per aggiungere la sovrimpressione poligona:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.iOS
{
    public class CustomMapRenderer : MapRenderer
    {
        MKPolygonRenderer polygonRenderer;

        protected override void OnElementChanged(ElementChangedEventArgs<View> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null) {
                var nativeMap = Control as MKMapView;
                if (nativeMap != null) {
                    nativeMap.RemoveOverlays(nativeMap.Overlays);
                    nativeMap.OverlayRenderer = null;
                    polygonRenderer = null;
                }
            }

            if (e.NewElement != null) {
                var formsMap = (CustomMap)e.NewElement;
                var nativeMap = Control as MKMapView;

                nativeMap.OverlayRenderer = GetOverlayRenderer;

                CLLocationCoordinate2D[] coords = new CLLocationCoordinate2D[formsMap.ShapeCoordinates.Count];

                int index = 0;
                foreach (var position in formsMap.ShapeCoordinates)
                {
                    coords[index] = new CLLocationCoordinate2D(position.Latitude, position.Longitude);
                    index++;
                }

                var blockOverlay = MKPolygon.FromCoordinates(coords);
                nativeMap.AddOverlay(blockOverlay);
            }
        }
        ...
    }
}

```

Questo metodo esegue la configurazione seguente, a condizione che il renderer personalizzato sia associato a un nuovo elemento di Xamarin.Forms:

- La proprietà `MKMapView.OverlayRenderer` viene impostata su un delegato corrispondente.
- La raccolta di coordinate di latitudine e longitudine vengono recuperate dalla proprietà `CustomMap.ShapeCoordinates` e archiviate come matrice di istanze di `CLLocationCoordinate2D`.
- Il poligono viene creato chiamando il metodo statico `MKPolygon.FromCoordinates`, che specifica la latitudine e longitudine di ogni punto.
- Il poligono viene aggiunto alla mappa chiamando il metodo `MKMapView.AddOverlay`. Questo metodo chiude automaticamente il poligono tracciando una linea che connette il primo e l'ultimo punto.

Implementare poi il metodo `GetOverlayRenderer` per personalizzare il rendering della sovrimpressione:

```csharp
public class CustomMapRenderer : MapRenderer
{
  MKPolygonRenderer polygonRenderer;
  ...

  MKOverlayRenderer GetOverlayRenderer(MKMapView mapView, IMKOverlay overlayWrapper)
  {
      if (polygonRenderer == null && !Equals(overlayWrapper, null)) {
          var overlay = Runtime.GetNSObject(overlayWrapper.Handle) as IMKOverlay;
          polygonRenderer = new MKPolygonRenderer(overlay as MKPolygon) {
              FillColor = UIColor.Red,
              StrokeColor = UIColor.Blue,
              Alpha = 0.4f,
              LineWidth = 9
          };
      }
      return polygonRenderer;
  }
}
```

#### <a name="creating-the-custom-renderer-on-android"></a>Creazione del renderer personalizzato in Android

Creare una sottoclasse della classe `MapRenderer` ed eseguire l'override dei metodi `OnElementChanged` e `OnMapReady` per aggiungere la sovrimpressione poligona:

```csharp
[assembly: ExportRenderer(typeof(CustomMap), typeof(CustomMapRenderer))]
namespace MapOverlay.Droid
{
    public class CustomMapRenderer : MapRenderer
    {
        List<Position> shapeCoordinates;

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
                shapeCoordinates = formsMap.ShapeCoordinates;
                Control.GetMapAsync(this);
            }
        }

        protected override void OnMapReady(Android.Gms.Maps.GoogleMap map)
        {
            base.OnMapReady(map);

            var polygonOptions = new PolygonOptions();
            polygonOptions.InvokeFillColor(0x66FF0000);
            polygonOptions.InvokeStrokeColor(0x660000FF);
            polygonOptions.InvokeStrokeWidth(30.0f);

            foreach (var position in shapeCoordinates)
            {
                polygonOptions.Add(new LatLng(position.Latitude, position.Longitude));
            }
            NativeMap.AddPolygon(polygonOptions);
        }
    }
}
```

Il metodo `OnElementChanged` recupera la raccolta delle coordinate di latitudine e longitudine dalla proprietà `CustomMap.ShapeCoordinates` e le archivia in una variabile membro. Il metodo chiama poi il metodo `MapView.GetMapAsync`, che ottiene l'oggetto sottostante `GoogleMap` associato alla visualizzazione, a condizione che il renderer personalizzato sia associato a un nuovo elemento di Xamarin.Forms. Quando l'istanza di `GoogleMap` è disponibile, verrà richiamato il metodo `OnMapReady`, in cui viene creato il poligono creando un'istanza dell'oggetto `PolygonOptions` che specifica la latitudine e longitudine di ogni punto. Il poligono poi viene aggiunto alla mappa chiamando il metodo `NativeMap.AddPolygon`. Questo metodo chiude automaticamente il poligono tracciando una linea che connette il primo e l'ultimo punto.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Creazione del renderer personalizzato sulla piattaforma UWP (Universal Windows Platform)

Creare una sottoclasse della classe `MapRenderer` ed eseguire l'override del metodo `OnElementChanged` per aggiungere la sovrimpressione poligona:

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
                foreach (var position in formsMap.ShapeCoordinates)
                {
                    coordinates.Add(new BasicGeoposition() { Latitude = position.Latitude, Longitude = position.Longitude });
                }

                var polygon = new MapPolygon();
                polygon.FillColor = Windows.UI.Color.FromArgb(128, 255, 0, 0);
                polygon.StrokeColor = Windows.UI.Color.FromArgb(128, 0, 0, 255);
                polygon.StrokeThickness = 5;
                polygon.Path = new Geopath(coordinates);
                nativeMap.MapElements.Add(polygon);
            }
        }
    }
}
```

Questo metodo esegue le operazioni seguenti, a condizione che il renderer personalizzato sia associato a un nuovo elemento di Xamarin.Forms:

- La raccolta di coordinate di latitudine e longitudine viene recuperata dalla proprietà `CustomMap.ShapeCoordinates` e convertita in un elenco (`List`) di coordinate `BasicGeoposition`.
- Il poligono viene creato creando un'istanza di un oggetto `MapPolygon`. La classe `MapPolygon` viene usata per visualizzare una forma multipunto sulla mappa mediante l'impostazione della relativa proprietà `Path` su un oggetto `Geopath` che contiene le coordinate della forma.
- Viene eseguito il rendering del poligono sulla mappa aggiungendolo alla raccolta `MapControl.MapElements`. Si noti che il poligono verrà chiuso automaticamente tracciando una linea che connette il primo e l'ultimo punto.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come aggiungere una sovrimpressione poligonale a una mappa per evidenziare un'area della mappa stessa. I poligoni sono forme chiuse con riempimento.

## <a name="related-links"></a>Collegamenti correlati

- [Polygon Map Overlay (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-map-polygon) (Sovrimpressione poligonale in una mappa)
- [Personalizzazione di una puntina per la mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
