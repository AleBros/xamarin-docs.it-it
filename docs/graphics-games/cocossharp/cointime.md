---
title: Dettagli di moneta della fase di gioco
description: Questa guida illustra i dettagli di implementazione in gioco moneta tempo, tra cui utilizzano le mappe di riquadro, la creazione delle entità, l'animazione Sprite e implementazione efficiente collisione.
ms.prod: xamarin
ms.assetid: 5D285684-0417-4E16-BD14-2D1F6DEFBB8B
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: af914e10eb93aa0668743a113ffe647a939fea75
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112624"
---
# <a name="coin-time-game-details"></a>Dettagli di moneta della fase di gioco

_Questa guida illustra i dettagli di implementazione in gioco moneta tempo, tra cui utilizzano le mappe di riquadro, la creazione delle entità, l'animazione Sprite e implementazione efficiente collisione._

Medaglia tempo è un gioco per iOS e Android di platformer completo. L'obiettivo del gioco è per raccogliere tutti i monete in un livello e quindi raggiungere la porta di uscita e si evitano nemici e ostacoli.

![](cointime-images/image1.png "L'obiettivo del gioco è per raccogliere tutti i monete in un livello e quindi raggiungere la porta di uscita e si evitano nemici e ostacoli")

Questa guida vengono illustrati i dettagli di implementazione in fase di moneta che copre gli argomenti seguenti:

- [Uso dei file tmx](#working-with-tmx-files)
- [Il livello di caricamento](#level-loading)
- [Aggiunta di nuove entità](#adding-new-entities)
- [Entità animata](#animated-entities)


## <a name="content-in-coin-time"></a>Contenuto in fase di moneta

Medaglia tempo è un progetto di esempio che rappresenta la modalità potrebbe essere organizzato in un progetto CocosSharp completo. Coin Time struttura mira a semplificare l'aggiunta e la manutenzione del contenuto. Viene utilizzato **.tmx** i file creati da [affiancato](http://www.mapeditor.org) per i livelli e i file XML per definire animazioni. Con il minimo sforzo, è possibile modificare o aggiungere nuovo contenuto. 

Sebbene questo approccio rende moneta ora un progetto efficace per apprendimento e sperimentazione, e riflette giochi professionali modo in cui vengono apportate. Questa guida illustra alcuni degli approcci adottati per semplificare l'aggiunta e modifica del contenuto.


## <a name="working-with-tmx-files"></a>Uso dei file tmx

Livelli temporali moneta vengono definiti utilizzando il formato di file .tmx, che verrà restituito dalle [affiancato](http://www.mapeditor.org) tessera mappa editor. Per una descrizione dettagliata dell'utilizzo affiancato, vedere la [uso di Tiled con Cocos Sharp Guida](~/graphics-games/cocossharp/tiled.md). 

Ogni livello è definito nel proprio file .tmx contenuti nel **CoinTime/risorse/contenuto/livelli** cartella. Tutti i livelli di tempo moneta condividono un file tileset, che viene definito nel **mastersheet.tsx** file. Questo file definisce le proprietà personalizzate per ogni sezione, ad esempio se il riquadro ha collisioni a tinta unita o se il riquadro deve essere sostituito da un'istanza di entità. Il file mastersheet.tsx consente proprietà definito una sola volta e usarla per tutti i livelli. 


### <a name="editing-a-tile-map"></a>Modifica di una tessera mappa

Per modificare una mappa di riquadro, aprire il file .tmx in affiancato facendo doppio clic il file .tmx oppure l'apertura del file tramite il menu File in affiancato. Coin Time livello tessere mappe contengono tre livelli: 

- **Le entità** : questo livello contiene i riquadri che verranno sostituiti con le istanze delle entità in fase di esecuzione. Sono esempi di Windows Media player, monete, nemici e lo sportello della fine-di-level.
- **Terreno** : questo livello contiene i riquadri che normalmente presentano conflitti a tinta unita. Collisione Solid consente di scorrere su questi riquadri senza passare. Riquadri con conflitto solid possono anche agire come pareti e parte intera superiore.
- **Sfondo** : il livello di sfondo contiene riquadri che vengono usati come lo sfondo statico. Questo livello non scorre quando si sposta la fotocamera in tutto il livello, creazione dell'aspetto di profondità tramite parallasse.

Come verrà presa in esame in un secondo momento, il codice a livello di caricamento si aspetta che questi tre livelli in tutti i livelli di tempo medaglia.

#### <a name="editing-terrain"></a>Modifica di terreno

I riquadri possono essere effettuati dal facendo clic nel **mastersheet** tileset e quindi fare clic sul riquadro della mappa. Ad esempio, per disegnare nuovi terreno in un livello:

1. Selezionare il livello di terreno
1. Fare clic sul riquadro per disegnare
1. Fare clic o effettuare il push e trascinare sulla mappa per disegnare il riquadro

    ![](cointime-images/image2.png "Fare clic sul riquadro per disegnare 1")

In alto a sinistra del tileset contiene tutti il terreno temporizzato medaglia. Territorio, che è solido, include il **SolidCollision** proprietà, come illustrato nelle proprietà del riquadro a sinistra della schermata:

![](cointime-images/image3.png "Territorio, che è solido, include la proprietà SolidCollision, come illustrato nelle proprietà del riquadro a sinistra della schermata")

#### <a name="editing-entities"></a>La modifica delle entità

Le entità possono essere aggiunti o rimossi da un livello – come terreno. Il **mastersheet** tileset è posizionate a metà orizzontalmente, tutte le entità in modo che potrebbero non essere visibili senza dover scorrere a destra:

![](cointime-images/image4.png "Il tileset mastersheet è posizionate a metà orizzontalmente, tutte le entità in modo che potrebbero non essere visibili senza dover scorrere a destra")

Nuove entità devono essere posizionate nel **entità** livello.

![](cointime-images/image5.png "Nuove entità deve essere posizionata nel livello di entità")

CoinTime codice cerca il **EntityType** quando viene caricato un livello per identificare i riquadri che devono essere sostituiti da entità: 

![](cointime-images/image6.png "Codice CoinTime Cerca l'elemento EntityType quando viene caricato un livello per identificare i riquadri che devono essere sostituiti da entità")

Dopo aver modificato e salvato il file, le modifiche verranno automaticamente visualizzata se il progetto viene compilato ed eseguire:

![](cointime-images/image7.png "Dopo che il file modificato e salvato, le modifiche verranno automaticamente visualizzate se il progetto viene compilato ed eseguire")


### <a name="adding-new-levels"></a>Aggiunta di nuovi livelli

Il processo di aggiunta livelli moneta ora richiede alcuna modifica al codice e solo alcune piccole modifiche al progetto. Per aggiungere un nuovo livello:

1. Aprire la cartella di livello disponibile nel <**CoinTime Root > \CoinTime\Assets\Content\levels**
1. Copiare e incollare uno dei livelli, ad esempio **level0.tmx**
1. Rinominare il nuovo file .tmx in modo da continuare la sequenza di numeri di livello con livelli esistenti, ad esempio **level8.tmx**
1. In Visual Studio o Visual Studio per Mac, aggiungere il nuovo file .tmx nella cartella di livelli di Android. Verificare che il file utilizza il **AndroidAsset** azione di compilazione.

    ![](cointime-images/image8.png "Verificare che il file Usa l'azione di compilazione AndroidAsset")

1. Aggiungere il nuovo file .tmx nella cartella di livelli di iOS. Assicurarsi di collegare il file dal percorso originale e verificare che usa il **BundleResource** azione di compilazione.

    ![](cointime-images/image9.png "Assicurarsi di collegare il file dal percorso originale e verificare che usa l'azione di compilazione BundleResource")

Il nuovo elemento level dovrebbe essere visualizzato nella schermata di selezione a livello come livello 9 (il livello di nomi di file iniziano da 0, ma i pulsanti di livello iniziano con il numero 1):

![](cointime-images/image10.png "Il nuovo elemento level dovrebbe essere visualizzato nella schermata di selezione a livello come livello 9 a livello di file i nomi partono da 0, ma i pulsanti di livello iniziano con il numero 1")


## <a name="level-loading"></a>Il livello di caricamento

Come illustrato in precedenza, i nuovi livelli non necessitano di modifiche nel codice: il gioco rileva automaticamente i livelli se sono corretti e aggiunto al **livelli** cartella con l'azione di compilazione corretto (**BundleResource**oppure **AndroidAsset**).

Cui è contenuta la logica per determinare il numero di livelli di `LevelManager` classe. Quando un'istanza del `LevelManager` viene costruito (usando il modello singleton), il `DetermineAvailbleLevels` viene chiamato il metodo:


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

CocosSharp non fornisce un approccio multipiattaforma per rilevare se i file sono presenti, quindi dobbiamo fare affidamento su di `TitleContainer` classe per tentare di aprire un flusso. Se il codice per l'apertura di un flusso genera un'eccezione, il file esiste e il ciclo while interruzioni. Al termine del ciclo, il `NumberOfLevels` proprietà segnala quanti livelli validi è parte del progetto.

Il `LevelSelectScene` classe Usa il `LevelManager.NumberOfLevels` per determinare il numero di pulsanti per creare nel `CreateLevelButtons` (metodo):


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

Il `NumberOflevels` proprietà viene utilizzata per determinare quali pulsanti devono essere creati. Questo codice considera quale pagina l'utente sta attualmente visualizzando e consente di creare solo un massimo di sei pulsanti per ogni pagina. Quando si fa clic, il pulsante istanze chiamata il `HandleButtonClicked` metodo:


```csharp
private void HandleButtonClicked(object sender, EventArgs args)
{
    // levelNumber is 1-based, so subtract 1:
    var levelIndex = (sender as Button).LevelNumber - 1;
    LevelManager.Self.CurrentLevel = levelIndex;
    CoinTime.GameAppDelegate.GoToGameScene ();
}
```

Questo metodo assegna il `CurrentLevel` proprietà che vengono usati dal `GameScene` durante il caricamento di un livello. Dopo aver impostato la `CurrentLevel`, il `GoToGameScene` metodo viene generato, cambio di scena `LevelSelectScene` a `GameScene`.

Il `GameScene` chiamate al costruttore `GoToLevel`, che consente di eseguire la logica a livello di caricamento:


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

Quindi prenderemo in esame metodi chiamati negli `GoToLevel`.


### <a name="loadlevel"></a>LoadLevel

Il `LoadLevel` metodo è responsabile per il caricamento del file .tmx e aggiungendolo al `GameScene`. Questo metodo non crea tutti gli oggetti interattivi, ad esempio collisione o entità, viene semplicemente creato gli oggetti visivi per il livello, noto anche come il *ambiente*.


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

Il `CCTileMap` costruttore accetta un nome di file, che viene creato tramite il numero del livello passato alla `LoadLevel`. Per altre informazioni sulla creazione e utilizzo con `CCTileMap` istanze, vedere la [uso di Tiled con CocosSharp Guida](~/graphics-games/cocossharp/tiled.md).

Attualmente, CocosSharp non consente il riordinamento dei livelli senza rimuoverla e aggiungerli nuovamente al relativo elemento padre `CCScene` (ovvero il `GameScene` in questo caso), in modo che le ultime righe del metodo sono necessari per riordinare i livelli.


### <a name="createcollision"></a>CreateCollision

Il `CreateCollision` metodo costruisce una `LevelCollision` istanza che viene usato per eseguire *collisione solid* tra l'ambiente e Windows Media player.


```csharp
private void CreateCollision()
{
    levelCollision = new LevelCollision();
    levelCollision.PopulateFrom(currentLevel);
}
```

Senza questa collisione, il giocatore rientrerebbero attraverso il livello e il gioco potrebbe risultare non riproducibile. Collisione Solid consente il giocatore scorrere su zero e impedisce che il lettore illustrando le pareti o il passaggio di attraverso limiti.

È possibile aggiungere dei conflitti nel tempo moneta senza codice aggiuntivo – solo le modifiche apportate ai file affiancati. 


### <a name="processtileproperties"></a>ProcessTileProperties

Una volta che viene caricato un livello e il conflitto viene creato, `ProcessTileProperties` viene chiamato per eseguire una logica basata su proprietà del riquadro. Medaglia ora include un `PropertyLocation` struct per la definizione di proprietà e le coordinate del riquadro con queste proprietà:


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

Questo struct viene usato per costruire istanze dell'entità di creare e rimuovere i riquadri non necessari nel `ProcessTileProperties` metodo:


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

Il ciclo foreach restituisce ogni proprietà di riquadro, controlla se la chiave è uno `EntityType` o `RemoveMe`. `EntityType` indica che è necessario creare un'istanza di entità. `RemoveMe` indica che il riquadro deve essere rimosso completamente in fase di esecuzione.

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

Medaglia ora Usa il modello di entità per gli oggetti giochi (illustrata nella [guidano per le entità in CocosSharp](~/graphics-games/cocossharp/entities.md)). Tutte le entità di ereditano `CCNode`, ovvero possono essere aggiunti come elementi figlio del `gameplayLayer`.

Ogni tipo di entità viene anche fatto riferimento direttamente tramite un elenco o una singola istanza. Ad esempio, il `Player` fa riferimento il `player` campo e tutte `Coin` istanze vengono fatto riferimento in un `coins` elenco. Mantenimento dei riferimenti diretti alle entità (anziché fare riferimento a essi tramite il `gameLayer.Children` elenco) rende il codice che accede a queste entità più facile da leggere ed elimina il cast dei tipi potenzialmente dispendiosa.

Il codice esistente fornisce una serie di tipi di entità come esempi su come creare nuove entità. La procedura seguente consente di creare una nuova entità:


### <a name="1---define-a-new-class-using-the-entity-pattern"></a>1: definire una nuova classe usando il modello di entità

L'unico requisito per la creazione di un'entità consiste nel creare una classe che eredita da `CCNode`. La maggior parte delle entità dispongono di un oggetto visivo, ad esempio un `CCSprite`, che deve essere aggiunto come elemento figlio dell'entità nel relativo costruttore.

CoinTime fornisce il `AnimatedSpriteEntity` classe che semplifica la creazione di entità animata. Animazioni verranno trattate più dettagliatamente la [sezione animato entità](#animated-entities).


### <a name="2--add-a-new-entry-to-the-trycreateentity-switch-statement"></a>2: aggiungere una nuova voce per l'istruzione switch TryCreateEntity

Le istanze della nuova entità devono essere inizializzate nel `TryCreateEntity`. Se l'entità richiede una logica di ogni frame come conflitto, intelligenza artificiale o la lettura di input, il `GameScene` deve mantenere un riferimento all'oggetto. Se sono necessarie più istanze (ad esempio `Coin` o `Enemy` istanze), quindi un nuovo `List` devono essere aggiunte al `GameScene` classe.


### <a name="3--modify-tile-properties-for-the-new-entity"></a>3: modificare le proprietà del riquadro per la nuova entità

Una volta il codice supporta la creazione della nuova entità, deve essere aggiunto al tileset la nuova entità. Il tileset possono essere modificate tramite l'apertura di qualsiasi livello `.tmx` file. 

Il tileset viene archiviato separato dal .tmx nel **mastersheet.tsx** file, in modo che non deve essere importato prima che possa essere modificato:

![](cointime-images/image11.png "Il tileset è archiviata separato dal file TSX, pertanto deve essere importato prima che possa essere modificato")

Una volta importati, le proprietà per i riquadri selezionati sono modificabili e l'elemento EntityType può essere aggiunto:

![](cointime-images/image12.png "Una volta importati, le proprietà per i riquadri selezionati sono modificabili e può essere aggiunto l'elemento EntityType")

Dopo aver creata la proprietà, il valore può essere impostato in modo che corrisponda il nuovo `case` in `TryCreateEntity`:

![](cointime-images/image13.png "Dopo aver creata la proprietà, il valore può essere impostato in modo che corrisponda al nuovo case in TryCreateEntity")

Dopo aver modificato il tileset, che deve essere esportato, che in questo modo le modifiche disponibili per tutti gli altri livelli:

![](cointime-images/image14.png "Dopo aver modificato il tileset, deve essere esportato")

Deve sovrascrivere il tileset esistente **mastersheet.tsx** tileset:

![](cointime-images/image15.png "he tileset deve sovrascrivere il tileset mastersheet.tsx esistente")


## <a name="entity-tile-removal"></a>Rimozione di entità riquadro

Quando una tessera mappa verrà caricata in un gioco, i singoli riquadri sono gli oggetti statici. Poiché le entità richiedono un comportamento personalizzato, ad esempio lo spostamento, codice in fase di moneta rimuove i riquadri quando vengono create le entità.

`ProcessTileProperties` include logica per rimuovere i riquadri che creare entità con la `RemoveTile` metodo:


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

La rimozione automatica dei riquadri è sufficiente per le entità che occupano un solo riquadro nel tileset, ad esempio monete e nemici. Entità di dimensioni maggiori richiedono logica aggiuntiva e proprietà.

Lo sportello richiede due riquadri da disegnare completamente:

![](cointime-images/image16.png "Lo sportello richiede due riquadri da disegnare completamente")

Il riquadro inferiore in una porta contiene le proprietà per la creazione di un'entità (**EntityType** impostata su **sportello**):

![](cointime-images/image17.png "Il riquadro inferiore in una porta contiene le proprietà per la creazione di una set di entità EntityType a sportello")

Poiché solo il riquadro inferiore in una porta viene rimosso quando viene creata l'istanza di porta, sarà necessaria logica aggiuntiva per rimuovere il riquadro superiore in fase di esecuzione. Il riquadro superiore è un **RemoveMe** impostata su **true**:

![](cointime-images/image18.png "Il riquadro superiore include una proprietà RemoveMe impostata su true")



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

Le entità create dai riquadri sono posizionate per allineare il centro dell'entità con il centro del riquadro. Entità di dimensioni maggiori, ad esempio `Door`, usare le proprietà aggiuntive e per la logica da inserire in modo corretto. 

Il riquadro di porta nella parte inferiore, che definisce il `Door` posizionamento di entità, specifica una **OffsetY** valore 4. Senza questa proprietà, il `Door` istanza viene posizionata al centro del riquadro:

![](cointime-images/image19.png "Senza questa proprietà, l'istanza di porta viene posizionato al centro del riquadro")

 

Ciò è corretto, applicando il **OffsetY** valore `ProcessTileProperties`:


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

Tempo moneta include varie entità animata. Il `Player` e `Enemy` entità riprodurre animazioni di percorso e il `Door` entità svolge un'animazione di apertura dopo che sono state raccolte monete tutti.


### <a name="achx-files"></a>file .achx

Medaglia ora animazioni definite nei file .achx. Ogni animazione viene definito tra `AnimationChain` tag, come illustrato nell'animazione seguente definito in **propanimations.achx**:


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

Questa animazione contiene solo un singolo fotogramma, causando la visualizzazione di un'immagine statica all'entità di picco. Le entità possono usare file .achx se vengano visualizzate le animazioni singola o a più frame. I frame aggiuntivi possono essere aggiunti ai file .achx senza richiedere alcuna modifica nel codice. 

Frame di definiscono l'immagine da visualizzare nella `TextureName` parametro e le coordinate dello schermo nel `LeftCoordinate`, `RightCoordinate`, `TopCoordinate`, e `BottomCoordinate` tag. Queste rappresentano le coordinate di pixel del frame di animazione nell'immagine che viene usato – **mastersheet.png** in questo caso.

![](cointime-images/image20.png "Queste rappresentano le coordinate di pixel del frame di animazione nell'immagine")

Il `FrameLength` proprietà definisce il numero di secondi che deve essere visualizzato un frame in un'animazione. Le animazioni con fotogrammi singolo ignorano questo valore.

Tutte le altre proprietà AnimationChain nel file .achx vengono ignorati dal tempo medaglia.


### <a name="animatedspriteentity"></a>AnimatedSpriteEntity

Logica dell'animazione è contenuta nel `AnimatedSpriteEntity` classe, che funge da classe base per la maggior parte delle entità nel `GameScene`. Fornisce le funzionalità seguenti:

 - Il caricamento di `.achx` file
 - Cache di animazione delle animazioni caricate
 - Istanza CCSprite per la visualizzazione dell'animazione
 - Per la logica per modificare il frame corrente

Il costruttore picchi offre un semplice esempio di come caricare e usare le animazioni:


```csharp
public Spikes ()
{
    LoadAnimations ("Content/animations/propanimations.achx");
    CurrentAnimation = animations [0];
}
```

Il **propAnimations.achx** contiene solo un'animazione, in modo che il costruttore accede a questa animazione in base all'indice. Se un file .achx contiene più animazioni, allora le animazioni possono farvi riferimento in base al nome, come illustrato nel `Enemy` costruttore:


```csharp
walkLeftAnimation = animations.Find (item => item.Name == "WalkLeft");
walkRightAnimation = animations.Find (item => item.Name == "WalkRight");
```


## <a name="summary"></a>Riepilogo

Questa guida illustra i dettagli di implementazione di tempo medaglia. Medaglia ora viene creato da un gioco completo, ma è anche un progetto che può essere facilmente modificato ed espanso. I lettori sono invitati a dedicare apportate modifiche di tempo a livelli, aggiunta di nuovi livelli e la creazione di nuove entità per comprendere a fondo la modalità di implementazione ora medaglia.

## <a name="related-links"></a>Collegamenti correlati

- [Progetto di gioco (esempio)](https://developer.xamarin.com/samples/mobile/CoinTime/)
