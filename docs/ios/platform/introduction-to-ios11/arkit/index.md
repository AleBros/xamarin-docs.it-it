---
title: Introduzione a ARKit
description: "Realtà aumentata per iOS 11"
ms.topic: article
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2016
ms.openlocfilehash: 1655d426a0181c88479eef2bdea909eb5935e642
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-arkit"></a>Introduzione a ARKit

_Realtà aumentata per iOS 11_

ARKit consente un'ampia gamma di giochi e applicazioni realtà aumentata. In questa sezione vengono trattati i seguenti argomenti:

- [Introduzione a ARKit](#gettingstarted)
- [Con ARKit UrhoSharp](urhosharp.md)

<a name="gettingstarted" />

## <a name="getting-started-with-arkit"></a>Introduzione a ARKit

Per informazioni introduttive su realtà aumentata, le istruzioni seguenti illustrata una semplice applicazione: posizionamento di un modello 3D e consentendo ARKit di mantenere il modello con le funzionalità di rilevamento.

![Modello di Jet 3D Mobile in immagine](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1. Aggiungere un modello 3D

Asset devono essere aggiunti al progetto con il **SceneKitAsset** azione di compilazione.

![SceneKit asset in un progetto](images/scene-assets.png)


### <a name="2-configure-the-view"></a>2. Configurare la visualizzazione

Il controller di visualizzazione `ViewDidLoad` (metodo), caricare la risorsa di scena e impostare il `Scene` proprietà nella vista:

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3. È possibile implementare un delegato di sessione

Anche se non è necessaria nei casi semplici, l'implementazione di un delegato di sessione può essere utile per il debug lo stato della sessione ARKit (e nelle applicazioni reali, inviare commenti e suggerimenti all'utente). Creare un delegato semplice tramite il codice riportato di seguito:

```csharp
public class SessionDelegate : ARSessionDelegate
{
  public SessionDelegate() {}
  public override void CameraDidChangeTrackingState(ARSession session, ARCamera camera)
  {
    Console.WriteLine("{0} {1}", camera.TrackingState, camera.TrackingStateReason);
  }
}
```

Assegnare il delegato nel `ViewDidLoad` metodo:

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4. Posizionare il modello 3D in tutto il mondo

In `ViewWillAppear`, il codice seguente consente di stabilire una sessione ARKit e imposta la posizione del modello 3D nello spazio relativo della fotocamera del dispositivo:

```csharp
// Create a session configuration
var configuration = new ARWorldTrackingConfiguration {
  PlaneDetection = ARPlaneDetection.Horizontal,
  LightEstimationEnabled = true
};

// Run the view's session
SceneView.Session.Run(configuration, ARSessionRunOptions.ResetTracking);

// Find the ship and position it just in front of the camera
var ship = SceneView.Scene.RootNode.FindChildNode("ship", true);

ship.Position = new SCNVector3(2f, -2f, -9f);
```

Ogni volta che l'applicazione viene eseguito o ripreso, il modello 3D verrà posizionato davanti la fotocamera. Una volta che si trova il modello, spostare la camera e osservare come ARKit mantiene il modello posizionato.

### <a name="5-pause-the-augmented-reality-session"></a>5. Sospendere la sessione realtà aumentata

È consigliabile sospendere la sessione ARKit quando il controller di visualizzazione non è visibile (nel `ViewWillDisappear` metodo:

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>Riepilogo

Il codice precedente comporta una semplice applicazione ARKit. Esempi più complessi si aspetta che ospita la sessione realtà aumentata per implementare il controller di visualizzazione `IARSCNViewDelegate`, e implementare i metodi aggiuntivi.

ARKit offre molte delle funzionalità più sofisticate, ad esempio rilevamento superficie e l'interazione dell'utente. Vedere il [UrhoSharp demo](urhosharp.md) per un esempio la combinazione di rilevamento con UrhoSharp ARKit.


## <a name="related-links"></a>Collegamenti correlati

- [Realtà aumentata (mela)](https://developer.apple.com/arkit/)
- [Con ARKit UrhoSharp](urhosharp.md)
- [Esempio semplice ARKit (Jet)](https://developer.xamarin.com/samples/monotouch/ios11/ARKitSample/)
- [ARKit inserimento di oggetti (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/ARKitPlacingObjects/)
- [Introduzione a ARKit - realtà aumentata per iOS (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/602/)
