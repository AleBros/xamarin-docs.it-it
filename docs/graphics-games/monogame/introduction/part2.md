---
title: 'Parte 2: implementazione di WalkingGame'
description: Questa procedura dettagliata viene illustrato come aggiungere logica del gioco e al contenuto di un progetto vuoto MonoGame per creare una demo di un sprite animata lo spostamento con input tocco.
ms.topic: article
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 2bc8f5448e249a801edfc88726f3dfb5d24b158d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="part-2--implementing-the-walkinggame"></a>Parte 2: implementazione di WalkingGame

_Questa procedura dettagliata viene illustrato come aggiungere logica del gioco e al contenuto di un progetto vuoto MonoGame per creare una demo di un sprite animata lo spostamento con input tocco._

Le parti di questa procedura dettagliata precedente è stato illustrato come creare progetti MonoGame vuoti. In queste parti precedente verrà compilata, eseguendo una semplice gioco demo. In questo articolo sono contenute le sezioni seguenti:

 - Decomprimere il contenuto di gioco
 - Cenni preliminari sulla classe MonoGame
 - Il primo Sprite di rendering
 - Creazione di CharacterEntity
 - Aggiunta di CharacterEntity per il gioco
 - Creazione della classe di animazione
 - Aggiungere la prima animazione CharacterEntity
 - Aggiunta di spostamento per il carattere
 - Lo spostamento e animazione corrispondenti


# <a name="unzipping-our-game-content"></a>Decomprimere il contenuto di gioco

Prima di iniziare la scrittura di codice, è possibile decomprimere il gioco *contenuto*. Sviluppatori di giochi usano spesso il termine *contenuto* per fare riferimento a file non di codice che vengono generalmente creati dal visual artisti, finestre di progettazione del gioco o audio finestre di progettazione. Tipi di contenuto comuni includono file utilizzati per visualizzare gli oggetti visivi, riprodurre un suono o controllare il comportamento di intelligenza artificiale (AI). Di sviluppo di giochi contenuto prospettiva del team viene creato in genere non programmatori.

Il contenuto utilizzato qui sono reperibili [su github](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true). È necessario che questi file scaricati in un percorso a cui si accederà più avanti in questa procedura dettagliata.


# <a name="monogame-class-overview"></a>Cenni preliminari sulla classe MonoGame

Per i principianti esamineremo le classi MonoGame utilizzate nel rendering di base:

 - `SpriteBatch` : usato per disegnare la grafica 2D sullo schermo. *Sprite* sono 2D elementi visivi che consentono di visualizzare immagini sullo schermo. Il `SpriteBatch` oggetto consente di disegnare un singolo sprite alla volta tra relativo `Begin` e `End` più Sprite o metodi possono essere raggruppati, o *batch*.
 - `Texture2D` : rappresenta un oggetto immagine in fase di esecuzione. `Texture2D` le istanze vengono spesso create dai formati di file, ad esempio con estensione PNG o BMP, anche se possono anche essere creati in modo dinamico in fase di esecuzione. `Texture2D` le istanze vengono utilizzate durante il rendering con `SpriteBatch` istanze.
 - `Vector2` : rappresenta una posizione in un sistema di coordinate 2D spesso utilizzato per il posizionamento degli oggetti visivi. Include inoltre MonoGame `Vector3` e `Vector4` ma si utilizzerà solo `Vector2` in questa procedura dettagliata.
 - `Rectangle` : un'area quattro lati con la posizione, larghezza e altezza. Verrà usato questo per definire la parte del nostro `Texture2D` per eseguire il rendering quando si inizia con le animazioni.

Si noti inoltre che MonoGame non è gestito da Microsoft: nonostante lo spazio dei nomi. Il `Microsoft.Xna` dello spazio dei nomi viene usato in MonoGame per semplificare la migrazione di progetti XNA esistenti a MonoGame.


# <a name="rendering-our-first-sprite"></a>Il primo Sprite di rendering

Successivamente eseguire il disegno sprite singolo sullo schermo per mostrare come eseguire il rendering 2D in MonoGame.


## <a name="creating-a-texture2d"></a>Creazione di un Texture2D

È necessario creare un `Texture2D` istanza da utilizzare durante il rendering dei nostri sprite. Tutto il contenuto del gioco in definitiva è contenuto in una cartella denominata **contenuto** si trova nel progetto specifico della piattaforma. Progetti MonoGame condiviso non possono contenere contenuto, come il contenuto è necessario usare le azioni di compilazione specifiche della piattaforma. Gli sviluppatori CocosSharp trovare la cartella del contenuto un concetto familiare: si trovano nella stessa posizione nei progetti CocosSharp sia MonoGame. La cartella del contenuto è reperibile nel progetto iOS e nella cartella Assets nel progetto Android.

Per aggiungere il contenuto del nostro gioco, fare clic su di **contenuto** cartella e selezionare **Aggiungi > Aggiungi file...** Passare al percorso in cui è stato estratto il file content.zip e selezionare il **charactersheet.png** file. Se frequenti su come aggiungere file alla cartella, è necessario selezionare il **copia** opzione:

![](part2-images/image1.png "Se viene richiesto su come aggiungere file alla cartella, selezionare l'opzione di copia")

La cartella del contenuto contiene ora il file charactersheet.png:

![](part2-images/image2.png "La cartella del contenuto contiene ora il file charactersheet.png")

Successivamente, verrà aggiunto il codice per caricare il file charactersheet.png e creare un `Texture2D`. Per eseguire questa operazione, aprire il `Game1.cs` file e aggiungere il campo seguente alla classe Game1.cs:


```csharp
Texture2D characterSheetTexture;
```

Successivamente, si creerà il `characterSheetTexture` nel `LoadContent` metodo. Prima di eventuali modifiche `LoadContent` metodo è simile al seguente:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```
Si noti che il progetto predefinito già creata un'istanza di `spriteBatch` istanza riservata. Verrà usato questo in un secondo momento, ma che non aggiungendo codice aggiuntivo per preparare il `spriteBatch` per l'utilizzo. D'altra parte, il nostro `spriteSheetTexture` richiedono l'inizializzazione, pertanto si inizializzerà dopo il `spriteBatch` viene creato:


```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

Ora che è disponibile un `SpriteBatch` istanza e un `Texture2D` istanza che è possibile aggiungere il codice per il rendering per il `Game1.Draw` metodo:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Esecuzione del gioco ora mostra un singolo sprite la visualizzazione di trama creata da charactersheet.png:

![](part2-images/image3.png "Esecuzione del gioco ora mostra un singolo sprite la visualizzazione di trama creata da charactersheet.png")


#  <a name="creating-the-characterentity"></a>Creazione di CharacterEntity

Finora è stato aggiunto il codice per il rendering di un singolo sprite sullo schermo. Tuttavia, se si dovesse sviluppare un gioco completo senza la creazione di altre classi, è viene eseguita in problemi di organizzazione del codice.


## <a name="what-is-an-entity"></a>Che cos'è un'entità?

Un modello comune per l'organizzazione di codice del gioco consiste nel creare una nuova classe per ogni partita *entità* tipo. Sviluppo di giochi per un'entità è un oggetto che può contenere alcune delle caratteristiche seguenti (non tutti sono obbligatori):

 - Un elemento visivo, ad esempio un sprite, un testo o un modello 3D
 - Ogni comportamento di frame, ad esempio un'unità patrolling un set di percorso o un carattere di Windows Media player risponde di input o fisica
 - Possono essere creati e distrutti in modo dinamico, ad esempio una visualizzazione accensione e raccolti da Windows Media player

Sistemi di entità dell'organizzazione possono essere complessi e molti motori di giochi offrono le classi che consentono di gestire le entità. Si sarà implementare un sistema di entità molto semplice, pertanto è importante notare che giochi complete in genere richiedono ulteriori organizzazione parte dello sviluppatore.


## <a name="defining-the-characterentity"></a>Definizione di CharacterEntity

L'entità, che chiameremo `CharacterEntity`, avrà le caratteristiche seguenti:

 - La possibilità di caricare il proprio `Texture2D`
 - La possibilità di eseguire il rendering, inclusi che contiene i metodi di chiamata per aggiornare l'animazione di analisi
 - `X `proprietà e Y per controllare la posizione del carattere.
 - La possibilità di aggiornamento, in particolare, per leggere i valori dal tocco schermata e regolare posizione, in modo appropriato.

Per aggiungere il `CharacterEntity` per il gioco, pulsante destro del mouse o fare clic sul controllo di **WalkingGame** del progetto e selezionare **Aggiungi > Nuovo File...** . Selezionare il **classe vuota** opzione e denominare il nuovo file **CharacterEntity**, quindi fare clic su **New**.

Innanzitutto verrà aggiunto il possibilità per il `CharacterEntity` per caricare un `Texture2D` nonché per il disegno. Si modificherà l'aggiunte `CharacterEntity.cs` file come segue:


```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;


namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

Il codice sopra riportato aggiunge la responsabilità di posizionamento, il rendering e il caricamento del contenuto per il `CharacterEntity`. È opportuno notare alcune considerazioni impiegati nel codice precedente.


## <a name="why-are-x-and-y-floats"></a>Perché sono X e Y float?

Gli sviluppatori che hanno familiarità con la programmazione di gioco potrebbero chiedere perché il `float` viene usato il tipo anziché `int` o `double`. Il motivo è che un valore a 32 bit è più comune per il posizionamento nel codice di basso livello per il rendering. Il tipo double occupa 64 bit di precisione, raramente necessaria per il posizionamento di oggetti. Una differenza a 32 bit potrebbe sembrare non significativa, molti dei giochi moderni includono grafica che richiedono l'elaborazione di decine di migliaia di valori di posizione ogni frame (30 o 60 volte al secondo). Taglia la quantità di memoria che devono essere spostati tramite la grafica della pipeline metà può avere un impatto significativo sulle prestazioni di un gioco.

Il `int` tipo di dati può essere un'unità di misura per il posizionamento appropriata, ma è possibile impostare limitazioni per il modo in cui vengono posizionate le entità. Ad esempio, utilizzando i valori interi rende più difficile implementare movimento uniforme per i giochi che supportano lo zoom avanti o fotocamere 3D (che sono consentite dai `SpriteBatch`).

Infine, si noterà che la logica che consente di spostare il carattere sullo schermo verrà effettuare l'operazione utilizzando i valori di intervallo del gioco. Il risultato della moltiplicazione tra velocità e il tempo trascorso un determinato intervallo di raramente comporterà un numero intero, pertanto è necessario utilizzare `float` per `X` e `Y`.


## <a name="why-is-charactersheettexture-static"></a>Motivo per cui è characterSheetTexture statico?

Il `characterSheetTexture` `Texture2D` istanza è una rappresentazione di runtime del file charactersheet.png. In altre parole, contiene i valori di colore per ciascun pixel estratti dall'origine **charactersheet.png** file. Se il gioco creare due `CharacterEntity` istanze, ciascuna di esse devono accedere alle informazioni da charactersheet.png. In questa situazione non si desidera incorrere nell'onere delle prestazioni del caricamento charactersheet.png due volte, né si vuole disporre di memoria di trama duplicati archiviata nella ram. Utilizzando un `static` campo consente di condividere lo stesso `Texture2D` tutti `CharacterEntity` istanze.

Utilizzando `static` membri per la rappresentazione di runtime del contenuto è una soluzione semplice e che non è risolvere i problemi verificati nei giochi più grande, ad esempio lo scaricamento del contenuto durante lo spostamento da un livello in un altro. Le soluzioni più sofisticate, che non rientrano nell'ambito di questa procedura dettagliata, includano tramite pipeline di MonoGame contenuto o la creazione di un sistema di gestione dei contenuti personalizzato.


## <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>Perché è SpriteBatch passato come un argomento anziché creare un'istanza di entità?

Il `Draw` , implementato sopra accetta un `SpriteBatch` argomento, ma al contrario, il `characterSheetTexture` viene creata un'istanza mediante il `CharacterEntity`.

Questo accade perché è possibili quando il rendering più efficiente la stessa `SpriteBatch` istanza viene utilizzata per tutti i `Draw` chiamate e quando tutti `Draw` vengono eseguite chiamate tra un singolo set di `Begin` e `End` chiamate. Naturalmente, il gioco includerà solo una sola istanza di entità, ma è più complicati giochi trarranno vantaggio dal modello che consente a più entità di utilizzare lo stesso `SpriteBatch` istanza.


# <a name="adding-characterentity-to-the-game"></a>Aggiunta di CharacterEntity per il gioco

Ora che è stato aggiunto il nostro `CharacterEntity` con il codice per il rendering di se stesso, è possibile sostituire il codice in `Game1.cs` per utilizzare un'istanza di questa nuova entità. A tale scopo è possibile sostituire il `Texture2D` campo con un `CharacterEntity` campo `Game1`:


```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

Successivamente, aggiungeremo la creazione di istanze di questa entità in `Game1.Initialize`:


```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

È necessario rimuovere anche il `Texture2D` creazione da `LoadContent` dal momento che è ora gestita all'interno del nostro `CharacterEntity`. `Game1.LoadContent` dovrebbe essere simile al seguente:


```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

Vale la pena notare che, nonostante il nome di `LoadContent` metodo non è l'unico punto in cui il contenuto può essere caricato: molti implementare giochi è contenuto il caricamento nel metodo Initialize, o anche nel proprio codice di aggiornamento come contenuto, potrebbe non essere necessarie fino a quando il giocatore raggiunge un determinato punto del gioco.

È infine possibile modificare il metodo di disegno come segue:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Se si esegue il gioco, si verrà ora visualizzato il carattere. Poiché X e Y predefinito a 0, il carattere è posizionato in alto a sinistra dello schermo:

![](part2-images/image4.png "Poiché X e Y predefinito a 0, il carattere è posizionato in alto a sinistra della schermata")


# <a name="creating-the-animation-class"></a>Creazione della classe di animazione

Attualmente il nostro `CharacterEntity` Visualizza la versione completa **charactersheet.png** file. Questa disposizione di più immagini in un file viene definita un *foglio sprite*. In genere, un sprite verrà eseguito il rendering solo una parte del foglio di sprite. Si modificherà il `CharacterEntity` per eseguire il rendering di una parte di questo **charactersheet.png**, e questa parte cambia nel corso del tempo per visualizzare un'animazione in movimento.

Verrà creata la `Animation` classe per controllare la logica e stato dell'animazione CharacterEntity. La classe di animazione sarà una classe generale che potrebbe essere usata per qualsiasi entità, non solo `CharacterEntity` animazioni. Ultimate il `Animation` classe fornirà un `Rectangle` che il `CharacterEntity` verrà utilizzato per disegnare se stessa. Verrà creata anche una `AnimationFrame` classe che verrà utilizzato per definire l'animazione.


## <a name="defining-animationframe"></a>Definizione AnimationFrame

`AnimationFrame` non conterrà alcuna logica correlata all'animazione. Verrà usato solo per archiviare i dati. Per aggiungere il `AnimationFrame` classe, rapida o fare clic sul controllo di **WalkingGame** condiviso progetto e selezionare **Aggiungi > Nuovo File...** Immettere il nome **AnimationFrame** e fare clic su di **New** pulsante. Verrà modificata la `AnimationFrame.cs` file in modo che contenga il codice seguente:


```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

La `AnimationFrame` classe contiene due tipi di informazioni:

 - `SourceRectangle` : Definisce l'area della `Texture2D` che verranno visualizzate la `AnimationFrame`. Questo valore viene misurato in pixel, con il superiore sinistro (0, 0).
 - `Duration` : Definisce quanto tempo un `AnimationFrame` viene visualizzata quando utilizzato un `Animation`.


## <a name="defining-animation"></a>Definizione di animazione

Il `Animation` classe conterrà un `List<AnimationFrame>` nonché la logica per passare il frame è attualmente visualizzato in base a quanto tempo è trascorso.

Per aggiungere il `Animation` classe, rapida o fare clic sul controllo di **WalkingGame** condiviso progetto e selezionare **Aggiungi > Nuovo File...** . Immettere il nome **animazione** e fare clic su di **New** pulsante. Verrà modificata la `Animation.cs` file in modo che contenga il codice seguente:


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;


            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

Ecco alcuni dettagli dalla `Animation` classe.


## <a name="frames-list"></a>Elenco di frame

Il `frames` membro è ciò che archivia i dati per l'animazione. Verrà aggiunto il codice che crea le animazioni `AnimationFrame` istanze per il `frames` elenco tramite la `AddFrame` metodo. Può offrire un'implementazione più completa `public` metodi o proprietà per la modifica di `frames`, ma ci limiteremo la funzionalità per l'aggiunta di frame per questa procedura dettagliata.


## <a name="duration"></a>Durata

Durata restituisce la durata totale del `Animation,` ottenuta aggiungendo la durata di tutti i contenuti `AnimationFrame` istanze. Questo valore può essere memorizzato nella cache se `AnimationFrame` sono stati di un oggetto non modificabile, ma poiché AnimationFrame è implementato come classe che può essere modificata dopo l'aggiunta di animazione, è necessario calcolare tale valore ogni volta che si accede alla proprietà.


## <a name="update"></a>Aggiorna

Il `Update` metodo deve essere chiamato ogni frame (ovvero ogni volta che viene aggiornato il gioco intero). Il suo scopo consiste nell'aumentare il `timeIntoAnimation` membro che viene utilizzata per restituire il frame attualmente visualizzato. La logica `Update` impedisce il `timeIntoAnimation` in modo che vengano sempre più grande rispetto alla durata dell'intera animazione.

Poiché verrà usato il `Animation` classe per visualizzare un'animazione in movimento, è necessario ripetere una volta raggiunta la fine di questa animazione. A questo scopo, è possibile controllare se il `timeIntoAnimation` è maggiore di `Duration` valore. In tal caso verrà fino all'inizio del ciclo e mantenere il resto, in una ciclo di animazione.


## <a name="obtaining-the-current-frames-rectangle"></a>Ottenere il rettangolo del Frame corrente

Lo scopo del `Animation` classe è alla fine di fornire un `Rectangle` che può essere usato per disegnare un sprite. Attualmente il `Animation` classe contiene la logica per la modifica di `timeIntoAnimation` membro, che verrà usato per ottenere che `Rectangle` deve essere visualizzato. Faremo questo creando un `CurrentRectangle` proprietà la `Animation` classe. Copiare questa proprietà nel `Animation` classe:

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime;
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

# <a name="adding-the-first-animation-to-characterentity"></a>Aggiungere la prima animazione CharacterEntity

Il `CharacterEntity` conterrà le animazioni per analisi e permanente, nonché un riferimento all'oggetto corrente `Animation` venga visualizzata nuovamente.

In primo luogo, verrà aggiunto il primo `Animation,` che verrà usato per testare la funzionalità come finora scritti. Aggiungere i membri seguenti alla classe CharacterEntity:


```csharp
Animation walkDown;
Animation currentAnimation;
```

 Successivamente, è pertanto possibile definire il `walkDown` animazione. Per effettuare questa modifica il `CharacterEntity` costruttore come indicato di seguito:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```
Come accennato in precedenza, è necessario chiamare `Animation.Update` delle animazioni basate sul tempo per la riproduzione. È anche necessario assegnare il `currentAnimation`. Per questo punto si verrà assegnato il `currentAnimation` a `walkDown`, ma si sarà da sostituire questo codice in un secondo momento quando si implementa la logica di spostamento. Verrà aggiunto il `Update` metodo `CharacterEntity` come indicato di seguito:


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Dopo aver creato il `currentAnimation` viene assegnato e aggiornato, è possibile usarlo per eseguire il disegno. Verrà modificata `CharacterEntity.Draw` come indicato di seguito:


```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

L'ultimo passaggio per ottenere il `CharacterEntity` animazione consiste nel chiamare appena aggiunta `Update` metodo `Game1`. Modificare `Game1.Update` come indicato di seguito:


```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

A questo punto il `CharacterEntity` giocano relativo `walkDown` animazione:

![](part2-images/image5.gif "A questo punto il CharacterEntity verrà riprodotta l'animazione walkDown")


# <a name="adding-movement-to-the-character"></a>Aggiunta di spostamento per il carattere

Successivamente, aggiungeremo lo spostamento dei nostri carattere tramite tocco. Quando l'utente tocca lo schermo, il carattere verrà spostato verso il punto in cui viene eseguita la schermata. Se non viene rilevate tocca, il carattere verrà evidenziati sul posto.


## <a name="defining-getdesiredvelocityfrominput"></a>Defining GetDesiredVelocityFromInput

Verrà usato del MonoGame `TouchPanel` (classe), che fornisce informazioni sullo stato corrente del touch screen. Aggiungere un metodo che controlla il `TouchPanel` e restituire velocità desiderata del nostro carattere:


```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

Il `TouchPanel.GetState` metodo restituisce un `TouchCollection` che contiene informazioni in cui l'utente tocca lo schermo. Se l'utente non è toccare lo schermo, quindi il `TouchCollection` sarà vuoto, nel qual caso è non è consigliabile spostare il carattere.

Se l'utente tocca lo schermo, il carattere per il primo tocco, verrà spostato in altre parole, il `TouchLocation` in corrispondenza dell'indice 0. Inizialmente verrà impostata la velocità desiderata per la differenza tra la posizione del carattere e la posizione del primo tocco è uguale:


```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

Quello che segue è un po' di matematica che manterrà il carattere di spostamento con la stessa velocità. Per spiegare perché questo è importante, si consideri una situazione in cui l'utente tocca il 500 pixel lontano in cui si trova il carattere. La prima riga in cui `desiredVelocity.X` è set verrebbe assegnato un valore pari a 500. Tuttavia, se l'utente sono stati toccare lo schermo a una distanza di unità di 100 solo dal carattere, il `desiredVelocity.X `verrebbe impostato su 100. Il risultato sarebbe che velocità del movimento del carattere avrebbe risposto come lontano che è il punto di contatto tra il carattere. Poiché si desidera che il carattere di spostare sempre la stessa velocità, è necessario modificare il desiredVelocity.

Il `if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)` istruzione verifica se la velocità è non zero: in altre parole, controllata per verificare che l'utente non tocca stesso punto come posizione del carattere corrente. Se non, quindi è necessario impostare la velocità del carattere sia costante indipendentemente da lontano il tocco è. Questo scopo normalizzare il vettore di velocità determinando, che è una lunghezza pari a 1. Vettore di velocità pari a 1 indica che il carattere verrà spostati a 1 pixel al secondo. Si sarà velocizzare questa moltiplicando il valore per la velocità di 200 desiderata.


## <a name="applying-velocity-to-position"></a>L'applicazione di velocità per posizione

La velocità restituita da `GetDesiredVelocityFromInput` deve essere applicato al carattere `X` e `Y` valori abbia effetto in fase di esecuzione. Verrà modificata la `Update` metodo come indicato di seguito:


```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Quali sono stati implementati viene chiamato *basati sul tempo* lo spostamento (in contrapposizione a *basati su frame* lo spostamento). Lo spostamento basato sul tempo moltiplica un valore di velocità (in questo caso i valori archiviati nel `velocity` variabile) per quanto tempo è trascorsi dall'ultimo aggiornamento che viene archiviato in `gameTime.ElapsedGameTime.TotalSeconds`. Se il gioco è in esecuzione un minor numero di fotogrammi al secondo, aumenta il tempo trascorso tra fotogrammi: il risultato finale è che gli oggetti utilizzando basati sul tempo di spostamento verranno spostati sempre con la stessa velocità indipendentemente dalla frequenza dei fotogrammi.

Se si esegue ora il gioco, si noterà che il carattere è verso la posizione di tocco:

![](part2-images/image6.gif "Il carattere è lo spostamento verso la posizione di tocco")


# <a name="matching-movement-and-animation"></a>Lo spostamento e animazione corrispondenti

Una volta che il carattere, lo spostamento e la riproduzione di una singola animazione, è possibile definire la parte restante del nostro animazioni, quindi utilizzarli in modo da riflettere lo spostamento del carattere. Quando termine saranno disponibili le otto animazioni in totale:

 - Animazioni per risalire, verso il basso, a sinistra e a destra
 - Le animazioni per permanente ancora e rivolto verso l'alto, verso il basso, a sinistra e destra


## <a name="defining-the-rest-of-the-animations"></a>Definire il resto delle animazioni

Innanzitutto verrà aggiunto il `Animation` istanze per il `CharacterEntity` classe per tutti i nostri animazioni nella stessa posizione in cui è stato aggiunto `walkDown`. Una volta è eseguire questa operazione, il `CharacterEntity` avrà il seguente `Animation` membri:


```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

Ora è verranno definite le animazioni nel `CharacterEntity` costruttore come indicato di seguito:


```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Si noti che il codice precedente è stato aggiunto per il `CharacterEntity` costruttore per mantenere questa procedura dettagliata più breve. In genere i giochi verranno separare la definizione delle animazioni di carattere nelle proprie classi o caricare queste informazioni da un formato di dati, ad esempio XML o JSON.

Successivamente, si sarà regolare la logica per utilizzare le animazioni in base alla direzione che sta passando il carattere o in base l'ultima animazione, se il carattere è appena stato arrestato. A tale scopo, verrà modificata la `Update` metodo:


```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;


    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

Il codice per passare le animazioni è suddiviso in due blocchi. Il primo controlla se la velocità non è uguale a `Vector2.Zero` – questo vuol dire che il carattere è mobile. Se il carattere è mobile, quindi è possibile esaminare il `velocity.X` e `velocity.Y` valori per determinare l'animazione analisi per la riproduzione.

Se il carattere non è lo spostamento, quindi sarà necessario impostare il carattere `currentAnimation` a un'animazione permanente, ma è solo procedere se il `currentAnimation` è un percorso animazione o se non è stata impostata un'animazione. Se il `currentAnimation` non è una delle quattro animazioni analisi, quindi il carattere è già permanente in modo che non è necessario modificare `currentAnimation`.

Il risultato di questo codice è che il carattere verrà animare correttamente quando si esamina e quindi devono affrontare la direzione ultimo che è stato analisi quando si interrompe:

![](part2-images/image7.gif "Il risultato di questo codice è che il carattere verrà animare correttamente quando si esamina e quindi devono affrontare la direzione ultimo che è stato analisi quando si interrompe")


# <a name="summary"></a>Riepilogo

Questa procedura dettagliata è stato illustrato come utilizzare MonoGame per creare una libreria multipiattaforma gioco con Sprite, lo spostamento di oggetti, il rilevamento di input e animazione. Vengono illustrate la creazione di una classe generica di animazione. Inoltre mostrato come creare un'entità carattere per l'organizzazione logica del codice.

## <a name="related-links"></a>Collegamenti correlati

- [Risorsa immagine CharacterSheet (esempio)](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [Percorso completo di gioco (esempio)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
