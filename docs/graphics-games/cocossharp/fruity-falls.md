---
title: Dettagli del gioco Fruity cade
description: Questa guida esamina il gioco Fruity cade, che coprono CocosSharp comuni e i concetti di sviluppo di giochi, ad esempio fisica, la gestione dei contenuti, lo stato del gioco e progettazione di giochi.
ms.prod: xamarin
ms.assetid: A5664930-F9F0-4A08-965D-26EF266FED24
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 959f5eb149ad375d686b17a85eb3d3b8fbdf3659
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61160221"
---
# <a name="fruity-falls-game-details"></a>Dettagli del gioco Fruity cade

_Questa guida esamina il gioco Fruity cade, che coprono CocosSharp comuni e i concetti di sviluppo di giochi, ad esempio fisica, la gestione dei contenuti, lo stato del gioco e progettazione di giochi._

Cade Fruity è un gioco di semplice, basato sulla fisica in cui il lettore consente di ordinare frutti di colore gialli e rossi in bucket colorato per guadagnare punti. L'obiettivo del gioco è per ottenere tutti i punti possibili evitando un calo frutti in bin di errato, terminare il gioco.

![](fruity-falls-images/image1.png "L'obiettivo del gioco è per ottenere tutti i punti possibili evitando un calo frutti in bin di errato, terminare il gioco")

Cade Fruity estende i concetti introdotti nel [Guida di BouncingGame](~/graphics-games/cocossharp/bouncing-game.md) aggiungendo il codice seguente:

 - Contenuto sotto forma di file PNG
 - Fisica avanzata
 - Stato del gioco (eseguire la transizione tra le scene)
 - Possibilità di ottimizzare i coefficienti del gioco tramite le variabili contenute in una singola classe
 - Supporto di debug visual incorporato
 - Organizzazione del codice usando le entità di gioco
 - Progettazione di giochi con stato attivo sul valore divertente e riproduzione

Mentre il [Guida di BouncingGame](~/graphics-games/cocossharp/bouncing-game.md) incentrata sull'introduzione CocosSharp concetti, Fruity cade viene illustrato come inserirli tutti insieme in un prodotto finito di gioco. Poiché questa guida viene fatto riferimento di BouncingGame, i lettori devono innanzitutto acquisire familiarità con le [Guida di BouncingGame](~/graphics-games/cocossharp/bouncing-game.md) prima di leggere questa Guida.

Questa guida illustra l'implementazione e la progettazione di Fruity cade per offrire informazioni dettagliate che consentono di creare un gioco. Viene descritto come gli argomenti seguenti:


- [Classe GameController](#gamecontroller-class)
- [Entità del gioco](#game-entities)
- [Grafica di frutta](#fruit-graphics)
- [Fisica](#physics)
- [Gioco di contenuto](#game-content)
- [GameCoefficients](#gamecoefficients)


## <a name="gamecontroller-class"></a>Classe GameController

Il progetto PCL rientra Fruity include un `GameController` classe responsabile per la creazione di un'istanza del gioco e lo spostamento tra le scene. Questa classe viene utilizzata da entrambi i progetti iOS e Android per eliminare il codice duplicato.

Il codice contenuto all'interno di `Initialize` è simile al codice nel metodo il`Initialize` metodo in un modello di CocosSharp invariato, ma contiene un numero di modifiche.

Per impostazione predefinita, il `GameView.ContentManager.SearchPaths` dipende dalla risoluzione del dispositivo. Come illustrato di seguito in modo più dettagliato, Fruity cade Usa lo stesso contenuto indipendentemente dalla risoluzione del dispositivo, in modo che il `Initialize` metodo aggiunge le `Images` percorso (con nessuna sottocartella) il `SearchPaths`:


```csharp
contentSearchPaths.Add ("Images");
```

Nuovi modelli di CocosSharp includono una classe che eredita da `CCLayer`, implicando che gli oggetti visivi del gioco e per la logica devono essere aggiunte a questa classe. Invece, Fruity cade Usa più `CCLayer` istanze al controllo disegno ordine. Questi `CCLayer` le istanze sono contenute all'interno di `GameView` classe che eredita da `CCScene`. Cade Fruity include anche scene più, il primo è il `TitleScene`. Di conseguenza, il `Initialize` metodo crea un'istanza di un `TitleScene` istanza che viene passato al `RunWithScene`:


```csharp
var scene = new TitleScene (GameView);
GameView.Director.RunWithScene (scene);
```

Il contenuto per Fruity cade è stato creato come un'arte pixel a bassa risoluzione ai fini estetici. Il `GameView.DesignResolution` è impostato in modo che il gioco è solo a livello di 384 unità e 512 talla:


```csharp
// We use a lower-resolution display to get a pixellated appearance
int width = 384;
int height = 512;
GameView.DesignResolution = new CCSizeI (width, height); 
```

Infine, il `GameController` classe fornisce un metodo statico che può essere chiamato da qualsiasi `CCGameScene` per eseguire la transizione a un altro `CCScene`. Questo metodo viene utilizzato per spostarsi tra i `TitleScene` e il `GameScene`.


## <a name="game-entities"></a>Entità del gioco

Cade Fruity Usa il modello di entità per la maggior parte degli oggetti del gioco. Una spiegazione dettagliata di questo modello è reperibile nella [guidano per le entità in CocosSharp](~/graphics-games/cocossharp/entities.md).

Gli oggetti giochi-implementazione di entità sono reperibile nella cartella entità la **FruityFalls.Common** progetto:

![](fruity-falls-images/image2.png "La cartella di entità nel progetto FruityFalls.Common")

Le entità sono oggetti che ereditano da `CCNode`e può avere gli oggetti visivi, collisioni e comportamento di ogni fotogramma.

Il `Fruit` oggetto rappresenta un'entità di CocosSharp tipico: contiene un oggetto visivo, conflitti e logica di ogni fotogramma. È responsabile dell'inizializzazione del frutto il relativo costruttore:


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

Il `CreateFruitGraphic` metodo crea un' `CCSprite` dell'istanza e lo aggiunge al `Fruit`. Il `IsAntialiased` proprietà è impostata su false per dare un'occhiata disturbate il gioco. Questo valore è impostato su false in tutti i `CCSprite` e `CCLabel` istanze in tutto il gioco:


```csharp
private void CreateFruitGraphic()
{
    graphic = new CCSprite ("cherry.png");
    graphic.IsAntialiased = false;
    this.AddChild (graphic);
}
```

Ogni volta che il giocatore tocca un `Fruit` dell'istanza con il `Paddle`, il valore del punto di tale frutta aumenta di uno. In questo modo una richiesta di verifica aggiuntivo per i lettori esperti all'esecuzione di frutta per punti supplementari. Il valore di punto del frutto viene visualizzato utilizzando il `extraPointsLabel` istanza.

Il `CreateExtraPointsLabel` metodo crea un' `CCLabel` dell'istanza e lo aggiunge al `Fruit`:


```csharp
private void CreateExtraPointsLabel()
{
    extraPointsLabel = new CCLabel("", "Arial", 12, CCLabelFormat.SystemFont);
    extraPointsLabel.IsAntialiased = false;
    extraPointsLabel.Color = CCColor3B.Black;
    this.AddChild(extraPointsLabel);
}
```

Cade Fruity include due diversi tipi di frutta – striscia del gratta e limoni. Il `CreateFruitGraphic` assegna un oggetto visivo predefinito, ma gli oggetti visivi di frutta possono essere modificato tramite il `FruitColor` proprietà, che a sua volta chiama `UpdateGraphics`:


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

La prima istruzione if in `UpdateGraphics` Aggiorna la grafica di debug, che viene usata per visualizzare le aree di collisione. Questi oggetti visivi sono disattivati prima di una versione finale del gioco viene effettuata, ma può essere mantenuta fase di sviluppo per il debug di fisica. Il secondo modifica parte i `graphic.Texture` proprietà chiamando `CCTextureCache.SharedTextureCache.AddImage`. Questo metodo fornisce accesso a una trama di base al nome file. Altre informazioni su questo metodo sono reperibile nel [Guida per la memorizzazione nella cache della trama](~/graphics-games/cocossharp/texture-cache.md).

Il `extraPointsLabel` colore viene regolato per mantenere il contrasto con l'immagine di frutta e la relativa `PositionY` valore viene adattato al centro il `CCLabel` sui frutti `CCSprite`:

![](fruity-falls-images/image3.png "Il colore extraPointsLabel viene regolato per mantenere il contrasto con l'immagine di frutto e il relativo valore PositionY viene regolato per allineare al centro CCLabel sulla frutta CCSprite")

![](fruity-falls-images/image4.png "Il colore extraPointsLabel viene regolato per mantenere il contrasto con l'immagine di frutto e il relativo valore PositionY viene regolato per allineare al centro CCLabel sulla frutta CCSprite")


### <a name="collision"></a>Conflitto

Cade Fruity implementa una soluzione dei conflitti personalizzato utilizzando gli oggetti nella cartella della geometria:

![](fruity-falls-images/image5.png "Cade Fruity implementa una soluzione dei conflitti personalizzato utilizzando gli oggetti nella cartella di geometria")

Collisione in Fruity cade viene implementato usando il `Circle` o `Polygon` oggetto, in genere con uno di questi due tipi viene aggiunti come figlio di un'entità. Ad esempio, il `Fruit` oggetto ha un `Circle` chiamato `Collision` che viene inizializzata relativo `CreateCollision` (metodo):


```csharp
private void CreateCollision()
{
    Collision = new Circle ();
    Collision.Radius = GameCoefficients.FruitRadius;
    this.AddChild (Collision);
}
```

Si noti che il `Circle` classe eredita dal `CCNode` classe, pertanto può essere aggiunta come elemento figlio per le entità del nostro gioco:


```csharp
public class Circle : CCNode
{
    ...
}
```

`Polygon` è simile alla creazione `Circle` la creazione, come illustrato nel `Paddle` classe:


```csharp
private void CreateCollision()
{
    Polygon = Polygon.CreateRectangle(width, height);
    this.AddChild (Polygon);
}
```

Viene descritta la logica di collisione [più avanti in questa Guida](#collision).


## <a name="physics"></a>Fisica

La fisica in Fruity cade può essere suddivisa in due categorie: lo spostamento e la collisione. 


### <a name="movement-using-velocity-and-acceleration"></a>Spostamento con la velocità e l'accelerazione

Usa cade Fruity `Velocity` e `Acceleration` i valori per controllare lo spostamento delle entità, simile al [BouncingGame](~/graphics-games/cocossharp/bouncing-game.md). Entità di implementano la logica di spostamento in un metodo denominato `Activity`, che viene chiamato una volta per ogni fotogramma. Ad esempio, si noterà l'implementazione dello spostamento nel `Fruit` della classe `Activity` metodo:

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
Il `Fruit` oggetto è univoco nella relativa implementazione del trascinamento: un valore che determina un rallentamento della velocità in relazione alla velocità del frutto è in movimento. Questa implementazione del trascinamento fornisce una *velocità terminal*, che corrisponde alla velocità massima che può passare un'istanza di frutto. Trascinare rallenta anche lo spostamento orizzontale di frutta, rendendo il gioco leggermente più facile da riprodurre.

Il `Paddle` si applica anche l'oggetto `Velocity` nel relativo `Activity` metodo, ma la relativa `Velocity` viene calcolato usando un `desiredLocation` valore:


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

In genere, i giochi che usano `Velocity` per controllare la posizione dei loro sistemi di oggetti non direttamente impostare le posizioni di entità, almeno non dopo l'inizializzazione. Anziché impostare direttamente la `Paddle` posizione, la `Paddle.HandleInput` metodo assegna il `desiredLocation` valore:

```csharp
public void HandleInput(CCPoint touchPoint)
{
    desiredLocation = touchPoint;
}
```

### <a name="collision"></a>Conflitto

Cade Fruity implementa semi-realistico collisione tra i frutti e altri oggetti collidable, ad esempio la `Paddle` e `GameScene.Splitter`. Per eseguire il debug dei conflitti, le aree di collisione Fruity cade possono essere reso visibile modificando la `GameCoefficients.ShowDebugInfo` nella `GameCoefficients.cs` file:


```csharp
public static class GameCoefficients
{
    ... 
    public const bool ShowCollisionAreas = true;
    ...
}
```

Impostando questo valore su `true` Abilita il rendering delle aree di conflitto:  

![](fruity-falls-images/image6.png "Impostando questo valore su true consente il rendering delle aree di collisione")

Viene avviata per la logica di collisione nel `GameScene.Activity` metodo:


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

`PerformCollision` gestisce tutti scontrare `Fruit` istanze con altri oggetti: 


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

`FruitVsBorders` collisione esegue la propria logica per collisione anziché basarsi sulla logica contenuta in un'altra classe. Questa differenza esiste in quanto la collisione tra frutti e i bordi della schermata non è perfettamente a tinta unita – è possibile che forniscono frutta push esterno del bordo della schermata per lo spostamento di un'attenta racchetta. Frutto rimbalzerà dallo schermo quando raggiunto con la racchetta, ma se il giocatore lentamente effettua il push di frutta si sposta oltre il bordo e fuori dallo schermo:


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

Il `FruitVsBins` metodo è responsabile della verifica se eventuali frutta è scesa in uno dei due bin. In questo caso, il lettore è assegnava i punti (se il frutta/bin applica colori alla corrispondenza) o il gioco finisce (se non corrispondono i colori):


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

Frutti e racchetta e frutto e barra di divisione (l'area che separa i due bin) entrambi si basano sulla collisione di `FruitPolygonCollision` (metodo). Questo metodo ha tre parti:

1. Verificare se gli oggetti sono in conflitto
1. Spostare gli oggetti (solo la frutta Fruity cade) in modo che non si sovrappongano
1. Modificare la velocità degli oggetti (solo la frutta Fruity cade) per simulare un rimbalzo che il codice seguente illustra i punti creati in precedenza:


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

Risposta di collisione cade Fruity è unilaterale – vengono regolate solo la frutta velocità e la posizione. Vale la pena notare che altri giochi potrebbero avere conflitti che influisce su entrambi gli oggetti interessati, ad esempio un carattere di cui eseguire il push di boulder una o due automobili in modo anomalo in modo diverso.

I calcoli matematici dietro la logica di collisione contenuta nel `Polygon` e `CollisionResponse` classi esula dall'ambito di questa Guida, ma come scritto, questi metodi possono essere utilizzati per molti tipi di giochi. Il poligono e `CollisionResponse` classi supportano anche i poligoni non rettangolari e convessi, pertanto questo codice può essere usato per molti tipi di giochi.

 


## <a name="game-content"></a>Gioco di contenuto

L'arte in Fruity cade distingue immediatamente il gioco da di BouncingGame. Mentre le progettazioni di gioco sono simili, lettori verranno visualizzata immediatamente una differenza di aspetto dei giochi di due. Spesso i giocatori di decidono se provare un gioco, i rispettivi oggetti visivi. Pertanto, è estremamente importante che gli sviluppatori di investono risorse in effettua un dahsboard gioco.

L'arte per Fruity cade è stata creata con gli obiettivi seguenti:

 - Tema coerente
 - Enfasi su un solo carattere – monkey di Xamarin
 - Colori chiari per creare una più flessibile, piacevole esperienza
 - Il contrasto tra il primo piano e in modo che gli oggetti gioco sono facili tenere traccia visivamente
 - Possibilità di creare effetti visivi semplici senza animazioni elevati in termini di risorse


### <a name="content-location"></a>Percorso contenuto

Cade Fruity include tutto il contenuto nella cartella immagini nel progetto Android:

![](fruity-falls-images/image7.png "Cade Fruity include tutto il contenuto nella cartella immagini nel progetto Android")

Questi stessi file collegati nel progetto iOS per evitare la duplicazione e le modifiche in questo caso per i file influiscono entrambi i progetti:

![](fruity-falls-images/image8.png "Questi stessi file collegati nel progetto iOS per evitare la duplicazione e in tal caso modifiche ai file un impatto sulle entrambi i progetti")

Vale la pena notare che il contenuto non è contenuto all'interno di **Ld** oppure **Hd** cartelle, che fanno parte del modello CocosSharp predefinito. Il **Ld** e **Hd** cartelle devono essere utilizzati per i giochi che offrono due set di contenuto, uno per i dispositivi a bassa risoluzione, quali telefoni e uno per i dispositivi con risoluzione superiore, ad esempio i Tablet. L'arte cade Fruity viene intenzionalmente creata con un disturbate estetico, in modo che non è necessario fornire contenuti per diverse dimensioni dello schermo. Pertanto, il **Ld** e **Hd** cartelle siano stati completamente rimossi dal progetto.


### <a name="gamescene-layering"></a>Sovrapposizione GameScene

Come accennato in precedenza in questa Guida, il `GameScene` è responsabile della creazione dell'istanza di oggetto gioco, posizionamento e la logica tra oggetti (ad esempio conflitti). Tutti gli oggetti vengono aggiunti a uno dei quattro `CCLayer` istanze:


```csharp
CCLayer backgroundLayer;
CCLayer gameplayLayer;
CCLayer foregroundLayer;
CCLayer hudLayer;
```

Questi quattro livelli vengono creati nel `CreateLayers` (metodo). Si noti che vengono aggiunti al `GameScene` in ordine di back in primo piano:


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

Una volta creati i livelli, tutti gli oggetti visivi vengono aggiunti ai livelli usando il `AddChild` metodo, come illustrato nel `CreateBackground` metodo:


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

Il `Vine` entità in modo univoco è usato per il contenuto – ha alcun impatto sulla modalità di gioco. È composto da venti `CCSprite` istanze, un numero selezionato per prove empiriche per assicurarsi che la vite sempre raggiunge la parte superiore della schermata:


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

Il primo `CCSprite` è posizionato in modo che il bordo inferiore corrisponde alla posizione di vite. Questa operazione viene eseguita impostando l'AnchorPoint `new CCPoint(.5f, 0)`. Il `AnchorPoint` utilizzata dalla proprietà *normalizzato coordinate* quale intervallo compreso tra 0 e 1 indipendentemente dalle dimensioni del `CCSprite`:

![](fruity-falls-images/image9.png "La proprietà AnchorPoint Usa coordinate normalizzate l'intervallo compreso tra 0 e 1 indipendentemente dalle dimensioni del CCSprite")

Sprite vite successivi vengono posizionati mediante le `graphic.ContentSize.Height` valore, che restituisce l'altezza dell'immagine in pixel. Il diagramma seguente mostra come l'immagine di vite Affianca verso l'alto:

![](fruity-falls-images/image10.png "Il diagramma seguente illustra come l'immagine di vite Affianca verso l'alto")

Poiché l'origine del `Vine` entità è nella parte inferiore della vite, quindi posizionandola è semplice, come illustrato nell'esempio il `Paddle.CreateVines` metodo:


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

Vite di istanze nel `Paddle` entità dispone inoltre interessante il comportamento di rotazione. Poiché il `Paddle` entità nel suo complesso Ruota in base a input player (che in questa guida illustra in dettaglio più avanti), il `Vine` istanze sono inoltre interessate da questa rotazione. Tuttavia, la rotazione di `Vine` istanze con il `Paddle` produce un effetto visivo indesiderato, come illustrato nell'animazione seguente:

![](fruity-falls-images/image11.gif "Tuttavia, la rotazione le istanze di vite insieme la racchetta produce un effetto visivo indesiderato come illustrato nell'animazione seguente")

Contrastare il `Paddle` rotazione, il `leftVine` e `rightVine` istanze vengono ruotate la racchetta, come illustrato nella direzione opposta rispetto il `Paddle.Activity` metodo:


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

Si noti che una piccola quantità di rotazione viene nuovamente aggiunto al vite tramite il `vineAngle` coefficiente. Questo valore può essere modificato per modificare quanto il vines ruotare.


## <a name="gamecoefficients"></a>GameCoefficients

Ogni gioco valido è il prodotto di iterazione, in modo che cade Fruity includa una classe denominata `GameCoefficients` che controlla come viene riprodotto il gioco. Questa classe contiene variabili esplicite che vengono usate in tutto il gioco di controllo fisica, layout, durante la generazione e di assegnazione dei punteggi.

Ad esempio, la fisica forniscono frutta è controllata dalle variabili seguenti:


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

Come implicano i nomi, queste variabili possono essere utilizzate per regolare la velocità di frutta cade, la modalità orizzontale dello spostamento rallenta nel tempo e bounciness racchetta.

Giochi coefficienti archiviati in file di codice o dati (ad esempio XML) possono essere particolarmente utili per i team di progettisti di giochi che non sono presenti anche i programmatori.

Il `GameCoefficients` classe include anche i valori per l'attivazione di informazioni di debug, ad esempio la generazione di informazioni o conflitto di aree:


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

Questa guida ha esplorato il gioco Fruity cade. Trattati i concetti inclusi contenuti, fisica e la gestione dello stato del gioco.

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione API CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [Progetto completato (esempio)](https://developer.xamarin.com/samples/mobile/FruityFalls/)
