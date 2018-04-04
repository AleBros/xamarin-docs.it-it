---
title: Utilizzando UrhoSharp
description: Panoramica del motore di UrhoSharp
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: cdb32c0fe9aa1a267bda5768b9026667723d694c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="using-urhosharp"></a>Utilizzando UrhoSharp

_Panoramica del motore di UrhoSharp_

Prima di scrivere un gioco prima, si desidera ottenere è a conoscenza di base: come scena del programma di installazione, come caricare risorse (contiene la grafica) e come creare interazioni semplice per il gioco.

<a name="scenenodescomponentsandcameras"/>

# <a name="scenes-nodes-components-and-cameras"></a>Automaticamente, i nodi, componenti e fotocamere

Il modello di scena può essere descritta come un grafico basato su componenti. Scena è costituito da una gerarchia di nodi della scena, a partire dal nodo radice, che rappresenta anche la scena intera. Ogni [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/) dispone di una trasformazione 3D (posizione, rotazione e scala), un nome, un ID, oltre a un numero arbitrario di componenti.  Componenti di portare un nodo alla durata, rendono possibile aggiungere una rappresentazione visiva ([`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel)), possono generare suono ([`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource)), forniscono un limite di conflitto e così via.

È possibile creare le scene e nodi di programma di installazione utilizzando il [Urho Editor](#UrhoEditor), oppure è possibile eseguire operazioni dal codice c#.  In questo documento verranno analizzati operazioni impostazione mediante il codice, come illustrano gli elementi necessari per ottenere elementi da visualizzare sullo schermo

Oltre a configurare scena, è necessario configurare un [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/), questo è ciò che determina le informazioni che verranno ottenere visualizzate all'utente.

## <a name="setting-up-your-scene"></a>Impostazione della scena

In genere è possibile creare questo form del metodo di avvio:

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

## <a name="components"></a>Componenti

Il rendering di oggetti 3D, la riproduzione, fisica e logica tramite script aggiornamenti sono tutte abilitati creando componenti diversi nei nodi chiamando [ `CreateComponent<T>()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateComponent%3CT%3E/p/Urho.CreateMode/System.UInt32/).  Installazione, ad esempio, nodo e il componente chiaro simile al seguente:

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

È stata creata in precedenza a un nodo con il nome "`DirectionalLight`" e impostare una direzione per, ma nessun altro elemento.  È possibile tornare a questo punto, il nodo precedente in un nodo illuminante, collegando un [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/) componente, con `CreateComponent`:

```csharp
var light = lightNode.CreateComponent<Light>();
```

I componenti creati nel `Scene` dispone di un ruolo speciale: per implementare la funzionalità a livello di scena. Essi devono essere creati prima di tutti gli altri componenti e includono quanto segue:

* [`Octree`](https://developer.xamarin.com/api/type/Urho.Octree/): implementa il partizionamento spaziali e l'accelerazione delle query di visibilità. Senza questo 3D oggetti non possono essere visualizzati.
* [`PhysicsWorld`](https://developer.xamarin.com/api/type/Urho.Physics.PhysicsWorld/): implementa simulazione fisica. I componenti di fisica, ad esempio [ `RigidBody` ](https://developer.xamarin.com/api/type/Urho.Physics.RigidBody/) o [ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/) non può funzionare correttamente senza questa opzione.
* [`DebugRenderer`](https://developer.xamarin.com/api/type/Urho.DebugRenderer/): implementa debug per il rendering della geometria.

Componenti comuni, come [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light), [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera) o [ `StaticModel` ](https://developer.xamarin.com/api/type/Urho.StaticModel) non possono essere create direttamente nel [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene), ma piuttosto in nodi figlio.

La libreria viene fornito con un'ampia gamma di componenti che è possibile associare ai nodi per portarli vita: elementi visibile dall'utente (modelli), suoni, corpi rigida, le forme di conflitti, fotocamere, sorgenti di luce, funzioni di emissione di particelle e molto altro ancora.

## <a name="shapes"></a>Forme

Per comodità, sono disponibili come nodi semplici nello spazio dei nomi Urho.Shapes varie forme.  Sono incluse finestre, sfere, coni, cilindri e piani.

## <a name="camera-and-viewport"></a>Fotocamera e Viewport

Analogamente, la luce fotocamere sono componenti, pertanto è necessario associare il componente a un nodo, questa operazione viene eseguita come segue:

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

Con questa operazione, aver creato una fotocamera e inserita la fotocamera nel mondo 3D, il passaggio successivo consiste nell'informare il `Application` che si tratta della fotocamera che si desidera utilizzare, questa operazione viene eseguita con il codice seguente:

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

E a questo punto dovrebbe essere in grado di visualizzare i risultati della creazione del.

## <a name="identification-and-scene-hierarchy"></a>Gerarchia di identificazione e scena

A differenza dei nodi, i componenti non hanno nomi; i componenti all'interno dello stesso nodo solo sono identificati dal loro tipo e un indice nell'elenco dei componenti del nodo viene compilato in ordine di creazione, ad esempio, è possibile recuperare il [ `Light` ](https://developer.xamarin.com/api/type/Urho.Light) componente fuori il `lightNode` oggetto sopra come segue:

```csharp
var myLight = lightNode.GetComponent<Light>();
```

È anche possibile ottenere un elenco di tutti i componenti recuperando il [ `Components` ](https://developer.xamarin.com/api/property/Urho.Node.Components/) proprietà che restituisce un `IList<Component>` che è possibile utilizzare.

Creazione, i nodi e i componenti ottengono ID di tipo integer scena globali. È possibile eseguire query dalla schermata utilizzando le funzioni [ `GetNode(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetNode/p/System.UInt32/) e [ `GetComponent(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetComponent/p/System.UInt32/). Questo è molto più veloce rispetto all'utilizzo di ricorsiva, ad esempio le query di nodo di scena basato sul nome.

Non è previsto predefinita di un'entità o di un oggetto di gioco. è invece fino al programmatore di decidere della gerarchia dei nodi e in cui i nodi per inserire la logica di qualsiasi script. In genere, verrebbero creati lo spostamento liberare gli oggetti 3D come elementi figlio del nodo radice. Nodi possono essere creati con o senza un nome utilizzando [ `CreateChild()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateChild/p/System.String/Urho.CreateMode/System.UInt32/). Non viene applicata l'univocità dei nomi di nodo.

Tutte le volte che alcuni composizione gerarchici, è consigliato (e in realtà è necessaria, perché i componenti non dispone di propri trasformazioni 3D) per creare un nodo figlio.

Ad esempio se un carattere è stato premuto un oggetto in mano, l'oggetto deve disporre di un proprio nodo, che potrebbe essere associato a ossa mano del carattere (anche un [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/)).  L'eccezione è il fisica [ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape), che può essere offsetted e ruotato singolarmente in relazione al nodo.

Si noti che [ `Scene` ](https://developer.xamarin.com/api/type/Urho.Node/)del proprietario di trasformazione viene ignorata intenzionalmente per motivi di ottimizzazione durante il calcolo delle trasformazioni world derivato di nodi figlio, pertanto la modifica non ha alcun effetto e deve essere lasciato come (posizione di origine, nessuna rotazione alcun ridimensionamento.)

[`Scene`](https://developer.xamarin.com/api/type/Urho.Node/) i nodi possono essere liberamente riassociati come padre. Al contrario componenti sempre appartengono al nodo collegato e non possono essere spostati tra i nodi. I nodi e i componenti forniscono un [ `Remove()` ](https://developer.xamarin.com/api/member/Urho.Node.Remove()/) funzione a tale scopo, senza dover passare attraverso il padre. Dopo il nodo viene rimosso, nessuna operazione sul nodo o componente in questione sono sicura dopo la chiamata di funzione.

È anche possibile creare un `Node` che non appartiene a una scena. Ciò è utile, ad esempio con una fotocamera lo spostamento in una scena che può essere caricata o salvata, in quanto quindi la fotocamera non verrà salvata con la scena effettiva e verrà eliminata quando viene caricata la scena. Tuttavia, se si utilizza la creazione di componenti di geometria, fisica o dello script a un nodo non collegato e quindi spostarlo in una scena in un secondo momento, tali componenti non funzionare correttamente.

## <a name="scene-updates"></a>Aggiornamenti di scena

Una scena sono abilitati in cui gli aggiornamenti (impostazione predefinita) verrà aggiornata automaticamente a ogni iterazione del ciclo principale.  L'applicazione [ `SceneUpdate` ](https://developer.xamarin.com/api/event/Urho.Scene.SceneUpdate/) gestore dell'evento viene richiamato su di esso.

I nodi e i componenti possono essere esclusi dall'aggiornamento scena da disabilitazione, vedere [ `Enabled` ](https://developer.xamarin.com/api/member/Urho.Node.Enabled).  Il comportamento dipende dal componente specifico, ma ad esempio la disattivazione di un componente drawable rende invisibile, mentre la disabilitazione di un componente di origine audio viene disattivato il. Se un nodo è disabilitato, tutti i relativi componenti vengono trattati come disattivata indipendentemente dal proprio stato di abilitare o disabilitare.

# <a name="adding-behavior-to-your-components"></a>Aggiunta di un comportamento per i componenti

Il modo migliore per strutturare il gioco è per rendere il proprio componente che incapsulano un attore o un elemento in un gioco.  In questo modo la funzionalità autonoma, uno degli asset consentono di visualizzare, il relativo comportamento.

Il modo più semplice di aggiunta di un comportamento a un componente consiste nell'utilizzare azioni che rappresentano le istruzioni che è possibile accodare e combinando i risultati con la programmazione asincrona in c#.  Questo consente il comportamento del componente di livello molto elevato e rende più semplice da capire cosa succede.

In alternativa, è possibile controllare esattamente cosa accade per il componente aggiornando le proprietà del componente su ogni frame (descritto nella sezione di comportamento basato su Frame).

## <a name="actions"></a>Azioni

È possibile aggiungere il comportamento ai nodi molto facilmente usando le azioni.  Azioni possono modificare varie proprietà di nodo ed eseguirli in un periodo di tempo o ripeterli un numero di volte con una curva di animazione.

Si consideri ad esempio un nodo della scena "cloud", è possibile applicare la dissolvenza simile al seguente:

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

Le azioni sono oggetti non modificabili, che consente di riutilizzare l'azione per la Guida di oggetti diversi.

Un idioma comune consiste nel creare un'azione che esegue l'operazione inversa:

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

Nell'esempio seguente sarebbe dissolvenza l'oggetto per l'utente in un periodo di tre secondi.  È anche possibile eseguire un'azione dopo l'altro, ad esempio, è possibile innanzitutto spostare nel cloud e quindi nasconderlo:

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

Se si desidera entrambe le azioni da eseguire sul posto allo stesso tempo, è possibile utilizzare l'azione parallela e fornire tutte le operazioni verranno eseguite in parallelo:

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

Nell'esempio precedente il cloud verrà spostata e dissolvenza nello stesso momento.

Si noterà che si utilizza c# await, che consente di considerare il comportamento che si desidera ottenere in modo lineare.

## <a name="basic-actions"></a>Azioni di base

Queste sono le azioni supportate in UrhoSharp:

* Lo spostamento di nodi: [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo), [ `MoveBy` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveBy), [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place), [ `BezierTo` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierTo), [ `BezierBy` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierBy) , [`JumpTo`](https://developer.xamarin.com/api/type/Urho.Actions.JumpTo), [`JumpBy`](https://developer.xamarin.com/api/type/Urho.Actions.JumpBy)
* Rotazione di nodi: [ `RotateTo` ](https://developer.xamarin.com/api/type/Urho.Actions.RotateTo), [`RotateBy`](https://developer.xamarin.com/api/type/Urho.Actions.RotateBy)
* Ridimensionamento di nodi: [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo), [`ScaleBy`](https://developer.xamarin.com/api/type/Urho.Actions.ScaleBy)
* I nodi di dissolvenza: [ `FadeIn` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeIn), [ `FadeTo` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeTo), [ `FadeOut` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeOut), [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide), [`Blink`](https://developer.xamarin.com/api/type/Urho.Actions.Blink)
* Colorazione: [ `TintTo` ](https://developer.xamarin.com/api/type/Urho.Actions.TintTo), [`TintBy`](https://developer.xamarin.com/api/type/Urho.Actions.TintBy)
* Iniziale dell'intervallo: [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide), [ `Show` ](https://developer.xamarin.com/api/type/Urho.Actions.Show), [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place), [ `RemoveSelf` ](https://developer.xamarin.com/api/type/Urho.Actions.RemoveSelf), [`ToggleVisibility`](https://developer.xamarin.com/api/type/Urho.Actions.ToggleVisibility)
* Ciclo: [ `Repeat` ](https://developer.xamarin.com/api/type/Urho.Actions.Repeat), [ `RepeatForever` ](https://developer.xamarin.com/api/type/Urho.Actions.RepeatForever), [`ReverseTime`](https://developer.xamarin.com/api/type/Urho.Actions.ReverseTime)

Altre funzionalità avanzate per includere la combinazione di [ `Spawn` ](https://developer.xamarin.com/api/type/Urho.Actions.Spawn) e [ `Sequence` ](https://developer.xamarin.com/api/type/Urho.Actions.Sequence) azioni.

## <a name="easing---controlling-the-speed-of-your-actions"></a>Interpolazione - la velocità delle azioni di controllo

Interpolazione è un modo che indirizza il modo in cui l'animazione verrà unfold e le animazioni può rendere molto più piacevole.  Per impostazione predefinita le azioni avrà un comportamento lineare, ad esempio un [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo) azione avrebbe un movimento molto automatica.  È possibile eseguire il wrapping le operazioni eseguite in un'azione di variazione velocità per modificare il comportamento, ad esempio, uno che lenta iniziare lo spostamento, consente di accelerare e lenta raggiunge la fine ([`EasyInOut`](https://developer.xamarin.com/api/type/Urho.Actions.EasyInOut)).

Questo caso eseguendo il wrapping di un'azione esistente in un'azione di interpolazione, ad esempio:

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

Esistono molti modalità di interpolazione, il grafico seguente illustra i vari tipi di interpolazione e il relativo comportamento al valore dell'oggetto che controlla il periodo di tempo, dall'inizio alla fine:

![Modalità di interpolazione](using-images/easing.png "questo grafico mostra i diversi tipi di interpolazione e il relativo comportamento al valore dell'oggetto che controlla il periodo di tempo")

## <a name="using-actions-and-async-code"></a>Utilizzo di azioni e codice asincrono

Nel [ `Component` ](https://developer.xamarin.com/api/type/Urho.Component/) sottoclasse, è consigliabile introdurre un metodo asincrono che prepara il comportamento del componente e l'unità la funzionalità relativa.
Quindi è possibile chiamare questo metodo Usa c# `await` (parola chiave) da un'altra parte del programma, entrambi i `Application.Start` metodo o in risposta a una storia utente o un punto dell'applicazione.

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

Nel `Launch` metodo sopra tre azioni siano avviati: robot proviene nella scena, questa operazione modificherà il percorso del nodo in un periodo di 0,6 secondi.  Poiché si tratta di un'opzione asincrono, questo evento si verifica contemporaneamente come l'istruzione successiva che rappresenta la chiamata a `MoveRandomly`.  Questo metodo comporta la modifica la posizione del robot in parallelo per una posizione casuale.  A questo scopo, eseguire le due azioni composta, lo spostamento in una nuova posizione e tornando all'istanza originale di posizionare e ripetere questo passaggio, purché il robot rimanga attivo.  E per rendere più interessante cose, i robot verrà mantenere risoluzione contemporaneamente.  La risoluzione verrà avviato solo ogni 0,1 secondi.

## <a name="frame-based-behavior-programming"></a>Programmazione di comportamento basato sulla cornice

Se si desidera controllare il comportamento del componente base frame singolarmente anziché azioni, si farebbe è per eseguire l'override di [ `OnUpdate` ](https://developer.xamarin.com/api/member/Urho.Component.OnUpdate) metodo il [ `Component` ](https://developer.xamarin.com/api/type/Urho.Component) sottoclasse.  Questo metodo viene richiamato una volta per fotogramma e viene richiamato solo se si imposta la proprietà ReceiveSceneUpdates su true.

Nell'esempio seguente viene illustrato come creare un `Rotator` componente, che viene quindi collegato a un nodo, determinando il nodo ruotare:

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

Si tratta di come vengono collegate questo componente a un nodo:

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

## <a name="combining-styles"></a>La combinazione di stili

È possibile utilizzare il modello di azione asincrono o in base per la programmazione gran parte del comportamento che è ideale per fire-and-forget stile di programmazione, ma è possibile inoltre ottimizzare il comportamento del componente di eseguire anche il codice di aggiornamento su ogni frame.

Ad esempio, la demo SamplyGame viene utilizzato nel `Enemy` classe codifica di azioni di utilizza il comportamento di base, ma garantisce inoltre che i componenti puntano verso l'utente mediante l'impostazione della direzione del nodo con `Node.LookAt`:

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

# <a name="loading-and-saving-scenes"></a>Il caricamento e salvataggio scene

Scene possono essere caricate e salvate in formato XML; vedere le funzioni [ `LoadXml` ](https://developer.xamarin.com/api/member/Urho.Scene.LoadXml) e [ `SaveXML()` ](https://developer.xamarin.com/api/member/Urho.Scene.SaveXml). Quando viene caricata una scena, viene rimossa prima tutto il contenuto esistente in essa contenuti (nodi figlio e componenti). I nodi e i componenti che sono contrassegnati temporanei con il `Temporary` proprietà non verrà salvata. Il serializzatore gestisce tutti i componenti predefiniti e le proprietà ma non è abbastanza per gestire le proprietà personalizzate e dei campi definiti in sottoclassi del componente. Tuttavia fornisce due metodi virtuali per questo:

* [`OnSerialize`](https://developer.xamarin.com/api/member/Urho.Component.OnSerialize) in cui è possibile registrare per gli stati personalizzati per la serializzazione

* [`OnDeserialized`](https://developer.xamarin.com/api/member/Urho.Component.OnDeserialize) dove è possibile ottenere gli stati di personalizzato salvati.

In genere, un componente personalizzato sarà simile al seguente:

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

## <a name="object-prefabs"></a>Oggetto Prefabs

Solo il caricamento o salvataggio intere scene non è sufficientemente flessibile per i giochi in cui è necessario essere creato dinamicamente nuovi oggetti. D'altra parte, la creazione di oggetti complessi e impostando le relative proprietà nel codice verrà rivelarsi noiosa. Per questo motivo, è anche possibile salvare un nodo della scena cui i relativi nodi figlio, i componenti e gli attributi. Questi possono essere caricati facilmente in un secondo momento come un gruppo.  Un oggetto salvato di questo tipo è noto anche come un prefab. A questo scopo, è possibile eseguire tre operazioni:

- Nel codice chiamando [ `Node.SaveXml` ](https://developer.xamarin.com/api/member/Urho.Node.SaveXml) nel nodo
- Nell'editor, selezionando il nodo nella finestra gerarchia e scegliendo "Salva il nodo come" dal menu "File".
- Il comando "nodo" in `AssetImporter`, che comporta il salvataggio della gerarchia di nodi di scena e tutti i modelli contenuti nell'asset di input (ad es. un file Collada)

Per creare un'istanza del nodo salvato in una scena, chiamare [ `InstantiateXml()` ](https://developer.xamarin.com/api/member/Urho.Scene.InstantiateXml). Il nodo verrà creato come elemento figlio della scena ma può liberamente ricollegato in seguito. Posizione e della rotazione per inserire il nodo deve essere specificato. Il codice seguente viene illustrato come creare un'istanza di un prefab `Ninja.xm` in una scena con la rotazione e posizione desiderata:

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

# <a name="events"></a>Eventi

UrhoObjects generano un numero di eventi, queste vengono rilevate come eventi di c# in varie classi che generano.  Oltre a c#-modello basato su eventi, è anche possibile utilizzare una di `SubscribeToXXX` metodi che consentono di eseguire la sottoscrizione e di mantenere un token della sottoscrizione in un secondo momento consente di annullare la sottoscrizione.  La differenza è che il primo consente ai chiamanti di molti di sottoscrizione, mentre il secondo solo consente di specificare uno, ma consente di approccio per essere utilizzato lo stile di lambda coloro e ancora, consentono la semplice rimozione della sottoscrizione.  Questi si escludono a vicenda.

Quando si sottoscrive un evento, è necessario fornire un metodo che accetta un argomento con gli argomenti dell'evento appropriato.

Ad esempio, ecco come si sottoscrive un evento del pulsante del mouse:

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

Talvolta potrebbe essere necessario arrestare la ricezione delle notifiche per l'evento, in questi casi, salvare il valore restituito dalla chiamata a `SubscribeTo` (metodo) e richiamare il metodo di annullamento della sottoscrizione su di essa:

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

Il parametro ricevuto dal gestore eventi è una classe di argomenti di eventi fortemente tipizzati che è specifici per ogni evento e contiene il payload dell'evento.

# <a name="responding-to-user-input"></a>Risponde all'Input dell'utente

È possibile sottoscrivere vari eventi, ad esempio le sequenze di tasti verso il basso per la sottoscrizione all'evento e risponde all'input venga recapitato:

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

Ma in molti scenari, i gestori aggiornamento scena per controllare lo stato corrente dei tasti quando vengono aggiornate e aggiornare di conseguenza il codice.  Ad esempio, di seguito consente di aggiornare la posizione della fotocamera in base l'input da tastiera:

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

# <a name="resources-assets"></a>Risorse (attività)

Le risorse includono la maggior parte delle operazioni in UrhoSharp caricati da archiviazione di massa durante l'inizializzazione o di runtime:

- [`Animation`](https://developer.xamarin.com/api/type/Urho.Animation/) -utilizzato per animazioni di base
- [`Image`](https://developer.xamarin.com/api/type/Urho.Resources.Image) -rappresenta le immagini archiviate in una vasta gamma di formati
- [`Model`](https://developer.xamarin.com/api/type/Urho.Model/) -Modelli 3D
- [`Material`](https://developer.xamarin.com/api/type/Urho.Material) -i materiali utilizzati per eseguire il rendering di modelli.
- [`ParticleEffect`](https://developer.xamarin.com/api/type/Urho.ParticleEffect)- [viene descritto](http://urho3d.github.io/documentation/1.4/_particles.html) come una funzione di emissione particella funziona, vedere "[particelle](#particles)" di seguito.
- [`Shader`](https://developer.xamarin.com/api/type/Urho.Shader) -shader personalizzato
- [`Sound`](https://developer.xamarin.com/api/type/Urho.Audio.Sound) -suoni per la riproduzione, vedere "[suono](#sound)" di seguito.
- [`Technique`](https://developer.xamarin.com/api/type/Urho.Technique/) -tecniche di rendering materiale
- [`Texture2D`](https://developer.xamarin.com/api/type/Urho.Urho2D.Texture2D/) -Trama 2D
- [`Texture3D`](https://developer.xamarin.com/api/type/Urho.Texture3D/) -Trama 3D
- [`TextureCube`](https://developer.xamarin.com/api/type/Urho.TextureCube/) -Trama del cubo
- `XmlFile`

Vengono gestiti e caricati dal [ `ResourceCache` ](https://developer.xamarin.com/api/type/Urho.Resources.ResourceCache/) sottosistema (disponibile come [ `Application.ResourceCache` ](https://developer.xamarin.com/api/property/Urho.Application.ResourceCache/)).

Risorse sono identificate mediante i percorsi di file, relativi alla directory di risorse registrati o file del pacchetto. Per impostazione predefinita, il motore registra le directory delle risorse `Data` e `CoreData`, i pacchetti o `Data.pak` e `CoreData.pak` se presenti.

Se il caricamento di una risorsa ha esito negativo, verrà registrato un errore e viene restituito un riferimento null.

Nell'esempio seguente viene illustrato un tipico di recupero di una risorsa dalla cache delle risorse.  In questo caso, una trama per un elemento dell'interfaccia utente, questo metodo utilizza il `ResourceCache` proprietà la `Application` classe.

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

Le risorse possono anche essere create manualmente e memorizzate nella cache di risorse, come se fossero stati caricati dal disco.

I budget di memoria possono essere impostati per ogni tipo di risorsa: se le risorse utilizzano più memoria superiore a quello consentito, le risorse meno recente verranno rimosso dalla cache se non utilizzato più. Per impostazione predefinita i budget di memoria sono impostati su illimitato.

## <a name="bringing-3d-models-and-images"></a>Integrazione di immagini e i modelli 3D

Urho3D tenta di utilizzare formati di file esistenti quando possibile e definire i formati di file personalizzati solo quando strettamente necessario, ad esempio per i modelli (*.mdl) e per le animazioni (*ANI). Per questi tipi di risorse, Urho fornisce un convertitore - [AssetImporter](http://urho3d.github.io/documentation/1.4/_tools.html) che possono usare molti formati 3D più diffusi, ad esempio fbx, dae, 3ds e obj e così via.

Non vi è un componente aggiuntivo utile per Blender [ https://github.com/reattiva/Urho3D-Blender ](https://github.com/reattiva/Urho3D-Blender) che possono esportare gli asset Blender nel formato appropriato per Urho3D.

## <a name="background-loading-of-resources"></a>Caricamento in background delle risorse

In genere, quando viene richiesto di risorse utilizzando uno del `ResourceCache`del `Get` (metodo), vengono caricati immediatamente nel thread principale, che potrebbe richiedere diversi millisecondi per tutti i passaggi necessari (caricare file dal disco, analizzare i dati, carica GPU se necessario ) e possono pertanto causare Elimina frequenza dei fotogrammi.

Se si conosce in anticipo quali risorse è necessario, è possibile richiedere di essere caricato in un thread in background chiamando `BackgroundLoadResource()`. È possibile sottoscrivere l'evento di caricamento in Background risorse tramite il `SubscribeToResourceBackgroundLoaded` metodo. Verificare se il caricamento è effettivamente un esito positivo o un errore. A seconda della risorsa, solo una parte del processo di caricamento può essere spostato in un thread in background, ad esempio la fase di caricamento GPU terminando sempre deve essere eseguita nel thread principale. Si noti che se si chiama uno della risorsa durante il caricamento di metodi per una risorsa che viene accodato per il caricamento di sfondo, il thread principale richiederà molto tempo fino a quando il caricamento è completato.

Funzionalità di caricamento asincrona scena `LoadAsync()` e `LoadAsyncXML()` ha la possibilità di caricamento in background le risorse prima di passare per caricare il contenuto di scena. E può essere utilizzato per caricare solo le risorse senza modificare la scena, specificando il `LoadMode.ResourcesOnly`. Questo consente di preparare un file prefab scena o dell'oggetto per la rapida creazione di istanze.

Infine il tempo massimo (in millisecondi) impiegato per ogni frame in background le risorse caricate possono essere configurate tramite l'impostazione di completamento di `FinishBackgroundResourcesMs` proprietà il `ResourceCache`.

<a name="sound"/>

# <a name="sound"></a>Audio

Audio è una parte importante del gioco e il framework UrhoSharp fornisce un modo per la riproduzione di suoni del gioco.  Riprodurre suoni collegando un [ `SoundSource` ](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource/) componente un [ `Node` ](https://developer.xamarin.com/api/type/Urho.Node) e quindi la riproduzione di un file denominato dalle risorse.

Ecco come farlo:

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"/>

# <a name="particles"></a>Particelle

Le particelle forniscono un mezzo di alcuni effetti semplice ed economici per aggiungere all'applicazione.  È possibile utilizzare le particelle archiviate in formato PEX, utilizzando strumenti quali [ http://onebyonedesign.com/flash/particleeditor/ ](http://onebyonedesign.com/flash/particleeditor/).

Le particelle sono componenti che possono essere aggiunti a un nodo.  È necessario chiamare il nodo `CreateComponent<ParticleEmitter2D>` per creare la particella e quindi configurare la particella impostando la proprietà effettiva per un effetto 2D che viene caricato dalla cache delle risorse.

Ad esempio, è possibile richiamare questo metodo sul componente per visualizzare alcuni particelle sottoposti a rendering come un'esplosione quando arriva:

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

Nel codice precedente verrà creato un nodo esplosione associato al componente corrente, all'interno di questo nodo esplosione creare una funzione di emissione particella 2D e configurarlo impostando la proprietà dell'effetto.  Esegui due azioni, uno che viene ridimensionata il nodo per essere più piccoli e uno che lascia le dimensioni specificate per 0,5 secondi.  Quindi si rimuove l'esplosione, che rimuove inoltre l'effetto della particella dallo schermo.

La particella precedente viene eseguito il rendering simile al seguente quando si utilizza una trama sfera:

![Le particelle con una trama sfera](using-images/image-1.png "la particella precedente viene eseguito il rendering simile al seguente quando si utilizza una trama sfera")

E questo è il risultato se si utilizza una trama a blocco:

![Le particelle con una trama casella](using-images/image-2.png "e questo è il risultato se si utilizza una trama a blocco")

# <a name="multithreading-support"></a>Supporto del multithreading

UrhoSharp è una libreria a thread singola.  Ciò significa che non è consigliabile tentare di chiamare i metodi in UrhoSharp da un thread in background o rischio di danneggiare lo stato dell'applicazione e probabilmente di arresto anomalo dell'applicazione.

Se si desidera eseguire il codice in background e aggiornare i componenti Urho nell'interfaccia utente principale, è possibile utilizzare il [ `Application.InvokeOnMain(Action)` ](https://developer.xamarin.com/api/member/Urho.Application.InvokeOnMain) metodo.  È inoltre possibile utilizzare await in c# e .NET attività API per assicurarsi che il codice viene eseguito nel thread appropriato.


# <a name="urhoeditor"></a>UrhoEditor

È possibile scaricare Editor Urho per la piattaforma di [sito Web Urho](http://urho3d.github.io/), passare a download e selezionare la versione più recente.

# <a name="copyrights"></a>Copyrights

Questa documentazione contiene contenuto originale da Inc Xamarin, ma disegna ampiamente nella documentazione di origine aperti per il progetto Urho3D e schermate dal progetto Cocos2D.



## <a name="related-links"></a>Collegamenti correlati

- [Cartella di lavoro di terra pianeta](https://developer.xamarin.com/workbooks/graphics/urhosharp/planetearth/planetearth.workbook)
- [Esplorazione di cartella di lavoro di coordinate](https://developer.xamarin.com/workbooks/graphics/urhosharp/coordinates/ExploringUrhoCoordinates.workbook)
