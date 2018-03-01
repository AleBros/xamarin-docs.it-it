---
title: Le annotazioni e sovrapposizioni
description: "In questo articolo presenta una procedura dettagliata che illustra come utilizzare le funzionalità di annotazione e sovrapposizione del Kit di mappa. Viene illustrato come aggiungere una mappa a un'applicazione che visualizza un'annotazione e sovrapposizione nella posizione della conferenza Xamarin evolvere 2013."
ms.topic: article
ms.prod: xamarin
ms.assetid: 1BC4F7FC-AE3C-46D7-A4D3-18E142F55B8E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 7eea7231ec4300a368e4612cbed2ba4ebc044a26
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="annotations-and-overlays--walkthrough"></a>Le annotazioni e sovrapposizioni: procedura dettagliata

L'applicazione che si intende compilare in questa procedura dettagliata è illustrato di seguito:

 [![](ios-maps-walkthrough-images/00-map-overlay.png "Un'app di esempio MapKit")](ios-maps-walkthrough-images/00-map-overlay.png)
 
È possibile trovare il codice completato nel **MapsWalkthroughComplete** cartella all'interno di [esempio dimostrativo mappa](https://developer.xamarin.com/samples/monotouch/MapDemo/).

Si inizierà creando un nuovo **iOS progetto vuoto**e assegnargli un nome pertinente. Si inizierà con l'aggiunta di codice per il Controller di visualizzazione per visualizzare l'oggetto MapView e verranno quindi creare nuove classi per il nostro MapDelegate e annotazioni personalizzate. A questo scopo, seguire questa procedura:

## <a name="viewcontroller"></a>ViewController


1. Aggiungere i seguenti spazi dei nomi di `ViewController`:

    ```csharp
    using MapKit;
    using CoreLocation;
    using UIKit
    using CoreGraphics
    ```

1. Aggiungere un `MKMapView` istanza variabile alla classe, insieme a un `MapDelegate` istanza. Si creerà il `MapDelegate` a breve:

    ```csharp
    public partial class ViewController : UIViewController
    {
        MKMapView map;
        MapDelegate mapDelegate;
        ...
    ```

1. Il controller `LoadView` metodo, aggiungere un `MKMapView` e impostarlo sul `View` proprietà del controller:

    ```csharp
    public override void LoadView ()
    {
        map = new MKMapView (UIScreen.MainScreen.Bounds);
        View = map;
    }
    ```

    Successivamente, verrà aggiunto il codice per inizializzare la mappa nel ' ViewDidLoad ' (metodo).

1. In `ViewDidLoad` aggiungere il codice per impostare il tipo di mappa, indicare la posizione dell'utente e consentire lo zoom e traslazione:

    ```csharp
    // change map type, show user location and allow zooming and panning
    map.MapType = MKMapType.Standard;
    map.ShowsUserLocation = true;
    map.ZoomEnabled = true;
    map.ScrollEnabled = true;
    
    ```

1. Successivamente, aggiungere codice per allineare al centro la mappa e impostare l'area dell'it:

    ```csharp
    double lat = 30.2652233534254;
    double lon = -97.73815460962083;
    CLLocationCoordinate2D mapCenter = new CLLocationCoordinate2D (lat, lon);
    MKCoordinateRegion mapRegion = MKCoordinateRegion.FromDistance (mapCenter, 100, 100);
    map.CenterCoordinate = mapCenter;
    map.Region = mapRegion;
    
    ```

1. Creare una nuova istanza della `MapDelegate` e assegnarlo al `Delegate` del `MKMapView`. Nuovamente, ti invieremo un implcodeent il `MapDelegate` a breve:

    ```csharp
    mapDelegate = new MapDelegate ();
    map.Delegate = mapDelegate;     
    ```

1. A partire da iOS 8, è consigliabile richiede autorizzazione da parte dell'utente da utilizzare nei percorsi, questo punto aggiungere questo elemento per questo esempio. Prima di definire un `CLLocationManager` variabile a livello di classe:

    ```csharp
    CLLocationManager locationManager = new CLLocationManager();
    ```

1. Nel `ViewDidLoad` metodo, si desidera controllare se il dispositivo che esegue l'applicazione usa iOS 8, e se è si richiederà autorizzazione quando l'app è in uso:

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion(8,0)){
                    locationManager.RequestWhenInUseAuthorization ();
                }
    ```

1. Infine, è necessario modificare il **Info. plist** file avvisare gli utenti del motivo per la richiesta di posizione. Nel **origine** dal menu del **Info. plist**, aggiungere la chiave seguente:
    
    `NSLocationWhenInUseUsageDescription` 
    
    e stringa: 

    `Maps Demo`.


## <a name="conferenceannotationcs--a-class-for-custom-annotations"></a>ConferenceAnnotation.cs: una classe personalizzata di annotazioni


1. Si intende utilizzare una classe personalizzata per l'annotazione chiamato `ConferenceAnnotation`. Aggiungere la classe seguente al progetto:

    ```csharp
    using System;
    using CoreLocation;
    using MapKit;
    
    namespace MapDemo
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

## <a name="viewcontroller---adding-the-annotation-and-overlay"></a>ViewController - aggiunta di annotazione e sovrapposizione

1. Con il `ConferenceAnnotation` sul posto è possibile aggiungerla alla mappa. Nel `ViewDidLoad` metodo il `ViewController`, aggiungere l'annotazione in coordinate center della mappa:

    ```csharp
    map.AddAnnotations (new ConferenceAnnotation ("Evolve Conference", mapCenter)); 
    ```

1. È inoltre possibile hanno una sovrapposizione di hotel. Aggiungere il codice seguente per creare il `MKPolygon` utilizzando le coordinate per hotel fornito e aggiungerla alla mappa da chiamata `AddOverlay`:

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
Questo passaggio completa il codice in `ViewDidLoad`. Ora è necessario implementare la `MapDelegate` classe per gestire la creazione di annotazione e viste in sovraimpressione rispettivamente.


## <a name="mapdelegate"></a>MapDelegate

1. Creare una classe denominata `MapDelegate` che eredita da `MKMapViewDelegate` e includere un `annotationId` variabile da utilizzare come identificatore riutilizzo per l'annotazione:

    ```csharp
    class MapDelegate : MKMapViewDelegate
    {
        static string annotationId = "ConferenceAnnotation";
        ...
    }
    ```
    Solo abbiamo una sola annotazione qui per consentire il riutilizzo di codice non è strettamente necessario, ma è consigliabile includerlo.

1. Implementare il `GetViewForAnnotation` per restituire una visualizzazione per il `ConferenceAnnotation` utilizzando il **conference.png** immagine incluso in questa procedura dettagliata:

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

1. Quando l'utente tocca sull'annotazione, si vuole visualizzare un'immagine che mostra le città di Austin. Aggiungere le seguenti variabili per il `MapDelegate` per l'immagine e la visualizzazione per visualizzarlo:

    ```csharp
    UIImageView venueView;
    UIImage venueImage;
    ```

1. Successivamente, per visualizzare l'immagine quando verrà scelti l'annotazione, implementare il `DidSelectAnnotation` metodo come indicato di seguito:

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
    È ora disponibile il codice per l'annotazione sul posto. Tutto ciò che resta consiste nell'aggiungere codice per il `MapDelegate` per creare la visualizzazione per la sovrimpressione hotel.

1. Aggiungere la seguente implementazione di `GetViewForOverlay` per il `MapDelegate`:

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

Eseguire l'applicazione. È ora disponibile una mappa interattiva con un'annotazione di tipo personalizzata e una sovrapposizione! Toccare l'annotazione e viene visualizzata l'immagine di Austin, come illustrato di seguito:

 [![](ios-maps-walkthrough-images/01-map-image.png "Toccare l'annotazione e viene visualizzata l'immagine di Austin")](ios-maps-walkthrough-images/01-map-image.png)

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come aggiungere un'annotazione a una mappa, nonché come aggiungere una sovrapposizione per un poligono specificato. È inoltre illustrato come aggiungere supporto per il tocco per l'annotazione per animare un'immagine su una mappa.


## <a name="related-links"></a>Collegamenti correlati

- [Demo di mappa (esempio)](https://developer.xamarin.com/samples/monotouch/MapDemo/)
- [Mappe di iOS](~/ios/user-interface/controls/ios-maps/index.md)
