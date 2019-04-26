---
title: Introduzione a OpenTK in xamarin. Mac
description: Questo articolo fornisce un'introduzione all'uso di OpenTK in un'applicazione xamarin. Mac. Viene illustrata la creazione e la gestione di una finestra del gioco, il rendering di un oggetto semplice e la visualizzazione dell'oggetto all'utente.
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 835b8cd0f2e689c4d7d4cace1d846543863b7393
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032686"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>Introduzione a OpenTK in xamarin. Mac

OpenTK (il Toolkit Open) è un'avanzato di basso livello libreria c# che semplifica l'uso di OpenGL e OpenCL OpenAL. OpenTK utilizzabile per giochi, applicazioni scientifiche o altri progetti che richiedono la grafica 3D, funzionalità audio o di calcolo. Questo articolo fornisce una breve introduzione all'uso OpenTK in un'app xamarin. Mac.

[![](opentk-images/intro01.png "Eseguire un'app di esempio")](opentk-images/intro01.png#lightbox)

In questo articolo verranno trattati i concetti fondamentali di OpenTK in un'applicazione xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` comandi utilizzato per wireup classi c# per gli oggetti di Objective-C e gli elementi dell'interfaccia utente.

<a name="About_OpenTK" />

## <a name="about-opentk"></a>Sulle OpenTK

Come indicato in precedenza, OpenTK (il Toolkit Open) è un'avanzato di basso livello libreria c# che semplifica l'uso di OpenGL e OpenCL OpenAL. Uso OpenTK in un'app xamarin. Mac offre le funzionalità seguenti:

- **Sviluppo rapido** -OpenTK fornisce tipi di dati complessa e la documentazione online per migliorare il flusso di lavoro di scrittura del codice e rilevare gli errori più semplici e quelli precedenti.
- **Semplice integrazione** -OpenTK è stata progettata per integrare facilmente con le applicazioni .NET.
- **Licenza permissiva** -OpenTK viene distribuito con le licenze MIT/X11 ed è completamente gratuito.
- **Associazioni indipendenti dai tipi a numerose** -OpenTK supporta le versioni più recenti di OpenGL, OpenGL | ES, OpenAL e OpenCL con il caricamento di estensione automatica, la documentazione in linea e il controllo di errore.
- **Opzioni flessibili di GUI** -OpenTK offre native, finestra di gioco a prestazioni elevate progettata specificamente per giochi e xamarin. Mac.
- **Completamente gestito, codice conforme a CLS** -OpenTK supporta le versioni a 32 bit e a 64 bit di macOS senza librerie non gestite.
- **Toolkit di matematica 3D** OpenTK fornisce `Vector`, `Matrix`, `Quaternion` e `Bezier` struct tramite il Toolkit di matematica 3D.

OpenTK utilizzabile per giochi, applicazioni scientifiche o altri progetti che richiedono la grafica 3D, funzionalità audio o di calcolo.

Per altre informazioni, vedi [Toolkit Open The](http://www.opentk.com) sito Web.

<a name="OpenTK_Quickstart" />

## <a name="opentk-quickstart"></a>Guida introduttiva a OpenTK

Come una rapida introduzione all'uso OpenTK in un'app xamarin. Mac, verrà creata una semplice applicazione che si apre una visualizzazione di gioco, esegue il rendering di un semplice triangolo in tale visualizzazione e la visualizzazione di gioco attachs alla finestra principale dell'app Mac per visualizzare il triangolo per l'utente.

<a name="Starting_a_New_Project" />

### <a name="starting-a-new-project"></a>Avvia un nuovo progetto

Avviare Visual Studio per Mac e creare una nuova soluzione xamarin. Mac. Selezionare **Mac** > **App** > **generale** > **App Cocoa**:

[![](opentk-images/sample01.png "Aggiunge una nuova App Cocoa")](opentk-images/sample01.png#lightbox)

Immettere `MacOpenTK` per il **nome progetto**:

[![](opentk-images/sample02.png "Impostazione del nome del progetto")](opentk-images/sample02.png#lightbox)

Scegliere il **Create** per compilare il nuovo progetto.

<a name="Including_OpenTK" />

### <a name="including-opentk"></a>Tra cui OpenTK

Prima di utilizzare TK aperto in un'applicazione xamarin. Mac, è necessario includere un riferimento all'assembly OpenTK. Nel **Esplora soluzioni**, fare doppio clic il **riferimenti** cartella e selezionare **Modifica riferimenti...** .

Inserire un segno di spunta dal `OpenTK` e scegliere il **OK** pulsante:

[![](opentk-images/sample03.png "Modifica i riferimenti al progetto")](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK" />

### <a name="using-opentk"></a>Usando OpenTK

Con il nuovo progetto creato, fare doppio clic il `MainWindow.cs` del file nei **Esplora soluzioni** per aprirlo e modificarlo. Rendere il `MainWindow` classe aspetto simile al seguente:

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

Vediamo questo codice in dettaglio di seguito.

<a name="Required_APIs" />

### <a name="required-apis"></a>API necessarie

Per utilizzare OpenTK in una classe di xamarin. Mac sono necessario alcuni riferimenti. Abbiamo incluso quanto segue all'inizio della definizione `using` istruzioni:

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
Questo set minimo necessario per qualsiasi classe usando OpenTK.

<a name="Adding_the_Game_View" />

### <a name="adding-the-game-view"></a>Aggiunta della visualizzazione di gioco

Quindi dobbiamo creare una vista di gioco per contenere tutti l'interazione con gli OpenTK e visualizzare i risultati. È stato usato il codice seguente:

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

Qui è stata presa la visualizzazione di gioco le stesse dimensioni di finestra Mac Main e sostituita la visualizzazione contenuto della finestra con il nuovo `MonoMacGameView`. Perché è stato sostituito il contenuto della finestra esistente, la vista ha offerto sarà ridimensionato automaticamente quando viene ridimensionato il Windows principale.

<a name="Responding_to_Events" />

### <a name="responding-to-events"></a>Risposta agli eventi

Esistono numerosi eventi predefiniti che visualizza ogni gioco deve rispondere al. In questa sezione illustra gli eventi principali necessari.

<a name="The_Load_Event" />

### <a name="the-load-event"></a>L'evento di caricamento

Il `Load` evento è la posizione in cui caricare le risorse da disco, ad esempio trame, immagini o brani musicali. Per il nostro semplice, testare l'app, non viene usato il `Load` evento, ma sono inclusi come riferimento:

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event" />

### <a name="the-resize-event"></a>L'evento Resize

Il `Resize` evento deve essere chiamato ogni volta che viene ridimensionata la visualizzazione di gioco. Per l'app di esempio, è stata apportata Viewport GL le stesse dimensioni di visualizzazione Game (che viene essere automaticamente ridimensionato tramite la finestra principale di Mac) con il codice seguente:

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event" />

### <a name="the-updateframe-event"></a>L'evento UpdateFrame

Il `UpdateFrame` evento viene utilizzato per gestire l'input dell'utente, aggiornare le posizioni di oggetto, la fase fisica o i calcoli di intelligenza artificiale. Per il nostro semplice, testare l'app, non viene usato il `UpdateFrame` evento, ma sono inclusi come riferimento: 

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> L'implementazione di xamarin. Mac di OpenTK non include il `Input API`, pertanto sarà necessario usare Apple fornito supporto API per aggiungere tastiera e Mouse. Facoltativamente è possibile creare un'istanza personalizzata del `MonoMacGameView` ed eseguire l'override di `KeyDown` e `KeyUp` metodi.

<a name="The_RenderFrame_Event" />

### <a name="the-renderframe-event"></a>L'evento RenderFrame

Il `RenderFrame` evento contiene il codice che consente di eseguire il rendering (disegno) degli elementi grafici. Per l'app di esempio, si sta riempire la visualizzazione di gioco con un semplice triangolo: 

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
> Per la versione di xamarin. Mac di OpenTK **non li** chiamare le `SwapBuffers` metodo del `MonoMacGameView` istanza alla fine del codice per il rendering. Questa operazione causerà la visualizzazione di gioco a strobe rapidamente invece di visualizzare la visualizzazione sottoposta a rendering.

<a name="Running_the_Game_View" />

### <a name="running-the-game-view"></a>Esegue la visualizzazione del gioco

Con tutti gli eventi richiesti definire e la visualizzazione di gioco collegato al Mac finestra principale dell'app, si verrà letto per eseguire la vista di gioco e visualizzare i grafici. Usare il codice riportato di seguito.

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

Passiamo la frequenza dei fotogrammi desiderati che intendiamo la visualizzazione di gioco per l'aggiornamento a, per questo esempio sono stati scelti `60` fotogrammi al secondo (la stessa frequenza di aggiornamento come normale TV).

È possibile eseguire l'App nell'app e visualizzare l'output:

[![](opentk-images/intro01.png "Un esempio dell'output di App")](opentk-images/intro01.png#lightbox)

Se si ridimensiona la finestra, la visualizzazione di gioco sarà anche si trovano e triangolo verrà ridimensionato e anch'esso aggiornato in tempo reale.

<a name="Where_to_Next" />

### <a name="where-to-next"></a>Articoli successivi?

Con le nozioni di base dell'utilizzo di OpenTk in un'applicazione xamarin. Mac, ecco alcuni suggerimenti di ciò che occorre provare Avanti:

- Provare a modificare il colore del triangolo e il colore di sfondo della visualizzazione in gioco la `Load` e `RenderFrame` eventi.
- Rendere il triangolo cambiare colore quando l'utente preme un tasto nel `UpdateFrame` e `RenderFrame` eventi o rendere personalizzati `MonoMacGameView` classe ed eseguire l'override la `KeyUp` e `KeyDown` metodi.
- Rendere il triangolo di spostare sullo schermo con le chiavi di conoscenza nel `UpdateFrame` evento. Suggerimento: usare la `Matrix4.CreateTranslation` metodo per creare una matrice di traslazione e chiamare il `GL.LoadMatrix` metodo per caricarlo nel `RenderFrame` evento.
- Usare un `for` ciclo per eseguire il rendering diverse triangoli nel `RenderFrame` evento.
- Ruotare la fotocamera per offrire una vista diversa del triangolo nello spazio 3D. Suggerimento: usare la `Matrix4.CreateTranslation` metodo per creare una matrice di traslazione e chiamare il `GL.LoadMatrix` metodo per caricarlo. È anche possibile usare la `Vector2`, `Vector3`, `Vector4` e `Matrix4` classi per manipolazioni di fotocamera.

Per altri esempi, vedere la [GitHub degli esempi OpenTK](https://github.com/opentk/opentk/tree/master/Source/Examples) repository. Contiene un elenco di esempi di utilizzo OpenTK ufficiale. È possibile adattare questi esempi per l'uso con la versione di xamarin. Mac di OpenTK.

Per un esempio di xamarin. Mac più complesso di un'implementazione OpenTK, vedere la [MonoMacGameView](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/) esempio.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha richiesto un'occhiata lavora con OpenTK in un'applicazione xamarin. Mac. È stato illustrato come creare una finestra del gioco, collegare la finestra di gioco a una finestra del Mac e su come eseguire il rendering di una forma semplice nella finestra del gioco.

## <a name="related-links"></a>Collegamenti correlati

- [MacOpenTK (sample)](https://developer.xamarin.com/samples/mac/MacOpenTK/)
- [MonoMacGameView (esempio)](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Il Toolkit Open](http://www.opentk.com)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
