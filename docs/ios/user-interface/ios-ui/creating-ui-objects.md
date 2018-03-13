---
title: Creazione di oggetti dell'interfaccia utente
ms.topic: article
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: e7a61dcf2cf2fabf575e30ef402121db3bea7912
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="creating-user-interface-objects"></a>Creazione di oggetti dell'interfaccia utente

Gruppi di Apple relative funzionalità in "Framework" equivalgono a spazi dei nomi di xamarin. IOS. `UIKit` è lo spazio dei nomi che contiene tutti i controlli dell'interfaccia utente per iOS.

Ogni volta che il codice deve fare riferimento a un controllo dell'interfaccia utente, ad esempio un'etichetta o un pulsante, ricordarsi di includere la seguente istruzione using:

```csharp
using UIKit;
```


Tutti i controlli descritti in questo capitolo sono nello spazio dei nomi UIKit e il nome di ogni classe di controllo utente di `UI` prefisso.

È possibile modificare i controlli dell'interfaccia utente e i layout in tre modi:

-  **[Xamarin iOS progettazione](~/ios/user-interface/designer/index.md)**  : progettazione di layout predefinito di Xamarin utilizzare per progettare le schermate. Fare doppio clic su uno storyboard o file XI da modificare con la finestra di progettazione incorporata.
-  **Generatore di interfaccia Xcode** : trascinare controlli il layout per la schermata con il generatore di interfaccia. Aprire storyboard o file XI in Xcode facendo clic con il file di **soluzione riempimento** e scegliendo **Apri con > Xcode interfaccia generatore**.
-  **In c#** – controlli inoltre possono essere costruiti con codice a livello di codice e aggiungere la gerarchia della visualizzazione.

È possibile aggiungere nuovi file di Storyboard e XI facendo clic su un progetto iOS e scegliendo **Aggiungi > Nuovo File...** .

Indipendentemente dal metodo, utilizzare le proprietà del controllo e gli eventi possono essere modificati con c# ancora nella logica dell'applicazione.

## <a name="using-xamarin-ios-designer"></a>Usare Xamarin iOS, finestra di progettazione

Per avviare la creazione di un'interfaccia utente nella finestra di progettazione iOS, fare doppio clic su un file di storyboard. Nell'area di progettazione da, è possibile trascinare i controlli di **della casella degli strumenti** come illustrato di seguito:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

 [![](creating-ui-objects-images/image2b.png "Riempimento della casella degli strumenti")](creating-ui-objects-images/image2b.png#lightbox)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

 [![](creating-ui-objects-images/image2b-vs.png "Riempimento della casella degli strumenti - Visual Studio")](creating-ui-objects-images/image2b.png#lightbox)
 
-----

Quando un controllo è selezionato nella finestra di progettazione di **proprietà riempimento** verranno visualizzati gli attributi per tale controllo. Il **Widget > identità > nome** campo, che viene compilato nella schermata seguente, viene utilizzato come il *presa* nome. Questa è la modalità con cui è possibile fare riferimento il controllo in c#:

 [![](creating-ui-objects-images/image3b.png "Proprietà Widget riquadro")](creating-ui-objects-images/image3b.png#lightbox)

Per un approfondimento in utilizzando la finestra di progettazione iOS, vedere il [Introduzione a progettazione iOS](~/ios/user-interface/designer/introduction.md) Guida.

## <a name="using-xcode-interface-builder"></a>Tramite il generatore di interfaccia Xcode

Se non si ha familiarità con il generatore di interfaccia, fare riferimento a Apple [interfaccia generatore](https://developer.apple.com/xcode/interface-builder/) documenti.

Per aprire uno Storyboard in Xcode, fare doppio clic per accedere ai menu di scelta rapida per il file di storyboard e si sceglie di aprire con il **Xcode interfaccia generatore**:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

 [![](creating-ui-objects-images/imagexcode.png "Menu di scelta rapida storyboard - Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)
 
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](creating-ui-objects-images/imagexcode-vs.png "Menu di scelta rapida storyboard - Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

Nell'area di progettazione da, è possibile trascinare i controlli di **oggetto libreria** illustrata di seguito:

 [![](creating-ui-objects-images/image5a.png "Libreria oggetti di Xcode")](creating-ui-objects-images/image5a.png#lightbox)

Quando si progetta l'interfaccia utente con il generatore di interfaccia è necessario creare un **presa** per ogni controllo che si desidera fare riferimento in c#. Ciò avviene attivando il **Assistente Editor** utilizzando il centro **Editor** pulsante sul pulsante della barra degli strumenti Xcode:

 [![](creating-ui-objects-images/image6a.png "Pulsante Assistente Editor")](creating-ui-objects-images/image6a.png#lightbox)

Fare clic su un oggetto di interfaccia utente. quindi **controllo trascinare** nel file con estensione h. A * * controllo trascinare * *, tenere premuto il tasto CTRL, quindi fare clic e tenere sopra l'oggetto di interfaccia utente che si sta creando la presa (o azione) per. Tenere premuto il tasto CTRL mentre si trascina nel file di intestazione. Fine trascinando sotto il `@interface` definizione. Una linea blu dovrebbe essere visualizzata con una didascalia presa inserire o presa insieme, come illustrato nella schermata seguente.

Quando si rilascia il clic verrà richiesto di fornire un nome per l'uscita, che verrà utilizzato per creare una proprietà in c# che è possibile fare riferimento nel codice:

 [![](creating-ui-objects-images/image8a.png "Creazione di una presa di corrente")](creating-ui-objects-images/image8a.png#lightbox)

Per ulteriori informazioni su come generatore del Xcode di interfaccia si integra con Visual Studio per Mac, vedere il [la generazione di codice XI](~/ios/internals/xib-code-generation.md#generated) documento.

##  <a name="using-c"></a>Utilizzando il linguaggio c#

Se si decide di creare a livello di codice un oggetto dell'interfaccia utente utilizzando il linguaggio c# (in una vista o un Controller di visualizzazione, ad esempio), seguire questi passaggi:

-  Dichiarare un campo di livello di classe per l'oggetto dell'interfaccia utente. Creare lo stesso controllo in una sola volta, `ViewDidLoad` , ad esempio. L'oggetto è quindi possibile fare riferimento i metodi del ciclo di vita del Controller di visualizzazione (ad es.
`ViewWillAppear`).
-  Creare un `CGRect` che definisce il frame del controllo (le coordinate X e Y sullo schermo, nonché la larghezza e altezza). È necessario assicurarsi di avere un `using CoreGraphics` direttiva per l'oggetto.
-  Chiamare il costruttore per creare e assegnare il controllo.
-  Impostare le proprietà o i gestori di eventi.
-  Chiamare `Add()` per aggiungere il controllo per la gerarchia della visualizzazione.

Di seguito è riportato un esempio semplice di creazione di un `UILabel` in un Controller di visualizzazione utilizzando il linguaggio c#:

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

## <a name="using-c-and-storyboards"></a>Con c# e gli storyboard

Quando si visualizza controller vengono aggiunti all'area di progettazione, vengono creati due il file c# corrispondente nel progetto. In questo esempio, `ControlsViewController.cs` e `ControlsViewController.designer.cs` sono stati creati automaticamente:

 [![](creating-ui-objects-images/image9b.png "Classe parziale ViewController")](creating-ui-objects-images/image9b.png#lightbox)

Il `MainViewController.cs` file è stato progettato per *codice*. Questa opzione è quando il `View` i metodi del ciclo di vita come `ViewDidLoad` e `ViewWillAppear` vengono implementate e in cui è possibile aggiungere le proprietà, campi e i metodi.

Il `ControlsViewController.designer.cs` codice generato che contiene una classe parziale. Quando si nome di un controllo sulla progettazione superficie di attacco in Visual Studio per Mac o creare un'azione o presa in Xcode, una proprietà o un metodo parziale, viene aggiunto al file di progettazione (designer.cs). Il codice riportato di seguito viene illustrato un esempio di codice generato per due pulsanti e una visualizzazione di testo, in cui uno dei pulsanti ha anche un `TouchUpInside` evento.

Questi elementi della classe parziale che il codice fare riferimento ai controlli e rispondere alle azioni che vengono dichiarate nell'area di progettazione:

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

Il `designer.cs` file non deve essere modificato manualmente: IDE (Visual Studio per Mac o Visual Studio) è responsabile di mantenere sincronizzato con lo Storyboard.

Quando gli oggetti dell'interfaccia utente vengono aggiunti a livello di codice a un `View` o `ViewController`è creare un'istanza e gestire manualmente i riferimenti agli oggetti, e pertanto non è richiesto alcun file della finestra di progettazione.



## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://developer.xamarin.com/samples/Controls/)
