---
title: Creazione di controlli personalizzati in xamarin. Mac
description: Questo documento descrive come creare controlli personalizzati in xamarin. Mac. Viene illustrato come compilare il controllo personalizzato, monitorare lo stato, disegna l'interfaccia, rispondere all'input dell'utente e usare il controllo in un'applicazione.
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 015c1e315b6070777542a8f8c5871c00cf336b5c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61236191"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>Creazione di controlli personalizzati in xamarin. Mac

Quando si lavora con C# e .NET in un'applicazione xamarin. Mac, è possibile utilizzare lo stesso account utente consente di controllare che gli sviluppatori che lavorano *Objective-C*, *Swift* e *Xcode*viene. Poiché xamarin. Mac si integra direttamente con Xcode, è possibile usare Xcode _Interface Builder_ per creare e gestire i propri controlli utente (oppure crearle direttamente nel codice c#).

Anche se macOS offre un'ampia gamma di controlli utente incorporati, potrebbero esserci di volte in cui è necessario creare un controllo personalizzato per fornire funzionalità fornita non out-of-the-box o in modo che corrisponda un tema dell'interfaccia utente personalizzato (ad esempio, un'interfaccia del gioco).

[![](custom-controls-images/intro01.png "Esempio di controllo dell'interfaccia utente personalizzato")](custom-controls-images/intro01.png#lightbox)

In questo articolo, si affronterà le nozioni di base della creazione di un controllo di interfaccia utente personalizzata riutilizzabile in un'applicazione xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` comandi utilizzato per wireup classi c# per gli oggetti di Objective-C e gli elementi dell'interfaccia utente.

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>Introduzione ai controlli personalizzati

Come indicato in precedenza, potrebbero esserci casi è necessario creare un riutilizzabile, controllo dell'interfaccia utente personalizzata per fornire funzionalità univoche per l'interfaccia utente dell'app xamarin. Mac o per creare un tema dell'interfaccia utente personalizzato (ad esempio, un'interfaccia del gioco).

In queste situazioni, è facilmente possibile ereditarle da `NSControl` e creare uno strumento personalizzato che può essere aggiunta all'interfaccia utente dell'app tramite il codice c# o Interface Builder di Xcode. Tramite l'eredità dalla `NSControl` del controllo personalizzato avranno automaticamente tutte le funzionalità standard che dispone di un controllo dell'interfaccia utente incorporati (ad esempio `NSButton`).

Se il controllo personalizzato di interfaccia utente visualizza solo informazioni (ad esempio, una creazione di grafici personalizzati e uno strumento grafico), è possibile da cui ereditare `NSView` invece di `NSControl`.

Indipendentemente da quale classe di base viene usato, i passaggi di base per la creazione di un controllo personalizzato è lo stesso.

In questo articolo verrà, creare un componente personalizzato di capovolgere commutatore che fornisce un tema dell'interfaccia utente univoco e un esempio di creazione di un controllo di interfaccia utente personalizzata completamente funzionale.

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>Compilazione del controllo personalizzato

Poiché il controllo personalizzato che stiamo creando risponderà all'input dell'utente (clic del pulsante sinistro del mouse), si intende ereditare da `NSControl`. In questo modo, il controllo personalizzato verrà automaticamente dispone di tutte le funzionalità standard che dispone di un controllo dell'interfaccia utente incorporati e funzionano come un controllo standard macOS.

In Visual Studio per Mac, aprire il progetto xamarin. Mac che si desidera creare un controllo dell'interfaccia utente personalizzata per (o crearne uno nuovo). Aggiungere una nuova classe e chiamarlo `NSFlipSwitch`:

[![](custom-controls-images/custom01.png "Aggiunta di una nuova classe")](custom-controls-images/custom01.png#lightbox)

Modificare quindi il `NSFlipSwitch.cs` classe e renderlo simile al seguente:

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using AppKit;
using CoreGraphics;

namespace MacCustomControl
{
    [Register("NSFlipSwitch")]
    public class NSFlipSwitch : NSControl
    {
        #region Private Variables
        private bool _value = false;
        #endregion

        #region Computed Properties
        public bool Value {
            get { return _value; }
            set {
                // Save value and force a redraw
                _value = value;
                NeedsDisplay = true;
            }
        }
        #endregion

        #region Constructors
        public NSFlipSwitch ()
        {
            // Init
            Initialize();
        }

        public NSFlipSwitch (IntPtr handle) : base (handle)
        {
            // Init
            Initialize();
        }

        [Export ("initWithFrame:")]
        public NSFlipSwitch (CGRect frameRect) : base(frameRect) {
            // Init
            Initialize();
        }

        private void Initialize() {
            this.WantsLayer = true;
            this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
        }
        #endregion

        #region Draw Methods
        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Use Core Graphic routines to draw our UI
            ...

        }
        #endregion

        #region Private Methods
        private void FlipSwitchState() {
            // Update state
            Value = !Value;
        }
        #endregion

    }
}
```

La prima cosa da notare in questa classe personalizzata che si sta che eredita da `NSControl` e usando il **registrare** comando per espone questa classe per Objective-C e Xcode Interface Builder:

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

Nelle sezioni seguenti, prenderemo in esame il resto del codice precedente in modo dettagliato.

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>Tenere traccia dello stato del controllo

Poiché il controllo personalizzato è un'opzione, è necessario un modo per tenere traccia dello stato On/Off dell'opzione. Gestiamo che con il codice seguente in `NSFlipSwitch`:

```csharp
private bool _value = false;
...

public bool Value {
    get { return _value; }
    set {
        // Save value and force a redraw
        _value = value;
        NeedsDisplay = true;
    }
}
```

Quando viene modificato lo stato del commutatore, è necessario aggiornare l'interfaccia utente. Ciò è possibile imporre il controllo per ridisegnare relativa interfaccia utente con `NeedsDisplay = true`.

Se il controllo richiesto più che un singolo stato (ad esempio un'opzione più stato con 3 posizioni) attivato/disattivato, si sarebbe potuto usare un **Enum** per tenere traccia dello stato. Per questo esempio, una semplice **bool** eseguirà.

Abbiamo anche aggiunto un metodo helper per scambiare lo stato dell'interruttore tra e disattivare:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

Verranno espansi in un secondo momento, questa classe helper per informare il chiamante quando è stato modificato lo stato cambi.

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>Creazione dell'interfaccia del controllo

Si intende usare routine disegnare grafico Core per disegnare di interfaccia il controllo personalizzato utente in fase di esecuzione. Prima è possibile farlo, è necessario attivare i livelli per il controllo. Facciamo con il metodo privato seguente:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Questo metodo viene chiamato da ognuno dei costruttori del controllo per assicurarsi che il controllo sia configurato correttamente. Ad esempio:

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

Successivamente, è necessario eseguire l'override di `DrawRect` (metodo) e aggiungere le routine per disegnare il controllo grafico di base:

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

Si sarà modificando la rappresentazione visiva del controllo quando il relativo stato cambia (, ad esempio dal **sul** al **Off**). Ogni volta che i cambiamenti di stato, è possibile usare il `NeedsDisplay = true` comando per forzare il ridisegno per il nuovo stato del controllo.

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>Risponde all'Input dell'utente

Esistono due modalità di base che possiamo aggiungere input dell'utente per il controllo personalizzato: **Eseguire l'override di gestione di routine del Mouse** oppure **riconoscitori di movimento**. Quale metodo viene usato, si baseranno sulle funzionalità necessarie per il controllo.

> [!IMPORTANT]
> Per qualsiasi controllo personalizzato creato, è consigliabile usare **eseguire l'Override di metodi** _o_ **i riconoscitori di movimento**, ma non entrambi allo stesso tempo come possono entrare in conflitto tra loro.

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>Gestione dell'Input dell'utente con i metodi di Override

Gli oggetti da cui ereditare `NSControl` (o `NSView`) invece avere diversi l'override di metodi per la gestione mouse o i tasti di input. Per il controllo di esempio, si desidera invertire lo stato dell'opzione tra **sul** e **Off** quando l'utente fa clic sul controllo con il pulsante sinistro del mouse. Possiamo aggiungere quanto segue l'override di metodi per il `NSFlipSwitch` classe per gestire questa situazione:

```csharp
#region Mouse Handling Methods
// --------------------------------------------------------------------------------
// Handle mouse with Override Methods.
// NOTE: Use either this method or Gesture Recognizers, NOT both!
// --------------------------------------------------------------------------------
public override void MouseDown (NSEvent theEvent)
{
    base.MouseDown (theEvent);

    FlipSwitchState ();
}

public override void MouseDragged (NSEvent theEvent)
{
    base.MouseDragged (theEvent);
}

public override void MouseUp (NSEvent theEvent)
{
    base.MouseUp (theEvent);
}

public override void MouseMoved (NSEvent theEvent)
{
    base.MouseMoved (theEvent);
}
## endregion
```

Nel codice precedente, chiamiamo il `FlipSwitchState` metodo (definito in precedenza) per capovolgere di attivazione/disattivazione dello stato del commutatore nel `MouseDown` (metodo). Anche questa operazione forzerà il controllo deve avvenire in modo da riflettere lo stato corrente.

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>Gestione dell'Input dell'utente con i riconoscitori di movimento

Facoltativamente, è possibile utilizzare i riconoscitori di movimento per gestire l'utente interagisce con il controllo. Rimuovere le sostituzioni aggiunte in precedenza, modificare il `Initialize` (metodo) e renderlo simile al seguente:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;

    // --------------------------------------------------------------------------------
    // Handle mouse with Gesture Recognizers.
    // NOTE: Use either this method or the Override Methods, NOT both!
    // --------------------------------------------------------------------------------
    var click = new NSClickGestureRecognizer (() => {
        FlipSwitchState();
    });
    AddGestureRecognizer (click);
}
```

In questo caso, verrà creato un nuovo `NSClickGestureRecognizer` e la chiamata a nostro `FlipSwitchState` metodo per modificare lo stato dell'interruttore quando l'utente fa clic su di esso con il pulsante sinistro del mouse. Il `AddGestureRecognizer (click)` metodo aggiunge il riconoscitore di movimento al controllo.

Anche in questo caso, quale metodo utilizzare dipende cosa si sta provando a eseguire con il controllo personalizzato. Se è necessario l'accesso a livello basso all'interazione dell'utente, usare i metodi di eseguire l'Override. Se si necessita di funzionalità predefinite, ad esempio clic del mouse, usare i riconoscitori di movimento.

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>Risponde a eventi di modifica di stato

Quando l'utente modifica lo stato del controllo personalizzato, è necessario un modo per rispondere alla modifica dello stato nel codice (ad esempio eseguendo un'operazione simile quando si fa clic su un pulsante personalizzato). 

Per fornire questa funzionalità, modificare il `NSFlipSwitch` classe e aggiungere il codice seguente:

```csharp
#region Events
public event EventHandler ValueChanged;

internal void RaiseValueChanged() {
    if (this.ValueChanged != null)
        this.ValueChanged (this, EventArgs.Empty);

    // Perform any action bound to the control from Interface Builder
    // via an Action.
    if (this.Action !=null) 
        NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
}
## endregion
```

Modificare quindi il `FlipSwitchState` (metodo) e renderlo simile al seguente:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

In primo luogo, offriamo un `ValueChanged` eventi che aggiungiamo un gestore di codice c# in modo che è possibile eseguire un'azione quando l'utente modifica lo stato dell'interruttore.

In secondo luogo, poiché il controllo personalizzato eredita da `NSControl`, dispone automaticamente un **azione** che può essere assegnato in Interface Builder di Xcode. Chiameremo **azione** quando lo stato viene modificato, utilizziamo il codice seguente:

```csharp
if (this.Action !=null) 
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

Innanzitutto, occorre verificare la presenza di un' **azione** assegnato al controllo. Quindi, chiamiamo il **azione** se è stata definita.

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>Uso del controllo personalizzato

Con il controllo personalizzato definito completamente, è possibile aggiungere, all'interfaccia utente dell'app xamarin. Mac usando codice c# o in Interface Builder di Xcode.

Per aggiungere il controllo usando Interface Builder, prima di tutto eseguire una compilazione pulita del progetto xamarin. Mac, quindi fare doppio clic il `Main.storyboard` file per aprirlo in Interface Builder per la modifica:

[![](custom-controls-images/custom02.png "Modifica lo storyboard in Xcode")](custom-controls-images/custom02.png#lightbox)

Procedere quindi trascinando un `Custom View` nella progettazione dell'interfaccia utente:

[![](custom-controls-images/custom03.png "Selezione di una visualizzazione personalizzata dalla raccolta")](custom-controls-images/custom03.png#lightbox)

Con la vista personalizzata ancora selezionato, passare al **Identity Inspector** e passare alla visualizzazione **classe** a `NSFlipSwitch`:

[![](custom-controls-images/custom04.png "Setting-classe della visualizzazione")](custom-controls-images/custom04.png#lightbox)

Passare al **Assistente dell'Editor** e creare un **Outlet** per il controllo personalizzato (assicurandosi di eseguirne l'associazione nel `ViewController.h` file e non il `.m` file):

[![](custom-controls-images/custom05.png "Configurazione di un nuovo Negozio")](custom-controls-images/custom05.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac e consentire le modifiche per la sincronizzazione. Modificare il `ViewController.cs` file e apportare le `ViewDidLoad` metodo aspetto simile al seguente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Do any additional setup after loading the view.
    OptionTwo.ValueChanged += (sender, e) => {
        // Display the state of the option switch
        Console.WriteLine("Option Two: {0}", OptionTwo.Value);
    };
}
``` 

In questo caso, Microsoft risponde al `ValueChanged` evento è definiti in precedenza nel `NSFlipSwitch` classe e si scriverà corrente **valore** quando l'utente fa clic sul controllo.

Facoltativamente, è possibile tornare al Interface Builder e definire un **azione** sul controllo:

[![](custom-controls-images/custom06.png "Configurazione di una nuova azione")](custom-controls-images/custom06.png#lightbox)

Anche in questo caso, modificare il `ViewController.cs` file e aggiungere il metodo seguente:

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> È consigliabile usare la **evento** o definire un' **azione** in Interface Builder, ma non è consigliabile usare entrambi i metodi nello stesso momento o possono entrare in conflitto tra loro.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di creazione di un controllo di interfaccia utente personalizzata riutilizzabile in un'applicazione xamarin. Mac. Abbiamo visto come disegnare i controlli personalizzati dell'interfaccia utente, i due modi principali per rispondere all'input dell'utente e del mouse e su come esporre il nuovo controllo alle azioni in Interface Builder di Xcode.

## <a name="related-links"></a>Collegamenti correlati

- [MacCustomControl (esempio)](https://developer.xamarin.com/samples/mac/MacCustomControl/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Gestione degli eventi del Mouse](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
