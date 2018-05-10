---
title: Utilizzo della classe modello
description: La classe modello semplifica notevolmente il rendering di oggetti 3D complessi rispetto al metodo tradizionale per il rendering della grafica 3D. Gli oggetti del modello vengono creati da file di contenuto, consentendo di facile integrazione del contenuto con alcun codice personalizzato.
ms.prod: xamarin
ms.assetid: AD0A7971-51B1-4E38-B412-7907CE43CDDF
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 01e000b22749acb1b5c3a3203db7f372613cca16
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="using-the-model-class"></a>Utilizzo della classe modello

_La classe modello semplifica notevolmente il rendering di oggetti 3D complessi rispetto al metodo tradizionale per il rendering della grafica 3D. Gli oggetti del modello vengono creati da file di contenuto, consentendo di facile integrazione del contenuto con alcun codice personalizzato._

L'API MonoGame include un `Model` classe che può essere utilizzato per archiviare i dati caricati da un file di contenuto e per eseguire il rendering. File di modello possono essere molto semplici (ad esempio, un triangolo di colore a tinta unita) o possono includere informazioni per il rendering complesso, inclusi la trama e illuminazione.

Questa procedura dettagliata Usa [un modello 3D del robot](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) e ne illustra le operazioni seguenti:

- Avvio di un nuovo progetto di gioco
- Creazione di XNBs per il modello e la trama
- Tra cui la XNBs nel progetto di gioco
- Disegno di un modello 3D
- Più modelli di disegno

Al termine, il progetto verrà visualizzato come segue:

![Esempio completata con sei robot](part1-images/image1.png)

## <a name="creating-an-empty-game-project"></a>Creazione di un progetto di gioco vuoto

È necessario configurare un progetto di gioco prima chiamato MonoGame3D. Per informazioni sulla creazione di un nuovo progetto MonoGame, vedere [in questa procedura dettagliata sulla creazione di un progetto di Monogame Cross Platform](~/graphics-games/monogame/introduction/part1.md).

Prima di proseguire è necessario verificare che il progetto viene aperto e viene distribuito correttamente. Una volta distribuito dovremmo vedere una schermata blu vuota:

![Schermata di gioco blu vuota](part1-images/image2.png)


## <a name="including-the-xnbs-in-the-game-project"></a>Tra cui la XNBs nel progetto di gioco

Il formato di file .xnb è un'estensione standard per il contenuto incorporato (contenuto che è stato creato dal [strumento di Pipeline MonoGame](http://www.monogame.net/documentation/?page=Pipeline)). Tutto il contenuto incorporato un file di origine (ovvero un file con estensione fbx nel caso il nostro modello) e un file di destinazione (un file .xnb). Il formato con estensione fbx è un formato di un modello 3D comuni che può essere creato dalle applicazioni, ad esempio [Maya](http://www.autodesk.com/products/maya/overview) e [Blender](http://www.blender.org/). 

La `Model` classe può essere creata con il caricamento di un file .xnb da un disco che contiene dati geometrici 3D.   Questo file .xnb viene creato tramite un progetto di contenuti. Monogame modelli includono automaticamente un progetto (con l'estensione di .mgcp) contenuto nella cartella contenuto. Per informazioni dettagliate sullo strumento di MonoGame Pipeline, vedere il [Guida contenuto Pipeline](~/graphics-games/cocossharp/content-pipeline/index.md).

In questa guida si sarà saltare tramite la MonoGame Pipeline strumento e utilizzerà il. File XNB qui inclusi. Si noti che il. File XNB diversi per ogni piattaforma, pertanto assicurarsi di utilizzare il set corretto di file XNB per qualsiasi piattaforma in uso.

È possibile decomprimere il [Content.zip file](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true) in modo da poter utilizzare i file contenuti .xnb il gioco. Se utilizzi un progetto Android, fare clic su di **asset** cartella la **WalkingGame.Android** progetto. Se utilizzi un progetto iOS, fare clic su di **WalkingGame.iOS** progetto. Selezionare **Aggiungi -> Aggiungi file...**  e selezionare entrambi i file .xnb nella cartella per la piattaforma si sta lavorando.

I due file devono essere parte del progetto ora:

![Cartella del contenuto Esplora soluzioni con file xnb](part1-images/xnbsinxs.png)

Visual Studio per Mac non può impostare automaticamente l'azione di compilazione per XNBs appena aggiunti. Per iOS, fare doppio clic su ciascuno dei file e selezionare **azione di compilazione -> BundleResource**. Per Android, fare doppio clic su ciascuno dei file e selezionare **azione di compilazione -> AndroidAsset**.

## <a name="rendering-a-3d-model"></a>Il rendering di un modello 3D

L'ultimo passaggio necessario per il modello viene visualizzato sullo schermo consiste nell'aggiungere il caricamento e il codice di disegno. In particolare, si sarà possibile eseguire le operazioni seguenti:

- La definizione di un `Model` istanza nel nostro `Game1` classe
- Il caricamento di `Model` dell'istanza in `Game1.LoadContent`
- Disegno di `Model` dell'istanza in `Game1.Draw`

Sostituire il `Game1.cs` file di codice (che si trova nella **WalkingGame** PCL) con le operazioni seguenti:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;

    // This is the model instance that we'll load
    // our XNB into:
    Model model;

    public Game1()
    {
        graphics = new GraphicsDeviceManager(this);
        graphics.IsFullScreen = true;

        Content.RootDirectory = "Content";
    }
    protected override void LoadContent()
    {
        // Notice that loading a model is very similar
        // to loading any other XNB (like a Texture2D).
        // The only difference is the generic type.
        model = Content.Load<Model> ("robot");
    }

    protected override void Update(GameTime gameTime)
    {
        base.Update(gameTime);
    }

    protected override void Draw(GameTime gameTime)
    {
        GraphicsDevice.Clear(Color.CornflowerBlue);

        // A model is composed of "Meshes" which are
        // parts of the model which can be positioned
        // independently, which can use different textures,
        // and which can have different rendering states
        // such as lighting applied.
        foreach (var mesh in model.Meshes)
        {
            // "Effect" refers to a shader. Each mesh may
            // have multiple shaders applied to it for more
            // advanced visuals. 
            foreach (BasicEffect effect in mesh.Effects)
            {
                // We could set up custom lights, but this
                // is the quickest way to get somethign on screen:
                effect.EnableDefaultLighting ();
                // This makes lighting look more realistic on
                // round surfaces, but at a slight performance cost:
                effect.PreferPerPixelLighting = true;

                // The world matrix can be used to position, rotate
                // or resize (scale) the model. Identity means that
                // the model is unrotated, drawn at the origin, and
                // its size is unchanged from the loaded content file.
                effect.World = Matrix.Identity;

                // Move the camera 8 units away from the origin:
                var cameraPosition = new Vector3 (0, 8, 0);
                // Tell the camera to look at the origin:
                var cameraLookAtVector = Vector3.Zero;
                // Tell the camera that positive Z is up
                var cameraUpVector = Vector3.UnitZ;

                effect.View = Matrix.CreateLookAt (
                    cameraPosition, cameraLookAtVector, cameraUpVector);

                // We want the aspect ratio of our display to match
                // the entire screen's aspect ratio:
                float aspectRatio = 
                    graphics.PreferredBackBufferWidth / (float)graphics.PreferredBackBufferHeight;
                // Field of view measures how wide of a view our camera has.
                // Increasing this value means it has a wider view, making everything
                // on screen smaller. This is conceptually the same as "zooming out".
                // It also 
                float fieldOfView = Microsoft.Xna.Framework.MathHelper.PiOver4;
                // Anything closer than this will not be drawn (will be clipped)
                float nearClipPlane = 1;
                // Anything further than this will not be drawn (will be clipped)
                float farClipPlane = 200;

                effect.Projection = Matrix.CreatePerspectiveFieldOfView(
                    fieldOfView, aspectRatio, nearClipPlane, farClipPlane);

            }

            // Now that we've assigned our properties on the effects we can
            // draw the entire mesh
            mesh.Draw ();
        }
        base.Draw(gameTime);
    }
}
```

Se si esegue questo codice è verrà visualizzato sullo schermo del modello:

![Modello visualizzato sullo schermo](part1-images/image8.png "se questo codice viene eseguito, il modello verrà visualizzato sullo schermo")

### <a name="model-class"></a>Classe di modello

La `Model` classe è la classe di base per l'esecuzione di rendering 3D da file di contenuto (ad esempio file con estensione fbx). Contiene tutte le informazioni necessarie per il rendering, tra cui la geometria 3D, i riferimenti di trama, e `BasicEffect` istanze che controllano i valori di posizionamento di illuminazione e fotocamera.

Il `Model` stessa classe non dispone direttamente le variabili per il posizionamento poiché è possibile eseguire il rendering di un'istanza del modello singolo in più posizioni, come verrà illustrato più avanti in questa Guida.

Ogni `Model` è costituito da uno o più `ModelMesh` istanze, che sono esposte tramite la `Meshes` proprietà. Anche se è possibile prendere in considerazione un `Model` come un gioco singolo oggetto (ad esempio un robot o un'automobile), ogni `ModelMesh` possono essere disegnate con diversi `BasicEffect` valori. Ad esempio, rete singole parti possono rappresentare i segmenti di un robot o ruote su un'automobile, e Microsoft può cedere il `BasicEffect` spostano i valori per rendere la casella di selezione ruote o i segmenti. 

### <a name="basiceffect-class"></a>Classe BasicEffect

La `BasicEffect` classe fornisce proprietà per controllare le opzioni di rendering. La prima modifica che fare per il `BasicEffect` consiste nel chiamare il `EnableDefaultLighting` metodo. Come suggerisce il nome, in questo modo l'illuminazione predefinito, che è molto utile per verificare che un `Model` viene visualizzato all'interno del gioco come previsto. Se è impostare come commento il `EnableDefaultLighting` chiamare, quindi possiamo vedere il modello sottoposto a rendering con solo la trama, ma con Nessun alone ombreggiatura o speculare:

```csharp
//effect.EnableDefaultLighting ();
```

![Il modello sottoposto a rendering con semplicemente la trama, ma con Nessun alone ombreggiatura o speculare](part1-images/image9.png "il modello sottoposto a rendering con semplicemente la trama, ma con Nessun alone ombreggiatura o speculare")

Il `World` proprietà può essere utilizzata per modificare la posizione, rotazione e scala del modello. Il codice precedente viene usata la `Matrix.Identity` valore, il che significa che il `Model` verrà eseguito il rendering nel gioco esattamente come specificato nel file con estensione fbx. Parleremo matrici e le coordinate 3D in modo più dettagliato in [parte 3](~/graphics-games/monogame/3d/part3.md), ma come un esempio è possibile modificare la posizione del `Model` modificando il `World` proprietà come indicato di seguito:

```csharp
// Z is up, so changing Z to 3 moves the object up 3 units:
var modelPosition = new Vector3 (0, 0, 3);
effect.World = Matrix.CreateTranslation (modelPosition); 
```

Questo codice genera l'oggetto spostato verso l'alto per unità di misura internazionali 3:

![Questo codice genera l'oggetto spostato verso l'alto per 3 unità di misura internazionali](part1-images/image10.png "questo codice genera l'oggetto spostato verso l'alto per unità di misura internazionali 3")

Le due proprietà finale assegnate per la `BasicEffect` sono `View` e `Projection`. Parleremo fotocamere 3D in [parte 3](~/graphics-games/monogame/3d/part3.md), ma ad esempio, è possibile modificare la posizione della camera modificando locale `cameraPosition` variabile:

```csharp
// The 8 has been changed to a 30 to move the Camera further back
var cameraPosition = new Vector3 (0, 30, 0);
```

Possiamo vedere la fotocamera è stato spostato indietro ulteriormente, determinando la `Model` visualizzati inferiori a causa di punto di vista:

![La fotocamera è stato spostato back ulteriormente, risultante nel modello di visualizzazione più piccole a causa di prospettiva](part1-images/image11.png "la fotocamera è stato spostato back ulteriormente, risultante nel modello di visualizzazione più piccole a causa di prospettiva")

## <a name="rendering-multiple-models"></a>Il rendering di più modelli

Come indicato in precedenza, un singolo `Model` possono essere disegnate più volte. Per semplificare questa operazione sarà lo spostamento di `Model` codice di disegno nel relativo metodo che accetta il desiderato `Model` posizione come parametro. Una volta terminato, il nostro `Draw` e `DrawModel` metodi avrà un aspetto simile:


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

Ciò comporta robot modello tracciato sei volte:

![Di conseguenza, il modello che viene disegnato sei volte robot](part1-images/image1.png "di conseguenza, il modello che viene disegnato sei volte robot")

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata introdotto del MonoGame `Model` classe. Descritta la conversione di un file con estensione fbx in un .xnb, che può a loro volta essere caricata in un `Model` classe. Viene inoltre illustrato come le modifiche a un `BasicEffect` istanza può influire sulle `Model` disegno.

## <a name="related-links"></a>Collegamenti correlati

- [Riferimento del modello MonoGame](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Graphics_Model)
- [Content.zip](https://github.com/xamarin/mobile-samples/blob/master/ModelRenderingMG/Resources/Content.zip?raw=true)
- [Progetto completato (esempio)](https://developer.xamarin.com/samples/mobile/ModelRenderingMG/)
