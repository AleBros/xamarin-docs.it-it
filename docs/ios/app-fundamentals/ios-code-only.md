---
title: Creazione di interfacce utente iOS nel codice in Xamarin.iOS
description: Questo documento descrive come usare il codice per creare un'interfaccia utente per un'app Xamarin.iOS. Vengono illustrati i controller di visualizzazione, la compilazione di una gerarchia di visualizzazione, la gestione di una rotazione e altro ancora.
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: 42a2694239fdd55efa91b7fa30be8a10cafb4cc5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010329"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>Creazione di interfacce utente iOS nel codice in Xamarin.iOS

L'interfaccia utente di un'app iOS è simile a una vetrina: l'applicazione in genere ottiene una finestra, ma può riempire la finestra con il numero di oggetti necessario e gli oggetti e le disposizioni possono essere modificati a seconda di ciò che l'app vuole visualizzare. Gli oggetti di questo scenario, ovvero gli elementi visibili per l'utente, sono chiamati visualizzazioni. Per compilare una singola schermata in un'applicazione, le visualizzazioni sono sovrapposte l'una all'altra in una gerarchia di visualizzazione del contenuto e la gerarchia viene gestita da un unico controller di visualizzazione. Le applicazioni con più schermate hanno più gerarchie di visualizzazione del contenuto, ognuna con un proprio controller di visualizzazione, e l'applicazione inserisce visualizzazioni nella finestra per creare gerarchie di visualizzazione del contenuto diverse in base alla schermata visualizzata dall'utente.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Il diagramma seguente illustra le relazioni tra la finestra, le visualizzazioni, le visualizzazioni secondarie e il controller di visualizzazione che visualizzano l'interfaccia utente sullo schermo del dispositivo:

[![](ios-code-only-images/image9.png "This diagram illustrates the relationships between the Window, Views, Subviews, and View Controller")](ios-code-only-images/image9.png#lightbox)

Queste gerarchie di visualizzazione possono essere costruite usando il [Xamarin designer per iOS](~/ios/user-interface/designer/index.md) in Visual Studio, ma è consigliabile avere una conoscenza di base di come lavorare interamente nel codice. Questo articolo illustra alcuni punti di base per iniziare subito a usare l'interfaccia utente di solo codice.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Il diagramma seguente illustra le relazioni tra la finestra, le visualizzazioni, le visualizzazioni secondarie e il controller di visualizzazione che visualizzano l'interfaccia utente sullo schermo del dispositivo:

[![](ios-code-only-images/image9.png "This diagram illustrates the relationships between the Window, Views, Subviews, and View Controller")](ios-code-only-images/image9.png#lightbox)

Queste gerarchie di visualizzazione possono essere costruite usando il [Xamarin Designer per iOS](~/ios/user-interface/designer/index.md) Visual Studio per Mac, ma è consigliabile avere una conoscenza di base di come lavorare interamente nel codice. Questo articolo illustra alcuni punti di base per iniziare subito a usare l'interfaccia utente di solo codice.

-----

## <a name="creating-a-code-only-project"></a>Creazione di un progetto di solo codice

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="ios-blank-project-template"></a>modello di progetto vuoto iOS

Per prima cosa, creare un progetto iOS in Visual Studio usando il **File > nuovo progetto C# > visual > iPhone & iPad > app iOS (Novell)** , illustrato di seguito:

[![finestra di dialogo nuovo progetto](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

Selezionare quindi il modello di progetto **applicazione vuota** :

[![selezionare una finestra di dialogo modello](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

Il modello di progetto vuoto aggiunge 4 file al progetto:

[![File di progetto](ios-code-only-images/empty-project.w157-sml.png "File di progetto")](ios-code-only-images/empty-project.w157.png#lightbox)

1. **AppDelegate.cs** : contiene una sottoclasse `UIApplicationDelegate`, `AppDelegate`, che viene usata per gestire gli eventi dell'applicazione da iOS. La finestra dell'applicazione viene creata nel metodo `FinishedLaunching` del `AppDelegate`.
1. **Main.cs** : contiene il punto di ingresso per l'applicazione, che specifica la classe per la `AppDelegate`.
1. **Info. plist** : file di elenco delle proprietà che contiene le informazioni di configurazione dell'applicazione.
1. **Titles. plist** : file elenco delle proprietà che contiene informazioni sulle funzionalità e le autorizzazioni dell'applicazione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="ios-templates"></a>modelli iOS

Visual Studio per Mac non fornisce un modello vuoto. Tutti i modelli sono dotati del supporto per storyboard, che Apple consiglia di usare come metodo principale per creare un'interfaccia utente. Tuttavia, è possibile creare completamente l'interfaccia utente nel codice.

La procedura seguente illustra come rimuovere lo storyboard da un'applicazione:

1. Usare il modello di app visualizzazione singola per creare un nuovo progetto iOS:

    [![](ios-code-only-images/single-view-app.png "Use the Single View App template")](ios-code-only-images/single-view-app.png#lightbox)

1. Eliminare i file di `Main.Storyboard` e `ViewController.cs`. **Non** eliminare il `LaunchScreen.Storyboard`. Il controller di visualizzazione deve essere eliminato perché è il code-behind per il controller di visualizzazione creato nello storyboard:
1. Assicurarsi di selezionare **Elimina** dalla finestra di dialogo popup:

    [![](ios-code-only-images/delete.png "Select Delete from the pop-up dialog")](ios-code-only-images/delete.png#lightbox)

1. Nel file INFO. plist eliminare le informazioni all'interno delle informazioni di **distribuzione > opzione di interfaccia principale** :

    [![](ios-code-only-images/main-interface.png "Delete the information inside the Main Interface option")](ios-code-only-images/main-interface.png#lightbox)

1. Aggiungere infine il codice seguente al metodo `FinishedLaunching` nella classe AppDelegate:

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

Il codice aggiunto al metodo `FinishedLaunching` nel passaggio 5 precedente è la quantità minima di codice necessaria per creare una finestra per l'applicazione iOS.

-----

le applicazioni iOS vengono create usando il [modello MVC](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#model-view-controller-mvc). La prima schermata visualizzata da un'applicazione viene creata dal controller visualizzazione radice della finestra. Vedere la Guida di [Hello, iOS Multiscreen](~/ios/get-started/hello-ios-multiscreen/index.md) per altri dettagli sul modello MVC stesso.

L'implementazione per il `AppDelegate` aggiunto dal modello crea la finestra dell'applicazione, di cui esiste solo una per ogni applicazione iOS e la rende visibile con il codice seguente:

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

Se l'applicazione venisse eseguita adesso, è probabile che venga generata un'eccezione che informa che `Application windows are expected to have a root view controller at the end of application launch`. Aggiungere un controller e impostarlo come controller di visualizzazione radice dell'app.

## <a name="adding-a-controller"></a>Aggiunta di un controller

L'app può contenere molti controller di visualizzazione, ma deve disporre di un controller di visualizzazione radice per controllare tutti i controller di visualizzazione.  Aggiungere un controller alla finestra creando un'istanza di `UIViewController` e impostandolo sulla proprietà `Window.RootViewController`:

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

A ogni controller è associata una visualizzazione, accessibile dalla proprietà `View`. Il codice precedente modifica la proprietà `BackgroundColor` della vista in `UIColor.LightGray` in modo che sia visibile, come illustrato di seguito:

 [![](ios-code-only-images/image1.png "The View's background is a visible light gray")](ios-code-only-images/image1.png#lightbox)

In questo modo è possibile impostare anche qualsiasi sottoclasse `UIViewController` come `RootViewController`, inclusi i controller di UIKit e quelli che scriviamo. Il codice seguente, ad esempio, aggiunge un `UINavigationController` come `RootViewController`:

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

In questo modo viene generato il controller annidato all'interno del controller di spostamento, come illustrato di seguito:

 [![](ios-code-only-images/image2.png "The controller nested within the navigation controller")](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>Creazione di un controller di visualizzazione

Ora che abbiamo visto come aggiungere un controller come `RootViewController` della finestra, vediamo come creare un controller di visualizzazione personalizzato nel codice.

Aggiungere una nuova classe denominata `CustomViewController` come illustrato di seguito:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](ios-code-only-images/customviewcontroller.w157-sml.png "Add a new class named CustomViewController")](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](ios-code-only-images/new-file.png "Add a new class named CustomViewController")](ios-code-only-images/new-file.png#lightbox)

-----

La classe deve ereditare da `UIViewController`, che si trova nello spazio dei nomi `UIKit`, come illustrato:

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

## <a name="initializing-the-view"></a>Inizializzazione della visualizzazione

`UIViewController` contiene un metodo denominato `ViewDidLoad` che viene chiamato quando il controller di visualizzazione viene caricato per la prima volta in memoria. Si tratta di una posizione appropriata per l'inizializzazione della visualizzazione, ad esempio l'impostazione delle proprietà.

Il codice seguente, ad esempio, aggiunge un pulsante e un gestore eventi per eseguire il push di un nuovo controller di visualizzazione nello stack di navigazione quando si preme il pulsante:

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

Per caricare il controller nell'applicazione e dimostrare la semplice navigazione, creare una nuova istanza di `CustomViewController`. Creare un nuovo controller di spostamento, passare l'istanza del controller di visualizzazione e impostare il nuovo controller di spostamento sull'`RootViewController` della finestra nel `AppDelegate` come prima:

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

A questo punto, quando viene caricata l'applicazione, il `CustomViewController` viene caricato in un controller di spostamento:

 [![](ios-code-only-images/customvc.png "The CustomViewController is loaded inside a navigation controller")](ios-code-only-images/customvc.png#lightbox)

Se si fa clic sul pulsante, si eseguirà il _push_ di un nuovo controller di visualizzazione nello stack di navigazione:

[![](ios-code-only-images/customvca.png "A new View Controller pushed onto the navigation stack")](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>Compilazione della gerarchia di visualizzazione

Nell'esempio precedente è stata avviata la creazione di un'interfaccia utente nel codice aggiungendo un pulsante al controller di visualizzazione.

le interfacce utente iOS sono costituite da una gerarchia di visualizzazione. Viste aggiuntive, ad esempio etichette, pulsanti, dispositivi di scorrimento e così via, vengono aggiunte come visualizzazioni di una visualizzazione padre.

Modificare ad esempio la `CustomViewController` per creare una schermata di accesso in cui l'utente può immettere un nome utente e una password. La schermata è costituita da due campi di testo e un pulsante.

### <a name="adding-the-text-fields"></a>Aggiunta dei campi di testo

Rimuovere innanzitutto il pulsante e il gestore eventi che è stato aggiunto nella sezione [inizializzazione della visualizzazione](#initializing-the-view) . 

Aggiungere un controllo per il nome utente creando e inizializzando una `UITextField` e quindi aggiungendola alla gerarchia di visualizzazione, come mostrato di seguito:

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

Quando si crea il `UITextField`, si imposta la proprietà `Frame` per definirne la posizione e le dimensioni. In iOS la coordinata 0, 0 si trova in alto a sinistra con + x a destra e + y verso il basso. Dopo aver impostato il `Frame` insieme a un paio di altre proprietà, viene chiamato `View.AddSubview` per aggiungere il `UITextField` alla gerarchia di visualizzazione. In questo modo il `usernameField` una Sottovisualizzazione dell'istanza `UIView` a cui fa riferimento la proprietà `View`. Una sottovista viene aggiunta con un ordine z superiore alla visualizzazione padre, quindi viene visualizzata davanti alla visualizzazione padre sullo schermo.

L'applicazione con il `UITextField` incluso è illustrata di seguito:

 [![](ios-code-only-images/image4.png "The application with the UITextField included")](ios-code-only-images/image4.png#lightbox)

È possibile aggiungere un `UITextField` per la password in modo simile, solo questa volta la proprietà `SecureTextEntry` viene impostata su true, come illustrato di seguito:

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

Impostando `SecureTextEntry = true` si nasconde il testo immesso nel `UITextField` dall'utente, come illustrato di seguito:

 [![](ios-code-only-images/image4a.png "Setting SecureTextEntry true hides the text entered by the user")](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>Aggiunta del pulsante

Successivamente, verrà aggiunto un pulsante in modo che l'utente possa inviare il nome utente e la password. Il pulsante viene aggiunto alla gerarchia di visualizzazione come qualsiasi altro controllo, passandolo come argomento al metodo di `AddSubview` della visualizzazione padre.

Il codice seguente aggiunge il pulsante e registra un gestore eventi per l'evento `TouchUpInside`:

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

A questo punto, la schermata di accesso viene visualizzata come illustrato di seguito:

 [![](ios-code-only-images/image5.png "The login screen")](ios-code-only-images/image5.png#lightbox)

Diversamente dalle versioni precedenti di iOS, lo sfondo del pulsante predefinito è trasparente. La modifica della proprietà `BackgroundColor` del pulsante cambia:

```csharp
submitButton.BackgroundColor = UIColor.White;
```

Questo comporterà un pulsante quadrato anziché il tipico pulsante con bordi arrotondati. Per ottenere il bordo arrotondato, usare il frammento di codice seguente:

```csharp
submitButton.Layer.CornerRadius = 5f;
```

Con queste modifiche, la visualizzazione sarà simile alla seguente:

[![](ios-code-only-images/image6.png "An example run of the view")](ios-code-only-images/image6.png#lightbox)

## <a name="adding-multiple-views-to-the-view-hierarchy"></a>Aggiunta di più visualizzazioni alla gerarchia di visualizzazione

iOS fornisce una funzionalità per aggiungere più visualizzazioni alla gerarchia di visualizzazione utilizzando `AddSubviews`.

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton });
```

## <a name="adding-button-functionality"></a>Aggiunta di funzionalità di pulsante

Quando si fa clic su un pulsante, gli utenti si aspettano che succeda qualcosa. Ad esempio, viene visualizzato un avviso o viene eseguita la navigazione in un'altra schermata.

Aggiungere il codice per eseguire il push di un secondo controller di visualizzazione nello stack di navigazione.

Per prima cosa, creare il secondo controller di visualizzazione:

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

Aggiungere quindi la funzionalità all'evento `TouchUpInside`:

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

La navigazione è illustrata di seguito:

[![](ios-code-only-images/navigation.png "The navigation is illustrated in this chart")](ios-code-only-images/navigation.png#lightbox)

Si noti che, per impostazione predefinita, quando si utilizza un controller di spostamento, iOS assegna all'applicazione una barra di spostamento e un pulsante indietro per consentire di spostarsi di nuovo nello stack.

## <a name="iterating-through-the-view-hierarchy"></a>Iterazione nella gerarchia di visualizzazione

È possibile eseguire l'iterazione nella gerarchia della Sottovisualizzazione e scegliere una vista specifica. Ad esempio, per trovare ogni `UIButton` e assegnare a tale pulsante un'altra `BackgroundColor`, è possibile usare il frammento di codice seguente

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

Questa operazione, tuttavia, non funzionerà se la vista che viene iterata è una `UIView`, perché tutte le visualizzazioni restituiranno una `UIView` poiché gli oggetti aggiunti alla visualizzazione padre ereditano `UIView`.

## <a name="handling-rotation"></a>Gestione della rotazione

Se l'utente ruota il dispositivo in orizzontale, i controlli non vengono ridimensionati in modo appropriato, come illustrato nella schermata seguente:

[![](ios-code-only-images/image7.png "If the user rotates the device to landscape, the controls do not resize appropriately")](ios-code-only-images/image7.png#lightbox)

Un modo per risolvere questo problema consiste nell'impostare la proprietà `AutoresizingMask` per ogni visualizzazione. In questo caso si vuole che i controlli si allungano orizzontalmente, quindi si impostano ogni `AutoresizingMask`. L'esempio seguente è per `usernameField`, ma lo stesso deve essere applicato a ogni gadget nella gerarchia di visualizzazione.

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

A questo punto, quando si ruota il dispositivo o il simulatore, tutto si estende per riempire lo spazio aggiuntivo, come illustrato di seguito:

[![](ios-code-only-images/image8.png "All the controls stretch to fill the additional space")](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>Creazione di visualizzazioni personalizzate

Oltre a usare i controlli che fanno parte di UIKit, è anche possibile usare le visualizzazioni personalizzate. È possibile creare una vista personalizzata ereditando da `UIView` ed eseguendo l'override di `Draw`. Verrà ora creata una visualizzazione personalizzata che verrà aggiunta alla gerarchia di visualizzazione per illustrare.

### <a name="inheriting-from-uiview"></a>Eredità da UIView

Per prima cosa è necessario creare una classe per la visualizzazione personalizzata. Questa operazione verrà eseguita usando il modello di **classe** in Visual Studio per aggiungere una classe vuota denominata `CircleView`. La classe base deve essere impostata su `UIView`, che verrà richiamata nello spazio dei nomi `UIKit`. È necessario anche lo spazio dei nomi `System.Drawing`. Gli altri spazi dei nomi di `System.*` non verranno usati in questo esempio, pertanto è possibile rimuoverli.

La classe dovrebbe essere simile alla seguente:

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

Ogni `UIView` dispone di un metodo di `Draw` chiamato dal sistema quando è necessario disegnarlo. `Draw` non deve mai essere chiamato direttamente. Viene chiamato dal sistema durante l'elaborazione del ciclo di esecuzione. La prima volta che si esegue il ciclo di esecuzione dopo aver aggiunto una vista alla gerarchia di visualizzazione, viene chiamato il metodo `Draw`. Le chiamate successive a `Draw` si verificano quando la vista è contrassegnata per la necessità di essere disegnata chiamando `SetNeedsDisplay` o `SetNeedsDisplayInRect` nella vista.

È possibile aggiungere il codice di disegno alla visualizzazione aggiungendo tale codice all'interno del metodo di `Draw` sottoposto a override, come illustrato di seguito:

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

Poiché `CircleView` è un `UIView`, è anche possibile impostare `UIView` proprietà. Ad esempio, è possibile impostare il `BackgroundColor` nel costruttore:

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

Per usare il `CircleView` appena creato, è possibile aggiungerlo come una Sottovisualizzazione alla gerarchia di visualizzazione in un controller esistente, come è stato fatto con i `UILabels` e `UIButton` in precedenza, oppure è possibile caricarlo come visualizzazione di un nuovo controller. Facciamo la seconda.

### <a name="loading-a-view"></a>Caricamento di una vista

 `UIViewController` dispone di un metodo denominato `LoadView` chiamato dal controller per crearne la visualizzazione. Si tratta di una posizione appropriata per creare una visualizzazione e assegnarla alla proprietà `View` del controller.

Prima di tutto, è necessario un controller, quindi creare una nuova classe vuota denominata `CircleController`.

In `CircleController` aggiungere il codice seguente per impostare il `View` su un `CircleView` (non è necessario chiamare l'implementazione di `base` nell'override):

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

Infine, è necessario presentare il controller in fase di esecuzione. A questo scopo, aggiungere un gestore eventi nel pulsante Invia aggiunto in precedenza, come indicato di seguito:

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

A questo punto, quando si esegue l'applicazione e si tocca il pulsante Invia, viene visualizzata la nuova visualizzazione con un cerchio:

[![](ios-code-only-images/circles.png "The new view with a circle is displayed")](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>Creazione di una schermata di avvio

Viene visualizzata una [schermata di avvio](~/ios/app-fundamentals/images-icons/launch-screens.md) quando l'app viene avviata come modo per visualizzare gli utenti che rispondono. Poiché viene visualizzata una schermata di avvio quando l'app viene caricata, non è possibile crearla nel codice mentre l'applicazione viene ancora caricata in memoria.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Quando si crea un progetto iOS in Visual Studio, viene visualizzata una schermata di avvio sotto forma di file con estensione XIB, disponibile nella cartella **risorse** all'interno del progetto.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Quando si crea un progetto iOS in Visual Studio per Mac, viene visualizzata una schermata di avvio sotto forma di file di storyboard.

-----

Questa operazione può essere modificata facendo doppio clic su di essa e aprendola in iOS designer.

Apple consiglia di usare un file con estensione XIB o storyboard per le applicazioni destinate a iOS 8 o versioni successive, quando si avvia uno dei file in iOS designer, si useranno le classi di dimensioni e il layout automatico per adattare il layout in modo che risulti valido e venga visualizzato correttamente per tutti i dispositivi dimensioni. Un'immagine di avvio statico può essere usata in aggiunta a un XIB o a uno storyboard per consentire il supporto per le applicazioni destinate alle versioni precedenti.

Per ulteriori informazioni sulla creazione di una schermata di avvio, fare riferimento ai documenti seguenti:

- [Creazione di una schermata di avvio usando un XIB](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)
- [Gestione delle schermate di avvio con gli storyboard](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> A partire da iOS 9, Apple consiglia di usare gli storyboard come metodo principale per la creazione di una schermata di avvio.

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>Creazione di un'immagine di avvio per le applicazioni pre-iOS 8

Un'immagine statica può essere usata in aggiunta a una schermata di avvio di XIB o storyboard se l'applicazione ha come destinazione le versioni precedenti a iOS 8.

Questa immagine statica può essere impostata nel file INFO. plist o come catalogo asset (per iOS 7) nell'applicazione. È necessario specificare immagini separate per ogni dimensione del dispositivo (320x480, 640x960, 640X1136) in cui l'applicazione può essere eseguita. Per altre informazioni sulle dimensioni della schermata di avvio, vedere la guida sulle [Immagini di avvio della schermata](~/ios/app-fundamentals/images-icons/launch-screens.md) .

> [!IMPORTANT]
> Se l'app non ha una schermata di avvio, è possibile notare che non è completamente adatta allo schermo. In tal caso, è necessario assicurarsi di includere, almeno, un'immagine 640X1136 denominata `Default-568@2x.png` al file INFO. plist.

## <a name="summary"></a>Riepilogo

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Questo articolo ha illustrato come sviluppare applicazioni iOS a livello di codice in Visual Studio. Si è appreso come creare un progetto da un modello di progetto vuoto, illustrando come creare e aggiungere un controller di visualizzazione radice alla finestra. È stato quindi illustrato come usare i controlli di UIKit per creare una gerarchia di visualizzazione all'interno di un controller per sviluppare una schermata dell'applicazione. In seguito è stato esaminato come rendere le visualizzazioni appropriate in modo appropriato con orientamenti diversi e si è visto come creare una visualizzazione personalizzata mediante la creazione di sottoclassi `UIView`e come caricare la visualizzazione all'interno di un controller. Infine si è esplorato come aggiungere una schermata di avvio a un'applicazione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Questo articolo ha illustrato come sviluppare applicazioni iOS a livello di codice in Visual Studio per Mac. Si è appreso come creare un progetto da un modello di vista singolo, illustrando come creare e aggiungere un controller di visualizzazione radice alla finestra. È stato quindi illustrato come usare i controlli di UIKit per creare una gerarchia di visualizzazione all'interno di un controller per sviluppare una schermata dell'applicazione. In seguito è stato esaminato come rendere le visualizzazioni appropriate in modo appropriato con orientamenti diversi e si è visto come creare una visualizzazione personalizzata mediante la creazione di sottoclassi `UIView`e come caricare la visualizzazione all'interno di un controller. Infine si è esplorato come aggiungere una schermata di avvio a un'applicazione.

-----

## <a name="related-links"></a>Collegamenti correlati

- [SimpleLogin (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplelogin)
