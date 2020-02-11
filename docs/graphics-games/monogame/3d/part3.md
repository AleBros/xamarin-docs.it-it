---
title: Coordinate 3D in monogame
description: Conoscere il sistema di coordinate 3D è un passaggio importante per lo sviluppo di giochi 3D. Monogame fornisce varie classi per il posizionamento, l'orientamento e il ridimensionamento di oggetti nello spazio 3D.
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: a38f4b81f684d6d416e6abe017bc463e3097c6b1
ms.sourcegitcommit: 93697a20e6fc7da547a8714ac109d7953b61d63f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72980861"
---
# <a name="3d-coordinates-in-monogame"></a>Coordinate 3D in monogame

_Conoscere il sistema di coordinate 3D è un passaggio importante per lo sviluppo di giochi 3D. Monogame fornisce varie classi per il posizionamento, l'orientamento e il ridimensionamento di oggetti nello spazio 3D._

Per lo sviluppo di giochi 3D è necessario comprendere come modificare gli oggetti in un sistema di coordinate 3D. In questa procedura dettagliata verrà illustrato come modificare gli oggetti visivi (in particolare un modello). Verranno illustrati i concetti relativi al controllo di un modello per la creazione di una classe di fotocamera 3D.

I concetti presentati derivano dall'algebra lineare, ma verrà adottato un approccio pratico, in modo che qualsiasi utente senza uno sfondo matematico forte possa applicare questi concetti nei propri giochi.

Verranno trattati gli argomenti seguenti:

- Creazione di un progetto
- Creazione di un'entità robot
- Trasferimento dell'entità robot
- Moltiplicazione di matrici
- Creazione dell'entità della fotocamera
- Trasferimento della fotocamera con input

Al termine, si disporrà di un progetto con un robot che si sposta in un cerchio e una fotocamera che può essere controllata dall'input tocco:

![](part3-images/image1.gif "Once finished, the app will include a project with a robot moving in a circle and a camera which can be controlled by touch input")

## <a name="creating-a-project"></a>Creazione di un progetto

Questa procedura dettagliata è incentrata sullo spostare gli oggetti nello spazio 3D. Si inizierà con il progetto per il rendering di modelli e matrici di vertici [disponibili qui](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelsandvertsmg/). Una volta scaricato, decomprimere e aprire il progetto per assicurarsi che venga eseguito e dovrebbe essere visualizzato quanto segue:

![](part3-images/image2.png "Once downloaded, unzip and open the project to make sure it runs and this view should be displayed")

## <a name="creating-a-robot-entity"></a>Creazione di un'entità robot

Prima di iniziare a spostare il robot, viene creata una classe `Robot` per contenere la logica per il disegno e lo spostamento. Gli sviluppatori di giochi fanno riferimento a questo incapsulamento della logica e dei dati come *entità*.

Aggiungere un nuovo file di classe vuoto alla libreria di classi portabile **MonoGame3D** , non a ModelAndVerts. Android specifico della piattaforma. Denominare it **robot** e fare clic su **nuovo**:

![](part3-images/image3.png "Name it Robot and click New")

Modificare la classe `Robot` come indicato di seguito:

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace MonoGame3D
{
    public class Robot
    {
        Model model;

        public void Initialize(ContentManager contentManager)
        {
            model = contentManager.Load<Model> ("robot");

        }

        // For now we'll take these values in, eventually we'll
        // take a Camera object
        public void Draw(Vector3 cameraPosition, float aspectRatio)
        {
            foreach (var mesh in model.Meshes)
            {
                foreach (BasicEffect effect in mesh.Effects)
                {
                    effect.EnableDefaultLighting ();
                    effect.PreferPerPixelLighting = true;

                    effect.World = Matrix.Identity; 
                    var cameraLookAtVector = Vector3.Zero;
                    var cameraUpVector = Vector3.UnitZ;

                    effect.View = Matrix.CreateLookAt (
                        cameraPosition, cameraLookAtVector, cameraUpVector);

                    float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                    float nearClipPlane = 1;
                    float farClipPlane = 200;

                    effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                        fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

                }

                // Now that we've assigned our properties on the effects we can
                // draw the entire mesh
                mesh.Draw ();
            }
        }
    }
}
```

Il codice `Robot` è essenzialmente lo stesso codice `Game1` per il disegno di un `Model`. Per una revisione del caricamento e del disegno `Model`, vedere [questa guida sull'utilizzo dei modelli](~/graphics-games/monogame/3d/part1.md). È ora possibile rimuovere tutti i `Model` il codice di caricamento e rendering da `Game1`e sostituirlo con un'istanza di `Robot`:

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        Vector3 cameraPosition = new Vector3(15, 10, 10);

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

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

            robot = new Robot ();
            robot.Initialize (Content);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            float aspectRatio = 
                graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
            robot.Draw (cameraPosition, aspectRatio);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
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
    }
}
```

Se eseguiamo il codice ora, sarà presente una scena con un solo robot che viene disegnato principalmente sotto il piano:

![](part3-images/image4.png "If the code is run now, the app will display a scene with only one robot which is drawn mostly under the floor")

## <a name="moving-the-robot"></a>Trasferimento del robot

Ora che abbiamo una classe `Robot`, possiamo aggiungere la logica di spostamento al robot. In questo caso, il robot verrà semplicemente spostato in un cerchio in base al tempo di gioco. Si tratta di un'implementazione poco pratica per un gioco reale poiché un carattere può in genere rispondere all'intelligenza artificiale o di input, ma fornisce un ambiente per esplorare il posizionamento e la rotazione 3D.

Le uniche informazioni necessarie dall'esterno della classe `Robot` sono il tempo di gioco corrente. Verrà aggiunto un `Update` metodo che accetta un parametro di `GameTime`. Questo parametro `GameTime` verrà usato per incrementare una variabile angolo che verrà usata per determinare la posizione finale per il robot.

In primo luogo, verrà aggiunto il campo Angle alla classe `Robot` nel campo `model`:

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 A questo punto è possibile incrementare questo valore in una funzione `Update`:

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

È necessario assicurarsi che il metodo `Update` venga chiamato da `Game1.Update`:

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

Naturalmente, a questo punto il campo angolo non esegue alcuna operazione. è necessario scrivere codice per usarlo. Il metodo `Draw` verrà modificato in modo da poter calcolare il `Matrix` globale in un metodo dedicato: 

```csharp
public void Draw(Vector3 cameraPosition, float aspectRatio)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            // We’ll be doing our calculations here...
            effect.World = GetWorldMatrix();

            var cameraLookAtVector = Vector3.Zero;
            var cameraUpVector = Vector3.UnitZ;

            effect.View = Matrix.CreateLookAt (
                cameraPosition, cameraLookAtVector, cameraUpVector);

            float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
            float nearClipPlane = 1;
            float farClipPlane = 200;

            effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
        }

        mesh.Draw ();
    }
}
```

Verrà quindi implementato il metodo `GetWorldMatrix` nella classe `Robot`:

```csharp
Matrix GetWorldMatrix()
{
    const float circleRadius = 8;
    const float heightOffGround = 3;

    // this matrix moves the model "out" from the origin
    Matrix translationMatrix = Matrix.CreateTranslation (
        circleRadius, 0, heightOffGround);

    // this matrix rotates everything around the origin
    Matrix rotationMatrix = Matrix.CreateRotationZ (angle);

    // We combine the two to have the model move in a circle:
    Matrix combined = translationMatrix * rotationMatrix;

    return combined;
}
```

Il risultato dell'esecuzione di questo codice comporta che il robot si sposti in un cerchio:

![](part3-images/image5.gif "Running this code results in the robot moving in a circle")

## <a name="matrix-multiplication"></a>Moltiplicazione di matrici

Il codice precedente ruota il robot creando una `Matrix` nel metodo `GetWorldMatrix`. Lo struct `Matrix` contiene 16 valori float che possono essere usati per tradurre (impostare la posizione), ruotare e ridimensionare (set size). Quando si assegna la proprietà `effect.World`, viene indicato al sistema di rendering sottostante come posizionare, ridimensionare e orientare qualsiasi cosa sia il disegno (una `Model` o una geometria dai vertici). 

Fortunatamente, lo struct `Matrix` include un numero di metodi che semplificano la creazione di tipi comuni di matrici. Il primo usato nel codice precedente è `Matrix.CreateTranslation`. La *conversione* del termine matematico si riferisce a un'operazione che restituisce un punto (o, in questo caso, un modello) che passa da una posizione a un'altra senza alcuna modifica, ad esempio la rotazione o il ridimensionamento. La funzione accetta un valore X, Y e Z per la conversione. Se si visualizza la scena dall'alto verso il basso, il metodo `CreateTranslation` (in isolamento) esegue le operazioni seguenti:

![](part3-images/image6.png "The CreateTranslation method in isolation performs this action")

La seconda matrice creata è una matrice di rotazione usando la matrice `CreateRotationZ`. Si tratta di uno dei tre metodi che possono essere usati per creare la rotazione:

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

Ogni metodo crea una matrice di rotazione ruotando circa un asse specificato. In questo caso, si sta ruotando sull'asse Z, che punta a "up". Il codice seguente consente di visualizzare il funzionamento della rotazione basata su Axis:

![](part3-images/image7.png "This can help visualize how axis-based rotation works")

Viene anche usato il metodo `CreateRotationZ` con il campo Angle, che aumenta nel tempo a causa della chiamata del metodo `Update`. Il risultato è che il metodo `CreateRotationZ` fa in modo che il robot si orbiti intorno all'origine nel momento in cui passa il tempo.

L'ultima riga di codice combina le due matrici in una sola:

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

Questa operazione viene definita moltiplicazione di matrici, che funziona in modo leggermente diverso dalla moltiplicazione regolare. La *proprietà commutativa degli Stati di moltiplicazione* indica che l'ordine dei numeri in un'operazione di moltiplicazione non comporta la modifica del risultato. Ovvero 3 \* 4 equivale a 4 \* 3. La moltiplicazione della matrice è diversa perché non è commutativa. Ciò significa che la riga precedente può essere letta come "Apply the translationMatrix to Move the Model, then Rotating all applying the rotationMatrix". È possibile visualizzare il modo in cui la riga precedente influisca sulla posizione e sulla rotazione come indicato di seguito:

![](part3-images/image8.png "A visualization pf the way that the above line affects the position and rotation")

Per comprendere in che modo l'ordine di moltiplicazione della matrice può influisca sul risultato, tenere presente quanto segue, in cui la moltiplicazione di matrici viene invertita:

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

Il codice precedente ruoterà prima di tutto il modello sul posto, quindi lo tradurrà:

![](part3-images/image9.png "The code above would first rotate the model in-place, then translate it")

Se si esegue il codice con la moltiplicazione invertita, si noterà che, dal momento che la rotazione si applica per prima, influisce solo sull'orientamento del modello e la posizione del modello rimane invariata. In altre parole, il modello ruota sul posto:

![](part3-images/image10.gif "The model rotates in place")

## <a name="creating-the-camera-entity"></a>Creazione dell'entità della fotocamera

L'entità `Camera` conterrà tutta la logica necessaria per eseguire lo spostamento basato su input e fornire proprietà per l'assegnazione delle proprietà nella classe `BasicEffect`.

Prima di tutto verrà implementata una fotocamera statica (senza spostamento basato su input) che verrà integrata nel progetto esistente. Aggiungere una nuova classe alla libreria di classi portabile **MonoGame3D** (lo stesso progetto con `Robot.cs`) e denominarla **camera**. Sostituire tutti i contenuti del file con il codice seguente:

```csharp
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Camera
    {
        // We need this to calculate the aspectRatio
        // in the ProjectionMatrix property.
        GraphicsDevice graphicsDevice;

        Vector3 position = new Vector3(15, 10, 10);

        public Matrix ViewMatrix
        {
            get
            {
                var lookAtVector = Vector3.Zero;
                var upVector = Vector3.UnitZ;

                return Matrix.CreateLookAt (
                    position, lookAtVector, upVector);
            }
        }

        public Matrix ProjectionMatrix
        {
            get
            {
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                float nearClipPlane = 1;
                float farClipPlane = 200;
                float aspectRatio = graphicsDevice.Viewport.Width / (float)graphicsDevice.Viewport.Height;

                return Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);
            }
        }

        public Camera(GraphicsDevice graphicsDevice)
        {
            this.graphicsDevice = graphicsDevice;
        }

        public void Update(GameTime gameTime)
        {
            // We'll be doing some input-based movement here
        }
    }
}
```

Il codice precedente è molto simile al codice di `Game1` e `Robot` che assegnano le matrici in `BasicEffect`. 

A questo punto è possibile integrare la nuova classe `Camera` nei progetti esistenti. In primo luogo, si modificherà la classe `Robot` per eseguire un'istanza di `Camera` nel metodo `Draw`, in modo da eliminare una grande quantità di codice duplicato. Sostituire il metodo `Robot.Draw` con quanto segue:

```csharp
public void Draw(Camera camera)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;

            effect.World = GetWorldMatrix();
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;
        }

        mesh.Draw ();
    }
}
```

Modificare quindi il file di `Game1.cs`:

```csharp
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace MonoGame3D
{
    public class Game1 : Game
    {
        GraphicsDeviceManager graphics;

        VertexPositionNormalTexture[] floorVerts;

        BasicEffect effect;

        Texture2D checkerboardTexture;

        // New camera code
        Camera camera;

        Robot robot;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            graphics.IsFullScreen = true;

            Content.RootDirectory = "Content";
        }

        protected override void Initialize ()
        {
            floorVerts = new VertexPositionNormalTexture[6];

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

            robot = new Robot ();
            robot.Initialize (Content);

            // New camera code
            camera = new Camera (graphics.GraphicsDevice);

            base.Initialize ();
        }

        protected override void LoadContent()
        {
            using (var stream = TitleContainer.OpenStream ("Content/checkerboard.png"))
            {
                checkerboardTexture = Texture2D.FromStream (this.GraphicsDevice, stream);
            }
        }

        protected override void Update(GameTime gameTime)
        {
            robot.Update (gameTime);
            // New camera code
            camera.Update (gameTime);
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);

            DrawGround ();

            // New camera code
            robot.Draw (camera);

            base.Draw(gameTime);
        }

        void DrawGround()
        {
            // New camera code
            effect.View = camera.ViewMatrix;
            effect.Projection = camera.ProjectionMatrix;

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
    }
}
```

Le modifiche apportate al `Game1` dalla versione precedente (identificate con `// New camera code`) sono:

- campo `Camera` in `Game1`
- creazione di un'istanza di `Camera` in `Game1.Initialize`
- chiamata `Camera.Update` in `Game1.Update`
- `Robot.Draw` ora accetta un parametro di `Camera`
- `Game1.Draw` USA ora `Camera.ViewMatrix` e `Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>Trasferimento della fotocamera con input

Finora è stata aggiunta un'entità `Camera` ma non è stata eseguita alcuna operazione per modificare il comportamento in fase di esecuzione. Si aggiungerà un comportamento che consente all'utente di:

- Toccare il lato sinistro dello schermo per ruotare la fotocamera verso sinistra.
- Toccare il lato destro dello schermo per trasformare la fotocamera a destra
- Toccare il centro dello schermo per spostare la fotocamera avanti

### <a name="making-lookat-relative"></a>Creazione di un'occhiata relativa

Prima di tutto verrà aggiornata la classe `Camera` per includere un campo di `angle` che verrà usato per impostare la direzione della `Camera`. Attualmente, il `Camera` determina la direzione con cui si trova attraverso la `lookAtVector`locale, assegnata al `Vector3.Zero`. In altre parole, il `Camera` analizza sempre l'origine. Se la fotocamera viene spostata, cambierà anche l'angolo che la fotocamera deve affrontare:

![](part3-images/image11.gif "If the Camera moves, then the angle that the camera is facing will also change")

Si vuole che il `Camera` si trovino nella stessa direzione, indipendentemente dalla relativa posizione, almeno fino a quando non si implementa la logica per la rotazione della `Camera` tramite input. La prima modifica consiste nel modificare la variabile `lookAtVector` in modo che sia basata sulla posizione corrente, invece di esaminare una posizione assoluta:

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    // Let's start at X = 0 so we're looking at things head-on
    Vector3 position = new Vector3(0, 20, 10);

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

In questo modo si ottiene la `Camera` che Visualizza il mondo in modo diretto. Si noti che il valore di `position` iniziale è stato modificato in modo da `(0, 20, 10)` quindi la `Camera` è centrata sull'asse X. L'esecuzione del gioco Visualizza:

![](part3-images/image12.png "Running the game displays this view")

### <a name="creating-an-angle-variable"></a>Creazione di una variabile Angle

La variabile `lookAtVector` controlla l'angolo visualizzato dalla fotocamera. Attualmente è corretto visualizzare l'asse Y negativo e inclinato leggermente verso il basso (dal valore `-.5f` Z). Verrà creata una variabile `angle` che verrà usata per modificare la proprietà `lookAtVector`. 

Nelle sezioni precedenti di questa procedura dettagliata è stato illustrato che è possibile usare matrici per ruotare la modalità di disegno degli oggetti. È anche possibile usare le matrici per ruotare i vettori come il `lookAtVector` usando il metodo `Vector3.Transform`. 

Aggiungere un campo `angle` e modificare la proprietà `ViewMatrix` come indicato di seguito:

```csharp
public class Camera
{
    GraphicsDevice graphicsDevice;

    Vector3 position = new Vector3(0, 20, 10);

    float angle;

    public Matrix ViewMatrix
    {
        get
        {
            var lookAtVector = new Vector3 (0, -1, -.5f);
            // We'll create a rotation matrix using our angle
            var rotationMatrix = Matrix.CreateRotationZ (angle);
            // Then we'll modify the vector using this matrix:
            lookAtVector = Vector3.Transform (lookAtVector, rotationMatrix);
            lookAtVector += position;

            var upVector = Vector3.UnitZ;

            return  Matrix.CreateLookAt (
                position, lookAtVector, upVector);
        }
    }
    ...
```

### <a name="reading-input"></a>Lettura dell'input

È ora possibile controllare completamente l'entità `Camera` tramite la relativa posizione e le variabili angolo, ma è sufficiente modificarle in base all'input.

Prima di tutto, verrà visualizzato lo stato `TouchPanel` per individuare il punto in cui l'utente tocca lo schermo. Per altre informazioni sull'uso della classe `TouchPanel`, vedere [le informazioni di riferimento sull'API touchpanel](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel).

Se l'utente sta toccando il terzo a sinistra, si modificherà il valore di `angle` in modo che la `Camera` ruoti verso sinistra e se l'utente tocca il terzo a destra, verrà ruotato in altro modo. Se l'utente sta toccando il terzo terzo dello schermo, la `Camera` verrà spostata in avanti.

Per prima cosa, aggiungere un'istruzione using per qualificare le classi `TouchPanel` e `TouchCollection` in `Camera.cs`:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

Modificare quindi il metodo `Update` per leggere il pannello touch e regolare le variabili `angle` e `position` in modo appropriato:

```csharp
public void Update(GameTime gameTime)
{
    TouchCollection touchCollection = TouchPanel.GetState();

    bool isTouchingScreen = touchCollection.Count > 0;
    if (isTouchingScreen)
    {
        var xPosition = touchCollection [0].Position.X;

        float xRatio = xPosition / (float)graphicsDevice.Viewport.Width;

        if (xRatio < 1 / 3.0f)
        {
            angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
        else if (xRatio < 2 / 3.0f)
        {
            var forwardVector = new Vector3 (0, -1, 0);

            var rotationMatrix = Matrix.CreateRotationZ (angle);
            forwardVector = Vector3.Transform (forwardVector, rotationMatrix);

            const float unitsPerSecond = 3;

            this.position += forwardVector * unitsPerSecond *
                (float)gameTime.ElapsedGameTime.TotalSeconds ;
        }
        else
        {
            angle -= (float)gameTime.ElapsedGameTime.TotalSeconds;
        }
    }
}
```

A questo punto il `Camera` risponderà all'input tocco:

![](part3-images/image1.gif "Now the Camera will respond to touch input")

Il metodo Update inizia chiamando `TouchPanel.GetState`, che restituisce una raccolta di tocchi. Sebbene `TouchPanel.GetState` possa restituire più punti di tocco, il primo è preoccupato per semplicità.

Se l'utente tocca lo schermo, il codice verifica se il primo tocco è a sinistra, a metà o a destra del terzo dello schermo. I terzi sinistro e destro ruotano la fotocamera aumentando o diminuendo la variabile `angle` in base al valore `TotalSeconds` (in modo che il gioco si comporterebbe lo stesso indipendentemente dalla frequenza dei fotogrammi).

Se l'utente sta toccando il centro del terzo schermo, la fotocamera verrà spostata in avanti. Questa operazione viene eseguita prima ottenendo il vettore di avanzamento, che inizialmente viene definito come puntamento verso l'asse Y negativo, quindi ruotato da una matrice creata utilizzando `Matrix.CreateRotationZ` e il valore di `angle`. Infine, il `forwardVector` viene applicato ai `position` utilizzando il coefficiente `unitsPerSecond`.

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata illustra come spostare e ruotare `Models` nello spazio 3D utilizzando `Matrices` e la proprietà `BasicEffect.World`. Questa forma di spostamento costituisce la base per lo spostamento di oggetti in giochi 3D. Questa procedura dettagliata illustra anche come implementare un'entità `Camera` per la visualizzazione del mondo da qualsiasi posizione e angolo.

## <a name="related-links"></a>Collegamenti correlati

- [Collegamento all'API monogame](http://www.monogame.net/documentation/?page=api)
- [Progetto terminato (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/monogame3dcamera)
