---
title: Disegno di grafica 3D con vertici in MonoGame
description: MonoGame supporta l'utilizzo di matrici di vertici per definire la modalità di rendering in base al punto di un oggetto 3D. Gli utenti possono sfruttare le matrici di vertici per creare una geometria dinamica, implementare effetti speciali e migliorare l'efficienza delle loro il rendering tramite della faccia posteriore.
ms.prod: xamarin
ms.assetid: 932AF5C2-884D-46E1-9455-4C359FD7C092
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: df36c149e98e8c0cbb16de4c2cf52def5713ec13
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61178589"
---
# <a name="drawing-3d-graphics-with-vertices-in-monogame"></a>Disegno di grafica 3D con vertici in MonoGame

_MonoGame supporta l'utilizzo di matrici di vertici per definire la modalità di rendering in base al punto di un oggetto 3D. Gli utenti possono sfruttare le matrici di vertici per creare una geometria dinamica, implementare effetti speciali e migliorare l'efficienza delle loro il rendering tramite della faccia posteriore._

Gli utenti che hanno già letto tramite il [Guida per il rendering di modelli](~/graphics-games/monogame/3d/part1.md) avranno familiarità con il rendering di un modello 3D in MonoGame. Il `Model` classe è un metodo efficace per eseguire il rendering di grafica 3D quando si lavora con i dati definiti in un file (ad esempio. fbx) e quando si lavora con dati statici. Alcuni giochi richiedono la geometria 3D da definire o modificati in modo dinamico in fase di esecuzione. In questi casi, è possibile usare le matrici di *vertici* per definire ed eseguire il rendering della geometria. Il vertice è un termine generale per un punto nello spazio 3D che fa parte di un elenco ordinato consente di definire la geometria. In genere i vertici vengono ordinati in modo da definire una serie di triangoli.

Per semplificare la visualizzazione come vertici vengono utilizzati per creare oggetti 3D, si consideri la sfera seguente:

![](part2-images/image1.png "Per semplificare la visualizzazione come vertici vengono utilizzati per creare oggetti 3D, prendere in considerazione questo sfera")

Come illustrato in precedenza, la sfera è chiaramente composta da più di triangoli. È possibile visualizzare la struttura della sfera per visualizzare come vertici connettono a triangoli form:

![](part2-images/image2.png "Visualizzare la struttura della sfera per visualizzare come vertici connettono a triangoli form")

Questa procedura dettagliata verrà trattati i seguenti argomenti:

- Creazione di un progetto
- Creazione di vertici
- Aggiunta di codice di disegno
- Rendering con una trama
- Modifica le coordinate di trama
- I vertici con modelli di rendering

Progetto finito conterrà un piano a scacchi che verrà disegnato usando una matrice di vertice:

![](part2-images/image3.png "Il progetto completato conterrà un piano a scacchi che verrà disegnato usando una matrice di vertice")

## <a name="creating-a-project"></a>Creazione di un progetto

In primo luogo, si verrà scaricato un progetto che verrà utilizzata come il punto di partenza. Si userà il progetto di modello [cui è disponibile qui](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/).

Dopo aver scaricato e decompresso, aprire ed eseguire il progetto. Si prevede di visualizzare sei modelli robot da disegnare sullo schermo:

![](part2-images/image4.png "Sei modelli robot da disegnare sullo schermo")

Entro la fine di questo progetto si sarà combinazione personalizzato per il rendering del vertice personalizzato con i robot `Model`, in modo che non ci occuperemo per eliminare il codice per il rendering di robot. Al contrario, viene possibile semplicemente cancellare le il `Game1.Draw` chiamata per rimuovere il disegno dei 6 robot per il momento. A tale scopo, aprire il **Game1.cs** del file e individuare il `Draw` (metodo). Modificarla in modo che contenga il codice seguente:

```csharp
protected override void Draw(GameTime gameTime)
{
  GraphicsDevice.Clear(Color.CornflowerBlue);
  base.Draw(gameTime);
}
```

Ciò comporterà il gioco visualizzando una schermata blu vuota:

![](part2-images/image5.png "L'operazione comporterà il gioco visualizzando una schermata blu vuota")

## <a name="creating-the-vertices"></a>Creazione di vertici

Si creerà una matrice di vertici per definire la geometria. In questa procedura dettagliata, verrà creato un piano 3D (un quadrato nello spazio 3D, non un aereo). Anche se il piano ha quattro angoli e quattro i lati, si sarà composta da due triangoli, ognuno dei quali richiede tre vertici. Pertanto, si verrà definito un totale di sei punti.

Finora è stato già accennato vertici in senso generale, ma MonoGame fornisce alcuni struct standard che può essere usato per i vertici:

- `Microsoft.Xna.Framework.Graphics.VertexPositionColor`
- `Microsoft.Xna.Framework.Graphics.VertexPositionColorTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionNormalTexture`
- `Microsoft.Xna.Framework.Graphics.VertexPositionTexture`

Nome del tipo di ogni indica i componenti in che esso contenuti. Ad esempio, `VertexPositionColor` contiene valori per la posizione e il colore. Esaminiamo ciascuno dei componenti:

- Posizione: tutti i vertici che includono un `Position` componente. Il `Position` valori definiscono dove si trova sul vertice nello spazio 3D (X, Y e Z).
- Colore: vertici, è possibile specificare un `Color` valore eseguire tinte personalizzato.
- Normale: Normals definiscono in che modo è rivolta la superficie dell'oggetto. Le normali sono necessari che se il rendering di un oggetto con illuminazione poiché la direzione che impatti chiaro quanto che riceve è rivolta verso una superficie. Le normali sono in genere specificate come un *vettore unitario* – un vettore 3D con una lunghezza pari a 1.
- Trama-trama fa riferimento a coordinate di trama, vale a dire, quale parte di una trama dovrebbe essere posizionata in un determinato vertice. I valori di trama sono necessari se il rendering di un oggetto 3D con una trama. Le coordinate di trama sono coordinate normalizzate, che significa che i valori verranno compreso tra 0 e 1. Illustreremo le coordinate di trama in modo più dettagliato più avanti in questa Guida.

Il piano fungerà da un piano e si voglia applicare una trama quando si esegue il rendering, in modo che si userà il `VertexPositionTexture` tipo per definire i vertici.

In primo luogo, viene aggiunto un membro alla nostra classe Game1:

```csharp
VertexPositionTexture[] floorVerts; 
```

Successivamente, definire i vertici in `Game1.Initialize`. Si noti che il modello fornito fa riferimento più indietro in questo articolo non contiene un `Game1.Initialize` metodo, pertanto è necessario aggiungere l'intero metodo a `Game1`:

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

Per consentire di visualizzare i vertici avrà un aspetto, ad esempio, si consideri il diagramma seguente:

![](part2-images/image6.png "Per semplificare la visualizzazione dei vertici aspetto, prendere in considerazione questo diagramma")

È necessario fare affidamento sul nostro diagramma per visualizzare i vertici fino a quando non è completare l'implementazione di codice per il rendering.

## <a name="adding-drawing-code"></a>Aggiunta di codice del disegno

Ora che abbiamo le posizioni per la geometria definita, è possibile scrivere codice per il rendering.

In primo luogo, sarà necessario definire un `BasicEffect` istanza che conterrà i parametri per il rendering, ad esempio posizione e illuminazione. A questo scopo, aggiungere un `BasicEffect` membro per il `Game1` classe inferiore rispetto alla posizione di `floorVerts` campo viene definito:


```csharp
...
VertexPositionTexture[] floorVerts;
// new code:
BasicEffect effect;
```

A questo punto, modificare il `Initialize` metodo per definire l'effetto:

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

A questo punto è possibile aggiungere codice per eseguire le operazioni di disegno:

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

È necessario chiamare `DrawGround` nel nostro `Game1.Draw`:

```csharp
protected override void Draw (GameTime gameTime)
{
    GraphicsDevice.Clear (Color.CornflowerBlue);

    DrawGround ();

    base.Draw (gameTime);
}
```

L'app visualizzerà quanto segue quando eseguita:

![](part2-images/image7.png "L'app visualizzerà questo quando eseguita")

Esaminiamo alcuni dettagli nel codice precedente.

### <a name="view-and-projection-properties"></a>Visualizzare e le proprietà di proiezione

Il `View` e `Projection` proprietà controllano come considereremo della scena. Si verrà modificato il codice in un secondo momento quando si aggiunge nuovamente il codice del modello di rendering. In particolare `View` controlla la posizione e l'orientamento della fotocamera, e `Projection` controlli il *campo visivo* (che può essere utilizzata per zoom della fotocamera).

### <a name="techniques-and-passes"></a>Le tecniche e passa

Una volta è stato assegnato proprietà sul nostro effettiva che è possibile eseguire il rendering effettivo. 

Microsoft non verrà modificato il `CurrentTechnique` proprietà in questa procedura dettagliata, ma i giochi più avanzati potrebbero avere un singolo effetto che può eseguire il disegno in modi diversi (ad esempio come viene applicato il valore del colore). Ognuna di queste modalità di rendering può essere rappresentato come una tecnica che può essere assegnata prima del rendering. Inoltre, ogni tecnica potrebbe necessitare di più passaggi per eseguire il rendering correttamente. Se il rendering di oggetti visivi complessi, ad esempio un surface brillante o pelo, effetti potrebbe essere necessario più passaggi.

La cosa importante da tenere presente è che il `foreach` ciclo consente lo stesso C# codice per eseguire il rendering di alcun effetto indipendentemente dalla complessità dell'oggetto sottostante `BasicEffect`.

### <a name="drawuserprimitives"></a>DrawUserPrimitives

`DrawUserPrimitives` è in cui viene eseguito il rendering dei vertici. Il primo parametro indica il metodo come sono stati organizzati i vertici. È stata strutturata in modo che ogni triangolo è definito da tre vertici ordinati, perciò usiamo il `PrimitiveType.TriangleList` valore.

Il secondo parametro è la matrice di vertici che è stato definito in precedenza.

Il terzo parametro specifica il primo indice da disegnare. Poiché si desidera che la matrice di intero vertice da sottoporre a rendering, passiamo a un valore pari a 0.

Infine, specifichiamo quanti triangoli per eseguire il rendering. La matrice di vertice contiene due triangoli, quindi, passare un valore pari a 2.

## <a name="rendering-with-a-texture"></a>Rendering con una trama

A questo punto l'app esegue il rendering di un piano di bianco (nel punto di vista). Successivamente si aggiungerà una trama al progetto da utilizzare per il rendering del nostro piano. 

Per semplificare le operazioni si aggiungerà il PNG direttamente alla parte del progetto anziché utilizzare lo strumento della MonoGame Pipeline. A tale scopo, scaricare [questo file con estensione PNG](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true) nel computer. Al termine del download, fare clic sul **contenuti** cartella nel riquadro della soluzione e selezionare **Aggiungi > Aggiungi file...**  . Se si lavora su Android, questa cartella verrà creata sotto la **asset** cartella nel progetto Android specifici. Se in iOS, questa cartella sarà nella radice del progetto iOS. Passare al percorso in cui **checkerboard.png** viene salvato e selezionare questo file. Selezionare questa opzione per copiare il file nella directory.

Successivamente, si aggiungerà il codice per creare il `Texture2D` istanza. In primo luogo, aggiungere il `Texture2D` come membro del `Game1` sotto il `BasicEffect` istanza:

```csharp
...
BasicEffect effect;
// new code:
Texture2D checkerboardTexture;
```

Modificare `Game1.LoadContent` come indicato di seguito:


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

A questo punto, modificare il `DrawGround` (metodo). L'unica modifica necessaria consiste nell'assegnare `effect.TextureEnabled` al `true` e impostare le `effect.Texture` a `checkerboardTexture`:

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

Infine, è necessario modificare il `Game1.Initialize` metodo anche assegnare trama coordina sui nostri vertici:


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

Se eseguiamo il codice, è possibile osservare che il piano viene ora visualizzato un motivo a scacchi:

![](part2-images/image8.png "Il piano viene ora visualizzato un motivo a scacchi")

## <a name="modifying-texture-coordinates"></a>Coordina la modifica di trama

MonoGame utilizzata normalizzato le coordinate di trama, vengono coordinate compreso tra 0 e 1 anziché tra 0 e la larghezza della trama o dell'altezza. Il diagramma seguente può semplificare la visualizzazione delle coordinate normalizzate:

![](part2-images/image9.png "Questo diagramma può semplificare la visualizzazione delle coordinate normalizzate")

Le coordinate di trama normalizzato consentono il ridimensionamento della trama senza la necessità di riscrivere il codice o ricreare i modelli (ad esempio i file. fbx). Questo è possibile perché normalizzate coordinate rappresentano una percentuale anziché pixel specifico. Ad esempio, verranno (1,1) rappresentano sempre l'angolo inferiore destro indipendentemente dalle dimensioni della trama.

È possibile modificare l'assegnazione di coordinate di trama per usare una singola variabile per il numero di ripetizioni:


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

Ciò comporta la trama ripetere 20 volte:

![](part2-images/image10.png "Ciò comporta la trama ripetere 20 volte")


## <a name="rendering-vertices-with-models"></a>I vertici con modelli di rendering

Ora che il piano viene eseguito il rendering correttamente, è possibile aggiungere nuovamente i modelli per visualizzare tutti gli elementi tra loro. In primo luogo, si aggiungerà nuovamente il codice di modello al nostro `Game1.Draw` metodo (con posizioni di modificare):

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

Verrà anche creato un `Vector3` in `Game1` per rappresentare la posizione della camera nostro. Si aggiungerà un campo nel nostro `checkerboardTexture` dichiarazione:

```csharp
...
Texture2D checkerboardTexture;
// new code:
Vector3 cameraPosition = new Vector3(0, 10, 10); 
```

Successivamente, rimuovere locale `cameraPosition` variabile dal `DrawModel` metodo:

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

Rimuovere in modo analogo locale `cameraPosition` variabile dal `DrawGround` metodo:

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

Se eseguiamo il codice si può vedere ora entrambi i modelli e zero allo stesso tempo:

![](part2-images/image11.png "I modelli sia zero vengono visualizzati nello stesso momento")

Se si modifica la posizione della camera (ad esempio aumentando il valore di X, che in questo caso consente di spostare la fotocamera a sinistra) si noterà che il valore influisce sia zero e i modelli:

```csharp
Vector3 cameraPosition = new Vector3(15, 10, 10);
```

Questo codice produce quanto segue:

![](part2-images/image3.png "Questo codice genera in questa visualizzazione")

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata ha illustrata come usare una matrice di vertici per eseguire il rendering personalizzato. In questo caso, abbiamo creato un piano a scacchi combinando il rendering basato su vertici con una trama e `BasicEffect`, ma il codice presentato qui viene utilizzato come base per l'eventuale rendering in 3D. È stato inoltre illustrato che il rendering di vertici in base può essere combinato con i modelli nella scena stesso.

## <a name="related-links"></a>Collegamenti correlati

- [File scacchiera (esempio)](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/checkerboard.png?raw=true)
- [Progetto completato (esempio)](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/)
