---
title: Disegno di immagini 3D con vertici in monogame
description: Monogame supporta l'uso di matrici di vertici per definire la modalità di rendering di un oggetto 3D in base al punto. Gli utenti possono sfruttare gli array di vertici per creare geometria dinamica, implementare effetti speciali e migliorare l'efficienza del rendering attraverso l'abbattimento.
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: f125f8f20d22da4e988440cbaa936771d86a7673
ms.sourcegitcommit: f255aa286bd52e8a80ffa620c2e93c97f069f8ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68680975"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>Disegno di immagini 3D con vertici in monogame

_Monogame supporta l'uso di matrici di vertici per definire la modalità di rendering di un oggetto 3D in base al punto. Gli utenti possono sfruttare gli array di vertici per creare geometria dinamica, implementare effetti speciali e migliorare l'efficienza del rendering attraverso l'abbattimento._

Gli utenti che hanno letto la [Guida sui modelli di rendering](~/graphics-games/monogame/3d/part1.md) avranno familiarità con il rendering di un modello 3D in monogame. La `Model` classe è un modo efficace per eseguire il rendering della grafica 3D quando si utilizzano i dati definiti in un file, ad esempio FBX, e quando si gestiscono dati statici. Per alcuni giochi è necessario definire o modificare dinamicamente la geometria 3D in fase di esecuzione. In questi casi, è possibile usare matrici di *vertici* per definire ed eseguire il rendering della geometria. Un vertice è un termine generale per un punto nello spazio 3D che fa parte di un elenco ordinato usato per definire la geometria. In genere i vertici sono ordinati in modo da definire una serie di triangoli.

Per facilitare la visualizzazione del modo in cui i vertici vengono usati per creare oggetti 3D, consideriamo la sfera seguente:

![](part2-images/image1.png "Per facilitare la visualizzazione del modo in cui vengono usati i vertici per creare oggetti 3D, considerare questa sfera")

Come illustrato in precedenza, la sfera è chiaramente composta da più triangoli. È possibile visualizzare il wireframe della sfera per vedere il modo in cui i vertici si connettono ai triangoli dei form:

![](part2-images/image2.png "Visualizzare il wireframe della sfera per vedere il modo in cui i vertici si connettono ai triangoli dei form")

In questa procedura dettagliata vengono trattati gli argomenti seguenti:

- Creazione di un progetto
- Creazione dei vertici
- Aggiunta del codice di disegno
- Rendering con trama
- Modifica di coordinate di trama
- Rendering dei vertici con i modelli

Il progetto finito conterrà un piano a scacchi che verrà disegnato usando una matrice di vertici:

![](part2-images/image3.png "Il progetto finito conterrà un piano a scacchi che verrà disegnato usando una matrice di vertici")

## <a name="creating-a-project"></a>Creazione di un progetto

Prima di tutto, verrà scaricato un progetto che fungerà da punto di partenza. Verrà usato il progetto di modello [disponibile qui](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelrenderingmg/).

Una volta scaricato e decompresso, aprire ed eseguire il progetto. Si prevede di visualizzare sei modelli robotizzati disegnati sullo schermo:

![](part2-images/image4.png "Sei modelli robotizzati disegnati sullo schermo")

Alla fine di questo progetto verrà combinato il rendering del vertice personalizzato con il robot `Model`, quindi non verrà eliminato il codice di rendering del robot. Al contrario, è sufficiente cancellare `Game1.Draw` la chiamata per rimuovere il disegno dei 6 robot per il momento. A tale scopo, aprire il file **Game1.cs** e individuare il `Draw` metodo. Modificarlo in modo che contenga il codice seguente:

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

Questo comporterà la visualizzazione di una schermata blu vuota nel gioco:

![](part2-images/image5.png "Questo comporterà la visualizzazione di una schermata blu vuota nel gioco")

## <a name="creating-the-vertices"></a>Creazione dei vertici

Viene creata una matrice di vertici per definire la geometria. In questa procedura dettagliata verrà creato un piano 3D (un quadrato nello spazio 3D, non un aeroplano). Sebbene il piano abbia quattro lati e quattro angoli, sarà composto da due triangoli, ognuno dei quali richiede tre vertici. Pertanto, verrà definito un totale di sei punti.

Finora abbiamo parlato dei vertici in senso generale, ma monogame fornisce alcuni struct standard che possono essere usati per i vertici:

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

Il nome di ogni tipo indica i componenti in essa contenuti. Contiene, ad `VertexPositionColor` esempio, i valori per position e color. Esaminiamo ogni componente:

- Position: tutti i tipi di vertici includono un `Position` componente. I `Position` valori definiscono la posizione in cui si trova il vertice nello spazio 3D (X, Y e Z).
- Color: i vertici possono facoltativamente specificare `Color` un valore per eseguire la colorazione personalizzata.
- Normale: i normali definiscono il modo in cui la superficie dell'oggetto è rivolte. I normali sono necessari se il rendering di un oggetto con illuminazione dopo la direzione di una superficie ha un effetto sulla quantità di luce che riceve. Le normali vengono in genere specificate come *vettore di unità* , ovvero un vettore 3D con lunghezza pari a 1.
- Trama: la trama si riferisce alle coordinate di trama, ovvero la parte di una trama da visualizzare in un vertice specificato. I valori di trama sono necessari se si esegue il rendering di un oggetto 3D con una trama. Le coordinate di trama sono coordinate normalizzate, il che significa che i valori saranno compresi tra 0 e 1. Le coordinate di trama verranno illustrate in modo più dettagliato più avanti in questa guida.

Il piano verrà usato come base e si desidera applicare una trama durante l'esecuzione del rendering, quindi verrà usato il `VertexPositionTexture` tipo per definire i vertici.

In primo luogo, verrà aggiunto un membro alla classe Game1:

```csharp
VertexPositionTexture[] floorVerts; 
```

Definire quindi i vertici in `Game1.Initialize`. Si noti che il modello fornito a cui viene fatto riferimento in precedenza in questo `Game1.Initialize` articolo non contiene un metodo, quindi è necessario aggiungere l' `Game1`intero metodo a:

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

Per semplificare la visualizzazione dei vertici, prendere in considerazione il diagramma seguente:

![](part2-images/image6.png "Per semplificare la visualizzazione dei vertici, considerare questo diagramma")

È necessario basarsi sul diagramma per visualizzare i vertici fino a quando non viene completata l'implementazione del codice di rendering.

## <a name="adding-drawing-code"></a>Aggiunta del codice di disegno

Ora che sono state definite le posizioni per la geometria, è possibile scrivere il codice di rendering.

Prima di tutto, è necessario definire un' `BasicEffect` istanza che conterrà i parametri per il rendering, ad esempio posizione e illuminazione. A tale scopo, aggiungere un `BasicEffect` membro `Game1` alla classe di seguito in cui `floorVerts` è definito il campo:


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

Modificare quindi il `Initialize` metodo per definire l'effetto:

```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;
    // new code:
    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

A questo punto è possibile aggiungere il codice per eseguire il disegno:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);


    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
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

È necessario chiamare `DrawGround`: `Game1.Draw`

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

Quando viene eseguita, l'app visualizzerà quanto segue:

![](part2-images/image7.png "L'app visualizzerà questa operazione al momento dell'esecuzione")

Esaminiamo alcuni dei dettagli nel codice riportato sopra.

### <a name="view-and-projection-properties"></a>Proprietà di visualizzazione e proiezione

Le `View` proprietà `Projection` e controllano la modalità di visualizzazione della scena. Questo codice verrà modificato in un secondo momento, quando si aggiunge di nuovo il codice per il rendering del modello. In particolare `View` , controlla la posizione e l'orientamento della fotocamera e `Projection` controlla il *campo di visualizzazione* , che può essere usato per ingrandire la fotocamera.

### <a name="techniques-and-passes"></a>Tecniche e sessioni

Una volta assegnate le proprietà, è possibile eseguire il rendering effettivo. 

Non verrà modificata la proprietà `CurrentTechnique` in questa procedura dettagliata, ma i giochi più avanzati possono avere un singolo effetto che può eseguire il disegno in modi diversi, ad esempio come viene applicato il valore del colore. Ognuna di queste modalità di rendering può essere rappresentata come una tecnica che può essere assegnata prima del rendering. Ogni tecnica può inoltre richiedere più passaggi per il rendering corretto. Gli effetti possono richiedere più sessioni se si esegue il rendering di oggetti visivi complessi, ad esempio una superficie o una pelliccia incandescente.

L'aspetto importante da tenere presente è che il `foreach` ciclo consente allo stesso C# codice di eseguire il rendering di qualsiasi effetto indipendentemente dalla complessità dell'oggetto `BasicEffect`sottostante.

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives`è il punto in cui viene eseguito il rendering dei vertici. Il primo parametro indica al metodo come sono stati organizzati i vertici. Sono state strutturate in modo che ogni triangolo venga definito da tre vertici ordinati, quindi viene usato `PrimitiveType.TriangleList` il valore.

Il secondo parametro è la matrice dei vertici definiti in precedenza.

Il terzo parametro specifica il primo indice da creare. Poiché si vuole che venga eseguito il rendering dell'intera matrice di vertici, viene passato un valore pari a 0.

Infine, viene specificato il numero di triangoli di cui eseguire il rendering. La matrice di vertici contiene due triangoli, quindi passare il valore 2.

## <a name="rendering-with-a-texture"></a>Rendering con trama

A questo punto, l'app esegue il rendering di un piano bianco (in prospettiva). Si aggiungerà quindi una trama al progetto da usare quando si esegue il rendering del piano. 

Per semplificare l'attività, aggiungeremo il. png direttamente al progetto anziché usare lo strumento della pipeline monogame. A tale scopo, scaricare il [file con estensione png](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true) nel computer. Al termine del download, fare clic con il pulsante destro del mouse sulla cartella **contenuto** nel riquadro della soluzione e scegliere **Aggiungi > Aggiungi file.** Se si usa Android, questa cartella si trova nella cartella assets del progetto specifico per Android. Se si utilizza iOS, la cartella si troverà nella radice del progetto iOS. Passare al percorso in cui è stato salvato il file **scacchi. png** e selezionare il file. Selezionare per copiare il file nella directory.

Successivamente, verrà aggiunto il codice per creare `Texture2D` l'istanza. `Texture2D` Per prima cosa, aggiungere come membro di `Game1` sotto l' `BasicEffect` istanza:

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

Modificare `Game1.LoadContent` come segue:


```csharp
protected override void LoadContent()
{
    // Notice that loading a model is very similar
    // to loading any other XNB (like a Texture2D).
    // The only difference is the generic type.
    model = Content.Load<Model> ("robot");

    // We aren't using the content pipeline, so we need
    // to access the stream directly:
    using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
    {
        checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
    }
}
```

Modificare quindi il `DrawGround` metodo. L'unica modifica necessaria consiste nell'assegnare `effect.TextureEnabled` a `true` e a impostare `effect.Texture` su `checkerboardTexture`:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraPosition = new Vector3 (0, 40, 20);
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);

    float aspectRatio = 
        graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
    float nearClipPlane = 1;
    float farClipPlane = 200;

    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

    // new code:
    effect.TextureEnabled = true;
    effect.Texture = checkerboardTexture;

    foreach (var pass in effect.CurrentTechnique.Passes)
    {
        pass.Apply ();

        graphics.GraphicsDevice.DrawUserPrimitives (
                    PrimitiveType.TriangleList,
            floorVerts,
            0,
            2);
    }
}
```

Infine, è necessario modificare il metodo `Game1.Initialize` in modo da assegnare anche le coordinate di trama sui vertici:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    // New code:
    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, 1);
    floorVerts [2].TextureCoordinate = new Vector2 (1, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (1, 1);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
} 
```

Se eseguiamo il codice, possiamo notare che il nostro piano ora Visualizza un modello a scacchi:

![](part2-images/image8.png "Il piano ora Visualizza un modello a scacchi")

## <a name="modifying-texture-coordinates"></a>Modifica di coordinate di trama

Monogame USA coordinate di trama normalizzate, ovvero coordinate comprese tra 0 e 1 anziché tra 0 e la larghezza o l'altezza della trama. Il diagramma seguente può essere utile per visualizzare le coordinate normalizzate:

![](part2-images/image9.png "Questo diagramma può essere utile per visualizzare le coordinate normalizzate")

Le coordinate di trama normalizzate consentono il ridimensionamento della trama senza la necessità di riscrivere il codice o di ricreare i modelli, ad esempio i file con estensione FBX. Questo è possibile perché le coordinate normalizzate rappresentano un rapporto anziché pixel specifici. Ad esempio, (1,1) rappresenterà sempre l'angolo inferiore destro indipendentemente dalle dimensioni della trama.

È possibile modificare l'assegnazione delle coordinate di trama per usare una singola variabile per il numero di ripetizioni:


```csharp
protected override void Initialize ()
{
    floorVerts = new VertexPositionTexture[6];

    floorVerts [0].Position = new Vector3 (-20, -20, 0);
    floorVerts [1].Position = new Vector3 (-20,  20, 0);
    floorVerts [2].Position = new Vector3 ( 20, -20, 0);

    floorVerts [3].Position = floorVerts[1].Position;
    floorVerts [4].Position = new Vector3 ( 20,  20, 0);
    floorVerts [5].Position = floorVerts[2].Position;

    int repetitions = 20;

    floorVerts [0].TextureCoordinate = new Vector2 (0, 0);
    floorVerts [1].TextureCoordinate = new Vector2 (0, repetitions);
    floorVerts [2].TextureCoordinate = new Vector2 (repetitions, 0);

    floorVerts [3].TextureCoordinate = floorVerts[1].TextureCoordinate;
    floorVerts [4].TextureCoordinate = new Vector2 (repetitions, repetitions);
    floorVerts [5].TextureCoordinate = floorVerts[2].TextureCoordinate;

    effect = new BasicEffect (graphics.GraphicsDevice);

    base.Initialize ();
}
```

In questo modo la trama viene ripetuta 20 volte:

![](part2-images/image10.png "In questo modo la trama viene ripetuta 20 volte")


## <a name="rendering-vertices-with-models"></a>Rendering dei vertici con i modelli

Ora che è in corso il rendering corretto del piano, è possibile aggiungere nuovamente i modelli per visualizzare tutti gli elementi insieme. In primo luogo, il codice del modello verrà aggiunto nuovamente al `Game1.Draw` metodo (con le posizioni modificate):

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    DrawGround ();

    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));

    DrawModel (new Vector3 (-4, 4, 3));
    DrawModel (new Vector3 ( 0, 4, 3));
    DrawModel (new Vector3 ( 4, 4, 3));

    base.Draw(gameTime);
} 
```

Viene anche creato un `Vector3` in `Game1` per rappresentare la posizione della fotocamera. Si aggiungerà un campo nella `checkerboardTexture` dichiarazione:

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10); 
```

Rimuovere quindi la variabile locale `cameraPosition` `DrawModel` dal metodo:

```csharp
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = Matrix.CreateTranslation (modelPosition);

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector); 
            ...
```

Analogamente, rimuovere `cameraPosition` la variabile locale `DrawGround` dal metodo:

```csharp
void DrawGround()
{
    // The assignment of effect.View and effect.Projection
    // are nearly identical to the code in the Model drawing code.
    var cameraLookAtVector = Vector3.Zero;
    var cameraUpVector = Vector3.UnitZ;

    effect.View = Matrix.CreateLookAt (
        cameraPosition, cameraLookAtVector, cameraUpVector);
    ... 
```

A questo punto, se si esegue il codice, è possibile visualizzare contemporaneamente i modelli e il campo:

![](part2-images/image11.png "Sia i modelli che il terreno vengono visualizzati contemporaneamente")

Se si modifica la posizione della fotocamera, ad esempio aumentando il valore X, che in questo caso sposta la fotocamera a sinistra, è possibile osservare che il valore influisca sia sulla base che sui modelli:

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

Questo codice comporta quanto segue:

![](part2-images/image3.png "Questo codice genera questa visualizzazione")

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stato illustrato come utilizzare una matrice di vertici per eseguire il rendering personalizzato. In questo caso, è stato creato un piano a scacchi combinando il rendering basato su vertici con una `BasicEffect`trama e, ma il codice presentato viene utilizzato come base per qualsiasi rendering 3D. È stato inoltre illustrato che il rendering basato sui vertici può essere combinato con i modelli nella stessa scena.

## <a name="related-links"></a>Collegamenti correlati

- [File scacchi (esempio)](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [Progetto completato (esempio)](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelsandvertsmg/)
