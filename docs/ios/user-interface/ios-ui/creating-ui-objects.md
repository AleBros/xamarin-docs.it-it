---
title: Creazione di oggetti dell'interfaccia utente in xamarin. IOS
description: Questo documento fornisce una panoramica su come creare un'interfaccia utente in xamarin. IOS. Vengono illustrati iOS Designer, Interface Builder di Xcode, C#e gli storyboard.
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: a80bf876e239e1788a371a1f09d36d73247d4611
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865005"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>Creazione di oggetti dell'interfaccia utente in xamarin. IOS

I gruppi di Apple relative parti di funzionalità in "Framework", che equivalgono a spazi dei nomi di xamarin. IOS. `UIKit` è lo spazio dei nomi che contiene tutti i controlli dell'interfaccia utente per iOS.

Ogni volta che il codice deve fare riferimento a un controllo dell'interfaccia utente, ad esempio un'etichetta o un pulsante, ricordare di includere la seguente istruzione using:

```csharp
using UIKit;
```

Tutti i controlli descritti in questo capitolo sono nello spazio dei nomi UIKit e ha il nome di ogni classe di controllo utente di `UI` prefisso.

È possibile modificare i controlli dell'interfaccia utente e i layout in tre modi:

-  **[Xamarin iOS Designer](~/ios/user-interface/designer/index.md)**  : progettazione di layout predefinite di Xamarin Usa per progettare le schermate. Fare doppio clic dello storyboard o del file XIB per modificare il codice con la progettazione incorporata.
-  **Interface Builder Xcode** : trascinare i controlli nel layout di schermata con Interface Builder. Aprire dello storyboard o del file XIB in Xcode facendo clic con il file nei **riquadro della soluzione** e scegliendo **Apri con > Interface Builder Xcode**.
-  **Usando C#**  – controlli inoltre possono essere costruiti con codice a livello di programmazione e aggiunto alla gerarchia di visualizzazione.

È possibile aggiungere nuovi file Storyboard e XIB facendo clic su un progetto iOS e scegliendo **Aggiungi > Nuovo File...** .

Qualunque sia il metodo da utilizzare, le proprietà del controllo e gli eventi possono essere manipolati ancora con C# nella logica dell'applicazione.

## <a name="using-xamarin-ios-designer"></a>Utilizzo di Xamarin iOS Designer

Per iniziare a creare un'interfaccia utente in iOS Designer, fare doppio clic su un file storyboard. I controlli possono essere trascinati nell'area di progettazione dal **casella degli strumenti** come illustrato di seguito:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

 [![](creating-ui-objects-images/image2b.png "Riquadro della casella degli strumenti")](creating-ui-objects-images/image2b.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 [![](creating-ui-objects-images/image2b-vs.png "Riquadro della casella degli strumenti - Visual Studio")](creating-ui-objects-images/image2b.png#lightbox)
 
-----

Quando viene selezionato un controllo nell'area di progettazione di **riquadro delle proprietà** verranno visualizzati gli attributi per il controllo. Il **Widget > identità > nome** campo, che viene compilato nello screenshot seguente, viene utilizzato come il *Outlet* nome. Si tratta di come è possibile fare riferimento il controllo in C#:

 [![](creating-ui-objects-images/image3b.png "Riquadro delle proprietà Widget")](creating-ui-objects-images/image3b.png#lightbox)

Per un approfondimento dell'uso di iOS designer, vedere la [Introduzione a iOS Designer](~/ios/user-interface/designer/introduction.md) Guida.

## <a name="using-xcode-interface-builder"></a>Uso di Interface Builder di Xcode

Se non si ha familiarità con Interface Builder, fare riferimento a Apple [Interface Builder](https://developer.apple.com/xcode/interface-builder/) documenti.

Per aprire uno Storyboard in Xcode, fare doppio clic per accedere a menu di scelta rapida per il file storyboard e scegliere di aprire con il **Interface Builder Xcode**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

 [![](creating-ui-objects-images/imagexcode.png "Menu di scelta rapida storyboard - Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](creating-ui-objects-images/imagexcode-vs.png "Menu di scelta rapida storyboard - Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

I controlli possono essere trascinati nell'area di progettazione dal **libreria di oggetti** illustrato di seguito:

 [![](creating-ui-objects-images/image5a.png "Libreria oggetti di Xcode")](creating-ui-objects-images/image5a.png#lightbox)

Quando si progetta l'interfaccia utente con Interface Builder, è necessario creare un **Outlet** per ogni controllo che si vuole fare riferimento C#. Ciò avviene attivando il **Assistente dell'Editor** utilizzando il centro **Editor** pulsante sul pulsante della barra degli strumenti di Xcode:

 [![](creating-ui-objects-images/image6a.png "Pulsante Assistente dell'Editor")](creating-ui-objects-images/image6a.png#lightbox)

Fare clic su un oggetto di interfaccia utente; quindi **trascinamento controllo** nel file con estensione h. Per **trascinamento controllo**, tenere premuto il tasto CTRL, quindi fare clic e tenere sopra l'oggetto di interfaccia utente che si sta creando la presa (o azione) per. Tenere premuto il tasto CTRL mentre si trascina nel file di intestazione. Fine trascinamento di sotto di `@interface` definizione. Una linea blu dovrebbe essere visualizzata con un Outlet Inserisci didascalia o una raccolta di Outlet, come illustrato nello screenshot seguente.

Quando si rilascia il clic verrà richiesto di specificare un nome per il Negozio, che verrà usato per creare un C# proprietà che è possibile fare riferimento nel codice:

 [![](creating-ui-objects-images/image8a.png "Creazione di un outlet")](creating-ui-objects-images/image8a.png#lightbox)

Per altre informazioni su come Interface Builder di Xcode si integra con Visual Studio per Mac, vedere la [generazione di codice Xib](~/ios/internals/xib-code-generation.md#generated) documento.

## <a name="using-c"></a>UtilizzoC#

Se si decide di creare a livello di codice un oggetto di interfaccia utente usando C# (in una vista o un Controller di visualizzazione, ad esempio), seguire questa procedura:

-  Dichiarare un campo a livello di classe per l'oggetto di interfaccia utente. Creare lo stesso controllo in una sola volta, `ViewDidLoad` , ad esempio. L'oggetto è quindi possibile fare riferimento i metodi del ciclo di vita del Controller di visualizzazione (ad es.
`ViewWillAppear`).
-  Creare un `CGRect` che definisce il frame del controllo (relative coordinate X e Y su schermo, nonché la larghezza e altezza). È necessario assicurarsi di avere un `using CoreGraphics` direttiva per questo oggetto.
-  Chiamare il costruttore per creare e assegnare il controllo.
-  Impostare qualsiasi proprietà o i gestori eventi.
-  Chiamare `Add()` per aggiungere il controllo per la gerarchia di visualizzazione.

Ecco un semplice esempio di creazione di un `UILabel` in un Controller di visualizzazione tramite C#:

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

## <a name="using-c-and-storyboards"></a>Usando C# e gli storyboard

Quando i controller di visualizzazione vengono aggiunti all'area di progettazione, due corrispondente C# vengono creati i file nel progetto. In questo esempio `ControlsViewController.cs` e `ControlsViewController.designer.cs` sono stati creati automaticamente:

 [![](creating-ui-objects-images/image9b.png "Classe parziale ViewController")](creating-ui-objects-images/image9b.png#lightbox)

Il `MainViewController.cs` file destinato *codice*. Si tratta di dove il `View` metodi del ciclo di vita, ad esempio `ViewDidLoad` e `ViewWillAppear` vengono implementate e in cui è possibile aggiungere le proprie proprietà, campi e metodi.

Il `ControlsViewController.designer.cs` viene generato codice che contiene una classe parziale. Quando si nome di un controllo sulla progettazione della superficie di attacco in Visual Studio per Mac o creare un'azione o presa in Xcode, una proprietà corrispondente o il metodo parziale, viene aggiunto al file di progettazione (designer.cs). Il codice seguente illustra un esempio di codice generato per due pulsanti e una visualizzazione di testo, in cui uno dei pulsanti ha anche un `TouchUpInside` evento.

Questi elementi della classe parziale consentono al codice fare riferimento ai controlli e rispondere alle azioni che vengono dichiarate nell'area di progettazione:

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

Il `designer.cs` file non deve essere modificato manualmente, l'IDE (Visual Studio per Mac o Visual Studio) è responsabile dei mantenendola sincronizzata con lo Storyboard.

Quando gli oggetti dell'interfaccia utente vengono aggiunti a livello di codice a un `View` o `ViewController`, si crea un'istanza e di gestire i riferimenti agli oggetti e pertanto sarà necessario alcun file della finestra di progettazione.



## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://developer.xamarin.com/samples/monotouch/Controls/)
