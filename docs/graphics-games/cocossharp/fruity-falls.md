---
title: Dettagli di gioco cade Fruity
description: Questa guida esamina il gioco, cade Fruity relativi CocosSharp comuni e ai concetti di sviluppo di giochi, ad esempio fisica, gestione del contenuto, lo stato del gioco e progettazione di gioco.
ms.prod: xamarin
ms.assetid: A5664930-F9F0-4A08-965D-26EF266FED24
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 2138e27c1097e014f302cc0b9de0fa411bed89fc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="fruity-falls-game-details"></a>Dettagli di gioco cade Fruity

_Questa guida esamina il gioco, cade Fruity relativi CocosSharp comuni e ai concetti di sviluppo di giochi, ad esempio fisica, gestione del contenuto, lo stato del gioco e progettazione di gioco._

Fruity fallback è un gioco semplice basata sul fisica, in cui il giocatore Ordina frutta giallo e rosso in bucket colorata di guadagnare punti. L'obiettivo del gioco è guadagnare più punti possibile evitando un calo di frutta in bin errato, terminare il gioco.

![](fruity-falls-images/image1.png "L'obiettivo del gioco è guadagnare più punti possibile evitando un calo di frutta in bin errato, terminare il gioco")

Rientra Fruity estende i concetti introdotti nel [BouncingGame Guida](~/graphics-games/cocossharp/bouncing-game.md) aggiungendo quanto segue:

 - Contenuto sotto forma di file PNG
 - Fisica avanzata
 - Stato del gioco (transizione tra le scene)
 - Possibilità di ottimizzare i coefficienti di giochi tramite le variabili contenute in una classe singola
 - Supporto di debug visual incorporato
 - Organizzazione di codice usando le entità di gioco
 - Progettazione di gioco attente divertente e riproduzione

Mentre il [BouncingGame Guida](~/graphics-games/cocossharp/bouncing-game.md) illustri introducono concetti CocosSharp core, cade Fruity viene illustrato come attivare la modalità tutti insieme in un prodotto finito di gioco. Poiché questa guida fa riferimento il BouncingGame, lettori devono prima acquisire familiarità con la [BouncingGame Guida](~/graphics-games/cocossharp/bouncing-game.md) prima della lettura di questa Guida.

Questa guida illustra l'implementazione e la progettazione di Fruity scende a forniscono informazioni dettagliate che consentono di rendere un gioco di. Vengono trattati gli argomenti seguenti:


- [Classe GameController](#gamecontroller-class)
- [Entità di gioco](#game-entities)
- [Grafica di frutta](#fruit-graphics)
- [Fisica](#physics)
- [Gioco contenuto](#game-content)
- [GameCoefficients](#gamecoefficients)


## <a name="gamecontroller-class"></a>Classe GameController

Il progetto PCL rientra Fruity include un `GameController` classe responsabile per la creazione di un'istanza del gioco e lo spostamento tra le scene. Questa classe è utilizzata da entrambi i progetti Android e iOS per eliminare il codice duplicato.

Il codice contenuto all'interno di `Initialize` è simile al codice nel metodo il`Initialize` metodo in un modello CocosSharp invariato, ma contiene un numero di modifiche.

Per impostazione predefinita, il `GameView.ContentManager.SearchPaths` dipende dalla risoluzione del dispositivo. Come illustrato di seguito in modo più dettagliato, cade Fruity utilizza lo stesso contenuto indipendentemente dalla risoluzione di dispositivo, pertanto la `Initialize` metodo aggiunge il `Images` percorso (con nessuna sottocartella) il `SearchPaths`:


```csharp
contentSearchPaths.Add ("Images");
```

Nuovi modelli CocosSharp includono una classe che eredita da `CCLayer`, per indicare che gli oggetti visivi di giochi e logica devono essere aggiunti a questa classe. Rientra Fruity utilizza invece più `CCLayer` ordine di disegno istanze al controllo. Questi `CCLayer` le istanze sono contenute all'interno di `GameView` (classe), che eredita da `CCScene`. Rientra Fruity include anche le scene più, il primo dei quali è il `TitleScene`. Pertanto, il `Initialize` crea un'istanza di metodo un `TitleScene` istanza che viene passato a `RunWithScene`:


```csharp
var scene = new TitleScene (GameView);
GameView.Director.RunWithScene (scene);
```

Il contenuto di fallback Fruity è stato creato come immagine a bassa risoluzione pixel per migliorare. Il `GameView.DesignResolution` è impostato in modo che il gioco è solo di 384 unità di larghezza e altezza 512:


```csharp
// We use a lower-resolution display to get a pixellated appearance
int width = 384;
int height = 512;
GameView.DesignResolution = new CCSizeI (width, height); 
```

Infine, il `GameController` classe fornisce un metodo statico che può essere chiamato da qualsiasi `CCGameScene` per eseguire la transizione a un altro `CCScene`. Questo metodo viene utilizzato per lo spostamento tra le `TitleScene` e `GameScene`.


## <a name="game-entities"></a>Entità di gioco

Rientra Fruity si avvalgono del modello di entità per la maggior parte degli oggetti del gioco. Una spiegazione dettagliata di questo modello, vedere il [Guida le entità in CocosSharp](~/graphics-games/cocossharp/entities.md).

Gli oggetti di giochi di implementazione di entità sono reperibile nella cartella delle entità di **FruityFalls.Common** progetto:

![](fruity-falls-images/image2.png "La cartella di entità nel progetto FruityFalls.Common")

Le entità sono oggetti che ereditano da `CCNode`e può contenere oggetti visivi, conflitti e il comportamento di ogni fotogramma.

Il `Fruit` oggetto rappresenta un'entità CocosSharp tipico: contiene un oggetto visivo, conflitti e logica di ogni fotogramma. Il costruttore è responsabile per l'inizializzazione del frutto:


```csharp
public Fruit ()
{
    CreateFruitGraphic ();
    if (GameCoefficients.ShowCollisionAreas)
    {
        CreateDebugGraphic ();
    }
    CreateCollision ();
    CreateExtraPointsLabel ();
    Acceleration.Y = GameCoefficients.FruitGravity;
}
```


### <a name="fruit-graphics"></a>Grafica di frutta

Il `CreateFruitGraphic` metodo crea un `CCSprite` istanza e lo aggiunge al `Fruit`. Il `IsAntialiased` proprietà è impostata su false per conferire un aspetto di livello di gioco. Questo valore è impostato su false in tutti `CCSprite` e `CCLabel` istanze in tutto il gioco:


```csharp
private void CreateFruitGraphic()
{
    graphic = new CCSprite ("cherry.png");
    graphic.IsAntialiased = false;
    this.AddChild (graphic);
}
```

Ogni volta che il lettore tocca un `Fruit` istanza con il `Paddle`, il valore del punto di tale frutta incrementa di uno. Viene fornita una richiesta di verifica aggiuntiva al lettori esperti all'esecuzione di frutta per altri punti. Il valore del punto del frutto viene visualizzato utilizzando il `extraPointsLabel` istanza.

Il `CreateExtraPointsLabel` metodo crea un `CCLabel` istanza e lo aggiunge al `Fruit`:


```csharp
private void CreateExtraPointsLabel()
{
    extraPointsLabel = new CCLabel("", "Arial", 12, CCLabelFormat.SystemFont);
    extraPointsLabel.IsAntialiased = false;
    extraPointsLabel.Color = CCColor3B.Black;
    this.AddChild(extraPointsLabel);
}
```

Cade Fruity include due diversi tipi di frutta: ciliegie e limoni. Il `CreateFruitGraphic` assegna un oggetto visivo predefinito, ma gli oggetti visivi frutta possono essere modificato tramite il `FruitColor` proprietà, che a sua volta chiama `UpdateGraphics`:


```csharp
private void UpdateGraphics()
{
if (GameCoefficients.ShowCollisionAreas)
    {
        debugGrahic.Clear ();
        const float borderWidth = 4;
        debugGrahic.DrawSolidCircle (
            CCPoint.Zero,
            GameCoefficients.FruitRadius,
            CCColor4B.Black);
        debugGrahic.DrawSolidCircle (
            CCPoint.Zero,
            GameCoefficients.FruitRadius - borderWidth,
            fruitColor.ToCCColor ());
    }
    if (this.FruitColor == FruitColor.Yellow)
    {
        graphic.Texture = CCTextureCache.SharedTextureCache.AddImage ("lemon.png");
        extraPointsLabel.Color = CCColor3B.Black;
        extraPointsLabel.PositionY = 0;
    }
    else
    {
        graphic.Texture = CCTextureCache.SharedTextureCache.AddImage ("cherry.png");
        extraPointsLabel.Color = CCColor3B.White;
        extraPointsLabel.PositionY = -8;
    }
}
```

La prima istruzione if in `UpdateGraphics` Aggiorna la grafica di debug, che viene usata per visualizzare le aree di conflitto. Gli oggetti visivi sono disattivati prima di una versione finale del gioco viene effettuata, ma può essere incluso in durante lo sviluppo per il debug di fisica. Le modifiche parte secondo il `graphic.Texture` proprietà chiamando `CCTextureCache.SharedTextureCache.AddImage`. Questo metodo fornisce l'accesso a una trama in base al nome di file. Ulteriori informazioni su questo metodo, vedere il [Guida per la memorizzazione nella cache di trama](~/graphics-games/cocossharp/texture-cache.md).

Il `extraPointsLabel` colore viene regolato in modo da mantenere contrasto con l'immagine di frutta e il relativo `PositionY` valore viene adattato al centro il `CCLabel` sul frutto `CCSprite`:

![](fruity-falls-images/image3.png "Il colore extraPointsLabel viene regolato per mantenere il contrasto con l'immagine di frutta, e il relativo valore PositionY viene regolato center CCLabel su frutta CCSprite")

![](fruity-falls-images/image4.png "Il colore extraPointsLabel viene regolato per mantenere il contrasto con l'immagine di frutta, e il relativo valore PositionY viene regolato center CCLabel su frutta CCSprite")


### <a name="collision"></a>Conflitto

Rientra Fruity implementa una soluzione di collisione personalizzata utilizzando oggetti nella cartella della geometria:

![](fruity-falls-images/image5.png "Rientra Fruity implementa una soluzione di collisione personalizzata utilizzando oggetti nella cartella della geometria")

Conflitto di cade Fruity viene implementato utilizzando il `Circle` o `Polygon` oggetto, in genere con uno di questi due tipi da aggiungere come figlio di un'entità. Ad esempio, il `Fruit` oggetto ha un `Circle` chiamato `Collision` che viene inizializzata nel relativo `CreateCollision` (metodo):


```csharp
private void CreateCollision()
{
    Collision = new Circle ();
    Collision.Radius = GameCoefficients.FruitRadius;
    this.AddChild (Collision);
}
```

Si noti che il `Circle` classe eredita la `CCNode` classe può essere aggiunto come elemento figlio alle entità del nostro gioco:


```csharp
public class Circle : CCNode
{
    ...
}
```

`Polygon` è simile a creazione `Circle` creazione, come illustrato nella `Paddle` classe:


```csharp
private void CreateCollision()
{
    Polygon = Polygon.CreateRectangle(width, height);
    this.AddChild (Polygon);
}
```

Viene descritta la logica di collisione [più avanti in questa Guida](#collision).


## <a name="physics"></a>Fisica

Fisica in cade Fruity può essere suddivisa in due categorie: spostamento e la collisione. 


### <a name="movement-using-velocity-and-acceleration"></a>Spostamento con velocità e l'accelerazione

Usa cade Fruity `Velocity` e `Acceleration` i valori per controllare lo spostamento delle entità, simile al [BouncingGame](~/graphics-games/cocossharp/bouncing-game.md). Entità di implementano la logica di spostamento in un metodo denominato `Activity`, che viene chiamato una volta per fotogramma. Ad esempio, si noterà che l'implementazione di spostamento nel `Fruit` classe `Activity` metodo:

```csharp
public void Activity(float frameTimeInSeconds)
{
    timeUntilExtraPointsCanBeAdded -= frameTimeInSeconds;
    
    // linear approximation:
    this.Velocity += Acceleration * frameTimeInSeconds;

    // This is a linear approximation to drag. It's used to
    // keep the object from falling too fast, and eventually
    // to slow its horizontal movement. This makes the game easier
    this.Velocity -= Velocity * GameCoefficients.FruitDrag * frameTimeInSeconds;
    
    this.Position += Velocity * frameTimeInSeconds;
}
```
Il `Fruit` oggetto è univoco nella sua implementazione di trascinamento: un valore che rallenta la velocità rispetto alle velocità di produzione è mobile. Fornisce l'implementazione di trascinare un *velocità terminal*, che è la velocità massima che può cercare un'istanza di frutta. Trascinare rallenta lo spostamento orizzontale di frutta, rendendo il gioco leggermente più facile da riprodurre.

Il `Paddle` si applica anche l'oggetto `Velocity` nel relativo `Activity` (metodo), ma lo `Velocity` viene calcolato usando un `desiredLocation` valore:


```csharp
public void Activity(float frameTimeInSeconds)
{
    // This code will cause the cursor to lag behind the touch point
    // Increasing this value reduces how far the paddle lags
    // behind the player’s finger. 
    const float velocityCoefficient = 4;
    // Get the velocity from current location and touch location
    Velocity = (desiredLocation - this.Position) * velocityCoefficient;
    this.Position += Velocity * frameTimeInSeconds;
    ...
}
```

In genere, i giochi che utilizzano `Velocity` per controllare la posizione dei relativi sistemi di oggetti non direttamente impostare le posizioni di entità, almeno non dopo l'inizializzazione. Anziché impostare direttamente la `Paddle` posizione, la `Paddle.HandleInput` metodo assegna il `desiredLocation` valore:

```csharp
public void HandleInput(CCPoint touchPoint)
{
    desiredLocation = touchPoint;
}
```

### <a name="collision"></a>Conflitto

Rientra Fruity implementa semi-realistico conflitto tra la frutta e altri oggetti collidable, ad esempio il `Paddle` e `GameScene.Splitter`. Per facilitare il debug di conflitto, aree di collisione cade Fruity possono essere reso visibile modificando il `GameCoefficients.ShowDebugInfo` nel `GameCoefficients.cs` file:


```csharp
public static class GameCoefficients
{
    ... 
    public const bool ShowCollisionAreas = true;
    ...
}
```

Impostando questo valore su `true` consente il rendering delle aree di conflitto:  

![](fruity-falls-images/image6.png "Impostando questo valore su true consente il rendering delle aree di conflitti")

Inizia la logica di conflitti di `GameScene.Activity` metodo:


```csharp
private void Activity(float frameTimeInSeconds)
{
    if (hasGameEnded == false)
    {
        paddle.Activity(frameTimeInSeconds);
        foreach (var fruit in fruitList)
        {
            fruit.Activity(frameTimeInSeconds);
        }
        spawner.Activity(frameTimeInSeconds);
        DebugActivity();
        PerformCollision();
    }
}
```

`PerformCollision` gestisce tutti in conflitto `Fruit` istanze con altri oggetti: 


```csharp
private void PerformCollision()
{
    // reverse for loop since fruit may be destroyed:
    for(int i = fruitList.Count - 1; i > -1; i--)
    {
        var fruit = fruitList[i];
        FruitVsPaddle(fruit);
        FruitPolygonCollision(fruit, splitter.Polygon, CCPoint.Zero);
        FruitVsBorders(fruit);
        FruitVsBins(fruit);
    }
}
```

#### <a name="fruitvsborders"></a>FruitVsBorders

`FruitVsBorders` collisione esegue la propria logica per collisione anziché basarsi sulla logica di contenuti in una classe diversa. Questa differenza esiste perché il conflitto tra frutti e i bordi della schermata non è perfettamente a tinta unito – è possibile per frutta essere inserita oltre il bordo dello schermo per lo spostamento racchetta attenzione. Frutta verrà bounce visualizzato sullo schermo quando raggiunto con la racchetta, ma se il giocatore inserisce lenta frutta verranno spostati oltre il bordo e lo schermo:


```csharp
private void FruitVsBorders(Fruit fruit)
{
    if(fruit.PositionX - fruit.Radius < 0 && fruit.Velocity.X < 0)
    {
        fruit.Velocity.X *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
    if(fruit.PositionX + fruit.Radius > gameplayLayer.ContentSize.Width && fruit.Velocity.X > 0)
    {
        fruit.Velocity.X *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
    if(fruit.PositionY + fruit.Radius > gameplayLayer.ContentSize.Height && fruit.Velocity.Y > 0)
    {
        fruit.Velocity.Y *= -1 * GameCoefficients.FruitCollisionElasticity;
    }
}
```

#### <a name="fruitvsbins"></a>FruitVsBins

Il `FruitVsBins` metodo è responsabile della verifica se eventuali frutta è scesa in uno di due contenitori. In questo caso, il lettore è attribuito punti (se/bin di frutta colori corrispondenza) o il gioco termina (se i colori non corrispondono):


```csharp
private void FruitVsBins(Fruit fruit)
{
    foreach(var bin in fruitBins)
    {
        if(bin.Polygon.CollideAgainst(fruit.Collision))
        {
            if(bin.FruitColor == fruit.FruitColor)
            {
                // award points:
                score += 1 + fruit.ExtraPointValue;
                scoreText.Score = score;
                Destroy(fruit);
            }
            else
            {
                EndGame();
            }
            break;
        }
    }
}
```

#### <a name="fruitvspaddle-and-fruitpolygoncollision"></a>FruitVsPaddle e FruitPolygonCollision

Frutta e racchetta e frutti e barra di divisione (l'area che separa i due contenitori) si basano sul conflitto il `FruitPolygonCollision` (metodo). Questo metodo è costituito da tre parti:

1. Verificare se gli oggetti sono in conflitto
1. Spostare gli oggetti (solo la frutta cade Fruity) in modo che non si sovrappongano
1. Regolare la velocità di oggetti (solo la frutta cade Fruity) per simulare un rimbalzo che il codice seguente illustra i punti apportati in precedenza:


```csharp
private static bool FruitPolygonCollision(Fruit fruit, Polygon polygon, CCPoint polygonVelocity)
{
    // Test whether the fruit collides
    bool didCollide = polygon.CollideAgainst(fruit.Collision);
    if (didCollide)
    {
        var circle = fruit.Collision;
        // Get the separation vector to reposition the fruit so it doesn't overlap the polygon
        var separation = CollisionResponse.GetSeparationVector(circle, polygon);
        fruit.Position += separation;
        // Adjust the fruit's Velocity to make it bounce:
        var normal = separation;
        normal.Normalize();
        fruit.Velocity = CollisionResponse.ApplyBounce(
            fruit.Velocity, 
            polygonVelocity, 
            normal, 
            GameCoefficients.FruitCollisionElasticity);
    }
    return didCollide;
}
```

Risposta di collisione cade Fruity è unilaterale: solo la frutta velocità e la posizione vengono modificate. Vale la pena notare che altri giochi possono disporre di conflitto che influisce su entrambi gli oggetti interessati, ad esempio un carattere inserendo un boulder o un arresto anomalo di due automobili in modo diverso.

I calcoli dietro la logica di collisione contenuto nel `Polygon` e `CollisionResponse` classi non rientra nell'ambito di questa Guida, ma come scritto, questi metodi possono essere utilizzati per molti tipi di giochi. Il poligono e `CollisionResponse` classi supportano anche non rettangolari e convessi poligoni, pertanto questo codice può essere usato per molti tipi di giochi.

 


## <a name="game-content"></a>Gioco contenuto

La grafica nel Fruity cade distingue immediatamente il gioco dal BouncingGame. Mentre le progettazioni di gioco sono simili, lettori verranno visualizzato immediatamente una differenza di aspetto i miei due giochi. Giocatori spesso decidono se provare un gioco per i rispettivi oggetti visivi. È pertanto estremamente importante che gli sviluppatori investono risorse gradevole visivamente la realizzazione di gioco.

La grafica per cade Fruity è stata creata con gli obiettivi seguenti:

 - Tema coerenza
 - Enfasi su un solo carattere – monkey di Xamarin
 - Colori chiari per creare un rilasciare piacevole esperienza
 - Contrasto tra il primo piano e gli oggetti di gioco sono facili da trovare in modo visivo
 - Possibilità di creare effetti visivi semplici senza animazioni elevati in termini di risorse


### <a name="content-location"></a>Percorso contenuto

Rientra Fruity include tutto il contenuto nella cartella immagini nel progetto Android:

![](fruity-falls-images/image7.png "Rientra Fruity include tutto il contenuto nella cartella immagini nel progetto Android")

Questi stessi file collegati nel progetto iOS per evitare la duplicazione e pertanto modifiche ai file impatto negativo sulle entrambi i progetti:

![](fruity-falls-images/image8.png "Questi stessi file collegati nel progetto iOS per evitare la duplicazione e pertanto modifiche ai file impatto negativo sulle entrambi i progetti")

Vale la pena notare che il contenuto non è contenuto all'interno di **Ld** o **Hd** cartelle, che fanno parte del modello CocosSharp predefinito. Il **Ld** e **Hd** cartelle devono essere utilizzati per i giochi che forniscono due set di contenuti: uno per i dispositivi a bassa risoluzione, quali telefoni e uno per i dispositivi con risoluzione superiore, ad esempio i Tablet. La locandina cade Fruity è intenzionalmente creata con un livello estetica, quindi non è necessario fornire contenuto per dimensioni dello schermo diverse. Pertanto, il **Ld** e **Hd** cartelle siano stati completamente rimossi dal progetto.


### <a name="gamescene-layering"></a>Ordine di disposizione GameScene

Come accennato in precedenza in questa Guida, il `GameScene` è responsabile per la creazione di istanze di oggetto gioco, posizionamento e logica tra oggetti (ad esempio conflitti). Tutti gli oggetti vengono aggiunti a uno dei quattro `CCLayer` istanze:


```csharp
CCLayer backgroundLayer;
CCLayer gameplayLayer;
CCLayer foregroundLayer;
CCLayer hudLayer;
```

Queste quattro livelli vengono creati nel `CreateLayers` metodo. Si noti che vengono aggiunti al `GameScene` in ordine di back in primo piano:


```csharp
private void CreateLayers()
{
    backgroundLayer = new CCLayer();
    this.AddLayer(backgroundLayer);
    gameplayLayer = new CCLayer();
    this.AddLayer(gameplayLayer);
    foregroundLayer = new CCLayer();
    this.AddLayer(foregroundLayer);
    hudLayer = new CCLayer();
    this.AddLayer(hudLayer);
}
```

Una volta creati i livelli, tutti gli oggetti visivi vengono aggiunti ai livelli utilizzando il `AddChild` (metodo), come illustrato nel `CreateBackground` metodo:


```csharp
private void CreateBackground()
{
    var background = new CCSprite("background.png");
    background.AnchorPoint = new CCPoint(0, 0);
    background.IsAntialiased = false;
    backgroundLayer.AddChild(background);
}
```


### <a name="vine-entity"></a>Entità vite

Il `Vine` entità viene utilizzata in modo univoco per il contenuto, non influisce sulla modalità di gioco. È composta da venti `CCSprite` istanze, un numero selezionato per prove ed errori per verificare che la vite raggiunge sempre nella parte superiore dello schermo:


```csharp
public Vine ()
{
    const int numberOfVinesToAdd = 20;
    for (int i = 0; i < numberOfVinesToAdd; i++)
    {
        var graphic = new CCSprite ("vine.png");
        graphic.AnchorPoint = new CCPoint(.5f, 0);
        graphic.PositionY = i * graphic.ContentSize.Height;
        this.AddChild (graphic);
    }
}
```

Il primo `CCSprite` è posizionato in modo bordo inferiore corrisponde alla posizione della vite. Questa operazione viene eseguita impostando l'AnchorPoint su `new CCPoint(.5f, 0)`. Il `AnchorPoint` Usa proprietà *normalizzato coordinate* intervallo compreso tra 0 e 1, indipendentemente dalle dimensioni del `CCSprite`:

![](fruity-falls-images/image9.png "La proprietà AnchorPoint utilizza coordinate normalizzate intervallo compreso tra 0 e 1, indipendentemente dalle dimensioni del CCSprite")

Sprite vite successivi vengono posizionati utilizzando il `graphic.ContentSize.Height` valore, che restituisce l'altezza dell'immagine in pixel. Il diagramma seguente mostra come l'immagine di vite Affianca verso l'alto:

![](fruity-falls-images/image10.png "Questo diagramma viene mostrato come l'immagine di vite Affianca verso l'alto")

Poiché l'origine del `Vine` entità è nella parte inferiore della vite quindi posizionandola è semplice, come illustrato nell'esempio il `Paddle.CreateVines` metodo:


```csharp
private void CreateVines()
{
    // Increasing this value moves the vines closer to the 
    // center of the paddle.
    const int vineDistanceFromEdge = 4;
    leftVine = new Vine ();
    var leftEdge = -width / 2.0f;
    leftVine.PositionX = leftEdge + vineDistanceFromEdge;
    this.AddChild (leftVine);
    rightVine = new Vine ();
    var rightEdge = width / 2.0f;
    rightVine.PositionX = rightEdge - vineDistanceFromEdge;
    this.AddChild (rightVine);
}
```

Vite istanze di `Paddle` entità dispone anche il comportamento di rotazione interessanti. Poiché il `Paddle` entità nel suo complesso Ruota in base a input di Windows Media player (che questa guida illustra in dettaglio più avanti), il `Vine` istanze anche sono interessate da questa rotazione. Tuttavia, la rotazione di `Vine` istanze lungo con il `Paddle` produce un effetto visivo indesiderato, come illustrato nella seguente animazione:

![](fruity-falls-images/image11.gif "Tuttavia, le istanze di viti con la racchetta rotazione produce effetti indesiderati visual come illustrato nella seguente animazione")

Per contrastare la `Paddle` rotazione, il `leftVine` e `rightVine` istanze vengono ruotate racchetta, come illustrato nella direzione opposta il `Paddle.Activity` metodo:


```csharp
public void Activity(float frameTimeInSeconds)
{
    ...
    // This value adds a slight amount of rotation
    // to the vine. Having a small amount of tilt looks nice.
    float vineAngle = this.Velocity.X / 100.0f;
    leftVine.Rotation = -this.Rotation + vineAngle;
    rightVine.Rotation = -this.Rotation + vineAngle;
}
```

Si noti che una piccola quantità di rotazione viene aggiunto al vite tramite il `vineAngle` coefficiente. Questo valore può essere modificato per regolare la quantità di ruotare il vines.


## <a name="gamecoefficients"></a>GameCoefficients

Ogni gioco valido è il prodotto di iterazione, in modo cade Fruity include una classe denominata `GameCoefficients` che controlla come viene riprodotto il gioco. Questa classe contiene le variabili espressive utilizzate in tutto il gioco per controllo fisica, layout, durante la generazione e l'assegnazione dei punteggi.

Ad esempio, l'ambito di frutta è controllata dalle variabili seguenti:


```csharp
public static class GameCoefficients
{
    ...
    
    // The strength of the gravity. Making this a 
    // smaller (bigger negative) number will make the
    // fruit fall faster. A larger (smaller negative) number
    // will make the fruit more floaty.
    public const float FruitGravity = -60;
    // The impact of "air drag" on the fruit, which gives
    // the fruit terminal velocity (max falling speed) and slows
    // the fruit's horizontal movement (makes the game easier)
    public const float FruitDrag = .1f;
    // Controls fruit collision bouncyness. A value of 1
    // means no momentum is lost. A value of 0 means all
    // momentum is lost. Values greater than 1 create a spring-like effect
    public const float FruitCollisionElasticity = .5f;
    
    ...
}
```

Come implicano i nomi, è possono utilizzare queste variabili regolare frutta velocità con cui si trova, la modalità orizzontale movimento rallenta nel tempo e racchetta bounciness.

Giochi coefficienti archiviati in file di codice o dati (ad esempio XML) possono essere particolarmente utili per i team con i progettisti di giochi che non sono presenti anche i programmatori.

La `GameCoefficients` classe include anche i valori per l'attivazione di informazioni di debug, ad esempio la generazione di informazioni o conflitto aree:


```csharp
public static class GameCoefficients
{
    ...
    // This controls whether debug information is displayed on screen.
    public const bool ShowDebugInfo = false;
    public const bool ShowCollisionAreas = false;
    ...
}
```


## <a name="conclusion"></a>Conclusione

Questa guida sono stati illustrati il gioco Fruity cadute. Descritta concetti, incluso il contenuto, fisica e la gestione dello stato del gioco.

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione CocosSharp API](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [Progetto completato (esempio)](https://developer.xamarin.com/samples/mobile/FruityFalls/)
