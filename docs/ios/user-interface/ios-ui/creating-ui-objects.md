---
title: Creazione di oggetti dell'interfaccia utente in Xamarin.iOS
description: Questo documento fornisce una panoramica su come creare un'interfaccia utente in Xamarin.iOS. Vengono illustrati gli storyboard di iOS designer, C#Xcode Interface Builder, e.
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 58a1fd68dda2216a62fe6f30cf61d6d2ec7d40d5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003665"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>Creazione di oggetti dell'interfaccia utente in Xamarin.iOS

Apple raggruppa i componenti di funzionalità correlati in "Framework", che corrispondono agli spazi dei nomi Xamarin.iOS. `UIKit` è lo spazio dei nomi che contiene tutti i controlli dell'interfaccia utente per iOS.

Ogni volta che il codice deve fare riferimento a un controllo dell'interfaccia utente, ad esempio un'etichetta o un pulsante, ricordarsi di includere la seguente istruzione using:

```csharp
using UIKit;
```

Tutti i controlli descritti in questo capitolo si trovano nello spazio dei nomi UIKit e ogni nome della classe del controllo utente ha il prefisso `UI`.

È possibile modificare i layout e i controlli dell'interfaccia utente in tre modi:

- **[Xamarin iOS designer](~/ios/user-interface/designer/index.md)** : usare la finestra di progettazione layout predefinita di Xamarin per progettare le schermate. Fare doppio clic su file storyboard o XIB per modificare con la finestra di progettazione incorporata.
- **Xcode Interface Builder** : trascinare i controlli sui layout dello schermo con Interface Builder. Aprire il file storyboard o XIB in Xcode facendo clic con il pulsante destro del mouse sul file nella **riquadro della soluzione** e scegliendo **Apri con > Xcode Interface Builder**.
- **Using C#**  : i controlli possono essere costruiti anche a livello di codice e aggiunti alla gerarchia di visualizzazione.

È possibile aggiungere nuovi file storyboard e XIB facendo clic con il pulsante destro del mouse su un progetto iOS e scegliendo **aggiungi > nuovo file.**

Indipendentemente dal metodo usato, controllare proprietà ed eventi può comunque essere modificato con C# nella logica dell'applicazione.

## <a name="using-xamarin-ios-designer"></a>Uso di Xamarin iOS designer

Per iniziare a creare l'interfaccia utente in iOS designer, fare doppio clic su un file dello storyboard. I controlli possono essere trascinati nell'area di progettazione dalla **casella degli strumenti** , come illustrato di seguito:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

 [![](creating-ui-objects-images/image2b.png "Toolbox Pad")](creating-ui-objects-images/image2b.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 [![](creating-ui-objects-images/image2b-vs.png "Toolbox Pad - Visual Stuio")](creating-ui-objects-images/image2b.png#lightbox)

-----

Quando si seleziona un controllo nell'area di progettazione, il **riquadro delle proprietà** visualizzerà gli attributi per il controllo. Il campo **Widget > Identity > Name** , che viene popolato nella schermata seguente, viene usato come nome di *uscita* . Questo è il modo in cui è possibile fare C#riferimento al controllo in:

 [![](creating-ui-objects-images/image3b.png "Properties Widget Pad")](creating-ui-objects-images/image3b.png#lightbox)

Per approfondire l'uso di iOS designer, vedere la Guida [introduttiva alla finestra di progettazione iOS](~/ios/user-interface/designer/introduction.md) .

## <a name="using-xcode-interface-builder"></a>Uso di Xcode Interface Builder

Se non si ha familiarità con l'uso di Interface Builder, vedere la pagina relativa ai documenti [Interface Builder](https://developer.apple.com/xcode/interface-builder/) di Apple.

Per aprire uno storyboard in Xcode, fare clic con il pulsante destro del mouse per accedere al menu di scelta rapida per il file dello storyboard e scegliere di aprire con la **Interface Builder Xcode**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

 [![](creating-ui-objects-images/imagexcode.png "Storyboard context menu - Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](creating-ui-objects-images/imagexcode-vs.png "Storyboard context menu - Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

I controlli possono essere trascinati nella Area di progettazione dalla **libreria di oggetti** illustrata di seguito:

 [![](creating-ui-objects-images/image5a.png "Xcode Object Library")](creating-ui-objects-images/image5a.png#lightbox)

Quando si progetta l'interfaccia utente con Interface Builder è necessario creare un **Outlet** per ogni controllo a cui si vuole fare C#riferimento. A tale scopo, attivare l' **editor di assistente** usando il pulsante centrale dell' **Editor** sul pulsante della barra degli strumenti di Xcode:

 [![](creating-ui-objects-images/image6a.png "Assistant Editor button")](creating-ui-objects-images/image6a.png#lightbox)

Fare clic su un oggetto dell'interfaccia utente; quindi **controllare il trascinamento** nel file con estensione h. Per **controllare il trascinamento**, tener premuto il tasto CTRL e quindi fare clic sull'oggetto dell'interfaccia utente per cui si sta creando la presa (o azione). Tenere premuto il tasto CTRL mentre si trascina il file di intestazione. Fine del trascinamento sotto la definizione del `@interface`. Dovrebbe essere visualizzata una linea blu con una raccolta didascalia Insert Outlet o Outlet, come illustrato nella schermata seguente.

Quando si rilascia il clic, verrà richiesto di specificare un nome per l'Outlet, che verrà usato per creare una C# proprietà a cui è possibile fare riferimento nel codice:

 [![](creating-ui-objects-images/image8a.png "Creating an outlet")](creating-ui-objects-images/image8a.png#lightbox)

Per altre informazioni sull'integrazione di Interface Builder di Xcode con Visual Studio per Mac, vedere il documento di [generazione del codice XIB](~/ios/internals/xib-code-generation.md#generated) .

## <a name="using-c"></a>Utilizzo di C \#

Se si decide di creare a livello di codice un oggetto dell' C# interfaccia utente usando (ad esempio, in una vista o in un controller di visualizzazione), seguire questa procedura:

- Dichiarare un campo a livello di classe per l'oggetto dell'interfaccia utente. Creare il controllo in una sola volta, ad esempio `ViewDidLoad`. È possibile fare riferimento all'oggetto in tutti i metodi del ciclo di vita del controller di visualizzazione, ad esempio
`ViewWillAppear`).
- Creare una `CGRect` che definisce il frame del controllo (le relative coordinate X e Y sullo schermo, nonché la larghezza e l'altezza). È necessario assicurarsi di disporre di una direttiva `using CoreGraphics` per questa operazione.
- Chiamare il costruttore per creare e assegnare il controllo.
- Impostare le proprietà o i gestori eventi.
- Chiamare `Add()` per aggiungere il controllo alla gerarchia di visualizzazione.

Ecco un semplice esempio di creazione di un `UILabel` in un controller di visualizzazione C#con:

```csharp
UILabel label1;
public override void ViewDidLoad () {
    base.ViewDidLoad ();
    var frame = new CGRect(10, 10, 300, 30);
    label1 = new UILabel(frame);
    label1.Text = "New Label";
    View.Add (label1);
}
```

<a name="partial_classes" />

## <a name="using-c-and-storyboards"></a>Uso C# degli storyboard e

Quando i controller di visualizzazione vengono aggiunti alla Area di progettazione, nel C# progetto vengono creati due file corrispondenti. In questo esempio `ControlsViewController.cs` e `ControlsViewController.designer.cs` sono stati creati automaticamente:

 [![](creating-ui-objects-images/image9b.png "ViewController partial class")](creating-ui-objects-images/image9b.png#lightbox)

Il file di `ControlsViewController.cs` è destinato al *codice*. Sono i metodi di `View` ciclo di vita, ad esempio `ViewDidLoad` e `ViewWillAppear`, e la posizione in cui è possibile aggiungere proprietà, campi e metodi.

Il `ControlsViewController.designer.cs` viene generato codice contenente una classe parziale. Quando si rinomina un controllo nell'area di progettazione in Visual Studio per Mac o si crea un outlet o un'azione in Xcode, viene aggiunta una proprietà corrispondente o un metodo parziale al file di progettazione (designer.cs). Nel codice riportato di seguito viene illustrato un esempio di codice generato per due pulsanti e una visualizzazione di testo, in cui uno dei pulsanti dispone anche di un evento `TouchUpInside`.

Questi elementi della classe parziale consentono al codice di fare riferimento ai controlli e rispondere alle azioni dichiarate nell'area di progettazione:

```csharp
[Register ("ControlsViewController")]
    partial class ControlsViewController
    {
        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button1 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button2 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UITextField textfield1 { get; set; }

        [Action ("button2_TouchUpInside:")]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        partial void button2_TouchUpInside (UIButton sender);

        void ReleaseDesignerOutlets ()
        {
            if (Button1 != null) {
                Button1.Dispose ();
                Button1 = null;
            }
            if (Button2 != null) {
                Button2.Dispose ();
                Button2 = null;
            }
            if (textfield1 != null) {
                textfield1.Dispose ();
                textfield1 = null;
            }
        }
    }
}
```

Il file di `designer.cs` non deve essere modificato manualmente. l'IDE (Visual Studio per Mac o Visual Studio) è responsabile di mantenerlo sincronizzato con lo storyboard.

Quando gli oggetti dell'interfaccia utente vengono aggiunti a livello di codice a un `View` o `ViewController`, si creano istanze e si gestiscono i riferimenti agli oggetti manualmente e pertanto non è necessario alcun file di progettazione.

## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
