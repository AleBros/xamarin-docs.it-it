---
title: 'Parte 2: implementazione di WalkingGame'
description: Questa procedura dettagliata viene illustrato come aggiungere la logica del gioco e il contenuto in un progetto MonoGame vuoto per creare una demo di un'animazione sprite lo spostamento con input di tocco.
ms.prod: xamarin
ms.assetid: F0622A01-DE7F-451A-A51F-129876AB6FFD
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 941b88f9109cf2f3a3485311c52b1250bd08e53f
ms.sourcegitcommit: 2ee36611ef667affee7d417db947fbb614d75315
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479771"
---
# <a name="part-2--implementing-the-walkinggame"></a>Parte 2: implementazione di WalkingGame

_Questa procedura dettagliata viene illustrato come aggiungere la logica del gioco e il contenuto in un progetto MonoGame vuoto per creare una demo di un'animazione sprite lo spostamento con input di tocco._

Le parti precedenti della procedura dettagliata ha illustrato come creare progetti MonoGame vuoti. Verrà compilata in queste parti precedenti eseguendo una semplice gioco demo. In questo articolo sono contenute le sezioni seguenti:

- Durante la decompressione dei contenuti dei giochi
- Cenni preliminari sulla classe MonoGame
- Il primo Sprite di rendering
- Creazione di CharacterEntity
- Aggiunta di CharacterEntity al gioco
- Creazione della classe di animazione
- Aggiunta di animazione prima a CharacterEntity
- Aggiunta dello spostamento al carattere
- Lo spostamento e animazione corrispondenti


## <a name="unzipping-our-game-content"></a>Durante la decompressione dei contenuti dei giochi

Prima di iniziare la scrittura di codice, è necessario decomprimere il gioco *contenuto*. Gli sviluppatori di giochi usano spesso il termine *contenuto* per fare riferimento ai file non di codice che in genere vengono creati da visual alle creazioni degli artisti, finestre di progettazione del gioco o audio finestre di progettazione. Tipi comuni di contenuto includono i file usati per visualizzare gli oggetti visivi, riprodurre un suono o controllare il comportamento di intelligenza artificiale (AI). Da un sviluppo di giochi contenuto punto di vista del team viene creato in genere non programmatori.

Il contenuto usato in questo esempio sono reperibili [su github](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true). Questi file scaricati in un percorso che si accede più avanti in questa procedura dettagliata sarà necessario.

## <a name="monogame-class-overview"></a>Cenni preliminari sulla classe MonoGame

Prima di tutto, verranno illustrate le classi MonoGame usate nel rendering di base:

- `SpriteBatch` : usato per disegnare la grafica 2D sullo schermo. *Sprite* sono 2D elementi visivi che consentono di visualizzare immagini sullo schermo. Il `SpriteBatch` oggetto consente di disegnare un singolo foglio alla volta tra i relativi `Begin` e `End` più sprite, o metodi possono essere raggruppati, o *in batch*.
- `Texture2D` : rappresenta un oggetto immagine in fase di esecuzione. `Texture2D` le istanze vengono spesso create dai formati di file, ad esempio con estensione PNG o BMP, anche se possono anche essere creati dinamicamente in fase di esecuzione. `Texture2D` le istanze vengono utilizzate durante il rendering con `SpriteBatch` istanze.
- `Vector2` : rappresenta una posizione in un sistema di coordinate 2D che viene spesso usato per il posizionamento degli oggetti visivi. Include anche MonoGame `Vector3` e `Vector4` ma si userà solo `Vector2` in questa procedura dettagliata.
- `Rectangle` : un'area con la posizione, larghezza e altezza quattro lati. Utilizzeremo questa per definire quale parte del nostro `Texture2D` per eseguire il rendering quando si comincerà a lavorare con le animazioni.

Si noti inoltre che MonoGame non è gestita da Microsoft: nonostante lo spazio dei nomi. Il `Microsoft.Xna` dello spazio dei nomi viene usato in MonoGame per renderlo più facile eseguire la migrazione di progetti esistenti XNA a MonoGame.

## <a name="rendering-our-first-sprite"></a>Il primo Sprite di rendering

È quindi verranno disegnare sullo schermo per mostrare come eseguire il rendering 2D in MonoGame sprite singolo.

### <a name="creating-a-texture2d"></a>Creazione di un Texture2D

È necessario creare un `Texture2D` istanza da utilizzare per il rendering del nostro sprite. Tutto il contenuto del gioco in definitiva è contenuto in una cartella denominata **contenuto,** che si trova nel progetto specifico della piattaforma. I progetti MonoGame condiviso non possono contenere contenuto, come il contenuto deve usare le azioni di compilazione specifiche per la piattaforma. Gli sviluppatori di CocosSharp troveranno nella cartella Content un concetto familiare: si trovano nella stessa posizione in progetti sia CocosSharp MonoGame. La cartella del contenuto è reperibile nel progetto iOS e cartella Assets nel progetto Android.

Per aggiungere il contenuto del nostro gioco, fare clic sui **contenuti** cartella e selezionare **Aggiungi > Aggiungi file...** Passare al percorso in cui è stato estratto il file content.zip e selezionare il **charactersheet.png** file. Se viene richiesto su come aggiungere file alla cartella, è consigliabile selezionare anche il **copia** opzione:

![](part2-images/image1.png "Se frequenti su come aggiungere file alla cartella, selezionare l'opzione di copia")

La cartella del contenuto contiene ora il file charactersheet.png:

![](part2-images/image2.png "La cartella del contenuto contiene ora il file charactersheet.png")

Successivamente, si aggiungerà codice per caricare il file charactersheet.png e creare un `Texture2D`. A questo scopo, aprire il `Game1.cs` file e aggiungere il campo seguente alla classe Game1.cs:

```csharp
Texture2D characterSheetTexture;
```

Successivamente, verrà creato il `characterSheetTexture` nella `LoadContent` (metodo). Prima di eventuali modifiche `LoadContent` metodo si presenta come segue:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
    // TODO: use this.Content to load your game content here
}
```

Si noti che il progetto predefinito già creata un'istanza di `spriteBatch` istanza per noi. Verrà usato questo in un secondo momento, ma che non verranno aggiunti alcun codice aggiuntivo per preparare il `spriteBatch` per l'uso. D'altra parte, nostro `spriteSheetTexture` richiedono l'inizializzazione, pertanto abbiamo verrà inizializzato dopo il `spriteBatch` creato:

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

Ora che abbiamo un `SpriteBatch` istanza e un `Texture2D` istanza possiamo aggiungere il codice per il rendering per il `Game1.Draw` metodo:

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

Eseguire ora il gioco Mostra un singolo sprite visualizzando la trama creata da charactersheet.png:

![](part2-images/image3.png "Eseguire ora il gioco viene illustrato un sprite singolo visualizzazione di trama creata da charactersheet.png")

## <a name="creating-the-characterentity"></a>Creazione di CharacterEntity

Finora è stato aggiunto codice per il rendering di un singolo sprite sullo schermo. Tuttavia, se fossimo per sviluppare un gioco completo senza la creazione di un'altra classe, verrebbe eseguiamo in problemi di organizzazione del codice.

### <a name="what-is-an-entity"></a>Che cos'è un'entità?

Un modello comune per l'organizzazione di codice del gioco consiste nel creare una nuova classe per ogni gioco *entità* tipo. Un'entità nello sviluppo di giochi è un oggetto che può contenere alcune delle caratteristiche seguenti (non tutti sono obbligatori):

- Un elemento visivo, ad esempio un sprite, text o un modello 3D
- Ogni comportamento frame, ad esempio un'unità patrolling un percorso di set o un carattere di Windows Media player risponde di input o fisica
- Possono essere creati ed eliminati in modo dinamico, ad esempio una visualizzazione power-up e raccolto dal lettore

I sistemi di entità dell'organizzazione possono essere complessi e molti motori di gioco offrono classi che consente di gestire le entità. Si verrà implementato un sistema molto semplice di entità, in modo che vale la pena notare che i giochi completi richiedono in genere maggiore organizzazione parte dello sviluppatore.


### <a name="defining-the-characterentity"></a>La definizione di CharacterEntity

L'entità, che chiameremo `CharacterEntity`, avrà le caratteristiche seguenti:

- La possibilità di caricare la propria `Texture2D`
- La possibilità di eseguire il rendering, tra cui contenente metodi di chiamata per aggiornare l'animazione in movimento
- `X` e le proprietà di Y per controllare la posizione del carattere.
- La possibilità di aggiornarsi, in particolare, per leggere i valori dal tocco schermata e modificherà di conseguenza posizione.

Per aggiungere il `CharacterEntity` nostro gioco, pulsante destro del mouse o CTRL + clic sul **WalkingGame** del progetto e selezionare **Aggiungi > Nuovo File...** . Selezionare il **classe vuota** opzione e denominare il nuovo file **CharacterEntity**, quindi fare clic su **New**.

Prima di tutto si aggiungerà la possibilità per i `CharacterEntity` per caricare un `Texture2D` nonché per il disegno. Si modificherà appena aggiunto `CharacterEntity.cs` file come segue:

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

Il codice sopra riportato aggiunge la responsabilità di posizionamento, il rendering e il caricamento del contenuto per il `CharacterEntity`. È opportuno sottolineare alcuni aspetti nel codice precedente.

### <a name="why-are-x-and-y-floats"></a>Sono il motivo per cui X e Y float?

Gli sviluppatori che hanno familiarità con la programmazione gioco lecito chiedersi perché il `float` viene usato il tipo anziché `int` o `double`. Il motivo è che un valore a 32 bit è più comune per il posizionamento nel codice di basso livello per il rendering. Il tipo double occupi 64 bit per la precisione, raramente necessaria per il posizionamento di oggetti. Mentre una differenza a 32 bit può sembrare irrilevante, molti giochi moderni includono grafica che richiedono l'elaborazione di decine di migliaia di valori di posizione ogni frame (30 o 60 volte al secondo). Taglia la quantità di memoria che deve passare attraverso la grafica pipeline metà può avere un impatto significativo sulle prestazioni del gioco.

Il `int` tipo di dati può essere un'unità di misura per il posizionamento appropriata, ma è possibile impostare limitazioni per il modo in cui le entità sono posizionate. Ad esempio, usando i valori interi rende più difficile implementare movimento uniforme per i giochi che supportano lo zoom avanti o videocamere 3D (che sono consentite dai `SpriteBatch`).

Infine, si vedrà che la logica che consente di spostare il carattere attorno alla schermata può eseguire questa operazione usando i valori di intervallo del gioco. Il risultato della moltiplicazione della velocità per quanto tempo è passato in un determinato intervallo raramente comporterà su numero intero, pertanto è necessario usare `float` per `X` e `Y`.

### <a name="why-is-charactersheettexture-static"></a>Perché è characterSheetTexture statico?

Il `characterSheetTexture` `Texture2D` istanza è una rappresentazione di runtime del file charactersheet.png. In altre parole, contiene i valori di colore per ogni pixel come estratto dall'origine **charactersheet.png** file. Se il gioco sono state creare due `CharacterEntity` istanze, ciascuna di esse sarebbe necessario accedere alle informazioni da charactersheet.png. In questo caso è preferibile non incorrere nell'onere delle prestazioni di caricamento charactersheet.png due volte, né si vuole disporre di memoria di trama duplicati archiviata nella ram. Usando un `static` campo consente di condividere lo stesso `Texture2D` complessivi di `CharacterEntity` istanze.

Usando `static` membri per la rappresentazione di runtime del contenuto è una soluzione semplice e non vengono fornite informazioni ai problemi riscontrati nei giochi di dimensioni maggiori, ad esempio scaricando contenuto, quando si spostano da un livello a un altro. Soluzioni più sofisticate, che esulano dall'ambito di questa procedura dettagliata, prevedere l'utilizzo della pipeline di contenuti di MonoGame oppure la creazione di un sistema di gestione dei contenuti personalizzato.

### <a name="why-is-spritebatch-passed-as-an-argument-instead-of-instantiated-by-the-entity"></a>Perché è SpriteBatch passato come un argomento Instead of crea un'istanza dell'entità?

Il `Draw` , implementato sopra accetta un `SpriteBatch` argomento, ma al contrario, il `characterSheetTexture` viene creata mediante il `CharacterEntity`.

Ciò accade perché è possibili quando il rendering più efficiente lo stesso `SpriteBatch` istanza viene usata per tutti `Draw` chiamate e quando tutto `Draw` vengono effettuate le chiamate tra un singolo set di `Begin` e `End` chiamate. Naturalmente, il gioco includerà solo una sola istanza di entità, ma più complicati giochi trarranno vantaggio dal modello che consente a più entità di utilizzare lo stesso `SpriteBatch` istanza.


## <a name="adding-characterentity-to-the-game"></a>Aggiunta di CharacterEntity al gioco

Ora che è stato aggiunto il nostro `CharacterEntity` con il codice per il rendering di se stesso, è possibile sostituire il codice in `Game1.cs` per utilizzare un'istanza di questa nuova entità. A tale scopo verrà sostituito il `Texture2D` campo con un `CharacterEntity` campo `Game1`:

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

Successivamente, si aggiungerà la creazione di istanze di questa entità in `Game1.Initialize`:

```csharp
protected override void Initialize()
{
    character = new CharacterEntity (this.GraphicsDevice);

    base.Initialize();
}
```

È necessario rimuovere anche il `Texture2D` la creazione da `LoadContent` dal momento che viene ora gestito all'interno del nostro `CharacterEntity`. `Game1.LoadContent` dovrebbe essere simile al seguente:

```csharp
protected override void LoadContent()
{
    // Create a new SpriteBatch, which can be used to draw textures.
    spriteBatch = new SpriteBatch(GraphicsDevice);
}
```

Vale la pena notare che, nonostante il nome di `LoadContent` metodo non è l'unica piattaforma in cui il contenuto può essere caricato: molti giochi implementare contenuto il caricamento nel relativo metodo Initialize, o anche nel proprio codice di aggiornamento come contenuto, può non essere necessaria fino a quando il giocatore raggiunge un determinato punto del gioco.

Infine è possibile modificare il metodo Draw come indicato di seguito:


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

Se eseguiamo il gioco, ora si vedrà il carattere. Poiché X e Y predefinito a 0, il carattere è posizionato in alto a sinistra della schermata:

![](part2-images/image4.png "Poiché X e Y predefinito a 0, il carattere è posizionato in alto a sinistra della schermata")

## <a name="creating-the-animation-class"></a>Creazione della classe di animazione

Attualmente nostri `CharacterEntity` consente di visualizzare l'intero **charactersheet.png** file. Questa disposizione di più immagini in un file viene definita un *foglio di sprite*. In genere, un sprite verrà eseguito il rendering solo una parte del foglio di sprite. Si modificherà il `CharacterEntity` per eseguire il rendering di una parte di questo **charactersheet.png**, e questa parte cambia nel corso del tempo per visualizzare un'animazione in movimento.

Si creerà il `Animation` classe per controllare la logica e stato dell'animazione CharacterEntity. La classe di animazione sarà una classe generale che potrebbe essere usata per qualsiasi entità, non solo `CharacterEntity` animazioni. Ultimate il `Animation` classe fornirà una `Rectangle` quale la `CharacterEntity` verrà usato quando si disegna se stesso. Verrà creata anche una `AnimationFrame` classe che verrà usato per definire l'animazione.


### <a name="defining-animationframe"></a>Definizione AnimationFrame

`AnimationFrame` non conterrà alcuna logica relative all'animazione. Verrà usato solo per archiviare i dati. Per aggiungere il `AnimationFrame` classe, pulsante destro del mouse o CTRL + clic sui **WalkingGame** condiviso progetto e selezionare **Aggiungi > Nuovo File...** Immettere il nome **AnimationFrame** e fare clic sui **New** pulsante. Modifichiamo il `AnimationFrame.cs` file in modo che contenga il codice seguente:


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

Il `AnimationFrame` classe contiene due tipi di informazioni:

- `SourceRectangle` : Definisce l'area della `Texture2D` che verrà visualizzata per il `AnimationFrame`. Questo valore viene misurato in pixel, dove la superiore sinistro è (0, 0).
- `Duration` : Definisce quanto tempo un `AnimationFrame` viene visualizzata quando utilizzata in un `Animation`.

### <a name="defining-animation"></a>La definizione di animazione

Il `Animation` classe conterrà un `List<AnimationFrame>` , nonché la logica per passare il frame è attualmente visualizzato in base alla quantità di tempo trascorso.

Per aggiungere il `Animation` classe, pulsante destro del mouse o CTRL + clic sui **WalkingGame** condiviso progetto e selezionare **Aggiungi > Nuovo File...** . Immettere il nome **animazione** e fare clic sui **New** pulsante. Modifichiamo il `Animation.cs` file in modo che contenga il codice seguente:


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

Esaminiamo alcuni dettagli dal `Animation` classe.

### <a name="frames-list"></a>Elenco frame

Il `frames` membro è ciò che archivia i dati per l'animazione. Verrà aggiunto il codice che crea un'istanza di animazioni `AnimationFrame` alle istanze di `frames` elenco tramite la `AddFrame` (metodo). Un'implementazione più completa può offrire `public` metodi o proprietà per la modifica `frames`, ma ci limiteremo le funzionalità per l'aggiunta di frame per questa procedura dettagliata.

### <a name="duration"></a>Durata

La durata viene restituita la durata totale dei `Animation,` cui viene ottenuto aggiungendo la durata di tutti i contenuti `AnimationFrame` istanze. Questo valore potrebbe essere memorizzato nella cache se `AnimationFrame` erano un oggetto non modificabile, ma poiché AnimationFrame viene implementato come una classe che può essere modificata dopo l'aggiunta di animazione, è necessario calcolare tale valore ogni volta che si accede alla proprietà.

### <a name="update"></a>Aggiorna

Il `Update` metodo deve essere chiamato per ogni fotogramma (vale a dire, ogniqualvolta viene aggiornato il gioco intero). Il suo scopo consiste nell'aumentare la `timeIntoAnimation` membro che viene utilizzata per restituire il frame attualmente visualizzato. La logica nel `Update` impedisce la `timeIntoAnimation` mai la più grande rispetto alla durata dell'intera animazione.

Poiché verrà usato il `Animation` classe per visualizzare un'animazione in movimento, quindi si vuole che questa animazione ripetere una volta raggiunta la fine. È possibile eseguire questa operazione verificando se la `timeIntoAnimation` è maggiore di `Duration` valore. In tal caso verrà fino all'inizio del ciclo e mantenere il resto, risultante in una ciclo di animazione.

### <a name="obtaining-the-current-frames-rectangle"></a>Come ottenere il rettangolo del Frame corrente

Lo scopo del `Animation` classe è in definitiva per fornire un `Rectangle` che può essere usato quando si disegnano sprite. Attualmente le `Animation` classe contiene la logica per la modifica di `timeIntoAnimation` membro, che verrà usata per ottenere che `Rectangle` deve essere visualizzato. Faremo questo mediante la creazione di un `CurrentRectangle` proprietà nel `Animation` classe. Copiare questa proprietà nel `Animation` classe:

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

## <a name="adding-the-first-animation-to-characterentity"></a>Aggiunta di animazione prima a CharacterEntity

Il `CharacterEntity` conterrà le animazioni per analisi e la condizione, nonché un riferimento all'oggetto corrente `Animation` visualizzati.

In primo luogo, si aggiungerà il primo `Animation,` che verrà usata per testare la funzionalità come scritto finora. È possibile aggiungere i membri seguenti alla classe CharacterEntity:

```csharp
Animation walkDown;
Animation currentAnimation;
```

Successivamente, è importante definire la `walkDown` animazione. Per eseguire questa modifica il `CharacterEntity` costruttore come illustrato di seguito:

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

Come accennato in precedenza, dobbiamo chiamare `Animation.Update` per le animazioni basate sul tempo per la riproduzione. È necessario anche assegnare il `currentAnimation`. Per questo punto si assegneranno i `currentAnimation` a `walkDown`, ma si sarà da sostituire questo codice in un secondo momento quando si implementa la logica di spostamento. Si aggiungerà il `Update` metodo `CharacterEntity` come indicato di seguito:


```csharp
public void Update(GameTime gameTime)
{
    // temporary - we'll replace this with logic based off of which way the
    // character is moving when we add movement logic
    currentAnimation = walkDown;

    currentAnimation.Update (gameTime);
}
```

Ora che abbiamo il `currentAnimation` viene assegnato e aggiornato, è possibile usarlo per eseguire il disegno. Questo punto, modificheremo `CharacterEntity.Draw` come indicato di seguito:

```csharp
public void Draw(SpriteBatch spriteBatch)
{
    Vector2 topLeftOfSprite = new Vector2 (this.X, this.Y);
    Color tintColor = Color.White;
    var sourceRectangle = currentAnimation.CurrentRectangle;

    spriteBatch.Draw(characterSheetTexture, topLeftOfSprite, sourceRectangle, Color.White);
}
```

L'ultimo passaggio per ottenere il `CharacterEntity` animazione consiste nel chiamare appena aggiunta `Update` metodo `Game1`. Modificare `Game1.Update` come indicato di seguito:

```csharp
protected override void Update(GameTime gameTime)
{
    character.Update (gameTime);
    base.Update(gameTime);
}
```

A questo punto il `CharacterEntity` riprodurrà relativo `walkDown` animazione:

![](part2-images/image5.gif "A questo punto il CharacterEntity riprodurrà relativa animazione walkDown")

## <a name="adding-movement-to-the-character"></a>Aggiunta dello spostamento al carattere

Successivamente, aggiungeremo spostamento al nostro carattere usando i controlli a tocco. Quando l'utente tocca lo schermo, il carattere verrà spostato verso il punto in cui viene manipolata la schermata. Se non vengono rilevati alcun computerizzati, il carattere verrà stand posto.

### <a name="defining-getdesiredvelocityfrominput"></a>Definizione GetDesiredVelocityFromInput

Verrà usato di MonoGame `TouchPanel` (classe), che fornisce informazioni sullo stato corrente del touch screen. È possibile aggiungere un metodo che controllerà la `TouchPanel` e restituire la velocità desiderata del nostro carattere:


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

Il `TouchPanel.GetState` metodo restituisce un `TouchCollection` che contiene informazioni su dove l'utente tocca la schermata. Se l'utente non tocca la schermata, quindi il `TouchCollection` sarà vuoto, nel qual caso è non è consigliabile spostare il carattere.

Se l'utente tocca la schermata, il carattere a primo tocco, verrà spostato in altre parole, il `TouchLocation` in corrispondenza dell'indice 0. Inizialmente verrà impostata la velocità desiderata deve essere uguale alla differenza tra la posizione del carattere e la posizione del primo tocco:

```csharp
        desiredVelocity.X = touchCollection [0].Position.X - this.X;
        desiredVelocity.Y = touchCollection [0].Position.Y - this.Y;
```

Ciò che segue è un po' di matematica che manterrà il carattere lo spostamento alla stessa velocità. Per spiegare il motivo per cui questo aspetto è importante, si consideri una situazione in cui l'utente tocca il 500 pixel sullo schermo lontano da in cui si trova il carattere. La prima riga dove `desiredVelocity.X` è set necessario assegnare un valore pari a 500. Tuttavia, se l'utente sono state toccare lo schermo a una distanza di solo 100 unità di caratteri, il `desiredVelocity.X `verrebbe impostato su 100. Il risultato sarebbe che velocità del movimento del carattere risponde al modo in cui a portata di mano che il punto di tocco viene dal carattere. Poiché si desidera che il carattere per spostare sempre la stessa velocità, è necessario modificare il desiredVelocity.

Il `if (desiredVelocity.X != 0 || desiredVelocity.Y != 0)` istruzione sta verificando se la velocità è non-zero-in altre parole, verifica per assicurarsi che l'utente non tocca stesso punto come posizione del carattere corrente. Se non, quindi è necessario impostare la velocità del carattere sia costante indipendentemente dalla modalità a portata di mano il tocco è. È effettuare questa operazione normalizzare il vettore di velocità, che comporta un'esecuzione di una lunghezza pari a 1. Un vettore di velocità pari a 1 indica che il carattere verrà spostati a 1 pixel al secondo. È possibile accelerare l'accesso moltiplicando il valore per la velocità desiderata pari a 200.


### <a name="applying-velocity-to-position"></a>Applicazione di velocità per posizione

La velocità restituita da `GetDesiredVelocityFromInput` deve essere applicato per il carattere `X` e `Y` valori abbia effetto in fase di esecuzione. Modifichiamo il `Update` metodo come indicato di seguito:

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

Ciò che abbiamo implementato di seguito viene chiamato *basati sul tempo* lo spostamento dei (in contrapposizione a *basato sul fotogramma* spostamento). Spostamento basati sul tempo moltiplica un valore di velocità (in questo caso i valori archiviati nel `velocity` variabile) dalla quantità di tempo trascorso dall'ultimo aggiornamento che viene archiviato in `gameTime.ElapsedGameTime.TotalSeconds`. Se il gioco viene eseguito a un minor numero di fotogrammi al secondo, aumenta il tempo trascorso tra i frame, il risultato finale è che gli oggetti utilizzando lo spostamento dei limiti di tempo verranno spostati sempre la stessa velocità indipendentemente dalla frequenza dei fotogrammi.

Se eseguiamo ora nostro gioco, si noterà che il carattere è lo spostamento verso la posizione di tocco:

![](part2-images/image6.gif "Il carattere viene spostata verso la posizione di tocco")

## <a name="matching-movement-and-animation"></a>Lo spostamento e animazione corrispondenti

Dopo aver ottenuto il carattere lo spostamento e la riproduzione di una singola animazione, è possibile definire la parte restante del nostro animazioni, quindi usarli in modo da riflettere lo spostamento del carattere. Quando completato saranno disponibili le otto animazioni in totale:

- Animazioni per scorrere verso l'alto, verso il basso a sinistra e a destra
- Le animazioni per la condizione ancora e rivolto verso l'alto, verso il basso, a sinistra e destra

### <a name="defining-the-rest-of-the-animations"></a>Che definisce il resto delle animazioni

Prima di tutto si aggiungerà il `Animation` alle istanze di `CharacterEntity` classe per tutti i nostri animazioni nella stessa posizione in cui è stato aggiunto `walkDown`. Esecuzione di questa operazione, il `CharacterEntity` avrà il seguente `Animation` membri:

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

A questo punto verranno definiti le animazioni nel `CharacterEntity` costruttore come illustrato di seguito:

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

Si noti che il codice precedente è stato aggiunto per il `CharacterEntity` costruttore per mantenere questa procedura dettagliata più breve. In genere i giochi verranno separare la definizione delle animazioni di carattere nelle proprie classi o caricare queste informazioni da un formato di dati, ad esempio XML o JSON.

Quindi, verranno modificati la logica per usare le animazioni in base alla direzione che il carattere è in movimento o in base l'ultima animazione, se il carattere è appena stata interrotta. A tale scopo, si modificherà il `Update` metodo:


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

Il codice per passare le animazioni è suddiviso in due blocchi. Il primo controlla se la velocità non è uguale a `Vector2.Zero` : indica se il carattere è in movimento. Se il carattere è in movimento, quindi è possibile esaminare i `velocity.X` e `velocity.Y` valori per determinare quali animazione in movimento da riprodurre.

Se il carattere non è in movimento, allora sarà necessario impostare il carattere `currentAnimation` per un'animazione in esecuzione, ma solo avviene se la `currentAnimation` è un esame animazione o se non è stata impostata un'animazione. Se il `currentAnimation` non è uno dei quattro animazioni in movimento, quindi il carattere è già attiva e è necessario modificare `currentAnimation`.

Il risultato di questo codice è che il carattere verrà animare correttamente quando si esamina e quindi devono affrontare la direzione ultimo che è stata walking quando si interrompe:

![](part2-images/image7.gif "Il risultato di questo codice è che il carattere verrà animare correttamente quando si esamina e quindi devono affrontare la direzione ultimo che è stata walking quando si interrompe")

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata ha illustrato come lavorare con MonoGame per multi-piattaforma creare giochi con Sprite, lo spostamento di oggetti, rilevamento dell'input e animazione. Viene descritto come creare una classe di animazione per utilizzo generico. È stato anche illustrato come creare un'entità carattere per l'organizzazione logica del codice.

## <a name="related-links"></a>Collegamenti correlati

- [Risorsa immagine CharacterSheet (esempio)](https://github.com/xamarin/mobile-samples/blob/master/WalkingGameMG/Resources/charactersheet.png?raw=true)
- [Analisi completa di gioco (esempio)](https://developer.xamarin.com/samples/mobile/WalkingGameMG/)
