---
title: Dettagli di BouncingGame
description: Questo documento fornisce una procedura dettagliata per la creazione di BouncingGame, un semplice gioco palla rimbalzo compilato con CocosSharp.
ms.prod: xamarin
ms.assetid: AC9FD56F-6E4A-40DA-8168-45A761D869FD
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: 42155eb541849f365e7fab0a3d5c3ad583e760b7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109406"
---
# <a name="bouncinggame-details"></a>Dettagli di BouncingGame

> [!TIP]
> Il codice per BouncingGame è reperibile nel [degli esempi di Xamarin](https://developer.xamarin.com/samples/mobile/BouncingGame/). Per meglio proseguire con questa Guida, scaricare ed esplorare il codice come guida fa riferimento a essa.

Questa procedura dettagliata viene illustrato come implementare un semplice gioco palla rimbalzo con CocosSharp. 

 - Decomprimere il contenuto del gioco
 - Elementi visivi di CocosSharp comuni
 - Aggiunta di elementi visivi per `GameLayer`
 - Implementazione della logica di ogni fotogramma

Il gioco completata avrà un aspetto simile al seguente:

![BouncingGame, completata](bouncing-game-images/image1.png "BouncingGame, completata")

## <a name="unzipping-game-content"></a>Decomprimere il contenuto del gioco

Gli sviluppatori di giochi usano spesso il termine *contenuto* per fare riferimento ai file non di codice che in genere vengono creati da visual alle creazioni degli artisti, finestre di progettazione del gioco o audio finestre di progettazione. Tipi comuni di contenuto includono i file usati per visualizzare gli oggetti visivi, riprodurre un suono o controllare il comportamento di intelligenza artificiale (AI). Dalla prospettiva del team lo sviluppo di giochi, il contenuto viene in genere creato da non programmatori. Il gioco include due tipi di contenuto:

 - file PNG per definire come vengono visualizzate la palla e una racchetta.
 - Un file singolo xnb per definire il tipo di carattere utilizzato per la visualizzazione di punteggio (descritto più dettagliatamente quando si aggiunge la visualizzazione di punteggio riportato di seguito)

Questo contenuto usato in questo esempio sono disponibili nel [zip di contenuto](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true). Questi file scaricato e decompresso in una posizione che si accede più avanti in questa procedura dettagliata sarà necessario.

## <a name="common-cocossharp-visual-elements"></a>Elementi visivi di CocosSharp comuni

CocosSharp fornisce una serie di classi consente di visualizzare gli oggetti visivi. Alcuni elementi sono direttamente visibili, mentre altri vengono utilizzati per organizzazione. Di seguito verranno usati nel gioco:

 - `CCNode` – Classe di base per tutti gli oggetti visivi in CocosSharp. Il `CCNode` classe contiene un `AddChild` funzione che può essere utilizzata per costruire una gerarchia padre/figlio, noto anche come una *struttura ad albero visuale* oppure *grafico della scena*. Tutte le classi indicate di seguito ereditano `CCNode`.
 - `CCScene` – Radice della struttura visiva per i giochi CocosSharp. Tutti gli elementi visivi devono far parte di un struttura ad albero visuale con un `CCScene` nella radice, o non saranno visibili.
 - `CCLayer` – Gli oggetti contenitore per oggetto visivo, ad esempio `CCSprite`. Come suggerisce il nome, il `CCLayer` classe viene utilizzata per controllare il livello di elementi come visivo uno sopra l'altro.
 - `CCSprite` : Visualizza un'immagine o una parte di un'immagine. `CCSprite` le istanze possono essere posizionate, ridimensionata e forniscono una serie di effetti visivi.
 - `CCLabel` : Visualizza una stringa nella schermata. Il tipo di carattere utilizzato da `CCLabel` è definito in un file xnb. Tratteremo xnbs in dettaglio più avanti.

Per comprendere come vengono usati i diversi tipi sarà considerata una singola `CCSprite`. Gli elementi visivi devono essere aggiunto a un `CCLayer`, e la struttura ad albero visuale deve avere un `CCScene` la cui radice. Pertanto, la relazione padre/figlio per una singola `CCSprite` sarebbe `CCScene`  >  `CCLayer`  >  `CCSprite`.

## <a name="adding-visual-elements-to-gamelayer"></a>Aggiunta di elementi visivi a GameLayer

### <a name="adding-the-paddle-sprite"></a>Aggiunta di sprite racchetta

Inizialmente verrà impostata in background del gioco nero e anche aggiungere un singolo `CCSprite` il rendering sullo schermo. Modificare il `GameLayer` classe per aggiungere un `CCSprite` come indicato di seguito:

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

Il codice precedente crea un singolo `CCSprite` e lo aggiunge come elemento figlio di `GameLayer`. Il `CCSprite` costruttore ci permette di definire il file di immagine da utilizzare come stringa. Il nostro codice indica CocosSharp per cercare un file denominato `paddle` (l'estensione viene omesso, che saranno illustrate più avanti in questa Guida). CocosSharp cercherà i nomi dei file `paddle` nella cartella del contenuto radice (ovvero **contenuto**), nonché le cartelle aggiunte al `gameView.ContentManager.SearchPaths` (descritto nella sezione precedente).

I file verrà aggiunto direttamente alla radice **contenuto** cartella per iOS e Android. A tale scopo, pulsante destro del mouse o CTRL + clic sul **contenuto** cartella nel progetto iOS e selezionare **Add** > **Aggiungi file...** Passare a in cui è stato decompresso il contenuto in precedenza e selezionare **paddle.png**. Se viene richiesto su come aggiungere file alla cartella, è consigliabile selezionare anche il **copia** opzione:

![Il File aggiunta alla finestra di dialogo cartelle](bouncing-game-images/image2.png "Add File alla finestra di dialogo di cartella")

Successivamente, si aggiungerà il file al progetto Android. Pulsante destro del mouse o CTRL + clic sulla cartella del contenuto (che è nel **asset** cartella nei progetti Android) e selezionare **Add** > **Add Files...** . Questa volta, passare al progetto di iOS **contenuto** cartella. Quando viene richiesto su come aggiungere il file, selezionare la **aggiungere un collegamento** opzione:

![Il File aggiunta alla finestra di dialogo cartelle](bouncing-game-images/addalink.png "di aggiunta file alla finestra di dialogo di cartella")

Illustreremo perché i file devono essere aggiunte a entrambi i progetti seguenti. Ogni progetto **contenuti** cartella ora contenere il **paddle.png** file:

![Il contenuto della cartella del contenuto](bouncing-game-images/image3.png "il contenuto della cartella del contenuto")

Se eseguiamo il gioco si vedrà il `CCSprite` da disegnare:

![La racchetta, disegnata sullo schermo](bouncing-game-images/image4.png "la racchetta, disegnata sullo schermo")

### <a name="file-details"></a>Dettagli del file

Finora è stato aggiunto un singolo file al progetto e il processo è stato piuttosto semplice. È sufficiente aggiungere il **paddle.png** file per il **contenuto** cartelle e viene fatto riferimento nel codice. È opportuno esaminare alcune considerazioni quando si lavora con i file in CocosSharp.

#### <a name="capitalization"></a>Maiuscole/minuscole

Si noti che il nome del file e la stringa viene usata nel codice per accedere al file sono entrambi minuscoli. Questo avviene perché alcune piattaforme (ad esempio simulatore di iOS e desktop Windows) sono maiuscole e minuscole, mentre altre piattaforme (ad esempio dispositivi Android e iOS) sono tra maiuscole e minuscole. Si userà tutti i file di lettere minuscoli nella parte restante di questa esercitazione in modo che i file e codice rimangono multipiattaforma possibili.

#### <a name="extensions"></a>Estensioni

Il costruttore per la creazione di Sprite non include l'estensione ". png" quando si fa riferimento al file racchetta. L'estensione per i file è in genere omessi quando lavorano a progetti CocosSharp come estensioni di file per lo stesso tipo di asset può differire tra piattaforme. Ad esempio, potrebbero essere file audio AIFF, MP3 o WMA formati di file a seconda della piattaforma. Lasciare disattivato l'estensione consente lo stesso codice a tutte le piattaforme indipendentemente dall'estensione del file.

#### <a name="content-in-platform-specific-projects"></a>Nei progetti specifici della piattaforma del contenuto

A differenza della maggior parte dei file di codice che possono trovarsi in una libreria di classi Portabile, è necessario aggiungere i file di contenuto a ogni progetto specifico della piattaforma. CocosSharp richiede questa configurazione per due motivi:

1. Ogni piattaforma ha diversi **azioni di compilazione**. Il contenuto aggiunto ai progetti iOS deve utilizzare il **BundleResource** azione di compilazione. Contenuto aggiunto per i progetti Android debba usare la **AndroidAsset** azione di compilazione.
2. Alcune piattaforme richiedono i formati di file specifiche della piattaforma. Ad esempio, i file del tipo di carattere .xnb differiscono tra iOS e Android, come vedremo più avanti in questa procedura dettagliata.

Se un formato di file differiscono tra le piattaforme (ad esempio con estensione png), ogni piattaforma può usare lo stesso file, in cui una o più piattaforme possono collegare il file dallo stesso percorso.

## <a name="orientation"></a>Orientamento

Proprio come qualsiasi altra app, App CocosSharp eseguibili in orientamento verticale o orizzontale. Verranno modificati il gioco per l'esecuzione in modalità "solo verticale". In primo luogo, viene modificato il codice di risoluzione del gioco per gestire un rapporto di aspetto verticale. A questo scopo, modificare il `width` e `height` i valori la `LoadGame` metodo nella `ViewController` classe in iOS e `MainActivity` classe in Android:

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

Per modificare l'orientamento del progetto iOS, selezionare la **Info. plist** del file nei **BouncingGame.iOS** del progetto e modificare **informazioni sulla distribuzione iPhone/iPod** e il **informazioni sulla distribuzione iPad** per includere solo gli orientamenti verticale:

![Opzioni di orientamento](bouncing-game-images/image5.png "opzioni di orientamento")

### <a name="android-orientation"></a>Orientamento Android

Per modificare l'orientamento del progetto Android, aprire il file MainActivity.cs nel progetto BouncingGame.Android. Modificare la definizione dell'attributo attività in modo che specifica solo un orientamento verticale, come indicato di seguito:

```csharp
[Activity (
    Label = "BouncingGame.Android",
    AlwaysRetainTaskState = true,
    Icon = "@drawable/icon",
    Theme = "@android:style/Theme.NoTitleBar",
    ScreenOrientation = ScreenOrientation.Portrait,
    LaunchMode = LaunchMode.SingleInstance,
    MainLauncher = true,
    ConfigurationChanges = ConfigChanges.Orientation | ConfigChanges.ScreenSize | ConfigChanges.Keyboard | ConfigChanges.KeyboardHidden)
]
public class MainActivity : Activity
{ 
...
```

## <a name="default-coordinate-system"></a>Sistema di coordinate predefinito

Il codice che crea un'istanza di un `CCSprite`, imposta le `PositionX` e `PositionY` valori a 100. Per impostazione predefinita, questo significa che il `CCSprite` center verrà posizionato a 100 pixel in alto e a destra da basso a sinistra della schermata. Il sistema di coordinate può essere modificato mediante l'aggiunta di un `CCCamera` per il `CCLayer`. Microsoft non si lavora con `CCCamera` in questo progetto, ma altre informazioni sul `CCCamera` è reperibile nel [documentazione delle API CocosSharp](https://developer.xamarin.com/api/type/CocosSharp.CCLayer/).

100 pixel indicato in precedenza "game" pixel anziché pixel nell'hardware. Ciò significa che è in esecuzione lo stesso gioco in un dispositivo di una risoluzione diversa (ad esempio un iPad o un iPhone) risulterà negli oggetti da posizionare e ridimensionare correttamente rispetto allo schermo fisico. In particolare, area visibile del gioco sarà sempre 1024 pixel di altezza e larghezza 768 pixel, poiché questa è la soluzione è stato indicato in precedenza nella `LoadGame` (metodo).

## <a name="adding-the-ball-sprite"></a>Aggiunta di sprite palla

Ora che si ha familiarità con le nozioni di base dell'utilizzo `CCSprite`, verrà aggiunto il secondo `CCSprite` – una palla. Si seguiranno i passaggi che sono molto simili a come è stata creata la racchetta `CCSprite`. 

In primo luogo, si aggiungerà il **ball.png** immagine dalla cartella non compressa del progetto iOS **contenuto** cartella. Selezionare questa opzione per **copia** il file per il **contenuto** directory. Seguire gli stessi passaggi come illustrato in precedenza per aggiungere un collegamento per il **ball.png** file nel progetto Android.

A questo punto creare il `CCSprite` per la palla mediante l'aggiunta di un membro denominato `ballSprite` per il `GameScene` classe, nonché il codice di creazione dell'istanza per il `ballSprite`. Al termine di `GameLayer` classe avrà un aspetto simile al seguente:

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

È l'ultimo elemento visivo si aggiungerà al gioco un `CCLabel` per visualizzare il numero di volte in cui l'utente ha completato rimbalzare la palla. Il `CCLabel` Usa un tipo di carattere specificato nel costruttore per visualizzare le stringhe nella schermata.

Aggiungere il codice seguente per creare un `CCLabel` dell'istanza in `GameLayer`. Una volta completato il codice dovrebbe essere simile al seguente:

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

Si noti che viene usato il scoreLabel un' `AnchorPoint` dei `CCPoint.AnchorUpperLeft`, vale a dire che le `PositionX` e `PositionY` valori definiscono la posizione dell'angolo superiore sinistro. Questo modo è possibile posizione il `scoreLabel` relativo alla parte superiore sinistra della schermata senza dover prendere in considerazione le dimensioni dell'etichetta.

## <a name="implementing-every-frame-logic"></a>Implementazione della logica di ogni fotogramma

Finora, il gioco presenta una scena statica. Verranno aggiunte per la logica per controllare lo spostamento di oggetti nella scena aggiungendo il codice che aggiorna la posizione degli oggetti con una frequenza elevata. In questo caso, il codice verrà eseguito sessanta volte al secondo - noto anche come sessanta *frame* al secondo (a meno che l'hardware non è possibile gestire questo aggiornamento frequente). In particolare, verranno aggiunte per la logica per semplificare la palla rientrano e bounce contro la racchetta, per spostare la racchetta in base a input e per aggiornare il punteggio del giocatore ogni volta che la palla colpisce la racchetta.

Il `Schedule` metodo, che avviene tramite il `CCNode` classe, consente di aggiungere la logica di ogni frame del gioco. Si aggiungerà il codice dopo `// New code` al costruttore GameLayer:

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

A questo punto creare un `RunGameLogic` metodo nella `GameLayer` (classe), che dovrà contenere tutta la logica di ogni frame:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
}
```

Il parametro float definisce quanto tempo il frame è espresso in secondi. Si userà questo valore quando si implementa lo spostamento della palla.

### <a name="making-the-ball-fall"></a>Effettua la palla rientrano

È possibile inviare la palla fallback per il codice di gravità implementazione manualmente o usando la funzionalità Box2D incorporata in CocosSharp. Il motore di simulazione Box2D fisica è disponibile per giochi CocosSharp. È molto potente ed efficiente, ma richiede la scrittura di codice di installazione. Poiché la simulazione di effetti fisici è piuttosto semplice, in questo caso verrà implementato manualmente.

Per implementare la gravità è necessario per archiviare corrente X e Y della velocità della palla. Aggiungiamo due membri per il `GameLayer` classe:

```csharp
float ballXVelocity;
float ballYVelocity;
// How much to modify the ball's y velocity per second:
const float gravity = 140;
```

Successivamente è possibile implementare la logica in diminuzione in `RunGameLogic`:

```csharp
void RunGameLogic(float frameTimeInSeconds)
{
    // This is a linear approximation, so not 100% accurate
    ballYVelocity += frameTimeInSeconds * -gravity;
    ballSprite.PositionX += ballXVelocity * frameTimeInSeconds;
    ballSprite.PositionY += ballYVelocity * frameTimeInSeconds;
}
```

### <a name="moving-the-paddle-with-touch-input"></a>Spostare la racchetta con input di tocco

Ora che è rimasto la palla, si aggiungerà movimento orizzontale per la racchetta utilizzando il `CCEventListenerTouchAllAtOnce` oggetto. Questo oggetto fornisce un numero di eventi correlati all'input. In questo caso, si vuole ricevere una notifica se i punti di tocco spostano in modo che è possibile modificare la posizione della racchetta. Il `GameLayer` già crea un'istanza una `CCEventListenerTouchAllAtOnce`, pertanto è sufficiente assegnare il `OnTouchesMoved` delegare. A tale scopo, modificare il metodo AddedToScene come segue:

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

Successivamente, viene implementato `HandleTouchesMoved`:

```csharp
void HandleTouchesMoved (System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    // we only care about the first touch:
    var locationOnScreen = touches [0].Location;
    paddleSprite.PositionX = locationOnScreen.X;
}
```

### <a name="implementing-ball-collision"></a>Implementazione di collisione palla

Se eseguiamo il gioco a questo punto possiamo notare che la palla passa a eseguire la racchetta. Si implementeranno *collisione* (per la logica per reagire gli oggetti gioco che si sovrappongono) nel codice di ogni fotogramma. Poiché di Sposta gli oggetti change posiziona ogni fotogramma, il controllo delle collisioni è in genere eseguite anche ogni fotogramma. Verranno anche aggiunte velocità sull'asse X quando la palla colpisca la racchetta per aggiungere qualche sfida per il gioco, ma in tal caso che è necessario mantenere la palla da lo spostamento oltre i bordi dello schermo. A tale scopo `RunGameLogic` codice dopo l'applicazione di velocità per il `ballSprite` dopo `// New Code`:

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

Ora che il gioco è riproducibile, l'ultimo passaggio consiste nell'aggiungere la logica di assegnazione dei punteggi. In primo luogo, si aggiungerà un membro di punteggio per la classe GameLayer denominata `score`:

```csharp
int score;
```

Questa variabile verrà utilizzata per tenere traccia del punteggio del giocatore e da visualizzare tramite il `scoreLabel`. Eseguire questo aggiungere il codice seguente all'interno dell'istruzione if in `RunGameLogic` quando la palla e una racchetta sovrapporsi:

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

A questo punto possiamo eseguire il gioco e vedere che il gioco viene visualizzato un punteggio che viene incrementato quando la palla si muove di fuori la racchetta:

![Il tuo gioco completato, con un punteggio che aumentano](bouncing-game-images/image1.png "il tuo gioco completato, con un punteggio incrementale")

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata presentato la creazione di un gioco cross-platform con grafici, fisica e uso di CocosSharp input. È il primo passaggio nella Guida introduttiva di sviluppo di giochi con CocosSharp. Abbiamo esaminato alcune delle classi più comuni in CocosSharp come costruire un struttura ad albero visuale in modo che gli oggetti viene visualizzato correttamente e come implementare la logica del gioco a ogni fotogramma.

Questa procedura dettagliata illustrata solo una piccola parte di ciò che offre il motore di gioco CocosSharp. Per informazioni e procedure dettagliate in altri argomenti CocosSharp, vedere [il resto delle guide CocosSharp](~/graphics-games/cocossharp/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [Gioco completato (esempio)](https://developer.xamarin.com/samples/mobile/BouncingGame/)
- [Contenuto del gioco (esempio)](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Content.zip?raw=true)
