---
title: Creazione di controlli personalizzati in Novell. Mac
description: Questo documento descrive come creare controlli personalizzati in Novell. Mac. Viene illustrato come compilare il controllo personalizzato, monitorarne lo stato, tracciarne l'interfaccia, rispondere all'input dell'utente e utilizzare il controllo in un'applicazione.
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: aee3d81375ab619fa2016a87951cce3e72cdbe47
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650187"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>Creazione di controlli personalizzati in Novell. Mac

Quando si lavora C# con e .NET in un'applicazione Novell. Mac, è possibile accedere agli stessi controlli utente che uno sviluppatore lavora in *Objective-C*, *Swift* e *Xcode* . Poiché Novell. Mac si integra direttamente con Xcode, è possibile usare _Interface Builder_ di Xcode per creare e gestire i controlli utente oppure, facoltativamente, crearli direttamente C# nel codice.

Sebbene macOS fornisca un'ampia gamma di controlli utente incorporati, è possibile che sia necessario creare un controllo personalizzato per fornire funzionalità non predefinite o per abbinare un tema personalizzato dell'interfaccia utente, ad esempio un'interfaccia di gioco.

[![](custom-controls-images/intro01.png "Esempio di controllo personalizzato dell'interfaccia utente")](custom-controls-images/intro01.png#lightbox)

In questo articolo verranno illustrate le nozioni di base per la creazione di un controllo dell'interfaccia utente personalizzata riutilizzabile in un'applicazione Novell. Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in Questo articolo.

Si consiglia di esaminare la sezione [ C# esporre classi/metodi in Objective-C](~/mac/internals/how-it-works.md) del documento [interno di Novell. Mac](~/mac/internals/how-it-works.md) , spiegando `Register` i comandi e `Export` usati per collegare le C# classi a. Oggetti Objective-C ed elementi dell'interfaccia utente.

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>Introduzione ai controlli personalizzati

Come indicato in precedenza, in alcuni casi è necessario creare un controllo dell'interfaccia utente personalizzato riutilizzabile per fornire funzionalità univoche per l'interfaccia utente dell'app Novell. Mac o per creare un tema personalizzato dell'interfaccia utente, ad esempio un'interfaccia di gioco.

In queste situazioni è possibile ereditare facilmente da `NSControl` e creare uno strumento personalizzato che può essere aggiunto all'interfaccia utente dell'app tramite C# codice o tramite la Interface Builder di Xcode. Ereditando dal `NSControl` controllo personalizzato verranno automaticamente incluse tutte le funzionalità standard di un controllo dell'interfaccia utente incorporato ( `NSButton`ad esempio).

Se il controllo dell'interfaccia utente personalizzata Visualizza solo le informazioni (ad esempio uno strumento grafico e grafico personalizzato), è possibile che si `NSView` desideri ereditare da `NSControl`anziché da.

Indipendentemente dalla classe di base utilizzata, i passaggi di base per la creazione di un controllo personalizzato sono gli stessi.

In questo articolo verrà creato un componente del Commuter personalizzato che fornisce un tema univoco dell'interfaccia utente e un esempio di creazione di un controllo dell'interfaccia utente personalizzata completamente funzionante.

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>Compilazione del controllo personalizzato

Poiché il controllo personalizzato che si sta creando risponderà all'input dell'utente (clic con il pulsante sinistro del mouse), verrà ereditata da `NSControl`. In questo modo, il controllo personalizzato includerà automaticamente tutte le funzionalità standard che un controllo dell'interfaccia utente predefinito ha e risponderà come un controllo macOS standard.

In Visual Studio per Mac aprire il progetto Novell. Mac per il quale si desidera creare un controllo personalizzato dell'interfaccia utente (o crearne uno nuovo). Aggiungere una nuova classe e chiamarla `NSFlipSwitch`:

[![](custom-controls-images/custom01.png "Aggiunta di una nuova classe")](custom-controls-images/custom01.png#lightbox)

Modificare quindi la `NSFlipSwitch.cs` classe e renderla simile alla seguente:

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

La prima cosa da notare sulla nostra classe personalizzata è che si sta ereditando da `NSControl` e usando il comando **Register** per esporre questa classe ai Interface Builder di Objective-C e Xcode:

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

Nelle sezioni seguenti verranno esaminati in dettaglio il resto del codice precedente.

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>Verifica dello stato del controllo

Poiché il controllo personalizzato è un'opzione, è necessario un modo per tenere traccia dello stato di attivazione/disattivazione del Commuter. Questa procedura viene gestita con il codice seguente `NSFlipSwitch`in:

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

Quando lo stato del commutatore cambia, è necessario un modo per aggiornare l'interfaccia utente. Questa operazione viene eseguita forzando il controllo per ricreare l'interfaccia utente `NeedsDisplay = true`con.

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

Questo metodo viene chiamato da ognuno dei costruttori del controllo per garantire che il controllo sia configurato correttamente. Ad esempio:

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

Successivamente, è necessario eseguire l'override `DrawRect` del metodo e aggiungere le routine grafiche di base per creare il controllo:

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

Si modificherà la rappresentazione visiva per il controllo quando viene modificato lo stato (ad esempio, passando da **on** a **off**). Ogni volta che lo stato viene modificato, è possibile `NeedsDisplay = true` usare il comando per forzare il riprogetto del controllo per il nuovo stato.

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>Risposta all'input dell'utente

Esistono due modalità di base in cui è possibile aggiungere input utente al controllo personalizzato: **Eseguire l'override delle routine di gestione del mouse** o dei riconoscitori di **movimento**. Il metodo usato sarà basato sulle funzionalità richieste dal controllo.

> [!IMPORTANT]
> Per qualsiasi controllo personalizzato creato, è necessario usare i **metodi di override** _o_ i riconoscitori di **movimento**, ma non entrambi nello stesso momento in cui possono essere in conflitto tra loro.

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>Gestione dell'input dell'utente con i metodi di override

Gli oggetti che ereditano da `NSView`(o) dispongono di diversi metodi di override per la gestione dell'input del mouse o della `NSControl` tastiera. Per il controllo di esempio, si vuole capovolgere lo stato del compartitore quando **l'** utente fa clic sul controllo con il pulsante sinistro del mouse. Per gestire questo problema, è possibile aggiungere i `NSFlipSwitch` seguenti metodi di override alla classe:

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

Nel codice sopra riportato, viene chiamato il `FlipSwitchState` metodo (definito in precedenza) per capovolgere lo stato di attivazione/disattivazione `MouseDown` dell'opzione nel metodo. In questo modo verrà forzato il ridisegnato anche il controllo per riflettere lo stato corrente.

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>Gestione dell'input dell'utente con i riconoscitori di movimento

Facoltativamente, è possibile utilizzare i riconoscitori di movimento per gestire l'interazione dell'utente con il controllo. Rimuovere le sostituzioni aggiunte in precedenza, `Initialize` modificare il metodo e renderlo simile al seguente:

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

Qui viene creato un nuovo `NSClickGestureRecognizer` oggetto e viene `FlipSwitchState` chiamato il metodo per modificare lo stato del commutatore quando l'utente fa clic su di esso con il pulsante sinistro del mouse. Il `AddGestureRecognizer (click)` metodo aggiunge il riconoscitore di movimento al controllo.

Anche in questo caso, il metodo da usare dipende da ciò che si sta tentando di eseguire con il controllo personalizzato. Se è necessario accedere a un livello basso per l'interazione dell'utente, usare i metodi di override. Se sono necessarie funzionalità predefinite, ad esempio i clic del mouse, usare i riconoscitori di movimento.

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>Risposta agli eventi di modifica dello stato

Quando l'utente modifica lo stato del controllo personalizzato, è necessario un modo per rispondere alla modifica dello stato nel codice, ad esempio quando si fa clic su un pulsante personalizzato. 

Per fornire questa funzionalità, modificare la `NSFlipSwitch` classe e aggiungere il codice seguente:

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

Modificare quindi il `FlipSwitchState` metodo e renderlo simile al seguente:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

In primo luogo, viene `ValueChanged` fornito un evento in cui è possibile aggiungere un C# gestore a nel codice in modo che sia possibile eseguire un'azione quando l'utente modifica lo stato dell'opzione.

In secondo luogo, poiché il controllo personalizzato `NSControl`eredita da, ha automaticamente un' **azione** che può essere assegnata nel Interface Builder di Xcode. Per chiamare questa **azione** quando lo stato viene modificato, viene usato il codice seguente:

```csharp
if (this.Action !=null) 
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

Prima di tutto, viene verificato se un' **azione** è stata assegnata al controllo. Viene quindi chiamata l' **azione** se è stata definita.

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>Uso del controllo personalizzato

Con il controllo personalizzato completamente definito, è possibile aggiungerlo all'interfaccia utente dell'app Novell. Mac usando C# il codice o la Interface Builder di Xcode.

Per aggiungere il controllo usando Interface Builder, eseguire prima di tutto una compilazione pulita del progetto Novell. Mac, quindi fare doppio clic `Main.storyboard` sul file per aprirlo in Interface Builder per la modifica:

[![](custom-controls-images/custom02.png "Modifica dello storyboard in Xcode")](custom-controls-images/custom02.png#lightbox)

Trascinare quindi un oggetto `Custom View` nella struttura dell'interfaccia utente:

[![](custom-controls-images/custom03.png "Selezione di una visualizzazione personalizzata dalla libreria")](custom-controls-images/custom03.png#lightbox)

Con la visualizzazione personalizzata ancora selezionata, passare al **controllo di identità** e modificare la **classe** della visualizzazione in `NSFlipSwitch`:

[![](custom-controls-images/custom04.png "Impostazione della classe della visualizzazione")](custom-controls-images/custom04.png#lightbox)

Passare all' **editor di assistente** e creare un **Outlet** per il controllo personalizzato (assicurandosi di associarlo nel `ViewController.h` file e non nel `.m` file):

[![](custom-controls-images/custom05.png "Configurazione di un nuovo outlet")](custom-controls-images/custom05.png#lightbox)

Salvare le modifiche, tornare a Visual Studio per Mac e consentire la sincronizzazione delle modifiche. Modificare il `ViewController.cs` file e fare in `ViewDidLoad` modo che il metodo sia simile al seguente:

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

Qui si risponde all' `ValueChanged` evento definito `NSFlipSwitch` in precedenza sulla classe e si scrive il **valore** corrente quando l'utente fa clic sul controllo.

Facoltativamente, è possibile tornare a Interface Builder e definire un' **azione** sul controllo:

[![](custom-controls-images/custom06.png "Configurazione di una nuova azione")](custom-controls-images/custom06.png#lightbox)

Modificare nuovamente il `ViewController.cs` file e aggiungere il metodo seguente:

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

Questo articolo ha esaminato in dettaglio la creazione di un controllo dell'interfaccia utente personalizzata riutilizzabile in un'applicazione Novell. Mac. È stato illustrato come creare l'interfaccia utente dei controlli personalizzati, i due modi principali per rispondere all'input del mouse e dell'utente e come esporre il nuovo controllo alle azioni nella Interface Builder di Xcode.

## <a name="related-links"></a>Collegamenti correlati

- [MacCustomControl (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccustomcontrol)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Gestione degli eventi del mouse](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
