---
title: Creare controlli personalizzati
description: In questo articolo viene descritto come creare controlli personalizzati e utilizzarli in Generatore di interfaccia.
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: e1ab3977df96e241fa2a5a80f6cabd74d7d775f8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="creating-custom-controls"></a>Creazione di controlli personalizzati

_In questo articolo viene descritto come creare controlli personalizzati e utilizzarli in Generatore di interfaccia._

Quando si utilizza c# e .NET in un'applicazione Xamarin.Mac, è possibile accedere allo stesso utente controlla che uno sviluppatore che lavora *Objective-C*, *Swift* e *Xcode* does . Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile utilizzare del Xcode _generatore interfaccia_ per creare e gestire i controlli utente (o, facoltativamente, crearli direttamente nel codice c#).

MacOS offre un'ampia gamma di controlli utente predefiniti, potrebbero esserci di volte in cui è necessario creare un controllo personalizzato per fornire funzionalità fornita non di dialogo o un tema personalizzato dell'interfaccia utente (ad esempio, un'interfaccia di gioco).

[![](custom-controls-images/intro01.png "Esempio di controllo dell'interfaccia utente personalizzato")](custom-controls-images/intro01.png#lightbox)

In questo articolo verranno descritte le nozioni di base della creazione di un controllo di interfaccia utente personalizzata riutilizzabile in un'applicazione Xamarin.Mac. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

Si consiglia di esaminare il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` comandi utilizzato per le classi c# in transito-fino a oggetti Objective-C e gli elementi dell'interfaccia utente.

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>Introduzione ai controlli personalizzati

Come descritto in precedenza, potrebbe esserci casi è necessario creare un riutilizzabile, controllo dell'interfaccia utente personalizzato per fornire funzionalità univoche per l'interfaccia utente dell'applicazione Xamarin.Mac o per creare un tema dell'interfaccia utente personalizzato (ad esempio, un'interfaccia di gioco).

In questi casi, è possibile ereditare facilmente da `NSControl` e creare uno strumento personalizzato che può essere aggiunto all'interfaccia utente dell'applicazione tramite codice c# o generatore del Xcode di interfaccia. Tramite l'eredità da `NSControl` controllo personalizzato avranno automaticamente tutte le funzionalità standard che dispone di un controllo incorporato di interfaccia utente (ad esempio `NSButton`).

Se il controllo dell'interfaccia utente personalizzato visualizza solo informazioni (ad esempio, la creazione di grafici personalizzati e strumento grafico), si desidera ereditare `NSView` anziché `NSControl`.

Indipendentemente da quale classe di base viene utilizzato, i passaggi di base per la creazione di un controllo personalizzato è lo stesso.

In questo articolo vengono, creare un componente personalizzato di capovolgere commutatore che fornisce un tema di interfaccia utente univoco e un esempio di creazione di un controllo di interfaccia utente personalizzata completamente funzionale.

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>Generazione del controllo personalizzato

Poiché il controllo personalizzato che si sta creando una risponderà all'input dell'utente (clic del pulsante sinistro del mouse), è possibile passare da cui ereditare `NSControl`. In questo modo, il controllo personalizzato verrà automaticamente dispone di tutte le funzionalità standard che dispone di un controllo incorporato di interfaccia utente e rispondere simile a un controllo standard macOS.

In Visual Studio per Mac, aprire il progetto Xamarin.Mac che si desidera creare un controllo di interfaccia utente personalizzata per (o crearne uno nuovo). Aggiungere una nuova classe e chiamarlo `NSFlipSwitch`:

[![](custom-controls-images/custom01.png "Aggiunta di una nuova classe")](custom-controls-images/custom01.png#lightbox)

Successivamente, modificare il `NSFlipSwitch.cs` classe e renderlo simile al seguente:

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

La prima cosa da notare la classe personalizzata che si sta ereditando dalla `NSControl` e l'utilizzo di **registrare** comando esporre questa classe per Objective-C e del Xcode generatore di interfaccia:

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

Nelle sezioni seguenti, si verrà esaminato un il resto del codice sopra riportato in modo dettagliato.

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>Tenere traccia dello stato del controllo

Poiché il controllo personalizzato è un'opzione, è necessario un modo per tenere traccia dello stato On/Off del commutatore. Viene gestito che con il codice seguente in `NSFlipSwitch`:

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

Quando viene modificato lo stato dell'opzione, è necessario aggiornare l'interfaccia utente. Facciamo forzando il nuovo disegno relativa interfaccia utente con controllo `NeedsDisplay = true`.

Se il controllo richiesto superiore a quella un singolo stato (ad esempio un commutatore più stato con 3 posizioni) attivato/disattivato, avremmo potuto utilizzare un **Enum** per tenere traccia dello stato. Per questo esempio, una semplice **bool** eseguirà.

È stato aggiunto anche un metodo di supporto per scambiare lo stato del commutatore tra attivare e disattivare:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

In un secondo momento, ci sarà espandere questa classe helper per informare il chiamante quando è stato modificato lo stato di commutatori.

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>Creazione dell'interfaccia del controllo

È possibile passare a utilizzare le routine di disegno grafico Core per disegnare di interfaccia il controllo personalizzato utente in fase di esecuzione. È possibile farlo, è necessario attivare i livelli del controllo. Facciamo con il metodo privato seguente:

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

Si sarà regolando la rappresentazione visiva per il controllo quando il relativo stato cambia (ad esempio passando da **su** a **Off**). Ogni volta che i cambiamenti di stato, è possibile utilizzare il `NeedsDisplay = true` comando per forzare il ridisegno per il nuovo stato del controllo.

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>Risponde all'Input dell'utente

Esistono due modalità di base che è possibile aggiungere l'input dell'utente per il controllo personalizzato: **eseguire l'Override di routine di gestione del Mouse** o **i riconoscitori di movimento**. Quale metodo utilizzare, si baserà la funzionalità richiesta da questo controllo.

> [!IMPORTANT]
> Per qualsiasi controllo personalizzato creato, è necessario utilizzare **eseguire l'Override di metodi** _o_ **i riconoscitori di movimento**, ma non entrambi allo stesso tempo possono entrare in conflitto tra loro.

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>Gestione dell'Input dell'utente con i metodi di Override

Gli oggetti che ereditano da `NSControl` (o `NSView`) dispone di più eseguire l'override di metodi per la gestione mouse o i tasti di input. Per il controllo di esempio, si desidera invertire lo stato del commutatore tra **su** e **Off** quando l'utente fa clic sul controllo con il pulsante sinistro del mouse. È possibile aggiungere i seguenti metodi per eseguire l'override di `NSFliwSwitch` classe per gestire questo:

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

Nel codice precedente, viene chiamato il `FlipSwitchState` (definito in precedenza) per invertire On/Off lo stato dell'opzione nel metodo il `MouseDown` metodo. Anche questa operazione forzerà il controllo deve avvenire in modo da riflettere lo stato corrente.

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>Gestione dell'Input dell'utente con i riconoscitori di movimento

Facoltativamente, è possibile utilizzare i riconoscitori di movimento per gestire l'utente interagisce con il controllo. Rimuovere le sostituzioni aggiunte in precedenza, modificare il `Initialize` metodo e renderlo simile al seguente:

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

In questo caso, verrà creato un nuovo `NSClickGestureRecognizer` e chiamando il nostro `FlipSwitchState` metodo per modificare lo stato dell'interruttore quando l'utente fa clic su di esso con il pulsante sinistro del mouse. Il `AddGestureRecognizer (click)` metodo aggiunge il riconoscitore di movimento al controllo.

Nuovamente, quale metodo utilizzare dipende ciò che si sta tentando di eseguire il controllo personalizzato. Se è necessario l'accesso a livello basso all'interazione dell'utente, utilizzare i metodi di Override. Se è necessaria la funzionalità predefinite, ad esempio clic del mouse, utilizzare i riconoscitori di movimento.

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>Risponde a eventi di modifica stato

Quando l'utente modifica lo stato di questo controllo personalizzato, è necessario un modo per rispondere alla modifica dello stato nel codice (ad esempio eseguendo un'operazione quando si fa clic su un pulsante personalizzato). 

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

Successivamente, modificare il `FlipSwitchState` metodo e renderlo simile al seguente:

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

In primo luogo, vengono forniti un `ValueChanged` eventi che è possibile aggiungere un gestore nel codice c# in modo che è possibile eseguire un'azione quando l'utente modifica lo stato del commutatore.

In secondo luogo, poiché il controllo personalizzato eredita da `NSControl`, dispone automaticamente un **azione** che può essere assegnato in Generatore del Xcode di interfaccia. Per chiamare questa **azione** quando lo stato cambia, è il codice seguente:

```csharp
if (this.Action !=null) 
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

Innanzitutto, verifica per controllare se un **azione** è stato assegnato al controllo. Successivamente, viene chiamato il **azione** se è stata definita.

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>Utilizzo del controllo personalizzato

Con il controllo personalizzato definito completamente, è possibile aggiungere, all'interfaccia utente dell'applicazione Xamarin.Mac usando codice c# o in Generatore del Xcode di interfaccia.

Per aggiungere il controllo utilizzando il generatore di interfaccia, dapprima eseguire una compilazione pulita del progetto Xamarin.Mac, quindi fare doppio clic su di `Main.storyboard` file per aprirlo in Generatore di interfaccia per la modifica:

[![](custom-controls-images/custom02.png "Modifica lo storyboard in Xcode")](custom-controls-images/custom02.png#lightbox)

Successivamente, trascinare un `Custom View` la progettazione dell'interfaccia utente:

[![](custom-controls-images/custom03.png "Selezione di una visualizzazione personalizzata dalla libreria")](custom-controls-images/custom03.png#lightbox)

Con la vista personalizzata ancora selezionato, attivare il **controllo di identità** e modificare la visualizzazione **classe** a `NSFlipSwitch`:

[![](custom-controls-images/custom04.png "Impostare la classe della visualizzazione")](custom-controls-images/custom04.png#lightbox)

Passare il **Assistente Editor** e creare un **presa** per il controllo personalizzato (assicurandosi di associarlo nel `ViewControler.h` file e non il `.m` file):

[![](custom-controls-images/custom05.png "Configurazione di nuovi punti vendita")](custom-controls-images/custom05.png#lightbox)

Salvare le modifiche, tornare a Visual Studio per Mac e consentire le modifiche per la sincronizzazione. Modificare il `ViewController.cs` file e verificare il `ViewDidLoad` metodo aspetto simile al seguente:

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

In questo caso, risposta per il `ValueChanged` evento è definiti in precedenza nel `NSFlipSwitch` classe e si scriverà corrente **valore** quando l'utente fa clic sul controllo.

Facoltativamente, è possibile tornare al generatore di interfaccia e definire un **azione** sul controllo:

[![](custom-controls-images/custom06.png "Configurazione di una nuova azione")](custom-controls-images/custom06.png#lightbox)

Modificare nuovamente il `ViewController.cs` file e aggiungere il metodo seguente:

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> È consigliabile utilizzare il **evento** o definire un **azione** in Generatore di interfaccia, ma entrambi i metodi di non utilizzare contemporaneamente o possono entrare in conflitto tra loro.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo ha richiesto la creazione di un controllo di interfaccia utente personalizzata riutilizzabile in un'applicazione Xamarin.Mac un'analisi approfondita. È stato illustrato come disegnare i controlli personalizzati dell'interfaccia utente, i due metodi principali per rispondere all'input dell'utente e di mouse e come esporre il nuovo controllo alle azioni in Generatore del Xcode di interfaccia.

## <a name="related-links"></a>Collegamenti correlati

- [MacCustomControl (esempio)](https://developer.xamarin.com/samples/mac/MacCustomControl/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Gestione degli eventi del Mouse](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
