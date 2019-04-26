---
title: Nuove funzionalità in MapKit in iOS 11
description: 'Questo documento descrive le nuove funzionalità MapKit iOS 11: raggruppamento di marcatori, il pulsante della bussola, la visualizzazione della scala e il pulsante di rilevamento utente.'
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/30/2017
ms.openlocfilehash: 3c1b29a4aba03ffe8a3131625ef29cf64766bb6c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342350"
---
# <a name="new-features-in-mapkit-on-ios-11"></a>Nuove funzionalità in MapKit in iOS 11

iOS 11 aggiunge le nuove funzionalità seguenti a MapKit:

- [Annotazione di Clustering](#clustering)
- [Pulsante della bussola](#compass)
- [Visualizzazione della scala](#scale)
- [Pulsante di rilevamento utente](#user-tracking)

![Mappa che illustra gli indicatori di cluster e della bussola pulsante](mapkit-images/cyclemap-heading.png)

<a name="clustering" />

## <a name="automatically-grouping-markers-while-zooming"></a>Marcatori di raggruppamento automaticamente durante lo zoom avanti

L'esempio [MapKit esempio "Tandm"](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) viene illustrato come implementare l'annotazione iOS 11 nuove funzionalità di clustering.

### <a name="1-create-an-mkpointannotation-subclass"></a>1. Creare un `MKPointAnnotation` sottoclasse

La classe di annotazione punto rappresenta ogni marcatore sulla mappa. Possono essere aggiunti utilizzando singolarmente `MapView.AddAnnotation()` o da una matrice usando `MapView.AddAnnotations()`.

Classi dei punti di annotazione non è una rappresentazione visiva, sono necessarie solo per rappresentare i dati associati al marcatore (soprattutto il `Coordinate` proprietà che rappresenta la latitudine e longitudine sulla mappa) e tutte le proprietà personalizzate:

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

### <a name="2-create-an-mkmarkerannotationview-subclass-for-single-markers"></a>2. Creare un `MKMarkerAnnotationView` sottoclasse per i marcatori singoli

La visualizzazione di annotazione marcatore è la rappresentazione visiva di ciascuna annotazione e viene applicato lo stile tramite proprietà, ad esempio:

- **MarkerTintColor** : il colore per il marcatore.
- **GlyphText** : testo visualizzato nell'indicatore.
- **GlyphImage** : imposta l'immagine visualizzata nel marcatore.
- **DisplayPriority** – determina l'ordine z (comportamento impilamento) quando la mappa è piena con marcatori. Usare uno dei `Required`, `DefaultHigh`, o `DefaultLow`.

Per supportare il clustering automatico, è necessario anche impostare:

- **ClusteringIdentifier** : consente di controllare quali indicatori vengono raggruppati insieme. È possibile utilizzare lo stesso identificatore per tutti i marcatori o usano identificatori diversi per controllare il modo in cui vengono raggruppate.

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

### <a name="3-create-an-mkannotationview-to-represent-clusters-of-markers"></a>3. Creare un `MKAnnotationView` per rappresentare i cluster di marcatori

Mentre la visualizzazione di annotazione che rappresenta un cluster di marcatori _è stato possibile_ essere una semplice immagine, gli utenti si aspettano l'applicazione fornisca segnali visivi sulla marcatori di quanti sono stati raggruppati.

Il [esempi di codice](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) utilizza CoreGraphics per visualizzare il numero di marcatori in cluster, nonché una rappresentazione di cerchio-grafico della percentuale di ogni tipo di marcatore.

È inoltre necessario impostare:

- **DisplayPriority** – determina l'ordine z (comportamento impilamento) quando la mappa è piena con marcatori. Usare uno dei `Required`, `DefaultHigh`, o `DefaultLow`.
- **CollisionMode** – `Circle` o `Rectangle`.

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

### <a name="4-register-the-view-classes"></a>4. Registrare le classi di visualizzazione

Quando il controllo di visualizzazione della mappa viene creato e aggiunto a una vista, registrare i tipi di visualizzazione di annotazione per abilitare il comportamento di clustering automatico come la mappa viene ingrandita o ridotta in e out:

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5. Eseguire il rendering della mappa.

Quando viene eseguito il rendering della mappa, i marcatori di annotazione verranno in cluster o il rendering a seconda del livello di zoom. Se il livello di zoom viene modificato, gli indicatori di aggiungere un'animazione da e verso i cluster.

![Simulatore la visualizzazione degli indicatori cluster sulla mappa](mapkit-images/cyclemap-sml.png)

Vedere le [esegue il mapping di sezione](~/ios/user-interface/controls/ios-maps/index.md) per altre informazioni sulla visualizzazione dei dati con MapKit.

<a name="compass" />

## <a name="compass-button"></a>Pulsante della bussola

iOS 11 aggiunge la possibilità di estrarre la bussola ridurre la mappa e trasformarlo in un' posizione nella visualizzazione. Vedere le [app di esempio Tandm](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) per un esempio.

Creare un pulsante che appare come un compasso (tra cui animazione in tempo reale quando viene modificato l'orientamento di mappa), e ne esegue il rendering in un altro controllo.

![Pulsante della bussola nella barra di spostamento](mapkit-images/compass-sml.png)

Il codice seguente crea un pulsante della bussola e ne esegue il rendering sulla barra di spostamento:

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

Il `ShowsCompass` proprietà può essere utilizzata per controllare la visibilità della bussola predefiniti all'interno della visualizzazione della mappa.

<a name="scale" />

## <a name="scale-view"></a>Visualizzazione della scala

Aggiungere la scalabilità in un' posizione nella vista che utilizza il `MKScaleView.FromMapView()` metodo per ottenere un'istanza della visualizzazione della scala per aggiungere in un' posizione nella gerarchia di visualizzazione.

![Visualizzazione della scala sovrapposte su una mappa](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

Il `ShowsScale` proprietà può essere utilizzata per controllare la visibilità della bussola predefiniti all'interno della visualizzazione della mappa.

<a name="user-tracking" />

## <a name="user-tracking-button"></a>Pulsante di rilevamento utente

Il pulsante di rilevamento utente Allinea al centro la mappa in base alla posizione dell'utente. Usare il `MKUserTrackingButton.FromMapView()` metodo per ottenere un'istanza del pulsante, applicare le modifiche alla formattazione e aggiungere in un' posizione nella gerarchia di visualizzazione.

![Pulsante di percorso utente sovrapposto su una mappa](mapkit-images/user-location-sml.png)

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

- [Esempio MapKit 'Tandm'](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/)
- [MKCompassButton](https://developer.apple.com/documentation/mapkit/mkcompassbutton)
- [What ' s New in MapKit (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/237/)
