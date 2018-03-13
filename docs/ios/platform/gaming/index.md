---
title: "modalità di gioco API iOS"
description: "In questo articolo vengono illustrati i nuovi miglioramenti di gioco forniti da iOS 9 che può essere utilizzato per migliorare le funzionalità audio e grafica di un gioco di xamarin. IOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: d0a66d4cfdb3050c7ad791d24e24d6917a031ee1
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="ios-gaming-apis"></a>modalità di gioco API iOS

_In questo articolo vengono illustrati i nuovi miglioramenti di gioco forniti da iOS 9 che può essere utilizzato per migliorare le funzionalità audio e grafica di un gioco di xamarin. IOS._

Apple ha diversi miglioramenti tecnologici per i giochi le API di iOS 9 che rendono più semplice implementare grafica di giochi e audio in un'app xamarin. IOS.
Questi includono entrambi semplicità di sviluppo tramite il framework di alto livello e sfruttare le potenzialità di GPU del dispositivo iOS per una migliore velocità e le capacità grafiche.

[![](images/flocking01.png "Un esempio di un'app in esecuzione floccaggio")](images/flocking01.png#lightbox)

Insieme a nuove funzionalità migliorate di metallo, SceneKit e SpriteKit inclusi GameplayKit, ReplayKit, i/o del modello, MetalKit e Metal prestazioni shader.

In questo articolo verrà presentate tutte le modalità per migliorare un gioco di xamarin. IOS con i nuovi miglioramenti di gioco iOS 9:

## <a name="introducing-gameplaykit"></a>Introduzione a GameplayKit

Il nuovo framework di GameplayKit Apple fornisce un set di tecnologie che rende più semplice creare giochi per dispositivi iOS, riducendo la quantità di codice ripetitivo, comuni necessari per l'implementazione. GameplayKit fornisce strumenti per lo sviluppo di rapidamente i meccanismi di giochi che possono quindi essere facilmente combinati con un motore grafico (ad esempio SceneKit o SpriteKit) per creare un gioco completato.

GameplayKit include diversi, comuni, gioco su algoritmi, ad esempio:

- Un comportamento di base, simulazione di agente che consente di definire i movimenti e gli obiettivi di AI proseguirà automaticamente.
- Un intelligence artificiale minmax per i giochi basato su.
- Un sistema di regola per la logica di gioco basati sui dati con fuzzy ragionevole per fornire un comportamento che potranno scaturire.

Inoltre, GameplayKit adotta un approccio di blocco predefinito per sviluppo di giochi con un'architettura modulare che offre le funzionalità seguenti:

- Macchina a stati per codice complesso, sulle procedure di gestione basato su sistemi di gioco.
- Strumenti per fornire casuale del gioco e potenziali senza causare problemi di debug.
- Un'architettura di componenti riutilizzabili basato su entità.

Per ulteriori informazioni sulla GameplayKit, vedere Apple [Guida per programmatori Gameplaykit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172) e [GameplayKit Framework riferimento](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199).

## <a name="gameplaykit-examples"></a>Esempi di GameplayKit

Esaminiamo un rapido implementazione alcuni meccanismi di gioco semplice in un'app xamarin con il kit di gioco.

### <a name="pathfinding"></a>Pathfinding

Pathfinding è la possibilità per un elemento di un gioco per trovare le regole per la tavola da gioco.
Ad esempio, un nemico 2D individuazione giungere in un labirinto o un carattere 3D tramite un terrain prima persona-strumenti-per world.

Si consideri la mappa seguente:

[![](images/gkpathfindpath.png "Un esempio di mapping di pathfinding")](images/gkpathfindpath.png#lightbox)

Pathfinding utilizzando il codice c# può individuare un modo tramite la mappa:

```csharp
var a = GKGraphNode2D.FromPoint (new Vector2 (0, 5));
var b = GKGraphNode2D.FromPoint (new Vector2 (3, 0));
var c = GKGraphNode2D.FromPoint (new Vector2 (2, 6));
var d = GKGraphNode2D.FromPoint (new Vector2 (4, 6));
var e = GKGraphNode2D.FromPoint (new Vector2 (6, 5));
var f = GKGraphNode2D.FromPoint (new Vector2 (6, 0));

a.AddConnections (new [] { b, c }, false);
b.AddConnections (new [] { e, f }, false);
c.AddConnections (new [] { d }, false);
d.AddConnections (new [] { e, f }, false);

var graph = GKGraph.FromNodes(new [] { a, b, c, d, e, f });

var a2e = graph.FindPath (a, e); // [ a, c, d, e ]
var a2f = graph.FindPath (a, f); // [ a, b, f ]

Console.WriteLine(String.Join ("->", (object[]) a2e));
Console.WriteLine(String.Join ("->", (object[]) a2f));
```

### <a name="classical-expert-system"></a>Sistema esperto classico

Il seguente frammento di codice c# viene illustrato come utilizzare GameplayKit per implementare un sistema esperto classico:

```csharp
string output = "";
bool reset = false;
int input = 15;

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    /*
    If reset is true, clear the output and set reset to false
    */
    var clearRule = GKRule.FromPredicate ((rules) => reset, rules => {
        output = "";
        reset = false;
    });
    clearRule.Salience = 1;

    var fizzRule = GKRule.FromPredicate (mod (3), rules => {
        output += "fizz";
    });
    fizzRule.Salience = 2;

    var buzzRule = GKRule.FromPredicate (mod (5), rules => {
        output += "buzz";
    });
    buzzRule.Salience = 2;

    /*
    This *always* evaluates to true, but is higher Salience, so evaluates after lower-salience items
    (which is counter-intuitive). Print the output, and reset (thus triggering "ResetRule" next time)
    */
    var outputRule = GKRule.FromPredicate (rules => true, rules => {
        System.Console.WriteLine(output == "" ? input.ToString() : output);
        reset = true;
    });
    outputRule.Salience = 3;

    var rs = new GKRuleSystem ();
    rs.AddRules (new [] {
        clearRule,
        fizzRule,
        buzzRule,
        outputRule
    });

    for (input = 1; input < 16; input++) {
        rs.Evaluate ();
        rs.Reset ();
    }
}

protected Func<GKRuleSystem, bool> mod(int m)
{
    Func<GKRuleSystem,bool> partiallyApplied = (rs) => input % m == 0;
    return partiallyApplied;
}
```

In base a un determinato set di regole (`GKRule`) e un set noto di input, il sistema esperto (`GKRuleSystem`) creerà output stimabile (`fizzbuzz` dell'esempio precedente).

### <a name="flocking"></a>Floccaggio

Floccaggio consente a che un gruppo di AI controllata gioco entità si comporti come un gruppo, in cui il gruppo di risposta per i movimenti e le azioni di un'entità responsabile come un gruppo di volatili in fase di trasferimento o di una scuola di fish datato.

Nel frammento di codice c# seguente implementa le comportamento utilizzando GameplayKit e SpriteKit per la visualizzazione grafica:

```csharp
using System;
using SpriteKit;
using CoreGraphics;
using UIKit;
using GameplayKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
using OpenTK;

namespace FieldBehaviorExplorer
{
    public static class FlockRandom
    {
        private static GKARC4RandomSource rand = new GKARC4RandomSource ();

        static FlockRandom ()
        {
            rand.DropValues (769);
        }

        public static float NextUniform ()
        {
            return rand.GetNextUniform ();
        }
    }

    public class FlockingScene : SKScene
    {
        List<Boid> boids = new List<Boid> ();
        GKComponentSystem componentSystem;
        GKAgent2D trackingAgent; //Tracks finger on screen
        double lastUpdateTime = Double.NaN;
        //Hold on to behavior so it doesn't get GC'ed
        static GKBehavior flockingBehavior;
        static GKGoal seekGoal;


        public FlockingScene (CGSize size) : base (size)
        {
            AddRandomBoids (20);

            var scale = 0.4f;
            //Flocking system
            componentSystem = new GKComponentSystem (typeof(GKAgent2D));
            var behavior = DefineFlockingBehavior (boids.Select (boid => boid.Agent).ToArray<GKAgent2D>(), scale);
            boids.ForEach (boid => {
                boid.Agent.Behavior = behavior;
                componentSystem.AddComponent(boid.Agent);
            });

            trackingAgent = new GKAgent2D ();
            trackingAgent.Position = new Vector2 ((float) size.Width / 2.0f, (float) size.Height / 2.0f);
            seekGoal = GKGoal.GetGoalToSeekAgent (trackingAgent);
        }

        public override void TouchesBegan (NSSet touches, UIEvent evt)
        {
            boids.ForEach(boid => boid.Agent.Behavior.SetWeight(1.0f, seekGoal));
        }

        public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            boids.ForEach (boid => boid.Agent.Behavior.SetWeight (0.0f, seekGoal));
        }

        public override void TouchesMoved (NSSet touches, UIEvent evt)
        {
            var touch = (UITouch) touches.First();
            var loc = touch.LocationInNode (this);
            trackingAgent.Position = new Vector2((float) loc.X, (float) loc.Y);
        }


        private void AddRandomBoids (int count)
        {
            var scale = 0.4f;
            for (var i = 0; i < count; i++) {
                var b = new Boid (UIColor.Red, this.Size, scale);
                boids.Add (b);
                this.AddChild (b);
            }
        }

        internal static GKBehavior DefineFlockingBehavior(GKAgent2D[] boidBrains, float scale)
        {
            if (flockingBehavior == null) {
                var flockingGoals = new GKGoal[3];
                flockingGoals [0] = GKGoal.GetGoalToSeparate (boidBrains, 100.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [1] = GKGoal.GetGoalToAlign (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);
                flockingGoals [2] = GKGoal.GetGoalToCohere (boidBrains, 40.0f * scale, (float)Math.PI * 8.0f);

                flockingBehavior = new GKBehavior ();
                flockingBehavior.SetWeight (25.0f, flockingGoals [0]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [1]);
                flockingBehavior.SetWeight (10.0f, flockingGoals [2]);
            }
            return flockingBehavior;
        }

        public override void Update (double currentTime)
        {
            base.Update (currentTime);
            if (Double.IsNaN(lastUpdateTime)) {
                lastUpdateTime = currentTime;
            }
            var delta = currentTime - lastUpdateTime;
            componentSystem.Update (delta);
        }
    }

    public class Boid : SKNode, IGKAgentDelegate
    {
        public GKAgent2D Agent { get { return brains; } }
        public SKShapeNode Sprite { get { return sprite; } }

        class BoidSprite : SKShapeNode
        {
            public BoidSprite (UIColor color, float scale)
            {
                var rot = CGAffineTransform.MakeRotation((float) (Math.PI / 2.0f));
                var path = new CGPath ();
                path.MoveToPoint (rot, new CGPoint (10.0, 0.0));
                path.AddLineToPoint (rot, new CGPoint (0.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 20.0));
                path.AddLineToPoint (rot, new CGPoint (20.0, 30.0));
                path.AddLineToPoint (rot, new CGPoint (10.0, 0.0));
                path.CloseSubpath ();

                this.SetScale (scale);
                this.Path = path;
                this.FillColor = color;
                this.StrokeColor = UIColor.White;

            }
        }

        private GKAgent2D brains;
        private BoidSprite sprite;
        private static int boidId = 0;

        public Boid (UIColor color, CGSize size, float scale)
        {
            brains = BoidBrains (size, scale);
            sprite = new BoidSprite (color, scale);
            sprite.Position = new CGPoint(brains.Position.X, brains.Position.Y);
            sprite.ZRotation = brains.Rotation;
            sprite.Name = boidId++.ToString ();

            brains.Delegate = this;

            this.AddChild (sprite);
        }

        private GKAgent2D BoidBrains(CGSize size, float scale)
        {
            var brains = new GKAgent2D ();
            var x = (float) (FlockRandom.NextUniform () * size.Width);
            var y = (float) (FlockRandom.NextUniform () * size.Height);
            brains.Position = new Vector2 (x, y);

            brains.Rotation = (float)(FlockRandom.NextUniform () * Math.PI * 2.0);
            brains.Radius = 30.0f * scale;
            brains.MaxSpeed = 0.5f;
            return brains;
        }

        [Export ("agentDidUpdate:")]
        public void AgentDidUpdate (GameplayKit.GKAgent agent)
        {
        }

        [Export ("agentWillUpdate:")]
        public void AgentWillUpdate (GameplayKit.GKAgent agent)
        {
            var brainsIn = (GKAgent2D) agent;
            sprite.Position = new CGPoint(brainsIn.Position.X, brainsIn.Position.Y);
            sprite.ZRotation = brainsIn.Rotation;
            Console.WriteLine ($"{sprite.Name} -> [{sprite.Position}], {sprite.ZRotation}");
        }
    }
}
```

Successivamente, implementare la scena in un controller di visualizzazione:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.
        this.View = new SKView {
        ShowsFPS = true,
        ShowsNodeCount = true,
        ShowsDrawCount = true
    };
}

public override void ViewWillLayoutSubviews ()
{
    base.ViewWillLayoutSubviews ();

    var v = (SKView)View;
    if (v.Scene == null) {
        var scene = new FlockingScene (View.Bounds.Size);
        scene.ScaleMode = SKSceneScaleMode.AspectFill;
        v.PresentScene (scene);
    }
}
```

Quando viene eseguita, l'animazione leggermente _"Boids"_ verrà esaminato intorno scelte il dito:

[![](images/flocking01.png "Il Boids leggermente animata verrà esaminato intorno le scelte dito")](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>Altri esempi di Apple

Oltre a esempi descritti in precedenza, Apple ha fornito le app di esempio seguenti che possono essere transcodificato in c# e xamarin. ios:

- [FourInARow: Utilizzo di Strategist GameplayKit Minmax per avversario AI](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog: Utilizza il sistema di agenti in GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots: Compilazione di un gioco multipiattaforma con SpriteKit e GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Metal

In iOS 9, Apple apportate più modifiche e aggiunte al Metal per fornire accesso a basso overhead della GPU. È possibile ottimizzare le immagini e il potenziale di elaborazione delle App iOS utilizzando Metal.

Il framework Metal include le nuove funzionalità seguenti:

- Nuovo private e profondità trame stencil per OS X.
- Shadow migliorata qualità con front separato e bloccaggio di profondità e i valori di stencil indietro.
- Miglioramenti di ombreggiatura lingua e la libreria Standard di metallo metalli.
- Calcolo shader supporta una vasta gamma di formati di pixel.

### <a name="the-metalkit-framework"></a>Il Framework MetalKit

Il framework MetalKit fornisce un set di classi di utilità e caratteristiche che consentono di ridurre la quantità di lavoro necessaria per utilizzare Metal in un'app iOS. MetalKit fornisce il supporto in tre aree principali:

1. Trama asincrona durante il caricamento da una varietà di origini, tra cui i formati comuni, ad esempio PNG, JPEG, KTX e PVR.
2. Facile accesso dei / o modello basato su attività per la gestione di metallo modello specifico. Queste funzionalità sono state ottimizzate elevata per consentire il trasferimento di dati efficiente tra reti dei / o del modello e i buffer Metal.
3. Visualizzazioni Metal predefinite e la gestione di visualizzazione che consente di ridurre notevolmente la quantità di codice necessario per la visualizzazione di rendering dei grafici all'interno di un'app iOS.

Per ulteriori informazioni sulla MetalKit, vedere Apple [MetalKit Framework riferimento](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356), [Guida per programmatori Metal](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221), [Metal Framework riferimento](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161) e [Metal Guida al linguaggio di ombreggiatura](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

### <a name="metal-performance-shaders-framework"></a>Prestazioni Metal shader Framework

Il framework di Shader prestazioni Metal fornisce un set altamente ottimizzato di grafica e calcolo basato su shader per l'utilizzo nel Metal basato su app iOS. Ogni shader nel framework è stato ottimizzato in modo specifico per fornire prestazioni elevate in metallo Metal prestazioni Shader supportati iOS GPU.

Utilizzando le classi Metal prestazioni Shader, è possibile ottenere le migliori prestazioni possibili su ogni GPU di iOS specifico senza la necessità di destinazione e gestire singoli codebase. Metal prestazioni shader è utilizzabile con qualsiasi risorsa, ad esempio le trame e buffer Metal.

Il framework di Shader prestazioni Metal offre un set di shader comuni, ad esempio:

- **Controllo sfocatura** (`MPSImageGaussianBlur`)
- **Rilevamento dei bordi Sobel** (`MPSImageSobel`)
- **Immagine di istogramma** (`MPSImageHistogram`)

Per ulteriori informazioni, vedere Apple [Metal Guida al linguaggio di ombreggiatura](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

## <a name="introducing-model-io"></a>Introduzione ai / o modello

Il framework dei / o modello di Apple fornisce una conoscenza approfondita degli asset 3D (ad esempio i modelli e le relative risorse). I/o modello fornisce giochi iOS con materiali basato su processore fisico, modelli e illuminazione che può essere utilizzato con GameplayKit, metallo e SceneKit.

Con i/o del modello, è possibile supportare i tipi di attività seguenti:

- Importare l'illuminazione, materiali, mesh dati, impostazioni videocamera e altre informazioni basate su scena da una varietà di formati di motore di giochi e software più diffusi.
- Elaborare o generare informazioni basate su scena ad esempio la creazione a livello di routine trama sky dome o ragione illuminazione in una rete.
- Funziona con MetalKit, SceneKit e GLKit per caricare in modo efficiente risorse del gioco nel buffer GPU per il rendering.
- Esportare informazioni basate su scena in un'ampia gamma di software più diffusi e formati di motore di giochi.

Per ulteriori informazioni sui / o del modello, vedere Apple [riferimento Framework dei / o del modello](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421)

## <a name="introducing-replaykit"></a>Introduzione a ReplayKit

Nuovo framework di ReplayKit Apple consente di aggiungere la registrazione del gioco per i giochi iOS facilmente e consentire all'utente in modo semplice e rapido modificare e condividere questo video all'interno dell'app.

Per ulteriori informazioni, vedere Apple [prevede Social con video ReplayKit e Game Center](https://developer.apple.com/videos/wwdc/2015/?id=605) e i relativi [DemoBots: creazione di un gioco di piattaforma incrociata con SpriteKit e GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) app di esempio.

## <a name="scenekit"></a>SceneKit

Scena Kit è un grafico di scena 3D API che semplifica l'utilizzo con la grafica 3D. È stata introdotta in OS X è 10.8 e che è ora giunto a iOS 8. Kit di scena creare accattivanti visualizzazioni 3D e casuali giochi 3D non richiede competenze in OpenGL. Compilazione sui concetti relativi al grafico di scena comuni, i Kit di scena estrae le complessità di OpenGL e OpenGL ES, semplificando notevolmente 3D aggiungere contenuto a un'applicazione. Tuttavia, se si è esperti OpenGL, scena Kit è ottimo supporto per l'associazione di direttamente con OpenGL anche. Inoltre, include numerose funzionalità che integrano la grafica 3D, ad esempio fisica e si integra molto bene con diversi altri framework di Apple, ad esempio Core animazione, immagine di base e Sprite Kit.

Per ulteriori informazioni, vedere il nostro [SceneKit](~/ios/platform/gaming/scenekit.md) documentazione.

### <a name="scenekit-changes"></a>Modifiche SceneKit

Apple ha aggiunto le seguenti nuove funzionalità per SceneKit per iOS 9:

- Xcode offre ora un Editor di scena che consente di compilare rapidamente giochi e App 3D interactive modificando le scene direttamente all'interno di Xcode.
- Il `SCNView` e `SCNSceneRenderer` classi possono essere utilizzate per attivare il rendering Metal (nei dispositivi iOS supportata).
- Il `SCNAudioPlayer` e `SCNNode` classi possono essere utilizzate per aggiungere effetti audio spaziali che consente di rilevare automaticamente una posizione del lettore in un'app iOS.

Per ulteriori informazioni, vedere il nostro [SceneKit documentazione](~/ios/platform/introduction-to-ios8.md#scenekit) e Apple [SceneKit Framework riferimento](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283) e [volpe: creazione di un gioco SceneKit con l'Editor di scena Xcode](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154)progetto di esempio.

## <a name="spritekit"></a>SpriteKit

Sprite Kit, il framework di gioco 2D di Apple, include alcune nuove funzionalità interessanti in iOS 8 e OS X Yosemite. Queste includono l'integrazione con scena Kit, supporto dello shader, illuminazione, ombreggiature, vincoli, la generazione di mappe normali e miglioramenti fisica. In particolare, le nuove funzionalità di fisica rendono molto semplice aggiungere effetti realistici per un gioco.

Per ulteriori informazioni, vedere il nostro [SpriteKit](~/ios/platform/gaming/spritekit.md) documentazione.

### <a name="spritekit-changes"></a>Modifiche SpriteKit

Apple ha aggiunto le seguenti nuove funzionalità per SpriteKit per iOS 9:

- Effetto audio spaziale che consente di rilevare automaticamente la posizione del lettore con la `SKAudioNode` classe.
- Xcode include ora un Editor di scena e l'Editor di azioni per la creazione di giochi e app 2D semplice.
- Scorrimento semplice gioco supporto con nuovi nodi fotocamera (`SKCameraNode`) gli oggetti.
- Nei dispositivi iOS che supportano Metal SpriteKit automaticamente utilizzerà per il rendering, anche se si utilizzano già shader OpenGL ES personalizzato.

Per ulteriori informazioni, vedere il nostro [SpriteKit documentazione](~/ios/platform/introduction-to-ios8.md#spritekit) Apple [SpriteKit Framework riferimento](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041) e i relativi [DemoBots: creazione di un gioco di piattaforma incrociata con SpriteKit e GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) app di esempio.

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati le nuove caratteristiche di gioco che iOS 9 fornite per le app xamarin. IOS.
È stato introdotto GameplayKit e i/o modello; i principali miglioramenti apportati a Metal; e le nuove funzionalità di SceneKit e SpriteKit.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 per gli sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
