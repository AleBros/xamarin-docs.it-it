---
title: Disegnare la grafica 3D con vertici in MonoGame
description: MonoGame supporta l'utilizzo di matrici di vertici per definire la modalità di rendering di un oggetto 3D in base al punto. Gli utenti possono sfruttare le matrici di vertici per creare una geometria dinamica, implementare effetti speciali e migliorare l'efficienza della loro il rendering tramite il culling.
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 4fa6929a8de19608c06690e4cf24514f6749480a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921482"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>Disegnare la grafica 3D con vertici in MonoGame

_MonoGame supporta l'utilizzo di matrici di vertici per definire la modalità di rendering di un oggetto 3D in base al punto. Gli utenti possono sfruttare le matrici di vertici per creare una geometria dinamica, implementare effetti speciali e migliorare l'efficienza della loro il rendering tramite il culling._

Gli utenti che hanno già letto tramite il [Guida sul rendering modelli](~/graphics-games/monogame/3d/part1.md) risulteranno familiari con un modello 3D in MonoGame per il rendering. La `Model` classe è un metodo efficace per eseguire il rendering di grafica 3D quando si lavora con dati definiti in un file (ad esempio con estensione fbx) e quando si lavora con dati statici. Alcuni giochi richiedono la geometria 3D da definire o modificare in modo dinamico in fase di esecuzione. In questi casi, è possibile utilizzare matrici di *vertici* per definire ed eseguire il rendering della geometria. Il vertice è un termine generale per un punto nello spazio 3D che fa parte di un elenco ordinato, utilizzato per definire la geometria. In genere i vertici vengono ordinati in modo da definire una serie di triangoli.

Per facilitare la visualizzazione come vertici vengono utilizzati per creare oggetti 3D, si consideri la sfera seguente:

![](part2-images/image1.png "Per facilitare la visualizzazione come vertici vengono utilizzati per creare oggetti 3D, prendere in considerazione questo sfera")

Come illustrato in precedenza, la sfera è chiaramente composta da più triangoli. È possibile visualizzare la struttura della sfera per vedere come i vertici connettono triangoli form:

![](part2-images/image2.png "Visualizzare la struttura della sfera per vedere come i vertici connettono triangoli modulo")

Questa procedura dettagliata verrà trattati i seguenti argomenti:

- Creazione di un progetto
- Creazione di vertici
- Aggiungere il codice di disegno
- Il rendering con una trama
- Modifica le coordinate di trama
- Rendering dei vertici con modelli

Il progetto completato conterrà un piano Scacchi che verrà creato utilizzando una matrice di vertex:

![](part2-images/image3.png "Il progetto completato conterrà un piano Scacchi che verrà creato utilizzando una matrice di vertex")

## <a name="creating-a-project"></a>Creazione di un progetto

È in primo luogo, verrà scaricato un progetto che verrà utilizzata come il punto di partenza. Si userà il progetto di modello [che sono disponibili qui](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/).

Una volta scaricato e decompresso, aprire ed eseguire il progetto. Prevediamo di vedere sei modelli robot che viene disegnati sullo schermo:

![](part2-images/image4.png "Sei modelli robot che viene disegnati sullo schermo")

Entro la fine di questo progetto si sarà combinazione personalizzato per il rendering del vertice personalizzato con i robot `Model`, pertanto si propone di eliminare il codice di rendering robot. In alternativa, è solo cancellare il contenuto di `Game1.Draw` chiamata per rimuovere il disegno dei 6 robot per il momento. A tale scopo, aprire il **Game1.cs** file e individuare il `Draw` metodo. Modificarla in modo che contenga il codice seguente:

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

Il risultato sarà il gioco la visualizzazione di un errore irreversibile vuota:

![](part2-images/image5.png "Il risultato sarà il gioco la visualizzazione di un errore irreversibile vuoto")

## <a name="creating-the-vertices"></a>Creazione di vertici

Verrà creata una matrice di vertici per definire la geometria. In questa procedura dettagliata, si verrà creato un piano 3D (un quadrato nello spazio 3D, non un aereo). Anche se il piano include quattro lati e quattro angoli, si sarà costituito da due triangoli, ognuno dei quali richiede tre vertici. Pertanto, si definiranno un totale di sei punti.

Finora è stato già accennato vertici in senso generale, ma MonoGame fornisce alcune strutture standard che possono essere utilizzate per i vertici:

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

Nome del tipo di ogni indica i componenti in che esso contenuti. Ad esempio, `VertexPositionColor` contiene valori per posizione e il colore. Diamo un'occhiata ognuno dei componenti:

- Posizione: tutti i tipi di vertice includono un `Position` componente. Il `Position` valori definiscono dove si trova sul vertice nello spazio 3D (X, Y e Z).
- Colore: vertici, è possibile specificare un `Color` valore per eseguire tinte personalizzato.
- Normale: normali definiscono la modalità in cui si sta verificando la superficie dell'oggetto. Le normali sono necessari che se il rendering di un oggetto con illuminazione dopo la direzione di una superficie è rivolta verso impatti che riceve quanto più chiaro. Le normali usano in genere vengono specificate come una *vettore unitario* – un vettore 3D che ha una lunghezza pari a 1.
- Trama-trama fa riferimento a coordinate di trama –, ovvero la parte di una trama deve essere visualizzati in un vertice specifico. I valori di trama se sono necessari per il rendering di un oggetto con una trama 3D. Trama coordinate vengono normalizzati, il che significa che i valori verranno compreso tra 0 e 1. Verranno descritte le coordinate di trama in modo più dettagliato più avanti in questa Guida.

Il piano verrà utilizzata come un piano, e si desidera applicare una trama, quando si esegue il rendering, pertanto verrà utilizzato il `VertexPositionTexture` tipo per definire i vertici.

In primo luogo, verrà aggiunto un membro alla classe Game1:

```csharp
VertexPositionTexture[] floorVerts; 
```

Successivamente, definire i vertici `Game1.Initialize`. Si noti che il modello fornito in precedenza in questo articolo non contiene un `Game1.Initialize` metodo, pertanto è necessario aggiungere l'intero metodo per `Game1`:

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];
    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);
    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // We’ll be assigning texture values later
    base.Initialize ();
}
```

Per consentire di visualizzare l'aspetto i vertici, prendere in considerazione il diagramma seguente:

![](part2-images/image6.png "Per consentire di visualizzare l'aspetto dei vertici, prendere in considerazione questo diagramma")

È necessario affidarsi il diagramma per visualizzare i vertici finché non è completata l'implementazione di codice per il rendering.

## <a name="adding-drawing-code"></a>Aggiungere il codice di disegno

Ora che le posizioni è disponibile per la geometria definita, è possibile scrivere codice per il rendering.

Innanzitutto, è necessario definire un `BasicEffect` istanza che conterrà i parametri per il rendering, ad esempio posizione e illuminazione. A tale scopo, aggiungere un `BasicEffect` membro per il `Game1` classe sotto la posizione di `floorVerts` campo viene definito:


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

Successivamente, modificare il `Initialize` metodo per definire l'effetto:

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

È ora possibile aggiungere codice per eseguire il disegno:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);


    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
            // We’ll be rendering two trinalges
            PrimitiveType.TriangleList,
            // The array of verts that we want to render
            floorVerts,
            // The offset, which is 0 since we want to start 
            // at the beginning of the floorVerts array
            0,
            // The number of triangles to draw
            2);
    }
}
```

È necessario chiamare `DrawGround` nel nostro `Game1.Draw`:

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

L'app verrà visualizzato quanto segue quando eseguita:

![](part2-images/image7.png "L'applicazione visualizzerà questo quando eseguita")

Ecco alcune informazioni nel codice precedente.

### <a name="view-and-projection-properties"></a>Visualizzare e le proprietà di proiezione

Il `View` e `Projection` proprietà consentono di controllare come si visualizza la scena. Si verrà modificato il codice in un secondo momento quando si aggiunge nuovamente il codice per il rendering del modello. In particolare, `View` controlla la posizione e l'orientamento della fotocamera, e `Projection` controlli il *campo visivo* (che consente di ingrandire la fotocamera).

### <a name="techniques-and-passes"></a>Le tecniche e passa

Una volta è stato assegnato proprietà in questo effetto, che è possibile eseguire il rendering effettivo. 

Si verrà modificato il `CurrentTechnique` proprietà in questa procedura dettagliata, ma più avanzati giochi può avere un unico effetto che può eseguire il disegno in modi diversi (ad esempio di come viene applicato il valore di colore). Ognuna di queste modalità di rendering può essere rappresentato come una tecnica che può essere assegnata prima del rendering. Inoltre, ogni tecnica può richiedere più passaggi per eseguire il rendering correttamente. Se il rendering di oggetti visivi complessi, ad esempio una superficie brillante o pellicce, effetti potrebbe essere necessario più passaggi.

La cosa importante da ricordare è che il `foreach` ciclo consente lo stesso codice c# per il rendering di qualsiasi effetto indipendentemente dalla complessità del sottostante `BasicEffect`.

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives` è in cui i vertici vengono sottoposti a rendering. Il primo parametro indica il metodo come è stato organizzato i vertici. È stato strutturato in modo che ogni triangolo è definito da tre vertici ordinati, pertanto viene usato il `PrimitiveType.TriangleList` valore.

Il secondo parametro è una matrice di vertici che è stato definito in precedenza.

Il terzo parametro specifica il primo indice da disegnare. Poiché si desidera che la matrice di intero vertex da sottoporre a rendering, si verrà passato un valore pari a 0.

Infine, si specificano il numero di triangoli per eseguire il rendering. La matrice di vertex contiene due triangoli, quindi passare il valore 2.

## <a name="rendering-with-a-texture"></a>Il rendering con una trama

A questo punto l'app esegue il rendering di un piano bianco (in prospettiva). Una trama successivo verrà aggiunto al progetto da utilizzare durante il rendering di questo piano. 

Per semplificare le operazioni di PNG verrà aggiunto direttamente al progetto, anziché utilizzare lo strumento di MonoGame Pipeline. A tale scopo, scaricare [questo file con estensione PNG](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true) nel computer. Una volta scaricato, fare clic su di **contenuto** cartella nel riquadro soluzione e scegliere **Aggiungi > Aggiungi file...**  . Se si lavora su Android, questa cartella verrà creata sotto il **asset** cartella nel progetto Android specifiche. Se in iOS, questa cartella verrà inserito la radice del progetto iOS. Passare al percorso in cui **checkerboard.png** viene salvato e selezionare il file. Selezionare questa opzione per copiare il file nella directory.

Successivamente, verrà aggiunto il codice per creare il nostro `Texture2D` istanza. Aggiungere innanzitutto i `Texture2D` come membro del `Game1` sotto il `BasicEffect` istanza:

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

Modificare `Game1.LoadContent` come indicato di seguito:


```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
}
```

Successivamente, modificare il `DrawGround` metodo. La modifica sola necessaria consiste nell'assegnare `effect.TextureEnabled` a `true` e impostare il `effect.Texture` a `checkerboardTexture`:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

    // new code:
    effect.TextureEnabled = true;
    effect.Texture = checkerboardTexture;

    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
                    PrimitiveType.TriangleList,
            floorVerts,
            0,
            2);
    }
}
```

Infine, è necessario modificare il `Game1.Initialize` metodo inoltre assegnare trama coordinate sul nostro vertici:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

Se si esegue il codice, si noterà che il piano viene ora visualizzato un motivo a scacchi:

![](part2-images/image8.png "Il piano viene ora visualizzato un motivo a scacchi")

## <a name="modifying-texture-coordinates"></a>Le coordinate di trama di modifica

Usa MonoGame normalizzata le coordinate di trama, vengono coordinate tra 0 e 1 anziché tra 0 e la larghezza della trama o dell'altezza. Nel diagramma seguente consente di visualizzare le coordinate normalizzate:

![](part2-images/image9.png "Questo diagramma consente di visualizzare le coordinate normalizzate")

Le coordinate di trama normalizzata consentono il ridimensionamento di trama senza la necessità di riscrivere il codice o ricreare i modelli (ad esempio file con estensione fbx). Questo è possibile perché coordinate normalizzate rappresentano una percentuale anziché di pixel specifico. Ad esempio, verranno (1,1) rappresentano sempre nell'angolo in basso a destra indipendentemente dalle dimensioni della trama.

È possibile modificare l'assegnazione di coordinate di trama per utilizzare una singola variabile per il numero di ripetizioni:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    int repetitions = 20;

    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
    floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

Ciò comporta la trama ripetuta 20 volte:

![](part2-images/image10.png "Di conseguenza, la trama ripetuta 20 volte")


## <a name="rendering-vertices-with-models"></a>Rendering dei vertici con modelli

Ora che il piano viene eseguito il rendering correttamente, è possibile aggiungere nuovamente i modelli per visualizzare tutti gli elementi insieme. In primo luogo, verrà aggiunto nuovamente il codice del modello per il nostro `Game1.Draw` metodo (con posizioni modificate):

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
} 
```

Verrà inoltre creato un `Vector3` in `Game1` per rappresentare la posizione della camera nostri. Verrà aggiunto un campo con il nostro `checkerboardTexture` dichiarazione:

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10); 
```

Successivamente, si rimuove locale `cameraPosition` variabile il `DrawModel` metodo:

```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector); 
            ...
```

Rimuovere in modo analogo locale `cameraPosition` variabile il `DrawGround` metodo:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ... 
```

Se si esegue il codice è ora possiamo vedere sia i modelli e zero nello stesso momento:

![](part2-images/image11.png "Sia i modelli e i dati vengono visualizzati nello stesso momento")

Se si modifica la posizione della camera (ad esempio aumentando il valore di X, che in questo caso consente di spostare la camera a sinistra) possiamo vedere che il valore influisce su zero sia i modelli:

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

Questo codice produce quanto segue:

![](part2-images/image3.png "Questo codice genera in questa visualizzazione")

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata viene illustrato come utilizzare una matrice di vertici per eseguire il rendering personalizzato. In questo caso, viene creato un piano Scacchi combinando il rendering basato su vertice con una trama e `BasicEffect`, ma il codice fornite in questo documento viene utilizzato come base per l'eventuale rendering 3D. Abbiamo anche mostrato anche che per il rendering basato su vertice può essere combinato con i modelli nella scena stesso.

## <a name="related-links"></a>Collegamenti correlati

- [File scacchiera (esempio)](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [Progetto completato (esempio)](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)
