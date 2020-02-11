---
title: Uso di UrhoSharp per creare un gioco 3D
description: Questo documento offre una panoramica di UrhoSharp, che descrive scene, componenti, forme, fotocamere, azioni, input utente, audio e altro ancora.
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: cb4e524977b53f1a17552298c509d43ccf460f08
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73011646"
---
# <a name="using-urhosharp-to-build-a-3d-game"></a>Uso di UrhoSharp per creare un gioco 3D

Prima di scrivere il primo gioco, è opportuno acquisire familiarità con le nozioni di base: come configurare la scena, come caricare le risorse (che contiene la grafica) e come creare semplici interazioni per il gioco.

<a name="scenenodescomponentsandcameras"/>

## <a name="scenes-nodes-components-and-cameras"></a>Scene, nodi, componenti e fotocamere

Il modello di scena può essere descritto come un grafico della scena basato su componenti. La scena è costituita da una gerarchia di nodi della scena, a partire dal nodo radice, che rappresenta anche l'intera scena. Ogni `Node` dispone di una trasformazione 3D (posizione, rotazione e scala), un nome, un ID, più un numero arbitrario di componenti.  I componenti portano un nodo alla vita, possono aggiungere una rappresentazione visiva (`StaticModel`), possono emettere un suono (`SoundSource`), possono fornire un limite di collisione e così via.

È possibile creare le scene e i nodi di configurazione usando l' [Editor Urho](#urhoeditor)oppure è possibile eseguire operazioni dal C# codice.  In questo documento verrà illustrata la configurazione dell'uso del codice, in quanto illustrano gli elementi necessari per visualizzare le informazioni sullo schermo.

Oltre a configurare la scena, è necessario configurare una `Camera`, che determina cosa verrà visualizzato all'utente.

### <a name="setting-up-your-scene"></a>Impostazione della scena

In genere si crea questo modulo come metodo di avvio:

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

Il rendering degli oggetti 3D, la riproduzione audio, la fisica e gli aggiornamenti della logica con script sono tutti abilitati creando componenti diversi nei nodi chiamando `CreateComponent<T>()`.  Ad esempio, configurare il nodo e il componente chiaro in questo modo:

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

È stato creato sopra un nodo con il nome "`DirectionalLight`" e ne è stata impostata una direzione, ma nient'altro.  A questo punto, è possibile trasformare il nodo precedente in un nodo di emissione chiara, collegando un componente `Light` al nodo, con `CreateComponent`:

```csharp
var light = lightNode.CreateComponent<Light>();
```

I componenti creati nel `Scene` hanno un ruolo speciale: per implementare la funzionalità a livello di scena. Devono essere creati prima di tutti gli altri componenti e includono quanto segue:

 `Octree`: implementa il partizionamento spaziale e le query di visibilità accelerata. Senza questo oggetto 3D non è possibile eseguire il rendering.
 `PhysicsWorld`: implementa la simulazione fisica. I componenti fisici, ad esempio `RigidBody` o `CollisionShape`, non possono funzionare correttamente senza questo.
 `DebugRenderer`: implementa il rendering della geometria di debug.

Componenti comuni come `Light`, `Camera` o `StaticModel`
non devono essere creati direttamente nel `Scene`, bensì nei nodi figlio.

La libreria include una vasta gamma di componenti che è possibile associare ai nodi per renderli vita: elementi visibili all'utente (modelli), suoni, corpi rigidi, forme di collisione, fotocamere, sorgenti di luce, generatori di particelle e molto altro ancora.

### <a name="shapes"></a>Forme

Per praticità, sono disponibili diverse forme come nodi semplici nello spazio dei nomi Urho. Shapes.  Sono incluse caselle, sfere, coni, cilindri e piani.

### <a name="camera-and-viewport"></a>Fotocamera e Viewport

Analogamente alla luce, le fotocamere sono componenti, quindi è necessario aggiungere il componente a un nodo, come indicato di seguito:

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

Con questa operazione, è stata creata una fotocamera e la fotocamera è stata inserita nel mondo 3D, il passaggio successivo consiste nell'informare il `Application` che si tratta della fotocamera che si vuole usare. questa operazione viene eseguita con il codice seguente:

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

A questo punto dovrebbe essere possibile visualizzare i risultati della creazione.

### <a name="identification-and-scene-hierarchy"></a>Gerarchia di identificazione e di scena

A differenza dei nodi, i componenti non hanno nomi; i componenti all'interno dello stesso nodo sono identificati solo dal tipo e dall'indice nell'elenco dei componenti del nodo, che viene compilato in base all'ordine di creazione, ad esempio, è possibile recuperare il componente `Light` dall'oggetto `lightNode` precedente, come indicato di seguito:

```csharp
var myLight = lightNode.GetComponent<Light>();
```

È anche possibile ottenere un elenco di tutti i componenti recuperando la proprietà `Components` che restituisce un `IList<Component>` che è possibile usare.

Al momento della creazione, i nodi e i componenti ottengono gli ID di tipo Integer globali. È possibile eseguire query sulla scena usando le funzioni `GetNode(uint id)` e `GetComponent(uint id)`. Si tratta di un'operazione molto più rapida rispetto ad esempio alla query del nodo della scena basata sul nome ricorsiva.

Non esiste un concetto incorporato di un'entità o di un oggetto di gioco; piuttosto, spetta al programmatore decidere la gerarchia dei nodi e in quali nodi inserire qualsiasi logica con script. In genere, gli oggetti con trasferimento libero nel mondo 3D vengono creati come elementi figlio del nodo radice. I nodi possono essere creati con o senza un nome usando `CreateChild`. L'univocità dei nomi di nodo non viene applicata.

Ogni volta che è presente una composizione gerarchica, è consigliabile (e in effetti necessario, perché i componenti non dispongono di trasformazioni 3D) per creare un nodo figlio.

Se, ad esempio, un carattere teneva in mano un oggetto, l'oggetto deve avere il proprio nodo, che verrebbe associato all'osso della mano del carattere (anche un `Node`).  L'eccezione è rappresentata dalla `CollisionShape`fisica, che può essere offsetted e ruotata singolarmente in relazione al nodo.

Si noti che la trasformazione di `Scene`viene intenzionalmente ignorata come ottimizzazione durante il calcolo delle trasformazioni derivate dal mondo dei nodi figlio, quindi la modifica non ha alcun effetto e deve essere lasciata invariata (posizione all'origine, nessuna rotazione, nessun ridimensionamento).

`Scene` nodi possono essere liberamente riassociati. I componenti a contrasto fanno sempre parte del nodo a cui sono collegati e non possono essere spostati tra i nodi. Sia i nodi che i componenti forniscono una funzione `Remove` per eseguire questa operazione senza dover passare attraverso l'elemento padre. Una volta rimosso il nodo, nessuna operazione sul nodo o sul componente in questione è sicura dopo la chiamata a tale funzione.

È anche possibile creare un `Node` che non appartiene a una scena. Questa operazione è utile ad esempio con una fotocamera che si muove in una scena che può essere caricata o salvata, perché la fotocamera non verrà salvata insieme alla scena effettiva e non verrà distrutta quando viene caricata la scena. Si noti, tuttavia, che la creazione di geometria, della fisica o dei componenti di script in un nodo non collegato e che quindi lo si trasferisce in una scena in seguito causerà il mancato funzionamento di tali componenti.

### <a name="scene-updates"></a>Aggiornamenti della scena

Una scena i cui aggiornamenti sono abilitati (impostazione predefinita) verrà aggiornata automaticamente in ogni iterazione del ciclo principale.  Viene richiamato il gestore dell'evento `SceneUpdate` dell'applicazione.

I nodi e i componenti possono essere esclusi dall'aggiornamento della scena tramite la relativa disabilitazione, vedere `Enabled`.  Il comportamento dipende dal componente specifico, ma, ad esempio, la disabilitazione di un componente disegnatore ne rende invisibile anche la disabilitazione di un componente di origine audio. Se un nodo è disabilitato, tutti i relativi componenti vengono considerati disabilitati indipendentemente dallo stato di abilitazione o disabilitazione.

## <a name="adding-behavior-to-your-components"></a>Aggiunta del comportamento ai componenti

Il modo migliore per strutturare il gioco consiste nel creare un componente personalizzato che incapsula un attore o un elemento nel gioco.  Ciò rende la funzionalità autonoma, dagli asset usati per visualizzarla, al suo comportamento.

Il modo più semplice per aggiungere il comportamento a un componente consiste nell'usare le azioni, ovvero le istruzioni che è possibile accodare e C# combinare con la programmazione asincrona.  In questo modo il comportamento per il componente è molto elevato e rende più semplice comprendere cosa accade.

In alternativa, è possibile controllare esattamente cosa accade al componente aggiornando le proprietà dei componenti in ogni frame (descritto nella sezione relativa al comportamento basato su frame).

### <a name="actions"></a>Azioni

È possibile aggiungere facilmente il comportamento ai nodi usando le azioni.  Le azioni possono modificare varie proprietà del nodo ed eseguirle in un determinato periodo di tempo oppure ripeterle per un determinato numero di volte con una curva di animazione specificata.

Si consideri, ad esempio, un nodo "cloud" nella scena, che può essere sfumato in questo modo:

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

Le azioni sono oggetti non modificabili che consentono di riutilizzare l'azione per la Guida di oggetti diversi.

Un idioma comune consiste nel creare un'azione che esegua l'operazione inversa:

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

Nell'esempio seguente l'oggetto viene dissolto in un periodo di tre secondi.  È anche possibile eseguire un'azione dopo l'altra, ad esempio prima di tutto spostare il cloud, quindi nasconderlo:

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

Se si desidera che entrambe le azioni avvengano contemporaneamente, è possibile utilizzare l'azione parallela e fornire tutte le azioni che si desidera eseguire in parallelo:

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

Nell'esempio precedente il cloud viene spostato e si dissolve nello stesso momento.

Si noterà che questi utilizzino C#`await`, che consente di valutare in modo lineare il comportamento che si desidera ottenere.

### <a name="basic-actions"></a>Azioni di base

Queste sono le azioni supportate in UrhoSharp:

- Nodi mobili: `MoveTo`, `MoveBy`, `Place`, `BezierTo`, `BezierBy`, `JumpTo`, `JumpBy`
- Nodi rotanti: `RotateTo`, `RotateBy`
- Ridimensionamento di nodi: `ScaleTo`, `ScaleBy`
- Nodi di dissolvenza: `FadeIn`, `FadeTo`, `FadeOut`, `Hide``Blink`
- Colorazione: `TintTo`, `TintBy`
- Istanti: `Hide`, `Show`, `Place`, `RemoveSelf`, `ToggleVisibility`
- Ciclo: `Repeat`, `RepeatForever`, `ReverseTime`

Altre funzionalità avanzate includono la combinazione delle azioni `Spawn` e `Sequence`.

### <a name="easing---controlling-the-speed-of-your-actions"></a>Interpolazione del controllo della velocità delle azioni

L'interpolazione è un modo che indica il modo in cui l'animazione verrà rivelata e può rendere le animazioni molto più gradevoli.  Per impostazione predefinita, le azioni avranno un comportamento lineare, ad esempio un'azione `MoveTo` avrà un movimento molto robotico.  È possibile eseguire il wrapping delle azioni in un'azione di interpolazione per modificare il comportamento, ad esempio uno che avvia lentamente lo spostamento, accelera e raggiunge lentamente la fine (`EasyInOut`).

A questo scopo, eseguire il wrapping di un'azione esistente in un'azione di interpolazione, ad esempio:

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

Sono disponibili diverse modalità di interpolazione, il grafico seguente mostra i diversi tipi di interpolazione e il relativo comportamento sul valore dell'oggetto che controllano nel periodo di tempo, dall'inizio alla fine:

![Modalità di interpolazione](using-images/easing.png "Questo grafico mostra i vari tipi di interpolazione e il relativo comportamento sul valore dell'oggetto che controllano nel periodo di tempo")

### <a name="using-actions-and-async-code"></a>Uso di azioni e codice asincrono

Nella sottoclasse `Component` è necessario introdurre un metodo asincrono che prepara il comportamento del componente e ne guida la funzionalità.
Si richiama quindi questo metodo usando la C# parola chiave`await`da un'altra parte del programma, ovvero il metodo `Application.Start`o in risposta a un utente o a un punto della storia nell'applicazione.

Esempio:

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

Nel metodo `Launch` sono state avviate tre azioni: il robot passa alla scena, questa azione altererà la posizione del nodo in un periodo di 0,6 secondi.  Poiché si tratta di un'opzione asincrona, questo si verificherà simultaneamente come l'istruzione successiva che è la chiamata a `MoveRandomly`.  Questo metodo consente di modificare la posizione del robot in parallelo in una posizione casuale.  Questa operazione viene eseguita eseguendo due azioni composte, lo spostamento in una nuova posizione e tornando alla posizione originale e la ripetizione fino a quando il robot rimane attivo.  Per rendere le cose più interessanti, il robot continuerà a scattare simultaneamente.  Il tiro viene avviato solo ogni 0,1 secondi.

### <a name="frame-based-behavior-programming"></a>Programmazione di comportamenti basati su frame

Se si vuole controllare il comportamento del componente in base a un frame, anziché usare le azioni, è necessario eseguire l'override del metodo `OnUpdate` della sottoclasse `Component`.  Questo metodo viene richiamato una volta per ogni frame e viene richiamato solo se si imposta la proprietà ReceiveSceneUpdates su true.

Di seguito viene illustrato come creare un componente `Rotator`, che viene quindi collegato a un nodo, causando la rotazione del nodo:

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
            RotationSpeed.X  timeStep,
            RotationSpeed.Y  timeStep,
            RotationSpeed.Z  timeStep),
            TransformSpace.Local);
    }
}
```

Questo è il modo in cui il componente viene collegato a un nodo:

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

### <a name="combining-styles"></a>Combinazione di stili

È possibile usare il modello basato su Async/Action per la programmazione di gran parte del comportamento, ideale per lo stile di programmazione Fire-and-Forget, ma è anche possibile ottimizzare il comportamento del componente per eseguire anche codice di aggiornamento in ogni frame.

Ad esempio, nella demo di SamplyGame questo viene usato nella classe `Enemy` codifica il comportamento di base usa le azioni, ma garantisce anche che i componenti puntino verso l'utente impostando la direzione del nodo con `Node.LookAt`:

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

## <a name="loading-and-saving-scenes"></a>Caricamento e salvataggio di scene

Le scene possono essere caricate e salvate in formato XML; vedere le funzioni `LoadXml` e `SaveXML`. Quando viene caricata una scena, viene rimosso per primo tutto il contenuto esistente (nodi e componenti figlio). I nodi e i componenti contrassegnati come temporanei con la proprietà `Temporary` non verranno salvati. Il serializzatore gestisce tutti i componenti e le proprietà predefiniti, ma non è sufficientemente intelligente da gestire le proprietà personalizzate e i campi definiti nelle sottoclassi dei componenti. Tuttavia, fornisce due metodi virtuali:

 `OnSerialize` dove è possibile registrare gli stati personalizzati per la serializzazione

 `OnDeserialized` dove è possibile ottenere gli stati personalizzati salvati.

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

### <a name="object-prefabs"></a>Prefabbricati di oggetti

Semplicemente il caricamento o il salvataggio di intere scene non è sufficientemente flessibile per i giochi in cui è necessario creare dinamicamente nuovi oggetti. D'altra parte, la creazione di oggetti complessi e l'impostazione delle relative proprietà nel codice saranno anche noiosi. Per questo motivo, è anche possibile salvare un nodo della scena che includerà i relativi nodi figlio, componenti e attributi. Questi possono essere caricati in un secondo momento in modo appropriato come gruppo.  Tale oggetto salvato viene spesso definito prefabbricato. A questo scopo, è possibile eseguire tre operazioni:

- Nel codice chiamando `Node.SaveXml` nel nodo
- Nell'editor, selezionando il nodo nella finestra gerarchia e scegliendo "Salva nodo con nome" dal menu "file".
- Usando il comando "node" in `AssetImporter`, che salverà la gerarchia del nodo della scena e tutti i modelli contenuti nell'asset di input (ad esempio, un file Collada)

Per creare un'istanza del nodo salvato in una scena, chiamare `InstantiateXml`. Il nodo verrà creato come elemento figlio della scena, ma potrà essere riassociato liberamente. È necessario specificare la posizione e la rotazione per il posizionamento del nodo. Il codice seguente illustra come creare un'istanza di un `Ninja.xm` prefabbricato in una scena con la posizione e la rotazione desiderate:

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

## <a name="events"></a>eventi

UrhoObjects generano una serie di eventi, che vengono esposti come C# eventi sulle varie classi che li generano.  Oltre al modello di C#eventi basato su, è anche possibile usare un`SubscribeToXXX`metodi che consentiranno di sottoscrivere e tenere un token di sottoscrizione che potrà essere usato in un secondo momento per annullare la sottoscrizione.  La differenza risiede nel fatto che il primo consentirà a molti chiamanti di sottoscrivere, mentre il secondo ne consente l'uso, ma consente di usare l'approccio più gradevole in stile lambda e, ancora, di rimuovere facilmente la sottoscrizione.  Si escludono a vicenda.

Quando si sottoscrive un evento, è necessario fornire un metodo che accetti un argomento con gli argomenti dell'evento appropriati.

Ad esempio, questo è il modo in cui si sottoscrive l'evento di un pulsante del mouse:

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

A volte si desidera interrompere la ricezione delle notifiche per l'evento, in questi casi, salvare il valore restituito dalla chiamata al metodo `SubscribeTo` e richiamare il metodo di annullamento della sottoscrizione:

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

Il parametro ricevuto dal gestore eventi è una classe di argomenti di evento fortemente tipizzata che sarà specifica per ogni evento e conterrà il payload dell'evento.

## <a name="responding-to-user-input"></a>Risposta all'input dell'utente

È possibile sottoscrivere diversi eventi, ad esempio le sequenze di tasti sottoscrivendo l'evento e rispondendo all'input recapitato:

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

In molti scenari, tuttavia, si desidera che i gestori degli aggiornamenti della scena controllino lo stato corrente delle chiavi quando vengono aggiornati e aggiornano il codice di conseguenza.  Ad esempio, è possibile usare quanto segue per aggiornare la posizione della fotocamera in base all'input da tastiera:

```csharp
protected override void OnUpdate(float timeStep)
{
    Input input = Input;
    // Movement speed as world units per second
    const float moveSpeed = 4.0f;
    // Read WASD keys and move the camera scene node to the
    // corresponding direction if they are pressed
    if (input.GetKeyDown(Key.W))
        CameraNode.Translate(Vector3.UnitY  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.S))
        CameraNode.Translate(new Vector3(0.0f, -1.0f, 0.0f)  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.A))
        CameraNode.Translate(new Vector3(-1.0f, 0.0f, 0.0f)  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.D))
        CameraNode.Translate(Vector3.UnitX  moveSpeed  timeStep, TransformSpace.Local);
}
```

## <a name="resources-assets"></a>Risorse (asset)

Le risorse includono la maggior parte degli elementi in UrhoSharp caricati dall'archiviazione di massa durante l'inizializzazione o il runtime:

- `Animation`: usato per animazioni scheletriche
- `Image`: rappresenta le immagini archiviate in un'ampia gamma di formati grafici
- Modelli di `Model` 3D
- `Material` materiali utilizzati per eseguire il rendering dei modelli.
- `ParticleEffect`- [descrive](https://urho3d.github.io/documentation/1.4/_particles.html) il funzionamento di un emettitore di particelle, vedere "[Particles](#particles)" di seguito.
- shader `Shader` personalizzati
- `Sound`-suoni da riprodurre, vedere "[Sound](#sound)" di seguito.
- tecniche di rendering del materiale `Technique`
- trama `Texture2D`-2D
- trama `Texture3D`-3D
- trama `TextureCube`-Cube
- `XmlFile`

Sono gestite e caricate dal sottosistema `ResourceCache` (disponibile come `Application.ResourceCache`).

Le risorse stesse sono identificate dai rispettivi percorsi di file, in relazione alle directory di risorse registrate o ai file del pacchetto. Per impostazione predefinita, il motore registra le directory delle risorse `Data` e `CoreData`, oppure i pacchetti `Data.pak` e `CoreData.pak` se esistono.

Se il caricamento di una risorsa ha esito negativo, viene registrato un errore e viene restituito un riferimento null.

Nell'esempio seguente viene illustrato un modo tipico per recuperare una risorsa dalla cache delle risorse.  In questo caso, una trama per un elemento dell'interfaccia utente, usa la proprietà `ResourceCache` dalla classe `Application`.

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

Le risorse possono anche essere create manualmente e archiviate nella cache delle risorse come se fossero state caricate dal disco.

È possibile impostare i budget di memoria per ogni tipo di risorsa: se le risorse usano più memoria del consentito, le risorse meno recenti verranno rimosse dalla cache se non sono più utilizzate. Per impostazione predefinita, i budget della memoria sono impostati su illimitati.

### <a name="bringing-3d-models-and-images"></a>Introduzione a modelli e immagini 3D

Urho3D tenta di utilizzare i formati di file esistenti laddove possibile e definire formati di file personalizzati solo quando sono assolutamente necessari, ad esempio per i modelli (. mdl) e per le animazioni (. ani). Per questi tipi di asset, Urho fornisce un convertitore- [AssetImporter](https://urho3d.github.io/documentation/1.4/_tools.html) che può utilizzare molti formati 3D diffusi, ad esempio FBX, DAE, 3DS e obj e così via.

È inoltre disponibile un comodo componente aggiuntivo per Blender [https://github.com/reattiva/Urho3D-Blender](https://github.com/reattiva/Urho3D-Blender) in grado di esportare le risorse di Blender nel formato appropriato per Urho3D.

### <a name="background-loading-of-resources"></a>Caricamento in background delle risorse

In genere, quando si richiedono risorse usando uno dei `Get` metodo di `ResourceCache`, vengono caricati immediatamente nel thread principale, operazione che può richiedere diversi millisecondi per tutti i passaggi necessari (caricare il file dal disco, analizzare i dati, caricare nella GPU se necessario) e può di conseguenza, le gocce di framerate.

Se si conoscono in anticipo le risorse necessarie, è possibile richiederne il caricamento in un thread in background chiamando `BackgroundLoadResource`. Per sottoscrivere l'evento di caricamento in background delle risorse, è possibile usare il metodo `SubscribeToResourceBackgroundLoaded`. indica se il caricamento è stato effettivamente un esito positivo o negativo. A seconda della risorsa, solo una parte del processo di caricamento può essere spostata in un thread in background. ad esempio, il passaggio di caricamento della GPU finale deve sempre verificarsi nel thread principale. Si noti che se si chiama uno dei metodi di caricamento delle risorse per una risorsa in coda per il caricamento in background, il thread principale si blocca fino al completamento del caricamento.

La funzionalità asincrona di caricamento della scena `LoadAsync` e `LoadAsyncXML` ha l'opzione di caricare in background le risorse prima di continuare a caricare il contenuto della scena. Può anche essere usato per caricare solo le risorse senza modificare la scena, specificando il `LoadMode.ResourcesOnly`. In questo modo, è possibile preparare un file prefabbricato della scena o dell'oggetto per creare un'istanza rapida.

Infine, il tempo massimo (in millisecondi) impiegato per ogni fotogramma al completamento delle risorse caricate in background può essere configurato impostando la proprietà `FinishBackgroundResourcesMs` nel `ResourceCache`.

<a name="sound"/>

## <a name="sound"></a>Suono

Il suono è una parte importante della riproduzione del gioco e il Framework di UrhoSharp offre un modo per riprodurre suoni nel gioco.  Per riprodurre suoni, è necessario aggiungere un `SoundSource`
componente per un `Node` e quindi riprodurre un file denominato dalle risorse.

Questo è il modo in cui viene eseguito:

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"/>

## <a name="particles"></a>Particelle

Le particelle rappresentano un modo semplice per aggiungere all'applicazione alcuni effetti semplici e convenienti.  È possibile utilizzare le particelle archiviate in formato PEX utilizzando strumenti come [http://onebyonedesign.com/flash/particleeditor/](http://onebyonedesign.com/flash/particleeditor/).

Le particelle sono componenti che possono essere aggiunti a un nodo.  È necessario chiamare il metodo `CreateComponent<ParticleEmitter2D>` del nodo per creare la particella e quindi configurare la particella impostando la proprietà Effect su un effetto 2D caricato dalla cache delle risorse.

Ad esempio, è possibile richiamare questo metodo sul componente per visualizzare alcune particelle di cui viene eseguito il rendering come esplosione quando si verifica:

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

Il codice precedente creerà un nodo di esplosione collegato al componente corrente, all'interno di questo nodo di esplosione viene creato un emettitore particellare 2D e configurato impostando la proprietà Effect.  Vengono eseguite due azioni, una con scalabilità del nodo inferiore e una che la lascia a tale dimensione per 0,5 secondi.  Quindi si rimuove l'esplosione, che rimuove anche l'effetto particellare dalla schermata.

Quando si usa una trama Sphere, il rendering della particella precedente è simile al seguente:

![Particelle con una trama Sphere](using-images/image-1.png "Il rendering della particella precedente è simile al seguente quando si usa una trama Sphere")

Si tratta di un aspetto che viene visualizzato se si usa una trama a blocchi:

![Particelle con una trama box](using-images/image-2.png "E questo è l'aspetto che viene visualizzato se si usa una trama a blocchi")

## <a name="multi-threading-support"></a>Supporto per multithread

UrhoSharp è una libreria a thread singolo.  Ciò significa che non si deve tentare di richiamare i metodi in UrhoSharp da un thread in background o si rischia di danneggiare lo stato dell'applicazione e probabilmente si è verificato un arresto anomalo dell'applicazione.

Se si vuole eseguire codice in background e quindi aggiornare i componenti di Urho nell'interfaccia utente principale, è possibile usare il `Application.InvokeOnMain(Action)`
ProcessOnStatus.  Inoltre, è possibile usare C# await e le API delle attività .NET per garantire che il codice venga eseguito sul thread appropriato.

## <a name="urhoeditor"></a>UrhoEditor

È possibile scaricare l'editor Urho per la piattaforma dal [sito Web Urho](http://urho3d.github.io/), passare a download e selezionare la versione più recente.

## <a name="copyrights"></a>Copyright

Questa documentazione contiene contenuti originali di Xamarin
 Inc, ma viene disegnata in maniera estensiva dalla documentazione Open Source per il progetto Urho3D e contiene screenshot del progetto Cocos2D.
