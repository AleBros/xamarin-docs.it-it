---
title: Dettagli BouncingGame
description: Questa procedura dettagliata viene illustrato come implementare un semplice gioco palla rimbalzo con CocosSharp.
ms.prod: xamarin
ms.assetid: AC9FD56F-6E4A-40DA-8168-45A761D869FD
author: charlespetzold
ms.author: chape
ms.date: 03/29/2018
ms.openlocfilehash: d12d6fb8ecfcba5e5093b2af4790a51ef8cf8e47
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="bouncinggame-details"></a>Dettagli BouncingGame

> [!TIP]
> Il codice per BouncingGame è reperibile nella [Xamarin samples](https://developer.xamarin.com/samples/mobile/BouncingGame/). Per meglio proseguire con questa Guida, scaricare ed esaminare il codice come la Guida fa riferimento a essa.

Questa procedura dettagliata viene illustrato come implementare un semplice gioco palla rimbalzo con CocosSharp. 

 - Decompressione del contenuto di gioco
 - Elementi visual CocosSharp comuni
 - Aggiunta di elementi visivi per `GameLayer`
 - Implementazione della logica di ogni fotogramma.

Il gioco completata sarà simile al seguente:

![BouncingGame, completata](bouncing-game-images/image1.png "BouncingGame, completata")

## <a name="unzipping-game-content"></a>Decompressione del contenuto di gioco

Sviluppatori di giochi usano spesso il termine *contenuto* per fare riferimento a file non di codice che vengono generalmente creati dal visual artisti, finestre di progettazione del gioco o audio finestre di progettazione. Tipi di contenuto comuni includono file utilizzati per visualizzare gli oggetti visivi, riprodurre un suono o controllare il comportamento di intelligenza artificiale (AI). Dalla prospettiva del team di sviluppo di giochi, contenuto in genere viene creato da non programmatori. Il gioco include due tipi di contenuto:

 - file PNG per definire come vengono visualizzati la palla e racchetta.
 - Un file xnb singolo per definire il tipo di carattere utilizzato per la visualizzazione di punteggio (illustrata più dettagliatamente quando si aggiunge la visualizzazione di punteggio riportato di seguito)

Questo contenuto utilizzato in questo argomento sono disponibili [contenuto zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true). È necessario che questi file scaricato e decompresso il codice in un percorso a cui si accederà più avanti in questa procedura dettagliata.

## <a name="common-cocossharp-visual-elements"></a>Elementi visual CocosSharp comuni

CocosSharp fornisce una serie di classi utilizzate per visualizzare gli oggetti visivi. Alcuni elementi sono direttamente visibili, mentre altri sono utilizzati per l'organizzazione. Durante il gioco, si userà quanto segue:

 - `CCNode` -Classe di base per tutti gli oggetti visivi CocosSharp. Il `CCNode` classe contiene un `AddChild` funzione che può essere usato per costruire una gerarchia padre/figlio, detta anche un *struttura ad albero visuale* o *grafico*. Tutte le classi descritte di seguito ereditano da `CCNode`.
 - `CCScene` – Radice dell'albero visuale per i giochi CocosSharp. Tutti gli elementi visivi devono far parte di un struttura ad albero visuale con un `CCScene` alla radice, o non saranno visibili.
 - `CCLayer` : Contenitore per visual oggetti, ad esempio `CCSprite`. Come suggerisce il nome, la `CCLayer` classe viene utilizzata per controllare il livello di elementi come visivo sopra l'altro.
 - `CCSprite` : Visualizza un'immagine o una parte di un'immagine. `CCSprite` le istanze possono essere posizionate, ridimensionare e forniscono una serie di effetti visivi.
 - `CCLabel` : Visualizza una stringa sullo schermo. Il tipo di carattere utilizzato `CCLabel` è definito in un file xnb. Parleremo xnbs in dettaglio più avanti.

Per comprendere come vengono usati i diversi tipi vengono verranno prese in considerazione un unico `CCSprite`. Elementi visivi devono essere aggiunto a un `CCLayer`, e la struttura ad albero visuale deve avere un `CCScene` alla radice. Pertanto, la relazione padre/figlio per un singolo `CCSprite` sarebbe `CCScene`  >  `CCLayer`  >  `CCSprite`.

## <a name="adding-visual-elements-to-gamelayer"></a>Aggiunta di elementi visivi a GameLayer

### <a name="adding-the-paddle-sprite"></a>Aggiunta di sprite racchetta

Inizialmente verrà impostata sullo sfondo del gioco per nero e anche aggiungere un singolo `CCSprite` il rendering sullo schermo. Modificare il `GameLayer` classe per aggiungere un `CCSprite` come indicato di seguito:

```csharp
using System;
using System.Collections.Generic;
using CocosSharp;
namespace BouncingGame
{
    public class GameLayer : CCLayer
    {
        CCSprite paddleSprite;
        public GameLayer () : base(CCColor4B.Black)
        {
            // "paddle" refers to the paddle.png image
            paddleSprite = new CCSprite ("paddle");
            paddleSprite.PositionX = 100;
            paddleSprite.PositionY = 100;
            AddChild (paddleSprite);
        }
        protected override void AddedToScene ()
        {
            base.AddedToScene ();
            // Use the bounds to layout the positioning of the drawable assets
            CCRect bounds = VisibleBoundsWorldspace;
            // Register for touch events
            var touchListener = new CCEventListenerTouchAllAtOnce ();
            touchListener.OnTouchesEnded = OnTouchesEnded;
            AddEventListener (touchListener, this);
        }
        void OnTouchesEnded (List<CCTouch> touches, CCEvent touchEvent)
        {
            if (touches.Count > 0)
            {
                // Perform touch handling here
            }
        }
    }
}
```

Il codice precedente viene creato un singolo `CCSprite` e lo aggiunge come elemento figlio di `GameLayer`. Il `CCSprite` costruttore consente di definire il file di immagine da utilizzare come stringa. Il codice indica CocosSharp per cercare un file denominato `paddle` (l'estensione viene omesso, che verrà descritta più avanti in questa Guida). CocosSharp cercherà i nomi dei file `paddle` nella cartella radice del contenuto (ovvero **contenuto**) nonché le cartelle aggiunte il `gameView.ContentManager.SearchPaths` (descritto nella sezione precedente).

I file verrà aggiunto direttamente alla radice **contenuto** cartella per iOS e Android. A tale scopo, pulsante destro del mouse oppure fare clic sul controllo di **contenuto** cartella nel progetto iOS e selezionare **Aggiungi** > **Aggiungi file...** Passare in cui è stato decompresso il contenuto precedente e selezionare **paddle.png**. Se frequenti su come aggiungere file alla cartella, è necessario selezionare il **copia** opzione:

![Aggiungi File alla finestra di dialogo cartella](bouncing-game-images/image2.png "Add File alla finestra di dialogo cartella")

Successivamente, aggiungeremo il file al progetto Android. Pulsante destro del mouse o controllo scegliere la cartella del contenuto (che è il **asset** cartella progetti Android) e scegliere **Aggiungi** > **Aggiungi file...** . Questa volta, passare al progetto di iOS **contenuto** cartella. Quando viene chiesto di aggiungere il file, selezionare il **aggiungere un collegamento** opzione:

![Aggiungi File alla finestra di dialogo cartella](bouncing-game-images/addalink.png "il aggiungere file alla finestra di dialogo cartella")

Parleremo perché con cui i file devono essere aggiunte a entrambi i progetti seguenti. Ogni progetto **contenuto** cartella contiene ora il **paddle.png** file:

![Il contenuto della cartella del contenuto](bouncing-game-images/image3.png "il contenuto della cartella del contenuto")

Se eseguiamo il gioco vedremo le `CCSprite` tracciata:

![Racchetta, tracciare sullo schermo](bouncing-game-images/image4.png "racchetta, tracciare sullo schermo")

### <a name="file-details"></a>Dettagli del file

Finora è stato aggiunto un singolo file al progetto e il processo è piuttosto semplice. È sufficiente aggiungere il **paddle.png** del file per il **contenuto** cartelle e fatto riferimento nel codice. È opportuno esaminare alcune considerazioni quando si lavora con i file in CocosSharp.

#### <a name="capitalization"></a>Maiuscole/minuscole

Si noti che il nome del file e la stringa viene usata nel codice per accedere al file sono minuscole. Questo avviene perché alcune piattaforme (ad esempio simulatore di iOS e desktop Windows) sono fatta distinzione tra maiuscole e minuscole, mentre altre piattaforme (ad esempio i dispositivi Android e iOS) sono tra maiuscole e minuscole. Si utilizzerà tutti i file minuscoli nella parte restante di questa esercitazione in modo che i file e codice rimangono invariate multipiattaforma presto.

#### <a name="extensions"></a>Estensioni

Il costruttore per la creazione di Sprite non include l'estensione "PNG" quando si fa riferimento il file racchetta. L'estensione per i file in genere viene omessa quando lavorando su progetti CocosSharp come estensioni di file per lo stesso tipo di risorsa può variare tra le piattaforme. File audio, ad esempio, potrebbero essere AIFF o MP3,. wma formati di file a seconda della piattaforma. Lasciare disattivato l'estensione consente lo stesso codice funzionare in tutte le piattaforme indipendentemente dall'estensione di file.

#### <a name="content-in-platform-specific-projects"></a>Pacchetto di contenuto nei progetti specifici della piattaforma

A differenza della maggior parte dei file di codice che possono essere in una PCL, contenuta file devono essere aggiunti a ogni progetto specifico della piattaforma. CocosSharp richiede questa configurazione per due motivi:

1. Ogni piattaforma presenta diversi **le operazioni di compilazione**. Il contenuto aggiunto ai progetti iOS deve utilizzare il **BundleResource** azione di compilazione. Il contenuto aggiunto ai progetti Android deve utilizzare il **AndroidAsset** azione di compilazione.
2. Alcune piattaforme richiedono formati di file specifici della piattaforma. Ad esempio, file .xnb caratteri differiscono tra iOS e Android, come verrà illustrato più avanti nella procedura dettagliata.

Se un formato di file differiscono tra le piattaforme (ad esempio con estensione png), ogni piattaforma possibile utilizzare lo stesso file, in cui una o più piattaforme possono collegare il file nella stessa località.

## <a name="orientation"></a>Orientamento

Come qualsiasi altra app, App CocosSharp possono essere eseguite in orientamento verticale o orizzontale. Verranno verranno modificati per il gioco per l'esecuzione in modalità solo verticale. Prima di tutto, verranno modificate il codice di risoluzione del gioco per gestire una proporzione verticale. A tale scopo, modificare il `width` e `height` i valori di `LoadGame` metodo nel `ViewController` classe in iOS e `MainActivity` classe in Android:

```csharp
void LoadGame (object sender, EventArgs e)
{
    CCGameView gameView = sender as CCGameView;

    if (gameView != null)
    {
        var contentSearchPaths = new List<string> () { "Fonts", "Sounds" };
        CCSizeI viewSize = gameView.ViewSize;

        int width = 768;
        int height = 1027;
    // ...
```

Successivamente che sarà necessario modificare ogni progetto specifico della piattaforma per l'esecuzione in modalità verticale.

### <a name="ios-orientation"></a>orientamento di iOS

Per modificare l'orientamento del progetto iOS, selezionare il **Info. plist** file nel **BouncingGame.iOS** il progetto e modificare **iPhone/iPod le informazioni sulla distribuzione** e il **iPad le informazioni sulla distribuzione** per includere solo gli orientamenti verticale:

![Opzioni di orientamento](bouncing-game-images/image5.png "opzioni di orientamento")

### <a name="android-orientation"></a>Orientamento Android

Per modificare l'orientamento del progetto Android, aprire il file Mainactivity nel progetto BouncingGame.Android. Modificare la definizione dell'attributo attività in modo che specifica solo un orientamento verticale come indicato di seguito:

```csharp
[Activity (
    Label = "BouncingGame.Android",
    AlwaysRetainTaskState = true,
    Icon = "@drawable/icon",
    Theme = "@android:style/Theme.NoTitleBar",
    ScreenOrientation = ScreenOrientation.Portrait,
    LaunchMode = LaunchMode.SingleInstance,
    MainLauncher = true,
    ConfigurationChanges = ConfigChanges.Orientation | ConfigChanges.ScreenSize | ConfigChanges.Keyboard | ConfigChanges.KeyboardHidden)
]
public class MainActivity : Activity
{ 
...
```

## <a name="default-coordinate-system"></a>Il sistema di coordinate

Il codice che crea un'istanza di un `CCSprite`, imposta il `PositionX` e `PositionY` valori a 100. Per impostazione predefinita, questo significa che il `CCSprite` centro verrà posizionato 100 pixel in alto e a destra da basso a sinistra della schermata. Il sistema di coordinate può essere modificato da associare un `CCCamera` per il `CCLayer`. Si non si lavora con `CCCamera` in questo progetto, ma altre informazioni su `CCCamera` è reperibile nel [documenti CocosSharp API](https://developer.xamarin.com/api/type/CocosSharp.CCLayer/).

Uguale a 100 pixel in precedenza "gioco" pixel anziché pixel dell'hardware. Ciò significa che l'esecuzione del gioco stesso in un dispositivo di una risoluzione diversa (ad esempio un iPad e un iPhone) restituirà negli oggetti viene posizionato e dimensionati dello schermo fisico. In particolare, area visibile del gioco saranno sempre 1024 pixel in altezza e larghezza 768 pixel, poiché questa è la risoluzione è stato indicato nella sezione precedente di `LoadGame` metodo.

## <a name="adding-the-ball-sprite"></a>Aggiunta di sprite palla

Ora che si ha familiarità con le nozioni di base dell'utilizzo `CCSprite`, verrà aggiunto il secondo `CCSprite` : un cerchio. Si saranno in seguito a operazioni che sono molto simili alle modalità di creazione la racchetta `CCSprite`. 

In primo luogo, aggiungeremo il **ball.png** immagine dalla cartella decompresso al progetto iOS **contenuto** cartella. Selezionare questa opzione per **copia** il file per il **contenuto** directory. Seguire la stessa procedura descritta sopra per aggiungere un collegamento per il **ball.png** file nel progetto Android.

Quindi creare il `CCSprite` per la palla mediante l'aggiunta di un membro denominato `ballSprite` per il `GameScene` (classe), nonché il codice di creazione dell'istanza per il `ballSprite`. Al termine il `GameLayer` classe avrà un aspetto simile al seguente:

```csharp
public class GameLayer : CCLayer
{
    CCSprite paddleSprite;
    CCSprite ballSprite;
    public GameLayer () : base (CCColor4B.Black)
    {
        // "paddle" refers to the paddle.png image
        paddleSprite = new CCSprite ("paddle");
        paddleSprite.PositionX = 100;
        paddleSprite.PositionY = 100;
        AddChild (paddleSprite);
        ballSprite = new CCSprite ("ball");
        ballSprite.PositionX = 320;
        ballSprite.PositionY = 600;
        AddChild (ballSprite);
    }
```

## <a name="adding-the-score-label"></a>Aggiunta l'etichetta di punteggio

L'ultimo elemento visual verrà aggiunto per il gioco è un `CCLabel` per visualizzare il numero di volte in cui l'utente è stata ha al mittente alla palla. Il `CCLabel` utilizza un tipo di carattere specificato nel costruttore per stringhe di visualizzazione sullo schermo.

Aggiungere il codice seguente per creare un `CCLabel` dell'istanza in `GameLayer`. Una volta terminato il codice dovrebbe essere simile al seguente:

```csharp
public class GameLayer : CCLayer
{
    CCSprite paddleSprite;
    CCSprite ballSprite;
    CCLabel scoreLabel;
    public GameLayer () : base (CCColor4B.Black)
    {
        // "paddle" refers to the paddle.png image
        paddleSprite = new CCSprite ("paddle");
        paddleSprite.PositionX = 100;
        paddleSprite.PositionY = 100;
        AddChild (paddleSprite);
        ballSprite = new CCSprite ("ball");
        ballSprite.PositionX = 320;
        ballSprite.PositionY = 600;
        AddChild (ballSprite);
        scoreLabel = new CCLabel ("Score: 0", "Arial", 20, CCLabelFormat.SystemFont);
        scoreLabel.PositionX = 50;
        scoreLabel.PositionY = 1000;
        scoreLabel.AnchorPoint = CCPoint.AnchorUpperLeft;
        AddChild (scoreLabel);
    }
    // ...
```

Si noti che utilizza il scoreLabel un `AnchorPoint` di `CCPoint.AnchorUpperLeft`, vale a dire che il `PositionX` e `PositionY` valori definiscono la posizione dell'angolo superiore sinistro. In questo modo la posizione di `scoreLabel` relativo in alto a sinistra della schermata senza dover prendere in considerazione le dimensioni dell'etichetta.

## <a name="implementing-every-frame-logic"></a>Implementazione della logica di ogni fotogramma.

Finora, il gioco presenta una scena statica. Verranno aggiunte logica per controllare il movimento degli oggetti nella scena aggiungendo il codice che aggiorna la posizione degli oggetti a una frequenza elevata. In questo caso, il codice verrà eseguito sessanta volte al secondo - anche detta sessanta *frame* al secondo (a meno che non può gestire l'hardware aggiornare questo frequentemente). In particolare, verranno aggiunte logica per rendere la palla fallback e bounce contro racchetta, spostare la racchetta in base all'input e per aggiornare il punteggio del lettore ogni volta che la palla colpisce la racchetta.

Il `Schedule` metodo, che viene fornito dal `CCNode` classe, consente di aggiungere la logica di ogni frame del gioco. Verrà aggiunto il codice dopo `// New code` al costruttore GameLayer:

```csharp
public GameLayer () : base (CCColor4B.Black)
{
    // "paddle" refers to the paddle.png image
    paddleSprite = new CCSprite ("paddle");
    paddleSprite.PositionX = 100;
    paddleSprite.PositionY = 100;
    AddChild (paddleSprite);
    ballSprite = new CCSprite ("ball");
    ballSprite.PositionX = 320;
    ballSprite.PositionY = 600;
    AddChild (ballSprite);
        scoreLabel = new CCLabel ("Score: 0", "arial", 22, CCLabelFormat.SpriteFont);
    scoreLabel.PositionX = 50;
    scoreLabel.PositionY = 1000;
    scoreLabel.AnchorPoint = CCPoint.AnchorUpperLeft;
    AddChild (scoreLabel);
    // New code:
    Schedule (RunGameLogic);
}
```

Creare ora un `RunGameLogic` metodo il `GameLayer` (classe), che conterrà tutta la logica di ogni fotogramma:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
}
```

Il parametro float definisce quanto tempo il frame è espresso in secondi. Verrà usato questo valore quando si implementa lo spostamento della palla.

### <a name="making-the-ball-fall"></a>Effettua la palla rientrano

È possibile rendere la palla rientrano dal codice di gravità implementazione manualmente o utilizzando la funzionalità Box2D incorporata in CocosSharp. Il motore di simulazione fisica Box2D è disponibile per i giochi CocosSharp. È molto efficace ed efficiente, ma richiede la scrittura di codice di installazione. Poiché la simulazione fisica è piuttosto semplice, in questo caso verrà implementato manualmente.

Per implementare la gravità è necessario nell'archivio corrente X e Y velocità della palla da. Verrà aggiunto due membri per il `GameLayer` classe:

```csharp
float ballXVelocity;
float ballYVelocity;
// How much to modify the ball's y velocity per second:
const float gravity = 140;
```

Successivamente è possibile implementare la logica in diminuzione nel `RunGameLogic`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
}
```

### <a name="moving-the-paddle-with-touch-input"></a>Lo spostamento racchetta con input tocco

Ora che la palla sia in, per la racchetta aggiungeremo spostamento orizzontale mediante la `CCEventListenerTouchAllAtOnce` oggetto. Questo oggetto fornisce un numero di eventi correlati all'input. In questo caso, si desidera ricevere una notifica se nessuno dei punti di tocco sposta in modo che è possibile regolare la posizione della racchetta. Il `GameLayer` già crea un `CCEventListenerTouchAllAtOnce`, pertanto, è sufficiente assegnare il `OnTouchesMoved` delegato. A tale scopo, modificare il metodo AddedToScene come segue:

```csharp
protected override void AddedToScene ()
{
    base.AddedToScene ();
    // Use the bounds to layout the positioning of the drawable assets
    CCRect bounds = VisibleBoundsWorldspace;
    // Register for touch events
    var touchListener = new CCEventListenerTouchAllAtOnce ();
    touchListener.OnTouchesEnded = OnTouchesEnded;
    // new code:
    touchListener.OnTouchesMoved = HandleTouchesMoved;
    AddEventListener (touchListener, this);
}
```

Successivamente, verrà implementato `HandleTouchesMoved`:

```csharp
void HandleTouchesMoved (System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    // we only care about the first touch:
    var locationOnScreen = touches [0].Location;
    paddleSprite.PositionX = locationOnScreen.X;
}
```

### <a name="implementing-ball-collision"></a>Implementazione di collisione palla

Se Esegui il gioco a questo punto si noterà che la palla passa la racchetta. Verrà implementato *collisione* (logica di rispondere agli oggetti giochi sovrapposti) nel codice di ogni fotogramma. Poiché gli oggetti modifiche di spostamento posiziona ogni frame, il controllo delle collisioni è in genere eseguite anche ogni frame. Verranno inoltre aggiunte velocità sull'asse X quando la palla la racchetta per aggiungere alcune richiesta per il gioco, ma in tal caso che è necessario mantenere la palla da lo spostamento oltre i bordi della schermata. Abbiamo verranno effettuate durante il `RunGameLogic` codice dopo aver applicato la velocità di `ballSprite` dopo `// New Code`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
    // New Code:
    // Check if the two CCSprites overlap...
    bool doesBallOverlapPaddle = ballSprite.BoundingBoxTransformedToParent.IntersectsRect(
        paddleSprite.BoundingBoxTransformedToParent);
    // ... and if the ball is moving downward.
    bool isMovingDownward = ballYVelocity < 0;
    if (doesBallOverlapPaddle && isMovingDownward)
    {
        // First let's invert the velocity:
        ballYVelocity *= -1;
        // Then let's assign a random value to the ball's x velocity:
        const float minXVelocity = -300;
        const float maxXVelocity = 300;
        ballXVelocity = CCRandom.GetRandomFloat (minXVelocity, maxXVelocity);
    }
    // First let’s get the ball position:   
    float ballRight = ballSprite.BoundingBoxTransformedToParent.MaxX;
    float ballLeft = ballSprite.BoundingBoxTransformedToParent.MinX;
    // Then let’s get the screen edges
    float screenRight = VisibleBoundsWorldspace.MaxX;
    float screenLeft = VisibleBoundsWorldspace.MinX;
    // Check if the ball is either too far to the right or left:    
    bool shouldReflectXVelocity = 
        (ballRight > screenRight && ballXVelocity > 0) ||
        (ballLeft < screenLeft && ballXVelocity < 0);
    if (shouldReflectXVelocity)
    {
        ballXVelocity *= -1;
    }
}
```

## <a name="adding-scoring"></a>Aggiunta di assegnazione dei punteggi

Ora che il gioco è riproducibile, l'ultimo passaggio consiste nell'aggiungere logica di assegnazione dei punteggi. In primo luogo, verrà aggiunto un membro di punteggio per la classe GameLayer denominata `score`:

```csharp
int score;
```

Questa variabile verrà utilizzata per tenere traccia del punteggio del lettore e da visualizzare tramite il `scoreLabel`. Per eseguire questo aggiungere il seguente codice all'interno dell'istruzione if in `RunGameLogic` quando la palla e racchetta si sovrappongono:

```csharp
// ...
if (doesBallOverlapPaddle && isMovingDownward)
{
    // First let's invert the velocity:
    ballYVelocity *= -1;
    // Then let's assign a random to the ball's x velocity:
    const float minXVelocity = -300;
    const float maxXVelocity = 300;
    ballXVelocity = CCRandom.GetRandomFloat (minXVelocity, maxXVelocity);
    // New code:
    score++;
    scoreLabel.Text = "Score: " + score;
}
// ...
```

Ora possiamo eseguire il gioco e vedere che il gioco consente di visualizzare un punteggio che incrementa come la palla aperture di fuori di racchetta:

![Il gioco completato, con un punteggio a incremento](bouncing-game-images/image1.png "gioco completato, con un punteggio di incremento")

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata presentato la creazione di un gioco multipiattaforma con input utilizzando CocosSharp grafica e fisica. È il primo passaggio nella Guida introduttiva di sviluppo di giochi CocosSharp. È stato esaminato alcune classi comunemente CocosSharp come costruire un struttura ad albero visuale in modo oggetti sottoposti a rendering in modo corretto e come implementare la logica di ogni frame del gioco.

Questa procedura dettagliata descritta solo una piccola parte di ciò che il motore di giochi CocosSharp offre. Per informazioni e procedure dettagliate in altri argomenti CocosSharp, vedere [il resto delle guide CocosSharp](~/graphics-games/cocossharp/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [Gioco completato (esempio)](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [Contenuto del gioco (esempio)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
