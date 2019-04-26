---
title: ARKit 2 in Xamarin.iOS
description: Questo documento descrive gli aggiornamenti a ARKit in iOS 12. Illustra l'uso di immagini e oggetti di riferimento per il rilevamento, include il codice per la trama ambientali e vengono illustrati i problemi più comuni nella programmazione ARKit.
ms.prod: xamarin
ms.assetid: af758092-1523-4ab7-aa53-c37a81fb156a
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/22/2018
ms.openlocfilehash: 559ef9cc9a3e5ace7a4023e81363825c6861f6d4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399204"
---
# <a name="arkit-2-in-xamarinios"></a>ARKit 2 in Xamarin.iOS

ARKit è maturato notevolmente rispetto alla sua introduzione ultimo anno in iOS 11. Prima di tutto, può ora rilevare verticale, orizzontali piani, nonché questo migliora notevolmente la praticità di esperienze di realtà aumentata interni. Inoltre, sono disponibili nuove funzionalità:

* Riconoscimento di immagini di riferimento e gli oggetti come punto di intersezione tra il mondo reale e immagini digitali
* Una nuova modalità di illuminazione che simula l'illuminazione reale
* La possibilità di condividere e rendere persistenti gli ambienti di AR
* Un nuovo formato di file preferito per l'archiviazione dei contenuti di AR

## <a name="recognizing-reference-objects"></a>Riconoscendo gli oggetti di riferimento

Una funzionalità di presentazione in ARKit 2 è la capacità di riconoscere le immagini di riferimento e gli oggetti. Immagini di riferimento possono essere caricate dal file di immagine normale ([descritti in un secondo momento](#more-tracking-configurations)), tranne riferimento gli oggetti devono essere analizzati, utilizzando lo sviluppatori [ `ARObjectScanningConfiguration` ](xref:ARKit.ARObjectScanningConfiguration).

### <a name="sample-app-scanning-and-detecting-3d-objects"></a>App di esempio: L'analisi e rilevamento di oggetti 3D

Il [analisi e rilevamento di oggetti 3D](https://developer.xamarin.com/samples/monotouch/ios12/ScanningAndDetecting3DObjects/) esempio è una porta di un [progetto Apple](https://developer.apple.com/documentation/arkit/scanning_and_detecting_3d_objects?language=objc) che dimostra:

* Gestione dello stato di applicazione utilizzando [ `NSNotification` ](xref:Foundation.NSNotification) oggetti
* Visualizzazione personalizzata
* Movimenti complessi
* L'analisi dell'oggetto
* L'archiviazione di un [`ARReferenceObject`](xref:ARKit.ARReferenceObject)

L'analisi di un oggetto di riferimento è batteria - e intensivo del processore e i dispositivi meno recenti avrà spesso problemi a raggiungere rilevamento stabile.

### <a name="state-management-using-nsnotification-objects"></a>Gestione dello stato tramite gli oggetti NSNotification

Questa applicazione usa una macchina a stati che effettua la transizione tra gli stati seguenti:

* `AppState.StartARSession`
* `AppState.NotReady`
* `AppState.Scanning`
* `AppState.Testing`

E inoltre utilizza un set incorporato di stati e transizioni in `AppState.Scanning`:

* `Scan.ScanState.Ready`
* `Scan.ScanState.DefineBoundingBox`
* `Scan.ScanState.Scanning`
* `Scan.ScanState.AdjustingOrigin`

L'app Usa un'architettura reattiva che invii notifiche di transizione di stato agli [ `NSNotificationCenter` ](xref:Foundation.NSNotificationCenter) e sottoscrive le notifiche. Il programma di installazione è simile a questo frammento di codice da `ViewController.cs`:

```csharp
// Configure notifications for application state changes
var notificationCenter = NSNotificationCenter.DefaultCenter;

notificationCenter.AddObserver(Scan.ScanningStateChangedNotificationName, State.ScanningStateChanged);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxCreatedNotificationName, State.GhostBoundingBoxWasCreated);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxRemovedNotificationName, State.GhostBoundingBoxWasRemoved);
notificationCenter.AddObserver(ScannedObject.BoundingBoxCreatedNotificationName, State.BoundingBoxWasCreated);
notificationCenter.AddObserver(BoundingBox.ScanPercentageChangedNotificationName, ScanPercentageChanged);
notificationCenter.AddObserver(BoundingBox.ExtentChangedNotificationName, BoundingBoxExtentChanged);
notificationCenter.AddObserver(BoundingBox.PositionChangedNotificationName, BoundingBoxPositionChanged);
notificationCenter.AddObserver(ObjectOrigin.PositionChangedNotificationName, ObjectOriginPositionChanged);
notificationCenter.AddObserver(NSProcessInfo.PowerStateDidChangeNotification, DisplayWarningIfInLowPowerMode);

```

Un gestore di notifica tipica viene generata verrà aggiornare l'interfaccia utente ed eventualmente modificare lo stato dell'applicazione, ad esempio il gestore per l'aggiornamento come viene analizzato l'oggetto:

```csharp
private void ScanPercentageChanged(NSNotification notification)
{
    var pctNum = TryGet<NSNumber>(notification.UserInfo, BoundingBox.ScanPercentageUserKey);
    if (pctNum == null)
    {
        return;
    }
    double percentage = pctNum.DoubleValue;
    // Switch to the next state if scan is complete
    if (percentage >= 100.0)
    {
        State.SwitchToNextState();
    }
    else
    {
        DispatchQueue.MainQueue.DispatchAsync(() => navigationBarController.SetNavigationBarTitle($"Scan ({percentage})"));
    }
}

```

Infine, `Enter{State}` metodi modificano il modello e l'esperienza utente come appropriato per il nuovo stato:

```csharp
internal void EnterStateTesting()
{
    navigationBarController.SetNavigationBarTitle("Testing");
    navigationBarController.ShowBackButton(false);
    loadModelButton.Hidden = true;
    flashlightButton.Hidden = false;
    nextButton.Enabled = true;
    nextButton.SetTitle("Share", UIControlState.Normal);

    testRun = new TestRun(sessionInfo, sceneView);
    TestObjectDetection();
    CancelMaxScanTimeTimer();
}
```

### <a name="custom-visualization"></a>Visualizzazione personalizzata

L'app Mostra di basso livello "punto di cloud" dell'oggetto contenute all'interno di un rettangolo proiettato su un piano orizzontale rilevato.

Cloud e questo punto è disponibile agli sviluppatori per il [ `ARFrame.RawFeaturePoints` ](xref:ARKit.ARFrame.RawFeaturePoints) proprietà. Visualizzazione in modo efficiente sul cloud punto può essere un problema spinoso. Scorrere i punti, quindi creando e inserendo un nuovo nodo di SceneKit per ogni punto sarebbe terminato la frequenza dei fotogrammi. In alternativa, se eseguita in modo asincrono, non vi sarà un ritardo. L'esempio mantiene le prestazioni con una strategia di tre parti:

* Usare codice unsafe per aggiungere i dati di posizionano e interpretano i dati come un buffer di byte non elaborati.
* Conversione di tale buffer non elaborato in un [ `SCNGeometrySource` ](xref:SceneKit.SCNGeometrySource) e la creazione di un "modello" [ `SCNGeometryElement` ](xref:SceneKit.SCNGeometryElement) oggetto.
* Rapidamente "unire" i dati non elaborati e il modello usando [`SCNGeometry.Create(SCNGeometrySource[], SCNGeometryElement[])`](xref:SceneKit.SCNGeometry.Create(SceneKit.SCNGeometrySource[],SceneKit.SCNGeometryElement[]))

```csharp
internal static SCNGeometry CreateVisualization(NVector3[] points, UIColor color, float size)
{
  if (points.Length == 0)
  {
    return null;
  }

  unsafe
  {
    var stride = sizeof(float) * 3;

    // Pin the data down so that it doesn't move
    fixed (NVector3* pPoints = &amp;points[0])
    {
      // Important: Don't unpin until after `SCNGeometry.Create`, because geometry creation is lazy

      // Grab a pointer to the data and treat it as a byte buffer of the appropriate length
      var intPtr = new IntPtr(pPoints);
      var pointData = NSData.FromBytes(intPtr, (System.nuint) (stride * points.Length));

      // Create a geometry source (factory) configured properly for the data (3 vertices)
      var source = SCNGeometrySource.FromData(
        pointData,
        SCNGeometrySourceSemantics.Vertex,
        points.Length,
        true,
        3,
        sizeof(float),
        0,
        stride
      );

      // Create geometry element
      // The null and bytesPerElement = 0 look odd, but this is just a template object
      var template = SCNGeometryElement.FromData(null, SCNGeometryPrimitiveType.Point, points.Length, 0);
      template.PointSize = 0.001F;
      template.MinimumPointScreenSpaceRadius = size;
      template.MaximumPointScreenSpaceRadius = size;

      // Stitch the data (source) together with the template to create the new object
      var pointsGeometry = SCNGeometry.Create(new[] { source }, new[] { template });
      pointsGeometry.Materials = new[] { Utilities.Material(color) };
      return pointsGeometry;
    }
  }
}
```

Il risultato è simile alla seguente:

![point_cloud](images/arkit_point_cloud.jpeg)

### <a name="complex-gestures"></a>Movimenti complessi

L'utente può ridimensionare, ruotare e trascinare il rettangolo di selezione che circonda l'oggetto di destinazione. Esistono due aspetti interessanti nei riconoscitori di movimento associato.

In primo luogo, tutti i riconoscitori di movimento attivare solo dopo che è stata passata una soglia; ad esempio, un dito ha trascinato così tante pixel o la rotazione supera un angolo. La tecnica consiste nell'accumulare lo spostamento fino a quando non è stata superata la soglia e quindi applicarlo in modo incrementale:

```csharp
// A custom rotation gesture recognizer that fires only when a threshold is passed
internal partial class ThresholdRotationGestureRecognizer : UIRotationGestureRecognizer
{
    // The threshold after which this gesture is detected.
    const double threshold = Math.PI / 15; // (12°)

    // Indicates whether the currently active gesture has exceeded the threshold
    private bool thresholdExceeded = false;

    private double previousRotation = 0;
    internal double RotationDelta { get; private set; }

    internal ThresholdRotationGestureRecognizer(IntPtr handle) : base(handle)
    {
    }

    // Observe when the gesture's state changes to reset the threshold
    public override UIGestureRecognizerState State
    {
        get => base.State;
        set
        {
            base.State = value;

            switch(value)
            {
                case UIGestureRecognizerState.Began :
                case UIGestureRecognizerState.Changed :
                    break;
                default :
                    // Reset threshold check
                    thresholdExceeded = false;
                    previousRotation = 0;
                    RotationDelta = 0;
                    break;
            }
        }
    }

    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);

        if (thresholdExceeded)
        {
            RotationDelta = Rotation - previousRotation;
            previousRotation = Rotation;
        }

        if (! thresholdExceeded && Math.Abs(Rotation) > threshold)
        {
            thresholdExceeded = true;
            previousRotation = Rotation;
        }
    }
}
```

La seconda parte interessante fatta in relazione ai movimenti è il modo in cui il rettangolo di selezione viene spostata in relazione al rilevato piani reali. Questo aspetto verrà discusso [questo post di blog di Xamarin](https://blog.xamarin.com/exploring-new-ios-12-arkit-capabilities-with-xamarin/).

## <a name="other-new-features-in-arkit-2"></a>Altre nuove funzionalità in ARKit 2

### <a name="more-tracking-configurations"></a>Più configurazioni del rilevamento

A questo punto, è possibile usare uno dei seguenti come base per un'esperienza di realtà mista:

* Solo l'accelerometro dispositivo ([`AROrientationTrackingConfiguration`](xref:ARKit.AROrientationTrackingConfiguration), iOS 11)
* I volti ([`ARFaceTrackingConfiguration`](xref:ARKit.ARFaceTrackingConfiguration), iOS 11)
* Fare riferimento a immagini ([`ARImageTrackingConfiguration`](xref:ARKit.ARImageTrackingConfiguration), iOS 12)
* L'analisi di oggetti 3D ([`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration), iOS 12)
* Odometrica inerziale Visual ([`ARWorldTrackingConfiguration`](xref:ARKit.ARWorldTrackingConfiguration)migliorato in iOS 12)

`AROrientationTrackingConfiguration`, descritto in [questo post di blog e F# campione](https://github.com/lobrien/FSharp_Face_AR), è più limitato e offre un'esperienza di realtà mista insufficiente, come inserisce solo oggetti digitali in relazione al movimento del dispositivo, senza tentare di associare il dispositivo e schermata in il mondo reale.

Il `ARImageTrackingConfiguration` consente di riconoscere reali le immagini 2D (dipinti, logo e così via) e usandole per immagini digitali anchor:

```csharp
var imagesAndWidths = new[] {
    ("cover1.jpg", 0.185F),
    ("cover2.jpg", 0.185F),
     //...etc...
    ("cover100.jpg", 0.185F),
};

var referenceImages = new NSSet<ARReferenceImage>(
    imagesAndWidths.Select( imageAndWidth =>
    {
      // Tuples cannot be destructured in lambda arguments
        var (image, width) = imageAndWidth;
        // Read the image
        var img = UIImage.FromFile(image).CGImage;
        return new ARReferenceImage(img, ImageIO.CGImagePropertyOrientation.Up, width);
    }).ToArray());

configuration.TrackingImages = referenceImages;
```

Esistono due aspetti interessanti per questa configurazione:

* È efficiente e può essere utilizzato con un numero potenzialmente elevato di immagini di riferimento
* Le immagini digitali sono ancorata all'immagine, anche se tale immagine viene spostato nel mondo reale (ad esempio, se viene riconosciuto il coperchio di un libro, terrà traccia libro come viene eseguito dimostrato, laid verso il basso e così via.).

Il `ARObjectScanningConfiguration` avevo [precedentemente](#recognizing-reference-objects) ed è una configurazione per lo sviluppatore per l'analisi di oggetti 3D. È altamente con utilizzo intensivo della batteria e del processore e non deve essere utilizzata nelle applicazioni dell'utente finale. L'esempio [analisi e rilevamento di oggetti 3D](https://developer.xamarin.com/samples/monotouch/ios12/ScanningAndDetecting3DObjects/) illustra l'uso di questa configurazione.

La configurazione di rilevamento finale, `ARWorldTrackingConfiguration` , è il componente di base della maggior parte delle esperienze di realtà mista. Questa configurazione usa "visual inerziale odometrica" per correlare reali "punti di funzionalità" a immagini digitali. Geometria digitale sprite sono ancorati relativo piani verticali e orizzontali reali o relativo a rilevato `ARReferenceObject` istanze. In questa configurazione, l'origine del mondo è posizione originale della camera nello spazio con l'asse z allineato alla gravità e oggetti digitali "rimangono invariate" rispetto oggetti nel mondo reale.

### <a name="environmental-texturing"></a>La trama ambientale

2 ARKit supporta "ambientale della trama" che usa immagini acquisite per stimare l'illuminazione e addirittura applicare evidenziazioni speculari agli oggetti shiny. Mappa cubi dell'ambiente viene creato dinamicamente e, dopo che la videocamera ha preso in tutte le direzioni, possono produrre un'esperienza straordinariamente realistica:

![immagine di demo trama ambientale](images/arkit_env_texturing.png)

Per utilizzare la trama dell'ambiente:

* I [ `SCNMaterial` ](xref:SceneKit.SCNMaterial) necessario usare gli oggetti [ `SCNLightingModel.PhysicallyBased` ](xref:SceneKit.SCNLightingModel.PhysicallyBased) e assegnare un valore compreso tra 0 e 1 per [ `Metalness.Contents` ](xref:SceneKit.SCNMaterial.Metalness) e [ `Roughness.Contents` ](xref:SceneKit.SCNMaterialProperty.Contents) e
* È necessario impostare la configurazione di rilevamento [ `EnvironmentTexturing` ](xref:ARKit.ARWorldTrackingConfiguration.EnvironmentTexturing)  =  [AREnvironmentTexturing.Automatic'](xref:ARKit.AREnvironmentTexturing.Automatic) :

```csharp
var sphere = SCNSphere.Create(0.33F);
sphere.FirstMaterial.LightingModelName = SCNLightingModel.PhysicallyBased;
// Shiny metallic sphere
sphere.FirstMaterial.Metalness.Contents = new NSNumber(1.0F);
sphere.FirstMaterial.Roughness.Contents = new NSNumber(0.0F);

// Session configuration:
var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic
};
```

Sebbene la trama perfettamente riflettente mostrata nel frammento di codice precedente è divertente in un esempio, la trama ambientale è probabilmente preferibile utilizzato con moderazione correre attivare una risposta "valley uncanny" (la trama è solo una stima basata su quali la fotocamera registrati).


### <a name="shared-and-persistent-ar-experiences"></a>Esperienze di AR condivise e permanenti

Un'altra novità principali e ARKit 2 è il [ `ARWorldMap` ](xref:ARKit.ARWorldMap) (classe), che consente di condividere o archiviare dati di rilevamento delle modifiche world. Si ottiene la mappa del mondo corrente con [ `ARSession.GetCurrentWorldMapAsync` ](xref:ARKit.ARSession.GetCurrentWorldMapAsync) oppure [ `GetCurrentWorldMap(Action<ARWorldMap,NSError>` ](xref:ARKit.ARSession.GetCurrentWorldMap(System.Action{ARKit.ARWorldMap,Foundation.NSError})) :

```csharp
// Local storage
var PersistentWorldPath => Environment.GetFolderPath(Environment.SpecialFolder.Personal) + "/arworldmap";

// Later, after scanning the environment thoroughly...
var worldMap = await Session.GetCurrentWorldMapAsync();
if (worldMap != null)
{
    var data = NSKeyedArchiver.ArchivedDataWithRootObject(worldMap, true, out var err);
    if (err != null)
    {
        Console.WriteLine(err);
    }
    File.WriteAllBytes(PersistentWorldPath, data.ToArray());
}
```

Per condividere o ripristinare la mappa del mondo:

1. Caricare i dati dal file,
2. Estrarre i dati in un `ARWorldMap` oggetto,
3. Usarlo come valore per il [ `ARWorldTrackingConfiguration.InitialWorldMap` ](xref:ARKit.ARWorldTrackingConfiguration.InitialWorldMap) proprietà:

```csharp
var data = NSData.FromArray(File.ReadAllBytes(PersistentWorldController.PersistenWorldPath));
var worldMap = (ARWorldMap)NSKeyedUnarchiver.GetUnarchivedObject(typeof(ARWorldMap), data, out var err);

var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic,
    InitialWorldMap = worldMap
};
```

Il `ARWorldMap` contiene solo dati non visibili world-rilevamento e la [ `ARAnchor` ](xref:ARKit.ARAnchor) oggetti, esegue _non_ contengono risorse digitali. Per condividere la geometria o immagini, è possibile sviluppare la propria strategia appropriata per il caso d'uso (probabilmente da archiviare o la trasmissione solo la posizione e l'orientamento della geometria e applicarlo a statica `SCNGeometry` o probabilmente da archiviare o la trasmissione. oggetti serializzati). Il vantaggio del `ARWorldMap` è che gli asset, una volta posizionati relativo condivisa `ARAnchor`, verranno visualizzati in modo coerente tra dispositivi o le sessioni.

### <a name="universal-scene-description-file-format"></a>Formato di file di descrizione della scena universale

La funzionalità headline finale ARKit 2 è l'adozione di Apple del Pixar [Universal Description scena](https://graphics.pixar.com/usd/docs/Introduction-to-USD.html) formato di file. Questo formato viene sostituito formato del Collada DAE come il formato preferito per la condivisione e l'archiviazione ARKit asset. Supporto per la visualizzazione di asset è incorporato in iOS 12 e Mojave. L'estensione di file USDZ è un archivio zip non compresso e crittografato che contiene i file USD. Pixar [fornisce strumenti per lavorare con file USD](https://graphics.pixar.com/usd/docs/USD-Toolset.html#USDToolset-usdedit) ma non è stato ancora molto terze parti.

## <a name="arkit-programming-tips"></a>Suggerimenti per la programmazione ARKit

### <a name="manual-resource-management"></a>Gestione manuale delle risorse

In ARKit, è essenziale gestire manualmente le risorse. Non solo in questo modo frequenze di fotogrammi elevate, è effettivamente _necessari_ confondere un "blocco dello schermo". Il framework ARKit è differito su fornendo un nuovo frame di fotocamera ([`ARSession.CurrentFrame`](xref:ARKit.ARSession.CurrentFrame). Finché l'oggetto corrente [ `ARFrame` ](xref:ARKit.ARFrame) ha avuto `Dispose()` ARKit verrà chiamato su di esso, non specificare un nuovo frame. In questo modo il video di "bloccare" anche se il resto dell'applicazione è reattivo. La soluzione consiste nell'accedere sempre ai `ARSession.CurrentFrame` con un `using` bloccare o chiamare manualmente `Dispose()` su di esso.

Tutti gli oggetti derivati da `NSObject` vengono `IDisposable` e `NSObject` implementa il [modello Dispose](https://docs.microsoft.com/dotnet/standard/design-guidelines/dispose-pattern), in modo che in genere è consigliabile seguire [questo modello per l'implementazione `Dispose` su un oggetto derivato classe](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose).

### <a name="manipulating-transform-matrices"></a>La modifica di matrici di trasformazione

In qualsiasi applicazione 3D, si intende a che fare con le matrici di 4x4 trasformazione che descrivono in modo compatto come spostare, ruotare e inclinare un oggetto nello spazio 3D. In SceneKit, si tratta [ `SCNMatrix4` ](xref:SceneKit.SCNMatrix4) oggetti.  

Il [ `SCNNode.Transform` ](xref:SceneKit.SCNNode.Transform) proprietà restituisce il `SCNMatrix4` matrice di trasformazione per il [ `SCNNode` ](xref:SceneKit.SCNNode) _come supportato da_ la lunghezza di riga `simdfloat4x4` tipo. Quindi, ad esempio:

```csharp
var node = new SCNNode { Position = new SCNVector3(2, 3, 4) };  
var xform = node.Transform;
Console.WriteLine(xform);
// Output is: "(1, 0, 0, 0)\n(0, 1, 0, 0)\n(0, 0, 1, 0)\n(2, 3, 4, 1)"
```  

Come può notare, la posizione è codificata in primi tre elementi della riga inferiore.

In Xamarin, è il tipo comune per la modifica di matrici di trasformazione `NVector4`, che per convenzione viene interpretato in modo colonna ordinata. Vale a dire, il componente/posizione di traslazione è previsto nella M14 M24, M34, non M41, M42, M43:

![Visual Studio per lunghezza di riga per lunghezza di colonna](images/arkit_row_vs_column.png)

La coerenza con la scelta di interpretazione di matrice è fondamentale per comportamento corretto. Poiché le matrici di trasformazione 3D sono 4x4, gli errori di coerenza non produrrà alcun tipo di eccezione in fase di compilazione o anche in fase di esecuzione, è sufficiente che saranno operazioni vengono eseguite in modo imprevisto. Se il SceneKit / oggetti ARKit sembrano bloccarsi, veloce da subito o instabilità, una matrice di trasformazione non corretta è una buona opzione. La soluzione è semplice: [ `NMatrix4.Transpose` ](xref:OpenTK.NMatrix4.Transpose*) eseguirà una posto trasposizione di elementi.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio: l'analisi e rilevamento di oggetti 3D](https://developer.xamarin.com/samples/monotouch/iOS12/ScanningAndDetecting3DObjects/)
- [Novità in 2 ARKit (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/602/)
- [Understanding ARKit di rilevamento e il rilevamento (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/610/)
- [Introduzione a ARKit in xamarin. IOS](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/arkit/)
