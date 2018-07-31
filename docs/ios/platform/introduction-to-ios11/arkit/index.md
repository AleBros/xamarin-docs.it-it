---
title: Introduzione a ARKit in xamarin. IOS
description: Questo documento descrive la realtà aumentata in iOS 11 con ARKit. Illustra come aggiungere un modello 3D per un'app, configurare la visualizzazione, implementare un delegato di sessione, posizionare il modello 3D in tutto il mondo e sospendere la sessione di realtà aumentata.
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2017
ms.openlocfilehash: 14bbb35477c098738e9cd7e2cb92154422d394ee
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350615"
---
# <a name="introduction-to-arkit-in-xamarinios"></a>Introduzione a ARKit in xamarin. IOS

_Realtà aumentata per iOS 11_

ARKit consente un'ampia gamma di applicazioni di realtà aumentata e giochi. In questa sezione vengono trattati i seguenti argomenti:

- [Guida introduttiva a ARKit](#gettingstarted)
- [Uso di ARKit con UrhoSharp](urhosharp.md)

<a name="gettingstarted" />

## <a name="getting-started-with-arkit"></a>Guida introduttiva a ARKit

Per iniziare a usare realtà aumentata, le istruzioni riportate di seguito illustra una semplice applicazione: posizionamento di un modello 3D e consentendo ARKit mantenere il modello con le sue funzionalità di rilevamento.

![Modello 3D di Jet a virgola mobile in immagine](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1. Aggiungere un modello 3D

Gli asset devono essere aggiunto al progetto con il **SceneKitAsset** azione di compilazione.

![In un progetto di asset SceneKit](images/scene-assets.png)


### <a name="2-configure-the-view"></a>2. Configurare la visualizzazione

Nel controller di visualizzazione `ViewDidLoad` metodo, caricare l'asset di scena e impostare il `Scene` proprietà nella vista:

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3. Se lo si desidera implementare un delegato di sessione

Sebbene non obbligatorio per i casi semplici, che implementa un delegato di sessione può essere utile per eseguire il debug lo stato della sessione ARKit (e in applicazioni reali, inviare commenti e suggerimenti all'utente). Creare un delegato semplice usando il codice seguente:

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

Assegnare il delegato nel nel `ViewDidLoad` metodo:

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4. Posizionare il modello 3D in tutto il mondo

In `ViewWillAppear`, il codice seguente consente di stabilire una sessione ARKit e imposta la posizione del modello 3D nello spazio in relazione la fotocamera del dispositivo:

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

Ogni volta che l'applicazione viene eseguito o ripreso, il modello 3D verrà posizionato davanti alla fotocamera. Una volta che si trova il modello, spostare la camera e osservare come ARKit mantiene il modello posizionato.

### <a name="5-pause-the-augmented-reality-session"></a>5. Sospendere la sessione di realtà aumentata

È buona norma per sospendere la sessione ARKit quando il controller di visualizzazione non è visibile (nel `ViewWillDisappear` metodo:

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>Riepilogo

Il codice precedente comporta una semplice applicazione ARKit. Esempi più complessi aspetta il controller di visualizzazione che ospita la sessione di realtà aumentata per implementare `IARSCNViewDelegate`, e implementare i metodi aggiuntivi.

ARKit offre molte delle funzionalità più sofisticate, ad esempio rilevamento superficie e l'interazione dell'utente. Vedere le [demo di UrhoSharp](urhosharp.md) per un esempio ARKit rilevamento con UrhoSharp combinazione.


## <a name="related-links"></a>Collegamenti correlati

- [Realtà aumentata (Apple)](https://developer.apple.com/arkit/)
- [Uso di ARKit con UrhoSharp](urhosharp.md)
- [Esempio ARKit semplice (Jet)](https://developer.xamarin.com/samples/monotouch/ios11/ARKitSample/)
- [ARKit inserimento di oggetti (esempio)](https://developer.xamarin.com/samples/monotouch/ios11/ARKitPlacingObjects/)
- [Introduzione a ARKit - realtà aumentata per iOS (WWDC) (video)](https://developer.apple.com/videos/play/wwdc2017/602/)
