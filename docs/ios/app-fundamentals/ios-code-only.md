---
title: Creazione di interfacce utente di iOS nel codice in xamarin. IOS
description: Questo documento descrive come usare il codice per compilare un'interfaccia utente per un'app xamarin. IOS. Vengono illustrati i controller di visualizzazione, creazione di una gerarchia di visualizzazione, la gestione di una rotazione e altro ancora.
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: 105d06c7cea23ac0873c8de68d7ad1ad62ae1ffc
ms.sourcegitcommit: 84764b9c51e769d6d6570a362af8451607c7e0d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68665706"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>Creazione di interfacce utente iOS nel codice in xamarin. IOS

L'interfaccia utente di un'app per iOS è simile a una vetrina: l'applicazione ottiene in genere una finestra, ma può riempirla con come numero di oggetti necessari, e gli oggetti e disposizioni possono essere modificati in base alla quale l'app desidera visualizzare. Gli oggetti di questo scenario, ovvero gli elementi visibili per l'utente, sono chiamati visualizzazioni. Per compilare una singola schermata in un'applicazione, le visualizzazioni sono accatastati uno su altro in una gerarchia di visualizzazione del contenuto e la gerarchia è gestita da un singolo Controller di visualizzazione. Le applicazioni con più schermate hanno più gerarchie di visualizzazione del contenuto, ognuna con un proprio controller di visualizzazione, e l'applicazione inserisce visualizzazioni nella finestra per creare gerarchie di visualizzazione del contenuto diverse in base alla schermata visualizzata dall'utente.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Il diagramma seguente illustra le relazioni tra la finestra, le visualizzazioni, le visualizzazioni secondarie e il controller di visualizzazione che visualizzano l'interfaccia utente sullo schermo del dispositivo:

[![](ios-code-only-images/image9.png "Questo diagramma illustra le relazioni tra la finestra, viste, le visualizzazioni secondarie e Controller di visualizzazione")](ios-code-only-images/image9.png#lightbox)

Queste gerarchie di visualizzazione possono essere costruite utilizzando il [finestra di progettazione di Xamarin per iOS](~/ios/user-interface/designer/index.md) in Visual Studio, tuttavia è buona norma disporre di una conoscenza di come utilizzare interamente nel codice. Questo articolo illustra alcuni aspetti di base per iniziare subito e in esecuzione con lo sviluppo dell'interfaccia utente di solo codice.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Il diagramma seguente illustra le relazioni tra la finestra, le visualizzazioni, le visualizzazioni secondarie e il controller di visualizzazione che visualizzano l'interfaccia utente sullo schermo del dispositivo:

[![](ios-code-only-images/image9.png "Questo diagramma illustra le relazioni tra la finestra, viste, le visualizzazioni secondarie e Controller di visualizzazione")](ios-code-only-images/image9.png#lightbox)

Queste gerarchie di visualizzazione possono essere costruite utilizzando il [finestra di progettazione di Xamarin per iOS](~/ios/user-interface/designer/index.md) in Visual Studio per Mac, tuttavia è buona norma disporre di una conoscenza di come utilizzare interamente nel codice. Questo articolo illustra alcuni aspetti di base per iniziare subito e in esecuzione con lo sviluppo dell'interfaccia utente di solo codice.

-----

## <a name="creating-a-code-only-project"></a>Creazione di un progetto di solo codice

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="ios-blank-project-template"></a>modello di progetto vuoto di iOS

In primo luogo, creare un progetto iOS in Visual Studio usando il **File > Nuovo progetto > Visual C# > iPhone & iPad > App iOS (Xamarin)** progetto, illustrato di seguito:

[![Finestra di dialogo Nuovo progetto](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

Quindi selezionare il **applicazione vuota** modello di progetto:

[![Selezionare una finestra di dialogo di modello](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

Il modello di progetto vuoto aggiunge 4 file al progetto:

[![I file di progetto](ios-code-only-images/empty-project.w157-sml.png "i file di progetto")](ios-code-only-images/empty-project.w157.png#lightbox)

1. **AppDelegate.cs** -contiene una `UIApplicationDelegate` sottoclasse, `AppDelegate` , che consente di gestire gli eventi dell'applicazione da iOS. La finestra dell'applicazione viene creata nel `AppDelegate`del `FinishedLaunching` (metodo).
1. **Main.cs** -contiene il punto di ingresso per l'applicazione, che specifica la classe per il `AppDelegate` .
1. **File Info. plist** -file elenco proprietà contenente le informazioni di configurazione dell'applicazione.
1. **Entitlements. plist** – file elenco proprietà contenente le informazioni sulle funzionalità e le autorizzazioni dell'applicazione.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="ios-templates"></a>modelli di iOS


Visual Studio per Mac non è incluso un modello vuoto. Tutti i modelli sono dotate di supporto per Storyboard, che Apple consiglia di usare come metodo principale per creare un'interfaccia utente. Tuttavia, è possibile creare l'interfaccia utente completamente nel codice.

I passaggi seguenti consentono di rimuovere lo Storyboard da un'applicazione:


1. Usare il modello di App visualizzazione singola per creare un nuovo progetto iOS:

    [![](ios-code-only-images/single-view-app.png "Usare il modello di App visualizzazione singola")](ios-code-only-images/single-view-app.png#lightbox)

1. Eliminare il `Main.Storyboard` e `ViewController.cs` file. Effettuare **non** Elimina la `LaunchScreen.Storyboard`. Il Controller di visualizzazione deve essere eliminato perché il code-behind per il controller di visualizzazione che viene creato nello Storyboard è:
1. Assicurarsi di selezionare **eliminare** dalla finestra di dialogo popup:

    [![](ios-code-only-images/delete.png "Selezionare Elimina dalla finestra di dialogo popup")](ios-code-only-images/delete.png#lightbox)

1. In Info. plist, eliminare le informazioni all'interno di **le informazioni sulla distribuzione > interfaccia principale** opzione:

    [![](ios-code-only-images/main-interface.png "Eliminare le informazioni all'interno dell'opzione di interfaccia principale")](ios-code-only-images/main-interface.png#lightbox)

1. Infine, aggiungere il codice seguente per il `FinishedLaunching` metodo nella classe AppDelegate:

    ```csharp
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
    ```

Il codice che è stato aggiunto per il `FinishedLaunching` metodo nel passaggio 5 riportato sopra, è la quantità minima di codice necessario per creare una finestra per l'applicazione iOS.

-----

compilazione di applicazioni iOS usando il [pattern MVC](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#model-view-controller-mvc). La prima schermata che visualizza un'applicazione viene creata dal controller di visualizzazione della finestra principale. Vedere le [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md) Guida per altre informazioni su MVC modello stesso.

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
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

Se si intende eseguire ora questa applicazione, si otterrebbe probabilmente un'eccezione generata per informare che `Application windows are expected to have a root view controller at the end of application launch`. È possibile aggiungere un Controller e impostarlo come Controller visualizzazione radice dell'app.

## <a name="adding-a-controller"></a>Aggiunta di un controller

L'app può contenere molti controller di visualizzazione, ma è necessario disporre di un Controller visualizzazione radice per controllare tutti i controller di visualizzazione.  Aggiungere un controller alla finestra tramite la creazione di un `UIViewController` istanza e impostandola sul `Window.RootViewController` proprietà:

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

Tutti i controller ha una visualizzazione associata, accessibile tramite il `View` proprietà. La visualizzazione di modifica del codice sopra riportato `BackgroundColor` proprietà `UIColor.LightGray` in modo che sia visibile, come illustrato di seguito:

 [![](ios-code-only-images/image1.png "Sfondo della visualizzazione è un grigio chiaro visualizzato")](ios-code-only-images/image1.png#lightbox)

Possiamo impostare eventuali `UIViewController` sottoclasse come il `RootViewController` in questo modo, tra cui i controller da UIKit, nonché quelli scriviamo noi stessi. Ad esempio, il codice seguente aggiunge un `UINavigationController` come il `RootViewController`:

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

Questo codice produce il controller annidato all'interno del controller di spostamento, come illustrato di seguito:

 [![](ios-code-only-images/image2.png "Il controller è annidato all'interno di controller di spostamento")](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>Creazione di un controller di visualizzazione

Ora che abbiamo visto come aggiungere un controller come il `RootViewController` della finestra, è possibile vedere come creare un controller di visualizzazione personalizzata nel codice.

Aggiungere una nuova classe denominata `CustomViewController` come illustrato di seguito:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](ios-code-only-images/customviewcontroller.w157-sml.png "Aggiungere una nuova classe denominata CustomViewController")](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

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

## <a name="initializing-the-view"></a>L'inizializzazione della visualizzazione

`UIViewController` contiene un metodo denominato `ViewDidLoad` che viene chiamato quando il controller di visualizzazione viene caricato inizialmente in memoria. Questo è il posto appropriato per eseguire l'inizializzazione della visualizzazione, ad esempio l'impostazione di proprietà.

Ad esempio, il codice seguente aggiunge un pulsante e un gestore eventi per inserire un nuovo Controller di visualizzazione nello stack di navigazione quando viene premuto il pulsante:

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

Per caricare questo controller nell'applicazione e illustrano la navigazione semplice, creare una nuova istanza della `CustomViewController`. Creare un nuovo controller di spostamento, passare l'istanza di controller di visualizzazione e impostare il nuovo controller di spostamento nella finestra `RootViewController` nella `AppDelegate` come indicato in precedenza:

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

A questo punto quando l'applicazione viene caricato, il `CustomViewController` viene caricato all'interno di un controller di spostamento:

 [![](ios-code-only-images/customvc.png "Il CustomViewController viene caricata all'interno di un controller di spostamento")](ios-code-only-images/customvc.png#lightbox)

Fare clic sul pulsante, verrà _push_ un nuovo Controller di visualizzazione nello stack di navigazione:

[![](ios-code-only-images/customvca.png "Un nuovo Controller di visualizzazione nello stack di navigazione")](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>La compilazione della gerarchia di visualizzazione

Nell'esempio precedente, abbiamo iniziato a creare un'interfaccia utente nel codice tramite l'aggiunta di un pulsante al Controller di visualizzazione.

interfacce utente iOS sono costituite da una gerarchia di visualizzazione. Visualizzazioni aggiuntive, ad esempio etichette, i pulsanti, i dispositivi di scorrimento, e così via vengono aggiunti come le visualizzazioni secondarie di alcuni visualizzazione padre.

Ad esempio, è possibile modificare il `CustomViewController` per creare una schermata di accesso in cui l'utente può immettere un nome utente e password. La schermata sarà costituito da due campi di testo e un pulsante.

### <a name="adding-the-text-fields"></a>Aggiunta di campi di testo

Prima di tutto rimuovere il pulsante e il gestore eventi che è stata aggiunta nel [inizializzando la visualizzazione](#initializing-the-view) sezione. 

Aggiungere un controllo per il nome utente tramite la creazione e inizializzazione di un `UITextField` quindi aggiungendola alla gerarchia di visualizzazione, come illustrato di seguito:

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

Quando si crea il `UITextField`, viene impostato il `Frame` proprietà da definire la posizione e dimensioni. In iOS è la coordinata 0.0 in alto a sinistra con + x a destra e + y verso il basso. Dopo aver impostato la `Frame` insieme a due altre proprietà, chiamiamo `View.AddSubview` per aggiungere il `UITextField` alla gerarchia di visualizzazione. In questo modo il `usernameField` una visualizzazione secondaria del `UIView` dell'istanza che il `View` riferimenti alle proprietà. Una visualizzazione secondaria viene aggiunta a un ordine z che è maggiore della visualizzazione padre, in modo che venga visualizzato all'inizio la visualizzazione padre sullo schermo.

L'applicazione con il `UITextField` incluso è illustrato di seguito:

 [![](ios-code-only-images/image4.png "L'applicazione con il UITextField incluso")](ios-code-only-images/image4.png#lightbox)

È possibile aggiungere un `UITextField` per la password in modo analogo, solo questa volta viene impostato il `SecureTextEntry` la proprietà su true, come illustrato di seguito:

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

L'impostazione `SecureTextEntry = true` nasconde il testo immesso nel `UITextField` dall'utente come illustrato di seguito:

 [![](ios-code-only-images/image4a.png "Impostazione SecureTextEntry true consente di nascondere il testo immesso dall'utente")](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>Aggiunta del pulsante

Successivamente, aggiungiamo un pulsante in modo che l'utente può inviare il nome utente e password. Viene aggiunto alla gerarchia di visualizzazione come qualsiasi altro controllo, il pulsante, passarlo come argomento per la visualizzazione padre `AddSubview` metodo nuovamente.

Il codice seguente aggiunge il pulsante e registra un gestore eventi per il `TouchUpInside` evento:

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

A questo punto, la schermata di accesso verrà ora visualizzato come illustrato di seguito:

 [![](ios-code-only-images/image5.png "Schermata di accesso")](ios-code-only-images/image5.png#lightbox)

A differenza delle versioni precedenti di iOS, lo sfondo del pulsante predefinito è trasparente. Modifica del pulsante `BackgroundColor` proprietà cambia questo:

```csharp
submitButton.BackgroundColor = UIColor.White;
```

Ciò comporterà un pulsante quadrato anziché il tipico arrotondato con bordi pulsante. Per ottenere il bordo arrotondato, usare il frammento seguente:

```csharp
submitButton.Layer.CornerRadius = 5f;
```

Con queste modifiche, la visualizzazione avrà un aspetto simile al seguente:

[![](ios-code-only-images/image6.png "Un'esecuzione di esempio della visualizzazione")](ios-code-only-images/image6.png#lightbox)

## <a name="adding-multiple-views-to-the-view-hierarchy"></a>Aggiunta di più visualizzazioni alla gerarchia di visualizzazione

iOS fornisce una funzionalità per aggiungere più viste per la gerarchia di visualizzazione tramite `AddSubviews`.

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton });
```

## <a name="adding-button-functionality"></a>Aggiunta di funzionalità di pulsante

Quando viene selezionato un pulsante, probabile che gli utenti di un intervento. Ad esempio, viene visualizzato un avviso o spostamento viene eseguito a un'altra schermata.

È possibile aggiungere codice per inserire un secondo controller di visualizzazione nello stack di navigazione.

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

Riquadro di spostamento è illustrato di seguito:

[![](ios-code-only-images/navigation.png "La navigazione è illustrata in questo grafico")](ios-code-only-images/navigation.png#lightbox)

Si noti che per impostazione predefinita, quando si usa un Controller di spostamento, iOS fornisce all'applicazione una barra di spostamento e un pulsante Indietro consente di spostare nuovamente attraverso lo stack.

## <a name="iterating-through-the-view-hierarchy"></a>L'iterazione attraverso la gerarchia di visualizzazione

È possibile eseguire l'iterazione attraverso la gerarchia di visualizzazione secondaria e permettere di individuare una visualizzazione specifica. Ad esempio, per trovare ognuno `UIButton` e assegnare un altro pulsante `BackgroundColor`, il frammento seguente può essere usato

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

Questa operazione, tuttavia non funzionerà se la visualizzazione in fase di iterazione per un `UIView` come tutte le visualizzazioni verranno restituiti come un `UIView` degli oggetti aggiunti alla visualizzazione padre di se stessi ereditano `UIView`.

## <a name="handling-rotation"></a>Gestione della rotazione

Se l'utente ruota il dispositivo da verticale a orizzontale, i controlli non vengono ridimensionano in modo appropriato, come illustrato nella schermata seguente:

[![](ios-code-only-images/image7.png "Se l'utente ruota il dispositivo da verticale a orizzontale, i controlli non vengono ridimensionano in modo appropriato")](ios-code-only-images/image7.png#lightbox)

Un modo per risolvere questo problema consiste nell'impostare il `AutoresizingMask` proprietà in ogni visualizzazione. In questo caso vogliamo i controlli per l'estensione in senso orizzontale, pertanto è sufficiente impostare ciascuno `AutoresizingMask`. L'esempio seguente si riferisce `usernameField`, ma lo stesso dovrà essere applicato a ogni gadget nella gerarchia di visualizzazione.

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

A questo punto quando si ruota il dispositivo o simulatore, tutto ciò che si estende per riempire lo spazio aggiuntivo, come illustrato di seguito:

[![](ios-code-only-images/image8.png "Tutti i controlli adattano per riempire lo spazio aggiuntivo")](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>Creazione di viste personalizzate

Oltre a usare i controlli che fanno parte di UIKit, le visualizzazioni personalizzate sono anche utilizzabile. È possibile creare una vista personalizzata ereditando da `UIView` ed eseguire l'override `Draw`. È possibile creare una visualizzazione personalizzata e aggiungerla alla gerarchia di visualizzazione per illustrare.

### <a name="inheriting-from-uiview"></a>Eredità da UIView

La prima cosa che dobbiamo fare è creare una classe per la visualizzazione personalizzata. È possibile eseguire questa operazione usando il **classe** modello in Visual Studio per aggiungere una classe vuota denominata `CircleView`. La classe di base deve essere impostata su `UIView`, che viene richiamata è nel `UIKit` dello spazio dei nomi. Sono necessari anche il `System.Drawing` anche lo spazio dei nomi. Diversi altri `System.*` gli spazi dei nomi non è usato in questo esempio, è possibile rimuoverli.

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

### <a name="drawing-in-a-uiview"></a>Disegno di un UIView

Ogni `UIView` ha un `Draw` metodo chiamato dal sistema quando deve essere disegnato. `Draw` non dovrebbe mai essere chiamato direttamente. Bensì dal sistema durante l'elaborazione di cicli di esecuzione. Alla prima iterazione del ciclo di esecuzione dopo aver aggiunto alla gerarchia di visualizzazione, una vista relativa `Draw` viene chiamato il metodo. Le chiamate successive a `Draw` si verificano quando la visualizzazione è contrassegnata come debba essere disegnato chiamando `SetNeedsDisplay` o `SetNeedsDisplayInRect` nella vista.

È possibile aggiungere codice di disegno alla visualizzazione aggiungendo tale codice sottoposto a override `Draw` metodo come illustrato di seguito:

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

Usare la `CircleView` appena creato, è possibile sia aggiunto come una visualizzazione secondaria la gerarchia di visualizzazione in un controller esistente, come è stato fatto con il `UILabels` e `UIButton` in precedenza, oppure è possibile caricare, come la visualizzazione di un nuovo controller. Di seguito si quest'ultimo.

### <a name="loading-a-view"></a>Il caricamento di una vista

 `UIViewController` contiene un metodo denominato `LoadView` che viene chiamato dal controller per creare la relativa visualizzazione. Questo è il posto appropriato per creare una vista e assegnarla al controller `View` proprietà.

Innanzitutto, è necessario un controller, quindi, creare una nuova classe vuota denominata `CircleController`.

Nella `CircleController` aggiungere il codice seguente per impostare il `View` a un `CircleView` (non è necessario chiamare il `base` implementazione nell'override):

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

Infine, è necessario presentare il controller in fase di esecuzione. È possibile eseguire questa operazione aggiungendo un gestore eventi sul pulsante Invia che abbiamo aggiunto in precedenza, come indicato di seguito:

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

Oggetto [schermata di avvio](~/ios/app-fundamentals/images-icons/launch-screens.md) viene visualizzata all'avvio di app che consente di visualizzare agli utenti che è reattivo. Perché quando l'app viene caricata, viene visualizzata una schermata di avvio, non può essere creata nel codice come l'applicazione è ancora caricata nella memoria.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Quando si crea un progetto iOS in Visual Studio, una schermata di avvio viene fornita automaticamente sotto forma di un file con estensione xib, reperibile nella **risorse** cartella all'interno del progetto.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Quando si crea un progetto iOS in Visual Studio per Mac, una schermata di avvio viene fornita per l'utente sotto forma di un file Storyboard.

-----

Si possono essere modificata doppio clic su di esso e aprendolo in iOS Designer.

Apple consiglia un xib o uno Storyboard viene usato per le applicazioni destinate a iOS 8 o versioni successive, quando si avvia uno dei file in iOS Designer, si userà le classi di dimensioni e il Layout automatico per adattare il layout in modo che la correttezza e visualizzato correttamente, per tutti i dispositivi dimensioni. Un'immagine di avvio statico è utilizzabile, oltre a una con estensione xib o uno Storyboard per consentire al supporto per le applicazioni destinate a versioni precedenti.

Per altre informazioni sulla creazione di una schermata di avvio, vedere la documentazione seguente:

- [Creazione di una schermata di avvio usando un xib](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)
- [La gestione delle schermate di avvio con gli storyboard](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> A partire da iOS 9, Apple consiglia che gli storyboard devono essere utilizzati come metodo principale di creazione di una schermata di avvio.

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>Creazione di un'immagine di avvio per pre-iOS 8 applicazioni

Un'immagine statica è utilizzabile, oltre a una con estensione xib o una schermata di avvio di Storyboard se l'applicazione ha come destinazione le versioni precedenti a iOS 8.

Questa immagine statica può essere impostata nel file Info. plist, o come un catalogo di Asset (per iOS 7) nell'applicazione. È necessario fornire immagini separate per ogni dimensione di dispositivo (320 x 480, 960 x 640, 640 x 1136) che è possibile eseguire l'applicazione in. Per altre informazioni sulle dimensioni dello schermo di avviare, visualizzare il [immagini di avvio schermata](~/ios/app-fundamentals/images-icons/launch-screens.md) Guida.

> [!IMPORTANT]
> Se l'app non dispone di alcuna schermata avvio veloce, è possibile notare che completamente non adattarlo alla schermata. In questo caso, assicurarsi di includere almeno un'immagine di 640 x 1136 denominata `Default-568@2x.png` al file Info. plist.

## <a name="summary"></a>Riepilogo

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Questo articolo ha illustrato come sviluppare applicazioni iOS a livello di codice in Visual Studio. È stato illustrato come compilare un progetto dal modello di progetto vuoto, che illustrano come creare e aggiungere un controller visualizzazione radice alla finestra. È stato quindi illustrato come usare i controlli da UIKit per creare una gerarchia di visualizzazione all'interno di un controller per sviluppare una schermata dell'applicazione. Successivamente abbiamo esaminato come apportare le viste di definire il layout in modo appropriato in diversi orientamenti e abbiamo visto come creare una visualizzazione personalizzata creando sottoclassi `UIView`, nonché come caricare la visualizzazione all'interno di un controller. Infine è stato illustrato come aggiungere una schermata di avvio a un'applicazione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Questo articolo ha illustrato come sviluppare applicazioni iOS a livello di codice in Visual Studio per Mac. È stato illustrato come compilare un progetto da un modello di visualizzazione singola, che illustrano come creare e aggiungere un controller visualizzazione radice alla finestra. È stato quindi illustrato come usare i controlli da UIKit per creare una gerarchia di visualizzazione all'interno di un controller per sviluppare una schermata dell'applicazione. Successivamente abbiamo esaminato come apportare le viste di definire il layout in modo appropriato in diversi orientamenti e abbiamo visto come creare una visualizzazione personalizzata creando sottoclassi `UIView`, nonché come caricare la visualizzazione all'interno di un controller. Infine è stato illustrato come aggiungere una schermata di avvio a un'applicazione.

-----

## <a name="related-links"></a>Collegamenti correlati

- [SimpleLogin (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplelogin)
