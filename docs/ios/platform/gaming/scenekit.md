---
title: SceneKit in Xamarin.iOS
description: Questo documento descrive SceneKit, un'API Graph della scena 3D che semplifica l'uso della grafica 3D astraendone le complessità di OpenGL.
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 5279effa83a8784f6d475188e67a535f7b5e1262
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032491"
---
# <a name="scenekit-in-xamarinios"></a>SceneKit in Xamarin.iOS

SceneKit è un'API Graph della scena 3D che semplifica l'uso della grafica 3D. È stata introdotta per la prima volta in OS X 10,8 ed è ora disponibile in iOS 8. Con SceneKit, la creazione di visualizzazioni 3D immersive e giochi 3D casuali non richiede esperienza in OpenGL. Basandosi sui concetti comuni relativi ai grafici delle scene, SceneKit estrae le complessità di OpenGL e OpenGL, semplificando l'aggiunta di contenuto 3D a un'applicazione. Tuttavia, se si è un esperto OpenGL, SceneKit offre un ottimo supporto per il collegamento diretto con OpenGL. Include inoltre numerose funzionalità che integrano grafica 3D, ad esempio la fisica, e si integra molto bene con diversi altri framework Apple, ad esempio Core Animation, Core Image e sprite Kit.

SceneKit è estremamente facile da usare. Si tratta di un'API dichiarativa che si occupa del rendering. È sufficiente impostare una scena, aggiungervi proprietà e SceneKit gestisce il rendering della scena.

Per usare SceneKit è possibile creare un grafico della scena usando la classe `SCNScene`. Una scena contiene una gerarchia di nodi, rappresentati da istanze di `SCNNode`, definendo posizioni nello spazio 3D. Ogni nodo dispone di proprietà quali geometria, illuminazione e materiali che incidono sull'aspetto, come illustrato nella figura seguente:

![](scenekit-images/image7.png "The SceneKit hierarchy")

## <a name="create-a-scene"></a>Creare una scena

Per visualizzare una scena sullo schermo, aggiungerla a un `SCNView` assegnando il valore alla proprietà della scena della visualizzazione. Inoltre, se si apportano modifiche alla scena, `SCNView` si aggiornerà per visualizzare le modifiche.

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

Le scene possono essere popolate da file esportati tramite uno strumento di modellazione 3D oppure a livello di codice dalle primitive geometriche. Ad esempio, questo è come creare una sfera e aggiungerla alla scena:

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>Aggiunta di luce

A questo punto, la sfera non visualizzerà nulla perché non è presente alcuna luce nella scena. Il fissaggio di `SCNLight` istanze ai nodi crea luci in SceneKit. Ci sono diversi tipi di luci che variano da diverse forme di illuminazione direzionale a illuminazione ambientale. Il codice seguente, ad esempio, crea una luce omnidirezionale sul lato della sfera:

```csharp
// omnidirectional light
var light = SCNLight.Create ();
var lightNode = SCNNode.Create ();
light.LightType = SCNLightType.Omni;
light.Color = UIColor.Blue;
lightNode.Light = light;
lightNode.Position = new SCNVector3 (-40, 40, 60);
scene.RootNode.AddChildNode (lightNode);
```

L'illuminazione omnidirezionale genera una reflection diffusa che produce un'illuminazione uniforme, simile a una torcia luminosa. La creazione di un ambiente chiaro è simile, anche se non ha alcuna direzione, perché si distingue in modo uniforme in tutte le direzioni. Pensa a questo tipo di illuminazione:)

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

Con la luce sul posto, la sfera è ora visibile nella scena.

![](scenekit-images/image8.png "The sphere is visible in the scene when lit")

## <a name="adding-a-camera"></a>Aggiunta di una fotocamera

Quando si aggiunge una fotocamera (SCNCamera) alla scena, viene modificato il punto di visualizzazione. Il modello per aggiungere la fotocamera è simile. Creare la fotocamera, collegarla a un nodo e aggiungere il nodo alla scena.

```csharp
// camera
camera = new SCNCamera {
    XFov = 80,
    YFov = 80
};
cameraNode = new SCNNode {
    Camera = camera,
    Position = new SCNVector3 (0, 0, 40)
};
scene.RootNode.AddChildNode (cameraNode);
```

Come si può notare dal codice precedente, gli oggetti SceneKit possono essere creati usando i costruttori o dal metodo Create Factory. Il primo consente di C# usare la sintassi dell'inizializzatore, ma quello da usare è in gran parte una cosa preferenziale.

Con la fotocamera sul posto, l'intera sfera è visibile all'utente:

![](scenekit-images/image9.png "The entire sphere is visible to the user")

È anche possibile aggiungere altre luci alla scena. Ecco il risultato dell'aspetto con alcune altre luci omnidirezionali:

![](scenekit-images/image10.png "The sphere with a few more omnidirectional lights")

Inoltre, impostando `sceneView.AllowsCameraControl = true`, l'utente può modificare il punto di visualizzazione con un movimento tocco.

### <a name="materials"></a>Materiali

I materiali vengono creati con la classe SCNMaterial. Ad esempio, per aggiungere un'immagine nella superficie della sfera, impostare l'immagine sul contenuto *diffuso* del materiale.

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

In questo modo l'immagine viene sovrapposta al nodo come illustrato di seguito:

![](scenekit-images/image11.png "Layering the image onto the sphere")

È possibile impostare un materiale per rispondere ad altri tipi di illuminazione. Ad esempio, l'oggetto può essere reso lucido e il relativo contenuto speculare è impostato in modo da visualizzare la reflection speculare, ottenendo una posizione luminosa sull'area, come mostrato di seguito:

![](scenekit-images/image12.png "The object made shiny with specular reflection, resulting in a bright spot on the surface")

I materiali sono molto flessibili e consentono di ottenere molto poco codice. Ad esempio, invece di impostare l'immagine sul contenuto diffuso, impostarlo sul contenuto riflettente.

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

Ora la scimmia sembra posizionarsi visivamente all'interno della sfera, indipendentemente dal punto di vista.

### <a name="animation"></a>Animazione

SceneKit è progettato per funzionare correttamente con l'animazione. È possibile creare animazioni sia implicite che esplicite ed è anche possibile eseguire il rendering di una scena da un albero del livello di animazione principale. Quando si crea un'animazione implicita, SceneKit fornisce una classe di transizione personalizzata, `SCNTransaction`.

Di seguito è riportato un esempio che ruota la sfera:

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

Tuttavia, è possibile animare molto più della rotazione. Molte proprietà di SceneKit sono animabili. Il codice seguente, ad esempio, anima le `Shininess` del materiale per aumentare la reflection speculare.

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

SceneKit è molto semplice da usare. Offre un'ampia gamma di funzionalità aggiuntive, tra cui vincoli, fisica, azioni dichiarative, testo 3D, supporto della profondità dei campi, integrazione di sprite kit e integrazione di immagini di base per citarne alcuni.
