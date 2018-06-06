---
title: Introduzione a UrhoSharp
description: Questo documento descrive la struttura di base di un'applicazione di UrhoSharp e collegamenti alle varie guide e applicazioni di esempio che illustrano come usare UrhoSharp.
ms.prod: xamarin
ms.assetid: 18041443-5093-4AF7-8B20-03E00478EF35
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: d39faabc0851e3e89b03ad58a7f1ead3894efc15
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783555"
---
# <a name="an-introduction-to-urhosharp"></a>Introduzione a UrhoSharp

![Logo UrhoSharp](introduction-images/urhosharp-icon.png)

UrhoSharp è un potente motore di giochi 3D per gli sviluppatori di Xamarin e .NET.  È simile a Apple SceneKit e SpriteKit bevande e includere fisica, navigazione, rete e molto più mentre viene ancora più piattaforme.

È un'associazione di .NET per la [Urho3D](http://urho3d.github.io/) del motore e consente agli sviluppatori di scrivere codice multipiattaforma destinate Android, iOS, Windows e Mac con la stessa codebase e possono eseguire il rendering OpenGL sia Direct3D sistemi.

UrhoSharp è un motore di gioco con molte funzionalità predefinita:

- Per il rendering di grafica 3D di potente
- [Simulazione di fisica](https://developer.xamarin.com/api/namespace/Urho.Physics/) (tramite la libreria punto elenco)
- [Gestione di scena](https://developer.xamarin.com/api/type/Urho.Scene/)
- Supporto Await/asincrono
- [Azioni descrittive API](https://developer.xamarin.com/api/namespace/Urho.Actions/)
- [Integrazione 2D scene 3D](https://developer.xamarin.com/api/namespace/Urho.Urho2D/)
- [Rendering dei caratteri con FreeType](https://developer.xamarin.com/api/type/Urho.Gui.FontFaceFreeType/)
- [Funzionalità di rete server e client](https://developer.xamarin.com/api/namespace/Urho.Network/)
- [Importare una vasta gamma di risorse](https://developer.xamarin.com/api/namespace/Urho.Resources/) (con aprire raccolta di risorse)
- [Rete di navigazione e pathfinding](https://developer.xamarin.com/api/namespace/Urho.Navigation/) (tramite rifondere in un nuovo/deviazione)
- [Generazione di struttura convessa per il rilevamento di collisioni](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/) (tramite StanHull)
- [Riproduzione audio](https://developer.xamarin.com/api/namespace/Urho.Audio/) (con **libvorbis**)

## <a name="getting-started"></a>Introduzione

UrhoSharp viene distribuito in modo appropriato come un [pacchetto NuGet](https://www.nuget.org/) e può essere aggiunto ai progetti in c# o F # destinate a Windows, Mac, Android o iOS.  NuGet include sia le librerie necessarie per eseguire il programma, nonché le risorse di base (CoreData) utilizzate dal motore.

### <a name="urho-as-a-portable-class-library"></a>Urho come una libreria di classi portabile

Il pacchetto Urho può essere utilizzato da un progetto specifico della piattaforma o da un progetto libreria di classi portabile, che consente di riutilizzare tutto il codice per tutte le piattaforme.  Ciò significa che tutto è necessario eseguire in ciascuna piattaforma è come scrivere il punto di ingresso specifico della piattaforma e quindi trasferire il controllo al codice condiviso gioco.

### <a name="samples"></a>Esempi

È possibile ottenere un'idea per le funzionalità di Urho aprendo in Visual Studio per Mac o Visual Studio la soluzione di esempio da:

[https://github.com/xamarin/urho-samples](https://github.com/xamarin/urho-samples)

La soluzione predefinito contiene progetti per Android, iOS, Windows e Mac.  È stato strutturato tale soluzione in modo che è disponibile un'utilità di avvio specifico della piattaforma di piccole dimensioni, mentre tutto il codice di esempio e il codice del gioco si trova in una libreria di classi portabile, che illustra come ottimizzare il riutilizzo del codice per tutte le piattaforme.

Consultare la [Urho e piattaforma di](~/graphics-games/urhosharp/platform/index.md) pagina per altre informazioni su come creare soluzioni personalizzate.

Poiché tutti gli esempi condividono un set comune di elementi dell'interfaccia utente, gli esempi sono indipendenti e possono essere il programma di installazione di base in questo file:

[https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/Core/Sample.cs)

Ciò fornisce una classe di base di esempio che gestisce alcuni tasti di base e tocco eventi, le impostazioni di una fotocamera, fornisce gli elementi dell'interfaccia utente di base e questo consente a ogni esempio di concentrarsi sulle funzionalità specifiche che è viene mostrata.

L'esempio seguente viene illustrato ciò che il motore è in grado di procedere:

- [Gioco samply](https://github.com/xamarin/urho-samples/tree/master/SamplyGame) un clone di ShootySkies semplice.

Mentre gli altri esempi mostrano le singole proprietà di ogni esempio.

## <a name="basic-structure"></a>Struttura di base

Il gioco deve essere una sottoclasse di [ `Application` ](https://developer.xamarin.com/api/type/Urho.Application/) (classe), si tratta in cui si installerà il gioco (sul [ `Setup` ](https://developer.xamarin.com/api/member/Urho.Application.Setup/) (metodo)) e iniziare il gioco (nel [ `Start` ](https://developer.xamarin.com/api/member/Urho.Application.Start) (metodo)).  È quindi necessario creare un'interfaccia utente principale.  Verrà illustrata un piccolo esempio che illustra le API per il programma di installazione una scena 3D, alcuni elementi dell'interfaccia utente e la connessione di un comportamento semplice all'applicazione.

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

Se si esegue l'applicazione, si scoprirà presto che il runtime reclamo le risorse non sono presenti.  È necessario eseguire è creare una gerarchia del progetto che inizia con il nome di directory speciale "Data" e all'interno di questa operazione, si posiziona un asset di cui si fa riferimento nel programma.  È quindi necessario impostare nelle proprietà dell'elemento per ogni asset "Copia in Directory di Output" per "Copia se più recente", che garantisce che i dati siano presente.

Segnalare il problema, spiegare cosa accade in seguito.

Per avviare l'applicazione si chiama la funzione di inizializzazione del motore, seguita da creare una nuova istanza della classe dell'applicazione, simile al seguente:

```csharp
new MySample().Run();
```

Verrà richiamato dal runtime di `Setup` e `Start` metodi.  Se esegue l'override `Setup` è possibile configurare i parametri del motore (non illustrato in questo esempio).

È necessario eseguire l'override `Start` come verrà avviato il gioco.  In questo metodo verrà caricare le risorse, connessione di gestori eventi, scena del programma di installazione e avviare le azioni che si desiderano.  In questo esempio, entrambi creiamo un po' di interfaccia utente da visualizzare per l'utente, nonché impostare una scena 3D.

Il frammento di codice seguente utilizza il framework dell'interfaccia utente per creare un elemento di testo e aggiungerlo all'applicazione:

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

Framework dell'interfaccia utente è presente per fornire un'interfaccia utente di gioco molto semplice e funziona tramite l'aggiunta di nuovi nodi per il [ `UI.Root` ](https://developer.xamarin.com/api/property/Urho.Gui.UI.Root/) nodo.

La seconda parte del nostro installazioni esempio scena principale.  Ciò comporta un numero di passaggi, creando una scena 3D, la creazione di una casella 3D nella schermata di aggiunta di una luce, fotocamera e un riquadro di visualizzazione.  Questi vengono esaminati più dettagliatamente nella sezione [scena, i nodi, componenti e fotocamere](~/graphics-games/urhosharp/using.md#scenenodescomponentsandcameras).

La terza parte dell'esempio attiva un paio di azioni.  Le azioni sono alcuni esempi che descrivono un effetto specifico e una volta creato possono essere eseguite da un nodo su richiesta chiamando il [ `RunActionAsync` ](https://developer.xamarin.com/api/member/Urho.Node.RunActionsAsync) metodo su un `Node`.

La prima azione ridimensiona la finestra con un effetto di rimbalzo e secondo ruota la casella per sempre:

```csharp
await boxNode.RunActionsAsync(
    new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1)));
```

Sopra vengono illustrate la prima azione che verrà creata una [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo/) azione, si tratta semplicemente una recipe che indica che si desidera applicare la scalabilità per un secondo valore uno la proprietà di scala di un nodo.  Questa azione, a sua volta viene eseguito il wrapping intorno a un'azione di interpolazione, la [ `EaseBounceOut` ](https://developer.xamarin.com/api/type/Urho.Actions.EaseBounceInOut/) azione.  Le azioni di interpolazione distorcere l'esecuzione di un'azione lineare e applicano un effetto, in questo caso fornisce l'effetto di risposta in uscita.
Così come è possibile scrivere la ricetta:

```csharp
var recipe = new EaseBounceOut(new ScaleTo(duration: 1f, scale: 1));
```

Dopo aver creata la soluzione, viene eseguita la soluzione:

```csharp
await boxNode.RunActionsAsync (recipe)
```

Await indica che di desidera riprendere l'esecuzione dopo questa riga al termine dell'operazione.  Una volta completata l'azione è attivata l'animazione di secondo.

Il [UrhoSharp utilizzando](~/graphics-games/urhosharp/using.md) documento viene esaminata in modo più approfondito i concetti di base Urho e su come strutturare il codice per compilare un gioco.

## <a name="copyrights"></a>Copyright

Questa documentazione contiene contenuto originale da Inc Xamarin, ma disegna ampiamente nella documentazione di origine aperti per il progetto Urho3D e schermate dal progetto Cocos2D.

### <a name="related-links"></a>Collegamenti correlati

- [Cartella di lavoro di terra pianeta](https://developer.xamarin.com/workbooks/graphics/urhosharp/planetearth/planetearth.workbook)
- [Esplorazione di cartella di lavoro di coordinate](https://developer.xamarin.com/workbooks/graphics/urhosharp/coordinates/ExploringUrhoCoordinates.workbook)
