---
title: Creazione di interfacce utente iOS nel codice in xamarin. IOS
description: Questo documento viene descritto come usare il codice per compilare un'interfaccia utente per un'app xamarin. IOS. Vengono illustrati i controller di visualizzazione, la creazione di una gerarchia di visualizzazione, gestione una rotazione e altro ancora.
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/03/2018
ms.openlocfilehash: 5e8abc2cea2e2ca8abfada8bc85379d93d183768
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784634"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>Creazione di interfacce utente iOS nel codice in xamarin. IOS

L'interfaccia utente di un'app iOS è ad esempio un storefront: l'applicazione ottiene in genere una finestra, ma è possibile riempire la finestra con come molti oggetti disponibili, è necessario, e gli oggetti e i dispositivi possono essere modificate a seconda di ciò che l'app desidera visualizzare. Gli oggetti di questo scenario, ovvero gli elementi visibili per l'utente, sono chiamati visualizzazioni. Per compilare una singola schermata in un'applicazione, le visualizzazioni sono sovrapposti in una gerarchia di visualizzazione del contenuto e la gerarchia è gestita da un singolo Controller di visualizzazione. Le applicazioni con più schermate hanno più gerarchie di visualizzazione del contenuto, ognuna con un proprio controller di visualizzazione, e l'applicazione inserisce visualizzazioni nella finestra per creare gerarchie di visualizzazione del contenuto diverse in base alla schermata visualizzata dall'utente.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Il diagramma seguente illustra le relazioni tra la finestra, le visualizzazioni, le visualizzazioni secondarie e il controller di visualizzazione che visualizzano l'interfaccia utente sullo schermo del dispositivo: 

[![](ios-code-only-images/image9.png "Questo diagramma viene illustrata la relazione tra la finestra, viste, sottoviste e View Controller")](ios-code-only-images/image9.png#lightbox)

Queste gerarchie di visualizzazione possono essere costruite usando il [progettazione Xamarin per iOS](~/ios/user-interface/designer/index.md) in Visual Studio, tuttavia è opportuno disporre di conoscenze approfondite sulla modalità di utilizzo completamente nel codice. In questo articolo vengono illustrati alcuni aspetti di base per la configurazione e l'esecuzione con lo sviluppo di interfaccia di solo codice utente.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Il diagramma seguente illustra le relazioni tra la finestra, le visualizzazioni, le visualizzazioni secondarie e il controller di visualizzazione che visualizzano l'interfaccia utente sullo schermo del dispositivo: 

[![](ios-code-only-images/image9.png "Questo diagramma viene illustrata la relazione tra la finestra, viste, sottoviste e View Controller")](ios-code-only-images/image9.png#lightbox)

Queste gerarchie di visualizzazione possono essere costruite usando il [progettazione Xamarin per iOS](~/ios/user-interface/designer/index.md) in Visual Studio per Mac, tuttavia è opportuno disporre di conoscenze approfondite sulla modalità di utilizzo completamente nel codice. In questo articolo vengono illustrati alcuni aspetti di base per la configurazione e l'esecuzione con lo sviluppo di interfaccia di solo codice utente.

-----

## <a name="creating-a-code-only-project"></a>Creazione di un progetto di solo codice

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="ios-blank-project-template"></a>Modello di progetto vuoto di iOS

Creare innanzitutto un progetto iOS in Visual Studio usando il **File > Nuovo progetto > c# > iPhone & iPad > iOS App (Xamarin)** progetto, vedere di seguito:

[![Finestra di dialogo Nuovo progetto](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

Quindi selezionare il **applicazione vuota** modello di progetto:

[![Selezionare una finestra di dialogo di modello](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

Il modello di progetto vuoto aggiunge 4 file al progetto:

[![File di progetto](ios-code-only-images/empty-project.w157-sml.png "file di progetto")](ios-code-only-images/empty-project.w157.png#lightbox)


1. **Appdelegate. cs** -contiene un `UIApplicationDelegate` sottoclasse `AppDelegate` , che viene utilizzata per gestire gli eventi dell'applicazione IOS. La finestra dell'applicazione viene creata nel `AppDelegate`del `FinishedLaunching` metodo.
1. **Main.cs** -contiene il punto di ingresso per l'applicazione, che specifica la classe per il `AppDelegate` .
1. **Info. plist** -file elenco delle proprietà che contiene informazioni di configurazione dell'applicazione.
1. **Entitlements.plist** : file elenco delle proprietà che contiene informazioni sulle funzionalità e le autorizzazioni dell'applicazione.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

## <a name="ios-templates"></a>Modelli di iOS


Visual Studio per Mac non fornisce un modello vuoto. Tutti i modelli forniti con il supporto dello Storyboard, Apple consiglia come la modalità principale per creare un'interfaccia utente. Tuttavia, è possibile creare l'interfaccia utente completamente nel codice. 

I passaggi seguenti consentono di eseguire la rimozione dello Storyboard da un'applicazione: 


1. Utilizzare il modello di sola visualizzazione App per creare un nuovo progetto di iOS:
    
    [![](ios-code-only-images/single-view-app.png "Utilizzare il modello di sola visualizzazione App")](ios-code-only-images/single-view-app.png#lightbox)

1. Eliminare il `Main.Storyboard` e `ViewController.cs` file. Eseguire **non** eliminare il `LaunchScreen.Storyboard`. Visualizzazione Controller deve essere eliminato perché il code-behind per il controller di visualizzazione creata nello Storyboard è:
1. Assicurarsi di selezionare **eliminare** dalla finestra di dialogo popup:
    
    [![](ios-code-only-images/delete.png "Selezionare Elimina dalla finestra di dialogo popup")](ios-code-only-images/delete.png#lightbox)

1. Nel file Info. plist, eliminare le informazioni all'interno di **le informazioni sulla distribuzione > interfaccia principale** opzione:
    
    [![](ios-code-only-images/main-interface.png "Eliminare le informazioni all'interno di opzione dell'interfaccia principale")](ios-code-only-images/main-interface.png#lightbox)

1. Infine, aggiungere il codice seguente per il `FinishedLaunching` metodo nella classe AppDelegate:
        
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            // create a new window instance based on the screen size
            window = new UIWindow(UIScreen.MainScreen.Bounds);

            // make the window visible
            window.MakeKeyAndVisible();

            return true;
        }

Il codice che è stato aggiunto per il `FinishedLaunching` metodo nel passaggio 5 sopra, è la quantità minima di codice necessario per creare una finestra per l'applicazione iOS.


-----



le applicazioni iOS vengono compilate mediante il [modello MVC](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#Model_View_Controller). La prima schermata che consente di visualizzare un'applicazione viene creata dal controller di visualizzazione della finestra principale. Vedere il [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md) della Guida per ulteriori informazioni sul MVC criterio stesso.

L'implementazione per il `AppDelegate` aggiunto per il modello crea la finestra dell'applicazione, di cui si è solo uno per ogni applicazione iOS e lo rende visibile con il codice seguente:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    public override UIWindow Window
            {
                get;
                set;
            }

    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        window.MakeKeyAndVisible();

        return true;
    }
}
```

Se si esegue l'applicazione ora, si otterrebbe probabilmente un'eccezione indicante che `Application windows are expected to have a root view controller at the end of application launch`. Si aggiunge un Controller e renderlo Controller di visualizzazione principale dell'applicazione.

## <a name="adding-a-controller"></a>Aggiunta di un controller

L'app può contenere molti controller di visualizzazione, ma è necessario disporre di un Controller di visualizzazione principale per controllare tutti i controller di visualizzazione.  Aggiungere un controller per la finestra creando un `UIViewController` istanza e l'impostazione il `window.RootViewController` proprietà:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;

        Window.RootViewController = controller;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Ogni controller dispone di una visualizzazione associata, accessibile dal `View` proprietà. La visualizzazione di modifica il codice sopra riportato `BackgroundColor` proprietà `UIColor.LightGray` in modo che sia visibile, come illustrato di seguito:

 [![](ios-code-only-images/image1.png "Sfondo della visualizzazione è un grigio chiaro visibile")](ios-code-only-images/image1.png#lightbox)

È stato possibile impostare qualsiasi `UIViewController` sottoclasse come il `RootViewController` in questo modo, inclusi controller da UIKit nonché quelle creiamo effettuata. Ad esempio, il codice seguente aggiunge un `UINavigationController` come il `RootViewController`:

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;
        controller.Title = "My Controller";

        var navController = new UINavigationController(controller);

        Window.RootViewController = navController;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Viene prodotto il controller annidato all'interno del controller di navigazione, come illustrato di seguito:

 [![](ios-code-only-images/image2.png "Il controller annidato all'interno del controller di navigazione")](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>Creazione di un Controller di visualizzazione

Ora che abbiamo visto come aggiungere un controller di come il `RootViewController` della finestra, di seguito viene illustrato come creare un controller di visualizzazione personalizzata nel codice.

Aggiungere una nuova classe denominata `CustomViewController` come illustrato di seguito:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](ios-code-only-images/customviewcontroller.w157-sml.png "Aggiungere una nuova classe denominata CustomViewController")](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](ios-code-only-images/new-file.png "Aggiungere una nuova classe denominata CustomViewController")](ios-code-only-images/new-file.png#lightbox)

-----

La classe deve ereditare da `UIViewController`, che è il `UIKit` dello spazio dei nomi, come illustrato:

```csharp
using System;
using UIKit;

namespace CodeOnlyDemo
{
    class CustomViewController : UIViewController
    {
    }
}
```

<a name="Initializing_the_View"/>

## <a name="initializing-the-view"></a>L'inizializzazione della visualizzazione

`UIViewController` contiene un metodo denominato `ViewDidLoad` che viene chiamato quando il controller di visualizzazione viene caricato inizialmente in memoria. Si tratta di una posizione appropriata per eseguire l'inizializzazione della visualizzazione, ad esempio l'impostazione della proprietà.

Ad esempio, il codice seguente aggiunge un pulsante e un gestore eventi per effettuare il push nello stack di navigazione, un nuovo Controller di visualizzazione quando viene premuto il pulsante:

```csharp
using System;
using CoreGraphics;
using UIKit;

namespace CodyOnlyDemo
{
    public class CustomViewController : UIViewController
    {
        public CustomViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            View.BackgroundColor = UIColor.White;
            Title = "My Custom View Controller";

            var btn = UIButton.FromType (UIButtonType.System);
            btn.Frame = new CGRect (20, 200, 280, 44);
            btn.SetTitle ("Click Me", UIControlState.Normal);

            var user = new UIViewController ();
            user.View.BackgroundColor = UIColor.Magenta;

            btn.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (user, true);
            };

            View.AddSubview (btn);

        }
    }
}
```

Per caricare questo controller dell'applicazione e illustrano la navigazione semplice, creare una nuova istanza della `CustomViewController`. Creare un nuovo controller di spostamento, passare l'istanza di controller di visualizzazione e impostare il nuovo controller di navigazione della finestra `RootViewController` nel `AppDelegate` come in precedenza:

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

A questo punto quando l'applicazione viene caricato, il `CustomViewController` viene caricato all'interno di un controller di navigazione:

 [![](ios-code-only-images/customvc.png "Viene caricato il CustomViewController all'interno di un controller di navigazione")](ios-code-only-images/customvc.png#lightbox)
 
Fare clic sul pulsante, verrà _push_ un nuovo Controller di visualizzazione nello stack di navigazione:

[![](ios-code-only-images/customvca.png "Un nuovo Controller di visualizzazione nello stack di navigazione")](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>La compilazione della gerarchia di visualizzazione

Nell'esempio precedente, è iniziato a creare un'interfaccia utente nel codice tramite l'aggiunta di un pulsante al Controller di visualizzazione.

interfacce utente iOS sono costituite da una gerarchia di visualizzazione. Visualizzazioni aggiuntive, ad esempio etichette, i pulsanti, i dispositivi di scorrimento e così via vengono aggiunti come sottoviste di alcuni visualizzazione padre.

Ad esempio, si modifica il `CustomViewController` per creare una schermata di accesso in cui l'utente può immettere un nome utente e una password. La schermata sarà costituito da due campi di testo e un pulsante.

### <a name="adding-the-text-fields"></a>Aggiunta di campi di testo

Innanzitutto, rimuovere il pulsante e il gestore eventi che è stato aggiunto nel [durante l'inizializzazione della visualizzazione](#Initializing_the_View) sezione. 

Aggiungere un controllo per il nome utente mediante la creazione e inizializzazione di un `UITextField` e quindi aggiungerlo alla gerarchia di visualizzazione, come illustrato di seguito:

```csharp
class CustomViewController : UIViewController
{
    UITextField usernameField;

    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        View.BackgroundColor = UIColor.Gray;

        nfloat h = 31.0f;
        nfloat w = View.Bounds.Width;

        usernameField = new UITextField
        {
            Placeholder = "Enter your username",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 82, w - 20, h)
        };

        View.AddSubview(usernameField);
    }
}
```

Quando si crea il `UITextField`, viene impostato il `Frame` proprietà per definire la posizione e dimensioni. In iOS coordinate 0,0 è in alto a sinistra con + x a destra e + y verso il basso. Dopo aver impostato la `Frame` con poche altre proprietà, viene chiamato `View.AddSubview` per aggiungere il `UITextField` per la gerarchia della visualizzazione. In questo modo il `usernameField` una visualizzazione secondaria del `UIView` istanza il `View` riferimenti alle proprietà. Una visualizzazione secondaria viene aggiunto con un ordine z è superiore alla relativa visualizzazione padre, viene visualizzato davanti visualizzazione padre sullo schermo.

L'applicazione con il `UITextField` incluso di seguito è riportato:

 [![](ios-code-only-images/image4.png "L'applicazione con il UITextField inclusi")](ios-code-only-images/image4.png#lightbox)

È possibile aggiungere un `UITextField` per la password in modo simile, solo l'ora viene impostato il `SecureTextEntry` la proprietà su true, come illustrato di seguito:

```csharp
public class CustomViewController : UIViewController
{
    UITextField usernameField, passwordField;
    public override void ViewDidLoad()
    {
       // keep the code the username UITextField
        passwordField = new UITextField
        {
            Placeholder = "Enter your password",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 114, w - 20, h),
            SecureTextEntry = true
        };

      View.AddSubview(usernameField); 
      View.AddSubview(passwordField);
   }
}

```

Impostazione `SecureTextEntry = true` nascondere il testo immesso nel `UITextField` dall'utente come illustrato di seguito:

 [![](ios-code-only-images/image4a.png "Impostazione SecureTextEntry true nascosta il testo immesso dall'utente")](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>Aggiunta del pulsante

Successivamente, verrà aggiunto un pulsante in modo l'utente può inviare il nome utente e password. Il pulsante verrà aggiunto alla gerarchia di visualizzazione come qualsiasi altro controllo, passarlo come argomento per la visualizzazione padre `AddSubview` nuovo metodo.

Il codice seguente viene aggiunto il pulsante e registra un gestore eventi per il `TouchUpInside` evento:

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

A questo punto, la schermata di accesso a questo punto viene visualizzato come illustrato di seguito:

 [![](ios-code-only-images/image5.png "Schermata di accesso")](ios-code-only-images/image5.png#lightbox)

A differenza delle versioni precedenti di iOS, lo sfondo del pulsante predefinito è trasparente. Modifica del pulsante `BackgroundColor` modifica questa proprietà:

```csharp
submitButton.BackgroundColor = UIColor.White;
```

Ciò comporterà un pulsante quadrato anziché la tipica arrotondato pulsante con bordi. Per ottenere il bordo arrotondato, usare il frammento seguente:

```csharp
submitButton.Layer.CornerRadius = 5f;
```

Con queste modifiche, la visualizzazione sarà simile al seguente:

[![](ios-code-only-images/image6.png "Eseguire un esempio della vista")](ios-code-only-images/image6.png#lightbox)
 
## <a name="adding-multiple-views-to-the-view-hierarchy"></a>Aggiunta di più visualizzazioni per la gerarchia della visualizzazione

iOS consente di aggiungere più viste per la gerarchia della visualizzazione tramite `AddSubviews`.

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton }); 
```

## <a name="adding-button-functionality"></a>Aggiunta di funzionalità di pulsante

Quando viene scelto un pulsante, gli utenti previsti di un intervento. Ad esempio, viene visualizzato un avviso o spostamento viene eseguito a un'altra schermata. 

Aggiungere codice per inserire un secondo controller di visualizzazione nello stack di navigazione.

Innanzitutto, creare il secondo controller di visualizzazione:

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

Quindi, aggiungere la funzionalità per il `TouchUpInside` evento:

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

Di seguito è illustrata la navigazione:

[![](ios-code-only-images/navigation.png "La navigazione è illustrata in questo grafico")](ios-code-only-images/navigation.png#lightbox)

Si noti che per impostazione predefinita, quando si utilizza un Controller di navigazione, iOS assegna all'applicazione una barra di spostamento e un pulsante Indietro che consente di spostarsi all'indietro lo stack.

## <a name="iterating-through-the-view-hierarchy"></a>Scorrere la gerarchia della visualizzazione

È possibile scorrere la gerarchia secondaria e di individuare qualsiasi vista specifica. Ad esempio, per trovare ogni `UIButton` e assegnare un altro pulsante `BackgroundColor`, il frammento di codice seguente può essere utilizzato.

```csharp
foreach(var subview in View.Subviews)
{
    if (subview is UIButton)
    {
         var btn = subview as UIButton;
         btn.BackgroundColor = UIColor.Green;
    }
}
```

Questa operazione, tuttavia non funzionerà se la vista viene eseguita l'iterazione per un `UIView` come tutte le visualizzazioni, verrà ripristinato come una `UIView` come gli oggetti aggiunti alla visualizzazione padre stessi ereditare `UIView`.

## <a name="handling-rotation"></a>Rotazione di gestione

Se l'utente ruota il dispositivo in orizzontale, i controlli non vengono ridimensionano in modo appropriato, come illustrato nella schermata seguente:

 [![](ios-code-only-images/image7.png "Se l'utente ruota il dispositivo in orizzontale, i controlli non vengono ridimensionano in modo appropriato")](ios-code-only-images/image7.png#lightbox)

È un modo per risolvere questo problema impostando il `AutoresizingMask` proprietà in ogni visualizzazione. In questo caso si desidera che i controlli per l'estensione in orizzontale, pertanto è necessario impostare ogni `AutoresizingMask`. L'esempio seguente è per `usernameField`, ma lo stesso dovrà essere applicato a ogni gadget nella gerarchia della visualizzazione.

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

A questo punto quando si ruota il dispositivo o simulatore, tutto ciò che si estende per riempire lo spazio aggiuntivo, come illustrato di seguito:

 [![](ios-code-only-images/image8.png "Tutti i controlli adattano per riempire lo spazio aggiuntivo")](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>Creazione di visualizzazioni personalizzate

Oltre a utilizzare i controlli che fanno parte di UIKit, visualizzazioni personalizzate possono anche essere utilizzate. È possibile creare una vista personalizzata ereditando dalla `UIView` ed eseguendo l'override `Draw`. Si crea una visualizzazione personalizzata e aggiungerla alla gerarchia di visualizzazione per illustrare.

### <a name="inheriting-from-uiview"></a>Eredita da UIView

La prima cosa da fare è creare una classe per la visualizzazione personalizzata. Faremo questa utilizzando il **classe** modello in Visual Studio per aggiungere una classe vuota denominata `CircleView`. La classe di base deve essere impostata su `UIView`, che si richiama il `UIKit` dello spazio dei nomi. È necessario anche il `System.Drawing` anche lo spazio dei nomi. Diversi altri `System.*` gli spazi dei nomi non potrà più essere utilizzato in questo esempio, pertanto è possibile rimuoverli.

La classe dovrebbe essere simile al seguente:

```csharp
using System;

namespace CodeOnlyDemo
{
    class CircleView : UIView
    {
    }
}
```

### <a name="drawing-in-a-uiview"></a>Disegno in un UIView

Ogni `UIView` ha un `Draw` metodo che viene chiamato dal sistema quando deve essere disegnato. `Draw` non deve essere chiamato direttamente. Bensì dal sistema durante l'elaborazione di cicli di esecuzione. La prima volta attraverso il ciclo di esecuzione dopo l'aggiunta di una vista per la gerarchia della visualizzazione, il relativo `Draw` metodo viene chiamato. Le chiamate successive a `Draw` si verificano quando la vista è contrassegnata come da disegnare con una chiamata a `SetNeedsDisplay` o `SetNeedsDisplayInRect` nella vista.

È possibile aggiungere codice di disegno per la visualizzazione aggiungendo tale codice sottoposto a override `Draw` metodo come illustrato di seguito:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    //get graphics context
    using (var g = UIGraphics.GetCurrentContext())
    {
        // set up drawing attributes
        g.SetLineWidth(10.0f);
        UIColor.Green.SetFill();
        UIColor.Blue.SetStroke();

        // create geometry
        var path = new CGPath();
        path.AddArc(Bounds.GetMidX(), Bounds.GetMidY(), 50f, 0, 2.0f * (float)Math.PI, true);

        // add geometry to graphics context and draw
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);
    }
}
```

Poiché `CircleView` è un `UIView`, è anche possibile impostare `UIView` anche le proprietà. Ad esempio, è possibile impostare il `BackgroundColor` nel costruttore:

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

Utilizzare il `CircleView` appena creato, è possibile aggiungere come una visualizzazione secondaria per la gerarchia della visualizzazione in un controller esistente, come è stato fatto con il `UILabels` e `UIButton` in precedenza, oppure è possibile caricare, come la visualizzazione di un nuovo controller. Di seguito, eseguire quest'ultimo.

### <a name="loading-a-view"></a>Il caricamento di una vista

 `UIViewController` contiene un metodo denominato `LoadView` che viene chiamato dal controller per creare una vista. Si tratta di una posizione appropriata per creare una vista e assegnarla al controller `View` proprietà.

Innanzitutto, è necessario un controller, quindi creare una nuova classe vuota denominata `CircleController`.

In `CircleController` aggiungere il codice seguente per impostare il `View` per un `CircleView` (non è necessario chiamare il `base` implementazione nell'override):

```csharp
using UIKit;

namespace CodeOnlyDemo
{
    class CircleController : UIViewController
    {
        CircleView view;

        public override void LoadView()
        {
            view = new CircleView();
            View = view;
        }
    }
}
```

Infine, è necessario presentare il controller in fase di esecuzione. Di seguito questo scopo, aggiungere un gestore eventi sul pulsante Invia che è stato aggiunto in precedenza, come segue:

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

A questo punto, quando si esegue l'applicazione e tocca il pulsante di invio, viene visualizzata la nuova vista con un cerchio:

 [![](ios-code-only-images/circles.png "Viene visualizzata la nuova vista con un cerchio")](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>Creazione di una schermata di avvio

Oggetto [schermata di avvio](~/ios/app-fundamentals/images-icons/launch-screens.md) viene visualizzato quando l'app viene avviata in modo da visualizzare agli utenti che sia reattiva. Poiché durante il caricamento di app, viene visualizzata una schermata di avvio, non può essere creata nel codice come l'applicazione è ancora caricato in memoria. 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Quando il creare progetto in Visual Studio, una schermata di avvio viene fornito automaticamente sotto forma di un file .xib, che può trovarsi in iOS il **risorse** cartella all'interno del progetto. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Quando la creazione di un progetto iOS in Visual Studio per Mac, una schermata di avvio viene fornito automaticamente sotto forma di un file di Storyboard. 

-----

Questo può essere modificato dal doppio clic su di esso e aprirlo nella finestra di progettazione iOS.

Si consiglia di Apple che viene utilizzato un .xib o un file di Storyboard per applicazioni destinate a iOS 8 o versioni successive, quando si avvia uno dei file nella finestra di progettazione iOS, si userà le classi di dimensioni e Layout automatico per adattare il layout in modo che l'aspetto ottimale e visualizzato correttamente, per tutti i dispositivi dimensioni. Un'immagine di avvio statico utilizzabile oltre a un .xib o uno Storyboard per consentire al supporto per applicazioni destinate a versioni precedenti.

Per ulteriori informazioni sulla creazione di una schermata di avvio, vedere la documentazione seguente:

- [Creazione di una schermata di avvio utilizzando un .xib](https://developer.xamarin.com/recipes/ios/general/templates/launchscreen-xib/)
- [La gestione delle schermate di avvio con storyboard](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> A partire da iOS 9, Apple consiglia storyboard devono essere utilizzati come il metodo principale di creazione di una schermata di avvio.

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>Creazione di un'immagine di avvio per pre-iOS 8 applicazioni

Se l'applicazione è destinata alle versioni precedenti a iOS 8, è possibile utilizzare un'immagine statica oltre a una schermata di avvio di uno Storyboard o a .xib. 

Nel file Info. plist o come un catalogo di Asset (per iOS 7) nell'applicazione, è possibile impostare questa immagine statica. È necessario fornire immagini separate per ogni dimensione di dispositivo (320 x 480, 640 x 960, 640 x 1136) che è possibile eseguire l'applicazione in. Per ulteriori informazioni sulle dimensioni di una schermata di avvio, visualizzare il [immagini della schermata avvio](~/ios/app-fundamentals/images-icons/launch-screens.md) Guida.

> [!IMPORTANT]
> Se l'applicazione non ha alcuna schermata di avvio, è possibile riscontrare che non rientrano completamente la schermata. In questo caso, accertarsi di includere almeno un'immagine di 640 x 1136 denominata `Default-568@2x.png` per il file Info. plist. 



## <a name="summary"></a>Riepilogo

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

In questo articolo viene illustrato come sviluppare applicazioni iOS a livello di codice in Visual Studio. Abbiamo esaminato come compilare un progetto dal modello di progetto vuoto, che illustrano come creare e aggiungere un controller di visualizzazione principale nella finestra. È stato illustrato come utilizzare i controlli da UIKit per creare una gerarchia di visualizzazione all'interno di un controller per lo sviluppo di una schermata dell'applicazione. Successivamente viene esaminato come rendere le viste vengono disposti in modo appropriato orientamenti diversi ed è stato illustrato come creare una visualizzazione personalizzata creando sottoclassi `UIView`, nonché come caricare la visualizzazione all'interno di un controller. Infine, è esplorare come aggiungere una schermata di avvio a un'applicazione.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

In questo articolo viene descritto come sviluppare applicazioni iOS a livello di codice in Visual Studio per Mac. Abbiamo esaminato come compilare un progetto da un modello di visualizzazione singola, che illustrano come creare e aggiungere un controller di visualizzazione principale nella finestra. È stato illustrato come utilizzare i controlli da UIKit per creare una gerarchia di visualizzazione all'interno di un controller per lo sviluppo di una schermata dell'applicazione. Successivamente viene esaminato come rendere le viste vengono disposti in modo appropriato orientamenti diversi ed è stato illustrato come creare una visualizzazione personalizzata creando sottoclassi `UIView`, nonché come caricare la visualizzazione all'interno di un controller. Infine, è esplorare come aggiungere una schermata di avvio a un'applicazione.

-----




## <a name="related-links"></a>Collegamenti correlati

- [SimpleLogin (esempio)](https://developer.xamarin.com/samples/monotouch/SimpleLogin)
