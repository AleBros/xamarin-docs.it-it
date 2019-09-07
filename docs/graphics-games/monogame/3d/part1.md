---
title: Uso della classe Model
description: La classe modello semplifica notevolmente il rendering di oggetti 3D complessi rispetto al metodo tradizionale di rendering della grafica 3D. Gli oggetti modello vengono creati da file di contenuto, consentendo una facile integrazione del contenuto senza codice personalizzato.
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: c5702780b6a0f0732d846a2cd4226aec5e49fc21
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766827"
---
# <a name="using-the-model-class"></a>Uso della classe Model

_La classe modello semplifica notevolmente il rendering di oggetti 3D complessi rispetto al metodo tradizionale di rendering della grafica 3D. Gli oggetti modello vengono creati da file di contenuto, consentendo una facile integrazione del contenuto senza codice personalizzato._

L'API monogame include una `Model` classe che può essere usata per archiviare i dati caricati da un file di contenuto e per eseguire il rendering. I file di modello possono essere molto semplici, ad esempio un triangolo a tinta unita, oppure possono includere informazioni per il rendering complesso, tra cui texturing e illuminazione.

In questa procedura dettagliata viene utilizzato [un modello 3D di un robot](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) e vengono illustrate le operazioni seguenti:

- Avvio di un nuovo progetto di gioco
- Creazione di XNBs per il modello e la relativa trama
- Inclusione di XNBs nel progetto di gioco
- Disegno di un modello 3D
- Disegno di più modelli

Al termine, il progetto verrà visualizzato come segue:

![Esempio completato che mostra sei robot](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>Creazione di un progetto di gioco vuoto

Per prima cosa è necessario configurare un progetto di gioco denominato MonoGame3D. Per informazioni sulla creazione di un nuovo progetto MonoGame, vedere [questa procedura dettagliata sulla creazione di un progetto MonoGame multipiattaforma](~/graphics-games/monogame/introduction/part1.md).

Prima di procedere, è necessario verificare che il progetto venga aperto e distribuito correttamente. Una volta distribuita, verrà visualizzata una schermata blu vuota:

![Schermata del gioco blu vuota](part1-images/image2.png)

## <a name="including-the-xnbs-in-the-game-project"></a>Inclusione di XNBs nel progetto di gioco

Il formato del file. xnb è un'estensione standard per il contenuto compilato (contenuto creato dallo [strumento Pipeline monogame](http://www.monogame.net/documentation/?page=Pipeline)). Tutti i contenuti compilati hanno un file di origine (un file con estensione FBX nel caso del modello) e un file di destinazione (un file con estensione xnb). Il formato FBX è un formato di modello 3D comune che può essere creato da applicazioni come [Maya](http://www.autodesk.com/products/maya/overview) e [Blender](http://www.blender.org/). 

La `Model` classe può essere costruita caricando un file con estensione xnb da un disco che contiene dati geometrici 3D.   Il file con estensione xnb viene creato tramite un progetto di contenuto. I modelli monogame includono automaticamente un progetto di contenuto (con estensione MGCP) nella cartella del contenuto. Per una descrizione dettagliata dello strumento Pipeline monogame, vedere la [Guida alla pipeline di contenuti](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md).

Per questa guida si ignorerà l'uso dello strumento Pipeline monogame e verrà usato il. File XNB inclusi qui. Si noti che. I file XNB sono diversi per ogni piattaforma. Assicurarsi quindi di usare il set corretto di file XNB per qualsiasi piattaforma utilizzata.

Il [file content. zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) verrà decompresso per poter usare i file xnb contenuti nel gioco. Se si sta lavorando a un progetto Android, fare clic con il pulsante destro del mouse sulla cartella **assets** nel progetto **WalkingGame. Android** . Se si sta lavorando a un progetto iOS, fare clic con il pulsante destro del mouse sul progetto **WalkingGame. iOS** . Selezionare **Add-> Aggiungi file...** e selezionare entrambi i file con estensione xnb nella cartella della piattaforma su cui si sta lavorando.

I due file devono far parte del progetto ora:

![Cartella contenuto di Esplora soluzioni con file xnb](part1-images/xnbsinxs.png)

Visual Studio per Mac possibile non impostare automaticamente l'azione di compilazione per XNBs appena aggiunto. Per iOS, fare clic con il pulsante destro del mouse su ognuno dei file e scegliere **Crea azione-> BundleResource**. Per Android, fare clic con il pulsante destro del mouse su ognuno dei file e scegliere **Crea azione-> AndroidAsset**.

## <a name="rendering-a-3d-model"></a>Rendering di un modello 3D

L'ultimo passaggio necessario per visualizzare il modello sullo schermo consiste nell'aggiungere il codice di caricamento e di disegno. In particolare, verranno svolte le operazioni seguenti:

- Definizione di `Model` un'istanza `Game1` nella classe
- Caricamento dell' `Model` istanza in`Game1.LoadContent`
- Disegno dell' `Model` istanza in`Game1.Draw`

Sostituire il `Game1.cs` file di codice (che si trova nella libreria di classi portabile **WalkingGame** ) con quanto segue:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;

    // This is the model instance that we'll load
    // our XNB into:
    Model model;

    public Game1()
    {
        graphics = new GraphicsDeviceManager(this);
        graphics.IsFullScreen = true;

        Content.RootDirectory = "Content";
    }
    protected override void LoadContent()
    {
        // Notice that loading a model is very similar
        // to loading any other XNB (like a Texture2D).
        // The only difference is the generic type.
        model = Content.Load<Model> ("robot");
    }

    protected override void Update(GameTime gameTime)
    {
        base.Update(gameTime);
    }

    protected override void Draw(GameTime gameTime)
    {
        GraphicsDevice.Clear(Color.CornflowerBlue);

        // A model is composed of "Meshes" which are
        // parts of the model which can be positioned
        // independently, which can use different textures,
        // and which can have different rendering states
        // such as lighting applied.
        foreach (var mesh in model.Meshes)
        {
            // "Effect" refers to a shader. Each mesh may
            // have multiple shaders applied to it for more
            // advanced visuals. 
            foreach (BasicEffect effect in mesh.Effects)
            {
                // We could set up custom lights, but this
                // is the quickest way to get somethign on screen:
                effect.EnableDefaultLighting ();
                // This makes lighting look more realistic on
                // round surfaces, but at a slight performance cost:
                effect.PreferPerPixelLighting = true;

                // The world matrix can be used to position, rotate
                // or resize (scale) the model. Identity means that
                // the model is unrotated, drawn at the origin, and
                // its size is unchanged from the loaded content file.
                effect.World = Matrix.Identity;

                // Move the camera 8 units away from the origin:
                var cameraPosition = new Vector3 (0, 8, 0);
                // Tell the camera to look at the origin:
                var cameraLookAtVector = Vector3.Zero;
                // Tell the camera that positive Z is up
                var cameraUpVector = Vector3.UnitZ;

                effect.View = Matrix.CreateLookAt (
                    cameraPosition, cameraLookAtVector, cameraUpVector);

                // We want the aspect ratio of our display to match
                // the entire screen's aspect ratio:
                float aspectRatio = 
                    graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
                // Field of view measures how wide of a view our camera has.
                // Increasing this value means it has a wider view, making everything
                // on screen smaller. This is conceptually the same as "zooming out".
                // It also 
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                // Anything closer than this will not be drawn (will be clipped)
                float nearClipPlane = 1;
                // Anything further than this will not be drawn (will be clipped)
                float farClipPlane = 200;

                effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            }

            // Now that we've assigned our properties on the effects we can
            // draw the entire mesh
            mesh.Draw ();
        }
        base.Draw(gameTime);
    }
}
```

Se eseguiamo questo codice, il modello verrà visualizzato sullo schermo:

![Modello visualizzato sullo schermo](part1-images/image8.png "Se questo codice viene eseguito, il modello verrà visualizzato sullo schermo")

### <a name="model-class"></a>Classe modello

La `Model` classe è la classe principale per l'esecuzione del rendering 3D dai file di contenuto, ad esempio i file con estensione FBX. Contiene tutte le informazioni necessarie per il rendering, tra cui la geometria 3D, i riferimenti alla trama e `BasicEffect` le istanze che controllano i valori di posizionamento, illuminazione e fotocamera.

La `Model` classe stessa non dispone direttamente di variabili per il posizionamento poiché è possibile eseguire il rendering di una singola istanza del modello in più posizioni, come verrà illustrato più avanti in questa guida.

Ogni `Model` è costituito da una o `ModelMesh` più istanze, che vengono esposte tramite la `Meshes` proprietà. Sebbene sia possibile considerare `Model` come un unico oggetto gioco, ad esempio un robot o un'automobile, ognuno `ModelMesh` di essi può essere disegnato con valori `BasicEffect` diversi. Ad esempio, singole parti mesh possono rappresentare le gambe di un robot o delle ruote in un'auto ed è possibile assegnare i `BasicEffect` valori per far ruotare le ruote o spostare le gambe. 

### <a name="basiceffect-class"></a>Classe BasicEffect

La `BasicEffect` classe fornisce le proprietà per controllare le opzioni di rendering. La prima modifica apportata a `BasicEffect` consiste nel chiamare il `EnableDefaultLighting` metodo. Come suggerisce il nome, questo consente l'illuminazione predefinita, che è molto utile per verificare che un `Model` venga visualizzato nel gioco come previsto. Se si imposta come commento `EnableDefaultLighting` la chiamata, il modello verrà visualizzato solo con la relativa trama, ma senza bagliore ombreggiatura o speculare:

```csharp
//effect.EnableDefaultLighting ();
```

![Il modello di cui è stato eseguito il rendering con solo la trama, ma senza ombreggiatura o bagliore speculare](part1-images/image9.png "Il modello di cui è stato eseguito il rendering con solo la trama, ma senza ombreggiatura o bagliore speculare")

La `World` proprietà può essere utilizzata per modificare la posizione, la rotazione e la scala del modello. Il codice precedente usa il `Matrix.Identity` valore, il che significa che `Model` l'oggetto eseguirà il rendering nel gioco esattamente come specificato nel file con estensione FBX. Le matrici e le coordinate 3D verranno illustrate in modo più dettagliato nella [parte 3](~/graphics-games/monogame/3d/part3.md), ma, come esempio, è possibile modificare la `Model` posizione del modificando la `World` proprietà nel modo seguente:

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

Questo codice comporta lo spostamento dell'oggetto in base a 3 unità internazionali:

![Questo codice genera l'oggetto spostato di 3 unità internazionali](part1-images/image10.png "Questo codice genera l'oggetto spostato di 3 unità internazionali")

Le due proprietà finali assegnate in `BasicEffect` sono `View` e `Projection`. Verranno illustrate le fotocamere 3D nella [parte 3](~/graphics-games/monogame/3d/part3.md), ma, ad esempio, è possibile modificare la posizione della fotocamera cambiando la variabile locale `cameraPosition` :

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

Si noterà che la fotocamera è stata spostata di nuovo, con `Model` conseguente riduzione della visualizzazione a causa della prospettiva:

![La fotocamera è stata spostata di nuovo, di conseguenza il modello risulta più piccolo a causa della prospettiva](part1-images/image11.png "La fotocamera è stata spostata di nuovo, di conseguenza il modello risulta più piccolo a causa della prospettiva")

## <a name="rendering-multiple-models"></a>Rendering di più modelli

Come indicato in precedenza, un `Model` singolo può essere disegnato più volte. Per semplificare questa operazione, il `Model` codice di disegno verrà spostato in un proprio metodo che accetta la posizione desiderata `Model` come parametro. Al termine, i `Draw` metodi `DrawModel` e appariranno come segue:

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);
    DrawModel (new Vector3 (-4, 0, 0));
    DrawModel (new Vector3 ( 0, 0, 0));
    DrawModel (new Vector3 ( 4, 0, 0));
    DrawModel (new Vector3 (-4, 0, 3));
    DrawModel (new Vector3 ( 0, 0, 3));
    DrawModel (new Vector3 ( 4, 0, 3));
    base.Draw(gameTime);
}
void DrawModel(Vector3 modelPosition)
{
    foreach (var mesh in model.Meshes)
    {
        foreach (BasicEffect effect in mesh.Effects)
        {
            effect.EnableDefaultLighting ();
            effect.PreferPerPixelLighting = true;
            effect.World = Matrix.CreateTranslation (modelPosition);
            var cameraPosition = new Vector3 (0, 10, 0);
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
        }
        // Now that we've assigned our properties on the effects we can
        // draw the entire mesh
        mesh.Draw ();
    }
}
```

In questo modo il modello di robot viene disegnato sei volte:

![Ciò comporta il disegno del modello robot sei volte](part1-images/image1.png "Ciò comporta il disegno del modello robot sei volte")

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stata `Model` introdotta la classe monogame. Viene illustrata la conversione di un file con estensione FBX in un file con estensione xnb, che può essere `Model` caricato a sua volta in una classe. Viene inoltre illustrato in che modo le `BasicEffect` modifiche apportate a un'istanza possono influisca `Model` sul disegno

## <a name="related-links"></a>Collegamenti correlati

- [Riferimento al modello monogame](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [Progetto completato (esempio)](https://docs.microsoft.com/samples/xamarin/mobile-samples/modelrenderingmg/)
