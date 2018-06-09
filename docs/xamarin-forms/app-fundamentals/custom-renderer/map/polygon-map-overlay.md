---
title: Evidenziare una regione su una mappa
description: In questo articolo viene illustrato come aggiungere una sovrapposizione di poligono a una mappa, per evidenziare un'area sulla mappa. Poligoni sono una forma chiusa e le parti interne compilato.
ms.prod: xamarin
ms.assetid: E79EB2CF-8DD6-44A8-B47D-5F0A94FB0A63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: b38ff41415477a8898ee3bd9593f983c705d949e
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241790"
---
# <a name="highlighting-a-region-on-a-map"></a>Evidenziare una regione su una mappa

_Questo articolo ha descritto come aggiungere una sovrapposizione di poligono a una mappa, per evidenziare un'area sulla mappa. Poligoni sono una forma chiusa e le parti interne compilato._

## <a name="overview"></a>Panoramica

Sovrapposizione è un'immagine a più livelli in una mappa. Sovrapposizioni supportano disegno del contenuto con interfaccia grafica che consente una scalabilità con la mappa come si esegue lo zoom. Le schermate seguenti mostrano il risultato dell'aggiunta di una sovrapposizione di poligono a una mappa:

![](polygon-map-overlay-images/screenshots.png)

Quando un [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) rendering del controllo da un'applicazione di xamarin. Forms, in iOS il `MapRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza nativa `MKMapView` controllo. Nella piattaforma Android, il `MapRenderer` un'istanza nativa `MapView` controllo. Nel Windows piattaforma UWP (Universal), il `MapRenderer` un'istanza nativa `MapControl`. Il processo di rendering può essere sfruttato per implementare le personalizzazioni specifiche della piattaforma mappa creando un renderer personalizzato per un `Map` in ciascuna piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_Map) una mappa personalizzata xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Map) la mappa personalizzata da xamarin. Forms.
1. [Personalizzare](#Customizing_the_Map) la mappa mediante la creazione di un renderer personalizzato per la mappa in ogni piattaforma.

> [!NOTE]
> [`Xamarin.Forms.Maps`](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/) deve essere inizializzato e configurato prima dell'uso. Per altre informazioni, vedere [`Maps Control`](~/xamarin-forms/user-interface/map.md).

Per informazioni sulla personalizzazione di una mappa utilizzando un renderer personalizzato, vedere [personalizzazione di un Pin mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md).

<a name="Creating_the_Custom_Map" />

### <a name="creating-the-custom-map"></a>Creazione della mappa personalizzata

Creare una sottoclasse di [ `Map` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Map/) (classe), che aggiunge un `ShapeCoordinates` proprietà:

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

Il `ShapeCoordinates` proprietà archivierà una raccolta di coordinate che definiscono l'area viene evidenziata.

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
    customMap.ShapeCoordinates.Add (new Position (37.797513, -122.402058));
    customMap.ShapeCoordinates.Add (new Position (37.798433, -122.402256));
    customMap.ShapeCoordinates.Add (new Position (37.798582, -122.401071));
    customMap.ShapeCoordinates.Add (new Position (37.797658, -122.400888));

    customMap.MoveToRegion (MapSpan.FromCenterAndRadius (new Position (37.79752, -122.40183), Distance.FromMiles (0.1)));
  }
}
```

L'inizializzazione specifica una serie di coordinate di latitudine e longitudine, per definire l'area della mappa viene evidenziata. Viene quindi posizionato visualizzazione della mappa con la [ `MoveToRegion` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan)/) (metodo), che modifica la posizione e il livello di zoom della mappa creando un [ `MapSpan` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.MapSpan/) da un [ `Position` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Position/) e [ `Distance` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Maps.Distance/).

<a name="Customizing_the_Map" />

### <a name="customizing-the-map"></a>Personalizzazione della mappa

Un renderer personalizzato deve ora essere aggiunto a ogni progetto di applicazione per aggiungere la sovrapposizione di poligono della mappa.

#### <a name="creating-the-custom-renderer-on-ios"></a>Creazione di Renderer personalizzato in iOS

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` metodo per aggiungere la sovrapposizione di poligono:

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

Questo metodo esegue la configurazione seguente, a condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms:

- Il `MKMapView.OverlayRenderer` è impostata su un delegato corrispondente.
- La raccolta di coordinate di latitudine e longitudine vengono recuperati i `CustomMap.ShapeCoordinates` proprietà e archiviata come una matrice di `CLLocationCoordinate2D` istanze.
- Il poligono viene creato chiamando il metodo statico `MKPolygon.FromCoordinates` metodo, che specifica la latitudine e longitudine di ogni punto.
- Il poligono viene aggiunto alla mappa chiamando il `MKMapView.AddOverlay` metodo. Questo metodo chiude automaticamente il poligono tracciando una linea che connette il primo e ultimo punto.

Implementare quindi il `GetOverlayRenderer` metodo per personalizzare il rendering della sovrimpressione:

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

#### <a name="creating-the-custom-renderer-on-android"></a>Creazione di Renderer personalizzato in Android

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` e `OnMapReady` metodi per aggiungere la sovrapposizione di poligono:

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

Il `OnElementChanged` consente di recuperare la raccolta delle coordinate di latitudine e longitudine dal `CustomMap.ShapeCoordinates` proprietà e li archivia in una variabile membro. Chiama quindi il `MapView.GetMapAsync` metodo, che ottiene l'oggetto sottostante `GoogleMap` che è correlato alla vista, a condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms. Una volta il `GoogleMap` istanza è disponibile, il `OnMapReady` verrà richiamato metodo, in cui viene creato il poligono creando un `PolygonOptions` oggetto che specifica la latitudine e longitudine di ogni punto. Il poligono viene quindi aggiunto alla mappa chiamando il `NativeMap.AddPolygon` metodo. Questo metodo chiude automaticamente il poligono tracciando una linea che connette il primo e ultimo punto.

#### <a name="creating-the-custom-renderer-on-the-universal-windows-platform"></a>Creazione di Renderer personalizzato nella piattaforma Windows universale

Creare una sottoclasse del `MapRenderer` classe ed eseguire l'override relativo `OnElementChanged` metodo per aggiungere la sovrapposizione di poligono:

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

Questo metodo esegue le operazioni seguenti, a condizione che il renderer personalizzato è collegato a un nuovo elemento di xamarin. Forms:

- La raccolta di coordinate di latitudine e longitudine vengono recuperati il `CustomMap.ShapeCoordinates` proprietà e convertito in un `List` di `BasicGeoposition` coordinate.
- Viene creato il poligono creando un `MapPolygon` oggetto. Il `MapPolygon` classe viene utilizzata per visualizzare una forma multipunto sulla mappa impostando il relativo `Path` proprietà per un `Geopath` oggetto che contiene le coordinate della forma.
- Il poligono viene eseguito il rendering della mappa aggiungendolo al `MapControl.MapElements` insieme. Si noti che il poligono verrà chiusi automaticamente tracciando una linea che connette il primo e ultimo punto.

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come aggiungere una sovrapposizione di poligono a una mappa, per evidenziare un'area della mappa. Poligoni sono una forma chiusa e le parti interne compilato.


## <a name="related-links"></a>Collegamenti correlati

- [Poligono mappa sovrapposizione (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/map/polygon/)
- [Personalizzazione di una puntina per la mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)
- [Xamarin.Forms.Maps](https://developer.xamarin.com/api/namespace/Xamarin.Forms.Maps/)
