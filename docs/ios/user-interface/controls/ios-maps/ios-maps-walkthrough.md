---
title: Annotazioni e sovrimpressioni in Novell. iOS
description: Questo articolo presenta una procedura dettagliata che illustra come usare le funzionalità di annotazione e sovrapposizione del kit mappa. Viene illustrato come aggiungere una mappa a un'applicazione che visualizza un'annotazione e una sovrapposizione nella posizione della conferenza Novell evolve 2013.
ms.prod: xamarin
ms.assetid: 1BC4F7FC-AE3C-46D7-A4D3-18E142F55B8E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 7e9010eb579f28e62b5f7ab72ac061e9898e7ecf
ms.sourcegitcommit: a9b180651863cb7da31d3af14182fe3ad44796f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2020
ms.locfileid: "78292294"
---
# <a name="annotations-and-overlays-in-xamarinios"></a>Annotazioni e sovrimpressioni in Novell. iOS

L'applicazione che verrà compilata in questa procedura dettagliata è illustrata di seguito:

 [![](ios-maps-walkthrough-images/00-map-overlay.png "An example MapKit app")](ios-maps-walkthrough-images/00-map-overlay.png#lightbox)

È possibile trovare il codice completato nell' [esempio di procedura dettagliata Maps](https://docs.microsoft.com/samples/xamarin/ios-samples/mapswalkthrough).

Si inizia creando un nuovo **progetto iOS vuoto**e dandogli un nome pertinente. Per iniziare, aggiungere il codice al controller di visualizzazione per visualizzare il MapView e quindi creare nuove classi per il MapDelegate e le annotazioni personalizzate. A questo scopo, seguire questa procedura:

## <a name="viewcontroller"></a>ViewController

1. Aggiungere gli spazi dei nomi seguenti al `ViewController`:

    ```csharp
    using MapKit;
    using CoreLocation;
    using UIKit
    using CoreGraphics
    ```

1. Aggiungere una variabile di istanza `MKMapView` alla classe insieme a un'istanza di `MapDelegate`. Il `MapDelegate` verrà creato a breve:

    ```csharp
    public partial class ViewController : UIViewController
    {
        MKMapView map;
        MapDelegate mapDelegate;
        ...
    ```

1. Nel metodo `LoadView` del controller aggiungere una `MKMapView` e impostarla sulla proprietà `View` del controller:

    ```csharp
    public override void LoadView ()
    {
        map = new MKMapView (UIScreen.MainScreen.Bounds);
        View = map;
    }
    ```

    Successivamente, verrà aggiunto il codice per inizializzare la mappa nel metodo ' ViewDidLoad ''.

1. In `ViewDidLoad` aggiungere il codice per impostare il tipo di mappa, visualizzare il percorso utente e consentire lo zoom e la panoramica:

    ```csharp
    // change map type, show user location and allow zooming and panning
    map.MapType = MKMapType.Standard;
    map.ShowsUserLocation = true;
    map.ZoomEnabled = true;
    map.ScrollEnabled = true;

    ```

1. Successivamente, aggiungere il codice per centrare la mappa e impostarne l'area:

    ```csharp
    double lat = 30.2652233534254;
    double lon = -97.73815460962083;
    CLLocationCoordinate2D mapCenter = new CLLocationCoordinate2D (lat, lon);
    MKCoordinateRegion mapRegion = MKCoordinateRegion.FromDistance (mapCenter, 100, 100);
    map.CenterCoordinate = mapCenter;
    map.Region = mapRegion;

    ```

1. Creare una nuova istanza di `MapDelegate` e assegnarla alla `Delegate` dell'`MKMapView`. Anche in questo caso, la `MapDelegate` verrà implementata a breve:

    ```csharp
    mapDelegate = new MapDelegate ();
    map.Delegate = mapDelegate;
    ```

1. A partire da iOS 8, è necessario richiedere l'autorizzazione all'utente per usare la propria posizione, quindi aggiungere questo esempio al nostro esempio. Definire innanzitutto una variabile a livello di classe `CLLocationManager`:

    ```csharp
    CLLocationManager locationManager = new CLLocationManager();
    ```

1. Nel metodo `ViewDidLoad` si vuole verificare se il dispositivo che esegue l'applicazione usa iOS 8 e, in caso contrario, verrà richiesta l'autorizzazione quando l'app è in uso:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion(8,0)){
        locationManager.RequestWhenInUseAuthorization ();
    }
    ```

1. Infine, è necessario modificare il file **info. plist** per informare gli utenti del motivo della richiesta della loro posizione. Nel menu **origine** di **info. plist**aggiungere la chiave seguente:

    `NSLocationWhenInUseUsageDescription`

    e stringa:

    `Maps Walkthrough Docs Sample`.

## <a name="conferenceannotationcs--a-class-for-custom-annotations"></a>ConferenceAnnotation.cs: classe per le annotazioni personalizzate

1. Verrà usata una classe personalizzata per l'annotazione denominata `ConferenceAnnotation`. Aggiungere la classe seguente al progetto:

    ```csharp
    using System;
    using CoreLocation;
    using MapKit;

    namespace MapsWalkthrough
    {
        public class ConferenceAnnotation : MKAnnotation
        {
            string title;
            CLLocationCoordinate2D coord;

            public ConferenceAnnotation (string title,
            CLLocationCoordinate2D coord)
            {
                this.title = title;
                this.coord = coord;
            }

            public override string Title {
                get {
                    return title;
                }
            }

            public override CLLocationCoordinate2D Coordinate {
                get {
                    return coord;
                }
            }
        }
    }
    ```

## <a name="viewcontroller---adding-the-annotation-and-overlay"></a>ViewController-aggiunta dell'annotazione e della sovrimpressione

1. Con il `ConferenceAnnotation` sul posto è possibile aggiungerlo alla mappa. Tornare al metodo `ViewDidLoad` della `ViewController`, aggiungere l'annotazione in corrispondenza della coordinata centrale della mappa:

    ```csharp
    map.AddAnnotations (new ConferenceAnnotation ("Evolve Conference", mapCenter));
    ```

1. Si vuole anche avere una sovrapposizione dell'hotel. Aggiungere il codice seguente per creare il `MKPolygon` usando le coordinate per l'Hotel specificato e aggiungerlo alla mappa tramite Call `AddOverlay`:

    ```csharp
    // add an overlay of the hotel
    MKPolygon hotelOverlay = MKPolygon.FromCoordinates(
        new CLLocationCoordinate2D[]{
        new CLLocationCoordinate2D(30.2649977168594, -97.73863627705),
        new CLLocationCoordinate2D(30.2648461170005, -97.7381627734755),
        new CLLocationCoordinate2D(30.2648355402574, -97.7381750192576),
        new CLLocationCoordinate2D(30.2647791309417, -97.7379872505988),
        new CLLocationCoordinate2D(30.2654525150319, -97.7377341711021),
        new CLLocationCoordinate2D(30.2654807195004, -97.7377994819399),
        new CLLocationCoordinate2D(30.2655089239607, -97.7377994819399),
        new CLLocationCoordinate2D(30.2656428950368, -97.738346460207),
        new CLLocationCoordinate2D(30.2650364981811, -97.7385709662122),
        new CLLocationCoordinate2D(30.2650470749025, -97.7386199493406)
    });

    map.AddOverlay (hotelOverlay);
    ```

Il codice è stato completato in `ViewDidLoad`. A questo punto è necessario implementare la classe `MapDelegate` per gestire rispettivamente la creazione di annotazioni e visualizzazioni sovrapposte.

## <a name="mapdelegate"></a>MapDelegate

1. Creare una classe denominata `MapDelegate` che erediti da `MKMapViewDelegate` e includa una variabile `annotationId` da usare come identificatore di riutilizzo per l'annotazione:

    ```csharp
    class MapDelegate : MKMapViewDelegate
    {
        static string annotationId = "ConferenceAnnotation";
        ...
    }
    ```

    Qui abbiamo una sola annotazione, quindi il codice di riutilizzo non è strettamente necessario, ma è consigliabile includerlo.

1. Implementare il metodo `GetViewForAnnotation` per restituire una visualizzazione per il `ConferenceAnnotation` utilizzando l'immagine **Conference. png** inclusa in questa procedura dettagliata:

    ```csharp
    public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
    {
        MKAnnotationView annotationView = null;

        if (annotation is MKUserLocation)
            return null;

        if (annotation is ConferenceAnnotation) {

            // show conference annotation
            annotationView = mapView.DequeueReusableAnnotation (annotationId);

            if (annotationView == null)
                annotationView = new MKAnnotationView (annotation, annotationId);

            annotationView.Image = UIImage.FromFile ("images/conference.png");
            annotationView.CanShowCallout = true;
        }

        return annotationView;
    }
    ```

1. Quando l'utente tocca l'annotazione, si vuole visualizzare un'immagine che mostra la città di Austin. Aggiungere le variabili seguenti al `MapDelegate` per l'immagine e la visualizzazione per visualizzarla:

    ```csharp
    UIImageView venueView;
    UIImage venueImage;
    ```

1. Quindi, per visualizzare l'immagine quando l'annotazione viene toccata, implementare il metodo `DidSelectAnnotation` come indicato di seguito:

    ```csharp
    public override void DidSelectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // show an image view when the conference annotation view is selected
        if (view.Annotation is ConferenceAnnotation) {

            venueView = new UIImageView ();
            venueView.ContentMode = UIViewContentMode.ScaleAspectFit;
            venueImage = UIImage.FromFile ("image/venue.png");
            venueView.Image = venueImage;
            view.AddSubview (venueView);

            UIView.Animate (0.4, () => {
            venueView.Frame = new CGRect (-75, -75, 200, 200); });
        }
    }
    ```

1. Per nascondere l'immagine quando l'utente deseleziona l'annotazione toccando qualsiasi altra posizione sulla mappa, implementare il `DidDeselectAnnotationView` metodo come segue:

    ```csharp
    public override void DidDeselectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // remove the image view when the conference annotation is deselected
        if (view.Annotation is ConferenceAnnotation) {

            venueView.RemoveFromSuperview ();
            venueView.Dispose ();
            venueView = null;
        }
    }
    ```

    A questo punto è disponibile il codice per l'annotazione. È necessario aggiungere il codice alla `MapDelegate` per creare la visualizzazione per la sovrimpressione dell'hotel.

1. Aggiungere la seguente implementazione di `GetViewForOverlay` al `MapDelegate`:

    ```csharp
    public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
    {
        // return a view for the polygon
        MKPolygon polygon = overlay as MKPolygon;
        MKPolygonView polygonView = new MKPolygonView (polygon);
        polygonView.FillColor = UIColor.Blue;
        polygonView.StrokeColor = UIColor.Red;
        return polygonView;
    }
    ```

Eseguire l'applicazione. È ora disponibile una mappa interattiva con un'annotazione personalizzata e una sovrimpressione. Toccare l'annotazione e viene visualizzata l'immagine di Austin, come illustrato di seguito:

 [![](ios-maps-walkthrough-images/01-map-image.png "Tap on the annotation and the image of Austin is displayed")](ios-maps-walkthrough-images/01-map-image.png#lightbox)

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come aggiungere un'annotazione a una mappa e come aggiungere una sovrapposizione per un poligono specificato. È stato inoltre illustrato come aggiungere il supporto tocco all'annotazione per animare un'immagine su una mappa.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di procedura dettagliata Maps](https://docs.microsoft.com/samples/xamarin/ios-samples/mapswalkthrough)
- [Esempio di demo mappa](https://docs.microsoft.com/samples/xamarin/ios-samples/mapdemo)
- [Mappe iOS](~/ios/user-interface/controls/ios-maps/index.md)
