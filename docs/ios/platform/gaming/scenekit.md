---
title: SceneKit in xamarin. IOS
description: Questo documento descrive SceneKit, un grafico della scena 3D API che semplifica l'utilizzo di grafica 3D per astrarre le complessità di OpenGL.
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 0944978b34c8e164acd6e829db177bf4fd72dea9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109861"
---
# <a name="scenekit-in-xamarinios"></a>SceneKit in xamarin. IOS

SceneKit è un grafico della scena 3D API che semplifica l'utilizzo di grafica 3D. È stata introdotta in OS X 10.8 e ha ora iniziato a iOS 8. Con SceneKit creazione coinvolgenti visualizzazioni 3D e casual games 3D non richiede competenze in OpenGL. Sulla base di concetti comuni di graph scena, SceneKit consente di evitare le complessità correlate OpenGL e OpenGL ES, rendendo molto più semplice aggiungere 3D contenuto a un'applicazione. Tuttavia, se sei un esperto di OpenGL, SceneKit ha un notevole supporto per l'associazione di direttamente con OpenGL anche. Inoltre include numerose funzionalità che integrano quelli grafica 3D, ad esempio fisica e si integra molto bene con diversi altri framework di Apple, ad esempio Core Animation, Core immagine e Spritekit.

SceneKit è estremamente facile da usare. Si tratta di un'API dichiarativa che si occupa di rendering. Semplicemente impostato una scena, aggiungere le proprietà e gli handle di SceneKit il rendering della scena.

Per lavorare con SceneKit si crea un grafo di scena tramite il `SCNScene` classe. Una scena contiene una gerarchia di nodi, rappresentati da istanze della `SCNNode`, definire posizioni nello spazio 3D. Ogni nodo dispone di proprietà, ad esempio la geometria, illuminazione e materiali che ne modificano l'aspetto, come illustrato nella figura seguente:

![](scenekit-images/image7.png "La gerarchia di SceneKit") 

## <a name="create-a-scene"></a>Creare una scena

Per visualizzare una scena sullo schermo, viene aggiunto a un `SCNView` assegnando alla VM per la proprietà della visualizzazione scena. Inoltre, se si apportano modifiche alla scena, `SCNView` si aggiornerà per visualizzare le modifiche.

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

In background possono essere popolati dal file esportati tramite un strumento di modellazione 3d o a livello di codice da primitive geometriche. Ad esempio, questo viene illustrato come creare una sfera e aggiungerlo alla scena:

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>Aggiunta di luce

A questo punto la sfera non verrà visualizzato alcun elemento perché non è nessuna luce nella scena. Collegamento `SCNLight` istanze ai nodi consente di creare luci in SceneKit. Sono disponibili tipi diversi di luci che vanno da diverse forme di illuminazione direzionale alla luce di ambiente. Ad esempio il codice seguente crea un chiaro omnidirezionale fianco sfera di:

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

Illuminazione omnidirezionale produce una reflection con riflessione diffusa causando un'illuminazione anche, specie di, ad esempio cercare di sottrarci un torcia. Creazione di luce di ambiente è simile, anche se non ha alcuna direzione come lo si distingue in modo uniforme in tutte le direzioni. Considerare questa operazione, ad esempio uno stile di illuminazione :)

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

Con le luci posto, la sfera è ora visibile nella scena.

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

Come si può notare dal codice precedente, gli oggetti possono essere creati usando i costruttori di SceneKit o dal metodo factory Create. Il primo consente l'uso di C# sintassi dell'inizializzatore, ma è possibile utilizzare è sostanzialmente una questione di preferenza.

Con la fotocamera posto, l'intera sfera è visibile all'utente:

![](scenekit-images/image9.png "L'intera sfera è visibile all'utente")
 
È possibile aggiungere ulteriori luci anche nella scena. Ecco l'effetto con pochi luci omnidirezionale altre:

![](scenekit-images/image10.png "La sfera con pochi luci omnidirezionale altre")
 
Inoltre, tramite l'impostazione `sceneView.AllowsCameraControl = true`, l'utente può modificare il punto di vista con un gesto tocco.

### <a name="materials"></a>Materiali

I materiali vengono creati con la classe SCNMaterial. Ad esempio per aggiungere un'immagine nell'area della sfera, impostare l'immagine per il materiale *diffuso* contenuto.

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

Ciò tutti i livelli dell'immagine sul nodo come illustrato di seguito:

![](scenekit-images/image11.png "L'immagine nella sfera di sovrapposizione")
 
Un materiale può essere impostato per rispondere ad altri tipi di illuminazione troppo. Ad esempio, l'oggetto può essere reso shiny e avrà il suo contenuto speculari impostato per la visualizzazione reflection speculari, risultante in un vivace punto sulla superficie, come illustrato di seguito:

![](scenekit-images/image12.png "L'oggetto apportata shiny con riflessione speculare, risultante in un punto sull'area del brillante")
 
Materiali sono molto flessibili, consentendo di ottenere molto con poco codice. Ad esempio, anziché impostare l'immagine in base al contenuto di diffuso, in base al contenuto riflettente invece impostarla.

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

Viene ora visualizzato il monkey aspettare visivamente nella sfera, indipendentemente dal punto di vista.

### <a name="animation"></a>Animazione

SceneKit è progettato per funzionare bene con animazione. È possibile creare animazioni sia implicite o esplicite e può anche eseguire il rendering di una scena da una struttura ad albero di Core Animation livello. Quando si crea un'animazione implicita, SceneKit fornisce una propria classe di transizione, `SCNTransaction`.

Di seguito è riportato un esempio che ruota la sfera:

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

È possibile animare molto più di rotazione tuttavia. Molte proprietà di SceneKit sono animata. Ad esempio, il codice seguente aggiunge un'animazione del materiale `Shininess` per aumentare la reflection speculare.

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

SceneKit è molto semplice da utilizzare. Offre un'ampia gamma di funzionalità aggiuntive, inclusi i vincoli, physics, azioni dichiarative, testo 3D, profondità del supporto di campo, Spritekit integrazione e l'integrazione di immagine di base per citarne solo alcuni.