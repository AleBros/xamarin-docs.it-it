---
title: Introduzione a ARKit in Novell. iOS
description: Questo documento descrive la realtà aumentata in iOS 11 con ARKit. Viene illustrato come aggiungere un modello 3D a un'app, configurare la visualizzazione, implementare un delegato della sessione, posizionare il modello 3D nel mondo e sospendere la sessione di realtà aumentata.
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/30/2017
ms.openlocfilehash: 47c092215afef4aa6964a39f7dcb5b685d98a4fc
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655727"
---
# <a name="introduction-to-arkit-in-xamarinios"></a>Introduzione a ARKit in Novell. iOS

_Realtà aumentata per iOS 11_

ARKit consente un'ampia gamma di applicazioni e giochi di realtà aumentata. Questa sezione contiene gli argomenti seguenti:

- [Introduzione con ARKit](#gettingstarted)
- [Uso di ARKit con UrhoSharp](urhosharp.md)

<a name="gettingstarted" />

## <a name="getting-started-with-arkit"></a>Introduzione con ARKit

Per iniziare a usare la realtà aumentata, le istruzioni seguenti illustrano in modo dettagliato una semplice applicazione: posizionare un modello 3D e consentire a ARKit di tenere il modello con la funzionalità di rilevamento.

![Modello Jet 3D a virgola mobile nell'immagine della fotocamera](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1. Aggiungere un modello 3D

Gli asset devono essere aggiunti al progetto con l'azione di compilazione **SceneKitAsset** .

![Asset SceneKit in un progetto](images/scene-assets.png)


### <a name="2-configure-the-view"></a>2. Configurare la visualizzazione

Nel `ViewDidLoad` metodo del controller di visualizzazione caricare l'asset della scena e impostare la `Scene` proprietà nella vista:

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3. Implementare facoltativamente un delegato della sessione

Sebbene non sia necessario per i casi semplici, l'implementazione di un delegato della sessione può essere utile per il debug dello stato della sessione ARKit (e in applicazioni reali, che fornisce commenti e suggerimenti all'utente). Creare un delegato semplice usando il codice seguente:

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

Assegnare il delegato in nel `ViewDidLoad` metodo:

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4. Posizionare il modello 3D nel mondo

In `ViewWillAppear`il codice seguente stabilisce una sessione ARKit e imposta la posizione del modello 3D nello spazio relativo alla fotocamera del dispositivo:

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

Ogni volta che l'applicazione viene eseguita o ripresa, il modello 3D verrà posizionato davanti alla fotocamera. Quando il modello è posizionato, spostare la fotocamera e osservare come ARKit mantiene il modello posizionato.

### <a name="5-pause-the-augmented-reality-session"></a>5. Sospendere la sessione di realtà aumentata

È consigliabile sospendere la sessione ARKit quando il controller di visualizzazione non è visibile (nel `ViewWillDisappear` metodo:

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>Riepilogo

Il codice precedente genera una semplice applicazione ARKit. Esempi più complessi si aspettano che il controller di visualizzazione che ospita la sessione di `IARSCNViewDelegate`realtà aumentata implementi e che vengano implementati altri metodi.

ARKit offre numerose funzionalità più sofisticate, ad esempio il rilevamento della superficie e l'interazione dell'utente. Vedere la [demo UrhoSharp](urhosharp.md) per un esempio di combinazione del rilevamento ARKit con UrhoSharp.


## <a name="related-links"></a>Collegamenti correlati

- [Realtà aumentata (Apple)](https://developer.apple.com/arkit/)
- [Uso di ARKit con UrhoSharp](urhosharp.md)
- [Esempio di ARKit (Jet) semplice](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-arkitsample)
- [ARKit posizionamento di oggetti (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-arkitplacingobjects)
- [Introduzione a ARKit-Augmented Reality for iOS (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/602/)
