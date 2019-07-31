---
title: ARKit 2 in Novell. iOS
description: Questo documento descrive gli aggiornamenti di ARKit in iOS 12. Si concentra sull'uso di oggetti di riferimento e immagini per il rilevamento, include il codice per la texturing ambientale e illustra i problemi più comuni nella programmazione ARKit.
ms.prod: xamarin
ms.assetid: af758092-1523-4ab7-aa53-c37a81fb156a
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/22/2018
ms.openlocfilehash: 747eed60c40f7faee0ed7512d6db05116c81b50d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645742"
---
# <a name="arkit-2-in-xamarinios"></a>ARKit 2 in Novell. iOS

ARKit è maturato considerevolmente dall'introduzione dell'anno scorso in iOS 11. Prima di tutto, è ora possibile rilevare i piani verticali e orizzontali, che migliorano notevolmente la praticità delle esperienze di realtà aumentata internamente. Sono inoltre disponibili nuove funzionalità:

* Riconoscimento di immagini e oggetti di riferimento come giunzione tra il mondo reale e le immagini digitali
* Nuova modalità di illuminazione che simula l'illuminazione reale
* La possibilità di condividere e salvare in modo permanente gli ambienti AR
* Un nuovo formato di file preferito per l'archiviazione del contenuto AR

## <a name="recognizing-reference-objects"></a>Riconoscimento di oggetti di riferimento

Una funzionalità di presentazione di ARKit 2 è la possibilità di riconoscere immagini e oggetti di riferimento. Le immagini di riferimento possono essere caricate dai file di immagine normali ([descritte più avanti](#more-tracking-configurations)), ma gli oggetti di riferimento devono essere [`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration)analizzati, usando lo sviluppatore.

### <a name="sample-app-scanning-and-detecting-3d-objects"></a>App di esempio: Analisi e rilevamento di oggetti 3D

L'esempio [analisi e rilevamento oggetti 3D](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects) è una porta di un [progetto Apple](https://developer.apple.com/documentation/arkit/scanning_and_detecting_3d_objects?language=objc) che illustra:

* Gestione dello stato dell' [`NSNotification`](xref:Foundation.NSNotification) applicazione tramite oggetti
* Visualizzazione personalizzata
* Movimenti complessi
* Analisi degli oggetti
* Archiviazione di un[`ARReferenceObject`](xref:ARKit.ARReferenceObject)

L'analisi di un oggetto di riferimento è un utilizzo intensivo della batteria e del processore e i dispositivi meno recenti avranno spesso problemi a raggiungere il rilevamento stabile.

### <a name="state-management-using-nsnotification-objects"></a>Gestione dello stato mediante oggetti NSNotification

Questa applicazione usa una macchina a Stati che esegue la transizione tra gli Stati seguenti:

* `AppState.StartARSession`
* `AppState.NotReady`
* `AppState.Scanning`
* `AppState.Testing`

USA inoltre un set incorporato di Stati e transizioni quando in `AppState.Scanning`:

* `Scan.ScanState.Ready`
* `Scan.ScanState.DefineBoundingBox`
* `Scan.ScanState.Scanning`
* `Scan.ScanState.AdjustingOrigin`

L'app usa un'architettura reattiva che inserisce le notifiche di transizione dello [`NSNotificationCenter`](xref:Foundation.NSNotificationCenter) stato in e sottoscrive queste notifiche. Il programma di `ViewController.cs`installazione ha un aspetto simile al seguente:

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

Un tipico gestore di notifiche aggiornerà l'interfaccia utente ed eventualmente modificherà lo stato dell'applicazione, ad esempio questo gestore che aggiorna quando l'oggetto viene analizzato:

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

Infine, `Enter{State}` i metodi modificano il modello e l'esperienza utente in base alle esigenze del nuovo stato:

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

L'app mostra il "cloud punto" di basso livello dell'oggetto contenuto in un rettangolo di delimitazione proiettato su un piano orizzontale rilevato.

Questo cloud di punti è disponibile per gli sviluppatori [`ARFrame.RawFeaturePoints`](xref:ARKit.ARFrame.RawFeaturePoints) nella proprietà. La visualizzazione efficiente del cloud di punti può costituire un problema complesso. L'iterazione nei punti, quindi la creazione e l'inserimento di un nuovo nodo SceneKit per ogni punto comporta la terminazione della frequenza dei fotogrammi. In alternativa, se eseguita in modo asincrono, si verificherebbe un ritardo. L'esempio mantiene le prestazioni con una strategia in tre parti:

* Uso di codice unsafe per aggiungere i dati sul posto e interpretare i dati come buffer non elaborato di byte.
* Conversione del buffer non elaborato in [`SCNGeometrySource`](xref:SceneKit.SCNGeometrySource) un oggetto e creazione di un [`SCNGeometryElement`](xref:SceneKit.SCNGeometryElement) oggetto "template".
* "Unire" rapidamente i dati non elaborati e il modello usando[`SCNGeometry.Create(SCNGeometrySource[], SCNGeometryElement[])`](xref:SceneKit.SCNGeometry.Create(SceneKit.SCNGeometrySource[],SceneKit.SCNGeometryElement[]))

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

Il risultato è simile al seguente:

![point_cloud](images/arkit_point_cloud.jpeg)

### <a name="complex-gestures"></a>Movimenti complessi

L'utente può ridimensionare, ruotare e trascinare il rettangolo di selezione che racchiude l'oggetto di destinazione. Esistono due aspetti interessanti nei riconoscitori di movimento associati.

Prima di tutto, tutti i riconoscitori di movimento si attivano solo dopo che è stata superata una soglia; un dito, ad esempio, ha trascinato così tanti pixel o la rotazione supera un angolo. La tecnica consiste nell'accumulare lo spostamento fino a quando non viene superata la soglia, quindi applicarla in modo incrementale:

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

La seconda cosa interessante da fare in relazione ai movimenti è il modo in cui il rettangolo di delimitazione viene spostato in relazione ai piani reali rilevati. Questo aspetto è illustrato in [questo post di Blog di Novell](https://blog.xamarin.com/exploring-new-ios-12-arkit-capabilities-with-xamarin/).

## <a name="other-new-features-in-arkit-2"></a>Altre nuove funzionalità di ARKit 2

### <a name="more-tracking-configurations"></a>Altre configurazioni di rilevamento

A questo punto, è possibile usare uno degli elementi seguenti come base per un'esperienza di realtà mista:

* Solo l'accelerometro del[`AROrientationTrackingConfiguration`](xref:ARKit.AROrientationTrackingConfiguration)dispositivo (, iOS 11)
* Visi ([`ARFaceTrackingConfiguration`](xref:ARKit.ARFaceTrackingConfiguration), iOS 11)
* Immagini di riferimento[`ARImageTrackingConfiguration`](xref:ARKit.ARImageTrackingConfiguration)(, iOS 12)
* Analisi degli oggetti 3D[`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration)(, iOS 12)
* Visual Inertial odometry[`ARWorldTrackingConfiguration`](xref:ARKit.ARWorldTrackingConfiguration)(, migliorato in iOS 12)

`AROrientationTrackingConfiguration`, descritto in [questo post di Blog F# ed esempio](https://github.com/lobrien/FSharp_Face_AR), è il più limitato e fornisce un'esperienza di realtà mista, poiché inserisce solo oggetti digitali in relazione al movimento del dispositivo, senza tentare di collegare il dispositivo e lo schermo nel mondo reale.

`ARImageTrackingConfiguration` Consente di riconoscere immagini 2D reali (dipinti, logo e così via) e di usarle per ancorare le immagini digitali:

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

Questa configurazione presenta due aspetti interessanti:

* È efficiente e può essere usato con un numero potenzialmente elevato di immagini di riferimento
* Le immagini digitali sono ancorate all'immagine, anche se l'immagine viene spostata nel mondo reale, ad esempio se viene riconosciuta la copertura di un libro, il libro viene monitorato mentre viene tirato fuori dallo scaffale, è stato definito e così via.

È `ARObjectScanningConfiguration` stato illustrato [in precedenza](#recognizing-reference-objects) ed è una configurazione incentrata sugli sviluppatori per l'analisi degli oggetti 3D. È a elevato utilizzo di CPU e batteria e non deve essere usato nelle applicazioni degli utenti finali. Nell'esempio di [analisi e rilevamento di oggetti 3D](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects) viene illustrato l'utilizzo di questa configurazione.

La configurazione di rilevamento finale `ARWorldTrackingConfiguration` ,, è il cavallo di lavoro della maggior parte delle esperienze di realtà mista. Questa configurazione USA "Visual Inertial odometry" per correlare i punti "funzionalità reali" alle immagini digitali. La geometria o gli sprite digitali sono ancorati rispetto a piani orizzontali e verticali reali o relativi a istanze `ARReferenceObject` rilevate. In questa configurazione, l'origine mondiale è la posizione originale della fotocamera nello spazio con l'asse Z allineato alla gravità e gli oggetti digitali "rimanere sul posto" rispetto agli oggetti nel mondo reale.

### <a name="environmental-texturing"></a>Texturing ambientale

ARKit 2 supporta "texturing ambientale" che usa immagini acquisite per stimare l'illuminazione e persino applicare evidenziazioni speculari a oggetti lucidi. Il mappa cubi ambientale viene creato in modo dinamico e, una volta che la fotocamera ha esaminato tutte le direzioni, può produrre un'esperienza incredibilmente realistica:

![immagine della demo di texturing ambientale](images/arkit_env_texturing.png)

Per usare la texturing ambientale:

* Gli oggetti devono usare [`SCNLightingModel.PhysicallyBased`](xref:SceneKit.SCNLightingModel.PhysicallyBased) e assegnare un valore compreso tra 0 e 1 per [`Metalness.Contents`](xref:SceneKit.SCNMaterial.Metalness) e [`Roughness.Contents`e](xref:SceneKit.SCNMaterialProperty.Contents) [`SCNMaterial`](xref:SceneKit.SCNMaterial)
* La configurazione di rilevamento deve [`EnvironmentTexturing`](xref:ARKit.ARWorldTrackingConfiguration.EnvironmentTexturing) impostare  =  [`AREnvironmentTexturing.Automatic`](xref:ARKit.AREnvironmentTexturing.Automatic) :

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

Sebbene la trama perfettamente riflettente illustrata nel frammento di codice precedente sia divertente in un esempio, la texturing ambientale è probabilmente meglio usata con la funzione destraint per evitare che venga attivata una risposta "inquietante Valley" (la trama è solo una stima basata sulla fotocamera registrato).


### <a name="shared-and-persistent-ar-experiences"></a>Esperienze AR condivise e persistenti

Un'altra importante aggiunta a ARKit 2 è [`ARWorldMap`](xref:ARKit.ARWorldMap) la classe, che consente di condividere o archiviare dati di rilevamento internazionali. Si ottiene la mappa globale corrente con [`ARSession.GetCurrentWorldMapAsync`](xref:ARKit.ARSession.GetCurrentWorldMapAsync) o [`GetCurrentWorldMap(Action<ARWorldMap,NSError>)`](xref:ARKit.ARSession.GetCurrentWorldMap(System.Action{ARKit.ARWorldMap,Foundation.NSError})) :

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
2. Dearchiviarlo in un `ARWorldMap` oggetto,
3. Utilizzarlo come valore per la [`ARWorldTrackingConfiguration.InitialWorldMap`](xref:ARKit.ARWorldTrackingConfiguration.InitialWorldMap) proprietà:

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

L' `ARWorldMap` oggetto contiene solo dati [`ARAnchor`](xref:ARKit.ARAnchor) di rilevamento globale non visibili e oggetti, ma _non_ contiene risorse digitali. Per condividere la geometria o l'immagine, è necessario sviluppare una strategia appropriata per il caso d'uso, ad esempio archiviando/trasmettendo solo la posizione e l'orientamento della geometria e applicando `SCNGeometry` tale geometria a static o forse archiviando/trasmettendo oggetti serializzati). Il vantaggio di `ARWorldMap` è che le risorse, una volta posizionate rispetto a `ARAnchor`una condivisione, verranno visualizzate in modo coerente tra i dispositivi o le sessioni.

### <a name="universal-scene-description-file-format"></a>Formato del file di descrizione della scena universale

La funzionalità di titolo finale di ARKit 2 è l'adozione da parte di Apple del formato di file di [Descrizione della scena universale](https://graphics.pixar.com/usd/docs/Introduction-to-USD.html) di Pixar. Questo formato sostituisce il formato DAE di Collada come formato preferito per la condivisione e l'archiviazione degli asset ARKit. Il supporto per la visualizzazione degli asset è integrato in iOS 12 e Mojave. L'estensione di file USDZ è un archivio zip non compresso e non crittografato contenente i file USD. In Pixar sono [disponibili strumenti per l'utilizzo di file USD](https://graphics.pixar.com/usd/docs/USD-Toolset.html#USDToolset-usdedit) , ma non è ancora disponibile un supporto di terze parti.

## <a name="arkit-programming-tips"></a>Suggerimenti per la programmazione ARKit

### <a name="manual-resource-management"></a>Gestione manuale delle risorse

In ARKit è fondamentale gestire manualmente le risorse. Ciò consente non solo di eseguire una frequenza elevata di frame, ma è _necessario_ evitare una confusione di "blocco dello schermo". Il Framework ARKit è pigro per fornire un nuovo frame della fotocamera[`ARSession.CurrentFrame`](xref:ARKit.ARSession.CurrentFrame)(. Finché l'oggetto [`ARFrame`](xref:ARKit.ARFrame) corrente non `Dispose()` ha chiamato, ARKit non fornirà un nuovo frame. Questo fa sì che il video si blocchi anche se il resto dell'app risponde. La soluzione consiste nell'accedere `ARSession.CurrentFrame` sempre con un `using` blocco o chiamarlo `Dispose()` manualmente.

Tutti gli oggetti derivati `NSObject` da `NSObject` sono `IDisposable` e implementano il [modello Dispose](https://docs.microsoft.com/dotnet/standard/design-guidelines/dispose-pattern), pertanto è consigliabile attenersi in genere a [questo modello per l'implementazione `Dispose` di in una classe derivata](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose).

### <a name="manipulating-transform-matrices"></a>Modifica di matrici di trasformazione

In qualsiasi applicazione 3D, verranno affrontate le matrici di trasformazione 4x4 che descrivono in modo compatto come spostare, ruotare e tagliare un oggetto tramite lo spazio 3D. In SceneKit si [`SCNMatrix4`](xref:SceneKit.SCNMatrix4) tratta di oggetti.  

La [`SCNNode.Transform`](xref:SceneKit.SCNNode.Transform) proprietà restituisce la `SCNMatrix4` matrice di trasformazione per [`SCNNode`](xref:SceneKit.SCNNode) l'oggetto _come supportato dal_ tipo Row- `simdfloat4x4` Major. Quindi, ad esempio:

```csharp
var node = new SCNNode { Position = new SCNVector3(2, 3, 4) };  
var xform = node.Transform;
Console.WriteLine(xform);
// Output is: "(1, 0, 0, 0)\n(0, 1, 0, 0)\n(0, 0, 1, 0)\n(2, 3, 4, 1)"
```  

Come si può notare, la posizione è codificata nei primi tre elementi della riga inferiore.

In Novell, il tipo comune per la modifica delle matrici di `NVector4`trasformazione è, che per convenzione viene interpretato in un modo più importante. Ovvero, il componente Translation/position è previsto in M14, M24, M34, not M41, M42, M43:

![riga-principale vs colonna-principale](images/arkit_row_vs_column.png)

La coerenza con la scelta dell'interpretazione della matrice è essenziale per un comportamento appropriato. Poiché le matrici di trasformazione 3D sono 4x4, gli errori di coerenza non producono alcun tipo di eccezione in fase di compilazione o di esecuzione, ma solo le operazioni verranno eseguite in modo imprevisto. Se gli oggetti SceneKit/ARKit sembrano bloccati, abbandonati o jitter, una matrice di trasformazione non corretta è una valida possibilità. La soluzione è semplice: [`NMatrix4.Transpose`](xref:OpenTK.NMatrix4.Transpose*) eseguirà una Transposizione di elementi sul posto.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio: analisi e rilevamento di oggetti 3D](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-scanninganddetecting3dobjects)
- [Novità di ARKit 2 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/602/)
- [Informazioni sul rilevamento e rilevamento di ARKit (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/610/)
- [Introduzione a ARKit in Novell. iOS](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/arkit/)
