---
title: Utilizzo affiancato con CocosSharp
description: Affiancato è una potente, flessibile e applicazione obsoleta per la creazione di riquadro ortogonale e l'effetto viene eseguito il mapping per i giochi. CocosSharp fornisce integrazione incorporata per il formato di file nativi dell'affiancato.
ms.prod: xamarin
ms.assetid: 804C042C-F62A-4E6C-B10F-06528637F0E2
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 8a7782097324829b8150b968c5658a864d1fab4a
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921300"
---
# <a name="using-tiled-with-cocossharp"></a>Utilizzo affiancato con CocosSharp

_Affiancato è una potente, flessibile e applicazione obsoleta per la creazione di riquadro ortogonale e l'effetto viene eseguito il mapping per i giochi. CocosSharp fornisce integrazione incorporata per il formato di file nativi dell'affiancato._

Il *affiancato* applicazione è uno standard per la creazione di *riquadro mappe* per l'utilizzo nello sviluppo di gioco. Questa Guida verrà descritto come creare un file .tmx esistente (file creati da affiancato) e usarla in un gioco CocosSharp. In particolare, questa guida illustra:

 - Lo scopo delle mappe di riquadro
 - Utilizzo dei file .tmx
 - Considerazioni per il rendering di immagini di pixel
 - Utilizzando le proprietà del riquadro in fase di esecuzione

Quando termine dovremo demo seguente:

![](tiled-images/image1.png "L'app demo creato seguendo i passaggi descritti in questa Guida")


## <a name="the-purpose-of-tile-maps"></a>Lo scopo delle mappe di riquadro

Riquadro mappe esistito nello sviluppo di gioco per decenni, ma sono ancora utilizzate nei giochi 2D per l'efficacia ed esthetics. Riquadro mappe in grado di ottenere un elevato livello di efficienza tramite l'utilizzo di set di tessere: l'immagine di origine utilizzata dal riquadro mappe. Un set di tessere è una raccolta di immagini combinati in un file. Anche se i set di tessere fanno riferimento a immagini utilizzate nelle mappe di riquadro, i file che contengono più immagini di piccole dimensioni sono detti anche fogli sprite o sprite mapping nello sviluppo di gioco. È possibile visualizzare come set di tessere vengono utilizzati tramite l'aggiunta di una griglia al set di riquadro che verrà usato nella demo:

![](tiled-images/image2.png "Una vista possono essere visualizzata di utilizzo riquadro set mediante l'aggiunta di che una griglia nel riquadro del set che verrà usata nella demo")

Riquadro mappe disporre i singoli riquadri da set di sezioni. Si noti che ogni mappa riquadro non è necessario archiviare la propria copia del riquadro impostare – invece più mappe riquadro possono fare riferimento lo stesso set di sezioni. Ciò significa che oltre il set di tessere mappe di riquadro richiedono poca memoria. In questo modo la creazione di un numero elevato di mappe di riquadro, anche quando vengono utilizzati per creare un'area di gioco di grandi dimensioni, ad esempio un [scorrimento platformer](http://en.wikipedia.org/wiki/Platform_game) ambiente. Di seguito viene illustrato possibili disposizioni usando lo stesso set di sezioni:

![](tiled-images/image3.png "La seguente immagine illustra possibili disposizioni usando lo stesso set di sezioni")

![](tiled-images/image4.png "La seguente immagine illustra possibili disposizioni usando lo stesso set di sezioni")


## <a name="working-with-tmx-files"></a>Utilizzo dei file .tmx

Il formato di file .tmx è un file XML creato dall'applicazione affiancato, che può essere [scaricati gratuitamente nel sito Web affiancato](http://www.mapeditor.org/). Il formato del file .tmx archivia le informazioni per le mappe di riquadro. In genere un gioco avrà un file .tmx per ogni area separata o a un livello.

Questa guida viene illustrato come utilizzare i file esistenti .tmx in CocosSharp; Tuttavia, altre esercitazioni sono disponibili online, incluso [questa introduzione per l'editor di mappe affiancato](http://gamedevelopment.tutsplus.com/tutorials/introduction-to-tiled-map-editor--gamedev-2838).

È necessario decomprimere il [file zip contenuto](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true) per utilizzarlo nel gioco. La prima cosa da notare è che mappe riquadro utilizzano sia il file .tmx (dungeon.tmx) e uno o più file di immagine che definiscono il riquadro dati (dungeon_1.png). Il gioco deve includere entrambi i file per caricare il .tmx in fase di esecuzione, aggiungere in modo sia per il progetto **contenuto** cartella (che è contenuto nel **asset** cartella nei progetti Android). In particolare, aggiungere i file in una cartella denominata **tilemaps** all'interno di **contenuto** cartella:

![](tiled-images/image5.png "Aggiungere i file in una cartella denominata tilemaps all'interno della cartella del contenuto")

Il **dungeon.tmx** ora è possibile caricare file in fase di esecuzione in un `CCTileMap` oggetto. Successivamente, modificare il `GameLayer` (o un oggetto contenitore radice equivalente) per contenere un `CCTileMap` istanza e per aggiungerlo come elemento figlio:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");
        this.AddChild (tileMap);
    }
} 
```

Se Esegui il gioco vedremo tessera mappa vengono visualizzati nell'angolo inferiore sinistro dello schermo:

![](tiled-images/image6.png "Se viene eseguito il gioco, la mappa del riquadro vengono visualizzati nell'angolo inferiore sinistro dello schermo")


## <a name="considerations-for-rendering-pixel-art"></a>Considerazioni per il rendering di immagini di pixel

Immagine in pixel, nel contesto di sviluppo di gioco, si intende 2D arte visiva che è in genere creati da mano e spesso a bassa risoluzione. ClipArt pixel può essere correggono tempi lunghi per creare, quindi pixel grafica riquadro set includono spesso a bassa risoluzione riquadri, ad esempio 16 o 32 larghezza in pixel e l'altezza. Se non è ridimensionato in fase di esecuzione, arte pixel spesso è troppo piccolo per più moderni telefoni e Tablet.

È possibile regolare le dimensioni visualizzate nel file GameAppDelegate.cs nostri del gioco, in cui verrà aggiunto una chiamata a `CCScene.SetDefaultDesignResolution`:


```csharp
 public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";
    application.ContentSearchPaths.Add ("animations");
    application.ContentSearchPaths.Add ("fonts");
    application.ContentSearchPaths.Add ("sounds");

    CCSize windowSize = mainWindow.WindowSizeInPixels;

    float desiredWidth = 1024.0f;
    float desiredHeight = 768.0f;
    
    // This will set the world bounds to be (0,0, w, h)
    // CCSceneResolutionPolicy.ShowAll will ensure that the aspect ratio is preserved
    CCScene.SetDefaultDesignResolution (desiredWidth, desiredHeight, CCSceneResolutionPolicy.ShowAll);
    
    // Determine whether to use the high or low def versions of our images
    // Make sure the default texel to content size ratio is set correctly
    // Of course you're free to have a finer set of image resolutions e.g (ld, hd, super-hd)
    if (desiredWidth < windowSize.Width)
    {
        application.ContentSearchPaths.Add ("images/hd");
        CCSprite.DefaultTexelToContentSizeRatio = 2.0f;
    }
    else
    {
        application.ContentSearchPaths.Add ("images/ld");
        CCSprite.DefaultTexelToContentSizeRatio = 1.0f;
    }

    // New code:
    CCScene.SetDefaultDesignResolution (380, 240, CCSceneResolutionPolicy.ShowAll);

    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);

    mainWindow.RunWithScene (scene);
} 
```

Per ulteriori informazioni su `CCSceneResolutionPolicy`, vedere la Guida su [gestisce procedure di risoluzione nel CocosSharp](~/graphics-games/cocossharp/resolutions.md).

Se si esegue ora il gioco, vedremo il gioco occupato l'intero schermo nel dispositivo:

![](tiled-images/image7.png "Il gioco occupato lo schermo del dispositivo")

È opportuno infine disabilitare anti-aliasing nella mappa riquadro. Il `Antialiased` proprietà si applica un sfocatura effetto durante il rendering di oggetti che vengono ingranditi. Anti-aliasing può essere utile per ridurre l'aspetto di livello degli oggetti di grafici, ma può anche causare propri elementi di rendering. Anti-aliasing applica una sfocatura in particolare, il contenuto di ogni sezione. Tuttavia, i bordi di ogni riquadro non sfocatura, che rende i singoli riquadri risalto anziché sfumatura con riquadri adiacenti. Si noti inoltre che giochi locandina pixel spesso mantenere il loro aspetto di livello per mantenere un *retro* ritiene.

Impostare `Antialiased` a `false` dopo la creazione di `tileMap`:


```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();

    tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

    // new code:
    tileMap.Antialiased = false;

    this.AddChild (tileMap);
} 
```

A questo punto la mappa del riquadro non verrà visualizzato sfocata:

![](tiled-images/image8.png "Tessera mappa non verrà ora visualizzato sfocata")


## <a name="using-tile-properties-at-runtime"></a>Utilizzando le proprietà del riquadro in fase di esecuzione

Finora abbiamo un `CCTileMap` durante il caricamento di un file .tmx e visualizzarlo, ma non esiste alcun modo di interagire con essa. Alcuni riquadri (ad esempio, il corpo fissato irrilevanti) in particolare, è necessario disporre di logica personalizzata. Passeremo come rilevare le proprietà del riquadro personalizzato e i vari modi per rispondere a queste proprietà di una volta identificate in fase di esecuzione.

Prima è scrivere alcun codice, è necessario aggiungere le proprietà per la mappa riquadro tramite affiancato. A tale scopo, aprire il file dungeon.tmx nel programma affiancato. Assicurarsi di aprire il file che viene utilizzato nel progetto di gioco.

Una volta aperto, selezionare il corpo fissato irrilevanti nel riquadro set per visualizzarne le proprietà:

![](tiled-images/image9.png "Una volta aperto, selezionare il corpo fissato irrilevanti nel riquadro set per visualizzarne le proprietà")

Se non sono visualizzate le proprietà del corpo fissato irrilevanti, fare clic sul corpo fissato irrilevanti e selezionare **le proprietà del riquadro**:

![](tiled-images/image10.png "Se non sono visualizzate le proprietà del corpo fissato irrilevanti, fare clic sul corpo fissato irrilevanti e selezionare le proprietà del riquadro")

Proprietà affiancata vengono implementate con un nome e un valore. Per aggiungere una proprietà, fare clic su di **+** pulsante, immettere il nome **IsTreasure**, fare clic su **OK**, quindi immettere il valore **true**: 

![](tiled-images/image11.png "Per aggiungere una proprietà, fare clic sul pulsante, immettere il nome IsTreasure fare clic su OK, quindi immettere il valore true")

Non dimenticare di salvare il file .tmx dopo la modifica delle proprietà.

Infine, verrà aggiunto codice per cercare la proprietà appena aggiunti. Si esegue il ciclo ogni `CCTileMapLayer` (la mappa include 2 livelli), quindi tramite ogni riga e colonna per cercare qualsiasi riquadri che hanno il `IsTreasure` proprietà:


```csharp
public class GameLayer : CCLayer
{
    CCTileMap tileMap;

    public GameLayer ()
    {
    }

    protected override void AddedToScene ()
    {
        base.AddedToScene ();

        tileMap = new CCTileMap ("tilemaps/dungeon.tmx");

        // new code:
        tileMap.Antialiased = false;

        this.AddChild (tileMap);

        HandleCustomTileProperties (tileMap);
    }

    void HandleCustomTileProperties(CCTileMap tileMap)
    {
        // Width and Height are equal so we can use either
        int tileDimension = (int)tileMap.TileTexelSize.Width;

        // Find out how many rows and columns are in our tile map
        int numberOfColumns = (int)tileMap.MapDimensions.Size.Width;
        int numberOfRows = (int)tileMap.MapDimensions.Size.Height;

        // Tile maps can have multiple layers, so let's loop through all of them:
        foreach (CCTileMapLayer layer in tileMap.TileLayersContainer.Children)
        {
            // Loop through the columns and rows to find all tiles
            for (int column = 0; column < numberOfColumns; column++)
            {
                // We're going to add tileDimension / 2 to get the position
                // of the center of the tile - this will help us in 
                // positioning entities, and will eliminate the possibility
                // of floating point error when calculating the nearest tile:
                int worldX = tileDimension * column + tileDimension / 2;
                for (int row = 0; row < numberOfRows; row++)
                {
                    // See above on why we add tileDimension / 2
                    int worldY = tileDimension * row + tileDimension / 2;

                    HandleCustomTilePropertyAt (worldX, worldY, layer);
                }
            }
        }
    }

    void HandleCustomTilePropertyAt(int worldX, int worldY, CCTileMapLayer layer)
    {
        CCTileMapCoordinates tileAtXy = layer.ClosestTileCoordAtNodePosition (new CCPoint (worldX, worldY));

        CCTileGidAndFlags info = layer.TileGIDAndFlags (tileAtXy.Column, tileAtXy.Row);

        if (info != null)
        {
            Dictionary<string, string> properties = null;

            try
            {
                properties = tileMap.TilePropertiesForGID (info.Gid);
            }
            catch
            {
                // CocosSharp 
            }

            if (properties != null && properties.ContainsKey ("IsTreasure") && properties["IsTreasure"] == "true" )
            {
                layer.RemoveTile (tileAtXy);

                // todo: Create a treasure chest entity
            }
        }
    }
} 
```

La maggior parte del codice è chiara interpretazione, ma dovrebbe prende in esame la gestione dei riquadri irrilevanti. In questo caso verranno rimosse tutte le tessere identificati come chests irrilevanti. Questo avviene perché irrilevanti chests sarà probabilmente necessario codice personalizzato in fase di esecuzione a conflitti di effetto e assegnare il contenuto di irrilevanti all'apertura di Windows Media player. Inoltre, l'irrilevanti potrebbe essere necessario rispondere a essere aperto (modifica l'aspetto visivo) e potrebbe prevedere la logica per solo visualizzazione quando tutte le nemici stati annullati.

In altre parole, il corpo fissato irrilevanti beneficiare di un'entità viene anziché un semplice riquadro nel `CCTileMap`. Per ulteriori informazioni sulle entità di gioco, vedere il [Guida le entità in CocosSharp](~/graphics-games/cocossharp/entities.md).


## <a name="summary"></a>Riepilogo

Questa procedura dettagliata illustra come caricare i file di .tmx creati da affiancato in un'applicazione CocosSharp. Viene illustrato come modificare la risoluzione di app per tenere conto delle immagini a bassa risoluzione pixel e come individuare i riquadri per le relative proprietà per eseguire la logica personalizzata, quali la creazione di istanze di entità.

## <a name="related-links"></a>Collegamenti correlati

- [Sito Web affiancato](http://www.mapeditor.org/)
- [Contenuto zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Tiled.zip?raw=true)
