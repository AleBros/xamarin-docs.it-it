---
title: Introduzione a UrhoSharp
description: Questo documento descrive la struttura di base di un'applicazione UrhoSharp e i collegamenti a diverse guide e applicazioni di esempio che illustrano come usare UrhoSharp.
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 37540053cee03a83582fe19ffb1dcf9e1cf4564c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73011736"
---
# <a name="introduction-to-urhosharp"></a>Introduzione a UrhoSharp

![Logo di UrhoSharp](introduction-images/urhosharp-icon.png)

UrhoSharp è un potente motore di gioco 3D per gli sviluppatori Xamarin e .NET.  Si tratta di uno spirito simile a SceneKit e SpriteKit di Apple e includono la fisica, la navigazione, la rete e molto altro ancora.

Si tratta di un binding .NET al motore [Urho3D](https://urho3d.github.io/) e consente agli sviluppatori di scrivere codice multipiattaforma che può essere destinato a Android, iOS, Windows e Mac con la stessa codebase ed è in grado di eseguire il rendering nei sistemi OpenGL e Direct3D.

UrhoSharp è un motore di gioco con numerose funzionalità predefinite:

- Potente rendering grafica 3D
- Simulazione fisica (usando la libreria Bullet)
- Gestione della scena
- Supporto di await/Async
- API friendly actions
- integrazione 2D in scene 3D
- Rendering dei tipi di carattere con FreeType
- Funzionalità di rete client e server
- Importare un'ampia gamma di asset (con la libreria open assets)
- Mesh di navigazione e pathfinding (usando il ricast/deviazione)
- Generazione di gusci convessi per il rilevamento di collisioni (tramite StanHull)
- Riproduzione audio (con **libvorbis**)

## <a name="get-started"></a>Introduzione

UrhoSharp è comodamente distribuito come [pacchetto NuGet](https://www.nuget.org/) e può essere aggiunto ai progetti C# o F# destinati a Windows, Mac, Android o iOS.  NuGet include entrambe le librerie necessarie per eseguire il programma, nonché gli asset di base (CoreData) usati dal motore.

### <a name="urho-as-a-portable-class-library"></a>Urho come libreria di classi portabile

Il pacchetto Urho può essere utilizzato da un progetto specifico della piattaforma o da un progetto libreria di classi portabile, consentendo di riutilizzare tutto il codice in tutte le piattaforme.  Ciò significa che tutto ciò che è necessario fare in ogni piattaforma è scrivere il punto di ingresso specifico della piattaforma e quindi trasferire il controllo al codice del gioco condiviso.

### <a name="samples"></a>Esempi

È possibile ottenere un buon gusto per le funzionalità di Urho aprendo in Visual Studio per Mac o Visual Studio la soluzione di esempio da:

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

La soluzione predefinita contiene progetti per Android, iOS, Windows e Mac.  Questa soluzione è stata strutturata in modo da avere un piccolo avvio specifico della piattaforma e tutto il codice di esempio e il codice del gioco si trovano in una libreria di classi portabile, illustrando come ottimizzare il riutilizzo del codice in tutte le piattaforme.

Per altre informazioni su come creare soluzioni personalizzate, vedere la pagina [Urho e la piattaforma](~/graphics-games/urhosharp/platform/index.md) .

Poiché tutti gli esempi condividono un insieme comune di elementi dell'interfaccia utente, gli esempi hanno astratte la configurazione di base in questo file:

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

Fornisce una classe base di esempio che gestisce alcune sequenze di tasti di base ed eventi di tocco, configura una fotocamera, fornisce elementi di base dell'interfaccia utente e consente a ogni esempio di concentrarsi sulle funzionalità specifiche presentate.

Nell'esempio seguente viene illustrato ciò che il motore è in grado di eseguire:

- [Samply Gioca](https://github.com/xamarin/urho-samples/tree/master/SamplyGame) un clone semplice di ShootySkies.

Mentre gli altri esempi mostrano le singole proprietà di ogni esempio.

## <a name="basic-structure"></a>Struttura di base

Il gioco deve sottocreare una sottoclasse della classe `Application`, in cui verrà configurato il gioco (nel metodo `Setup`) e verrà avviato il gioco (nel metodo `Start`).  Quindi si costruisce l'interfaccia utente principale.  Verrà esaminato un piccolo esempio che mostra le API per configurare una scena 3D, alcuni elementi dell'interfaccia utente e un semplice comportamento.

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

Se si esegue questa applicazione, si noterà rapidamente che il Runtime sta comunicando che gli asset non sono presenti.  È necessario creare una gerarchia nel progetto che inizia con il nome di directory speciale "data" e, in questo caso, inserire gli asset a cui si fa riferimento nel programma.  È quindi necessario impostare le proprietà dell'elemento per ogni asset "copia nella directory di output" su "copia se più recente", in modo da garantire che i dati siano presenti.

Verrà illustrato il funzionamento di questo articolo.

Per avviare l'applicazione, chiamare la funzione di inizializzazione del motore, quindi creare una nuova istanza della classe dell'applicazione, come segue:

```csharp
new MySample().Run();
```

Il runtime richiamerà i metodi `Setup` e `Start`.  Se si esegue l'override di `Setup` è possibile configurare i parametri del motore (non mostrati in questo esempio).

È necessario eseguire l'override di `Start` perché verrà avviato il gioco.  In questo metodo si caricherà gli asset, si connettono i gestori eventi, si configura la scena e si avviano le azioni desiderate.  Nel nostro esempio, creiamo un po' di interfaccia utente per visualizzare l'utente, oltre a configurare una scena 3D.

Il frammento di codice seguente usa il Framework dell'interfaccia utente per creare un elemento di testo e aggiungerlo all'applicazione:

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

Il Framework dell'interfaccia utente è disponibile per fornire un'interfaccia utente molto semplice del gioco, che funziona aggiungendo nuovi nodi al nodo `UI.Root`.

La seconda parte dell'esempio configura la scena principale.  Ciò implica una serie di passaggi, la creazione di una scena 3D, la creazione di una finestra 3D nella schermata, l'aggiunta di una luce, una fotocamera e un viewport.  Queste informazioni vengono esaminate più dettagliatamente nella sezione [scena, nodi, componenti e fotocamere](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras).

La terza parte dell'esempio attiva alcune azioni.  Le azioni sono ricette che descrivono un particolare effetto e, una volta create, possono essere eseguite da un nodo su richiesta chiamando il metodo `RunActionAsync` su una `Node`.

La prima azione Ridimensiona la casella con un effetto di rimbalzo e la seconda ruota la casella per sempre:

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

Il precedente Mostra come la prima azione creata è un'azione `ScaleTo`, si tratta semplicemente di una ricetta che indica che si vuole ridimensionare un secondo verso il valore una proprietà scale di un nodo.  Questa azione viene a sua volta racchiusa tra un'azione di interpolazione, l'azione `EaseBounceOut`.  Le azioni di interpolazione comportano la distorsione dell'esecuzione lineare di un'azione e l'applicazione di un effetto, in questo caso fornisce l'effetto di rimbalzo.
Quindi, la ricetta può essere scritta come segue:

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

Una volta creata la ricetta, viene eseguita la ricetta:

```csharp
await boxNode.RunActionsAsync (recipe)
```

Await indica che la funzionerà per riprendere l'esecuzione dopo questa riga al termine dell'azione.  Al termine dell'azione, viene attivata la seconda animazione.

Il documento [using UrhoSharp](~/graphics-games/urhosharp/using.md) Esplora in modo più approfondito i concetti alla base di Urho e come strutturare il codice per la creazione di un gioco.

## <a name="copyrights"></a>Copyright

Questa documentazione contiene contenuti originali di Xamarin Inc, ma viene disegnata in maniera estensiva dalla documentazione Open Source per il progetto Urho3D e contiene screenshot del progetto Cocos2D.
