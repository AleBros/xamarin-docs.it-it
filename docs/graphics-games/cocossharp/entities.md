---
title: Entità in CocosSharp
description: Il modello di entità è un metodo efficace per organizzare il codice del gioco. Migliora la leggibilità, rende più facile da gestire, il codice e utilizza le funzionalità predefinite padre/figlio.
ms.prod: xamarin
ms.assetid: 1D3261CE-AC96-4296-8A53-A76A42B927A8
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 42034261c374183346c8072eb42014f43a4fe22c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61210380"
---
# <a name="entities-in-cocossharp"></a>Entità in CocosSharp

_Il modello di entità è un metodo efficace per organizzare il codice del gioco. Migliora la leggibilità, rende più facile da gestire, il codice e utilizza le funzionalità predefinite padre/figlio._

Il modello di entità può migliorare le attività di uno sviluppatore con CocosSharp all'interno dell'organizzazione di codice migliorata. Questa procedura dettagliata sarà un esempio pratico che illustra come creare due entità: un'entità di spedizione e un'entità di punto elenco. Queste entità saranno oggetti indipendenti, vale a dire che una volta creata un'istanza che verranno automaticamente sottoposti a rendering e si eseguirà la logica di spostamento come appropriato per il loro tipo. 

Questa guida illustra gli argomenti seguenti:

 - Introduzione alle entità del gioco
 - Generale e i tipi di entità specifico
 - Configurazione progetto
 - Creazione di classi di entità
 - Aggiunta di istanze di entità per il `GameLayer`
 - Reazione a per la logica di entità in Azure il `GameLayer`

Il gioco completato avrà un aspetto simile al seguente:

![](entities-images/image1.png "Il gioco completato avrà un aspetto simile al seguente")


## <a name="introduction-to-game-entities"></a>Introduzione alle entità del gioco

Le entità del gioco sono classi che definiscono gli oggetti che richiedono la logica di rendering, conflitto, fisica o di intelligenza artificiale. Fortunatamente, le entità presenti nel codice del gioco di base corrispondano spesso gli oggetti concettuali in un gioco. In tal caso, identificare le entità necessarie in un gioco è più semplice possibile. 

Ad esempio, uno spazio con temi [girare assestate di gioco](https://en.wikipedia.org/wiki/Shoot_%27em_up) può includere le entità seguenti:

 - `PlayerShip`
 - `EnemyShip`
 - `PlayerBullet`
 - `EnemyBullet`
 - `CollectableItem`
 - `HUD`
 - `Environment`

Queste entità sarebbe le proprie classi nel gioco e ogni istanza richiederebbe minima o senza alcuna installazione oltre la creazione di istanze.


## <a name="general-vs-specific-entity-types"></a>Generale e i tipi di entità specifico

Una delle prime domande affrontate dagli sviluppatori di giochi usando un sistema di entità è la quantità per generalizzare le proprie entità. La più specifica delle implementazioni definirebbe le classi per ogni tipo di entità, anche se si differenziano per alcune caratteristiche. I sistemi più generale verranno combinare gruppi di entità in una classe e consentire alle istanze per la personalizzazione.

Ad esempio Possiamo immaginare un gioco di spazio che definisce le classi seguenti:

 - `PlayerDogfighter`
 - `PlayerBomber`
 - `EnemyMissileShip`
 - `EnemyLaserShip`

Un approccio più generalizzato consisterebbe nel creare un'unica classe per Windows Media player viene fornito e una sola classe per avversario viene fornito, che può essere configurato per supportare i tipi di spedizione diverse. Personalizzazione può includere l'immagine per il caricamento, il tipo di entità di punto elenco da creare quando si riprende, i coefficienti di spostamento, e viene fornito per la logica per intelligenza artificiale per il nemico. In questo caso l'elenco delle entità può essere ridotto a:

 - `PlayerShip`
 - `EnemyShip`

Naturalmente, questi tipi di entità possono essere generalizzati ulteriormente, consentendo di personalizzazione per istanza per il controllo di spostamento. Le istanze di Windows Media Player ship legge dall'input mentre le istanze di spedizione avversario potrebbero eseguire per la logica per intelligenza artificiale. Ciò significa che l'entità è stato possibile procedere alla generalizzazione ulteriormente in un'unica classe:

 - `Ship`

La generalizzazione può continuare ulteriormente: un gioco potrebbe usare una singola classe di base per tutte le entità. Questa classe singola, che può essere chiamata `GameEntity`, sarebbe la classe usata per ogni istanza di entità in gioco intero, tra cui le entità non viene fornito come elenchi puntati e oggetti da collezione (power-up).

Ciò generale la maggior parte dei sistemi è noto anche come un *sistema componente*. In tale sistema, le entità del gioco possono avere i singoli componenti, ad esempio fisica, intelligenza artificiale, o il rendering di componenti aggiunti per personalizzare il comportamento e aspetto. Sistemi basati su componenti puri consentono la flessibilità di ultimate e possano evitare problemi causati dall'utilizzo dell'ereditarietà, ad esempio catene di eredità complessa. Come con altri generalizzazioni, componente efficaci sistemi possono essere difficili da configurare e consente di ridurre l'espressività del codice.

Il livello della generalizzazione usato dipende da molte considerazioni, tra cui:

 - Dimensioni del gioco, giochi più piccoli possono permettersi di creare classi specifiche, mentre i giochi più grandi potrebbero essere difficili da gestire con un numero elevato di classi.
 - Data driven development-giochi che si basano sui dati (immagini, modelli 3D e i file di dati come JSON o XML) potrebbero trarre vantaggio dall'avere generalizzato tipi di entità e le specifiche basate sui dati di configurazione. Ciò è particolarmente import per i giochi che prevede di aggiungere il nuovo contenuto durante lo sviluppo o dopo il rilascio di gioco.
 - Modelli di motore di gioco – alcuni motori di gioco caldamente di utilizzare l'utilizzo dei sistemi componente mentre altre consentono agli sviluppatori di decidere come organizzare le entità. CocosSharp non richiede l'utilizzo di un sistema di componente, in modo che gli sviluppatori sono liberi di implementare qualsiasi tipo di entità. 

Per ragioni di semplicità, verrà usato un approccio basato su classe specifico con una singola entità di spedizione e di punto elenco per questa esercitazione.


## <a name="project-setup"></a>Configurazione progetto

Prima di iniziare l'implementazione delle nostre entità, è necessario creare un progetto. I modelli di progetto CocosSharp verrà usato per semplificare la creazione del progetto. [Controllare questo post](http://forums.xamarin.com/discussion/26822/cocossharp-project-templates-for-xamarin-studio) per informazioni sulla creazione di un progetto CocosSharp da Visual Studio per i modelli Mac. Il resto di questa Guida verrà utilizzato il nome del progetto **EntityProject**.

Dopo aver creato il progetto verrà impostata la risoluzione del nostro gioco per l'esecuzione a 320 x 480. A tale scopo, chiamare `CCScene.SetDefaultDesignResolution` nella `GameAppDelegate.ApplicationDidFinishLaunching` metodo come indicato di seguito:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...

    // New code for resolution setting:
    CCScene.SetDefaultDesignResolution(480, 320, CCSceneResolutionPolicy.ShowAll);
    
    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);
    mainWindow.RunWithScene (scene);
} 
```

Per altre informazioni sulla gestione con CocosSharp risoluzioni, vedere la [Guida sulla gestione di più risoluzioni in CocosSharp](~/graphics-games/cocossharp/resolutions.md).


## <a name="adding-content-to-the-project"></a>Aggiunta di contenuto al progetto

Dopo aver creato il progetto, si aggiungerà i file contenuti nei [questo file zip contenuto](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true). A tale scopo, scaricare il file zip e decomprimerlo. Aggiungi entrambi **ship.png** e **bullet.png** per il **contenuto** cartella. Il **contenuto** cartella sarà all'interno di **asset** cartella in Android e dovrà essere eseguita la radice del progetto su iOS. Una volta aggiunto, dovremmo vedere entrambi i file nei **contenuto** cartella:

![](entities-images/image2.png "Una volta aggiunto, devono essere entrambi i file nella cartella del contenuto")


## <a name="creating-the-ship-entity"></a>Creare l'entità di spedizione

Il `Ship` classe sarà entità prima del nostro gioco. Per aggiungere un `Ship` classe, creare innanzitutto una cartella denominata **entità** a livello di radice del progetto. Aggiungere una nuova classe il **Entities** cartella denominata `Ship`:

![](entities-images/image3.png "Aggiungere una nuova classe nella cartella dell'entità denominata Ship")

La prima modifica apportare al nostro `Ship` è lasciare che ereditano dalla classe di `CCNode` classe. `CCNode` funge da classe base per classi CocosSharp comuni, ad esempio `CCSprite` e `CCLayer`e offre le funzionalità seguenti:

 - `Position` proprietà per lo spostamento di spedizione attorno alla schermata.
 - `Children` proprietà per l'aggiunta di un `CCSprite.`
 - `Parent` proprietà, che può essere utilizzata per collegare `Ship` istanze loro `CCNodes`. Questa funzionalità non verrà usato in questa esercitazione. giochi di dimensioni maggiori spesso sfruttano i vantaggi del collegamento di entità loro `CCNodes`. 
 - `AddEventListener` metodo per la risposta di input per lo spostamento di spedizione.
 - `Schedule` metodo per la ripresa di elenchi puntati.

Viene anche aggiunto un `CCSprite` dell'istanza in modo che la spedizione può essere visualizzato sullo schermo:


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Ship : CCNode
    {
        CCSprite sprite;

        public Ship () : base()
        {
            sprite = new CCSprite ("ship.png");
            // Center the Sprite in this entity to simplify
            // centering the Ship when it is instantiated
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);
        }
    }
}
```

Successivamente, si aggiungerà la spedizione al nostro `GameLayer` per osservare come venga visualizzato nel nostro gioco:


```csharp
public class GameLayer : CCLayer
{
    Ship ship;

    public GameLayer ()
    {
        ship = new Ship ();
        ship.PositionX = 240;
        ship.PositionY = 50;
        this.AddChild (ship);
    } 
    ...
```

Se è possibile eseguire il gioco a questo punto si vedrà l'entità di spedizione:

![](entities-images/image4.png "Quando si esegue il gioco, verrà visualizzata l'entità di spedizione")


### <a name="why-inherit-from-ccnode-instead-of-ccsprite"></a>Il motivo per cui ereditare CCNode anziché CCSprite?

A questo punto nostri `Ship` classe è un semplice wrapper per un `CCSprite` istanza. Poiché `CCSprite` eredita anche dal `CCNode`, è possibile avere ereditati direttamente dalla `CCSprite`, che sarebbe stato ridotto il codice in `Ship.cs`. Inoltre, che eredita direttamente da `CCSprite` riduce il numero di oggetti in memoria e di migliorare le prestazioni, rendendo l'albero delle dipendenze più piccoli.

Nonostante questi vantaggi, è ereditata da `CCNode` per nascondere alcune il `CCSprite` proprietà da ogni istanza. Ad esempio, il `Texture` proprietà non deve essere modificata di fuori del `Ship` classe e che eredita da `CCNode` consente di nascondere questa proprietà. I membri pubblici delle nostre entità diventano particolarmente importanti quando superano un gioco e altri sviluppatori vengono aggiunti a un team.


## <a name="adding-input-to-the-ship"></a>Aggiunta di input per la spedizione

Ora che la spedizione è visibile sullo schermo verranno aggiunte input. Il nostro approccio sarà simile all'approccio adottato nel [Guida di BouncingGame](~/graphics-games/cocossharp/bouncing-game.md), ad eccezione del fatto che è sarà possibile inserire il codice per lo spostamento nel `Ship` classe anziché nel contenitore `CCLayer` o `CCScene`.

Aggiungere il codice al `Ship` per supportare lo spostamento all'ovunque l'utente tocca la schermata:


```csharp
public class Ship : CCNode
{
    CCSprite sprite;

    CCEventListenerTouchAllAtOnce touchListener;

    public Ship () : base()
    {
        sprite = new CCSprite ("ship.png");
        // Center the Sprite in this entity to simplify
        // centering the Ship on screen
        sprite.AnchorPoint = CCPoint.AnchorMiddle;
        this.AddChild(sprite);

        touchListener = new CCEventListenerTouchAllAtOnce();
        touchListener.OnTouchesMoved = HandleInput;
        AddEventListener(touchListener, this);

    }

    private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
    {
        if(touches.Count > 0)
        {
            CCTouch firstTouch = touches[0];

            this.PositionX = firstTouch.Location.X;
            this.PositionY = firstTouch.Location.Y;
        } 
    }
} 
```

Molti girare assestate backup giochi implementano una velocità massima, che rispecchia lo spostamento sul lato controller tradizionale. Ciò premesso, è sufficiente implementeremo spostamento immediato per mantenere il codice più breve.


## <a name="creating-the-bullet-entity"></a>Creare l'entità elenco puntato

La seconda entità nel nostro semplice gioco è un'entità per la visualizzazione di elenchi puntati. Esattamente come le `Ship` entità, il `Bullet` entità conterrà un `CCSprite` in modo che venga visualizzato sullo schermo. La logica per lo spostamento è diverso in quanto non dipende dall'input dell'utente per lo spostamento. piuttosto, `Bullet` istanze verranno spostato in una linea retta utilizzando le proprietà della velocità.

Prima di tutto si aggiungerà un nuovo file di classe al nostro **Entities** cartella e chiamarlo **Bullet**:

![](entities-images/image5.png "Aggiungere un nuovo file di classe alla cartella entità e chiamarlo elenco puntato")

Una volta aggiunto a questo punto, modificheremo il `Bullet.cs` codice come indicato di seguito:


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Bullet : CCNode
    {
        CCSprite sprite;

        public float VelocityX
        {
            get;
            set;
        }

        public float VelocityY
        {
            get;
            set;
        }

        public Bullet () : base()
        {
            sprite = new CCSprite ("bullet.png");
            // Making the Sprite be centered makes
            // positioning easier.
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);

            this.Schedule (ApplyVelocity);
        }

        void ApplyVelocity(float time)
        {
            PositionX += VelocityX * time;
            PositionY += VelocityY * time;
        }
    }
} 
```

A parte la modifica del file utilizzato per il `CCSprite` al `bullet.png`, il codice nel `ApplyVelocity` include la logica di spostamento che si basa su due coefficienti: `VelocityX` e `VelocityY`.

Il `Schedule` metodo consente di aggiungere i delegati da chiamare ogni fotogramma. In questo caso stiamo aggiungendo il `ApplyVelocity` metodo in modo che si sposta il punto elenco in base ai relativi valori di velocità. Il `Schedule` metodo accetta un `Action<float>`, in cui il parametro di tipo float consente di specificare la quantità di tempo (in secondi) dopo l'ultimo fotogramma, viene usato per implementare lo spostamento basati sul tempo. Dal momento in valore viene misurato in secondi, quindi i valori di velocità rappresentano lo spostamento in *pixel al secondo*.


## <a name="adding-bullets-to-gamelayer"></a>Aggiunta di elenchi puntati a GameLayer

Prima di aggiungere eventuali `Bullet` istanze del nostro gioco Microsoft farà un contenitore, in particolare un `List<Bullet>`. Modificare il `GameLayer` in modo da includere un elenco di elenchi puntati:


```csharp
    public class GameLayer : CCLayer
    {
        Ship ship;
        List<Bullet> bullets;

        public GameLayer ()
        {
            ship = new Ship ();
            ship.PositionX = 240;
            ship.PositionY = 50;
            this.AddChild (ship);

            bullets = new List<Bullet> ();
        }
        ... 
```

Successivamente sarà necessario popolare il `Bullet` elenco. La logica di cui si vuole creare un `Bullet` devono essere contenute nel `Ship` entità, ma il `GameLayer` è responsabile per la memorizzazione dell'elenco di elenchi puntati. Si userà il modello di factory per consentire la `Ship` entità da creare `Bullet` istanze. Questa factory esporrà un evento che il `GameLayer` può gestire. 

Per eseguire questa operazione prima di tutto si aggiungerà una cartella per il progetto denominato **fabbriche**, quindi aggiungere una nuova classe denominata `BulletFactory`:

![](entities-images/image6.png "Aggiungere una cartella al progetto denominato factory e quindi aggiungere una nuova classe denominata BulletFactory")

Successivamente, viene implementato il `BulletFactory` classe singleton:


```csharp
using System;

namespace EntityProject
{
    public class BulletFactory
    {
        static Lazy<BulletFactory> self = 
            new Lazy<BulletFactory>(()=>new BulletFactory());

        // simple singleton implementation
        public static BulletFactory Self
        {
            get
            {
                return self.Value;
            }
        }

        public event Action<Bullet> BulletCreated;

        private BulletFactory()
        {

        }

        public Bullet CreateNew()
        {
            Bullet newBullet = new Bullet ();

            if (BulletCreated != null)
            {
                BulletCreated (newBullet);
            }

            return newBullet;
        }
    }
} 
```

Il `Ship` consente di gestire la creazione di entità `Bullet` istanze, in particolare, gestirà la frequenza `Bullet` le istanze devono essere create (ovvero frequenza viene attivato il punto elenco), la posizione e le velocità.

Modificare il `Ship` costruttore dell'entità per aggiungere un nuovo `Schedule` chiamare e quindi implementare questo metodo come indicato di seguito:


```csharp
...
public Ship () : base()
{
    sprite = new CCSprite ("ship.png");
    // Center the Sprite in this entity to simplify
    // centering the Ship on screen
    sprite.AnchorPoint = CCPoint.AnchorMiddle;
    this.AddChild(sprite);

    touchListener = new CCEventListenerTouchAllAtOnce();
    touchListener.OnTouchesMoved = HandleInput;
    AddEventListener(touchListener, this);

    Schedule (FireBullet, interval: 0.5f);

}

void FireBullet(float unusedValue)
{
    Bullet newBullet = BulletFactory.Self.CreateNew ();
    newBullet.Position = this.Position;
    newBullet.VelocityY = 100;
} 
...
```

L'ultimo passaggio consiste nel gestire la creazione di nuove `Bullet` istanze di `GameLayer` codice. Aggiungere un gestore eventi per il `BulletCreated` evento che aggiunge l'oggetto appena creato `Bullet` agli elenchi appropriati:


```csharp
...
public GameLayer ()
{
    ship = new Ship ();
    ship.PositionX = 240;
    ship.PositionY = 50;
    this.AddChild (ship);

    bullets = new List<Bullet> ();
    BulletFactory.Self.BulletCreated += HandleBulletCreated;
}

void HandleBulletCreated(Bullet newBullet)
{
    AddChild (newBullet);
    bullets.Add (newBullet);
}
... 
```

A questo punto possiamo eseguire il gioco e vedere le `Ship` ripresa `Bullet` istanze:

![](entities-images/image1.png "Eseguire il gioco e la spedizione verrà essere riprese le istanze di elenco puntato")


## <a name="why-gamelayer-has-ship-and-bullets-members"></a>Il motivo per cui GameLayer include membri ship ed elenchi puntati

Nostri `GameLayer` classe definisce due campi che contenga i riferimenti per le istanze di entità (`ship` e `bullets`), ma non esegue alcuna operazione con essi. Inoltre, le entità sono responsabili per il proprio comportamento, ad esempio lo spostamento e la ripresa. Pertanto, il motivo per cui è stata aggiungiamo `ship` e `bullets` i campi a `GameLayer`?

La ragione è stato aggiunto a questi membri è che un'implementazione completa del gioco richiederebbe la logica nel `GameLayer` per l'interazione tra le diverse entità. Ad esempio, questo gioco può essere ulteriormente sviluppato in modo da includere nemici che possono essere eliminato definitivamente dal lettore. Questi nemici sarebbe contenuti in una `List` nella `GameLayer`e per la logica per testare se `Bullet` istanze sono in conflitto con nemici creati verrebbe eseguite nel `GameLayer` anche. In altre parole, il `GameLayer` è la radice *proprietario* dell'entità di tutte le istanze ed è responsabile per le interazioni tra le istanze di entità.


## <a name="bullet-destruction-considerations"></a>Considerazioni sull'eliminazione elenco puntato

Il gioco attualmente non dispone di codice per l'eliminazione permanente `Bullet` istanze. Ciascuna `Bullet` istanza dispone di una logica per lo spostamento nella schermata, ma è ancora stato aggiunto codice per qualsiasi eliminare fuori dallo schermo `Bullet` istanze.

Inoltre, la distruzione del `Bullet` istanze non possono appartenere `GameLayer`. Ad esempio, anziché eliminata definitivamente quando fuori dello schermo, la `Bullet` entità potrebbero avere la logica da eliminare definitivamente se stesso dopo un determinato periodo di tempo. In questo caso, il `Bullet` necessita di un modo per comunicare che deve essere eliminato per il `GameLayer`, molto simile il `Ship` entità comunicati al `GameLayer` che un nuovo `Bullet` è stata creata tramite il `BulletFactory`.

La soluzione più semplice è necessario espandere la responsabilità della classe factory per supportare la distruzione. Quindi la factory può ricevere notifiche di un'istanza di entità in corso l'eliminazione, che può essere gestita da altri oggetti, ad esempio il `GameLayer` rimuovendo l'istanza di entità dal relativo elenco. 

## <a name="summary"></a>Riepilogo

Questa guida illustra come creare le entità CocosSharp ereditando dal `CCNode` classe. Queste entità sono oggetti indipendenti, gestisce la creazione di propri oggetti visivi e la logica personalizzata. Questa Guida designa codice appartenente all'interno di un'entità (spostamento e la creazione di altre entità) dal codice che fa parte il contenitore di entità radice (collisioni e altra logica di interazione dell'entità).

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione API CocosSharp](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [File zip del contenuto](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)
