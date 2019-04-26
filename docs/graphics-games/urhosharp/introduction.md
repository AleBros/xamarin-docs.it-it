---
title: Un'introduzione di UrhoSharp
description: Questo documento descrive la struttura di base di un'applicazione di UrhoSharp e collegamenti alle varie guide e applicazioni di esempio viene illustrato l'uso di UrhoSharp.
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: a3e14ebca961e828fc578035adaca5ba2a809438
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61288520"
---
# <a name="an-introduction-to-urhosharp"></a>Un'introduzione di UrhoSharp

![Logo di UrhoSharp](introduction-images/urhosharp-icon.png)

UrhoSharp è un potente motore di gioco 3D per gli sviluppatori di Xamarin e .NET.  È simile come spirito al SceneKit e SpriteKit Apple e includere la fisica, navigazione, rete e molto più mentre ancora in fase di cross-platform.

È un'associazione di .NET per le [Urho3D](http://urho3d.github.io/) del motore e consente agli sviluppatori di scrivere codice multipiattaforma destinate ad Android, iOS, Windows e Mac con lo stesso codebase e può eseguire il rendering OpenGL sia Direct3D sistemi.

UrhoSharp è un motore di gioco con numerose funzionalità predefinite:

- Potente di rendering della grafica 3D
- [Simulazione di fisica](https://developer.xamarin.com/api/namespace/Urho.Physics/) (usando la libreria di punto elenco)
- [Gestione di scena](https://developer.xamarin.com/api/type/Urho.Scene/)
- Supporto di Await/Async
- [Azioni descrittive API](https://developer.xamarin.com/api/namespace/Urho.Actions/)
- [Integrazione 2D in scene 3D](https://developer.xamarin.com/api/namespace/Urho.Urho2D/)
- [Per il rendering del tipo di carattere con FreeType](https://developer.xamarin.com/api/type/Urho.Gui.FontFaceFreeType/)
- [Client e server, funzionalità di rete](https://developer.xamarin.com/api/namespace/Urho.Network/)
- [Importare un'ampia gamma di asset](https://developer.xamarin.com/api/namespace/Urho.Resources/) (con la libreria di asset aperta)
- [Rete mesh di navigazione e pathfinding](https://developer.xamarin.com/api/namespace/Urho.Navigation/) (tramite rifondere in un nuovo/deviazione)
- [Generazione della struttura convessa per il rilevamento delle collisioni](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/) (tramite StanHull)
- [Riproduzione audio](https://developer.xamarin.com/api/namespace/Urho.Audio/) (con **libvorbis**)

## <a name="getting-started"></a>Introduzione

UrhoSharp per praticità viene distribuito come un [mobileengagement](https://www.nuget.org/) e può essere aggiunto al C# o F# progetti destinati a Windows, Mac, Android o iOS.  NuGet include sia le librerie necessarie per eseguire il programma, nonché gli asset di base (CoreData) utilizzati dal motore.

### <a name="urho-as-a-portable-class-library"></a>Urho come libreria di classi portabile

Il pacchetto Urho può essere utilizzato da un progetto specifico della piattaforma o da un progetto libreria di classi portabile, consentendo di riutilizzare tutto il codice tra tutte le piattaforme.  Ciò significa che è necessario eseguire in ogni piattaforma è sufficiente scrivere il punto di ingresso specifico della piattaforma e quindi trasferire il controllo al codice condiviso del gioco.

### <a name="samples"></a>Esempi

È possibile ottenere un'idea delle funzionalità dei Urho aprendo in Visual Studio per Mac o Visual Studio della soluzione di esempio in:

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

La soluzione predefinita contiene i progetti per Android, iOS, Windows e Mac.  È stata strutturata tale soluzione in modo che abbiamo un'utilità di avvio specifiche della piattaforma di piccole dimensioni e tutti i codice di esempio e il codice del gioco si trova in una libreria di classi portabile, che illustra come ottimizzare il riutilizzo del codice tra tutte le piattaforme.

Consultare il [Urho e piattaforma Your](~/graphics-games/urhosharp/platform/index.md) pagina per altre informazioni su come creare le proprie soluzioni.

Poiché tutti gli esempi condividono un set comune di elementi dell'interfaccia utente, gli esempi di eseguire l'astrazione la configurazione di base in questo file:

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

Ciò fornisce una classe di base di esempio che gestisce alcune sequenze di tasti base e una fotocamera eventi, configurazioni di tocco, fornisce gli elementi dell'interfaccia utente di base e ciò consente a ogni esempio di concentrarsi sulle funzionalità specifiche che viene viene presentato.

L'esempio seguente viene illustrato ciò che il motore è in grado di procedere:

- [Giochi samply](https://github.com/xamarin/urho-samples/tree/master/SamplyGame) un clone di ShootySkies semplice.

Mentre gli altri esempi mostrano le singole proprietà di ogni esempio.

## <a name="basic-structure"></a>Struttura di base

Il tuo gioco deve essere una sottoclasse di [`Application`](https://developer.xamarin.com/api/type/Urho.Application/)
(classe), si tratta in cui si installerà il tuo gioco (sul [ `Setup` ](https://developer.xamarin.com/api/member/Urho.Application.Setup/) metodo) e avvia il tuo gioco (nel [ `Start` ](https://developer.xamarin.com/api/member/Urho.Application.Start) (metodo)).  Quindi è costruire l'interfaccia utente principale.  Verrà illustrato un esempio di piccole dimensioni che mostra le API per impostare una scena 3D, alcuni elementi dell'interfaccia utente e la connessione di un comportamento semplice all'applicazione.

```csharp
class MySample : Application {
    protected override void Start ()
    {
        CreateScene ();
        Input.KeyDown += (args) => {
            if (args.Key == Key.Esc) Exit ();
        };
    }

    async void CreateScene()
    {
        // UI text
        var helloText = new Text()
        {
            Value = "Hello World from MySample",
            HorizontalAlignment = HorizontalAlignment.Center,
            VerticalAlignment = VerticalAlignment.Center
        };
        helloText.SetColor(new Color(0f, 1f, 1f));
        helloText.SetFont(
            font: ResourceCache.GetFont("Fonts/Font.ttf"),
            size: 30);
        UI.Root.AddChild(helloText);

        // Create a top-level scene, must add the Octree
        // to visualize any 3D content.
        var scene = new Scene();
        scene.CreateComponent<Octree>();
        // Box
        Node boxNode = scene.CreateChild();
        boxNode.Position = new Vector3(0, 0, 5);
        boxNode.Rotation = new Quaternion(60, 0, 30);
        boxNode.SetScale(0f);
        StaticModel modelObject = boxNode.CreateComponent<StaticModel>();
        modelObject.Model = ResourceCache.GetModel("Models/Box.mdl");
        // Light
        Node lightNode = scene.CreateChild(name: "light");
        lightNode.SetDirection(new Vector3(0.6f, -1.0f, 0.8f));
        lightNode.CreateComponent<Light>();
        // Camera
        Node cameraNode = scene.CreateChild(name: "camera");
        Camera camera = cameraNode.CreateComponent<Camera>();
        // Viewport
        Renderer.SetViewport(0, new Viewport(scene, camera, null));
        // Perform some actions
        await boxNode.RunActionsAsync(
            new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
        await boxNode.RunActionsAsync(
            new RepeatForever(new RotateBy(duration: 1,
                deltaAngleX: 90, deltaAngleY: 0, deltaAngleZ: 0)));
     }
}
```

Se si esegue questa applicazione, si scoprirà presto che la fase di esecuzione del reclamo gli asset non sono presenti.  Che cosa è necessario eseguire è creare una gerarchia del progetto che inizia con il nome della directory speciali "Dati" e all'interno di questa operazione, è possibile inserire l'asset di cui viene fatto riferimento nel programma.  È quindi necessario impostare nelle proprietà dell'elemento per ogni asset "Copia di Directory di Output" per "Copia se più recente", che garantisce che i dati siano presenti.

Verrà ora illustrato che cosa succede.

Per avviare l'applicazione si chiama la funzione di inizializzazione del motore, seguita dalla creazione di una nuova istanza della classe dell'applicazione, simile al seguente:

```csharp
new MySample().Run();
```

Il runtime richiama il `Setup` e `Start` metodi per l'utente.  Se esegue l'override `Setup` è possibile configurare i parametri del motore (non illustrato in questo esempio).

È necessario eseguire l'override `Start` come verrà avviato il tuo gioco.  In questo metodo si caricherà gli asset, connettere i gestori eventi, la scena di installazione e avviare tutte le azioni che si desiderano.  Nel nostro esempio, entrambi creiamo un po' di interfaccia utente per visualizzare l'utente, nonché impostazione di una scena 3D.

Il seguente frammento di codice Usa il framework dell'interfaccia utente per creare un elemento di testo e aggiungerlo alla propria applicazione:

```csharp
// UI text
var helloText = new Text()
{
    Value = "Hello World from UrhoSharp",
    HorizontalAlignment = HorizontalAlignment.Center,
    VerticalAlignment = VerticalAlignment.Center
};
helloText.SetColor(new Color(0f, 1f, 1f));
helloText.SetFont(
    font: ResourceCache.GetFont("Fonts/Font.ttf"),
    size: 30);
UI.Root.AddChild(helloText);
```

Il framework dell'interfaccia utente è presente per fornire un'interfaccia utente all'interno del gioco molto semplice e funziona mediante l'aggiunta di nuovi nodi per il [ `UI.Root` ](https://developer.xamarin.com/api/property/Urho.Gui.UI.Root/) nodo.

La seconda parte del nostro configurazioni di esempio della scena principale.  Ciò comporta una serie di passaggi, la creazione di una scena 3D, la creazione di una casella 3D nella schermata di aggiunta di una luce, una videocamera e un riquadro di visualizzazione.  Questi vengono presi in esame in modo più dettagliato nella sezione [scena, nodi, i componenti e fotocamere](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras).

La terza parte del nostro esempio attiva un paio di azioni.  Le azioni sono ricette che descrivono un effetto specifico e dopo la creazione possono essere eseguiti da un nodo su richiesta chiamando il [ `RunActionAsync` ](https://developer.xamarin.com/api/member/Urho.Node.RunActionsAsync) metodo su un `Node`.

La prima azione ridimensiona la finestra con un effetto di rimbalzo e il secondo ruota la casella per sempre:

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

Il codice precedente viene illustrato come viene la prima azione che viene creato un [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo/) azione, si tratta semplicemente una ricetta che indica che si desidera scalare per un secondo verso il valore uno la proprietà di scalabilità di un nodo.  Questa azione, a sua volta viene eseguito il wrapping intorno a un'azione di interpolazione, il [ `EaseBounceOut` ](https://developer.xamarin.com/api/type/Urho.Actions.EaseBounceInOut/) azione.  Le azioni di interpolazione distorcono l'esecuzione di un'azione lineare e applicano un effetto, in questo caso fornisce l'effetto di Rimbalzo orizzontale.
In modo nostra ricetta può essere scritta come:

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

Dopo aver creato il file recipe, eseguiamo il file recipe:

```csharp
await boxNode.RunActionsAsync (recipe)
```

Await indica che il dovranno riprendere l'esecuzione al termine di questa riga al termine dell'operazione.  Una volta completata l'azione è attiva la seconda animazione.

Il [uso di UrhoSharp](~/graphics-games/urhosharp/using.md) documento esamina in modo più approfondito i concetti alla base Urho e su come strutturare il codice per creare un gioco.

## <a name="copyrights"></a>Copyright

Questa documentazione vengono forniti contenuti originale da Xamarin Inc, ma traccia ampiamente dalla documentazione open source per il progetto Urho3D e è riportate le schermate dal progetto Cocos2D.

