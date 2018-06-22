---
title: Coordinate 3D in MonoGame
description: La comprensione del sistema di coordinate 3D è un passaggio importante dello sviluppo di giochi 3D. MonoGame fornisce una serie di classi per il posizionamento, orientamento e il ridimensionamento di oggetti nello spazio 3D.
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 2f14d21302ed4295d16baa28723df6ef79863686
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921638"
---
# <a name="3d-coordinates-in-monogame"></a>Coordinate 3D in MonoGame

_La comprensione del sistema di coordinate 3D è un passaggio importante dello sviluppo di giochi 3D. MonoGame fornisce una serie di classi per il posizionamento, orientamento e il ridimensionamento di oggetti nello spazio 3D._

Lo sviluppo di giochi 3D richiede la comprensione di come gestire gli oggetti in un sistema di coordinate 3D. Questa procedura dettagliata illustra le procedure modificare gli oggetti visivi (in particolare un modello). Creeremo sui concetti di controllo di un modello per creare una classe fotocamera 3D.

I concetti presentati provengono da algebra lineare, ma l'utente verrà reindirizzato a un approccio pratico in modo che qualsiasi utente senza uno sfondo matematica complessa è possibile applicare questi concetti nei propri giochi.

Parleremo gli argomenti seguenti:

- Creazione di un progetto
- Creazione di un'entità Robot
- Lo spostamento dell'entità Robot
- La moltiplicazione
- Creazione dell'entità della fotocamera
- Spostare la fotocamera con Input

Una volta terminato, è necessario un progetto con un robot lo spostamento in un cerchio e una fotocamera che può essere controllata da input tocco:

![](part3-images/image1.gif "Al termine, l'app verrà incluso un progetto con un robot lo spostamento in un cerchio e una fotocamera che può essere controllata da input tocco")


## <a name="creating-a-project"></a>Creazione di un progetto

Questa procedura dettagliata si concentra sullo spostamento di oggetti nello spazio 3D. Inizieremo con il progetto per il rendering di modelli e matrici di vertici [che sono disponibili qui](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/). Una volta scaricata, decomprimere e aprire il progetto per verificare che l'esecuzione e dovremmo vedere di seguito:

![](part3-images/image2.png "Una volta scaricata, decomprimere e aprire il progetto per verificare che l'esecuzione e questa visualizzazione")


## <a name="creating-a-robot-entity"></a>Creazione di un'entità Robot

Prima di iniziare lo spostamento i robot intorno, si creerà un `Robot` classe per contenere la logica di disegno e lo spostamento. Gli sviluppatori di giochi fare riferimento per l'incapsulamento della logica e i dati come un *entità*.

Aggiungere un nuovo file di classe vuota per il **MonoGame3D** libreria di classi portabile (non il ModelAndVerts.Android specifico della piattaforma). Il nome **Robot** e fare clic su **New**:

![](part3-images/image3.png "Nome Robot e fare clic su nuovo")

Modificare la `Robot` classe come indicato di seguito:

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace MonoGame3D
{
    public class Robot
    {
        Model model;

        public void Initialize(ContentManager contentManager)
        {
            model = contentManager.Load<Model> ("robot");

        }

        // For now we'll take these values in, eventually we'll
        // take a Camera object
        public void Draw(Vector3 cameraPosition, float aspectRatio)
        {
            foreach (var mesh in model.Meshes)
            {
                foreach (BasicEffect effect in mesh.Effects)
                {
                    effect.EnableDefaultLighting ();
                    effect.PreferPerPixelLighting = true;

                    effect.World = Matrix.Identity; 
                    var cameraLookAtVector = Vector3.Zero;
                    var cameraUpVector = Vector3.UnitZ;

                    effect.View = Matrix.CreateLookAt (
                        cameraPosition, cameraLookAtVector, cameraUpVector);

                    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                    float nearClipPlane = 1;
                    float farClipPlane = 200;

                    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);


                }

                // Now that we've assigned our properties on the effects we can
                // draw the entire mesh
                mesh.Draw ();
            }
        }
    }
}
```

Il `Robot` codice è essenzialmente lo stesso codice in `Game1` per disegnare un `Model`. Per informazioni generali su `Model` durante il caricamento e di disegno, vedere [questa Guida sull'utilizzo dei modelli](~/graphics-games/monogame/3d/part1.md). È ora possibile rimuovere tutti i `Model` durante il caricamento e il rendering di codice da `Game1`e sostituirlo con un `Robot` istanza:

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        Vector3 cameraPosition = new Vector3(15, 10, 10);

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

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

            robot = new Robot ();
            robot.Initialize (Content);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            robot.Draw (cameraPosition, aspectRatio);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
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
    }
}
```

Se si esegue il codice a questo punto si avrà una scena con solo un robot disegnato principalmente sotto il piano:

![](part3-images/image4.png "Se il codice viene eseguito a questo punto, l'applicazione visualizzerà una scena con solo un robot disegnato principalmente sotto il piano")

## <a name="moving-the-robot"></a>Lo spostamento del Robot

Ora che è disponibile un `Robot` (classe), è possibile aggiungere la logica di spostamento del robot. In questo caso, verrà semplicemente effettuata il robot spostare in un cerchio in base all'ora di gioco. Questa è un'implementazione piuttosto poco per un gioco reale poiché in genere risponda un carattere di input o intelligenza artificiale, ma offre un ambiente a Microsoft per esplorare il posizionamento 3D e la rotazione.

L'unica informazione è di fuori di dalla `Robot` classe è l'ora corrente del gioco. Verrà aggiunto un `Update` metodo che consentirà di passare un `GameTime` parametro. Questo `GameTime` parametro verrà utilizzato per incrementare la variabile di un angolo che verranno utilizzate per determinare la posizione finale per il robot.

In primo luogo, verrà aggiunto il campo angolo di `Robot` classe sotto la `model` campo:

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 Ora è possibile incrementare questo valore in un `Update` funzione:

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

È necessario assicurarsi che il `Update` metodo viene chiamato da `Game1.Update`:

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

Naturalmente, a questo punto il campo angolo non esegue alcuna operazione, è necessario scrivere codice per utilizzarlo. Verrà modificata la `Draw` metodo in modo che è possibile calcolare il mondo `Matrix` in un metodo dedicato: 

```csharp
public void Draw(Vector3 cameraPosition, float aspectRatio)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            // We’ll be doing our calculations here...
            effect.World = GetWorldMatrix();

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }

        mesh.Draw ();
    }
}
```

Successivamente, verrà implementato il `GetWorldMatrix` metodo la `Robot` classe:

```csharp
Matrix GetWorldMatrix()
{
    const float circleRadius = 8;
    const float heightOffGround = 3;

    // this matrix moves the model "out" from the origin
    Matrix translationMatrix = Matrix.CreateTranslation (
        circleRadius, 0, heightOffGround);

    // this matrix rotates everything around the origin
    Matrix rotationMatrix = Matrix.CreateRotationZ (angle);

    // We combine the two to have the model move in a circle:
    Matrix combined = translationMatrix * rotationMatrix;

    return combined;
}
```

Il risultato dell'esecuzione di questo codice genera il robot lo spostamento in un cerchio:

![](part3-images/image5.gif "L'esecuzione di questo genera codice del robot lo spostamento in un cerchio")

## <a name="matrix-multiplication"></a>La moltiplicazione

Il codice sopra riportato ruota robot creando un `Matrix` nel `GetWorldMatrix` metodo. Il `Matrix` struct contiene valori float a 16 che possono essere utilizzati per tradurre (imposta la posizione), ruotare e ridimensionare (dimensioni). Quando si assegna il `effect.World` proprietà, si definiranno sottostante per il rendering come posizione, dimensioni e orientamento indipendentemente dal sistema sono costituiti da un disegno (un `Model` o geometria da vertici). 

Fortunatamente, la `Matrix` struct include una serie di metodi che semplificano la creazione di tipi comuni di matrici. Il primo utilizzato nel codice precedente è `Matrix.CreateTranslation`. Il termine matematico *traduzione* fa riferimento a un'operazione che si traduce in un punto (o, in questo caso un modello) lo spostamento da una posizione a un'altra senza modifiche (ad esempio la rotazione o ridimensionamento). La funzione accetta un valore di X, Y e Z per la conversione. Se si visualizza la scena dall'alto in basso, il nostro `CreateTranslation` metodo (in modalità isolata) consente di eseguire le operazioni seguenti:

![](part3-images/image6.png "Il metodo CreateTranslation in isolamento esegue questa azione")

Seconda matrice che verrà creata è una matrice di rotazione usando la `CreateRotationZ` matrice. Questo è uno dei tre metodi che possono essere utilizzati per creare la rotazione:

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

Ogni metodo crea una matrice di rotazione la rotazione intorno a un asse specificato. In questo caso, ci stiamo rotazione intorno all'asse Z, che fa riferimento "backup". Di seguito consente di visualizzare basato su asse di rotazione works:

![](part3-images/image7.png "Ciò consente di visualizzare basato su asse di rotazione works")

Utilizziamo inoltre il `CreateRotationZ` metodo con il campo angolo, che aumenta nel tempo a causa di questo `Update` metodo chiamato. Il risultato è che il `CreateRotationZ` metodo fa sì che il nostro robot per orbita intorno all'origine col passare del tempo.

L'ultima riga del codice combina le due matrici in uno:

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

Questo processo è detto moltiplicazione, funziona in modo leggermente diverso rispetto a regolare moltiplicazione. Il *proprietà commutativa di moltiplicazione* indica che l'ordine dei numeri in un'operazione di moltiplicazione non modifica il risultato. 3 * 4 è equivalente a 4 * 3. La moltiplicazione è diverso in quanto non è commutativa. Nella riga precedente, ovvero può essere letto come "Applica il translationMatrix per spostare il modello, quindi ruotare tutto applicando il rotationMatrix". È possibile visualizzare il modo che nella riga precedente determina la posizione e la rotazione come indicato di seguito:

![](part3-images/image8.png "Una visualizzazione di cartelle pubbliche di modo che nella riga precedente determina la posizione e la rotazione")

Per comprendere l'ordine di moltiplicazione possibile impatto del risultato, considerare quanto segue, in cui viene invertita la moltiplicazione:

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

Il codice sopra riportato sarebbe innanzitutto ruota il modello sul posto, quindi convertirlo:

![](part3-images/image9.png "Il codice sopra riportato prima si ruota il modello sul posto, quindi viene convertito")

Se si esegue il codice con la moltiplicazione invertita, si noterà che, poiché la rotazione viene applicata prima di tutto, impatto solo l'orientamento del modello e la posizione del modello rimanga lo stesso. In altre parole, il modello ruota sul posto:

![](part3-images/image10.gif "Ruota il modello sul posto")

## <a name="creating-the-camera-entity"></a>Creazione dell'entità della fotocamera

Il `Camera` conterrà tutte le regole necessarie per eseguire lo spostamento basato su input e di specificare proprietà per l'assegnazione di proprietà in entità di `BasicEffect` classe.

Innanzitutto si verrà implementare una fotocamera statica (senza spostamento basato su input) e integrare il progetto esistente. Aggiungere una nuova classe per il **MonoGame3D** libreria di classi portabile (lo stesso progetto con `Robot.cs`) e denominarlo **fotocamera**. Sostituire tutti i contenuti del file con il codice seguente:

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Camera
    {
        // We need this to calculate the aspectRatio
        // in the ProjectionMatrix property.
        GraphicsDevice graphicsDevice;

        Vector3 position = new Vector3(15, 10, 10);

        public Matrix ViewMatrix
        {
            get
            {
                var lookAtVector = Vector3.Zero;
                var upVector = Vector3.UnitZ;

                return Matrix.CreateLookAt (
                    position, lookAtVector, upVector);
            }
        }

        public Matrix ProjectionMatrix
        {
            get
            {
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                float nearClipPlane = 1;
                float farClipPlane = 200;
                float aspectRatio = graphicsDevice.Viewport.Width / (float)graphicsDevice.Viewport.Height;

                return Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
            }
        }

        public Camera(GraphicsDevice graphicsDevice)
        {
            this.graphicsDevice = graphicsDevice;
        }

        public void Update(GameTime gameTime)
        {
            // We'll be doing some input-based movement here
        }
    }
}
```

Il codice precedente è molto simile al codice proveniente da `Game1` e `Robot` che consentono di assegnare le matrici in `BasicEffect`. 

Ora è possibile integrare il nuovo `Camera` classe nei nostri progetti esistenti. In primo luogo, verrà modificata la `Robot` classe per eseguire un `Camera` istanza nel relativo `Draw` metodo, che comporterà l'eliminazione di una grande quantità di codice duplicato. Sostituire il `Robot.Draw` metodo con le operazioni seguenti:

```csharp
public void Draw(Camera camera)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = GetWorldMatrix();
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;
        }

        mesh.Draw ();
    }
}
```

Successivamente, modificare il `Game1.cs` file:

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        // New camera code
        Camera camera;

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

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

            robot = new Robot ();
            robot.Initialize (Content);

            // New camera code
            camera = new Camera (graphics.GraphicsDevice);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            robot.Update (gameTime);
            // New camera code
            camera.Update (gameTime);
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            // New camera code
            robot.Draw (camera);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            // New camera code
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;

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
    }
}
```

Le modifiche per il `Game1` dalla versione precedente (che vengono identificati con `// New camera code` ) sono:

- `Camera` Campo `Game1`
- `Camera` creazione di un'istanza in `Game1.Initialize`
- `Camera.Update` chiamare in `Game1.Update`
- `Robot.Draw` ha ora un `Camera` parametro
- `Game1.Draw` Usa ora `Camera.ViewMatrix` e `Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>Spostare la fotocamera con Input

Finora è stato aggiunto un `Camera` entità ma non l'hai fatto nulla con la modifica del comportamento di runtime. Comportamento che consente all'utente verrà aggiunto a:

- Toccare il lato sinistro della schermata per disattivare la fotocamera verso sinistra
- Toccare il lato destro dello schermo per disattivare la fotocamera a destra
- Toccare il centro dello schermo per spostarsi in avanti la fotocamera

### <a name="making-lookat-relative"></a>Rendendo lookAt relativo

Innanzitutto verrà aggiornata la `Camera` classe per includere un `angle` campo che verrà usato per impostare la direzione che il `Camera` si sta verificando. Attualmente, il nostro `Camera` determina la direzione in cui sta verificando tramite locale `lookAtVector`, che viene assegnato a `Vector3.Zero`. In altre parole, il nostro `Camera` cerca sempre in corrispondenza dell'origine. Se si sposta la fotocamera, verrà inoltre modificare l'angolo di rotazione della fotocamera è rivolta a:

![](part3-images/image11.gif "Se si sposta la fotocamera, quindi l'angolo che si sta verificando la fotocamera verrà modificata anche")

Vogliamo il `Camera` per essere rivolto verso la stessa direzione indipendentemente dalla relativa posizione – almeno fino a quando non si implementano la logica per la rotazione il `Camera` utilizzando l'input. La prima modifica, è necessario regolare il `lookAtVector` variabile a partire da questo percorso corrente, anziché professionale in una posizione assoluta:

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    // Let's start at X = 0 so we're looking at things head-on
    Vector3 position = new Vector3(0, 20, 10);

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

Di conseguenza, il `Camera` visualizzando il mondo direttamente-on. Si noti che il primo `position` valore è stato modificato per `(0, 20, 10)` pertanto `Camera` è centrata sull'asse X. Esegue il gioco Visualizza:

![](part3-images/image12.png "Esecuzione del gioco Visualizza")

### <a name="creating-an-angle-variable"></a>Creazione di un angolo della variabile

Il `lookAtVector` variabile controlla l'angolo che sta visualizzando la fotocamera. Attualmente è fissa per visualizzare l'asse Y negativo verso il basso e leggermente inclinata verso il basso (dal `-.5f` valore Z). Si creerà un `angle` variabile che verrà utilizzato per regolare il `lookAtVector` proprietà. 

Nelle sezioni precedenti di questa procedura dettagliata illustrata matrici possono essere utilizzate per ruotare la modalità in cui vengono disegnati gli oggetti. Inoltre, è possibile utilizzare matrici per ruotare vettori, ad esempio il `lookAtVector` utilizzando il `Vector3.Transform` metodo. 

Aggiungere un `angle` campo e modificare il `ViewMatrix` proprietà come indicato di seguito:

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    Vector3 position = new Vector3(0, 20, 10);

    float angle;

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            // We'll create a rotation matrix using our angle
            var rotationMatrix = Matrix.CreateRotationZ (angle);
            // Then we'll modify the vector using this matrix:
            lookAtVector = Vector3.Transform (lookAtVector, rotationMatrix);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

### <a name="reading-input"></a>Lettura di input

Il nostro `Camera` entità può ora essere completamente controllato la posizione e le variabili angolo: è necessario modificarli in base all'input.

In primo luogo, si otterrà la `TouchPanel` allo stato per trovare in cui l'utente tocca lo schermo. Per ulteriori informazioni sull'utilizzo di `TouchPanel` classe, vedere [il riferimento all'API TouchPanel](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel).

Se l'utente tocca sul terzo a sinistra, è possibile modificare il `angle` valore pertanto `Camera` Ruota a sinistra, e se l'utente tocca sul terzo destra, si sarà Ruota in senso. Se l'utente tocca nel terzo al centro dello schermo, quindi si sposterà il `Camera` in avanti.

In primo luogo, aggiungere un tramite l'istruzione per qualificare il `TouchPanel` e `TouchCollection` classi `Camera.cs`:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

Successivamente, modificare il `Update` metodo per leggere il pannello di tocco e regolare la `angle` e `position` variabili in modo appropriato:

```csharp
public void Update(GameTime gameTime)
{
    TouchCollection touchCollection = TouchPanel.GetState();

    bool isTouchingScreen = touchCollection.Count > 0;
    if (isTouchingScreen)
    {
        var xPosition = touchCollection [0].Position.X;

        float xRatio = xPosition / (float)graphicsDevice.Viewport.Width;

        if (xRatio < 1 / 3.0f)
        {
            angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
        else if (xRatio < 2 / 3.0f)
        {
            var forwardVector = new Vector3 (0, -1, 0);

            var rotationMatrix = Matrix.CreateRotationZ (angle);
            forwardVector = Vector3.Transform (forwardVector, rotationMatrix);

            const float unitsPerSecond = 3;

            this.position += forwardVector * unitsPerSecond *
                (float)gameTime.ElapsedGameTime.TotalSeconds ;
        }
        else
        {
            angle -= (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
    }
}
```

A questo punto il `Camera` risponderà all'input tocco:

![](part3-images/image1.gif "Ora la fotocamera verrà risponde all'input tocco")

Il metodo di aggiornamento inizia chiamando `TouchPanel.GetState`, che restituisce una raccolta di elementi. Sebbene `TouchPanel.GetState` può restituire più punti di tocco, sarà solo preoccupazione il primo per motivi di semplicità.

Se l'utente tocca lo schermo, il codice controlla per verificare se il primo tocco sinistro, centrale o destro terzo dello schermo. I terzi sinistro e destro rotazione della fotocamera aumentando o diminuendo il `angle` variabile in base al `TotalSeconds` valore (in modo che il gioco è lo stesso indipendentemente dalla frequenza dei fotogrammi).

Se l'utente tocca terzo al centro dello schermo, quindi la fotocamera verrà spostare in avanti. Questa operazione viene eseguita prima ottenendo il vettore di inoltro, che viene definito come rivolta verso l'asse Y negativo inizialmente, quindi ruotato di una matrice creata utilizzando `Matrix.CreateRotationZ` e `angle` valore. Infine il `forwardVector` viene applicato a `position` utilizzando il `unitsPerSecond` coefficiente.

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata illustra come spostare e ruotare `Models` in 3D spazio utilizzando `Matrices` e `BasicEffect.World` proprietà. Questo modulo di spostamento costituisce la base per lo spostamento di oggetti nei giochi 3D. Questa procedura dettagliata illustra inoltre come implementare un `Camera` entità per la visualizzazione del mondo da qualsiasi posizione e l'angolo.

## <a name="related-links"></a>Collegamenti correlati

- [Collegamento di API MonoGame](http://www.monogame.net/documentation/?page=api)
- [Progetto finito (esempio)](https://developer.xamarin.com/samples/monodroid/MonoGame3DCamera/)
