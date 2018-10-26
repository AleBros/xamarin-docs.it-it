---
title: Uso di Tiled con CocosSharp
description: Affiancato è una potente, flessibile e applicazione obsoleta per la creazione di riquadro ortogonale e isometrica viene eseguito il mapping per i giochi. CocosSharp offre integrazione predefinita per il formato di file nativi dell'affiancato.
ms.prod: xamarin
ms.assetid: 804C042C-F62A-4E6C-B10F-06528637F0E2
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 4582b59a8a441c9e22761d498126898e66db08c1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117928"
---
# <a name="using-tiled-with-cocossharp"></a>Uso di Tiled con CocosSharp

_Affiancato è una potente, flessibile e applicazione obsoleta per la creazione di riquadro ortogonale e isometrica viene eseguito il mapping per i giochi. CocosSharp offre integrazione predefinita per il formato di file nativi dell'affiancato._

Il *affiancato* dell'applicazione è uno standard per la creazione *riquadro mappe* per l'uso nello sviluppo di giochi. Questa guida descrive come accettare un file .tmx esistente (file creati da affiancato) e usarla in un gioco CocosSharp. In particolare in questa guida illustrerà quanto segue:

 - Lo scopo di tessere mappe
 - Uso dei file .tmx
 - Considerazioni per il rendering di immagini di pixel
 - Utilizzo di proprietà sezione in fase di esecuzione

Quando termine si avrà la demo seguente:

![](tiled-images/image1.png "L'app demo creato seguendo i passaggi descritti in questa Guida")


## <a name="the-purpose-of-tile-maps"></a>Lo scopo di tessere mappe

Mappe di riquadro presenti nello sviluppo di giochi per decenni, ma vengono ancora utilizzate nei giochi 2D per l'efficacia ed esthetics. Riquadro le mappe sono in grado di raggiungere un elevato livello di efficienza tramite l'uso dei set di riquadro, l'immagine di origine utilizzata dal riquadro mappe. Un set di riquadro è una raccolta di immagini combinati in un unico file. Anche se i set di riquadro fare riferimento alle immagini utilizzate nelle mappe di riquadro, i file che contengono più immagini più piccole sono detti anche i fogli di sprite o sprite esegue il mapping allo sviluppo di giochi. È possibile visualizzare utilizzo riquadro set mediante l'aggiunta di una griglia per il set di riquadro che utilizzeremo nella nostra demo:

![](tiled-images/image2.png "Una vista visualizzata di come vengono usati set riquadro mediante l'aggiunta di che una griglia nel riquadro del set che verrà usata nella demo")

Riquadro mappe disporre i singoli riquadri dai affiancata set. Si noti che ogni riquadro mappa non è necessario archiviare la propria copia della tessera di dover impostare – invece più tessere mappe possono fare riferimento allo stesso set di riquadro. Ciò significa che oltre il set di riquadro, le mappe di riquadro richiedono poca memoria. Ciò consente la creazione di un numero elevato di mappe di riquadro, anche quando vengono usati per creare un'area di gioco di grandi dimensioni, ad esempio un [scorrimento platformer](http://en.wikipedia.org/wiki/Platform_game) ambiente. Possibili disposizioni usando lo stesso set di riquadro seguente:

![](tiled-images/image3.png "Questa immagine Mostra possibili disposizioni usando lo stesso set di riquadro")

![](tiled-images/image4.png "Questa immagine Mostra possibili disposizioni usando lo stesso set di riquadro")


## <a name="working-with-tmx-files"></a>Uso dei file .tmx

Il formato di file .tmx è un file XML creato dall'applicazione affiancato, che può essere [scaricati gratuitamente sul sito Web affiancato](http://www.mapeditor.org/). Il formato di file .tmx archivia le informazioni per le mappe di riquadro. In genere un gioco avranno un unico file .tmx per ogni area separato o a un livello.

Questa guida è incentrata su come usare i file esistenti .tmx in CocosSharp; Tuttavia, altre esercitazioni sono reperibili online, incluso [questa presentazione dell'editor di mappa affiancato](http://gamedevelopment.tutsplus.com/tutorials/introduction-to-tiled-map-editor--gamedev-2838).

È necessario decomprimere il [file con estensione zip contenuto](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true) usarlo nel nostro gioco. La prima cosa da notare è che maps riquadro usare sia il file .tmx (dungeon.tmx) oltre a una o più file di immagine che definiscono il riquadro dei dati (dungeon_1.png). Il gioco deve includere entrambi i file per caricare il .tmx in fase di esecuzione, in questo caso aggiungere entrambi al progetto **contenuto** cartella (in cui è contenuta la **asset** cartella nei progetti di Android). In particolare, aggiungere i file in una cartella denominata **tilemaps** all'interno di **contenuto** cartella:

![](tiled-images/image5.png "Aggiungere i file in una cartella denominata tilemaps all'interno della cartella del contenuto")

Il **dungeon.tmx** ora è possibile caricare file in fase di esecuzione in un `CCTileMap` oggetto. A questo punto, modificare il `GameLayer` (o un oggetto contenitore radice equivalente) per contenere un `CCTileMap` istanza e aggiungerlo come elemento figlio:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");
        this.AddChild (tileMap);
    }
} 
```

Se eseguiamo il gioco si vedrà la tessera mappa vengono visualizzati nell'angolo inferiore sinistro della schermata:

![](tiled-images/image6.png "Se il gioco viene eseguito, il riquadro mappa vengono visualizzati nell'angolo inferiore sinistro della schermata")


## <a name="considerations-for-rendering-pixel-art"></a>Considerazioni per il rendering di immagini di pixel

Arte pixel, nel contesto di sviluppo di videogiochi, fa riferimento a 2D visual arte che viene in genere creato dalla persona ed è spesso a bassa risoluzione. Arte pixel può essere correggono tempi lunghi per la creazione, in modo pixel art riquadro set spesso includono riquadri a bassa risoluzione, ad esempio 16 o 32 pixel larghezza e altezza. Se non è ridimensionato in fase di esecuzione, art pixel è spesso troppo piccolo per più moderni telefoni e Tablet.

È possibile modificare le dimensioni visualizzate nel file GameAppDelegate.cs del nostro gioco, in cui si aggiungerà una chiamata a `CCScene.SetDefaultDesignResolution`:


```csharp
 public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");

    CCSize windowSize = mainWindow.WindowSizeInPixels;

    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to be (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDefaultDesignResolution (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    
    // Determine whether to use the high or low def versions of our images
    // Make sure the default texel to content size ratio is set correctly
    // Of course you're free to have a finer set of image resolutions e.g (ld, hd, super-hd)
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }

    // New code:
    CCScene.SetDefaultDesignResolution (380, 240, CCSceneResolutionPolicy.ShowAll);

    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);

    mainWindow.RunWithScene (scene);
} 
```

Per ulteriori informazioni sul `CCSceneResolutionPolicy`, vedere la Guida sul [gestisce risoluzioni in CocosSharp](~/graphics-games/cocossharp/resolutions.md).

Se eseguiamo il gioco a questo punto, si noterà il gioco occupato l'intero schermo del dispositivo:

![](tiled-images/image7.png "Il gioco occupato l'intero schermo del dispositivo")

È infine opportuno disabilitare anti-aliasing sulla tessera mappa. Il `Antialiased` proprietà si applica un effetto di sfocatura durante il rendering di oggetti che vengono ingranditi. Anti-aliasing può essere utile per ridurre l'aspetto disturbate di oggetti grafici, ma potrebbe introdurre anche i propri elementi di rendering. In particolare, anti-aliasing consente di sfocare il contenuto di ogni sezione. Tuttavia, i bordi di ogni riquadro non sfocatura, che rende i singoli riquadri di mettere in evidenza anziché la fusione con riquadri adiacenti. Si noti anche che i giochi art pixel conservare spesso il loro aspetto disturbate per mantenere una *retrò* sentirsi.

Impostare `Antialiased` al `false` al termine della creazione di `tileMap`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();

    tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

    // new code:
    tileMap.Antialiased = false;

    this.AddChild (tileMap);
} 
```

A questo punto la tessera mappa non appariranno sfocata:

![](tiled-images/image8.png "A questo punto la tessera mappa non verrà visualizzato sfocata")


## <a name="using-tile-properties-at-runtime"></a>Utilizzo di proprietà sezione in fase di esecuzione

Finora abbiamo un `CCTileMap` il caricamento di file .tmx e visualizzarli, ma non è disponibile alcun modo per interagire con esso. In particolare, determinati riquadri (ad esempio, i nostri dotazione tesoro) devono avere una logica personalizzata. È possibile scorrere come rilevare le proprietà di riquadro personalizzato e sui vari modi per rispondere a queste proprietà di una volta identificate in fase di esecuzione.

Prima che si scrive codice, è necessario aggiungere le proprietà per la mappa di riquadro tramite affiancato. A tale scopo, aprire il file dungeon.tmx nel programma affiancato. Assicurarsi di aprire il file che viene usato nel progetto di gioco.

Una volta aperto, selezionare il dotazione tesoro nella sezione del set per visualizzarne le proprietà:

![](tiled-images/image9.png "Una volta aperto, selezionare il dotazione tesoro nella sezione del set per visualizzarne le proprietà")

Se non sono visualizzate le proprietà di dotazione Tesoro, su dotazione il Tesoro e scegliere **proprietà del riquadro**:

![](tiled-images/image10.png "Se non sono visualizzate le proprietà di dotazione Tesoro, su dotazione il Tesoro e scegliere Proprietà del riquadro")

Proprietà affiancata vengono implementate con un nome e un valore. Per aggiungere una proprietà, scegliere il **+** pulsante, immettere il nome **IsTreasure**, fare clic su **OK**, quindi immettere il valore **true**: 

![](tiled-images/image11.png "Per aggiungere una proprietà, fare clic sul pulsante, immettere il nome IsTreasure fare clic su OK, quindi immettere il valore true")

Non dimenticare di salvare il file .tmx dopo la modifica delle proprietà.

Infine, si aggiungerà codice per cercare la proprietà appena aggiunto. Si avvierà un loop attraverso ciascun `CCTileMapLayer` (la mappa include 2 livelli), quindi a ogni riga e colonna per cercare tutte le sezioni che hanno il `IsTreasure` proprietà:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

        // new code:
        tileMap.Antialiased = false;

        this.AddChild (tileMap);

        HandleCustomTileProperties (tileMap);
    }

    void HandleCustomTileProperties(CCTileMap tileMap)
    {
        // Width and Height are equal so we can use either
        int tileDimension = (int)tileMap.TileTexelSize.Width;

        // Find out how many rows and columns are in our tile map
        int numberOfColumns = (int)tileMap.MapDimensions.Size.Width;
        int numberOfRows = (int)tileMap.MapDimensions.Size.Height;

        // Tile maps can have multiple layers, so let's loop through all of them:
        foreach (CCTileMapLayer layer in tileMap.TileLayersContainer.Children)
        {
            // Loop through the columns and rows to find all tiles
            for (int column = 0; column < numberOfColumns; column++)
            {
                // We're going to add tileDimension / 2 to get the position
                // of the center of the tile - this will help us in 
                // positioning entities, and will eliminate the possibility
                // of floating point error when calculating the nearest tile:
                int worldX = tileDimension * column + tileDimension / 2;
                for (int row = 0; row < numberOfRows; row++)
                {
                    // See above on why we add tileDimension / 2
                    int worldY = tileDimension * row + tileDimension / 2;

                    HandleCustomTilePropertyAt (worldX, worldY, layer);
                }
            }
        }
    }

    void HandleCustomTilePropertyAt(int worldX, int worldY, CCTileMapLayer layer)
    {
        CCTileMapCoordinates tileAtXy = layer.ClosestTileCoordAtNodePosition (new CCPoint (worldX, worldY));

        CCTileGidAndFlags info = layer.TileGIDAndFlags (tileAtXy.Column, tileAtXy.Row);

        if (info != null)
        {
            Dictionary<string, string> properties = null;

            try
            {
                properties = tileMap.TilePropertiesForGID (info.Gid);
            }
            catch
            {
                // CocosSharp 
            }

            if (properties != null && properties.ContainsKey ("IsTreasure") && properties["IsTreasure"] == "true" )
            {
                layer.RemoveTile (tileAtXy);

                // todo: Create a treasure chest entity
            }
        }
    }
} 
```

La maggior parte del codice è facilmente comprensibile, ma dovrebbe essere illustra la gestione dei riquadri tesoro. In questo caso, verranno rimosse tutte le sezioni che vengono identificate come chests tesoro. Questo avviene perché tesoro chests sarà probabilmente necessario codice personalizzato in fase di esecuzione collisione di effetto di assegnare il contenuto di tesoro all'apertura il giocatore. Inoltre, potrebbe essere necessario il Tesoro di reagire allo stato aperto (modifica dell'aspetto visivo) e può avere la logica per solo visualizzazione tutti sullo schermo quando sono stati annullati nemici.

In altre parole, il dotazione tesoro trarranno beneficio da in corso di un'entità anziché un semplice riquadro nel `CCTileMap`. Per altre informazioni sulle entità del gioco, vedere la [guidano per le entità in CocosSharp](~/graphics-games/cocossharp/entities.md).


## <a name="summary"></a>Riepilogo

Questa procedura dettagliata illustra come caricare file .tmx creati da affiancato a un'applicazione di CocosSharp. Mostra come modificare la risoluzione di app per tenere conto delle immagini di pixel a bassa risoluzione e come trovare i riquadri per le relative proprietà per eseguire la logica personalizzata, come la creazione di istanze di entità.

## <a name="related-links"></a>Collegamenti correlati

- [Sito Web affiancato](http://www.mapeditor.org/)
- [File zip del contenuto](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)
