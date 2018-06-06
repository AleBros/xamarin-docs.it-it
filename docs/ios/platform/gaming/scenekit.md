---
title: SceneKit in xamarin. IOS
description: Questo documento descrive SceneKit, una scena 3D API graph che semplifica l'utilizzo con la grafica 3D da astrarre le complessità delle OpenGL.
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: fb72e194e14f903061e1bd2dc6d04ef88ab429d4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786785"
---
# <a name="scenekit-in-xamarinios"></a>SceneKit in xamarin. IOS

SceneKit è un grafico di scena 3D API che semplifica l'utilizzo con la grafica 3D. È stata introdotta in OS X è 10.8 e che è ora giunto a iOS 8. Con SceneKit creare accattivanti visualizzazioni 3D e giochi 3D occasionali non richiede competenze in OpenGL. Sulla base di concetti di graph scena comuni, SceneKit estrae le complessità di OpenGL e OpenGL ES, rendendo molto più semplice aggiungere effetti 3D contenuto a un'applicazione. Tuttavia, se si è esperti OpenGL, SceneKit presenta ottimo supporto per l'associazione di direttamente con OpenGL anche. Inoltre, include numerose funzionalità che integrano la grafica 3D, ad esempio fisica e si integra molto bene con diversi altri framework di Apple, ad esempio Core animazione, immagine di base e Sprite Kit.

SceneKit è estremamente facile da usare. È un'API dichiarativa che si occupa di rendering. È sufficiente impostare una scena, aggiungere proprietà al componente c++ e gli handle SceneKit il rendering della scena.

Per funzionare con SceneKit si crea un grafico di scena mediante il `SCNScene` classe. Una scena contiene una gerarchia di nodi, rappresentati da istanze della `SCNNode`, che definisce i percorsi nello spazio 3D. Ogni nodo dispone di proprietà, ad esempio la geometria, illuminazione e i materiali che influiscono sull'aspetto, come illustrato nella figura seguente:

![](scenekit-images/image7.png "La gerarchia SceneKit") 

## <a name="create-a-scene"></a>Creare una scena

Per visualizzare una scena sullo schermo, viene aggiunto a un `SCNView` assegnarlo alla proprietà scena della vista. Inoltre, se si apportano modifiche alla scena, `SCNView` verrà aggiornata automaticamente per visualizzare le modifiche.

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

È possibile popolare automaticamente dai file esportati tramite un strumento di modellazione 3d, o a livello di codice da primitive geometriche. Ad esempio, questo viene illustrato come creare una sfera e aggiungerlo alla scena:

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>Aggiunta di luce

A questo punto la sfera non verrà visualizzato nulla, poiché non esiste alcun luce nella scena Collegamento `SCNLight` istanze ai nodi crea luci nella SceneKit. Esistono diversi tipi di luce comprese tra varie forme della luce direzionale e illuminazione. Ad esempio il codice seguente crea una luce omnidirezionale fianco sfera di:

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

Illuminazione omnidirezionale produce una riflessione diffusa conseguente sorta di illuminazione un anche, come bagliore sulla lente una torcia. Creazione di luce è simile, anche se non presenta una direzione specifica come si riflette in modo uniforme in tutte le direzioni. Essere considerato come uno stile di illuminazione :)

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

Con le luci sul posto, la sfera è ora visibile nella scena.

![](scenekit-images/image8.png "Sfera è visibile nella scena quando è acceso")
 
## <a name="adding-a-camera"></a>Aggiunta di una fotocamera

L'aggiunta di una fotocamera (SCNCamera) alla scena Modifica punto di vista. Il modello per aggiungere la fotocamera è simile. Creare la fotocamera, collegarlo a un nodo e aggiungere il nodo della scena.

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

Come si può notare dal codice precedente, SceneKit oggetti possono essere creati usando i costruttori o dal metodo factory Create. Il primo consente l'utilizzo di sintassi dell'inizializzatore di c#, ma quella da utilizzare è principalmente una questione di preferenze.

Con la fotocamera sul posto, l'intera sfera è visibile all'utente:

![](scenekit-images/image9.png "L'intera sfera è visibile all'utente")
 
È possibile aggiungere ulteriori luci alla scena anche. Ecco l'aspetto con pochi luci omnidirezionale più:

![](scenekit-images/image10.png "Sfera con alcune altre luci omnidirezionale")
 
Inoltre, impostando `sceneView.AllowsCameraControl = true`, l'utente può modificare il punto di vista con un gesto tocco.

### <a name="materials"></a>Materiali

Materiali sono creati con la classe SCNMaterial. Ad esempio per aggiungere un'immagine nell'area della sfera, impostare l'immagine per il materiale *diffuso* contenuto.

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

Questo livelli dell'immagine sul nodo come illustrato di seguito:

![](scenekit-images/image11.png "L'immagine su una sfera di sovrapposizione")
 
Per rispondere ad altri tipi di illuminazione troppo, è possibile impostare un materiale. Ad esempio, l'oggetto può essere reso brillante e avrà il relativo contenuto speculari impostati per la visualizzazione riflessione speculare, risultante in un punto di colore sulla superficie, come illustrato di seguito:

![](scenekit-images/image12.png "L'oggetto apportata lucente con riflessione speculare, risultante in un punto nell'area di colore")
 
Materiali sono molto flessibili, consentono di ottenere una grande quantità con codice molto ridotta. Ad esempio, anziché impostare l'immagine in base al contenuto diffuso, impostarla in base al contenuto riflette invece.

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

Ora il monkey sembra sit visivamente all'interno di settore, indipendentemente dal punto di vista.

### <a name="animation"></a>Animazione

SceneKit è progettato per funzionare bene con animazione. È possibile creare animazioni sia implicite o esplicite e può anche eseguire il rendering di una scena da una struttura di livello principale animazione. Quando si crea un'animazione implicita, SceneKit fornisce la propria classe transizione `SCNTransaction`.

Di seguito è riportato un esempio che ruota sfera di:

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

È possibile animare molto più di rotazione tuttavia. Molte proprietà di SceneKit sono supporta l'animazione. Ad esempio, il codice seguente aggiunge un'animazione il materiale `Shininess` per aumentare la riflessione speculare.

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

SceneKit è molto semplice da utilizzare. Offre una vasta gamma di funzionalità aggiuntive, quali i vincoli, fisica, azioni dichiarative, testo 3D, profondità del supporto di campo, integration Kit Sprite e l'integrazione di immagine di base per assegnare un nome di un numero limitato.