---
title: 'Parte 2: implementazione di WalkingGame'
description: Questa procedura dettagliata illustra come aggiungere la logica del gioco e il contenuto a un progetto MonoGame vuoto per creare una demo di uno sprite animato che si muove con l'input tocco.
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 44ba9188a059cc28c7b4d89143cef1921a0b1701
ms.sourcegitcommit: 41a029c69925e3a9d2de883751ebfd649e8747cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68978474"
---
# <a name="part-2--implementing-the-walkinggame"></a>Parte 2: implementazione di WalkingGame

_Questa procedura dettagliata illustra come aggiungere la logica del gioco e il contenuto a un progetto MonoGame vuoto per creare una demo di uno sprite animato che si muove con l'input tocco._

Nelle parti precedenti di questa procedura dettagliata è stato illustrato come creare progetti monogame vuoti. In queste parti precedenti si creerà una semplice demo di gioco. In questo articolo sono contenute le sezioni seguenti:

- Decomprimere il contenuto del gioco
- Panoramica della classe monogame
- Rendering del primo sprite
- Creazione di CharacterEntity
- Aggiunta di CharacterEntity al gioco
- Creazione della classe Animation
- Aggiunta della prima animazione a CharacterEntity
- Aggiunta dello spostamento al carattere
- Spostamento e animazione corrispondenti

## <a name="unzipping-our-game-content"></a>Decomprimere il contenuto del gioco

Prima di iniziare a scrivere il codice, è necessario decomprimere il *contenuto*del gioco. Gli sviluppatori di giochi usano spesso il termine *contenuto* per fare riferimento a file non di codice che vengono in genere creati da artisti visivi, progettisti di giochi o finestre di progettazione audio. I tipi comuni di contenuto includono i file usati per visualizzare gli oggetti visivi, riprodurre un suono o controllare il comportamento di intelligenza artificiale. Dal punto di vista del team di sviluppo di giochi, il contenuto viene in genere creato da programmatori non.

Il contenuto usato qui è reperibile [in GitHub](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true). Questi file saranno necessari per il download in un percorso a cui si accederà più avanti in questa procedura dettagliata.

## <a name="monogame-class-overview"></a>Panoramica della classe monogame

Per i principianti si esploreranno le classi monogame usate nel rendering di base:

- `SpriteBatch`: usato per creare grafica 2D sullo schermo. Gli *sprite* sono elementi visivi 2D utilizzati per visualizzare immagini sullo schermo. L' `SpriteBatch` oggetto può creare un singolo sprite alla volta tra i relativi `Begin` metodi `End` e oppure più sprite possono essere raggruppati o in *batch*.
- `Texture2D`: rappresenta un oggetto immagine in fase di esecuzione. `Texture2D`le istanze vengono spesso create da formati di file come PNG o BMP, sebbene possano essere create anche in modo dinamico in fase di esecuzione. `Texture2D`le istanze vengono utilizzate per il `SpriteBatch` rendering con le istanze di.
- `Vector2`: rappresenta una posizione in un sistema di coordinate 2D usato spesso per posizionare oggetti visivi. Monogame include `Vector3` anche e `Vector4` ma verrà usato `Vector2` solo in questa procedura dettagliata.
- `Rectangle`: area su quattro lati con posizione, larghezza e altezza. Questo verrà usato per definire la parte del `Texture2D` di cui eseguire il rendering quando si inizia a lavorare con le animazioni.

Si noti anche che monogame non è gestito da Microsoft, nonostante il relativo spazio dei nomi. Lo `Microsoft.Xna` spazio dei nomi viene usato in monogame per semplificare la migrazione di progetti XNA esistenti a MonoGame.

## <a name="rendering-our-first-sprite"></a>Rendering del primo sprite

Verrà quindi disegnato un singolo sprite sullo schermo per illustrare come eseguire il rendering 2D in monogame.

### <a name="creating-a-texture2d"></a>Creazione di un Texture2D

È necessario creare un' `Texture2D` istanza da usare quando si esegue il rendering dello sprite. Tutto il contenuto del gioco è in definitiva contenuto in una cartella denominata **Content,** che si trova nel progetto specifico della piattaforma. I progetti condivisi monogame non possono contenere contenuto, perché il contenuto deve usare azioni di compilazione specifiche della piattaforma. La cartella Content si trova nel progetto iOS e all'interno della cartella assets nel progetto Android.

Per aggiungere il contenuto del gioco, fare clic con il pulsante destro del mouse sulla cartella **contenuto** e scegliere **Aggiungi > Aggiungi file...** Passare al percorso in cui è stato estratto il file content. zip e selezionare il file **charactersheet. png** . Se viene richiesto di aggiungere il file alla cartella, è necessario selezionare l'opzione **copia** :

![Se viene richiesto di aggiungere il file alla cartella, selezionare l'opzione copia](part2-images/image1.png)

La cartella Content contiene ora il file charactersheet. png:

![La cartella Content contiene ora il file charactersheet. png](part2-images/image2.png)

Successivamente, verrà aggiunto il codice per caricare il file charactersheet. png e creare un `Texture2D`. A tale scopo, aprire `Game1.cs` il file e aggiungere il campo seguente alla classe Game1.cs:

```csharp
Texture2D characterSheetTexture;
```

Successivamente, verrà creato `characterSheetTexture` `LoadContent` nel metodo. Prima che qualsiasi `LoadContent` metodo di modifica abbia un aspetto simile al seguente:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

Si noti che il progetto predefinito crea già un'istanza dell' `spriteBatch` istanza per noi. Verrà usato in un secondo momento, ma non verrà aggiunto altro codice per preparare il per `spriteBatch` l'uso. D'altra parte, `spriteSheetTexture` richiede l'inizializzazione, quindi verrà inizializzata dopo la creazione di: `spriteBatch`

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
    {
        characterSheetTexture = Texture2D.FromStream (this.GraphicsDevice, stream);

    }
}
```

Ora che sono disponibili un' `SpriteBatch` istanza e un' `Texture2D` istanza, è possibile aggiungere il `Game1.Draw` codice di rendering al metodo:

```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    spriteBatch.Begin ();

    Vector2 topLeftOfSprite = new Vector2 (50, 50);
    Color tintColor = Color.White;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);

    spriteBatch.End ();

    base.Draw(gameTime);
}
```

L'esecuzione del gioco ora Mostra un singolo sprite che visualizza la trama creata da charactersheet. png:

![L'esecuzione del gioco ora Mostra un singolo sprite che visualizza la trama creata da charactersheet. png](part2-images/image3.png)

## <a name="creating-the-characterentity"></a>Creazione di CharacterEntity

Finora è stato aggiunto il codice per eseguire il rendering di un singolo sprite sullo schermo; Tuttavia, in caso di sviluppo di un gioco completo senza creare altre classi, si verificano problemi nell'organizzazione del codice.

### <a name="what-is-an-entity"></a>Che cos'è un'entità?

Un modello comune per organizzare il codice del gioco consiste nel creare una nuova classe per ogni tipo di *entità* di gioco. Un'entità nello sviluppo di giochi è un oggetto che può contenere alcune delle seguenti caratteristiche (non tutte sono obbligatorie):

- Elemento visivo, ad esempio uno sprite, un testo o un modello 3D
- Comportamento fisico o di tutti i frame, ad esempio un'unità che sorveglia un percorso set o un carattere del lettore che risponde all'input
- Può essere creata ed eliminata in modo dinamico, ad esempio un'alimentazione che viene visualizzata e raccolta dal lettore

I sistemi di organizzazione di entità possono essere complessi e molti motori di gioco offrono classi per facilitare la gestione delle entità. Verrà implementato un sistema di entità molto semplice, quindi è importante sottolineare che i giochi completi richiedono in genere più organizzazioni per la parte dello sviluppatore.

### <a name="defining-the-characterentity"></a>Definizione del CharacterEntity

La nostra entità, che `CharacterEntity`chiameremo, avrà le caratteristiche seguenti:

- Possibilità di caricare i propri`Texture2D`
- Possibilità di eseguire il rendering, inclusi i metodi di chiamata per aggiornare l'animazione a distanza
- `X`e proprietà Y per controllare la posizione del carattere.
- La possibilità di eseguire l'aggiornamento in modo specifico per leggere i valori dal touchscreen e regolare la posizione in modo appropriato.

Per aggiungere il `CharacterEntity` al gioco, fare clic con il pulsante destro del mouse su un progetto **WalkingGame** e scegliere **Aggiungi > nuovo file.** Selezionare l'opzione **classe vuota** e denominare il nuovo file **CharacterEntity**, quindi fare clic su **nuovo**.

In primo luogo, si aggiungerà la `CharacterEntity` possibilità per l' `Texture2D` oggetto di caricare un oggetto e per disegnarlo. Il file appena aggiunto `CharacterEntity.cs` viene modificato come segue:

```csharp
using System;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Input.Touch;


namespace WalkingGame
{
    public class CharacterEntity
    {
        static Texture2D characterSheetTexture;

        public float X
        {
            get;
            set;
        }

        public float Y
        {
            get;
            set;
        }

        public CharacterEntity (GraphicsDevice graphicsDevice)
        {
            if (characterSheetTexture == null)
            {
                using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
                {
                    characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
                }
            }
        }

        public void Draw(SpriteBatch spriteBatch)
        {
            Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
            Color tintColor = Color.White;
            spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, tintColor);
        }
    }
}
```

Il codice precedente aggiunge la responsabilità del posizionamento, del rendering e del caricamento del contenuto `CharacterEntity`a. Di seguito sono riportate alcune considerazioni sul codice riportato sopra.

### <a name="why-are-x-and-y-floats"></a>Perché i float X e Y?

Gli sviluppatori che non hanno familiarità con la programmazione dei `float` giochi possono chiedersi perché il tipo viene `int` usato `double`in contrapposizione a o. Il motivo è che un valore a 32 bit è più comune per il posizionamento nel codice di rendering di basso livello. Il tipo Double occupa 64 bit per la precisione, che è raramente necessario per il posizionamento degli oggetti. Anche se una differenza di 32 bit può sembrare irrilevante, molti giochi moderni includono grafici che richiedono l'elaborazione di decine di migliaia di valori di posizione ogni frame (30 o 60 volte al secondo). Ridurre la quantità di memoria che deve essere spostata attraverso la pipeline grafica per mezzo può avere un impatto significativo sulle prestazioni di un gioco.

Il `int` tipo di dati può essere un'unità di misura appropriata per il posizionamento, ma è possibile inserire limitazioni sulla modalità di posizionamento delle entità. Ad esempio, l'utilizzo di valori integer rende più difficile l'implementazione di Smooth Movement per giochi che supportano lo zoom in o le fotocamere 3D (consentite da `SpriteBatch`).

Infine, si noterà che la logica che sposta il carattere intorno allo schermo verrà eseguita utilizzando i valori temporali del gioco. Il risultato della moltiplicazione della velocità in base alla quantità di tempo passata in un determinato frame genererà raramente un numero intero, quindi è necessario usare `float` per `X` e `Y`.

### <a name="why-is-charactersheettexture-static"></a>Perché characterSheetTexture è statico?

`characterSheetTexture` L'`Texture2D` istanza è una rappresentazione di runtime del file charactersheet. png. In altre parole, contiene i valori di colore per ogni pixel estratti dal file **charactersheet. png** di origine. Se il gioco fosse creare due `CharacterEntity` istanze, ognuna avrebbe bisogno di accedere alle informazioni di charactersheet. png. In questa situazione non si vuole sostenere il costo delle prestazioni per il caricamento di charactersheet. png due volte, né si vuole avere memoria di trama duplicata archiviata in RAM. L'uso `static` di un campo ci permette di condividere `Texture2D` lo stesso `CharacterEntity` in tutte le istanze.

L' `static` uso di membri per la rappresentazione del contenuto in fase di esecuzione è una soluzione semplicistica e non risolve i problemi riscontrati in giochi più grandi, ad esempio lo scaricamento del contenuto quando si passa da un livello a un altro. Soluzioni più sofisticate, che esulano dall'ambito di questa procedura dettagliata, includono l'uso della pipeline di contenuti monogame o la creazione di un sistema di gestione dei contenuti personalizzato.

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>Perché SpriteBatch viene passato come argomento anziché creare un'istanza dell'entità?

Il `Draw` metodo implementato in precedenza accetta un `SpriteBatch` argomento, `characterSheetTexture` ma, al contrario, viene creata un'istanza `CharacterEntity`dell'oggetto.

Il motivo è che il rendering più efficiente è possibile quando viene utilizzata la stessa `SpriteBatch` istanza per tutte `Draw` le chiamate e quando vengono effettuate tutte `Draw` le chiamate tra un singolo set di `Begin` chiamate e `End` . Naturalmente, il nostro gioco includerà solo una singola istanza di entità, ma i giochi più complessi trarranno vantaggio dal modello che consente a più entità di `SpriteBatch` usare la stessa istanza.

## <a name="adding-characterentity-to-the-game"></a>Aggiunta di CharacterEntity al gioco

Ora che il codice è stato `CharacterEntity` aggiunto per il rendering, è possibile sostituire il codice in `Game1.cs` per usare un'istanza della nuova entità. A tale scopo, si sostituirà `Texture2D` il campo con `CharacterEntity` un campo `Game1`in:

```csharp
public class Game1 : Game
{
    GraphicsDeviceManager graphics;
    SpriteBatch spriteBatch;
    // New code:
    CharacterEntity character;

    public Game1()
    {
    ...
```

Successivamente, si aggiungerà la creazione di un'istanza di questa `Game1.Initialize`entità in:

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

È anche necessario rimuovere la `Texture2D` creazione da `LoadContent` perché `CharacterEntity`ora è gestita all'interno di. `Game1.LoadContent`dovrebbe essere simile al seguente:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

Vale la pena notare che, nonostante il nome, `LoadContent` il metodo non è l'unica posizione in cui è possibile caricare il contenuto. molti giochi implementano il caricamento del contenuto nel metodo Initialize, o anche nel codice di aggiornamento come contenuto, potrebbero non essere necessari finché il giocatore non raggiunge un determinato punto del gioco.

Infine, è possibile modificare il metodo di estrazione come segue:


```csharp
protected override void Draw(GameTime gameTime)
{
    GraphicsDevice.Clear(Color.CornflowerBlue);

    // We'll start all of our drawing here:
    spriteBatch.Begin ();

    // Now we can do any entity rendering:
    character.Draw(spriteBatch);
    // End renders all sprites to the screen:
    spriteBatch.End ();

    base.Draw(gameTime);
}
```

Se si esegue il gioco, verrà ora visualizzato il carattere. Poiché il valore predefinito di X e Y è 0, il carattere viene posizionato nell'angolo superiore sinistro della schermata:

![Poiché il valore predefinito di X e Y è 0, il carattere viene posizionato nell'angolo superiore sinistro dello schermo](part2-images/image4.png)

## <a name="creating-the-animation-class"></a>Creazione della classe Animation

Attualmente viene visualizzato il file **charactersheet. png completo.** `CharacterEntity` Questa disposizione di più immagini in un file viene definita *foglio sprite*. In genere, uno sprite eseguirà il rendering solo di una parte del foglio sprite. Si modificherà l' `CharacterEntity` oggetto per eseguire il rendering di una parte di questo **charactersheet. png**e questa parte cambierà nel tempo per visualizzare un'animazione a discesa.

Si creerà la `Animation` classe per controllare la logica e lo stato dell'animazione CharacterEntity. La classe Animation sarà una classe generale che può essere usata per qualsiasi entità, non solo `CharacterEntity` per le animazioni. Ultimate la `Animation` classe fornirà un `Rectangle` oggetto che `CharacterEntity` utilizzerà durante il disegno. Verrà anche creata una `AnimationFrame` classe che verrà usata per definire l'animazione.

### <a name="defining-animationframe"></a>Definizione di AnimationFrame

`AnimationFrame`non conterrà alcuna logica correlata all'animazione. Verrà usato solo per archiviare i dati. Per aggiungere la `AnimationFrame` classe, fare clic con il pulsante destro del mouse o controllare il progetto condiviso **WalkingGame** e selezionare **Aggiungi > nuovo file.** Immettere il nome **AnimationFrame** e fare clic sul pulsante **nuovo** . Il `AnimationFrame.cs` file verrà modificato in modo che contenga il codice seguente:

```csharp
using System;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class AnimationFrame
    {
        public Rectangle SourceRectangle { get; set; }
        public TimeSpan Duration { get; set; }
    }
}
```

La `AnimationFrame` classe contiene due tipi di informazioni:

- `SourceRectangle`: Definisce l'area dell'oggetto `Texture2D` che verrà visualizzato `AnimationFrame`da. Questo valore viene misurato in pixel, con la parte superiore sinistra (0,0).
- `Duration`: Definisce il tempo di `AnimationFrame` visualizzazione di un oggetto quando viene `Animation`usato in un oggetto.

### <a name="defining-animation"></a>Definizione dell'animazione

La `Animation` classe conterrà un `List<AnimationFrame>` e la logica per cambiare il frame attualmente visualizzato in base al tempo trascorso.

Per aggiungere la `Animation` classe, fare clic con il pulsante destro del mouse o controllare il progetto condiviso **WalkingGame** e selezionare **Aggiungi > nuovo file.** Immettere il nome **Animation** , quindi fare clic sul pulsante **nuovo** . Il `Animation.cs` file verrà modificato in modo che contenga il codice seguente:


```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;

namespace WalkingGame
{
    public class Animation
    {
        List<AnimationFrame> frames = new List<AnimationFrame>();
        TimeSpan timeIntoAnimation;

        TimeSpan Duration
        {
            get
            {
                double totalSeconds = 0;
                foreach (var frame in frames)
                {
                    totalSeconds += frame.Duration.TotalSeconds;
                }

                return TimeSpan.FromSeconds (totalSeconds);
            }
        }

        public void AddFrame(Rectangle rectangle, TimeSpan duration)
        {
            AnimationFrame newFrame = new AnimationFrame()
            {
                SourceRectangle = rectangle,
                Duration = duration
            };

            frames.Add(newFrame);
        }

        public void Update(GameTime gameTime)
        {
            double secondsIntoAnimation = 
                timeIntoAnimation.TotalSeconds + gameTime.ElapsedGameTime.TotalSeconds;


            double remainder = secondsIntoAnimation % Duration.TotalSeconds;

            timeIntoAnimation = TimeSpan.FromSeconds (remainder);
        }
    }
}
```

Esaminiamo alcuni dei dettagli `Animation` della classe.

### <a name="frames-list"></a>Elenco frame

Il `frames` membro è quello che archivia i dati per l'animazione. Il codice che crea un'istanza delle animazioni consente `AnimationFrame` `frames` di aggiungere istanze all'elenco tramite `AddFrame` il metodo. Un'implementazione più completa può offrire `public` metodi o proprietà per la `frames`modifica, ma le funzionalità per l'aggiunta di frame per questa procedura dettagliata saranno limitate.

### <a name="duration"></a>Duration

Duration restituisce la durata totale dell' `Animation,` operazione ottenuta aggiungendo la durata di tutte le istanze contenute. `AnimationFrame` Questo valore potrebbe essere memorizzato nella `AnimationFrame` cache se fosse un oggetto non modificabile, ma poiché AnimationFrame è stato implementato come una classe che può essere modificata dopo essere stata aggiunta all'animazione, è necessario calcolare questo valore ogni volta che viene eseguito l'accesso alla proprietà.

### <a name="update"></a>Aggiorna

Il `Update` metodo deve essere chiamato ogni frame (ovvero ogni volta che viene aggiornato l'intero gioco). Lo scopo è quello di aumentare `timeIntoAnimation` il membro usato per restituire il frame attualmente visualizzato. La logica in `Update` impedisce che `timeIntoAnimation` l'oggetto sia sempre maggiore della durata dell'intera animazione.

Poiché verrà usata la `Animation` classe per visualizzare un'animazione a lungo termine, si vuole che questa animazione si ripeta quando ha raggiunto la fine. `timeIntoAnimation` È possibile eseguire questa operazione controllando se è maggiore `Duration` del valore. In tal caso, verrà eseguito il ciclo all'inizio e manterrà il resto, ottenendo un'animazione di ciclo.

### <a name="obtaining-the-current-frames-rectangle"></a>Recupero del rettangolo del frame corrente

Lo scopo della classe `Animation` è di fornire un oggetto `Rectangle` che può essere usato durante il disegno di uno sprite. Attualmente la `Animation` classe contiene la logica per modificare `timeIntoAnimation` il membro, che verrà usato per ottenere `Rectangle` la visualizzazione. Questa operazione verrà eseguita creando una `CurrentRectangle` proprietà `Animation` nella classe. Copiare questa proprietà nella `Animation` classe:

```csharp
public Rectangle CurrentRectangle
{
    get
    {
        AnimationFrame currentFrame = null;

        // See if we can find the frame
        TimeSpan accumulatedTime = new TimeSpan();
        foreach(var frame in frames)
        {
            if (accumulatedTime + frame.Duration >= timeIntoAnimation)
            {
                currentFrame = frame;
                break;
            }
            else
            {
                accumulatedTime += frame.Duration;
            }
        }

        // If no frame was found, then try the last frame, 
        // just in case timeIntoAnimation somehow exceeds Duration
        if (currentFrame == null)
        {
            currentFrame = frames.LastOrDefault ();
        }

        // If we found a frame, return its rectangle, otherwise
        // return an empty rectangle (one with no width or height)
        if (currentFrame != null)
        {
            return currentFrame.SourceRectangle;
        }
        else
        {
            return Rectangle.Empty;
        }
    }
}
```

## <a name="adding-the-first-animation-to-characterentity"></a>Aggiunta della prima animazione a CharacterEntity

Conterrà le `CharacterEntity` animazioni per l'esplorazione e la posizione, nonché un riferimento all'oggetto corrente `Animation` visualizzato.

Prima di tutto, aggiungeremo il `Animation,` primo da usare per testare la funzionalità come scritto finora. Aggiungere i membri seguenti alla classe CharacterEntity:

```csharp
Animation walkDown;
Animation currentAnimation;
```

Definire ora l' `walkDown` animazione. A tale scopo, modificare `CharacterEntity` il costruttore come segue:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Come indicato in precedenza, è necessario chiamare `Animation.Update` per riprodurre le animazioni basate sul tempo. È anche necessario assegnare `currentAnimation`. Per il momento verrà assegnato a `currentAnimation` , `walkDown`ma il codice verrà sostituito in un secondo momento quando si implementerà la logica di movimento. Aggiungeremo il `Update` metodo a come `CharacterEntity` indicato di seguito:


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Ora che è stata assegnata e aggiornata, è possibile usarla per eseguire il `currentAnimation` disegno. Verrà modificato `CharacterEntity.Draw` come segue:

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

L'ultimo passaggio per ottenere l' `CharacterEntity` animazione consiste nel chiamare il metodo appena aggiunto `Update` da `Game1`. Modificare `Game1.Update` come segue:

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

Ora verrà `CharacterEntity` `walkDown` riprodotto l'animazione:

![A questo punto il CharacterEntity eseguirà l'animazione walkDown](part2-images/image5.gif)

## <a name="adding-movement-to-the-character"></a>Aggiunta dello spostamento al carattere

Successivamente, si aggiungerà lo spostamento al nostro carattere usando i controlli touch. Quando l'utente tocca lo schermo, il carattere si sposta verso il punto in cui viene toccato lo schermo. Se non vengono rilevati tocchi, il carattere verrà inserito.

### <a name="defining-getdesiredvelocityfrominput"></a>Definizione di GetDesiredVelocityFromInput

Verrà usata la `TouchPanel` classe monogame, che fornisce informazioni sullo stato corrente del touchscreen. Si aggiungerà un metodo che verificherà e `TouchPanel` restituirà la velocità desiderata per il carattere:

```csharp
Vector2 GetDesiredVelocityFromInput()
{
    Vector2 desiredVelocity = new Vector2 ();

    TouchCollection touchCollection = TouchPanel.GetState();

    if (touchCollection.Count > 0)
    {
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;

        if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)
        {
            desiredVelocity.Normalize();
            const float desiredSpeed = 200;
            desiredVelocity *= desiredSpeed;
        }
    }

    return desiredVelocity;
}
```

Il `TouchPanel.GetState` metodo restituisce un `TouchCollection` oggetto che contiene informazioni sulla posizione in cui l'utente tocca lo schermo. Se l'utente non tocca lo schermo, `TouchCollection` sarà vuoto, nel qual caso non è necessario spostare il carattere.

Se l'utente tocca lo schermo, il carattere verrà spostato verso il primo tocco, in altre parole, `TouchLocation` in corrispondenza dell'indice 0. Inizialmente la velocità desiderata verrà impostata in modo da corrispondere alla differenza tra la posizione del carattere e la posizione del primo tocco:

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

Di seguito è riportato un po' di calcoli matematici che manterranno il carattere che si muove alla stessa velocità. Per spiegare il motivo per cui questo aspetto è importante, si consideri una situazione in cui l'utente sta toccando la schermata 500 pixel a partire dal punto in cui si trova il carattere. La prima riga in `desiredVelocity.X` cui è impostato assegna un valore di 500. Tuttavia, se l'utente tocca lo schermo a una distanza di soli 100 unità dal carattere, `desiredVelocity.X` viene impostato su 100. Il risultato sarebbe che la velocità di spostamento del carattere risponderebbe alla distanza del punto di tocco dal carattere. Poiché si vuole che il carattere si sposti sempre alla stessa velocità, è necessario modificare il desiredVelocity.

L' `if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)` istruzione sta verificando se la velocità è diversa da zero. in altre parole, verifica che l'utente non tocchi lo stesso punto della posizione corrente del carattere. In caso contrario, è necessario impostare la velocità del carattere in modo che sia costante indipendentemente dalla distanza del tocco. Questa operazione viene eseguita normalizzando il vettore di velocità, che restituisce una lunghezza pari a 1. Un vettore di velocità pari a 1 indica che il carattere viene spostato a 1 pixel al secondo. Questa operazione verrà velocizzata moltiplicando il valore per la velocità desiderata di 200.

### <a name="applying-velocity-to-position"></a>Applicazione della velocità alla posizione

La velocità restituita `GetDesiredVelocityFromInput` da deve essere applicata ai valori `X` e `Y` del carattere per avere un effetto in fase di esecuzione. Il `Update` metodo verrà modificato come segue:

```csharp
public void Update(GameTime gameTime)
{

    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;

    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Ciò che abbiamo implementato qui è denominato spostamento *basato sul tempo* (anziché spostamento *basato su frame* ). Lo spostamento basato sul tempo moltiplica un valore di velocità (in questo caso i valori archiviati nella `velocity` variabile) in base al tempo trascorso dall'ultimo aggiornamento archiviato in. `gameTime.ElapsedGameTime.TotalSeconds` Se il gioco viene eseguito in un minor numero di fotogrammi al secondo, il tempo trascorso tra i frame aumenta: il risultato finale è che gli oggetti che usano lo spostamento basato sul tempo si sposteranno sempre alla stessa velocità indipendentemente dalla frequenza dei fotogrammi.

Se si esegue il gioco adesso, si noterà che il carattere sta per passare alla posizione del tocco:

![Il carattere viene spostato verso la posizione di tocco](part2-images/image6.gif)

## <a name="matching-movement-and-animation"></a>Spostamento e animazione corrispondenti

Quando il carattere si sposta e si riproduce un'unica animazione, è possibile definire il resto delle animazioni, quindi usarle per riflettere lo spostamento del carattere. Al termine, si avranno in totale otto animazioni:

- Animazioni per le attività a discesa, a sinistra e a destra
- Animazioni per l'esecuzione ancora e la rivolta verso l'alto, verso il basso, verso sinistra e verso destra

### <a name="defining-the-rest-of-the-animations"></a>Definizione delle altre animazioni

Per prima `Animation` cosa verranno aggiunte le istanze `CharacterEntity` alla classe per tutte le animazioni nella stessa posizione in cui è stato aggiunto `walkDown`. Una volta eseguita questa operazione, `CharacterEntity` avrà i membri seguenti `Animation` :

```csharp
Animation walkDown;
Animation walkUp;
Animation walkLeft;
Animation walkRight;

Animation standDown;
Animation standUp;
Animation standLeft;
Animation standRight;

Animation currentAnimation;
```

A questo punto verranno definite le animazioni nel `CharacterEntity` costruttore, come indicato di seguito:

```csharp
public CharacterEntity (GraphicsDevice graphicsDevice)
{
    if (characterSheetTexture == null)
    {
        using (var stream = TitleContainer.OpenStream ("Content/charactersheet.png"))
        {
            characterSheetTexture = Texture2D.FromStream (graphicsDevice, stream);
        }
    }

    walkDown = new Animation ();
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (16, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkDown.AddFrame (new Rectangle (32, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkUp = new Animation ();
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (160, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkUp.AddFrame (new Rectangle (176, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkLeft = new Animation ();
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (64, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkLeft.AddFrame (new Rectangle (80, 0, 16, 16), TimeSpan.FromSeconds (.25));

    walkRight = new Animation ();
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (112, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
    walkRight.AddFrame (new Rectangle (128, 0, 16, 16), TimeSpan.FromSeconds (.25));

    // Standing animations only have a single frame of animation:
    standDown = new Animation ();
    standDown.AddFrame (new Rectangle (0, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standUp = new Animation ();
    standUp.AddFrame (new Rectangle (144, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standLeft = new Animation ();
    standLeft.AddFrame (new Rectangle (48, 0, 16, 16), TimeSpan.FromSeconds (.25));

    standRight = new Animation ();
    standRight.AddFrame (new Rectangle (96, 0, 16, 16), TimeSpan.FromSeconds (.25));
}
```

Si noti che il codice precedente è stato aggiunto al `CharacterEntity` costruttore per tenere più breve questa procedura dettagliata. I giochi in genere separano la definizione delle animazioni di caratteri nelle rispettive classi o caricano queste informazioni da un formato dati, ad esempio XML o JSON.

Successivamente, si modificherà la logica per usare le animazioni in base alla direzione in cui il carattere è in corso di trasferimento o in base all'ultima animazione se il carattere è stato appena interrotto. A tale scopo, si modificherà il `Update` metodo:

```csharp
public void Update(GameTime gameTime)
{
    var velocity = GetDesiredVelocityFromInput ();

    this.X += velocity.X * (float)gameTime.ElapsedGameTime.TotalSeconds;
    this.Y += velocity.Y * (float)gameTime.ElapsedGameTime.TotalSeconds;


    if (velocity != Vector2.Zero)
    {
        bool movingHorizontally = Math.Abs (velocity.X) > Math.Abs (velocity.Y);
        if (movingHorizontally)
        {
            if (velocity.X > 0)
            {
                currentAnimation = walkRight;
            }
            else
            {
                currentAnimation = walkLeft;
            }
        }
        else
        {
            if (velocity.Y > 0)
            {
                currentAnimation = walkDown;
            }
            else
            {
                currentAnimation = walkUp;
            }
        }
    }
    else
    {
        // If the character was walking, we can set the standing animation
        // according to the walking animation that is playing:
        if (currentAnimation == walkRight)
        {
            currentAnimation = standRight;
        }
        else if (currentAnimation == walkLeft)
        {
            currentAnimation = standLeft;
        }
        else if (currentAnimation == walkUp)
        {
            currentAnimation = standUp;
        }
        else if (currentAnimation == walkDown)
        {
            currentAnimation = standDown;
        }
        else if (currentAnimation == null)
        {
            currentAnimation = standDown;
        }

        // if none of the above code hit then the character
        // is already standing, so no need to change the animation.
    }

    currentAnimation.Update (gameTime);
}
```

Il codice per cambiare le animazioni viene suddiviso in due blocchi. Il primo controlla se la velocità non è uguale a `Vector2.Zero` : indica se il carattere è in continua evoluzione. Se il carattere è in fase di trasferimento, è possibile esaminare `velocity.X` i `velocity.Y` valori e per determinare quale animazione a piedi riprodurre.

Se il carattere non viene spostato, è necessario impostare il carattere `currentAnimation` su un'animazione in piedi, ma solo `currentAnimation` se è un'animazione a camminamento o se un'animazione non è stata impostata. Se non `currentAnimation`è una delle quattro animazioni a breve, il carattere è già in esecuzione, quindi non è necessario modificarlo. `currentAnimation`

Il risultato di questo codice è che il carattere viene animato correttamente quando si cammina, quindi si trova l'ultima direzione a cui si è camminato quando si arresta:

![Il risultato di questo codice è che il carattere viene animato correttamente quando si cammina, quindi si trova l'ultima direzione a cui si è camminato quando si arresta](part2-images/image7.gif)

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata ha illustrato come usare monogame per creare un gioco multipiattaforma con sprite, lo stato di oggetti, il rilevamento degli input e l'animazione. Viene illustrata la creazione di una classe di animazione per utilizzo generico. È stato inoltre illustrato come creare un'entità carattere per organizzare la logica del codice.

## <a name="related-links"></a>Collegamenti correlati

- [Risorsa immagine CharacterSheet (esempio)](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [Esecuzione del gioco completo (esempio)](https://docs.microsoft.com/samples/xamarin/mobile-samples/walkinggamemg/)
