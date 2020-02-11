---
title: Nuove funzionalità di MapKit in iOS 11
description: 'Questo documento descrive le nuove funzionalità di MapKit in iOS 11: marcatori di raggruppamento, pulsante bussola, visualizzazione scala e pulsante rilevamento utente.'
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/30/2017
ms.openlocfilehash: 02bd25c4b4e251536dfdabdef109eb659fe3be37
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032161"
---
# <a name="new-features-in-mapkit-on-ios-11"></a>Nuove funzionalità di MapKit in iOS 11

iOS 11 aggiunge le nuove funzionalità seguenti a MapKit:

- [Clustering delle annotazioni](#clustering)
- [Pulsante bussola](#compass)
- [Visualizzazione scala](#scale)
- [Pulsante rilevamento utenti](#user-tracking)

![Mappa che mostra i marcatori cluster e il pulsante bussola](mapkit-images/cyclemap-heading.png)

<a name="clustering" />

## <a name="automatically-grouping-markers-while-zooming"></a>Raggruppamento automatico dei marcatori durante lo zoom

Nell' [esempio MapKit di esempio "tandm"](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample) viene illustrato come implementare la nuova funzionalità di clustering delle annotazioni iOS 11.

### <a name="1-create-an-mkpointannotation-subclass"></a>1. creare una sottoclasse `MKPointAnnotation`

La classe di annotazione punto rappresenta ogni marcatore sulla mappa. Possono essere aggiunti singolarmente utilizzando `MapView.AddAnnotation()` o da una matrice utilizzando `MapView.AddAnnotations()`.

Le classi di annotazione punti non hanno una rappresentazione visiva, sono necessarie solo per rappresentare i dati associati al marcatore (in particolare, la proprietà `Coordinate`, ovvero la latitudine e la longitudine sulla mappa) e le eventuali proprietà personalizzate:

```csharp
public class Bike : MKPointAnnotation
{
  public BikeType Type { get; set; } = BikeType.Tricycle;
  public Bike(){}
  public Bike(NSNumber lat, NSNumber lgn, NSNumber type)
  {
    Coordinate = new CLLocationCoordinate2D(lat.NFloatValue, lgn.NFloatValue);
    switch(type.NUIntValue) {
      case 0:
        Type = BikeType.Unicycle;
        break;
      case 1:
        Type = BikeType.Tricycle;
        break;
    }
  }
}
```

### <a name="2-create-an-mkmarkerannotationview-subclass-for-single-markers"></a>2. creare una sottoclasse `MKMarkerAnnotationView` per i marcatori singoli

La visualizzazione dell'annotazione del marcatore è la rappresentazione visiva di ogni annotazione ed è con stile con proprietà quali:

- **MarkerTintColor** : colore per il marcatore.
- **GlyphText** : testo visualizzato nel marcatore.
- **GlyphImage** : imposta l'immagine visualizzata nel marcatore.
- **DisplayPriority** : determina l'ordine z (comportamento di stack) quando la mappa è affollata con i marcatori. Usare uno dei `Required`, `DefaultHigh`o `DefaultLow`.

Per supportare il clustering automatico, è necessario impostare anche:

- **ClusteringIdentifier** : consente di controllare i marcatori che vengono raggruppati in cluster. È possibile utilizzare lo stesso identificatore per tutti i marcatori oppure utilizzare identificatori diversi per controllare il modo in cui vengono raggruppati.

```csharp
[Register("BikeView")]
public class BikeView : MKMarkerAnnotationView
{
  public static UIColor UnicycleColor = UIColor.FromRGB(254, 122, 36);
  public static UIColor TricycleColor = UIColor.FromRGB(153, 180, 44);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;

      var bike = value as Bike;
      if (bike != null){
        ClusteringIdentifier = "bike";
        switch(bike.Type){
          case BikeType.Unicycle:
            MarkerTintColor = UnicycleColor;
            GlyphImage = UIImage.FromBundle("Unicycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultLow;
            break;
          case BikeType.Tricycle:
            MarkerTintColor = TricycleColor;
            GlyphImage = UIImage.FromBundle("Tricycle");
            DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
            break;
        }
      }
    }
  }
```

### <a name="3-create-an-mkannotationview-to-represent-clusters-of-markers"></a>3. creare un `MKAnnotationView` per rappresentare i cluster di marcatori

Sebbene la visualizzazione dell'annotazione che rappresenta un cluster di marcatori _possa_ essere un'immagine semplice, gli utenti si aspettano che l'app fornisca segnali visivi sul numero di marcatori raggruppati.

Il [codice di esempio](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample) USA CoreGraphics per eseguire il rendering del numero di marcatori nel cluster, nonché una rappresentazione del grafico a cerchio della proporzione di ogni tipo di marcatore.

È inoltre necessario impostare:

- **DisplayPriority** : determina l'ordine z (comportamento di stack) quando la mappa è affollata con i marcatori. Usare uno dei `Required`, `DefaultHigh`o `DefaultLow`.
- **CollisionMode** : `Circle` o `Rectangle`.

```csharp
[Register("ClusterView")]
public class ClusterView : MKAnnotationView
{
  public static UIColor ClusterColor = UIColor.FromRGB(202, 150, 38);
  public override IMKAnnotation Annotation
  {
    get {
      return base.Annotation;
    }
    set {
      base.Annotation = value;
      var cluster = MKAnnotationWrapperExtensions.UnwrapClusterAnnotation(value);
      if (cluster != null)
      {
        var renderer = new UIGraphicsImageRenderer(new CGSize(40, 40));
        var count = cluster.MemberAnnotations.Length;
        var unicycleCount = CountBikeType(cluster.MemberAnnotations, BikeType.Unicycle);

        Image = renderer.CreateImage((context) => {
          // Fill full circle with tricycle color
          BikeView.TricycleColor.SetFill();
          UIBezierPath.FromOval(new CGRect(0, 0, 40, 40)).Fill();
          // Fill pie with unicycle color
          BikeView.UnicycleColor.SetFill();
          var piePath = new UIBezierPath();
          piePath.AddArc(new CGPoint(20,20), 20, 0, (nfloat)(Math.PI * 2.0 * unicycleCount / count), true);
          piePath.AddLineTo(new CGPoint(20, 20));
          piePath.ClosePath();
          piePath.Fill();
          // Fill inner circle with white color
          UIColor.White.SetFill();
          UIBezierPath.FromOval(new CGRect(8, 8, 24, 24)).Fill();
          // Finally draw count text vertically and horizontally centered
          var attributes = new UIStringAttributes() {
            ForegroundColor = UIColor.Black,
            Font = UIFont.BoldSystemFontOfSize(20)
          };
          var text = new NSString($"{count}");
          var size = text.GetSizeUsingAttributes(attributes);
          var rect = new CGRect(20 - size.Width / 2, 20 - size.Height / 2, size.Width, size.Height);
          text.DrawString(rect, attributes);
        });
      }
    }
  }
  public ClusterView(){}
  public ClusterView(MKAnnotation annotation, string reuseIdentifier) : base(annotation, reuseIdentifier)
  {
    DisplayPriority = MKFeatureDisplayPriority.DefaultHigh;
    CollisionMode = MKAnnotationViewCollisionMode.Circle;
    // Offset center point to animate better with marker annotations
    CenterOffset = new CoreGraphics.CGPoint(0, -10);
  }
  private nuint CountBikeType(IMKAnnotation[] members, BikeType type) {
    nuint count = 0;
    foreach(Bike member in members){
      if (member.Type == type) ++count;
    }
    return count;
  }
}
```

### <a name="4-register-the-view-classes"></a>4. registrare le classi di visualizzazione

Quando il controllo della visualizzazione mappa viene creato e aggiunto a una visualizzazione, registrare i tipi di visualizzazione annotazione per abilitare il comportamento di clustering automatico quando la mappa viene ingrandita e visualizzata:

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5. eseguire il rendering della mappa.

Quando viene eseguito il rendering della mappa, i marcatori di annotazione saranno raggruppati o sottoposti a rendering a seconda del livello di zoom. Quando viene modificato il livello di zoom, i marcatori animano i cluster all'interno e all'esterno.

![Simulatore che mostra i marcatori cluster nella mappa](mapkit-images/cyclemap-sml.png)

Per altre informazioni sulla visualizzazione dei dati con MapKit, vedere la [sezione Maps](~/ios/user-interface/controls/ios-maps/index.md) .

<a name="compass" />

## <a name="compass-button"></a>Pulsante bussola

iOS 11 aggiunge la possibilità di estrarre la bussola dalla mappa ed eseguirne il rendering in un'altra posizione nella visualizzazione. Per un esempio, vedere l' [app di esempio tandm](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample) .

Creare un pulsante simile a una bussola (inclusa l'animazione in tempo reale quando l'orientamento della mappa viene modificato) ed eseguirne il rendering su un altro controllo.

![Pulsante bussola nella barra di spostamento](mapkit-images/compass-sml.png)

Il codice seguente crea un pulsante della bussola e ne esegue il rendering sulla barra di spostamento:

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

È possibile utilizzare la proprietà `ShowsCompass` per controllare la visibilità della bussola predefinita all'interno della vista mappa.

<a name="scale" />

## <a name="scale-view"></a>Visualizzazione scala

Aggiungere la scala in un altro punto della vista usando il metodo `MKScaleView.FromMapView()` per ottenere un'istanza della visualizzazione scala da aggiungere altrove nella gerarchia di visualizzazione.

![Visualizzazione scala sovrapposta su una mappa](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

È possibile utilizzare la proprietà `ShowsScale` per controllare la visibilità della bussola predefinita all'interno della vista mappa.

<a name="user-tracking" />

## <a name="user-tracking-button"></a>Pulsante rilevamento utenti

Il pulsante rilevamento utenti centra la mappa sulla posizione corrente dell'utente. Utilizzare il metodo `MKUserTrackingButton.FromMapView()` per ottenere un'istanza del pulsante, applicare le modifiche di formattazione e aggiungere altrove nella gerarchia di visualizzazione.

![Pulsante località utente sovrapposto a una mappa](mapkit-images/user-location-sml.png)

```csharp
var button = MKUserTrackingButton.FromMapView(MapView);
button.Layer.BackgroundColor = UIColor.FromRGBA(255,255,255,80).CGColor;
button.Layer.BorderColor = UIColor.White.CGColor;
button.Layer.BorderWidth = 1;
button.Layer.CornerRadius = 5;
button.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(button); // constraints omitted for simplicity
```

## <a name="related-links"></a>Collegamenti correlati

- [Esempio MapKit ' tandm '](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-mapkitsample)
- [MKCompassButton](https://developer.apple.com/documentation/mapkit/mkcompassbutton)
- [Novità di MapKit (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/237/)
