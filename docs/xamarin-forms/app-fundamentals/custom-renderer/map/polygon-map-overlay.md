---
title: Evidenziare una regione su una mappa
description: Questo articolo illustra come aggiungere una sovrapposizione di poligoni a una mappa, per evidenziare un'area sulla mappa. I poligoni sono una forma chiusa e le parti interne compilato.
ms.prod: xamarin
ms.assetid: E79EB2CF-8DD6-44A8-B47D-5F0A94FB0A63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 0a11e9c25922531727ad2fee3bbed9c8d4e2b80c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998134"
---
# <a name="highlighting-a-region-on-a-map"></a>Evidenziare una regione su una mappa

_Questo articolo ha illustrato come aggiungere una sovrapposizione di poligoni a una mappa, per evidenziare un'area sulla mappa. I poligoni sono una forma chiusa e le parti interne compilato._

## <a name="overview"></a>Panoramica

Un controllo overlay è un'immagine a più livelli in una mappa. Le sovrimpressioni supportano disegno contenuto grafico che viene ridimensionata con la mappa come si esegue lo zoom. Gli screenshot seguenti mostrano il risultato dell'aggiunta di una sovrimpressione di poligoni a una mappa:

![](polygon-map-overlay-images/screenshots.png)

Quando un [ `Map` ](xref:Xamarin.Forms.Maps.Map) rendering del controllo da un'applicazione xamarin. Forms, in iOS il `MapRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza di nativo `MKMapView` controllo. La piattaforma Android, il `MapRenderer` un'istanza nativa `MapView` controllo. In Universal Windows Platform (UWP), il `MapRenderer` un'istanza nativa `MapControl`. Il processo di rendering possibile avvantaggiarsi di implementare le personalizzazioni specifiche della piattaforma mappe mediante la creazione di un renderer personalizzato per un `Map` in ogni piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_Map) una mappa personalizzata di xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Map) la mappa personalizzata da xamarin. Forms.
1. [Personalizzare](#Customizing_the_Map) mappa mediante la creazione di un renderer personalizzato per la mappa su ogni piattaforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](xref:Xamarin.Forms.Maps) deve essere inizializzato e configurato prima dell'uso. Per altre informazioni, vedere [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Per informazioni sulla personalizzazione di una mappa usando un renderer personalizzato, vedere [personalizzazione di un Pin di mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Creazione della mappa personalizzata

Creare una sottoclasse del [ `Map` ](xref:Xamarin.Forms.Maps.Map) (classe), che aggiunge un `ShapeCoordinates` proprietà:

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

Il `ShapeCoordinates` proprietà archivierà una raccolta di coordinate che definiscono la regione viene evidenziata.

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
    customMap.ShapeCoordinates.Add (new Position (37.797513, -122.402058));
    customMap.ShapeCoordinates.Add (new Position (37.798433, -122.402256));
    customMap.ShapeCoordinates.Add (new Position (37.798582, -122.401071));
    customMap.ShapeCoordinates.Add (new Position (37.797658, -122.400888));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (0.1)));
  }
}
```

Questa inizializzazione specifica una serie di coordinate di latitudine e longitudine, per definire l'area della mappa da evidenziare. Quindi posiziona la visualizzazione della mappa con il [ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion*) metodo, che modifica la posizione e livello di zoom della mappa mediante la creazione di un [ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan) da un [ `Position` ](xref:Xamarin.Forms.Maps.Position) e una [ `Distance` ](xref:Xamarin.Forms.Maps.Distance).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizzazione della mappa

Un renderer personalizzato deve ora essere aggiunti a ogni progetto di applicazione per aggiungere la sovrimpressione di polygon alla mappa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Creare il Renderer personalizzati in iOS

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` metodo per aggiungere l'immagine sovrapposta al poligono:

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

Questo metodo esegue la configurazione seguente, a condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms:

- Il `MKMapView.OverlayRenderer` è impostata su un delegato corrispondente.
- La raccolta di coordinate di latitudine e longitudine vengono recuperati dal `CustomMap.ShapeCoordinates` proprietà e archiviati come matrice di `CLLocationCoordinate2D` istanze.
- Il poligono è stato creato chiamando il metodo statico `MKPolygon.FromCoordinates` metodo, che specifica la latitudine e longitudine di ogni punto.
- Il poligono viene aggiunto alla mappa chiamando il `MKMapView.AddOverlay` (metodo). Questo metodo deve essere chiuso automaticamente poligono tracciando una linea che connette il primo e ultimo punto.

Implementare poi il `GetOverlayRenderer` metodo per personalizzare il rendering della sovrimpressione:

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

#### <a name="creating-the-custom-renderer-on-android"></a>Creare il Renderer personalizzati in Android

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` e `OnMapReady` metodi per aggiungere l'immagine sovrapposta al poligono:

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

Il `OnElementChanged` metodo recupera l'insieme di coordinate di latitudine e longitudine dal `CustomMap.ShapeCoordinates` proprietà e li archivia in una variabile membro. Chiama poi il `MapView.GetMapAsync` metodo, che ottiene l'oggetto sottostante `GoogleMap` che è associato alla visualizzazione, condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms. Una volta il `GoogleMap` istanza è disponibile, il `OnMapReady` metodo verrà richiamato, in cui viene creato il poligono dalla creazione di istanze un `PolygonOptions` oggetto che specifica la latitudine e longitudine di ogni punto. Il poligono viene quindi aggiunto alla mappa chiamando il `NativeMap.AddPolygon` (metodo). Questo metodo deve essere chiuso automaticamente poligono tracciando una linea che connette il primo e ultimo punto.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Creare il Renderer personalizzato sulla piattaforma Windows universale

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` metodo per aggiungere l'immagine sovrapposta al poligono:

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

Questo metodo esegue le operazioni seguenti, a condizione che il renderer personalizzato è allegato a un nuovo elemento di xamarin. Forms:

- La raccolta di coordinate di latitudine e longitudine vengono recuperati dal `CustomMap.ShapeCoordinates` proprietà e convertita in un `List` di `BasicGeoposition` coordinate.
- Il poligono viene creato creando un `MapPolygon` oggetto. Il `MapPolygon` classe viene utilizzata per visualizzare una forma di multi-punto sulla mappa mediante l'impostazione relativa `Path` proprietà su un `Geopath` oggetto che contiene le coordinate di forma.
- Il poligono viene eseguito il rendering sulla mappa, aggiungerlo al `MapControl.MapElements` raccolta. Si noti che il poligono verrà chiusi automaticamente tracciando una linea che connette il primo e ultimo punto.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come aggiungere una sovrapposizione di poligoni a una mappa, per evidenziare un'area della mappa. I poligoni sono una forma chiusa e le parti interne compilato.


## <a name="related-links"></a>Collegamenti correlati

- [Poligono della mappa della sovrimpressione (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polygon/)
- [Personalizzazione di una puntina per la mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](xref:Xamarin.Forms.Maps)
