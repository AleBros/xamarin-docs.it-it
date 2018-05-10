---
title: Entità in CocosSharp
description: Il modello di entità è un metodo efficace per organizzare codice del gioco. Migliora la leggibilità, codice risulta più facile da gestire e utilizza le funzionalità predefinite padre/figlio.
ms.prod: xamarin
ms.assetid: 1D3261CE-AC96-4296-8A53-A76A42B927A8
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 58a8d4e6fcb8a2165fafad74a5c59481d1550351
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="entities-in-cocossharp"></a>Entità in CocosSharp

_Il modello di entità è un metodo efficace per organizzare codice del gioco. Migliora la leggibilità, codice risulta più facile da gestire e utilizza le funzionalità predefinite padre/figlio._

Il modello di entità può migliorare le attività dello sviluppatore con CocosSharp all'interno dell'organizzazione di miglioramento del codice. Questa procedura dettagliata è un esempio di esercitazione che illustra come creare due entità: un'entità di spedizione e di un'entità punto elenco. Queste entità sarà oggetti indipendenti, il che significa che una volta creata un'istanza verranno visualizzati automaticamente e verrà eseguita la logica di spostamento come appropriato per il relativo tipo. 

Questa guida vengono trattati gli argomenti seguenti:

 - Introduzione alle entità di gioco
 - Generale e tipi di entità specifico
 - Impostazione di progetto
 - Creazione di classi di entità
 - Aggiunta di istanze di entità per il `GameLayer`
 - Reazione alla logica di entità nel `GameLayer`

Il gioco completato sarà simile al seguente:

![](entities-images/image1.png "Il gioco completato sarà simile al seguente")


## <a name="introduction-to-game-entities"></a>Introduzione alle entità di gioco

Le entità di gioco sono le classi che definiscono gli oggetti che richiedono la logica di rendering, il conflitto, fisica o intelligenza artificiale. Fortunatamente, le entità presenti nel codice del gioco una base corrispondono spesso gli oggetti in un gioco concettuali. In tal caso, identificare le entità necessarie in un gioco può essere più facilmente eseguita. 

Ad esempio, uno spazio con temi [girare em di gioco](http://en.wikipedia.org/wiki/Shoot_%27em_up) può includere le entità seguenti:

 - `PlayerShip`
 - `EnemyShip`
 - `PlayerBullet`
 - `EnemyBullet`
 - `CollectableItem`
 - `HUD`
 - `Environment`

Queste entità sarebbe le proprie classi del gioco e ogni istanza, sarebbe necessario senza alcuna installazione oltre la creazione di istanze.


## <a name="general-vs-specific-entity-types"></a>Generale e tipi di entità specifico

Una delle prime domande affrontate dagli sviluppatori di giochi con un sistema di entità è la quantità per generalizzare le relative entità. Le implementazioni più specifico definirebbe le classi per ogni tipo di entità, anche se si differenziano per alcune caratteristiche. Sistemi più generali verranno combinare i gruppi di entità in una classe e consentire alle istanze da personalizzare.

È ad esempio possibile supporre un gioco di spazio che definisce le classi seguenti:

 - `PlayerDogfighter`
 - `PlayerBomber`
 - `EnemyMissileShip`
 - `EnemyLaserShip`

Potrebbe essere un approccio più generalizzato di creare un'unica classe per Windows Media player navi e un'unica classe per navi avversario, che possono essere configurati per supportare i tipi di spedizione diverse. Personalizzazione può includere immagini per caricare, il tipo di entità di punto elenco per creare durante la ripresa, i coefficienti di movimento, e viene fornita la logica per il nemico AI. In questo caso l'elenco di entità può essere ridotto a:

 - `PlayerShip`
 - `EnemyShip`

Naturalmente, questi tipi di entità possono essere generalizzati ulteriormente, consentendo la personalizzazione di ogni istanza per il controllo di spostamento. Le istanze di lettore spedizione verranno letto dall'input mentre le istanze di spedizione avversario possono eseguire la logica AI. Ciò significa che le entità potrebbero essere generalizzate ulteriormente in un'unica classe:

 - `Ship`

La generalizzazione può continuare ulteriormente – un gioco può utilizzare una singola classe di base per tutte le entità. Questa classe singola, che può essere chiamata `GameEntity`, sarebbe la classe utilizzata per ogni istanza di entità del gioco intera, incluse le entità che non sono disponibile quali elenchi puntati e gli elementi ritirabili (power-ups).

Questo generale la maggior parte dei sistemi è noto anche come un *sistema componente*. In un sistema, le entità di gioco possono avere singoli componenti, ad esempio fisica, AI, o il rendering di componenti aggiunti per personalizzare l'aspetto e comportamento. Puri sistemi basati su componente abilitare la massima flessibilità e possono evitare i problemi causati dall'uso di ereditarietà, ad esempio catene di ereditarietà complessi. Come con altri generalizzazioni, sistemi componente effettivo possono essere difficili da configurare e possono ridurre l'espressività del codice.

Il livello di generalizzazione usato dipende da considerare molti aspetti, tra cui:

 - Dimensione gioco: giochi più piccole possono permettersi di creare classi specifiche, mentre più grande giochi potrebbero essere difficile da gestire con un numero elevato di classi.
 - Dati: sviluppo basato su giochi che si basano sui dati (immagini, i modelli 3D e i file di dati come JSON o XML) può risultare vantaggioso disporre generalizzato di tipi di entità e le specifiche in base ai dati di configurazione. Ciò è particolarmente import per i giochi che prevede di aggiungere il nuovo contenuto durante lo sviluppo o dopo il gioco è stato rilasciato.
 - Modelli di motore di giochi: alcuni motori di giochi consiglia l'utilizzo dei sistemi componente mentre altre consentono agli sviluppatori di decidere come organizzare le entità. CocosSharp non richiedono l'utilizzo di un sistema di componente, per gli sviluppatori hanno la possibilità di implementare qualsiasi tipo di entità. 

Per ragioni di semplicità, verrà usato un approccio basato su classe specifico con una singola entità di spedizione e di punto elenco per questa esercitazione.


## <a name="project-setup"></a>Impostazione di progetto

Prima di iniziare l'implementazione di questo tipo di entità, è necessario creare un progetto. I modelli di progetto CocosSharp verrà usato per semplificare la creazione del progetto. [Controllare questo post](http://forums.xamarin.com/discussion/26822/cocossharp-project-templates-for-xamarin-studio) per informazioni sulla creazione di un progetto CocosSharp da Visual Studio per i modelli di Mac. Il resto di questa Guida verrà utilizzato il nome del progetto **EntityProject**.

Una volta creato il progetto verrà impostata la risoluzione di questo gioco per l'esecuzione a 480 x 320. A tale scopo, chiamare `CCScene.SetDefaultDesignResolution` nel `GameAppDelegate.ApplicationDidFinishLaunching` metodo come indicato di seguito:


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    ...

    // New code for resolution setting:
    CCScene.SetDefaultDesignResolution(480, 320, CCSceneResolutionPolicy.ShowAll);
    
    CCScene scene = new CCScene (mainWindow);
    GameLayer gameLayer = new GameLayer ();

    scene.AddChild (gameLayer);
    mainWindow.RunWithScene (scene);
} 
```

Per ulteriori informazioni sulla gestione delle CocosSharp risoluzioni, vedere il nostro [Guida sulla gestione di più risoluzioni in CocosSharp](~/graphics-games/cocossharp/resolutions.md).


## <a name="adding-content-to-the-project"></a>Aggiunta di contenuto al progetto

Dopo aver creato il progetto, si aggiungeranno i file contenuti in [il file zip contenuto](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true). A tale scopo, scaricare il file zip e decomprimerlo. Aggiungere i **ship.png** e **bullet.png** per il **contenuto** cartella. Il **contenuto** cartella sarà all'interno di **asset** cartella in Android e sarà alla radice del progetto in iOS. Una volta aggiunti, dovremmo vedere in entrambi i file di **contenuto** cartella:

![](entities-images/image2.png "Una volta aggiunti, entrambi i file devono trovarsi nella cartella contenuta")


## <a name="creating-the-ship-entity"></a>Creazione dell'entità di spedizione

La `Ship` classe sarà prima entità nostri del gioco. Per aggiungere un `Ship` di classi, creare innanzitutto una cartella denominata **entità** a livello di radice del progetto. Aggiungere una nuova classe nel **entità** cartella denominata `Ship`:

![](entities-images/image3.png "Aggiungere una nuova classe nella cartella entità chiamata spedizione")

La prima modifica da apportare al nostro `Ship` è consentire che ereditano dalla classe di `CCNode` classe. `CCNode` funge da classe base per classi CocosSharp comuni come `CCSprite` e `CCLayer`e offre le funzionalità seguenti:

 - `Position` proprietà per lo spostamento di spedizione sullo schermo.
 - `Children` proprietà per l'aggiunta di un `CCSprite.`
 - `Parent` proprietà, che può essere usata per collegare `Ship` istanze loro `CCNodes`. È non usare questa funzionalità in questa esercitazione; giochi maggiore spesso sfruttano i vantaggi del collegamento di entità loro `CCNodes`. 
 - `AddEventListener` metodo per la risposta di input per lo spostamento di spedizione.
 - `Schedule` metodo per la ripresa di elenchi puntati.

Verrà inoltre aggiunto un `CCSprite` istanza in modo che la spedizione può essere visualizzati sullo schermo:


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Ship : CCNode
    {
        CCSprite sprite;

        public Ship () : base()
        {
            sprite = new CCSprite ("ship.png");
            // Center the Sprite in this entity to simplify
            // centering the Ship when it is instantiated
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);
        }
    }
}
```

Successivamente, aggiungeremo la spedizione al nostro `GameLayer` per visualizzare il gioco:


```csharp
public class GameLayer : CCLayer
{
    Ship ship;

    public GameLayer ()
    {
        ship = new Ship ();
        ship.PositionX = 240;
        ship.PositionY = 50;
        this.AddChild (ship);
    } 
    ...
```

Se Esegui il gioco è ora verrà visualizzato l'entità di spedizione:

![](entities-images/image4.png "Quando si esegue il gioco, verrà visualizzato l'entità di spedizione")


### <a name="why-inherit-from-ccnode-instead-of-ccsprite"></a>Motivo per cui ereditare CCNode anziché CCSprite?

A questo punto il nostro `Ship` classe è un semplice wrapper per un `CCSprite` istanza. Poiché `CCSprite` eredita inoltre dalla `CCNode`, è possibile avere ereditata direttamente da `CCSprite`, che verrebbe ridotta di codice in `Ship.cs`. Inoltre, che eredita direttamente da `CCSprite` riduce il numero di oggetti in memoria e migliorare le prestazioni, ridurre le dimensioni l'albero delle dipendenze.

Nonostante questi vantaggi, è ereditata da `CCNode` per nascondere alcune il `CCSprite` le proprietà da ogni istanza. Ad esempio, il `Texture` proprietà non deve essere modificata all'esterno del `Ship` classe e che eredita da `CCNode` consente di nascondere questa proprietà. I membri pubblici dell'entità diventano particolarmente importanti quando un gioco aumenta e gli sviluppatori aggiuntivi vengono aggiunti a un team.


## <a name="adding-input-to-the-ship"></a>Aggiunta dell'input per l'avviso di spedizione

Ora che la spedizione è visibile sullo schermo verrà aggiunta input. L'approccio sarà simile all'approccio adottato [Guida BouncingGame](~/graphics-games/cocossharp/bouncing-game.md), ad eccezione del fatto che si sarà inserire il codice per lo spostamento nel `Ship` classe anziché nel contenitore `CCLayer` o `CCScene`.

Aggiungere il codice per `Ship` per supportare lo spostamento all'ovunque l'utente tocca lo schermo:


```csharp
public class Ship : CCNode
{
    CCSprite sprite;

    CCEventListenerTouchAllAtOnce touchListener;

    public Ship () : base()
    {
        sprite = new CCSprite ("ship.png");
        // Center the Sprite in this entity to simplify
        // centering the Ship on screen
        sprite.AnchorPoint = CCPoint.AnchorMiddle;
        this.AddChild(sprite);

        touchListener = new CCEventListenerTouchAllAtOnce();
        touchListener.OnTouchesMoved = HandleInput;
        AddEventListener(touchListener, this);

    }

    private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
    {
        if(touches.Count > 0)
        {
            CCTouch firstTouch = touches[0];

            this.PositionX = firstTouch.Location.X;
            this.PositionY = firstTouch.Location.Y;
        } 
    }
} 
```

Molte riprese em di giochi implementare una velocità massima, che rispecchia lo spostamento dei controller tradizionale. Ciò premesso, verrà semplicemente implementato lo spostamento immediato per mantenere il codice più breve.


## <a name="creating-the-bullet-entity"></a>Creazione dell'entità punto elenco

La seconda entità in questo semplice gioco è un'entità per la visualizzazione di elenchi puntati. Analogamente il `Ship` entità, il `Bullet` entità conterrà un `CCSprite` in modo che venga visualizzato sullo schermo. La logica per lo spostamento è diverso in quanto non dipende dall'input dell'utente per lo spostamento. piuttosto, `Bullet` istanze verranno spostato in una linea retta utilizzando le proprietà di velocità.

Innanzitutto verrà aggiunto un nuovo file di classe per il nostro **entità** cartella e chiamarlo **punto**:

![](entities-images/image5.png "Aggiungere un nuovo file di classe per la cartella di entità e chiamarlo punto elenco")

Dopo aver aggiunto verrà modificata la `Bullet.cs` codice come segue:


```csharp
using System;
using CocosSharp;

namespace EntityProject
{
    public class Bullet : CCNode
    {
        CCSprite sprite;

        public float VelocityX
        {
            get;
            set;
        }

        public float VelocityY
        {
            get;
            set;
        }

        public Bullet () : base()
        {
            sprite = new CCSprite ("bullet.png");
            // Making the Sprite be centered makes
            // positioning easier.
            sprite.AnchorPoint = CCPoint.AnchorMiddle;
            this.AddChild(sprite);

            this.Schedule (ApplyVelocity);
        }

        void ApplyVelocity(float time)
        {
            PositionX += VelocityX * time;
            PositionY += VelocityY * time;
        }
    }
} 
```

A parte la modifica del file utilizzato per il `CCSprite` a `bullet.png`, il codice in `ApplyVelocity` include la logica di spostamento che si basa su due coefficienti: `VelocityX` e `VelocityY`.

Il `Schedule` metodo consente di aggiungere i delegati per chiamare ogni fotogramma. In questo caso è in corso l'aggiunta di `ApplyVelocity` metodo in modo che si sposta il punto elenco in base ai relativi valori di velocità. Il `Schedule` metodo accetta un `Action<float>`, in cui il parametro di tipo float specifica la quantità di tempo (in secondi) dopo l'ultimo fotogramma utilizzato per implementare lo spostamento basato sul tempo. Dopo il valore viene misurato in secondi, quindi i valori di velocità rappresentano lo spostamento in *pixel al secondo*.


## <a name="adding-bullets-to-gamelayer"></a>Aggiunta di elenchi puntati a GameLayer

Prima di qualsiasi aggiungiamo `Bullet` istanze per il gioco si consentirà un contenitore, in particolare un `List<Bullet>`. Modificare il `GameLayer` in modo da includere un elenco di elenchi puntati:


```csharp
    public class GameLayer : CCLayer
    {
        Ship ship;
        List<Bullet> bullets;

        public GameLayer ()
        {
            ship = new Ship ();
            ship.PositionX = 240;
            ship.PositionY = 50;
            this.AddChild (ship);

            bullets = new List<Bullet> ();
        }
        ... 
```

Successivamente è necessario popolare il `Bullet` elenco. La logica per la creazione di un `Bullet` devono essere contenuti nel `Ship` entità, ma la `GameLayer` è responsabile per l'archiviazione dell'elenco di punti. Verrà utilizzato il modello di factory per consentire il `Ship` entità da creare `Bullet` istanze. Questa factory esporrà un evento che il `GameLayer` in grado di gestire. 

Per eseguire questa operazione in primo luogo, verrà aggiunto una cartella al progetto denominato **factory**, quindi aggiungere una nuova classe denominata `BulletFactory`:

![](entities-images/image6.png "Aggiungere una cartella al progetto denominato factory, e quindi aggiungere una nuova classe denominata BulletFactory")

Successivamente, verrà implementato il `BulletFactory` classe singleton:


```csharp
using System;

namespace EntityProject
{
    public class BulletFactory
    {
        static Lazy<BulletFactory> self = 
            new Lazy<BulletFactory>(()=>new BulletFactory());

        // simple singleton implementation
        public static BulletFactory Self
        {
            get
            {
                return self.Value;
            }
        }

        public event Action<Bullet> BulletCreated;

        private BulletFactory()
        {

        }

        public Bullet CreateNew()
        {
            Bullet newBullet = new Bullet ();

            if (BulletCreated != null)
            {
                BulletCreated (newBullet);
            }

            return newBullet;
        }
    }
} 
```

Il `Ship` consente di gestire la creazione di entità `Bullet` istanze, in particolare, gestirà la frequenza `Bullet` istanze devono essere create (ad esempio la frequenza con cui viene attivato il punto elenco), la posizione e la velocità.

Modificare il `Ship` costruttore dell'entità per aggiungere un nuovo `Schedule` chiamare e quindi implementare questo metodo, come indicato di seguito:


```csharp
...
public Ship () : base()
{
    sprite = new CCSprite ("ship.png");
    // Center the Sprite in this entity to simplify
    // centering the Ship on screen
    sprite.AnchorPoint = CCPoint.AnchorMiddle;
    this.AddChild(sprite);

    touchListener = new CCEventListenerTouchAllAtOnce();
    touchListener.OnTouchesMoved = HandleInput;
    AddEventListener(touchListener, this);

    Schedule (FireBullet, interval: 0.5f);

}

void FireBullet(float unusedValue)
{
    Bullet newBullet = BulletFactory.Self.CreateNew ();
    newBullet.Position = this.Position;
    newBullet.VelocityY = 100;
} 
...
```

L'ultimo passaggio consiste nel gestire la creazione di nuovi `Bullet` istanze di `GameLayer` codice. Aggiungere un gestore eventi per il `BulletCreated` evento che si aggiunge l'oggetto appena creato `Bullet` agli elenchi appropriati:


```csharp
...
public GameLayer ()
{
    ship = new Ship ();
    ship.PositionX = 240;
    ship.PositionY = 50;
    this.AddChild (ship);

    bullets = new List<Bullet> ();
    BulletFactory.Self.BulletCreated += HandleBulletCreated;
}

void HandleBulletCreated(Bullet newBullet)
{
    AddChild (newBullet);
    bullets.Add (newBullet);
}
... 
```

Ora è possibile eseguire il gioco e vedere il `Ship` risoluzione `Bullet` istanze:

![](entities-images/image1.png "Esecuzione del gioco e di spedizione verrà le riprese le istanze di elenco puntato")


## <a name="why-gamelayer-has-ship-and-bullets-members"></a>Motivo per cui GameLayer dispone di membri di spedizione e di elenchi puntati

Il nostro `GameLayer` classe definisce due campi per mantenere i riferimenti ai nostri istanze di entità (`ship` e `bullets`), ma non esegue alcuna operazione con essi. Inoltre, le entità sono responsabili per il proprio comportamento, ad esempio lo spostamento e la ripresa. Pertanto perché aggiungiamo `ship` e `bullets` campi `GameLayer`?

La ragione è aggiunto a questi membri è un'implementazione del gioco completa richiederebbe logica di `GameLayer` per l'interazione tra le diverse entità. Ad esempio, il gioco può essere ulteriormente sviluppato per includere i nemici che è possibile eliminare definitivamente dal lettore. Questi nemici sarebbe contenuti un `List` nel `GameLayer`e la logica per testare se `Bullet` istanze sono in conflitto con i nemici verrebbero eseguiti nel `GameLayer` anche. In altre parole, il `GameLayer` è la radice *proprietario* dell'entità di tutte le istanze e è responsabile per le interazioni tra istanze di entità.


## <a name="bullet-destruction-considerations"></a>Considerazioni sull'eliminazione di punto elenco

Il gioco attualmente non dispone di codice per l'eliminazione definitiva `Bullet` istanze. Ogni `Bullet` istanza dispone di una logica per lo spostamento nella schermata, ma è ancora stato aggiunto il codice per eliminare qualsiasi schermo `Bullet` istanze.

Inoltre, la distruzione del `Bullet` istanze potrebbero non appartenere `GameLayer`. Ad esempio, anziché eliminata definitivamente quando fuori dello schermo, il `Bullet` entità può disporre di logica per eliminare automaticamente dopo un determinato periodo di tempo. In questo caso, il `Bullet` deve disporre di un modo per comunicare che devono essere eliminato al `GameLayer`, molto simile di `Ship` entità comunicati al `GameLayer` che un nuovo `Bullet` è stata creata tramite il `BulletFactory`.

La soluzione più semplice consiste nell'espandere la responsabilità della classe factory per supportare la distruzione. Quindi la factory può ricevere una notifica di un'istanza di entità viene eliminata, che può essere gestita da altri oggetti, ad esempio il `GameLayer` rimozione dell'istanza di entità relativi elenchi. 

## <a name="summary"></a>Riepilogo

Questa guida viene illustrato come creare le entità CocosSharp ereditando dalla classe di `CCNode` classe. Queste entità sono oggetti indipendenti, la creazione di propri oggetti visivi e la logica personalizzata di gestione. Questa Guida consente di definire il codice che appartiene all'interno di un'entità (lo spostamento e la creazione di altre entità) dal codice che fa parte il contenitore di entità radice (conflitti e logica di interazione tra altre entità).

## <a name="related-links"></a>Collegamenti correlati

- [Documentazione CocosSharp API](https://developer.xamarin.com/api/namespace/CocosSharp/)
- [Contenuto zip](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/Entities.zip?raw=true)
