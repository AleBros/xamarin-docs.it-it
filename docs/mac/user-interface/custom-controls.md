---
title: Creazione di controlli personalizzati in Xamarin.Mac
description: Questo documento descrive come creare controlli personalizzati in Xamarin.Mac. Viene illustrato come compilare il controllo personalizzato, monitorarne lo stato, tracciarne l'interfaccia, rispondere all'input dell'utente e utilizzare il controllo in un'applicazione.
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 15a117ce2b0ccc84d73909eac183eeb6ea109711
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025606"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>Creazione di controlli personalizzati in Xamarin.Mac

Quando si lavora C# con e .NET in un'applicazione Xamarin.Mac, è possibile accedere agli stessi controlli utente che uno sviluppatore lavora in *Objective-C*, *Swift* e *Xcode* . Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile usare _Interface Builder_ di Xcode per creare e gestire i controlli utente oppure, facoltativamente, crearli direttamente C# nel codice.

Sebbene macOS fornisca un'ampia gamma di controlli utente incorporati, è possibile che sia necessario creare un controllo personalizzato per fornire funzionalità non predefinite o per abbinare un tema personalizzato dell'interfaccia utente, ad esempio un'interfaccia di gioco.

[![](custom-controls-images/intro01.png "Example of a custom UI control")](custom-controls-images/intro01.png#lightbox)

In questo articolo verranno illustrate le nozioni di base per la creazione di un controllo dell'interfaccia utente personalizzata riutilizzabile in un'applicazione Xamarin.Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in Questo articolo.

Si consiglia di esaminare la sezione [esporre C# classi/metodi in Objective-c](~/mac/internals/how-it-works.md) del documento [interno di Xamarin.Mac](~/mac/internals/how-it-works.md) , spiegando i comandi`Register`e`Export`usati per collegare le C# classi a Objective-c. oggetti ed elementi dell'interfaccia utente.

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>Introduzione ai controlli personalizzati

Come indicato in precedenza, in alcuni casi è necessario creare un controllo dell'interfaccia utente personalizzato riutilizzabile per fornire funzionalità univoche per l'interfaccia utente dell'app Xamarin.Mac o per creare un tema personalizzato dell'interfaccia utente, ad esempio un'interfaccia di gioco.

In queste situazioni è possibile ereditare facilmente da `NSControl` e creare uno strumento personalizzato che può essere aggiunto all'interfaccia utente dell'app tramite C# codice o tramite Interface Builder di Xcode. Ereditando da `NSControl` il controllo personalizzato avrà automaticamente tutte le funzionalità standard di un controllo dell'interfaccia utente incorporato, ad esempio `NSButton`.

Se il controllo dell'interfaccia utente personalizzata Visualizza solo le informazioni (ad esempio uno strumento grafico e grafico personalizzato), potrebbe essere necessario ereditare da `NSView` anziché `NSControl`.

Indipendentemente dalla classe di base utilizzata, i passaggi di base per la creazione di un controllo personalizzato sono gli stessi.

In questo articolo verrà creato un componente del Commuter personalizzato che fornisce un tema univoco dell'interfaccia utente e un esempio di creazione di un controllo dell'interfaccia utente personalizzata completamente funzionante.

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>Compilazione del controllo personalizzato

Poiché il controllo personalizzato che si sta creando risponderà all'input dell'utente (clic del pulsante sinistro del mouse), verrà ereditato da `NSControl`. In questo modo, il controllo personalizzato includerà automaticamente tutte le funzionalità standard che un controllo dell'interfaccia utente predefinito ha e risponderà come un controllo macOS standard.

In Visual Studio per Mac aprire il progetto Xamarin.Mac per il quale si desidera creare un controllo personalizzato dell'interfaccia utente (o crearne uno nuovo). Aggiungere una nuova classe e chiamarla `NSFlipSwitch`:

[![](custom-controls-images/custom01.png "Adding a new class")](custom-controls-images/custom01.png#lightbox)

Modificare quindi la classe `NSFlipSwitch.cs` e renderla simile alla seguente:

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

La prima cosa da notare sulla nostra classe personalizzata è che si sta ereditando da `NSControl` e usando il comando **Register** per esporre questa classe ai Interface Builder Objective-C e Xcode:

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

Nelle sezioni seguenti verranno esaminati in dettaglio il resto del codice precedente.

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>Verifica dello stato del controllo

Poiché il controllo personalizzato è un'opzione, è necessario un modo per tenere traccia dello stato di attivazione/disattivazione del Commuter. Questa procedura viene gestita con il codice seguente `NSFlipSwitch`:

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

Quando lo stato del commutatore cambia, è necessario un modo per aggiornare l'interfaccia utente. Questa operazione viene eseguita forzando il controllo per ricreare l'interfaccia utente con `NeedsDisplay = true`.

Se il controllo richiede un solo stato on/off (ad esempio, un comportatore a più Stati con 3 posizioni), è possibile che sia stata usata un' **enumerazione** per tenere traccia dello stato. Per questo esempio, viene eseguita una semplice **bool** .

È stato inoltre aggiunto un metodo helper per scambiare lo stato del commutatore tra on e off:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

In seguito, questa classe helper verrà espansa per informare il chiamante quando lo stato dei commutatori è stato modificato.

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>Disegno dell'interfaccia del controllo

Verranno usate le routine di creazione grafica di base per disegnare l'interfaccia utente del controllo personalizzato in fase di esecuzione. Prima di poter eseguire questa operazione, è necessario attivare i livelli per il controllo. Questa operazione viene eseguita con il metodo privato seguente:

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Questo metodo viene chiamato da ognuno dei costruttori del controllo per garantire che il controllo sia configurato correttamente. Esempio:

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

Successivamente, è necessario eseguire l'override del metodo `DrawRect` e aggiungere le routine grafiche di base per creare il controllo:

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

Si modificherà la rappresentazione visiva per il controllo quando viene modificato lo stato (ad esempio, passando da **on** a **off**). Ogni volta che lo stato viene modificato, è possibile usare il comando `NeedsDisplay = true` per forzare il riprogetto del controllo per il nuovo stato.

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>Risposta all'input dell'utente

Esistono due modalità di base in cui è possibile aggiungere input utente al controllo personalizzato: **eseguire l'override delle routine di gestione del mouse** o dei **Riconoscitori di movimento**. Il metodo usato sarà basato sulle funzionalità richieste dal controllo.

> [!IMPORTANT]
> Per qualsiasi controllo personalizzato creato, è necessario usare i **metodi di override** _o_ i **Riconoscitori di movimento**, ma non entrambi nello stesso momento in cui possono essere in conflitto tra loro.

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>Gestione dell'input dell'utente con i metodi di override

Gli oggetti che ereditano da `NSControl` (o `NSView`) presentano diversi metodi di override per la gestione dell'input del mouse o della tastiera. Per il controllo di esempio, si vuole capovolgere lo stato del **compartitore quando l'** utente fa **clic sul controllo** con il pulsante sinistro del mouse. Per gestire questo problema, è possibile aggiungere i metodi di override seguenti alla classe `NSFlipSwitch`:

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

Nel codice sopra riportato, viene chiamato il metodo `FlipSwitchState` (definito in precedenza) per capovolgere lo stato di attivazione/disattivazione dell'opzione nel metodo `MouseDown`. In questo modo verrà forzato il ridisegnato anche il controllo per riflettere lo stato corrente.

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>Gestione dell'input dell'utente con i riconoscitori di movimento

Facoltativamente, è possibile utilizzare i riconoscitori di movimento per gestire l'interazione dell'utente con il controllo. Rimuovere le sostituzioni aggiunte in precedenza, modificare il metodo `Initialize` e renderlo simile al seguente:

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

Qui viene creato un nuovo `NSClickGestureRecognizer` e viene chiamato il metodo `FlipSwitchState` per modificare lo stato del commutatore quando l'utente fa clic su di esso con il pulsante sinistro del mouse. Il metodo `AddGestureRecognizer (click)` aggiunge il riconoscitore di movimento al controllo.

Anche in questo caso, il metodo da usare dipende da ciò che si sta tentando di eseguire con il controllo personalizzato. Se è necessario accedere a un livello basso per l'interazione dell'utente, usare i metodi di override. Se sono necessarie funzionalità predefinite, ad esempio i clic del mouse, usare i riconoscitori di movimento.

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>Risposta agli eventi di modifica dello stato

Quando l'utente modifica lo stato del controllo personalizzato, è necessario un modo per rispondere alla modifica dello stato nel codice, ad esempio quando si fa clic su un pulsante personalizzato.

Per fornire questa funzionalità, modificare la classe `NSFlipSwitch` e aggiungere il codice seguente:

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

Modificare quindi il metodo `FlipSwitchState` e renderlo simile al seguente:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

Viene innanzitutto fornito un evento `ValueChanged` a cui è possibile aggiungere un gestore nel codice C# in modo che sia possibile eseguire un'azione quando l'utente modifica lo stato dell'opzione.

In secondo luogo, poiché il controllo personalizzato eredita da `NSControl`, ha automaticamente un' **azione** che può essere assegnata nell'Interface Builder di Xcode. Per chiamare questa **azione** quando lo stato viene modificato, viene usato il codice seguente:

```csharp
if (this.Action !=null)
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

Prima di tutto, viene verificato se un' **azione** è stata assegnata al controllo. Viene quindi chiamata l' **azione** se è stata definita.

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>Uso del controllo personalizzato

Con il controllo personalizzato completamente definito, è possibile aggiungerlo all'interfaccia utente dell'app Xamarin.Mac usando C# il codice o la Interface Builder di Xcode.

Per aggiungere il controllo tramite Interface Builder, eseguire prima una compilazione pulita del progetto Xamarin.Mac, quindi fare doppio clic sul file `Main.storyboard` per aprirlo in Interface Builder per la modifica:

[![](custom-controls-images/custom02.png "Editing the storyboard in Xcode")](custom-controls-images/custom02.png#lightbox)

Trascinare quindi un `Custom View` nella progettazione dell'interfaccia utente:

[![](custom-controls-images/custom03.png "Selecting a Custom View from the Library")](custom-controls-images/custom03.png#lightbox)

Con la visualizzazione personalizzata ancora selezionata, passare al **controllo di identità** e modificare la **classe** della visualizzazione in `NSFlipSwitch`:

[![](custom-controls-images/custom04.png "Setting the View's class")](custom-controls-images/custom04.png#lightbox)

Passare all' **editor di assistente** e creare un **Outlet** per il controllo personalizzato (assicurandosi di associarlo nel file `ViewController.h` e non nel file di `.m`):

[![](custom-controls-images/custom05.png "Configuring a new Outlet")](custom-controls-images/custom05.png#lightbox)

Salvare le modifiche, tornare a Visual Studio per Mac e consentire la sincronizzazione delle modifiche. Modificare il file di `ViewController.cs` e rendere il `ViewDidLoad` metodo simile al seguente:

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

In questo esempio si risponde all'evento `ValueChanged` definito in precedenza sulla classe `NSFlipSwitch` e si scrive il **valore** corrente quando l'utente fa clic sul controllo.

Facoltativamente, è possibile tornare a Interface Builder e definire un' **azione** sul controllo:

[![](custom-controls-images/custom06.png "Configuring a new Action")](custom-controls-images/custom06.png#lightbox)

Modificare di nuovo il file di `ViewController.cs` e aggiungere il metodo seguente:

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> È consigliabile usare l' **evento** o definire un' **azione** in Interface Builder, ma è consigliabile non usare entrambi i metodi contemporaneamente oppure è possibile che si verifichino conflitti tra loro.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in dettaglio la creazione di un controllo dell'interfaccia utente personalizzata riutilizzabile in un'applicazione Xamarin.Mac. È stato illustrato come creare l'interfaccia utente dei controlli personalizzati, i due modi principali per rispondere all'input del mouse e dell'utente e come esporre il nuovo controllo alle azioni nella Interface Builder di Xcode.

## <a name="related-links"></a>Collegamenti correlati

- [MacCustomControl (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccustomcontrol)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Gestione degli eventi del mouse](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
