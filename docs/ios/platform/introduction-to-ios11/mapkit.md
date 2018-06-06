---
title: Nuove funzionalità di MapKit in iOS 11
description: 'Questo documento descrive le nuove funzionalità MapKit iOS 11: raggruppamento marcatori, il pulsante bussola, la visualizzazione della scala e il pulsante di rilevamento utente.'
ms.prod: xamarin
ms.assetid: 304AE5A3-518F-422F-BE24-92D62CE30F34
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2016
ms.openlocfilehash: f73078a2dcbaeefeb5608ce7ec1e2c12b261acad
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787406"
---
# <a name="new-features-in-mapkit-on-ios-11"></a>Nuove funzionalità di MapKit in iOS 11

iOS 11 aggiunge le nuove funzionalità seguenti per MapKit:

- [Annotazione Clustering](#clustering)
- [Pulsante bussola](#compass)
- [Visualizzazione della scala](#scale)
- [Pulsante Verifica utente](#user-tracking)

![Mappa che illustra gli indicatori di cluster e pulsante cardinali](mapkit-images/cyclemap-heading.png)

<a name="clustering" />

## <a name="automatically-grouping-markers-while-zooming"></a>Automaticamente i marcatori di raggruppamento durante lo zoom

L'esempio [MapKit esempio "Tandm"](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) viene illustrato come implementare l'annotazione iOS 11 nuove funzionalità di clustering.

### <a name="1-create-an-mkpointannotation-subclass"></a>1. Creare un `MKPointAnnotation` sottoclasse

La classe di annotazione punto rappresenta ogni marcatore della mappa. Possono essere aggiunti singolarmente utilizzando `MapView.AddAnnotation()` o da una matrice utilizzando `MapView.AddAnnotations()`.

Le classi di annotazione punto non dispone di una rappresentazione visiva, sono necessarie solo per rappresentare i dati associati con il marcatore (in particolare, il `Coordinate` proprietà che rappresenta la latitudine e longitudine sulla mappa) e tutte le proprietà personalizzate:

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

La visualizzazione di annotazione del marcatore è la rappresentazione visiva di ciascuna annotazione e stile utilizzando le proprietà, ad esempio:

- **MarkerTintColor** : il colore per il marcatore.
- **GlyphText** : testo visualizzato nell'indicatore.
- **GlyphImage** : imposta l'immagine che viene visualizzato nell'indicatore.
- **DisplayPriority** -determina l'ordine z (comportamento sovrapposizione) quando la mappa è talmente con marcatori. Utilizzare uno dei `Required`, `DefaultHigh`, o `DefaultLow`.

Per supportare il clustering automatico, è necessario anche impostare:

- **ClusteringIdentifier** : consente di controllare quali marcatori ottengano raggruppati. È possibile utilizzare lo stesso identificatore per tutti gli indicatori o usano identificatori diversi per controllare il modo in cui vengono raggruppate.

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

Durante la visualizzazione di annotazione che rappresenta un cluster di marcatori _Impossibile_ essere un'immagine semplice, gli utenti prevedono che l'applicazione fornisca segnali visivi sulla marcatori quanti sono stati raggruppati insieme.

Il [codice di esempio](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) utilizza CoreGraphics per visualizzare il numero di marcatori in cluster, nonché una rappresentazione cerchio grafico della percentuale di ogni tipo di marcatore.

È anche necessario impostare:

- **DisplayPriority** -determina l'ordine z (comportamento sovrapposizione) quando la mappa è talmente con marcatori. Utilizzare uno dei `Required`, `DefaultHigh`, o `DefaultLow`.
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

Quando il controllo visualizzazione mappa verrà creato e aggiunto a una vista, registrare i tipi di visualizzazione di annotazione per abilitare il comportamento di clustering automatica, come la mappa viene applicato lo zoom avanti e indietro:

```csharp
MapView.Register(typeof(BikeView), MKMapViewDefault.AnnotationViewReuseIdentifier);
MapView.Register(typeof(ClusterView), MKMapViewDefault.ClusterAnnotationViewReuseIdentifier);
```

### <a name="5-render-the-map"></a>5. Eseguire il rendering della mappa.

Quando viene eseguito il rendering della mappa, i marcatori di annotazione verranno cluster o il rendering in base al livello di zoom. Man mano che cambia il livello di zoom, marcatori animare da e verso i cluster.

![La visualizzazione di cluster degli indicatori nella mappa del simulatore](mapkit-images/cyclemap-sml.png)

Consultare la [esegue il mapping di sezione](~/ios/user-interface/controls/ios-maps/index.md) per ulteriori informazioni sulla visualizzazione dei dati con MapKit.

<a name="compass" />

## <a name="compass-button"></a>Pulsante bussola

iOS 11 consente di estrarre bussola fuori della mappa e di eseguirne il rendering in un' posizione nella visualizzazione. Vedere il [app di esempio Tandm](https://developer.xamarin.com/samples/monotouch/ios11/MapKitSample/) per un esempio.

Creare un pulsante che appare come un compasso (incluso in tempo reale animazione quando viene modificato l'orientamento di mappa), e ne esegue il rendering in un altro controllo.

![Bussola pulsante nella barra di navigazione](mapkit-images/compass-sml.png)

Il codice seguente crea un pulsante bussola e ne esegue il rendering nella barra di spostamento:

```csharp
var compass = MKCompassButton.FromMapView(MapView);
compass.CompassVisibility = MKFeatureVisibility.Visible;
NavigationItem.RightBarButtonItem = new UIBarButtonItem(compass);
MapView.ShowsCompass = false; // so we don't have two compasses!
```

Il `ShowsCompass` proprietà può essere utilizzata per controllare la visibilità di bussola predefinito all'interno della visualizzazione della mappa.

<a name="scale" />

## <a name="scale-view"></a>Visualizzazione della scala

Aggiungere la scala altrove nella visualizzazione utilizzando il `MKScaleView.FromMapView()` metodo per ottenere un'istanza della visualizzazione della scala per aggiungere in un' posizione nella gerarchia della visualizzazione.

![Visualizzazione della scala sovrapposti su una mappa](mapkit-images/scale-sml.png)

```csharp
var scale = MKScaleView.FromMapView(MapView);
scale.LegendAlignment = MKScaleViewAlignment.Trailing;
scale.TranslatesAutoresizingMaskIntoConstraints = false;
View.AddSubview(scale); // constraints omitted for simplicity
MapView.ShowsScale = false; // so we don't have two scale displays!
```

Il `ShowsScale` proprietà può essere utilizzata per controllare la visibilità di bussola predefinito all'interno della visualizzazione della mappa.

<a name="user-tracking" />

## <a name="user-tracking-button"></a>Pulsante Verifica utente

Pulsante rilevamento utente Allinea al centro la mappa nella posizione corrente dell'utente. Utilizzare il `MKUserTrackingButton.FromMapView()` metodo per ottenere un'istanza del pulsante, applicare le modifiche alla formattazione e aggiungere in un' posizione nella gerarchia della visualizzazione.

![Pulsante percorso utente sovrapposti su una mappa](mapkit-images/user-location-sml.png)

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
- [Novità in MapKit (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/237/)
