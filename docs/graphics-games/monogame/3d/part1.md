---
title: Utilizzo della classe modello
description: La classe modello semplifica notevolmente il rendering di oggetti 3D complessi rispetto al metodo tradizionale per il rendering della grafica 3D. Gli oggetti modello vengono creati da file di contenuto, consentendo di facile integrazione del contenuto senza codice personalizzato.
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 4a72effc85657b4722b17eae486e81db5992a1da
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832252"
---
# <a name="using-the-model-class"></a>Utilizzo della classe modello

_La classe modello semplifica notevolmente il rendering di oggetti 3D complessi rispetto al metodo tradizionale per il rendering della grafica 3D. Gli oggetti modello vengono creati da file di contenuto, consentendo di facile integrazione del contenuto senza codice personalizzato._

L'API MonoGame include un `Model` classe che può essere usato per archiviare i dati caricati da un file di contenuto e per eseguire il rendering. I file di modello possono essere molto semplici (ad esempio, un triangolo di colorato a tinta unita) o possono includere le informazioni per il rendering complessa, tra cui la trama e illuminazione.

Questa procedura dettagliata usi [un modello 3D di un robot](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) e illustra quanto segue:

- Avvia un nuovo progetto di gioco
- Creazione XNBs per il modello e la trama
- Tra cui la XNBs nel progetto di gioco
- Disegno di un modello 3D
- Disegno di più modelli

Al termine, il progetto verrà visualizzato come segue:

![Esempio completato con i sei robot](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>Creazione di un progetto di gioco vuoto

È necessario configurare un progetto di gioco chiamato innanzitutto MonoGame3D. Per informazioni sulla creazione di un nuovo progetto MonoGame, vedere [questa procedura dettagliata sulla creazione di un progetto Monogame di piattaforma incrociata](~/graphics-games/monogame/introduction/part1.md).

Prima di procedere è necessario verificare che il progetto viene aperto e viene distribuito correttamente. Una volta distribuito dovremmo vedere una schermata blu vuota:

![Schermata di gioco blu vuota](part1-images/image2.png)


## <a name="including-the-xnbs-in-the-game-project"></a>Tra cui la XNBs nel progetto di gioco

Il formato di file .xnb è un'estensione standard per il contenuto predefinito (contenuto che è stato creato per il [strumento della Pipeline MonoGame](http://www.monogame.net/documentation/?page=Pipeline)). Tutto il contenuto predefinito è un file di origine (ovvero un file. fbx nel caso il nostro modello) e un file di destinazione (file .xnb). Il formato. fbx è un formato di modello 3D più comuni che può essere creato dalle applicazioni, ad esempio [Maya](http://www.autodesk.com/products/maya/overview) e [Blender](http://www.blender.org/). 

Il `Model` classe può essere costruita caricando un file .xnb da un disco che contiene dati geometrici 3D.   Questo file .xnb viene creato tramite un progetto content. Monogame modelli includono automaticamente un progetto (con l'estensione di .mgcp) contenuto nella cartella del contenuto del nostro. Per informazioni dettagliate sullo strumento MonoGame Pipeline, vedere la [Guida alla Pipeline di contenuti](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md).

In questa Guida verrà ignorata rispetto all'uso della MonoGame Pipeline dello strumento e userà il. File XNB inclusi qui. Si noti che il. File XNB differiscono per ogni piattaforma, pertanto assicurarsi di usare il set corretto di file XNB per qualsiasi piattaforma si sta lavorando.

È possibile decomprimere i [Content.zip file](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) in modo che è possibile usare i file contenuti .xnb nel nostro gioco. Se Usa un progetto Android, fare clic sul **asset** cartella la **WalkingGame.Android** progetto. Se Usa un progetto iOS, fare clic sui **WalkingGame.iOS** progetto. Selezionare **Aggiungi -> Aggiungi file...**  e selezionare entrambi i file .xnb nella cartella per la piattaforma si sta lavorando.

I due file devono far parte del nostro progetto ora:

![Cartella del contenuto Esplora soluzioni con file xnb](part1-images/xnbsinxs.png)

Visual Studio per Mac non impostare automaticamente l'azione di compilazione per XNBs appena aggiunto. Per iOS, fare clic su ognuno dei file e selezionare **azione di compilazione -> BundleResource**. Per Android, fare clic su ognuno dei file e selezionare **azione di compilazione -> AndroidAsset**.

## <a name="rendering-a-3d-model"></a>Il rendering di un modello 3D

L'ultimo passaggio necessario per visualizzare il modello sullo schermo consiste nell'aggiungere il caricamento e il codice di disegno. In particolare, è possibile essere le operazioni seguenti:

- La definizione di un `Model` dell'istanza nel nostro `Game1` classe
- Il caricamento di `Model` dell'istanza in `Game1.LoadContent`
- Disegno di `Model` dell'istanza in `Game1.Draw`

Sostituire il `Game1.cs` file di codice (che si trova nel **WalkingGame** PCL) con il codice seguente:

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

Se si esegue questo codice che si vedrà il modello sullo schermo:

![Modello visualizzato sullo schermo](part1-images/image8.png "se questo codice viene eseguito, il modello verrà visualizzato sullo schermo")

### <a name="model-class"></a>Classe di modello

Il `Model` classe è la classe di base per l'esecuzione di rendering 3D da file di contenuto (ad esempio i file. fbx). Contiene tutte le informazioni necessarie per il rendering, tra cui la geometria 3D, i riferimenti di trama, e `BasicEffect` istanze che consentono di controllare i valori di posizionamento, luce e fotocamera.

Il `Model` stessa classe non dispone direttamente le variabili per il posizionamento in quanto è possibile eseguire il rendering di un'istanza singola del modello in più posizioni, come si vedrà più avanti in questa Guida.

Ciascuna `Model` è costituito da uno o più `ModelMesh` istanze, che vengono esposte tramite il `Meshes` proprietà. Anche se si potrebbe prendere in considerazione una `Model` come un singolo gioco oggetto (ad esempio un robot o un'auto), ognuno `ModelMesh` possono essere disegnati con diversi `BasicEffect` valori. Ad esempio, rete mesh di singole parti possono rappresentare le tratte di trasformarsi in un'automobile o un robot e Microsoft può cedere il `BasicEffect` spostano i valori per rendere le gambe o la casella di selezione file Wheel. 

### <a name="basiceffect-class"></a>Classe BasicEffect

Il `BasicEffect` classe fornisce proprietà per controllare le opzioni di rendering. La prima modifica rendiamo per il `BasicEffect` consiste nel chiamare il `EnableDefaultLighting` (metodo). Come suggerisce il nome, in questo modo l'illuminazione predefinito, che è molto utile per la verifica che un `Model` viene visualizzata nel gioco come previsto. Se è impostare come commento il `EnableDefaultLighting` chiamare, vedremo il modello sottoposto a rendering con solo la trama, ma nessuna ombreggiatura o speculare alone:

```csharp
//effect.EnableDefaultLighting ();
```

![Il modello sottoposto a rendering con solo la trama, ma nessuna ombreggiatura o speculare alone](part1-images/image9.png "il modello sottoposto a rendering con solo la trama, ma nessuna ombreggiatura o speculare alone")

Il `World` proprietà può essere utilizzata per modificare la posizione, rotazione e ridimensionare il modello. Il codice precedente Usa la `Matrix.Identity` valore, il che significa che il `Model` verrà eseguito il rendering nel gioco esattamente come specificato nel file. fbx. Si parlerà matrici e le coordinate 3D in modo più dettagliato nella [parte 3](~/graphics-games/monogame/3d/part3.md), ma come un esempio è possibile passare alla posizione del `Model` modificando la `World` proprietà come indicato di seguito:

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

Questo codice genera l'oggetto spostato verso l'alto per 3 unità di misura internazionali:

![Questo codice genera l'oggetto spostato verso l'alto per 3 unità di misura internazionali](part1-images/image10.png "questo codice genera l'oggetto spostato verso l'alto per 3 unità di misura internazionali")

Le due proprietà finale assegnate per il `BasicEffect` vengono `View` e `Projection`. Si parlerà 3D videocamere [parte 3](~/graphics-games/monogame/3d/part3.md), ma ad esempio, è possibile modificare la posizione della camera modificando locale `cameraPosition` variabile:

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

Noteremo la fotocamera è stato spostato ulteriormente indietro, causando la `Model` visualizzati più piccolo a causa di un punto di vista:

![La fotocamera è stato spostato ulteriormente indietro, causando il modello di visualizzazione più piccolo a causa di un punto di vista](part1-images/image11.png "la fotocamera è stato spostato ulteriormente indietro, causando il modello di visualizzazione più piccolo a causa di un punto di vista")

## <a name="rendering-multiple-models"></a>Il rendering di più modelli

Come indicato in precedenza, un singolo `Model` possibile tracciare più volte. Per semplificare questa operazione sarà lo spostamento di `Model` codice di disegno in un metodo che accetta il valore desiderato `Model` posizione come parametro. Una volta completato, nostro `Draw` e `DrawModel` metodi avrà un aspetto simile:


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

Ciò comporta il robot del modello da disegnare sei volte:

![Il risultato è il modello da disegnare sei volte robot](part1-images/image1.png "il risultato è il modello da disegnare sei volte robot")

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata introdotto del MonoGame `Model` classe. Viene descritto come la conversione di un file. fbx in un .xnb, che può a sua volta essere caricata in un `Model` classe. Viene inoltre illustrato come le modifiche a un `BasicEffect` istanza può influire sulle `Model` disegno.

## <a name="related-links"></a>Collegamenti correlati

- [Riferimento del modello MonoGame](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [Progetto completato (esempio)](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/)
