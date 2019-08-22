---
title: Introduzione a OpenTK in Novell. Mac
description: Questo articolo fornisce un'introduzione all'uso di OpenTK in un'applicazione Novell. Mac. Viene illustrata la creazione e la gestione di una finestra di gioco, il rendering di un oggetto semplice e la visualizzazione dell'oggetto all'utente.
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: eb6eff59ccb44d9954139c294592fafcba1f08e0
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889529"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>Introduzione a OpenTK in Novell. Mac

OpenTK (The Open Toolkit) è una libreria avanzata di basso livello C# che rende più semplice l'uso di OpenGL, OpenCL e OpenAL. OpenTK può essere usato per giochi, applicazioni scientifiche o altri progetti che richiedono grafica 3D, audio o funzionalità di calcolo. Questo articolo fornisce una breve introduzione all'uso di OpenTK in un'app Novell. Mac.

[![](opentk-images/intro01.png "Esecuzione di un'app di esempio")](opentk-images/intro01.png#lightbox)

In questo articolo verranno illustrate le nozioni di base di OpenTK in un'applicazione Novell. Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in Questo articolo.

Si consiglia di esaminare la sezione [ C# esporre classi/metodi in Objective-C](~/mac/internals/how-it-works.md) del documento [interno di Novell. Mac](~/mac/internals/how-it-works.md) , spiegando `Register` i comandi e `Export` usati per collegare le C# classi a. Oggetti Objective-C ed elementi dell'interfaccia utente.

<a name="About_OpenTK" />

## <a name="about-opentk"></a>Informazioni su OpenTK

Come indicato in precedenza, OpenTK (The Open Toolkit) è una libreria avanzata di basso C# livello che rende più semplice l'uso di OpenGL, OpenCL e OpenAL. L'uso di OpenTK in un'app Novell. Mac offre le funzionalità seguenti:

- **Sviluppo rapido** : OpenTK fornisce tipi di dati avanzati e documentazione inline per migliorare il flusso di lavoro di codifica e rilevare gli errori in modo più semplice e rapido.
- **Facile integrazione** : OpenTK è stato progettato per integrarsi facilmente con le applicazioni .NET.
- **Licenza permissive** : OpenTK è distribuito con le licenze MIT/X11 ed è completamente gratuito.
- **Binding indipendenti dai tipi avanzati** : OpenTK supporta le versioni più recenti di OpenGL, OpenGL | ES, OpenAL e OpenCL con il caricamento automatico delle estensioni, il controllo degli errori e la documentazione inline.
- **Opzioni GUI flessibili** : OpenTK fornisce la finestra di gioco nativa a prestazioni elevate progettata specificamente per i giochi e Novell. Mac.
- **Codice completamente gestito** e conforme a CLS: OpenTK supporta le versioni a 32 bit e a 64 bit di MacOS senza librerie non gestite.
- **Toolkit per Math 3D** OpenTK fornisce `Vector`gli `Matrix`struct `Quaternion` , e`Bezier` tramite il suo 3D Math Toolkit.

OpenTK può essere usato per giochi, applicazioni scientifiche o altri progetti che richiedono grafica 3D, audio o funzionalità di calcolo.

Per ulteriori informazioni, vedere il sito Web [del Toolkit di apertura](http://www.opentk.com) .

<a name="OpenTK_Quickstart" />

## <a name="opentk-quickstart"></a>Guida introduttiva a OpenTK

Come introduzione rapida all'uso di OpenTK in un'app Novell. Mac, verrà creata una semplice applicazione che apre una visualizzazione del gioco, esegue il rendering di un triangolo semplice in tale visualizzazione e connette la visualizzazione del gioco alla finestra principale dell'app Mac per visualizzare il triangolo all'utente.

<a name="Starting_a_New_Project" />

### <a name="starting-a-new-project"></a>Avvio di un nuovo progetto

Avviare Visual Studio per Mac e creare una nuova soluzione Novell. Mac. Selezionare app **Mac** > app > **Cocoa** **generale**: > 

[![](opentk-images/sample01.png "Aggiunta di una nuova app Cocoa")](opentk-images/sample01.png#lightbox)

Immettere `MacOpenTK` per il **nome del progetto**:

[![](opentk-images/sample02.png "Impostazione del nome del progetto")](opentk-images/sample02.png#lightbox)

Fare clic sul pulsante **Crea** per compilare il nuovo progetto.

<a name="Including_OpenTK" />

### <a name="including-opentk"></a>Inclusione di OpenTK

Prima di poter usare Open TK in un'applicazione Novell. Mac, è necessario includere un riferimento all'assembly OpenTK. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sulla cartella **riferimenti** e scegliere **modifica riferimenti.**

Inserire un segno di `OpenTK` spunta per e fare clic sul pulsante **OK** :

[![](opentk-images/sample03.png "Modifica dei riferimenti al progetto")](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK" />

### <a name="using-opentk"></a>Uso di OpenTK

Dopo aver creato il nuovo progetto, fare doppio clic `MainWindow.cs` sul file nella **Esplora soluzioni** per aprirlo per la modifica. Rendere la `MainWindow` classe simile alla seguente:

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

Di seguito viene illustrato in dettaglio il codice.

<a name="Required_APIs" />

### <a name="required-apis"></a>API richieste

Per usare OpenTK in una classe Novell. Mac sono necessari diversi riferimenti. All'inizio della definizione sono state incluse le istruzioni seguenti `using` :

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

Questo set minimo sarà necessario per qualsiasi classe che usa OpenTK.

<a name="Adding_the_Game_View" />

### <a name="adding-the-game-view"></a>Aggiunta della visualizzazione del gioco

A questo punto è necessario creare una visualizzazione giochi per contenere tutte le interazioni con OpenTK e visualizzare i risultati. È stato usato il codice seguente:

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

Qui abbiamo fatto in modo che la visualizzazione del gioco sia uguale a quella della finestra principale del Mac e che la visualizzazione contenuto della finestra `MonoMacGameView`sia stata sostituita con la nuova. Poiché il contenuto della finestra esistente è stato sostituito, la visualizzazione assegnata verrà ridimensionata automaticamente quando le finestre principali verranno ridimensionate.

<a name="Responding_to_Events" />

### <a name="responding-to-events"></a>Risposta agli eventi

Sono presenti diversi eventi predefiniti a cui ogni visualizzazione del gioco deve rispondere. In questa sezione vengono descritti gli eventi principali necessari.

<a name="The_Load_Event" />

### <a name="the-load-event"></a>Evento Load

L' `Load` evento è la posizione in cui caricare le risorse dal disco, ad esempio immagini, trame o musica. Per la nostra semplice app di test, l' `Load` evento non viene usato, ma è stato incluso come riferimento:

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event" />

### <a name="the-resize-event"></a>Evento di ridimensionamento

L' `Resize` evento deve essere chiamato ogni volta che la visualizzazione del gioco viene ridimensionata. Per l'app di esempio, il viewport GL viene reso della stessa dimensione della visualizzazione del gioco, che viene ridimensionato automaticamente dalla finestra principale Mac, con il codice seguente:

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event" />

### <a name="the-updateframe-event"></a>Evento UpdateFrame

L' `UpdateFrame` evento viene usato per gestire l'input dell'utente, aggiornare le posizioni degli oggetti, eseguire calcoli di fisica o intelligenza artificiale. Per la nostra semplice app di test, l' `UpdateFrame` evento non viene usato, ma è stato incluso come riferimento: 

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> L'implementazione di Novell. Mac di OpenTK non include il `Input API`, pertanto sarà necessario usare le API fornite da Apple per aggiungere il supporto della tastiera e del mouse. Facoltativamente `MonoMacGameView` , è possibile creare un'istanza personalizzata di ed eseguire l' `KeyDown` override `KeyUp` dei metodi e.

<a name="The_RenderFrame_Event" />

### <a name="the-renderframe-event"></a>Evento RenderFrame

L' `RenderFrame` evento contiene il codice usato per eseguire il rendering (disegnare) della grafica. Per l'app di esempio, la visualizzazione del gioco viene compilata con un semplice triangolo: 

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

Il codice di rendering viene in genere eseguito con una `GL.Clear` chiamata a per rimuovere tutti gli elementi esistenti prima di disegnare i nuovi elementi.

> [!IMPORTANT]
> Per la versione Novell. Mac di OpenTK **non** chiamare il `SwapBuffers` metodo dell' `MonoMacGameView` istanza alla fine del codice di rendering. In questo modo la visualizzazione del gioco si attiverà rapidamente anziché visualizzare la visualizzazione sottoposta a rendering.

<a name="Running_the_Game_View" />

### <a name="running-the-game-view"></a>Esecuzione della visualizzazione del gioco

Con tutti gli eventi richiesti definiti e la visualizzazione del gioco collegata alla finestra principale del Mac dell'app, viene letto per eseguire la visualizzazione del gioco e visualizzare la grafica. Usare il codice riportato di seguito.

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

Si passa alla frequenza dei fotogrammi desiderata a cui si vuole aggiornare la visualizzazione del gioco, per questo esempio sono stati `60` scelti frame al secondo (la stessa frequenza di aggiornamento della normale TV).

Eseguire l'app e visualizzare l'output:

[![](opentk-images/intro01.png "Esempio di output delle app")](opentk-images/intro01.png#lightbox)

Se si ridimensiona la finestra, anche la visualizzazione del gioco verrà ripartita e il triangolo verrà ridimensionato e aggiornato in tempo reale.

<a name="Where_to_Next" />

### <a name="where-to-next"></a>Posizione successiva

Con le nozioni di base sull'uso di OpenTk in un'applicazione Novell. Mac, di seguito sono riportati alcuni suggerimenti su come provare a eseguire le operazioni seguenti:

- Provare a modificare il colore del triangolo e il colore di sfondo della visualizzazione del gioco negli `Load` eventi `RenderFrame` e.
- Modificare il colore del triangolo quando l'utente preme un `UpdateFrame` tasto negli eventi e `RenderFrame` o creare una classe personalizzata `MonoMacGameView` ed eseguire l'override dei `KeyUp` metodi `KeyDown` e.
- Fare in modo che il triangolo si sposti sullo schermo usando le chiavi `UpdateFrame` in grado di riconoscere nell'evento. Hint: usare il `Matrix4.CreateTranslation` metodo per creare una matrice di traslazione `GL.LoadMatrix` e chiamare il metodo `RenderFrame` per caricarla nell'evento.
- Usare un `for` ciclo per eseguire il rendering di diversi triangoli `RenderFrame` nell'evento.
- Ruotare la fotocamera per fornire una visualizzazione diversa del triangolo nello spazio 3D. Hint: usare il `Matrix4.CreateTranslation` metodo per creare una matrice di traslazione `GL.LoadMatrix` e chiamare il metodo per caricarla. È anche possibile usare le `Vector2`classi `Vector3` `Vector4` , e `Matrix4` per le manipolazioni della fotocamera.

Per altri esempi, vedere il repository [GitHub di OpenTK Samples](https://github.com/opentk/opentk/tree/master/Source/Examples) . Contiene un elenco ufficiale di esempi di uso di OpenTK. È necessario adattare questi esempi per l'uso di con la versione Novell. Mac di OpenTK.

Per un esempio Novell. Mac più complesso di un'implementazione di OpenTK, vedere l'esempio [MonoMacGameView](https://docs.microsoft.com/samples/xamarin/mac-samples/monomacgamewindow) .

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato rapidamente l'uso di OpenTK in un'applicazione Novell. Mac. È stato illustrato come creare una finestra del gioco, come allinearla a una finestra Mac e come eseguire il rendering di una forma semplice nella finestra del gioco.

## <a name="related-links"></a>Collegamenti correlati

- [MacOpenTK (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/macopentk)
- [MonoMacGameView (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/monomacgamewindow)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Open Toolkit](http://www.opentk.com)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
