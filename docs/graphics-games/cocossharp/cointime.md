---
title: Dettagli di gioco ora moneta
description: Questa guida vengono illustrati i dettagli di implementazione del gioco moneta tempo, tra cui usano le mappe di riquadro, creazione di entità, animazione Sprite e implementazione di collisione efficiente.
ms.topic: article
ms.prod: xamarin
ms.assetid: 5D285684-0417-4E16-BD14-2D1F6DEFBB8B
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 8c33b74af80a14df1626ab39ba8c055a81259194
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2018
---
# <a name="coin-time-game-details"></a>Dettagli di gioco ora moneta

_Questa guida vengono illustrati i dettagli di implementazione del gioco moneta tempo, tra cui usano le mappe di riquadro, creazione di entità, animazione Sprite e implementazione di collisione efficiente._

Ora di moneta è un platformer completo giochi per iOS e Android. L'obiettivo del gioco è per raccogliere tutti monete in un livello e quindi raggiungere la porta di uscita evitando nemici e ostacoli.

![](cointime-images/image1.png "L'obiettivo del gioco consiste nel raccogliere tutte le monete in un livello e quindi a raggiungere la porta di uscita evitando nemici e ostacoli")

Questa guida vengono illustrati i dettagli di implementazione in fase di moneta relativa agli argomenti seguenti:

- [Utilizzo dei file tmx](#working-with-tmx-files)
- [Il caricamento del livello](#level-loading)
- [Aggiunta di nuove entità](#adding-new-entities)
- [Entità animata](#animated-entities)


## <a name="content-in-coin-time"></a>Contenuto in fase di moneta

Ora di moneta è un progetto di esempio che rappresenta l'organizzazione potrebbe essere un intero progetto CocosSharp. Coin-ora struttura mira a semplificare l'aggiunta e la manutenzione del contenuto. Usa **.tmx** file creati da [affiancato](http://www.mapeditor.org) per livelli e i file XML per definire le animazioni. Modifica o aggiunta di nuovo contenuto può essere ottenuto con il minimo sforzo. 

Sebbene questo approccio rende moneta ora un progetto efficace per l'apprendimento e sperimentazione, riflette inoltre come professional giochi vengono apportate. Questa guida illustra alcuni degli approcci per semplificare l'aggiunta e modifica del contenuto.


## <a name="working-with-tmx-files"></a>Utilizzo dei file tmx

Livelli di tempo moneta vengono definiti utilizzando il formato di file .tmx, verrà restituito dal [affiancato](http://www.mapeditor.org) tessera mappa editor. Per una descrizione dettagliata dell'utilizzo affiancato, vedere il [utilizzando affiancato con Guida Cocos acuto](~/graphics-games/cocossharp/tiled.md). 

Ogni livello è definito nel proprio file .tmx contenuti nel **CoinTime/attività/contenuto/livelli** cartella. Tutti i livelli di tempo moneta condividono un file tileset, definito nel **mastersheet.tsx** file. Questo file definisce le proprietà personalizzate per ogni riquadro, ad esempio se il riquadro ha una collisione a tinta unita o se il riquadro deve essere sostituito da un'istanza di entità. Il file mastersheet.tsx consente di definire una sola volta e utilizzata per tutti i livelli proprietà. 


### <a name="editing-a-tile-map"></a>Modifica di una tessera mappa

Per modificare una mappa di riquadro, aprire il file .tmx doppio clic sul file .tmx o l'apertura del file tramite il menu File in affiancato affiancato. Ora di moneta livello tessera mappe contengono tre livelli: 

- **Entità** : questo livello contiene i riquadri che verranno sostituiti con istanze di entità in fase di esecuzione. Sono esempi di Windows Media player, nemici e moderne, lo sportello di livello finale.
- **Terrain** : questo livello contiene i riquadri in cui sono in genere conflitto a tinta unita. Collisione a tinta unita consente il lettore per spostarti in questi riquadri senza passare. Con conflitti a tinta unita possono fungere anche pareti e limiti.
- **Sfondo** : il livello di sfondo contiene riquadri che vengono utilizzati come sfondo statico. Questo livello non scorre quando si sposta la fotocamera in tutto il livello, l'impressione di profondità tramite parallasse.

Come verranno analizzati in un secondo momento, il codice di caricamento del livello prevede che questi tre livelli in tutti i livelli di moneta ora.

#### <a name="editing-terrain"></a>Modifica di terreno

Riquadri possono essere inseriti facendo clic di **mastersheet** tileset e quindi fare clic sul riquadro mappa. Ad esempio, per disegnare terrain nuovo in un livello:

1. Selezionare il livello di terreno
1. Fare clic sul riquadro per disegnare
1. Fare clic o premere e trascinare sulla mappa per disegnare il riquadro

    ![](cointime-images/image2.png "Fare clic sul riquadro per disegnare 1")

In alto a sinistra del tileset contiene tutti i terrain moneta specifico. Terrain, ovvero a tinta unita, include il **SolidCollision** proprietà, come illustrato nelle proprietà del riquadro a sinistra dello schermo:

![](cointime-images/image3.png "Terrain, ovvero a tinta unita, include la proprietà SolidCollision, come illustrato nelle proprietà del riquadro a sinistra della schermata")

#### <a name="editing-entities"></a>La modifica delle entità

Le entità possono essere aggiunti o rimossi da un livello – come terreno. Il **mastersheet** tileset dispone di tutte le entità posizionate sulla metà in senso orizzontale, in modo che potrebbero non essere visibili senza scorrere verso destra:

![](cointime-images/image4.png "Il tileset mastersheet dispone di tutte le entità posizionate sulla metà in senso orizzontale, in modo che potrebbero non essere visibili senza scorrere verso destra")

Nuove entità devono essere posizionate nel **entità** livello.

![](cointime-images/image5.png "Nuove entità deve essere posizionata sul livello di entità")

Codice CoinTime viene cercato il **EntityType** quando un livello viene caricato per identificare i riquadri che devono essere sostituiti da entità: 

![](cointime-images/image6.png "Codice CoinTime Cerca l'elemento EntityType quando viene caricato un livello per identificare i riquadri che devono essere sostituiti da entità")

Una volta modificato e salvato il file, le modifiche verranno visualizzati automaticamente se il progetto viene compilato ed eseguire:

![](cointime-images/image7.png "Una volta modificato e salvato il file, le modifiche verranno visualizzati automaticamente se il progetto viene compilato ed eseguire")


### <a name="adding-new-levels"></a>Aggiunta di nuovi livelli

Il processo di aggiunta di livelli all'ora di moneta richiede alcuna modifica del codice e solo alcune piccole modifiche al progetto. Per aggiungere un nuovo livello:

1. Aprire la cartella di livello si trova in <**CoinTime Root > \CoinTime\Assets\Content\levels**
1. Copiare e incollare uno dei livelli, ad esempio **level0.tmx**
1. Rinominare il nuovo file .tmx in modo continua la sequenza di numero di livello con livelli esistenti, ad esempio **level8.tmx**
1. In Visual Studio o Visual Studio per Mac, aggiungere il nuovo file .tmx nella cartella di livelli di Android. Verificare che il file utilizza il **AndroidAsset** azione di compilazione.

    ![](cointime-images/image8.png "Verificare che il file utilizza l'azione di compilazione AndroidAsset")

1. Aggiungere il nuovo file .tmx alla cartella iOS livelli. Assicurarsi di collegare il file dal percorso originale e verificare che utilizzi il **BundleResource** azione di compilazione.

    ![](cointime-images/image9.png "Assicurarsi di collegare il file dal percorso originale e verificare che usa l'azione di compilazione BundleResource")

Il nuovo livello verrà visualizzata nella schermata di seleziona di livello come livello 9 (nomi di file a livello iniziano da 0, ma i pulsanti di livello iniziano con il numero 1):

![](cointime-images/image10.png "Il nuovo livello verrà visualizzata nella schermata di seleziona di livello come livello 9 avvio di nomi file di livello 0, ma i pulsanti di livello iniziano con il numero 1")


## <a name="level-loading"></a>Il caricamento del livello

Come illustrato in precedenza, nuovi livelli non richiedono alcuna modifica nel codice: il gioco rileva automaticamente i livelli se sono denominati correttamente e aggiungere il **livelli** cartella con l'azione di compilazione corretta (**BundleResource**o **AndroidAsset**).

Cui è contenuta la logica per determinare il numero di livelli di `LevelManager` classe. Quando un'istanza del `LevelManager` viene costruita (usando il modello singleton), il `DetermineAvailbleLevels` metodo viene chiamato:


```csharp
private void DetermineAvailableLevels()
{
    // This game relies on levels being named "levelx.tmx" where x is an integer beginning with
    // 1. We have to rely on MonoGame's TitleContainer which doesn't give us a GetFiles method - we simply
    // have to check if a file exists, and if we get an exception on the call then we know the file doesn't
    // exist. 
    NumberOfLevels = 0;
    while (true)
    {
        bool fileExists = false;
        try
        {
            using(var stream = TitleContainer.OpenStream("Content/levels/level" + NumberOfLevels + ".tmx"))
            {
            }
            // if we got here then the file exists!
            fileExists = true;
        }
        catch
        {
            // do nothing, fileExists will remain false
        }
        if (!fileExists)
        {
            break;
        }
        else
        {
            NumberOfLevels++;
        }
    }
}
```

CocosSharp non fornisce un approccio multipiattaforma per rilevare se i file sono presenti, quindi è necessario affidarsi la `TitleContainer` classe per tentare di aprire un flusso. Se il codice per l'apertura di un flusso genera un'eccezione, il file esiste e il ciclo while interruzioni. Una volta terminato il ciclo, il `NumberOfLevels` proprietà riporta il numero di livelli valido è parte del progetto.

Il `LevelSelectScene` utilizzato dalla classe di `LevelManager.NumberOfLevels` per determinare il numero di pulsanti per la creazione nel `CreateLevelButtons` metodo:


```csharp
private void CreateLevelButtons()
{
    const int buttonsPerPage = 6;
    int levelIndex0Based = buttonsPerPage * pageNumber;
    int maxLevelExclusive = System.Math.Min (levelIndex0Based + 6, LevelManager.Self.NumberOfLevels);
    int buttonIndex = 0;
    float centerX = this.ContentSize.Center.X;
    const float topRowOffsetFromCenter = 16;
    float topRowY = this.ContentSize.Center.Y + topRowOffsetFromCenter;
    for (int i = levelIndex0Based; i < maxLevelExclusive; i++)
    {
        ...
    }
}
```

Il `NumberOflevels` proprietà viene utilizzata per determinare quali pulsanti devono essere creati. Questo codice prende in considerazione la pagina, l'utente sta attualmente visualizzando e crea solo un massimo di sei pulsanti per pagina. Facendo clic sul pulsante istanze chiamata il `HandleButtonClicked` metodo:


```csharp
private void HandleButtonClicked(object sender, EventArgs args)
{
    // levelNumber is 1-based, so subtract 1:
    var levelIndex = (sender as Button).LevelNumber - 1;
    LevelManager.Self.CurrentLevel = levelIndex;
    CoinTime.GameAppDelegate.GoToGameScene ();
}
```

Questo metodo assegna il `CurrentLevel` proprietà che viene utilizzata il `GameScene` durante il caricamento di un livello. Dopo aver impostato il `CurrentLevel`, `GoToGameScene` metodo generato, cambio di scena da `LevelSelectScene` per `GameScene`.

Il `GameScene` chiamate al costruttore `GoToLevel`, che esegue la logica di caricamento di livello:


```csharp
private void GoToLevel(int levelNumber)
{
    LoadLevel (levelNumber);
    CreateCollision();
    ProcessTileProperties ();
    touchScreen = new TouchScreenInput(gameplayLayer);
    secondsLeft = secondsPerLevel;
}
```

Successivamente si verrà esaminato un metodi chiamati negli `GoToLevel`.


### <a name="loadlevel"></a>LoadLevel

Il `LoadLevel` metodo è responsabile per il caricamento del file .tmx e aggiungendolo al `GameScene`. Questo metodo non crea tutti gli oggetti interattivi, ad esempio conflitti o entità: semplicemente crea gli oggetti visivi per il livello, detta anche il *ambiente*.


```csharp
private void LoadLevel(int levelNumber)
{
    currentLevel = new CCTileMap ("level" + levelNumber + ".tmx");
    currentLevel.Antialiased = false;
    backgroundLayer = currentLevel.LayerNamed ("Background");
    // CCTileMap is a CCLayer, so we'll just add it under all entities
    this.AddChild (currentLevel);
    // put the game layer after
    this.RemoveChild(gameplayLayer);
    this.AddChild(gameplayLayer);
    this.RemoveChild (hudLayer);
    this.AddChild (hudLayer);
}
```

Il `CCTileMap` costruttore accetta un nome di file, viene creato utilizzando il numero del livello passato a `LoadLevel`. Per ulteriori informazioni sulla creazione e utilizzo di `CCTileMap` istanze, vedere il [utilizzando affiancato con CocosSharp Guida](~/graphics-games/cocossharp/tiled.md).

Attualmente, CocosSharp non consente il riordinamento dei livelli senza rimuovere e aggiungere nuovamente al padre `CCScene` (ovvero il `GameScene` in questo caso), pertanto sono necessarie le ultime righe del metodo per riordinare i livelli.


### <a name="createcollision"></a>CreateCollision

Il `CreateCollision` metodo costruisce una `LevelCollision` istanza che viene utilizzata per eseguire *collisione a tinta unita* tra il lettore e un ambiente.


```csharp
private void CreateCollision()
{
    levelCollision = new LevelCollision();
    levelCollision.PopulateFrom(currentLevel);
}
```

Senza questa collisione, il lettore rientrerebbero tramite il livello e il gioco potrebbe risultare non riproducibile. Collisione a tinta unita consente di scorrere su zero il lettore e impedisce che il lettore attraverso pareti o il passaggio di attraverso i limiti massimi.

È possibile aggiungere conflitti in fase di moneta senza codice aggiuntivo: solo le modifiche apportate ai file affiancati. 


### <a name="processtileproperties"></a>ProcessTileProperties

Una volta che viene caricato un livello e viene creato il conflitto, `ProcessTileProperties` viene chiamato per eseguire la logica in base alle proprietà di riquadro. Tempo moneta include un `PropertyLocation` struttura per la definizione di proprietà e le coordinate del riquadro con queste proprietà:


```csharp
public struct PropertyLocation
{
    public CCTileMapLayer Layer;
    public CCTileMapCoordinates TileCoordinates;
    public float WorldX;
    public float WorldY;
    public Dictionary<string, string> Properties;
}
```

Questo struct viene utilizzato per costruire le istanze di entità di creare e rimuovere i riquadri non necessari nel `ProcessTileProperties` metodo:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            float worldX = propertyLocation.WorldX;
            float worldY = propertyLocation.WorldY;
            if (properties.ContainsKey ("YOffset"))
            {
                string yOffsetAsString = properties ["YOffset"];
                float yOffset = 0;
                float.TryParse (yOffsetAsString, out yOffset);
                worldY += yOffset;
            }
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            if (created)
            {
                propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
            }
        }
        else if (properties.ContainsKey ("RemoveMe"))
        {
            propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
        }
    }
}
```

Il ciclo foreach restituisce ogni proprietà di sezione, verifica se la chiave `EntityType` o `RemoveMe`. `EntityType` indica che deve essere creata un'istanza di entità. `RemoveMe` indica che il riquadro deve essere rimosso completamente in fase di esecuzione.

Se una proprietà con il `EntityType` chiave viene trovata, quindi `TryCreateEntity` viene chiamato, quali tenta di creare un'entità mediante le proprietà corrispondenti di `EntityType` chiave:


```csharp
private bool TryCreateEntity(string entityType, float worldX, float worldY)
{
    CCNode entityAsNode = null;
    switch (entityType)
    {
    case "Player":
        player = new Player ();
        entityAsNode = player;
        break;
    case "Coin":
        Coin coin = new Coin ();
        entityAsNode = coin;
        coins.Add (coin);
        break;
    case "Door":
        door = new Door ();
        entityAsNode = door;
        break;
    case "Spikes":
        var spikes = new Spikes ();
        this.damageDealers.Add (spikes);
        entityAsNode = spikes;
        break;
    case "Enemy":
        var enemy = new Enemy ();
        this.damageDealers.Add (enemy);
        this.enemies.Add (enemy);
        entityAsNode = enemy;
        break;
    }
    if(entityAsNode != null)
    {
        entityAsNode.PositionX = worldX;
        entityAsNode.PositionY = worldY;
        gameplayLayer.AddChild (entityAsNode);
    }
    return entityAsNode != null;
}
```


## <a name="adding-new-entities"></a>Aggiunta di nuove entità

Tempo moneta utilizza il modello di entità per gli oggetti di giochi (illustrata nella [Guida le entità in CocosSharp](~/graphics-games/cocossharp/entities.md)). Tutte le entità di ereditano da `CCNode`, ovvero possono essere aggiunti come elementi figlio del `gameplayLayer`.

Ogni tipo di entità viene inoltre fare riferimento direttamente tramite un elenco o una singola istanza. Ad esempio, il `Player` fa riferimento il `player` campo e il `Coin` istanze vengono fatto riferimento in un `coins` elenco. Mantenere riferimenti diretti alle entità (anziché fare riferimento tramite il `gameLayer.Children` elenco) rende il codice che accede a queste entità più facile da leggere ed elimina il cast di tipo potenzialmente dispendiosa.

Il codice esistente fornisce una serie di tipi di entità come esempi su come creare nuove entità. Per creare una nuova entità, è possibile utilizzare la procedura seguente:


### <a name="1---define-a-new-class-using-the-entity-pattern"></a>1: definire una nuova classe utilizzando il modello di entità

L'unico requisito per la creazione di un'entità consiste nel creare una classe che eredita da `CCNode`. La maggior parte delle entità presentano alcuni visivo, ad esempio un `CCSprite`, che deve essere aggiunto come elemento figlio dell'entità nel relativo costruttore.

CoinTime fornisce la `AnimatedSpriteEntity` classe che semplifica la creazione di entità animata. Animazioni verranno descritta più dettagliatamente il [sezione animata entità](#animated-entities).


### <a name="2--add-a-new-entry-to-the-trycreateentity-switch-statement"></a>2: aggiungere una nuova voce per l'istruzione switch TryCreateEntity

Le istanze della nuova entità devono essere creata un'istanza nel `TryCreateEntity`. Se l'entità richiede una logica di ogni fotogramma come conflitto, AI o la lettura di input, il `GameScene` deve mantenere un riferimento all'oggetto. Se sono necessarie più istanze (ad esempio `Coin` o `Enemy` istanze), quindi un nuovo `List` devono essere aggiunti alla `GameScene` classe.


### <a name="3--modify-tile-properties-for-the-new-entity"></a>3: modificare le proprietà del riquadro per la nuova entità

Una volta il codice supporta la creazione della nuova entità, la nuova entità deve essere aggiunto il tileset. Il tileset può essere modificato tramite l'apertura di qualsiasi livello `.tmx` file. 

Il tileset è memorizzate separatamente dal .tmx nel **mastersheet.tsx** file, pertanto deve essere importato prima che possa essere modificato:

![](cointime-images/image11.png "Il tileset vengono archiviati separato dal file. TSX, pertanto deve essere importato prima che possa essere modificato")

Una volta importati, le proprietà nei riquadri selezionati sono modificabili, è possibile aggiungere l'elemento EntityType:

![](cointime-images/image12.png "Una volta importati, le proprietà nei riquadri selezionati sono modificabili e può essere aggiunti all'elemento EntityType")

Dopo aver creata la proprietà, il relativo valore può essere impostato corrisponda al nuovo `case` in `TryCreateEntity`:

![](cointime-images/image13.png "Dopo aver creata la proprietà, il relativo valore può essere impostato in modo che corrisponda al nuovo case in TryCreateEntity")

Dopo aver modificato il tileset, che deve essere esportato – in questo modo le modifiche disponibili per tutti gli altri livelli:

![](cointime-images/image14.png "Dopo aver modificato il tileset, deve essere esportato")

Deve sovrascrivere il tileset esistente **mastersheet.tsx** tileset:

![](cointime-images/image15.png "egli tileset deve sovrascrivere il tileset mastersheet.tsx esistente")


## <a name="entity-tile-removal"></a>Rimozione di riquadro entità

Quando si mappa un riquadro verrà caricata in un gioco, i singoli riquadri sono oggetti statici. Poiché le entità richiedono un comportamento personalizzato, ad esempio lo spostamento, codice in fase di moneta rimuove i riquadri quando vengono create le entità.

`ProcessTileProperties` include una logica per rimuovere i riquadri in cui creare le entità utilizzando il `RemoveTile` metodo:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            ...
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            if (created)
            {
                propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
            }
        }
        ...
    }
}
```

La rimozione automatica dei riquadri è sufficiente per le entità che occupano un solo riquadro nel tileset, ad esempio monete e nemici. Entità di dimensioni maggiori richiedono le proprietà e la logica aggiuntiva.

Lo sportello richiede due riquadri da disegnare completamente:

![](cointime-images/image16.png "Lo sportello richiede due riquadri da disegnare completamente")

Il riquadro inferiore in una porta contiene le proprietà per la creazione di un'entità (**EntityType** impostato su **porta**):

![](cointime-images/image17.png "Il riquadro inferiore in una porta contiene le proprietà per la creazione di un set di EntityType lo sportello di entità")

Quando viene creata l'istanza di porta, viene rimosso solo il riquadro inferiore in una porta, la logica aggiuntiva è necessaria per rimuovere il riquadro superiore in fase di esecuzione. Il riquadro superiore è un **RemoveMe** proprietà impostata su **true**:

![](cointime-images/image18.png "Il riquadro superiore è una proprietà RemoveMe impostata su true")



Questa proprietà viene utilizzata per rimuovere i riquadri in `ProcessTileProperties`:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        ...
        else if (properties.ContainsKey ("RemoveMe"))
        {
            propertyLocation.Layer.RemoveTile (propertyLocation.TileCoordinates);
        }
    }
}
```


## <a name="entity-offsets"></a>Offset di entità

Le entità create dai riquadri vengono posizionate allineando il centro dell'entità con il centro del riquadro. Le entità di dimensioni maggiori, come `Door`, utilizzare proprietà aggiuntive e la logica per essere inserito correttamente. 

Il riquadro inferiore porta, che definisce il `Door` la selezione di entità, specifica una **OffsetY** valore 4. Senza questa proprietà, il `Door` istanza viene posizionata al centro del riquadro:

![](cointime-images/image19.png "Senza questa proprietà, l'istanza di porta viene posizionato al centro del riquadro")

 

Questo è corretto applicando il **OffsetY** valore `ProcessTileProperties`:


```csharp
private void ProcessTileProperties()
{
    TileMapPropertyFinder finder = new TileMapPropertyFinder (currentLevel);
    foreach (var propertyLocation in finder.GetPropertyLocations())
    {
        var properties = propertyLocation.Properties;
        if (properties.ContainsKey ("EntityType"))
        {
            float worldX = propertyLocation.WorldX;
            float worldY = propertyLocation.WorldY;
            if (properties.ContainsKey ("YOffset"))
            {
                string yOffsetAsString = properties ["YOffset"];
                float yOffset = 0;
                float.TryParse (yOffsetAsString, out yOffset);
                worldY += yOffset;
            }
            bool created = TryCreateEntity (properties ["EntityType"], worldX, worldY);
            ...
        }
...
    }
}
```


## <a name="animated-entities"></a>Entità animata

Tempo di moneta include varie entità animata. Il `Player` e `Enemy` entità Riproduci animazioni di percorso e il `Door` entità svolge un'animazione di apertura dopo che sono stati raccolti monete tutti.


### <a name="achx-files"></a>file .achx

Le animazioni ora moneta sono definite nel file .achx. Ogni animazione viene definito tra `AnimationChain` tag, come illustrato nella seguente animazione definito in **propanimations.achx**:


```xml
<AnimationChain>
  <Name>Spikes</Name>
  <ColorKey>0</ColorKey>
  <Frame>
    <FlipHorizontal>false</FlipHorizontal>
    <FlipVertical>false</FlipVertical>
    <TextureName>..\images\mastersheet.png</TextureName>
    <FrameLength>0.1</FrameLength>
    <LeftCoordinate>1152</LeftCoordinate>
    <RightCoordinate>1168</RightCoordinate>
    <TopCoordinate>128</TopCoordinate>
    <BottomCoordinate>144</BottomCoordinate>
    <RelativeX>0</RelativeX>
    <RelativeY>0</RelativeY>
  </Frame>
</AnimationChain> 
```

Questa animazione contiene solo un singolo frame, determinando l'entità di picco la visualizzazione di un'immagine statica. Entità consente .achx file indipendentemente dal fatto che visualizzano le animazioni di unica o più frame. I frame aggiuntivi possono essere aggiunte ai file .achx senza richiedere alcuna modifica nel codice. 

Frame definiscono quale immagine da visualizzare il `TextureName` parametro e le coordinate dello schermo nel `LeftCoordinate`, `RightCoordinate`, `TopCoordinate`, e `BottomCoordinate` tag. Rappresentano le coordinate di pixel del frame di animazione dell'immagine di cui è in uso: **mastersheet.png** in questo caso.

![](cointime-images/image20.png "Rappresentano le coordinate di pixel del frame di animazione dell'immagine")

Il `FrameLength` proprietà definisce il numero di secondi che deve essere visualizzato un frame in un'animazione. Le animazioni di fotogrammi singoli ignorano questo valore.

Ignora tutte le altre proprietà AnimationChain nel file .achx moneta ora.


### <a name="animatedspriteentity"></a>AnimatedSpriteEntity

Animazione logica è contenuta nel `AnimatedSpriteEntity` (classe), che funge da classe base per la maggior parte delle entità utilizzato per il `GameScene`. Fornisce le funzionalità seguenti:

 - Il caricamento di `.achx` file
 - Cache di animazione delle animazioni caricate
 - Istanza di CCSprite per la visualizzazione dell'animazione
 - Logica per modificare il frame corrente

Il costruttore picchi fornisce un semplice esempio di come caricare e utilizzare le animazioni:


```csharp
public Spikes ()
{
    LoadAnimations ("Content/animations/propanimations.achx");
    CurrentAnimation = animations [0];
}
```

Il **propAnimations.achx** contiene solo un'animazione, pertanto il costruttore accede a questa animazione in base all'indice. Se un file .achx contiene più animazioni, quindi le animazioni è possibile fare riferimento in base al nome, come illustrato nel `Enemy` costruttore:


```csharp
walkLeftAnimation = animations.Find (item => item.Name == "WalkLeft");
walkRightAnimation = animations.Find (item => item.Name == "WalkRight");
```


## <a name="summary"></a>Riepilogo

Questa guida illustra i dettagli di implementazione di tempo moneta. Tempo moneta viene creata come un gioco completo, ma è anche un progetto che può essere facilmente modificato ed espanso. I lettori sono invitati a spesa modifiche della fase di esecuzione a livelli, aggiunta di nuovi livelli e la creazione di nuove entità per comprendere meglio come moneta è implementata.

## <a name="related-links"></a>Collegamenti correlati

- [Progetto di gioco (esempio)](https://developer.xamarin.com/samples/mobile/CoinTime/)
