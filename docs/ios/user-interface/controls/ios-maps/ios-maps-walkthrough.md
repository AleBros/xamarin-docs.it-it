---
title: Le annotazioni e sovrimpressioni in xamarin. IOS
description: Questo articolo presenta una procedura dettagliata che illustra come usare le funzionalità di annotazione e sovrimpressione del Framework Map Kit. Viene illustrato come aggiungere una mappa a un'applicazione che visualizza un'annotazione e sovrimpressione in corrispondenza della posizione della conferenza 2013 evolvere Xamarin.
ms.prod: xamarin
ms.assetid: 1BC4F7FC-AE3C-46D7-A4D3-18E142F55B8E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 445661513b0cf79df99d54ed0bb4b0261dd75c2a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61381488"
---
# <a name="annotations-and-overlays-in-xamarinios"></a>Le annotazioni e sovrimpressioni in xamarin. IOS

L'applicazione che si intende compilare in questa procedura dettagliata è illustrato di seguito:

 [![](ios-maps-walkthrough-images/00-map-overlay.png "Esempio di app MapKit")](ios-maps-walkthrough-images/00-map-overlay.png#lightbox)
 
È possibile trovare il codice completato nei [esempio di procedura dettagliata di mappe](https://developer.xamarin.com/samples/monotouch/MapsWalkthrough/).

È innanzitutto necessario creare una nuova **iOS progetto vuoto**e assegnargli un nome rilevante. Si inizierà aggiungendo codice al nostro Controller di visualizzazione per visualizzare l'oggetto MapView e verranno quindi creare nuove classi per i nostri MapDelegate e le annotazioni personalizzate. A questo scopo, seguire questa procedura:

## <a name="viewcontroller"></a>ViewController


1. Aggiungere i seguenti spazi dei nomi di `ViewController`:

    ```csharp
    using MapKit;
    using CoreLocation;
    using UIKit
    using CoreGraphics
    ```

1. Aggiungere un `MKMapView` istanza variabile alla classe, insieme a un `MapDelegate` istanza. Creeremo il `MapDelegate` poco:

    ```csharp
    public partial class ViewController : UIViewController
    {
        MKMapView map;
        MapDelegate mapDelegate;
        ...
    ```

1. Del controller `LoadView` metodo, aggiungere un' `MKMapView` e impostarlo sul `View` proprietà del controller:

    ```csharp
    public override void LoadView ()
    {
        map = new MKMapView (UIScreen.MainScreen.Bounds);
        View = map;
    }
    ```

    Successivamente, si aggiungerà codice per inizializzare l'oggetto map nel ' ViewDidLoad ' (metodo).

1. In `ViewDidLoad` aggiungere codice per impostare il tipo di mappa, Mostra la posizione dell'utente e consentire lo zoom e panoramiche:

    ```csharp
    // change map type, show user location and allow zooming and panning
    map.MapType = MKMapType.Standard;
    map.ShowsUserLocation = true;
    map.ZoomEnabled = true;
    map.ScrollEnabled = true;
    
    ```

1. Successivamente, aggiungere codice per centrare la mappa e impostare l'area dell'it:

    ```csharp
    double lat = 30.2652233534254;
    double lon = -97.73815460962083;
    CLLocationCoordinate2D mapCenter = new CLLocationCoordinate2D (lat, lon);
    MKCoordinateRegion mapRegion = MKCoordinateRegion.FromDistance (mapCenter, 100, 100);
    map.CenterCoordinate = mapCenter;
    map.Region = mapRegion;
    
    ```

1. Creare una nuova istanza della `MapDelegate` e assegnarlo al `Delegate` del `MKMapView`. Anche in questo caso, ti invieremo un implcodeent il `MapDelegate` poco:

    ```csharp
    mapDelegate = new MapDelegate ();
    map.Delegate = mapDelegate;     
    ```

1. A partire da iOS 8, si dovrebbe essere richiedendo autorizzazione da parte dell'utente da usare la loro posizione, quindi, aggiungiamo questo al nostro esempio. In primo luogo, definire un `CLLocationManager` variabile a livello di classe:

    ```csharp
    CLLocationManager locationManager = new CLLocationManager();
    ```

1. Nel `ViewDidLoad` metodo, si desidera controllare se il dispositivo che esegue l'applicazione usa iOS 8 e se è ti inviteremo autorizzazione quando l'app è in uso:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion(8,0)){
                    locationManager.RequestWhenInUseAuthorization ();
                }
    ```

1. Infine, è necessario modificare il **Info. plist** file consigliare agli utenti del motivo per la richiesta di posizione. Nel **origine** dal menu delle **Info. plist**, aggiungere la chiave seguente:
    
    `NSLocationWhenInUseUsageDescription` 
    
    e stringa: 

    `Maps Walkthrough Docs Sample`.


## <a name="conferenceannotationcs--a-class-for-custom-annotations"></a>ConferenceAnnotation.cs: una classe per le annotazioni personalizzate


1. Si userà una classe personalizzata per l'annotazione denominata `ConferenceAnnotation`. Aggiungere la seguente classe al progetto:

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

## <a name="viewcontroller---adding-the-annotation-and-overlay"></a>ViewController - aggiunta dell'annotazione e sovrapposizione

1. Con la `ConferenceAnnotation` posto è possibile aggiungerla alla mappa. Nel `ViewDidLoad` metodo del `ViewController`, aggiungere l'annotazione in coordinate di centro della mappa:

    ```csharp
    map.AddAnnotations (new ConferenceAnnotation ("Evolve Conference", mapCenter)); 
    ```

1. Vogliamo anche disporre di una sovrimpressione dell'hotel. Aggiungere il codice seguente per creare il `MKPolygon` usando le coordinate per l'hotel fornito e aggiungerlo alla mappa da chiamata `AddOverlay`:

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
Questo passaggio completa il codice in `ViewDidLoad`. È ora necessario implementare il `MapDelegate` classe per la gestione della creazione dell'annotazione e sovrapporre rispettivamente le viste.


## <a name="mapdelegate"></a>MapDelegate

1. Creare una classe denominata `MapDelegate` che eredita da `MKMapViewDelegate` e includere un `annotationId` variabile da utilizzare come identificatore di riutilizzo per l'annotazione:

    ```csharp
    class MapDelegate : MKMapViewDelegate
    {
        static string annotationId = "ConferenceAnnotation";
        ...
    }
    ```
    È sufficiente una sola annotazione qui in modo che il riutilizzo di codice non è strettamente necessario, ma è consigliabile includerlo.

1. Implementare il `GetViewForAnnotation` per restituire una visualizzazione per il `ConferenceAnnotation` usando la **conference.png** immagine inclusi in questa procedura dettagliata:

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

1. Quando l'utente tocca sull'annotazione, si vuole visualizzare un'immagine che mostra la città di Austin. Aggiungere le variabili seguenti per il `MapDelegate` per l'immagine e la vista per visualizzarlo:

    ```csharp
    UIImageView venueView;
    UIImage venueImage;
    ```

1. Successivamente, per visualizzare l'immagine quando viene toccata l'annotazione, implementare il `DidSelectAnnotation` metodo come indicato di seguito:

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

1. Per nascondere l'immagine quando l'utente deseleziona l'annotazione toccando altrove sulla mappa, implementare il `DidSelectAnnotationView` metodo come indicato di seguito:

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
    È ora disponibile il codice per l'annotazione posto. Tutto ciò che resta consiste nell'aggiungere codice per il `MapDelegate` per creare la visualizzazione per la sovrimpressione hotel.

1. Aggiungere l'implementazione seguente del `GetViewForOverlay` per il `MapDelegate`:

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

Eseguire l'applicazione. Ora si dispone di una mappa interattiva con un'annotazione personalizzata e un controllo overlay. Toccare sull'annotazione e viene visualizzata l'immagine di Austin, come illustrato di seguito:

 [![](ios-maps-walkthrough-images/01-map-image.png "Toccare sull'annotazione e viene visualizzata l'immagine di Austin")](ios-maps-walkthrough-images/01-map-image.png#lightbox)

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come aggiungere un'annotazione a una mappa, nonché come aggiungere un overlay per un poligono specificato. È stato anche illustrato come aggiungere supporto per il tocco per l'annotazione per aggiungere un'animazione di un'immagine su una mappa.


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di questa procedura dettagliata di mappe](https://developer.xamarin.com/samples/monotouch/MapsWalkthrough/)
- [Eseguire il mapping di esempio dimostrativo](https://developer.xamarin.com/samples/monotouch/MapDemo/)
- [Mappe iOS](~/ios/user-interface/controls/ios-maps/index.md)
