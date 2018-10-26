---
title: API di gioco in xamarin. IOS iOS
description: Questo articolo illustra i nuovi miglioramenti di gioco forniti da iOS 9 che consente di migliorare la grafica e funzionalità audio del gioco di xamarin. IOS.
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: d8a531e495a19be7437d4a600e758028594248ab
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116004"
---
# <a name="ios-gaming-apis-in-xamarinios"></a>API di gioco in xamarin. IOS iOS

_Questo articolo illustra i nuovi miglioramenti di gioco forniti da iOS 9 che consente di migliorare la grafica e funzionalità audio del gioco di xamarin. IOS._

Apple ha apportato diversi miglioramenti tecnologici per i giochi le API in iOS 9 che rendono più semplice implementare grafiche di gioco e audio in un'app xamarin. IOS.
Questi includono sia facilità di sviluppo tramite Framework di alto livello e sfruttare la potenza di GPU del dispositivo iOS per la maggiore velocità e dalla capacità di grafica.

[![](images/flocking01.png "Un esempio di un'app in esecuzione uccelli")](images/flocking01.png#lightbox)

Insieme a funzionalità nuove e migliorate di bare Metal, SceneKit e SpriteKit inclusi GameplayKit, ReplayKit, i/o del modello, MetalKit e shader di Metal delle prestazioni.

Questo articolo descrive tutte le modalità per migliorare le tue competenze di xamarin. IOS con iOS 9 nuovi miglioramenti di gioco:

## <a name="introducing-gameplaykit"></a>Introduzione a GameplayKit

Framework GameplayKit nuovo Apple offre un set di tecnologie che rende più semplice creare giochi per dispositivi iOS, riducendo la quantità di codice ripetitivo e comuni necessari per l'implementazione. GameplayKit fornisce gli strumenti per sviluppare rapidamente i meccanismi di gioco che possono quindi essere combinati con facilità un motore grafico (ad esempio SceneKit o SpriteKit) per creare un gioco completato.

GameplayKit include diversi, comuni, gioco su algoritmi, ad esempio:

- Un comportamento basato, simulazione di agente che consente di definire gli spostamenti e gli obiettivi di esempio per l'intelligenza artificiale verrà automaticamente adottata.
- Intelligenza artificiale una minmax per basata su turni di gioco.
- Un sistema di regola per la logica del gioco basato sui dati con fuzzy ragionevole per ottenere un comportamento che potranno scaturire.

Inoltre, GameplayKit adotta un approccio di blocco predefinito per lo sviluppo di giochi utilizzando un'architettura modulare che fornisce le funzionalità seguenti:

- Macchina a stati per codice complesso, contenente le procedure di gestione basato su sistemi in gioco.
- Strumenti per fornire casuale di gioco e l'imprevedibilità senza causare problemi di debug.
- Un'entità riutilizzabile, costituito da componenti basati su architettura.

Per altre informazioni su GameplayKit, vedi di Apple [Guida per programmatori Gameplaykit](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172) e [riferimento a Framework GameplayKit](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199).

## <a name="gameplaykit-examples"></a>Esempi di GameplayKit

Diamo un'occhiata implementa alcuni meccanismi di gioco semplice in un'app xamarin. IOS con il kit di gioco.

### <a name="pathfinding"></a>Pathfinding

Pathfinding è la possibilità per un elemento di intelligenza artificiale di un gioco per trovare un modo per la tavola da gioco.
Ad esempio, un libro enemy 2D ricerca procede un labirinto o un carattere 3D tramite un terreno world first-persona-tiro a segno.

Prendere in considerazione la mappa seguente:

[![](images/gkpathfindpath.png "Una mappa pathfinding di esempio")](images/gkpathfindpath.png#lightbox)

Usando questo pathfinding C# codice è possibile trovare un modo tramite la mappa:

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

### <a name="classical-expert-system"></a>Sistema esperto classiche

Il frammento seguente di C# codice viene illustrato come GameplayKit utilizzabile per implementare un sistema esperto classico:

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

In un determinato set di regole di base (`GKRule`) e un set noto degli input, il sistema esperto (`GKRuleSystem`) verrà creato l'output stimabile (`fizzbuzz` ai fini dell'esempio precedente).

### <a name="flocking"></a>Uccelli

Uccelli consente a che un gruppo di intelligenza artificiale controllato le entità del gioco a comportarsi come un gruppo, in cui il gruppo risponde ai movimenti e azioni di un'entità responsabile come gruppi di in-flight volatili o una scuola di pesci piscina.

Il frammento seguente di C# codice implementa le comportamento utilizzando GameplayKit e SpriteKit per visualizzare gli elementi grafici:

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

Successivamente, implementare questa scena in un controller di visualizzazione:

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

Quando viene eseguito, il leggermente animata _"Boids"_ verrà esaminato tutto nostro TAP dito:

[![](images/flocking01.png "Il Boids leggermente animata verrà branco intorno le scelte del dito")](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>Altri esempi di Apple

Oltre a esempi presentati in precedenza, Apple ha fornito le app di esempio seguenti che possono essere transcodificati a C# e xamarin. ios:

- [FourInARow: Uso di Strategist GameplayKit Minmax per intelligenza artificiale avversario](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog: Utilizza il sistema di agenti in GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [DemoBots: Creare un gioco cross-Platform con SpriteKit e GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Metal

In iOS 9 Apple ha apportato diverse modifiche e aggiunte a bare Metal per fornire l'accesso con sovraccarico ridotto per la GPU. Usa Metal è possibile ottimizzare le immagini e il potenziale di elaborazione delle App iOS.

Il framework bare Metal include le nuove funzionalità seguenti:

- Nuovo privato e la profondità trame stencil per OS X.
- Qualità shadow migliorata con profondità clamping e separato e i valori di stencil back.
- Miglioramenti di ombreggiatura linguaggio e libreria Standard di Metal bare metalli.
- Calcolo degli shader supportano una vasta gamma di formati pixel.

### <a name="the-metalkit-framework"></a>Il Framework MetalKit

Il framework MetalKit fornisce un set di classi di utilità e caratteristiche che consentono di ridurre la quantità di lavoro è obbligatorio utilizzare bare Metal in un'app per iOS. MetalKit offre supporto in tre aree principali:

1. Il caricamento da un'ampia gamma di origini tra cui formati comuni, ad esempio PNG, JPEG, KTX e PVR ha trama asincrona.
2. Facile accesso dei / o del modello basato su risorse per la gestione di modello specifico bare Metal. Queste funzionalità sono stati altamente ottimizzate per consentire il trasferimento efficiente dei dati tra le reti mesh dei / o del modello e i buffer bare Metal.
3. Visualizzazioni Metal predefinite e la gestione di visualizzazione che riducono significativamente la quantità di codice necessario per visualizzare grafico rendering all'interno di un'app per iOS.

Per altre informazioni su MetalKit, vedi di Apple [riferimento a Framework MetalKit](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356), [Metal Programming Guide](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221), [riferimenti Framework Metal](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161) e [bare Metal Guida al linguaggio di ombreggiatura](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

### <a name="metal-performance-shaders-framework"></a>Framework di shader di Metal prestazioni

Il framework di Shader di Metal prestazioni offre un set altamente ottimizzato di grafica e calcolo basato su app di iOS basate su shader per l'uso in di Metal. Ogni shader nello Shader prestazioni Metal framework è stato ottimizzato in modo specifico per offrire prestazioni elevate su Metal supportati iOS GPU.

Utilizzando le classi di Shader di Metal delle prestazioni, è possibile ottenere le migliori prestazioni possibili su ogni GPU di iOS specifico senza dover di destinazione e gestire le basi di codice singoli. Gli shader di Metal prestazioni è utilizzabile con qualsiasi risorsa bare Metal, ad esempio trame e i buffer.

Il framework di Shader di Metal prestazioni offre un set di shader comuni, ad esempio:

- **Controllo sfocatura** (`MPSImageGaussianBlur`)
- **Rilevamento bordi Sobel** (`MPSImageSobel`)
- **Immagine di istogramma** (`MPSImageHistogram`)

Per altre informazioni, vedere di Apple [Guida al linguaggio di ombreggiatura Metal](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

## <a name="introducing-model-io"></a>Introduzione ai / o modello

Framework dei / o modello Apple offre una conoscenza approfondita di asset 3D (ad esempio i modelli e le relative risorse). I/o del modello fornisce i tuoi giochi iOS con materiali fisici basati, modelli e illuminazione che può essere utilizzato con GameplayKit, bare Metal e SceneKit.

Con i/o del modello, è possibile supportare i tipi di attività seguenti:

- Importazione di illuminazione, materiali, della rete di dati, le impostazioni della fotocamera e altre informazioni basate sulla scena da un'ampia gamma di software più diffusi e formati del motore di gioco.
- Elaborare o generare informazioni basate su scena, ad esempio creare modo procedurale trama dome sky o bake illuminazione in una mesh.
- È compatibile con MetalKit, SceneKit e GLKit per caricare in modo efficiente risorse del gioco nel buffer GPU per il rendering.
- Esportare informazioni basate sulla scena a una varietà di formati di motore di gioco e software più diffusi.

Per altre informazioni sui / o del modello, vedere Apple [riferimento a Framework dei / o modello](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421)

## <a name="introducing-replaykit"></a>Introduzione a ReplayKit

Framework ReplayKit nuovo Apple consente di aggiungere una registrazione di gioco per il tuo gioco iOS con facilità e consentire all'utente rapidamente e facilmente modificare e condividere il video all'interno dell'app.

Per altre informazioni, vedere di Apple [sarà basati su social network con video ReplayKit e Game Center](https://developer.apple.com/videos/wwdc/2015/?id=605) e i relativi [DemoBots: creare un gioco con SpriteKit e GameplayKit piattaforma Cross](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) app di esempio.

## <a name="scenekit"></a>SceneKit

Scena Kit è un grafico della scena 3D API che semplifica l'utilizzo di grafica 3D. È stata introdotta in OS X 10.8 e ha ora iniziato a iOS 8. Con Scenekit creazione coinvolgenti visualizzazioni 3D e casual games 3D non richiede competenze in OpenGL. Sulla base di concetti comuni di graph scena, Scenekit consente di evitare le complessità correlate OpenGL e OpenGL ES, rendendo molto più semplice aggiungere 3D contenuto a un'applicazione. Tuttavia, se sei un esperto di OpenGL, Scenekit ha un notevole supporto per l'associazione di direttamente con OpenGL anche. Inoltre include numerose funzionalità che integrano quelli grafica 3D, ad esempio fisica e si integra molto bene con diversi altri framework di Apple, ad esempio Core Animation, Core immagine e Spritekit.

Per altre informazioni, vedere la [SceneKit](~/ios/platform/gaming/scenekit.md) documentazione.

### <a name="scenekit-changes"></a>Modifiche di SceneKit

Apple ha aggiunto le seguenti nuove funzionalità per SceneKit per iOS 9:

- Xcode offre ora un Editor di scena che consente di compilare rapidamente giochi e App 3D interattiva modificando scene direttamente all'interno di Xcode.
- Il `SCNView` e `SCNSceneRenderer` classi possono essere utilizzate per attivare il rendering bare Metal (nei dispositivi iOS supportati).
- Il `SCNAudioPlayer` e `SCNNode` classi possono essere utilizzate per aggiungere effetti audio spaziali che tiene traccia automaticamente di una posizione dell'assegnatario a un'app iOS.

Per altre informazioni, vedere nostro [SceneKit documentazione](~/ios/platform/introduction-to-ios8.md#scenekit) e di Apple [riferimento a Framework SceneKit](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283) e [Fox: creare un gioco SceneKit con l'Editor di scena Xcode](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154)progetto di esempio.

## <a name="spritekit"></a>SpriteKit

Spritekit, il framework di gioco 2D da Apple, dispone di alcune nuove interessanti funzionalità in iOS 8 e OS X Yosemite. Queste includono l'integrazione con Scenekit, supporto dello shader, illuminazione, ombreggiature, vincoli, generazione di mappe normali e miglioramenti fisica. In particolare, le nuove funzionalità di effetti fisici rendono molto semplice aggiungere effetti realistici di un gioco.

Per altre informazioni, vedere la [SpriteKit](~/ios/platform/gaming/spritekit.md) documentazione.

### <a name="spritekit-changes"></a>Modifiche SpriteKit

Apple ha aggiunto le seguenti nuove funzionalità per SpriteKit per iOS 9:

- Tenere automaticamente traccia della posizione del lettore con spaziale audio effetto il `SKAudioNode` classe.
- Xcode include ora un Editor di scena e l'Editor di azioni per la creazione di app e giochi 2D semplice.
- Lo scorrimento semplice gioco supporto con i nuovi nodi fotocamera (`SKCameraNode`) oggetti.
- Nei dispositivi iOS che supportano bare Metal, SpriteKit userà automaticamente, per il rendering, anche se si usa già shader OpenGL ES personalizzati.

Per altre informazioni, vedere la [documentazione SpriteKit](~/ios/platform/introduction-to-ios8.md#spritekit) Apple [riferimento a Framework SpriteKit](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041) e i relativi [DemoBots: creare un gioco con SpriteKit piattaforma incrociata e GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) app di esempio.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la nuova funzionalità di gioco che iOS 9 fornisce per le app xamarin. IOS.
Ha introdotto GameplayKit e i/o modello; i principali miglioramenti apportati a bare Metal; e le nuove funzionalità di SceneKit e SpriteKit.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [per gli sviluppatori iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
