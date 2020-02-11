---
title: API per giochi iOS in Xamarin.iOS
description: Questo articolo illustra i nuovi miglioramenti dei giochi offerti da iOS 9 che possono essere usati per migliorare le funzionalità grafiche e audio del gioco Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 958D38FD-9240-482E-9A42-D6671ED8F2B0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: fd79153520036ae49fd6fd9abe652e2c7459b30d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032510"
---
# <a name="ios-gaming-apis-in-xamarinios"></a>API per giochi iOS in Xamarin.iOS

_Questo articolo illustra i nuovi miglioramenti dei giochi offerti da iOS 9 che possono essere usati per migliorare le funzionalità grafiche e audio del gioco Xamarin.iOS._

Apple ha apportato diversi miglioramenti tecnologici alle API di gioco in iOS 9 che semplificano l'implementazione di grafica e audio di giochi in un'app Xamarin.iOS.
Sono inclusi sia la facilità di sviluppo che i Framework di alto livello, sfruttando la potenza della GPU del dispositivo iOS per migliorare la velocità e le funzionalità grafiche.

[![](images/flocking01.png "An example of an app running flocking")](images/flocking01.png#lightbox)

Sono inclusi GameplayKit, ReplayKit, Model I/O, MetalKit e Metal Performance shader insieme alle nuove funzionalità migliorate di Metal, SceneKit e SpriteKit.

In questo articolo vengono presentati tutti i modi per migliorare il gioco Xamarin.iOS con i nuovi miglioramenti dei giochi di iOS 9:

## <a name="introducing-gameplaykit"></a>Introduzione a GameplayKit

Il nuovo Framework GameplayKit di Apple fornisce un set di tecnologie che semplificano la creazione di giochi per i dispositivi iOS riducendo la quantità di codice frequente e ripetitivo necessario per l'implementazione. GameplayKit offre strumenti per lo sviluppo di meccanismi di gioco che possono essere facilmente combinati con un motore grafico, ad esempio SceneKit o SpriteKit, per offrire rapidamente un gioco completo.

GameplayKit include diversi algoritmi di riproduzione del gioco comuni, ad esempio:

- Una simulazione basata sul comportamento, che consente di definire spostamenti e obiettivi che verranno automaticamente proseguiti dall'intelligenza artificiale.
- Intelligenza artificiale MinMax per la riproduzione basata su turni.
- Sistema di regole per la logica di gioco basata sui dati con ragionamento fuzzy per fornire un comportamento emergente.

Inoltre, GameplayKit adotta un approccio di base per lo sviluppo di giochi usando un'architettura modulare che fornisce le funzionalità seguenti:

- Macchina a stati per la gestione di sistemi complessi basati su codice procedurale in gioco.
- Strumenti per fornire giochi casuali e imprevedibilità senza causare problemi di debug.
- Un'architettura basata su entità riutilizzabile e con componenti.

Per altre informazioni su GameplayKit, vedere la guida alla [programmazione](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/GameplayKit_Guide/index.html#//apple_ref/doc/uid/TP40015172) di Apple GameplayKit e [riferimento a GameplayKit Framework](https://developer.apple.com/library/prerelease/ios/documentation/GameplayKit/Reference/GameplayKit_Framework/index.html#//apple_ref/doc/uid/TP40015199).

## <a name="gameplaykit-examples"></a>Esempi di GameplayKit

Si esaminerà ora l'implementazione di alcuni semplici meccanismi di riproduzione dei giochi in un'app Xamarin.iOS con Game Play Kit.

### <a name="pathfinding"></a>Pathfinding

Pathfinding è la possibilità per un elemento AI del gioco di trovare la propria strada intorno alla lavagna.
Un nemico 2D, ad esempio, è in grado di trovare la strada attraverso un labirinto o un carattere 3D attraverso un terreno del mondo sparatutto di prima persona.

Si consideri la mappa seguente:

[![](images/gkpathfindpath.png "An example pathfinding map")](images/gkpathfindpath.png#lightbox)

Utilizzando pathfinding, C# questo codice può trovare una soluzione tramite la mappa:

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

### <a name="classical-expert-system"></a>Sistema di esperti classici

Il frammento di C# codice seguente illustra come usare GameplayKit per implementare un sistema esperto classico:

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

In base a un determinato set di regole (`GKRule`) e a un set di input noto, il sistema esperto (`GKRuleSystem`) creerà un output prevedibile (`fizzbuzz` per l'esempio precedente).

### <a name="flocking"></a>Floccaggio

La moltitudine consente a un gruppo di entità di gioco controllate da un gruppo di persone di comportarsi come un gregge, in cui il gruppo risponde ai movimenti e alle azioni di un'entità lead come un gregge di uccelli in volo o una scuola di nuoto.

Il frammento di C# codice seguente implementa il comportamento di floccaggio usando GameplayKit e SpriteKit per la visualizzazione grafica:

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

Implementare quindi questa scena in un controller di visualizzazione:

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

Quando viene eseguito, il piccolo _"boids"_ animato ricorre intorno ai tocchi del dito:

[![](images/flocking01.png "The little animated Boids will flock around the finger taps")](images/flocking01.png#lightbox)

### <a name="other-apple-examples"></a>Altri esempi di Apple

Oltre agli esempi presentati in precedenza, Apple ha fornito le app di esempio seguenti che possono essere transcodificate in C# e Xamarin.iOS:

- [FourInARow: uso di GameplayKit MinMax Strategy per l'intelligenza artificiale](https://developer.apple.com/library/prerelease/ios/samplecode/FourInARow/Introduction/Intro.html#//apple_ref/doc/uid/TP40016142)
- [AgentsCatalog: uso del sistema agenti in GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/AgentsCatalog/Introduction/Intro.html#//apple_ref/doc/uid/TP40016141)
- [Demobos: creazione di un gioco multipiattaforma con SpriteKit e GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179)

## <a name="metal"></a>Metal

In iOS 9, Apple ha apportato diverse modifiche e aggiunte a metal per fornire un accesso con sovraccarico ridotto alla GPU. Usando Metal puoi ottimizzare la grafica e il potenziale di calcolo delle tue app iOS.

Il Framework Metal include le nuove funzionalità seguenti:

- Nuove trame private e depth stencil per OS X.
- Miglioramento della qualità dell'ombreggiatura con blocco di profondità e valori separati dello stencil anteriore e posteriore.
- Miglioramenti del linguaggio di Metal shading e della libreria standard metal.
- Gli shader computazionali supportano una gamma più ampia di formati pixel.

### <a name="the-metalkit-framework"></a>Framework MetalKit

Il Framework MetalKit fornisce un set di classi e funzionalità di utilità che consentono di ridurre la quantità di lavoro necessaria per l'uso di metal in un'app iOS. MetalKit fornisce il supporto in tre aree principali:

1. Caricamento di trama asincrono da diverse origini, inclusi formati comuni come PNG, JPEG, KTX e PVR.
2. Accesso semplificato alle risorse basate su I/O del modello per la gestione dei modelli specifici del metallo. Queste funzionalità sono state altamente ottimizzate per offrire un trasferimento dei dati efficiente tra le reti di I/O del modello e I buffer di metallo.
3. Visualizzazioni Metal predefinite e gestione delle visualizzazioni che consentono di ridurre notevolmente la quantità di codice necessaria per visualizzare i rendering grafici in un'app iOS.

Per altre informazioni su MetalKit, vedere la Guida di [riferimento a MetalKit Framework](https://developer.apple.com/library/prerelease/ios/documentation/MetalKit/Reference/MTKFrameworkReference/index.html#//apple_ref/doc/uid/TP40015356)di Apple, guida per la [programmazione](https://developer.apple.com/library/prerelease/ios/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)di Metal, Guida di riferimento per il [Framework](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalFrameworkReference/index.html#//apple_ref/doc/uid/TP40014161) di metal e [Language shader](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

### <a name="metal-performance-shaders-framework"></a>Framework shader performance metal

Il Framework shader per le prestazioni di Metal fornisce un set altamente ottimizzato di elementi grafici e shader basati sul calcolo da usare nelle app iOS basate su Metal. Ogni shader in Metal Performance shader Framework è stato ottimizzato in modo specifico per offrire prestazioni elevate sulle GPU iOS supportate da Metal.

Grazie alle classi di shader performance metal, è possibile ottenere le massime prestazioni possibili in ogni GPU iOS specifica senza dover specificare come destinazione e gestire singole basi di codice. Gli shader performance metal possono essere usati con qualsiasi risorsa metal, ad esempio trame e buffer.

Il Framework shader per le prestazioni di Metal fornisce un set di shader comuni, ad esempio:

- **Sfocatura gaussiana** (`MPSImageGaussianBlur`)
- **Rilevamento bordo Sobel** (`MPSImageSobel`)
- **Istogramma immagini** (`MPSImageHistogram`)

Per ulteriori informazioni, vedere la guida alla [lingua di Apple Metal shading](https://developer.apple.com/library/prerelease/ios/documentation/Metal/Reference/MetalShadingLanguageGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014364).

## <a name="introducing-model-io"></a>Introduzione all'I/O del modello

Il Framework di I/O del modello Apple fornisce una conoscenza approfondita delle risorse 3D, ad esempio i modelli e le risorse correlate. L'I/O del modello fornisce ai giochi iOS i materiali, i modelli e l'illuminazione basati su fisico che possono essere usati con GameplayKit, metal e SceneKit.

Con l'I/O del modello è possibile supportare i tipi di attività seguenti:

- Importa l'illuminazione, i materiali, i dati mesh, le impostazioni della fotocamera e altre informazioni basate sulla scena da un'ampia gamma di formati comuni di software e giochi.
- Elaborare o generare informazioni basate sulla scena, ad esempio creare cupole cielo con trama procedurale o cuocere in una rete.
- Funziona con MetalKit, SceneKit e GLKit per caricare in modo efficiente gli asset del gioco in buffer GPU per il rendering.
- Esporta informazioni basate sulla scena in un'ampia gamma di formati comuni di software e giochi.

Per ulteriori informazioni sull'I/O del modello, vedere le informazioni di [riferimento sul Framework di i/o del modello](https://developer.apple.com/library/prerelease/ios/documentation/ModelIO/Reference/ModelIO_Framework/index.html#//apple_ref/doc/uid/TP40015421) Apple

## <a name="introducing-replaykit"></a>Introduzione a ReplayKit

Il nuovo Framework ReplayKit di Apple ti permette di aggiungere facilmente la registrazione della riproduzione del gioco al gioco iOS e di consentire all'utente di modificare e condividere in modo rapido e semplice questo video dall'interno dell'app.

Per altre informazioni, vedere la pagina relativa ai [social network con ReplayKit e Game Center video](https://developer.apple.com/videos/wwdc/2015/?id=605) e i rispettivi [demobos di Apple: creazione di un gioco multipiattaforma con l'app di esempio SpriteKit e GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) .

## <a name="scenekit"></a>SceneKit

Scene kit è un'API Graph della scena 3D che semplifica l'uso della grafica 3D. È stata introdotta per la prima volta in OS X 10,8 ed è ora disponibile in iOS 8. Con scene kit creazione di visualizzazioni 3D immersive e giochi 3D casuali non richiede esperienza in OpenGL. Basandosi sui concetti comuni relativi a Graph scene, il kit della scena astrae le complessità di OpenGL e OpenGL, semplificando l'aggiunta di contenuto 3D a un'applicazione. Tuttavia, se si è un esperto di OpenGL, il kit di scene offre un ottimo supporto per la legatura diretta con OpenGL. Include inoltre numerose funzionalità che integrano grafica 3D, ad esempio la fisica, e si integra molto bene con diversi altri framework Apple, ad esempio Core Animation, Core Image e sprite Kit.

Per ulteriori informazioni, consultare la documentazione di [SceneKit](~/ios/platform/gaming/scenekit.md) .

### <a name="scenekit-changes"></a>SceneKit modifiche

Apple ha aggiunto le seguenti nuove funzionalità a SceneKit per iOS 9:

- Xcode offre ora un editor di scena che consente di creare rapidamente giochi e app 3D interattive modificando le scene direttamente dall'interno di Xcode.
- È possibile usare le classi `SCNView` e `SCNSceneRenderer` per abilitare il rendering Metal (nei dispositivi iOS supportati).
- È possibile usare le classi `SCNAudioPlayer` e `SCNNode` per aggiungere effetti audio spaziali che consentono di tenere traccia automaticamente di una posizione del lettore in un'app iOS.

Per altre informazioni, vedere la documentazione di SceneKit e le informazioni di [riferimento sul Framework SceneKit](https://developer.apple.com/library/prerelease/ios/documentation/SceneKit/Reference/SceneKit_Framework/index.html#//apple_ref/doc/uid/TP40012283) di Apple e la pagina [relativa](~/ios/platform/introduction-to-ios8.md#scenekit) [alla creazione di un gioco SceneKit con il](https://developer.apple.com/library/prerelease/ios/samplecode/Fox/Introduction/Intro.html#//apple_ref/doc/uid/TP40016154) progetto di esempio per l'editor di scena di Xcode.

## <a name="spritekit"></a>SpriteKit

Sprite Kit, il Framework di gioco 2D di Apple, presenta alcune nuove interessanti funzionalità in iOS 8 e OS X Yosemite. Sono inclusi l'integrazione con la scenografia, il supporto dello shader, l'illuminazione, le ombre, i vincoli, la generazione di mappe normali e i miglioramenti della fisica. In particolare, le nuove funzionalità di fisica facilitano l'aggiunta di effetti realistici a un gioco.

Per ulteriori informazioni, consultare la documentazione di [SpriteKit](~/ios/platform/gaming/spritekit.md) .

### <a name="spritekit-changes"></a>SpriteKit modifiche

Apple ha aggiunto le seguenti nuove funzionalità a SpriteKit per iOS 9:

- Effetto audio spaziale che tiene traccia automaticamente della posizione del lettore con la classe `SKAudioNode`.
- Xcode ora include un editor di scena e un editor di azioni per semplificare la creazione di giochi e app 2D.
- Supporto dei giochi con scorrimento semplice con nuovi oggetti dei nodi della fotocamera (`SKCameraNode`).
- Nei dispositivi iOS che supportano metal, SpriteKit lo userà automaticamente per il rendering, anche se si usano già shader OpenGL ES personalizzati.

Per ulteriori informazioni, vedere la [documentazione di SpriteKit documentazione](~/ios/platform/introduction-to-ios8.md#spritekit) di Apple [SpriteKit Framework Reference](https://developer.apple.com/library/prerelease/ios/documentation/SpriteKit/Reference/SpriteKitFramework_Ref/index.html#//apple_ref/doc/uid/TP40013041) e i relativi [demobos: creazione di un gioco multipiattaforma con l'app di esempio SpriteKit e GameplayKit](https://developer.apple.com/library/prerelease/ios/samplecode/DemoBots/Introduction/Intro.html#//apple_ref/doc/uid/TP40015179) .

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato le nuove funzionalità di gioco fornite da iOS 9 per le app Xamarin.iOS.
Ha introdotto GameplayKit e I/O del modello; i principali miglioramenti apportati al metal; e le nuove funzionalità di SceneKit e SpriteKit.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 per sviluppatori](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
