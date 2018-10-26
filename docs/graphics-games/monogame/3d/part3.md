---
title: Coordinate 3D in MonoGame
description: Comprendere il sistema di coordinate 3D è un passaggio importante nello sviluppo di giochi 3D. MonoGame fornisce una serie di classi per il posizionamento, orientamento e ridimensionamento di oggetti nello spazio 3D.
ms.prod: xamarin
ms.assetid: A4130995-48FD-4E2E-9C2B-ADCEFF35BE3A
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: dc21228f1daa74a90ff8f0ea346bc01b109f0987
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107641"
---
# <a name="3d-coordinates-in-monogame"></a>Coordinate 3D in MonoGame

_Comprendere il sistema di coordinate 3D è un passaggio importante nello sviluppo di giochi 3D. MonoGame fornisce una serie di classi per il posizionamento, orientamento e ridimensionamento di oggetti nello spazio 3D._

Sviluppo di giochi 3D richiede la comprensione di come modificare oggetti in un sistema di coordinate 3D. Questa procedura dettagliata illustra le procedure modificare gli oggetti visivi (in particolare un modello). Verrà compilata sui concetti di controllo di un modello per creare una classe della fotocamera 3D.

I concetti presentati derivano dalla algebra lineare, ma è possibile adottare un approccio pratico in modo che tutti gli utenti senza uno sfondo matematiche complesse è possono applicare questi concetti, i propri giochi.

Si parlerà gli argomenti seguenti:

- Creazione di un progetto
- Creazione di un'entità Robot
- Lo spostamento dell'entità Robot
- Moltiplicazione di matrici
- Creare l'entità della fotocamera
- Spostamento della videocamera con Input

Una volta terminato, avremo un progetto con un robot lo spostamento in un cerchio e fotocamera che può essere controllata tramite l'input tocco:

![](part3-images/image1.gif "Al termine, l'app includerà un progetto con un robot lo spostamento in un cerchio e fotocamera che può essere controllata tramite l'input tocco")


## <a name="creating-a-project"></a>Creazione di un progetto

Questa procedura dettagliata si concentra sullo spostamento di oggetti nello spazio 3D. Inizieremo con il progetto per il rendering di modelli e le matrici di vertice [cui è disponibile qui](https://developer.xamarin.com/samples/mobile/ModelsAndVertsMG/). Al termine del download, decomprimere e aprire il progetto per assicurarsi che è in esecuzione e dovremmo vedere quanto segue:

![](part3-images/image2.png "Al termine del download, decomprimere e aprire il progetto per assicurarsi che è in esecuzione e deve essere visualizzata in questa vista")


## <a name="creating-a-robot-entity"></a>Creazione di un'entità Robot

Prima di iniziare lo spostamento nostri robot intorno a, si creerà un `Robot` classe per contenere la logica di disegno e dello spostamento. Fare riferimento agli sviluppatori di giochi a incapsulare questa logica e i dati come un *entità*.

Aggiungere un nuovo file di classe vuota per il **MonoGame3D** libreria di classi portabile (non il ModelAndVerts.Android specifiche della piattaforma). Denominarlo **Robot** e fare clic su **New**:

![](part3-images/image3.png "Denominarla Robot e fare clic su nuovo")

Modificare il `Robot` classe come indicato di seguito:

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

Il `Robot` codice è essenzialmente lo stesso codice nei `Game1` per il disegno un `Model`. Per informazioni generali sul `Model` il caricamento e di disegno, vedere [questa Guida sull'uso dei modelli](~/graphics-games/monogame/3d/part1.md). A questo punto, possiamo rimuovere tutti i `Model` il caricamento e il rendering di codice da `Game1`e sostituirlo con un `Robot` istanza:

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

Se eseguiamo il codice a questo punto si avrà una scena con solo un robot che viene disegnato principalmente sotto il tetto minimo:

![](part3-images/image4.png "Se il codice viene eseguito a questo punto, l'app visualizzerà una scena con solo un robot che viene disegnato principalmente sotto il limite minimo")

## <a name="moving-the-robot"></a>Spostare il Robot

Ora che abbiamo un `Robot` (classe), è possibile aggiungere la logica di spostamento per il robot. In questo caso, verrà semplicemente rendiamo il robot spostare in un cerchio in base al valore di gioco. Si tratta di un'implementazione in qualche modo pratica per un gioco reale perché un carattere può in genere rispondere all'input o di intelligenza artificiale, ma offre un ambiente a Microsoft per esplorare il posizionamento 3D e la rotazione.

Le uniche informazioni sarà necessario all'esterno del `Robot` classe è l'ora corrente del gioco. Si aggiungerà un' `Update` metodo che consentirà di passare un `GameTime` parametro. Ciò `GameTime` parametro verrà utilizzato per incrementare la variabile di un angolo che verranno utilizzate per determinare la posizione finale per il robot.

In primo luogo, si aggiungerà il campo angolo per la `Robot` classe sotto il `model` campo:

```csharp
public class Robot
{
    public Model model;

    // new code:
    float angle;
    ...
```

 A questo punto è possibile incrementare questo valore in un `Update` funzione:

```csharp
public void Update(GameTime gameTime)
{
    // TotalSeconds is a double so we need to cast to float
    angle += (float)gameTime.ElapsedGameTime.TotalSeconds;
}
```

È necessario assicurarsi che il `Update` metodo viene chiamato da `Game1.Update`:

```csharp
protected override void Update(GameTime gameTime)
{
    robot.Update (gameTime);
    base.Update(gameTime);
}
```

Naturalmente, a questo punto il campo angolo non esegue alcuna operazione, è necessario scrivere codice per usarla. Questo punto, modificheremo il `Draw` metodo in modo che è possibile calcolare il mondo `Matrix` in un metodo dedicato: 

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

Successivamente, viene implementato il `GetWorldMatrix` nel metodo il `Robot` classe:

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

Il risultato dell'esecuzione di questo codice produce il robot lo spostamento in un cerchio:

![](part3-images/image5.gif "L'esecuzione genera questo codice il robot lo spostamento in un cerchio")

## <a name="matrix-multiplication"></a>Moltiplicazione di matrici

Il codice riportato sopra consente di ruotare il robot tramite la creazione di un `Matrix` nella `GetWorldMatrix` (metodo). Il `Matrix` struct contiene 16 valori float che possono essere utilizzati per tradurre (imposta la posizione), ruotare e ridimensionare (dimensioni). Quando si assegna il `effect.World` proprietà, si definiranno sottostante per il rendering come posizionare e ridimensionare orientarsi meglio all'interno di qualsiasi sistema si sono costituiti da disegno (un `Model` o una geometria dai vertici). 

Per fortuna, il `Matrix` struct include una serie di metodi che semplificano la creazione di tipi comuni di matrici. Il primo utilizzato nel codice precedente è `Matrix.CreateTranslation`. Il termine matematico *traduzione* un'operazione che comporta un punto (o in questo caso un modello) si intende lo spostamento da una posizione a un'altra senza alcuna altra modifica (ad esempio, la rotazione o il ridimensionamento). La funzione accetta un valore di X, Y e Z per la conversione. Se si visualizza la scena dall'alto in basso, nostro `CreateTranslation` (metodo) (in modalità isolata) esegue le operazioni seguenti:

![](part3-images/image6.png "Il metodo CreateTranslation in isolamento esegue questa azione")

Seconda matrice che verrà creata è una matrice di rotazione usando la `CreateRotationZ` matrice. Questo è uno dei tre metodi che possono essere utilizzati per creare la rotazione:

- `CreateRotationX`
- `CreateRoationY`
- `CreateRotationZ`

Ogni metodo crea una matrice di rotazione mediante la rotazione intorno a un asse specificato. In questo caso, si sta rotazione intorno all'asse Z, che fa riferimento "up". Di seguito può semplificare la visualizzazione come basata su asse di rotazione works:

![](part3-images/image7.png "Ciò consente di visualizzare la modalità basata su asse di rotazione works")

Utilizziamo inoltre il `CreateRotationZ` metodo con il campo di angolo, che viene incrementato nel corso del tempo a causa dell'errore nostro `Update` metodo chiamato. Il risultato è che il `CreateRotationZ` metodo fa in modo che i robot per orbita intorno all'origine col passare del tempo.

L'ultima riga del codice consente di combinare le due matrici in uno:

```csharp
Matrix combined = translationMatrix * rotationMatrix;
```

Ciò è detto moltiplicazione di matrici, che funziona in modo leggermente diverso rispetto a moltiplicazione regolare. Il *proprietà commutativa della moltiplicazione* indica che l'ordine dei numeri in un'operazione di moltiplicazione non modifica il risultato. 3 * 4 è equivalente a 4 * 3. Moltiplicazione di matrici è diverso in quanto non è commutativa. Vale a dire, la riga precedente può essere letto come "Applicare la translationMatrix per spostare il modello, quindi tutto ruota applicando il rotationMatrix". È stato possibile visualizzare il modo che la riga precedente riguarda la posizione e la rotazione come indicato di seguito:

![](part3-images/image8.png "Una visualizzazione pf di modo che la riga precedente riguarda la posizione e la rotazione")

Per comprendere l'ordine della moltiplicazione di matrici possibile impatto del risultato, tenere presente quanto segue, dove viene invertita la moltiplicazione di matrici:

```csharp
Matrix combined = rotationMatrix * translationMatrix;
```

Il codice sopra riportato sarebbe innanzitutto ruotare il modello sul posto, quindi convertirlo:

![](part3-images/image9.png "Il codice precedente prima di tutto sarebbe ruotare il modello sul posto, quindi convertirlo")

Se eseguiamo il codice con la moltiplicazione invertita, possiamo notare che, poiché la rotazione viene applicata per prima, interessa solo l'orientamento del modello e la posizione del modello rimanga lo stesso. In altre parole, il modello ruota posto:

![](part3-images/image10.gif "Il modello ruota posto")

## <a name="creating-the-camera-entity"></a>Creare l'entità della fotocamera

Il `Camera` entità conterrà tutta la logica necessaria per eseguire lo spostamento basato su input e per fornire le proprietà per l'assegnazione di proprietà nel `BasicEffect` classe.

Verrà prima di tutto implementare una fotocamera statica (non basato su input dello spostamento) e li integrano in nostro progetto esistente. Aggiungere una nuova classe per il **MonoGame3D** libreria di classi portabile (il progetto con lo stesso `Robot.cs`) e denominarlo **fotocamera**. Sostituire tutti i contenuti del file con il codice seguente:

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

Il codice precedente è molto simile al codice dal `Game1` e `Robot` che consentono di assegnare le matrici in `BasicEffect`. 

A questo punto è possibile integrare il nuovo `Camera` classe nei nostri progetti esistenti. Innanzitutto, Modifichiamo il `Robot` classe per eseguire un `Camera` dell'istanza nel relativo `Draw` metodo, che comporterà l'eliminazione di una grande quantità di codice duplicato. Sostituire il `Robot.Draw` metodo con il codice seguente:

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

A questo punto, modificare il `Game1.cs` file:

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

Le modifiche per il `Game1` dalla versione precedente (che vengono identificati con `// New camera code` ) sono:

- `Camera` Campo in `Game1`
- `Camera` creazione di un'istanza di `Game1.Initialize`
- `Camera.Update` chiamare in `Game1.Update`
- `Robot.Draw` accetta ora un `Camera` parametro
- `Game1.Draw` Usa ora `Camera.ViewMatrix` e `Camera.ProjectionMatrix`

## <a name="moving-the-camera-with-input"></a>Spostamento della videocamera con Input

Finora è stato aggiunto un `Camera` entità ma non sono state eseguite operazioni con la modifica del comportamento di runtime. Verrà aggiunto il comportamento che consente all'utente a:

- Toccare il lato sinistro della schermata per abilitare la fotocamera verso sinistra
- Toccare il lato destro della schermata per abilitare la fotocamera verso destra
- Toccare il centro della schermata per proseguire la fotocamera

### <a name="making-lookat-relative"></a>Rendendo sguardo relativo

Prima di tutto verrà aggiornato il `Camera` classe per includere un `angle` campo che verrà usato per impostare la direzione che il `Camera` nel sistema. Attualmente, nostro `Camera` determina la direzione sia rivolto verso tramite locale `lookAtVector`, che viene assegnato a `Vector3.Zero`. In altre parole, nostro `Camera` cerca sempre in corrispondenza dell'origine. Se si sposta la fotocamera, quindi l'angolo di rotazione della fotocamera verso modificherà anche:

![](part3-images/image11.gif "Se si sposta la fotocamera, quindi l'angolo di rotazione è rivolta verso la fotocamera verrà modificato anche")

Vogliamo la `Camera` a essere affiancate almeno la stessa direzione indipendentemente dalla relativa posizione – finché non si implementa la logica per la rotazione del `Camera` utilizzando l'input. La prima modifica sarà per regolare la `lookAtVector` variabile a partire dal percorso corrente, anziché dall'aspetto professionale in una posizione assoluta:

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

Di conseguenza il `Camera` visualizzando il mondo straight-on. Si noti che iniziale `position` valore è stato modificato da `(0, 20, 10)` in modo che il `Camera` è centrata sull'asse X. In esecuzione verrà visualizzato il gioco:

![](part3-images/image12.png "Eseguire il gioco viene visualizzato in questa vista")

### <a name="creating-an-angle-variable"></a>Creazione di un angolo variabile

Il `lookAtVector` variabile controlla l'angolo che sta visualizzando la fotocamera. Attualmente è fissa nella visualizzazione verso il basso l'asse Y negativo e leggermente inclinata verso il basso (dal `-.5f` valore Z). Si creerà un' `angle` variabile che verrà usato per regolare il `lookAtVector` proprietà. 

Nelle sezioni precedenti di questa procedura dettagliata è stato illustrato matrici possono essere utilizzate per ruotare la modalità in cui vengono disegnati gli oggetti. È possibile anche usare matrici per ruotare vettori, ad esempio la `lookAtVector` utilizzando il `Vector3.Transform` (metodo). 

Aggiungere un `angle` campo e modificare il `ViewMatrix` proprietà come indicato di seguito:

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

### <a name="reading-input"></a>La lettura di input

Nostro `Camera` entità può essere ora completamente controllata attraverso la posizione e le variabili angolo: è sufficiente per modificarli in base all'input.

In primo luogo, si otterrà il `TouchPanel` dello stato per individuare dove l'utente tocca la schermata. Per altre informazioni sull'uso di `TouchPanel` classe, vedere [riferimento all'elemento TouchPanel API](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_Touch_TouchPanel).

Se l'utente tocca al terzo a sinistra, quindi, verranno modificati i `angle` valore in modo che il `Camera` Ruota a sinistra, e se l'utente tocca al terzo a destra, si sarà ruotare l'altro modo. Se l'utente tocca per la terza al centro dello schermo, quindi si passerà il `Camera` in avanti.

In primo luogo, aggiungere un tramite istruzione per qualificare le `TouchPanel` e `TouchCollection` le classi nello `Camera.cs`:

```csharp
using Microsoft.Xna.Framework.Input.Touch; 
```

A questo punto, modificare il `Update` metodo per leggere il pannello di tocco e regolare il `angle` e `position` variabili in modo appropriato:

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

![](part3-images/image1.gif "A questo punto la fotocamera risponderà all'input tocco")

Il metodo di aggiornamento inizia chiamando `TouchPanel.GetState`, che restituisce una raccolta di elementi. Sebbene `TouchPanel.GetState` può restituire più punti di tocco, ci sarà solo preoccupiamo quella del primo per motivi di semplicità.

Se l'utente tocca la schermata, quindi il codice controlla se il primo tocco è a sinistra, centrale o a destra terzo dello schermo. I terzi di sinistro e destro ruotano la fotocamera aumentando o diminuendo le `angle` variabile in base al `TotalSeconds` valore (in modo che il gioco è identico indipendentemente dalla frequenza dei fotogrammi).

Se l'utente tocca terzo al centro dello schermo, quindi la fotocamera verrà spostati in avanti. Questa operazione viene eseguita prima di tutto ottenendo il vettore di inoltro, che viene inizialmente definito come rivolta verso l'asse Y negativo ruotato di una matrice creata utilizzando `Matrix.CreateRotationZ` e il `angle` valore. Infine il `forwardVector` viene applicata a `position` usando il `unitsPerSecond` coefficiente.

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata illustra come spostare e ruotare `Models` in 3D spazio usando `Matrices` e il `BasicEffect.World` proprietà. Questa forma di movimento fornisce la base per lo spostamento di oggetti in giochi 3D. Questa procedura dettagliata illustra inoltre come implementare un `Camera` entità per la visualizzazione del mondo da qualsiasi posizione e un angolo.

## <a name="related-links"></a>Collegamenti correlati

- [Collegamento all'API MonoGame](http://www.monogame.net/documentation/?page=api)
- [Progetto finito (esempio)](https://developer.xamarin.com/samples/monodroid/MonoGame3DCamera/)
