---
title: Introduzione a OpenTK
description: In questo articolo viene fornita un'introduzione all'utilizzo in un'applicazione Xamarin.Mac OpenTK. Vengono illustrate la creazione e gestione di una finestra del gioco, il rendering di un oggetto semplice e la visualizzazione dell'oggetto per l'utente.
ms.topic: article
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: f5383465f7bc5c4529eebefca02718c83a653e9f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="introduction-to-opentk"></a>Introduzione a OpenTK

OpenTK (il Toolkit Open) è un'avanzate di basso livello c# libreria che semplifica l'utilizzo di OpenGL e OpenCL OpenAL. OpenTK può essere utilizzato per giochi, le applicazioni di scientifiche o altri progetti che richiedono la grafica 3D, le funzionalità audio o di calcolo. In questo articolo fornisce una breve introduzione all'utilizzo OpenTK in un'app Xamarin.Mac.

[![](opentk-images/intro01.png "Eseguire un'app di esempio")](opentk-images/intro01.png#lightbox)

In questo articolo verranno descritte le nozioni di base di OpenTK in un'applicazione Xamarin.Mac. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

Si consiglia di esaminare il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` comandi utilizzato per le classi c# in transito-fino a oggetti Objective-C e gli elementi dell'interfaccia utente.

<a name="About_OpenTK" />

## <a name="about-opentk"></a>Su OpenTK

Come descritto in precedenza, OpenTK (il Toolkit Open) è un'avanzate di basso livello c# libreria che semplifica l'utilizzo di OpenGL e OpenCL OpenAL. L'uso di OpenTK in un'app Xamarin.Mac offre le funzionalità seguenti:

- **Lo sviluppo rapido** -OpenTK fornisce tipi di dati complessa e documentazione in linea per migliorare il flusso di lavoro di codifica e rilevamento degli errori in modo più semplice e più rapidamente.
- **Integrazione** -OpenTK è stato progettato per integrarsi facilmente con le applicazioni .NET.
- **Licenza permissiva** -OpenTK è distribuito ai sensi delle licenze MIT/X11 ed è completamente gratuito.
- **Il binding indipendente dai tipi a numerose** -OpenTK supporta le versioni più recenti di OpenGL, OpenGL | ES, OpenAL e OpenCL con il caricamento di estensione automatica, documentazione in linea e la verifica di errore.
- **Opzioni flessibili di GUI** -OpenTK fornisce nativo, ad alte prestazioni gioco finestra progettato specificamente per giochi e Xamarin.Mac.
- **Completamente gestito, codice conforme a CLS** -OpenTK supporta le versioni a 32 e 64 bit di macOS senza librerie non gestite.
- **Toolkit di matematica 3D** OpenTK fornisce `Vector`, `Matrix`, `Quaternion` e `Bezier` struct tramite il Toolkit di matematica 3D.

OpenTK può essere utilizzato per giochi, le applicazioni di scientifiche o altri progetti che richiedono la grafica 3D, le funzionalità audio o di calcolo.

Per ulteriori informazioni, vedere [il Toolkit aprire](http://www.opentk.com) sito Web.

<a name="OpenTK_Quickstart" />

## <a name="opentk-quickstart"></a>Guida introduttiva OpenTK

Come una rapida introduzione all'utilizzo OpenTK in un'app Xamarin.Mac, verrà creata una semplice applicazione che apre una visualizzazione di gioco, esegue il rendering di un triangolo semplice in tale visualizzazione e la visualizzazione di gioco attachs finestra principale dell'applicazione Mac da visualizzare sul triangolo all'utente.

<a name="Starting_a_New_Project" />

### <a name="starting-a-new-project"></a>Avvio di un nuovo progetto

Avviare Visual Studio per Mac e creare una nuova soluzione Xamarin.Mac. Selezionare **Mac** > **App** > **generale** > **Cocoa App**:

[![](opentk-images/sample01.png "Aggiunta di una nuova App Cocoa")](opentk-images/sample01.png#lightbox)

Immettere `MacOpenTK` per il **nome progetto**:

[![](opentk-images/sample02.png "L'impostazione del nome di progetto")](opentk-images/sample02.png#lightbox)

Fare clic su di **crea** per compilare il nuovo progetto.

<a name="Including_OpenTK" />

### <a name="including-opentk"></a>Tra cui OpenTK

Prima di poter utilizzare TK aperto in un'applicazione Xamarin.Mac, è necessario includere un riferimento all'assembly OpenTK. Nel **Esplora**, fare doppio clic su di **riferimenti** cartella e selezionare **Modifica riferimenti...** .

Inserire un segno di spunta da `OpenTK` e fare clic su di **OK** pulsante:

[![](opentk-images/sample03.png "Modifica i riferimenti al progetto")](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK" />

### <a name="using-opentk"></a>Utilizzando OpenTK

Con il nuovo progetto creato, fare doppio clic su di `MainWindow.cs` file nel **Esplora** per aprirlo e modificarlo. Rendere il `MainWindow` classe aspetto simile al seguente:

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacOpenTK
{
    public partial class MainWindow : NSWindow
    {
        #region Computed Properties
        public MonoMacGameView Game { get; set; }
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create new Game View and replace the window content with it
            Game = new MonoMacGameView(ContentView.Frame);
            ContentView = Game;

            // Wire-up any required Game events
            Game.Load += (sender, e) =>
            {
                // TODO: Initialize settings, load textures and sounds here
            };

            Game.Resize += (sender, e) =>
            {
                // Adjust the GL view to be the same size as the window
                GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
            };

            Game.UpdateFrame += (sender, e) =>
            {
                // TODO: Add any game logic or physics
            };

            Game.RenderFrame += (sender, e) =>
            {
                // Setup buffer
                GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
                GL.MatrixMode(MatrixMode.Projection);

                // Draw a simple triangle
                GL.LoadIdentity();
                GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
                GL.Begin(BeginMode.Triangles);
                GL.Color3(Color.MidnightBlue);
                GL.Vertex2(-1.0f, 1.0f);
                GL.Color3(Color.SpringGreen);
                GL.Vertex2(0.0f, -1.0f);
                GL.Color3(Color.Ivory);
                GL.Vertex2(1.0f, 1.0f);
                GL.End();

            };

            // Run the game at 60 updates per second
            Game.Run(60.0);
        }
        #endregion
    }
}
```

È opportuno discutere questo codice in dettaglio di seguito.

<a name="Required_APIs" />

### <a name="required-apis"></a>API necessarie

Più riferimenti devono utilizzare OpenTK in una classe Xamarin.Mac. Abbiamo incluso quanto segue all'inizio della definizione di `using` istruzioni:

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using CoreGraphics;
```
Il set minimo necessario per qualsiasi classe mediante OpenTK.

<a name="Adding_the_Game_View" />

### <a name="adding-the-game-view"></a>Aggiunta della visualizzazione di gioco

Successivamente, è necessario creare una vista di gioco per contenere tutti i nostri l'interazione con OpenTK e visualizzare i risultati. È stato utilizzato il codice seguente:

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

Di seguito sono stati apportati la visualizzazione di gioco le stesse dimensioni come la finestra principale di Mac e sostituire la visualizzazione contenuto della finestra con il nuovo `MonoMacGameView`. Poiché è sostituito il contenuto della finestra esistente, la vista assegnato verrà ridimensionato automaticamente quando viene ridimensionato il principale di Windows.

<a name="Responding_to_Events" />

### <a name="responding-to-events"></a>Risposta agli eventi

Esistono numerosi eventi predefiniti che visualizza ogni gioco deve rispondere. In questa sezione illustra gli eventi principali necessarie.

<a name="The_Load_Event" />

### <a name="the-load-event"></a>L'evento di caricamento

Il `Load` eventi sono possibile caricare le risorse da disco, ad esempio immagini, trame o musica. Per la semplice app di test, non vengono utilizzati il `Load` evento, ma sono inclusi come riferimento:

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event" />

### <a name="the-resize-event"></a>L'evento di ridimensionamento

Il `Resize` evento deve essere chiamato ogni volta che viene ridimensionata la visualizzazione di gioco. Per l'applicazione di esempio, stiamo Viewport GL le stesse dimensioni come la visualizzazione di gioco (che è essere automaticamente ridimensionato dalla finestra principale Mac) con il codice seguente:

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event" />

### <a name="the-updateframe-event"></a>L'evento UpdateFrame

Il `UpdateFrame` eventi consente di gestire l'input dell'utente, aggiornare le posizioni di oggetto, la fase fisica o calcoli AI. Per la semplice app di test, non vengono utilizzati il `UpdateFrame` evento, ma sono inclusi come riferimento: 

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> L'implementazione di Xamarin.Mac di OpenTK non include il `Input API`, pertanto è necessario utilizzare l'Apple che supportano le API per aggiungere una tastiera e Mouse. Facoltativamente è possibile creare un'istanza personalizzata del `MonoMacGameView` ed eseguire l'override di `KeyDown` e `KeyUp` metodi.

<a name="The_RenderFrame_Event" />

### <a name="the-renderframe-event"></a>L'evento RenderFrame

Il `RenderFrame` evento contiene il codice che viene utilizzato per eseguire il rendering (disegno) degli elementi grafici. Per l'applicazione di esempio, ci stiamo riempimento la visualizzazione di gioco con un triangolo semplice: 

```csharp
Game.RenderFrame += (sender, e) =>
{
    // Setup buffer
    GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
    GL.MatrixMode(MatrixMode.Projection);

    // Draw a simple triangle
    GL.LoadIdentity();
    GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
    GL.Begin(BeginMode.Triangles);
    GL.Color3(Color.MidnightBlue);
    GL.Vertex2(-1.0f, 1.0f);
    GL.Color3(Color.SpringGreen);
    GL.Vertex2(0.0f, -1.0f);
    GL.Color3(Color.Ivory);
    GL.Vertex2(1.0f, 1.0f);
    GL.End();

};
```

In genere il codice di rendering verrà being con una chiamata a `GL.Clear` per rimuovere tutti gli elementi esistenti prima di disegnare i nuovi elementi.

> [!IMPORTANT]
> Per la versione Xamarin.Mac di OpenTK **non** chiamare il `SwapBuffers` metodo i `MonoMacGameView` istanza alla fine del codice per il rendering. Questa operazione comporta la visualizzazione gioco strobe rapidamente anziché visualizzare la vista viene eseguito il rendering.

<a name="Running_the_Game_View" />

### <a name="running-the-game-view"></a>Esegue la visualizzazione di gioco

Con tutti gli eventi necessari definire e la visualizzazione di gioco associata alla finestra Mac principale dell'applicazione, è in lettura per eseguire la vista di gioco e visualizzare i grafici. Usare il codice riportato di seguito.

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

Siamo passati la frequenza dei fotogrammi desiderato che si desidera che la visualizzazione di gioco per l'aggiornamento a, ad esempio, abbiamo scelto `60` fotogrammi al secondo (la stessa frequenza di aggiornamento come TV normale).

Si esegue l'app e visualizzare l'output:

[![](opentk-images/intro01.png "Un esempio dell'output di App")](opentk-images/intro01.png#lightbox)

Se si ridimensiona la finestra, la visualizzazione di gioco sarà anche risiedere e il triangolo verrà ridimensionato e aggiornato in tempo reale.

<a name="Where_to_Next" />

### <a name="where-to-next"></a>Dove successivo?

Con i concetti di base dell'utilizzo di un'applicazione Xamarin.mac eseguita con OpenTk di seguito sono riportati alcuni suggerimenti sulle operazioni da provare Avanti:

- Provare a modificare il colore del triangolo e il colore di sfondo della visualizzazione in gioco il `Load` e `RenderFrame` eventi.
- Modificare il triangolo di colore quando l'utente preme un tasto nel `UpdateFrame` e `RenderFrame` eventi personalizzati o `MonoMacGameView` classe ed eseguire l'override di `KeyUp` e `KeyDown` metodi.
- Rendere il triangolo di spostare sullo schermo usando le chiavi in grado di riconoscere il `UpdateFrame` evento. Suggerimento: usare il `Matrix4.CreateTranslation` metodo per creare una matrice di traslazione e chiamare il `GL.LoadMatrix` per caricare nel `RenderFrame` evento.
- Utilizzare un `for` ciclo per eseguire il rendering diverse triangoli il `RenderFrame` evento.
- Ruotare la fotocamera per fornire una vista diversa del triangolo nello spazio 3D. Suggerimento: usare il `Matrix4.CreateTranslation` metodo per creare una matrice di traslazione e chiamare il `GL.LoadMatrix` metodo per caricarlo. È inoltre possibile utilizzare il `Vector2`, `Vector3`, `Vector4` e `Matrix4` classi per le modifiche della fotocamera.

Per ulteriori esempi, vedere il [GitHub degli esempi OpenTK](https://github.com/opentk/opentk/tree/master/Source/Examples) repository. Contiene un elenco di esempi di utilizzo OpenTK ufficiale. È necessario adattare questi esempi per l'utilizzo con la versione Xamarin.Mac di OpenTK.

Per un esempio più complesso Xamarin.Mac di un'implementazione OpenTK, consultare il nostro [MonoMacGameView](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/) esempio.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato un rapido controllo di utilizzo in un'applicazione Xamarin.Mac OpenTK. È stato illustrato come creare una finestra del gioco, come collegare la finestra di gioco a una finestra Mac e come eseguire il rendering di una forma semplice nella finestra del gioco.

## <a name="related-links"></a>Collegamenti correlati

- [MacOpenTK (esempio)](https://developer.xamarin.com/samples/mac/MacOpenTK/)
- [MonoMacGameView (esempio)](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Il Toolkit aperto](http://www.opentk.com)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
