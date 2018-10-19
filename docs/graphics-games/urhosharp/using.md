---
title: Uso di UrhoSharp per creare un gioco 3D
description: Questo documento viene fornita una panoramica di UrhoSharp, che descrivono scene, componenti, forme, fotocamere, azioni, input dell'utente, suono e altro ancora.
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 7d07733ebf62e6e12ccee05f9b72eaf1a74afad2
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2018
ms.locfileid: "34784039"
---
# <a name="using-urhosharp-to-build-a-3d-game"></a>Uso di UrhoSharp per creare un gioco 3D

Prima di scrivere il tuo primo gioco, si desidera acquisire familiarità con le nozioni di base: come configurare la scena, come caricare risorse (questo file contiene la grafica) e come creare semplici interazioni per il tuo gioco.

<a name="scenenodescomponentsandcameras"/>

## <a name="scenes-nodes-components-and-cameras"></a>Le scene, nodi, i componenti e videocamere

Il modello di scena può essere descritta come un grafico della scena in base al componente. La scena è costituito da una gerarchia di nodi della scena, a partire dal nodo radice, che rappresenta anche dell'intera scena. Ciascuna [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/) dispone di una trasformazione 3D (posizione, scala e rotazione), un nome, un ID, oltre a un numero arbitrario di componenti.  Componenti di portare un nodo di ciclo di vita, rendono possibile aggiungere una rappresentazione visiva ([`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel)), possono generare file audio ([`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource)), forniscono un limite di conflitto e così via.

È possibile creare scene e nodi di programma di installazione tramite il [Urho Editor](#UrhoEditor), oppure è possibile eseguire operazioni dal codice c#.  In questo documento verranno analizzati aspetti impostazione usando il codice, come illustrano gli elementi necessari per svolgere il proprio compaia sullo schermo

Oltre a configurare la scena, è necessario configurare un [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/), questo è ciò che determina ciò che verrà ottenere visualizzato all'utente.

### <a name="setting-up-your-scene"></a>Configurazione di scena

In genere è possibile creare questo modulo il metodo di avvio:

```csharp
var scene = new Scene ();
// Create the Octree component to the scene. This is required before
// adding any drawable components, or else nothing will show up. The
// default octree volume will be from -1000, -1000, -1000) to
//(1000, 1000, 1000) in world coordinates; it is also legal to place
// objects outside the volume but their visibility can then not be
// checked in a hierarchically optimizing manner
scene.CreateComponent<Octree> ();
// Create a child scene node (at world origin) and a StaticModel
// component into it. Set the StaticModel to show a simple plane mesh
// with a "stone" material. Note that naming the scene nodes is
// optional. Scale the scene node larger (100 x 100 world units)
var planeNode = scene.CreateChild("Plane");
planeNode.Scale = new Vector3 (100, 1, 100);
var planeObject = planeNode.CreateComponent<StaticModel> ();
planeObject.Model = ResourceCache.GetModel ("Models/Plane.mdl");
planeObject.SetMaterial(ResourceCache.GetMaterial("Materials/StoneTiled.xml"));
```

### <a name="components"></a>Componenti

Il rendering di oggetti 3D, la riproduzione audio, fisica e logica di script aggiornamenti tutti abilitata per la creazione di componenti diversi nei nodi chiamando [ `CreateComponent<T>()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateComponent%3CT%3E/p/Urho.CreateMode/System.UInt32/).  Ad esempio, configurare il nodo e dei componenti leggeri simile al seguente:

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

È stata creata di sopra di un nodo con il nome "`DirectionalLight`" e impostare una direzione per, ma nessun altro elemento.  È possibile tornare a questo punto, il nodo precedente in un nodo illuminante, collegando un [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/) componente, con `CreateComponent`:

```csharp
var light = lightNode.CreateComponent<Light>();
```

I componenti creati nel `Scene` può avere un ruolo speciale: per implementare la funzionalità a livello di scena. Essi devono essere creati prima di tutti gli altri componenti e includono quanto segue:

* [`Octree`](https://developer.xamarin.com/api/type/Urho.Octree/): implementa il partizionamento spaziali e con accelerazione delle query di visibilità. Senza questo 3D non possono essere rendering degli oggetti.
* [`PhysicsWorld`](https://developer.xamarin.com/api/type/Urho.Physics.PhysicsWorld/): implementa simulazione fisica. I componenti di fisica, ad esempio [ `RigidBody` ](https://developer.xamarin.com/api/type/Urho.Physics.RigidBody/) oppure [ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/) possono non funzionare correttamente senza questa opzione.
* [`DebugRenderer`](https://developer.xamarin.com/api/type/Urho.DebugRenderer/): implementa il debug per il rendering della geometria.

Componenti comuni, come [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light), [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera) o [`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel)
non devono essere create direttamente nel [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene), ma piuttosto in nodi figlio.

La libreria viene fornito con un'ampia gamma di componenti che è possibile collegare ai nodi per dare loro vita: visibile all'utente elementi (modelli), suoni, corpi rigidi, le forme di conflitti, fotocamere, sorgenti di luce, istanze di emissione FixIt particella e altro ancora.

### <a name="shapes"></a>Forme

Per maggiore praticità, sono disponibili come nodi semplici nello spazio dei nomi Urho.Shapes varie forme.  Sono inclusi finestre, sfere, coni, cilindri e piani.

### <a name="camera-and-viewport"></a>Fotocamere e Viewport

Esattamente come la luce, fotocamere sono componenti, pertanto sarà necessario collegare il componente a un nodo, questa operazione viene eseguita come segue:

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

Con questa impostazione, aver creato una fotocamera e inseriti la fotocamera nel mondo 3D, il passaggio successivo consiste nel comunicare il `Application` che si tratta della fotocamera che si desidera utilizzare, questa operazione viene eseguita con il codice seguente:

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

E a questo punto dovrebbe essere in grado di visualizzare i risultati della creazione del.

### <a name="identification-and-scene-hierarchy"></a>Identificazione e la scena gerarchia

A differenza dei nodi, i componenti non hanno nomi; i componenti all'interno dello stesso nodo vengono identificati solo per il tipo e indice nell'elenco dei componenti del nodo, che viene compilato in ordine di creazione, ad esempio, è possibile recuperare il [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light) componente fuori il `lightNode` oggetto precedenza simile al seguente:

```csharp
var myLight = lightNode.GetComponent<Light>();
```

È anche possibile ottenere un elenco di tutti i componenti recuperando il [ `Components` ](https://developer.xamarin.com/api/property/Urho.Node.Components/) che restituisce un `IList<Component>` che è possibile usare.

Quando si crea, i nodi e i componenti ottengono ID di tipo integer globale di scena. È possibile eseguire query dalla scena usando le funzioni [ `GetNode(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetNode/p/System.UInt32/) e [ `GetComponent(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetComponent/p/System.UInt32/). Ciò è molto più veloce rispetto all'utilizzo di ricorsiva, ad esempio le query di scena in base al nome del nodo.

Non è previsto predefinito di un'entità o un oggetto del gioco. è piuttosto fino al programmatore di decidere la gerarchia dei nodi e in quali nodi inserire qualsiasi logica di script. In genere, liberare-lo spostamento di oggetti nel mondo 3D verranno creati come elementi figlio del nodo radice. I nodi possono essere creati con o senza un nome usando [ `CreateChild()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateChild/p/System.String/Urho.CreateMode/System.UInt32/). Non viene applicata l'univocità dei nomi di nodo.

Ogni volta che si verifica alcuni composizione gerarchici, è consigliata (e in realtà necessaria, perché i componenti non hanno i propri trasformazioni 3D) per creare un nodo figlio.

Ad esempio se un carattere è stato premuto un oggetto a sua disposizione, l'oggetto deve avere un proprio nodo, che potrebbe essere associato a ossa mano del carattere (anche un [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/)).  L'eccezione è rappresentata la fisica [ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape), che può essere offsetted e viene ruotato singolarmente in relazione al nodo.

Si noti che [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Node/)di proprietari transform viene intenzionalmente ignorata come ottimizzazione durante il calcolo world derivato trasformazioni dei nodi figlio, pertanto la modifica non ha alcun effetto e deve essere lasciato invariato (posizione in corrispondenza dell'origine, nessuna rotazione di senza scalabilità.)

[`Scene`](https://developer.xamarin.com/api/type/Urho.Node/) i nodi possono essere liberamente riassociate come elementi padre. Al contrario componenti sempre appartengono al nodo collegato a e non possono essere spostati tra i nodi. I nodi e i componenti forniscono un [ `Remove()` ](https://developer.xamarin.com/api/member/Urho.Node.Remove()/) funzione per eseguire questa operazione senza dover passare attraverso l'elemento padre. Una volta che il nodo viene rimosso, dopo la chiamata di funzione non sono sicure operazioni del nodo o del componente in questione.

È anche possibile creare un `Node` che non appartiene a una scena. Ciò è utile, ad esempio con una fotocamera lo spostamento in una scena che possa essere caricata o salvata, perché quindi non verrà salvata con la scena effettiva della fotocamera e verrà eliminata quando viene caricata la scena. Tuttavia, se si utilizza la creazione di componenti di geometria, fisica o dello script a un nodo scollegato e quindi spostarli in una scena in un secondo momento, tali componenti non funzionino correttamente.

### <a name="scene-updates"></a>Aggiornamenti di scena

Una scena sono abilitati in cui gli aggiornamenti (impostazione predefinita) verrà aggiornata automaticamente a ogni iterazione del ciclo principale.  L'applicazione [ `SceneUpdate` ](https://developer.xamarin.com/api/event/Urho.Scene.SceneUpdate/) gestore eventi viene richiamato su di esso.

I nodi e i componenti possono essere esclusi dall'aggiornamento scena disabilitandole, vedere [ `Enabled` ](https://developer.xamarin.com/api/member/Urho.Node.Enabled).  Il comportamento dipende il componente specifico, ma, ad esempio la disabilitazione di un componente drawable anche lo rende invisibile, mentre la disabilitazione di un componente di origine audio viene disattivato. Se un nodo è disabilitato, tutti i relativi componenti vengono trattati come disabilitata indipendentemente dal fatto il proprio stato di attivazione/disattivazione.

## <a name="adding-behavior-to-your-components"></a>Aggiunta del comportamento per i componenti

Il modo migliore per strutturare il gioco è per rendere il componente che incapsula un attore o un elemento nel tuo gioco.  In questo modo la funzionalità autonoma, dagli asset usati per visualizzarlo, il relativo comportamento.

Il modo più semplice di aggiunta del comportamento a un componente è usare le azioni, di cui sono riportate le istruzioni che è possibile accodare e combinare che con la programmazione asincrona in c#.  Questo consente al comportamento per il componente sia di livello molto elevato e rende più semplice da capire cosa sta accadendo.

In alternativa, è possibile controllare esattamente cosa accade al componente aggiornando le proprietà del componente su ogni frame (descritto nella sezione comportamenti basati su Frame).

### <a name="actions"></a>Azioni

È possibile aggiungere il comportamento ai nodi molto facilmente usando le azioni.  Le azioni possono modificare varie proprietà del nodo ed eseguirli in un periodo di tempo o ripeterli un numero di volte con una curva di animazione specifica.

Si consideri ad esempio un nodo di "cloud" nella scena, eseguire una dissolvenza simile al seguente:

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

Le azioni sono oggetti non modificabili, che consente di riutilizzare l'azione per l'attivazione di oggetti diversi.

Un idioma comune consiste nel creare un'azione che esegue l'operazione inversa:

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

Nell'esempio seguente verrebbe dissolvenza l'oggetto per l'utente in un periodo di tre secondi.  È anche possibile eseguire un'azione dopo l'altro, ad esempio, è possibile innanzitutto spostare nel cloud e quindi nasconderlo:

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

Se si desidera che entrambe le azioni da eseguire sul posto allo stesso tempo, è possibile usare l'azione parallela e fornire tutte le azioni desiderate eseguite in parallelo:

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

Nell'esempio precedente il cloud verrà spostata e dissolvenza nello stesso momento.

Si noterà che si usa c# await, che consente di considerare il comportamento che si desidera ottenere in modo lineare.

### <a name="basic-actions"></a>Azioni di base

Queste sono le azioni supportate di UrhoSharp:

* Lo spostamento di nodi: [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo), [ `MoveBy` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveBy), [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place), [ `BezierTo` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierTo), [ `BezierBy` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierBy) , [`JumpTo`](https://developer.xamarin.com/api/type/Urho.Actions.JumpTo), [`JumpBy`](https://developer.xamarin.com/api/type/Urho.Actions.JumpBy)
* Rotazione di nodi: [ `RotateTo` ](https://developer.xamarin.com/api/type/Urho.Actions.RotateTo), [`RotateBy`](https://developer.xamarin.com/api/type/Urho.Actions.RotateBy)
* Il ridimensionamento dei nodi: [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo), [`ScaleBy`](https://developer.xamarin.com/api/type/Urho.Actions.ScaleBy)
* A dissolvenza nodi: [ `FadeIn` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeIn), [ `FadeTo` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeTo), [ `FadeOut` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeOut), [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide), [`Blink`](https://developer.xamarin.com/api/type/Urho.Actions.Blink)
* Tinte: [ `TintTo` ](https://developer.xamarin.com/api/type/Urho.Actions.TintTo), [`TintBy`](https://developer.xamarin.com/api/type/Urho.Actions.TintBy)
* Istanti: [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide), [ `Show` ](https://developer.xamarin.com/api/type/Urho.Actions.Show), [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place), [ `RemoveSelf` ](https://developer.xamarin.com/api/type/Urho.Actions.RemoveSelf), [`ToggleVisibility`](https://developer.xamarin.com/api/type/Urho.Actions.ToggleVisibility)
* Cicli: [ `Repeat` ](https://developer.xamarin.com/api/type/Urho.Actions.Repeat), [ `RepeatForever` ](https://developer.xamarin.com/api/type/Urho.Actions.RepeatForever), [`ReverseTime`](https://developer.xamarin.com/api/type/Urho.Actions.ReverseTime)

Altre funzionalità avanzate includono la combinazione dei [ `Spawn` ](https://developer.xamarin.com/api/type/Urho.Actions.Spawn) e [ `Sequence` ](https://developer.xamarin.com/api/type/Urho.Actions.Sequence) azioni.

### <a name="easing---controlling-the-speed-of-your-actions"></a>Interpolazione - controllare la velocità delle azioni

Interpolazione è un modo che indirizza il modo in cui verrà Espandi l'animazione e animazioni può risultare molto più piacevole.  Per impostazione predefinita le azioni avrà un comportamento lineare, ad esempio un [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo) azione sarebbe necessario uno spostamento molto robotico.  È possibile eseguire il wrapping di azioni in un'azione di interpolazione per modificare il comportamento, ad esempio, quella più lentamente iniziare lo spostamento, accelerare e lenta raggiunge la fine ([`EasyInOut`](https://developer.xamarin.com/api/type/Urho.Actions.EasyInOut)).

Eseguire questa operazione eseguendo il wrapping di un'azione esistente in un'azione di interpolazione, ad esempio:

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

Esistono molti modalità di interpolazione, il grafico seguente illustra i vari tipi di interpolazione e il relativo comportamento sul valore dell'oggetto che controlla il periodo di tempo, dall'inizio alla fine di:

![Modalità di interpolazione](using-images/easing.png "questo grafico illustra i vari tipi di interpolazione e il relativo comportamento sul valore dell'oggetto che controlla il periodo di tempo")

### <a name="using-actions-and-async-code"></a>Usando le azioni e il codice asincrono

Nel [ `Component` ](https://developer.xamarin.com/api/type/Urho.Component/) sottoclasse, è consigliabile introdurre un metodo asincrono che prepara il comportamento del componente e indirizza la funzionalità per tale.
Quindi è possibile chiamare questo metodo usando il linguaggio c# `await` parola chiave da un'altra parte del programma, entrambi i `Application.Start` (metodo) o in risposta a una storia utente o un punto all'interno dell'applicazione.

Ad esempio:

```csharp
class Robot : Component {
    public bool IsAlive;
    async void Launch ()
    {
        // Dress up our robot
        var cache = Application.ResourceCache;
        var model = node.CreateComponent<StaticModel>();
        model.Model = cache.GetModel ("robot.mdl"));
        model.SetMaterial (cache.GetMaterial ("robot.xml"));
        Node.SetScale (1);

        // Bring the robot into our scene
        await Node.RunActionsAsync(
            new MoveBy(duration: 0.6f, position: new Vector3(0, -2, 0)));
        // Make him move around to avoid the user fire
        MoveRandomly(minX: 1, maxX: 2, minY: -3, maxY: 3, duration: 1.5f);
        // And simultaneously have him shoot at the user
        StartShooting();
    }

    protected async void MoveRandomly (float minX, float maxX,
                                       float minY, float maxY,
                       float duration)
    {
        while (IsAlive){
            var moveAction = new MoveBy(duration,
                new Vector3(RandomHelper.NextRandom(minX, maxX),
                            RandomHelper.NextRandom(minY, maxY), 0));
            await Node.RunActionsAsync(moveAction, moveAction.Reverse());
        }
    }
    protected async void StartShooting()
    {
        while (IsAlive && Node.Components.Count > 0){
            foreach (var weapon in Node.Components.OfType<Weapon>()){
                await weapon.FireAsync(false);
                if (!IsAlive)
                    return;
            }
            await Node.RunActionsAsync(new DelayTime(0.1f));
        }
    }
}
```

Nel `Launch` metodo sopra tre azioni siano avviati: il robot entra in scena, questa azione verrà modificata la posizione del nodo in un periodo di 0,6 secondi.  Poiché si tratta di un'opzione async, ciò si verifica contemporaneamente come l'istruzione successiva che viene chiamata per `MoveRandomly`.  Questo metodo verrà modificata la posizione del robot in parallelo in una posizione casuale.  A questo scopo, eseguire due operazioni composta, lo spostamento in una nuova posizione e tornare all'istanza originale di posizionare e ripetere l'operazione fino a quando il robot rimane attivo.  E per rendere le cose più interessanti, sarà di mantenere contemporaneamente ripresa il robot.  Le riprese verrà avviato solo ogni 0,1 secondi.

### <a name="frame-based-behavior-programming"></a>Programmazione di comportamenti basati su frame

Se si desidera controllare il comportamento del componente in una base di ciascun frame singolarmente invece di usare le azioni, le operazioni consiste nel sostituire la [ `OnUpdate` ](https://developer.xamarin.com/api/member/Urho.Component.OnUpdate) metodo per il [ `Component` ](https://developer.xamarin.com/api/type/Urho.Component) sottoclasse.  Questo metodo viene richiamato una volta per ogni fotogramma e viene richiamato solo se si imposta la proprietà ReceiveSceneUpdates su true.

Di seguito viene illustrato come creare un `Rotator` componente, che viene quindi collegato a un nodo che fa sì che il nodo ruotare:

```csharp
class Rotator : Component {
    public Rotator()
    {
        ReceiveSceneUpdates = true;
    }
    public Vector3 RotationSpeed { get; set; }
    protected override void OnUpdate(float timeStep)
    {
        Node.Rotate(new Quaternion(
            RotationSpeed.X * timeStep,
            RotationSpeed.Y * timeStep,
            RotationSpeed.Z * timeStep),
            TransformSpace.Local);
    }
}
```

Si tratta di come si sarebbe associare questo componente a un nodo:

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

### <a name="combining-styles"></a>La combinazione degli stili

È possibile usare il modello basato su async/azione per la programmazione di gran parte del comportamento che è ideale per lo stile fire-and-forget della programmazione, ma è possibile anche ottimizzare il comportamento del componente per l'esecuzione anche un codice di aggiornamento in ogni fotogramma.

Ad esempio, nella demo SamplyGame viene utilizzato nel `Enemy` classe consente di codificare le azioni di utilizzi di comportamento di base, ma garantisce anche che i componenti puntino verso l'utente tramite l'impostazione della direzione del nodo con `Node.LookAt`:

```csharp
    protected override void OnUpdate(SceneUpdateEventArgs args)
    {
        Node.LookAt(
            new Vector3(0, -3, 0),
            new Vector3(0, 1, -1),
            TransformSpace.World);
        base.OnUpdate(args);
    }
```

## <a name="loading-and-saving-scenes"></a>Il caricamento e salvataggio scene

In background possono essere caricati e salvati in formato XML; vedere le funzioni [ `LoadXml` ](https://developer.xamarin.com/api/member/Urho.Scene.LoadXml) e [ `SaveXML()` ](https://developer.xamarin.com/api/member/Urho.Scene.SaveXml). Quando viene caricata una scena, viene innanzitutto rimosso tutto il contenuto esistente in esso (nodi figlio e i componenti). I nodi e i componenti contrassegnati temporanei con il `Temporary` proprietà non verrà salvata. Il serializzatore gestisce tutti i componenti predefiniti e le proprietà ma non è in grado di gestire le proprietà personalizzate e dei campi definiti nelle sottoclassi del componente. Tuttavia fornisce due metodi virtuali per questo:

* [`OnSerialize`](https://developer.xamarin.com/api/member/Urho.Component.OnSerialize) in cui è possibile registrare è stati personalizzati per la serializzazione

* [`OnDeserialized`](https://developer.xamarin.com/api/member/Urho.Component.OnDeserialize) in cui è possibile ottenere gli stati di personalizzato salvati.

In genere, un componente personalizzato avrà un aspetto simile al seguente:

```csharp
class MyComponent : Component {
    // Constructor needed for deserialization
    public MyComponent(IntPtr handle) : base(handle) { }
    public MyComponent() { }
    // user defined properties (managed state):
    public Quaternion MyRotation { get; set; }
    public string MyName { get; set; }

    public override void OnSerialize(IComponentSerializer ser)
    {
        // register our properties with their names as keys using nameof()
        ser.Serialize(nameof(MyRotation), MyRotation);
        ser.Serialize(nameof(MyName), MyName);
    }

    public override void OnDeserialize(IComponentDeserializer des)
    {
        MyRotation = des.Deserialize<Quaternion>(nameof(MyRotation));
        MyName = des.Deserialize<string>(nameof(MyName));
    }
    // called when the component is attached to some node
    public override void OnAttachedToNode()
    {
        var node = this.Node;
    }
}
```

### <a name="object-prefabs"></a>Oggetto Prefabs

Solo il caricamento o salvataggio intere scene non è sufficientemente flessibile per i giochi in cui devono essere creati dinamicamente nuovi oggetti. D'altra parte, la creazione di oggetti complessi e impostando le proprietà nel codice sarà anche noiose. Per questo motivo, è anche possibile salvare un nodo di scena che includerà i relativi nodi figlio, i componenti e gli attributi. Questi possono essere caricati in un secondo momento per praticità come gruppo.  Un oggetto salvato di questo tipo è noto anche come un prefab. A questo scopo, è possibile eseguire tre operazioni:

- Nel codice chiamando [ `Node.SaveXml` ](https://developer.xamarin.com/api/member/Urho.Node.SaveXml) nel nodo
- Nell'editor, selezionando il nodo nella finestra della gerarchia e scegliere "Salva il nodo come" dal menu "File".
- Usando il comando "nodo" in `AssetImporter`, che verrà salvata la gerarchia della scena nodo e tutti i modelli contenuti nell'asset di input (ad es. file Collada)

Per creare un'istanza del nodo salvato in una scena, chiamare [ `InstantiateXml()` ](https://developer.xamarin.com/api/member/Urho.Scene.InstantiateXml). Il nodo verrà creato come elemento figlio della scena, ma può essere liberamente riassociate come elementi padre in seguito. Posizione e la rotazione per l'inserimento del nodo deve essere specificato. Il codice seguente viene illustrato come creare un'istanza di un prefab `Ninja.xm` a una scena con posizione desiderata e la rotazione:

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

## <a name="events"></a>Eventi

UrhoObjects generano un numero di eventi, queste vengono rilevate come eventi c# nelle varie classi che generano.  Oltre a c#-modello basato su eventi, è anche possibile usare una di `SubscribeToXXX` metodi che ti permetterà di "subscribe" e mantenere un token di sottoscrizione che è possibile in un secondo momento usare per annullare la sottoscrizione.  La differenza è che il primo consentirà chiamanti molti per la sottoscrizione, mentre il secondo solo consente uno, ma lo stile di lambda coloro approccio da usare e consente ancora, la semplice rimozione della sottoscrizione.  Questi si escludono a vicenda.

Quando si sottoscrive un evento, è necessario fornire un metodo che accetta un argomento con gli argomenti dell'evento appropriato.

Ad esempio, si tratta come sottoscrivere un evento del pulsante del mouse:

```csharp
public void override Start ()
{
    UI.MouseButtonDown += HandleMouseButtonDown;
}

void HandleMouseButtonDown(MouseButtonDownEventArgs args)
{
    Console.WriteLine ("button pressed");
}
```

Con stile lambda:

```csharp
public void override Start ()
{
    UI.MouseButtonDown += args => {
        Console.WriteLine ("button pressed");
    };
}
```

In alcuni casi è opportuno per non ricevere più notifiche per l'evento, in questi casi, è possibile risparmiare il valore restituito dalla chiamata a `SubscribeTo` metodo e richiamare il metodo di annullamento della sottoscrizione su di essa:

```csharp
Subscription mouseSub;

public void override Start ()
{
    mouseSub = UI.SubscribeToMouseButtonDown (args => {
    Console.WriteLine ("button pressed");
      mouseSub.Unsubscribe ();
    };
}
```

Il parametro ricevuto dal gestore dell'evento è una classe di argomenti evento fortemente tipizzato che sarà specifiche per ogni evento e contiene il payload dell'evento.

## <a name="responding-to-user-input"></a>Risponde all'Input dell'utente

È possibile sottoscrivere eventi diversi, ad esempio le sequenze di tasti verso il basso per la sottoscrizione all'evento e risponde all'input inviati:

```csharp
Start ()
{
    UI.KeyDown += HandleKeyDown;
}

void HandleKeyDown (KeyDownEventArgs arg)
{
     switch (arg.Key){
     case Key.Esc: Engine.Exit (); return;
}
```

Ma in molti scenari, si desidera che i gestori aggiornamento scena per controllare lo stato corrente delle chiavi quando sono in corso l'aggiornamento e aggiornare di conseguenza il codice.  Ad esempio, di seguito è utilizzabile per aggiornare la posizione della fotocamera in base l'input da tastiera:

```csharp
protected override void OnUpdate(float timeStep)
{
    Input input = Input;
    // Movement speed as world units per second
    const float moveSpeed = 4.0f;
    // Read WASD keys and move the camera scene node to the
    // corresponding direction if they are pressed
    if (input.GetKeyDown(Key.W))
        CameraNode.Translate(Vector3.UnitY * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.S))
        CameraNode.Translate(new Vector3(0.0f, -1.0f, 0.0f) * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.A))
        CameraNode.Translate(new Vector3(-1.0f, 0.0f, 0.0f) * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.D))
        CameraNode.Translate(Vector3.UnitX * moveSpeed * timeStep, TransformSpace.Local);
}
```

## <a name="resources-assets"></a>Risorse (Asset)

Le risorse includono la maggior parte delle operazioni di UrhoSharp caricati da archiviazione di massa durante l'inizializzazione o di runtime:

- [`Animation`](https://developer.xamarin.com/api/type/Urho.Animation/) -usato per le animazioni di base
- [`Image`](https://developer.xamarin.com/api/type/Urho.Resources.Image) -rappresenta immagini archiviate in un'ampia gamma di formati di immagini
- [`Model`](https://developer.xamarin.com/api/type/Urho.Model/) -Modelli 3D
- [`Material`](https://developer.xamarin.com/api/type/Urho.Material) -i materiali utilizzati per eseguire il rendering di modelli.
- [`ParticleEffect`](https://developer.xamarin.com/api/type/Urho.ParticleEffect)- [descrive](http://urho3d.github.io/documentation/1.4/_particles.html) funzionamento di una funzione di emissione di particelle, vedere "[particelle](#particles)" di seguito.
- [`Shader`](https://developer.xamarin.com/api/type/Urho.Shader) -shader personalizzati
- [`Sound`](https://developer.xamarin.com/api/type/Urho.Audio.Sound) -suoni per la riproduzione, vedere "[suono](#sound)" di seguito.
- [`Technique`](https://developer.xamarin.com/api/type/Urho.Technique/) -le tecniche per il rendering material
- [`Texture2D`](https://developer.xamarin.com/api/type/Urho.Urho2D.Texture2D/) -Trama 2D
- [`Texture3D`](https://developer.xamarin.com/api/type/Urho.Texture3D/) -Trama 3D
- [`TextureCube`](https://developer.xamarin.com/api/type/Urho.TextureCube/) -Trama cubo
- `XmlFile`

Sono gestiti e caricati per la [ `ResourceCache` ](https://developer.xamarin.com/api/type/Urho.Resources.ResourceCache/) sottosistema (disponibile come [ `Application.ResourceCache` ](https://developer.xamarin.com/api/property/Urho.Application.ResourceCache/)).

Le risorse stesse vengono identificate mediante i relativi percorsi di file, relativo alla directory di risorse registrati o file del pacchetto. Per impostazione predefinita, il motore registra la directory di risorse `Data` e `CoreData`, o i pacchetti `Data.pak` e `CoreData.pak` se presenti.

Se il caricamento di una risorsa ha esito negativo, verrà registrato un errore e viene restituito un riferimento null.

Nell'esempio seguente viene illustrato il tipico di recuperare una risorsa dalla cache delle risorse.  In questo caso, una trama per un elemento dell'interfaccia utente, questo metodo utilizza il `ResourceCache` proprietà dal `Application` classe.

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

Risorse possono anche essere create manualmente e memorizzate nella cache di risorse come se fossero stati caricati dal disco.

I budget di memoria possono essere impostati per ogni tipo di risorsa: se le risorse usano più memoria consentito, le risorse meno recente verranno rimossa dalla cache se non vengono installati in uso più. Per impostazione predefinita i budget di memoria vengano impostati su illimitato.

### <a name="bringing-3d-models-and-images"></a>Portare le immagini e modelli 3D

Urho3D tenta di usare formati di file esistente quando possibile e definire formati di file personalizzati solo se assolutamente necessario, ad esempio per i modelli (*.mdl) e per le animazioni (* ANI). Per questi tipi di asset, Urho fornisce un convertitore - [AssetImporter](http://urho3d.github.io/documentation/1.4/_tools.html) che possono usare molti formati 3D più diffusi, ad esempio fbx, dae, 3ds e obj e così via.

È inoltre disponibile un componente aggiuntivo utile per Blender [ https://github.com/reattiva/Urho3D-Blender ](https://github.com/reattiva/Urho3D-Blender) che possono esportare gli asset di Blender in formato adatto per Urho3D.

### <a name="background-loading-of-resources"></a>Caricamento in background delle risorse

In genere, la richiesta di risorse usando uno dei `ResourceCache`del `Get` metodo vengano caricate immediatamente nel thread principale, che potrebbero essere necessari alcuni millisecondi per tutti i passaggi necessari (caricare file dal disco, analizzare i dati, carica GPU se necessario ) e può pertanto comportare la frequenza dei fotogrammi scende.

Se si conosce in anticipo le risorse a cui è necessario, è possibile richiedere loro di essere caricati in un thread in background chiamando `BackgroundLoadResource()`. È possibile sottoscrivere l'evento caricato in Background di risorse usando il `SubscribeToResourceBackgroundLoaded` (metodo). Questo cmdlet indicherà se il caricamento è stato effettivamente un esito positivo o un errore. A seconda della risorsa, solo una parte del processo di caricamento può essere spostato in un thread in background, ad esempio la fase di caricamento GPU terminando deve sempre essere eseguita nel thread principale. Si noti che se si chiama uno dei metodi per una risorsa che viene accodato per il caricamento in background di caricamento risorsa, il thread principale richiederà fino a quando il caricamento è completo.

Funzionalità di caricamento asincrona scena `LoadAsync()` e `LoadAsyncXML()` prevede l'opzione di caricamento in background le risorse prima di passare al caricamento del contenuto di scena. È anche utilizzabile per caricare solo le risorse senza modificare la scena, specificando il `LoadMode.ResourcesOnly`. Ciò consente di preparare un file prefab scena o dell'oggetto per la creazione di istanze veloce.

Infine il tempo massimo (in millisecondi) impiegato per ogni fotogramma su completato in background le risorse caricate possono essere configurate impostando il `FinishBackgroundResourcesMs` proprietà il `ResourceCache`.

<a name="sound"/>

## <a name="sound"></a>File audio

File audio è una parte importante del gioco e il framework di UrhoSharp offre un modo per la riproduzione di suoni nel gioco.  Riprodurre suoni mediante l'aggiunta di un [`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource/)
componente a un [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node) e quindi la riproduzione di un file denominato provenienti dalle risorse.

Si tratta di come farlo:

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"/>

## <a name="particles"></a>Particelle

Le particelle forniscono un modo semplice per aggiungere alcuni effetti semplice e conveniente all'applicazione.  È possibile utilizzare le particelle archiviate in formato PEX, mediante strumenti quali [ http://onebyonedesign.com/flash/particleeditor/ ](http://onebyonedesign.com/flash/particleeditor/).

Le particelle sono componenti che possono essere aggiunti a un nodo.  È necessario chiamare il nodo `CreateComponent<ParticleEmitter2D>` metodo per creare la particella e quindi configurare la particella impostando la proprietà Effect un effetto 2D che viene caricato dalla cache delle risorse.

Ad esempio, è possibile richiamare questo metodo nel componente per mostrare alcuni particelle che vengono visualizzate come un'esplosione quando raggiunge:

```csharp
public async void Explode (Component target)
{
    // show a small explosion when the missile reaches an aircraft.
    var cache = Application.ResourceCache;
    var explosionNode = Scene.CreateChild();
    explosionNode.Position = target.Node.WorldPosition;
    explosionNode.SetScale(1f);
    var particle = explosionNode.CreateComponent<ParticleEmitter2D>();
    particle.Effect = cache.GetParticleEffect2D("explosion.pex");
    var scaleAction = new ScaleTo(0.5f, 0f);
    await explosionNode.RunActionsAsync(
        scaleAction, new DelayTime(0.5f));
    explosionNode.Remove();
}
```

Il codice precedente creerà un nodo dell'esplosione che è collegato al componente corrente, all'interno di questo nodo esplosione si crea una funzione di emissione particella 2D e lo configura impostando la proprietà Effect.  Eseguiamo due azioni, uno che ridimensiona il nodo per essere più piccoli e uno che lascia le dimensioni specificate per 0,5 secondi.  È quindi possibile rimuovere l'esplosione, che rimuove inoltre l'effetto particellare dalla schermata.

Quando si usa una trama sfera la particella riportato sopra esegue il rendering simile al seguente:

![Le particelle con una trama sfera](using-images/image-1.png "la particella riportato sopra esegue il rendering come segue quando si usa una trama sfera")

E questo è il risultato se si usa una trama del:

![Le particelle con una trama di casella](using-images/image-2.png "e questo è il risultato se si usa una trama del")

## <a name="multithreading-support"></a>Supporto del multithreading

UrhoSharp è una libreria a thread singola.  Ciò significa che è consigliabile evitare di richiamare i metodi di UrhoSharp da un thread in background, o si rischia di danneggiare lo stato dell'applicazione e probabilmente di arresto anomalo dell'applicazione.

Se si desidera eseguire il codice in background e quindi aggiornare i componenti Urho nell'interfaccia utente principale, è possibile usare il [`Application.InvokeOnMain(Action)`](https://developer.xamarin.com/api/member/Urho.Application.InvokeOnMain)
ProcessOnStatus.  Inoltre, è possibile usare await c# e .NET API per garantire che il codice venga eseguito sul thread appropriato di attività.

## <a name="urhoeditor"></a>UrhoEditor

È possibile scaricare l'Editor Urho per la piattaforma nella [sito Web Urho](http://urho3d.github.io/), passare a download e selezionare la versione più recente.

## <a name="copyrights"></a>Copyright

Questa documentazione vengono forniti contenuti originale da Xamarin Inc, ma traccia ampiamente dalla documentazione open source per il progetto Urho3D e è riportate le schermate dal progetto Cocos2D.
