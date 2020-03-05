---
title: Barre schede e controller di barra schede in xamarin. IOS
description: Questo documento descrive i controller di barra della scheda di iOS e come usarli con xamarin. IOS. Viene illustrato come configurare un UITabBarController, lavorare con immagini, impostare i valori di badge, funzionano con gli eventi e altro ancora.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 25d8563288cce614bc2823b0146e5121688c6f02
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292841"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Barre schede e controller della barra schede in Novell. iOS

Le applicazioni a schede vengono usate in iOS per supportare le interfacce utente in cui è possibile accedere più schermate in nessun ordine particolare. Grazie alla classe `UITabBarController`, le applicazioni possono includere facilmente il supporto per scenari con più schermate. `UITabBarController` si occupa della gestione multischermo, consentendo allo sviluppatore di applicazioni di concentrarsi sui dettagli di ogni schermata.

In genere, le applicazioni a schede sono compilate con il `UITabBarController` `RootViewController` della finestra principale. Tuttavia, con un po' di codice aggiuntivo, le applicazioni a schede sono anche utilizzabile in successione per alcuni altri schermata iniziale, ad esempio lo scenario in cui un'applicazione viene presentato dapprima una schermata di accesso, seguita dall'interfaccia a schede.

In questa pagina vengono illustrati entrambi gli scenari: quando le schede si trovano alla radice della gerarchia di visualizzazione dell'applicazione e anche nello scenario non`RootViewController`.

## <a name="introducing-uitabbarcontroller"></a>Introduzione a UITabBarController

Il `UITabBarController` supporta lo sviluppo di applicazioni a schede da quanto segue:

- Che consente più controller da aggiungere ad esso.
- Fornire un'interfaccia utente a schede, tramite la classe `UITabBar`, per consentire a un utente di spostarsi tra i controller e le visualizzazioni.

I controller vengono aggiunti all'`UITabBarController` tramite la relativa proprietà `ViewControllers`, che è una matrice di `UIViewController`. Il `UITabBarController` stesso gestisce il caricamento del controller appropriato e la visualizzazione in base alla scheda selezionata.

Le schede sono istanze della classe `UITabBarItem`, che sono contenute in un'istanza di `UITabBar`. Ogni istanza di `UITabBar` è accessibile tramite la proprietà `TabBarItem` del controller in ogni scheda.

Per comprendere come usare la `UITabBarController`, è possibile esaminare la creazione di una semplice applicazione che ne usa una.

## <a name="tabbed-application-walkthrough"></a>Procedura dettagliata per l'applicazione a schede

Per questa procedura dettagliata si userà per creare la seguente applicazione:

[![app a schede di esempio](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

Sebbene sia già presente un modello di applicazione a schede disponibile in Visual Studio per Mac, per questo esempio, queste istruzioni funzionano da un progetto vuoto per comprendere meglio la modalità di costruzione dell'applicazione.

### <a name="creating-the-application"></a>Creazione dell'applicazione

Per iniziare, creare una nuova applicazione.

Selezionare la voce di menu **File > nuova soluzione >** in Visual Studio per Mac e selezionare un' **App > iOS > modello di progetto vuoto** , denominare il progetto `TabbedApplication`, come illustrato di seguito:

[![](creating-tabbed-applications-images/newsolution1.png "Select the Empty Project template")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Name the project TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)

### <a name="adding-the-uitabbarcontroller"></a>Aggiunta di UITabBarController

Successivamente, aggiungere una classe vuota selezionando **file > nuovo file** e scegliendo il modello di **classe generale: Empty** . Denominare il file `TabController` come illustrato di seguito:

[![](creating-tabbed-applications-images/02-newclass.png "Add the TabController class")](creating-tabbed-applications-images/02-newclass.png#lightbox)

La classe `TabController` conterrà l'implementazione del `UITabBarController` che gestirà una matrice di `UIViewControllers`. Quando l'utente seleziona una scheda, il `UITabBarController` si occuperà di presentare la visualizzazione per il controller di visualizzazione appropriato.

Per implementare il `UITabBarController` è necessario eseguire le operazioni seguenti:

1. Impostare la classe di base di `TabController` su `UITabBarController`.
1. Creare `UIViewController` istanze da aggiungere al `TabController`.
1. Aggiungere le istanze di `UIViewController` a una matrice assegnata alla proprietà `ViewControllers` dell'`TabController`.

Aggiungere il codice seguente alla classe `TabController` per eseguire questi passaggi:

```csharp
using System;
using UIKit;

namespace TabbedApplication {
    public class TabController : UITabBarController {

        UIViewController tab1, tab2, tab3;

        public TabController ()
        {
            tab1 = new UIViewController();
            tab1.Title = "Green";
            tab1.View.BackgroundColor = UIColor.Green;

            tab2 = new UIViewController();
            tab2.Title = "Orange";
            tab2.View.BackgroundColor = UIColor.Orange;

            tab3 = new UIViewController();
            tab3.Title = "Red";
            tab3.View.BackgroundColor = UIColor.Red;

            var tabs = new UIViewController[] {
                tab1, tab2, tab3
            };

            ViewControllers = tabs;
        }
    }
}
```

Si noti che per ogni istanza di `UIViewController` si imposta la proprietà `Title` dell'`UIViewController`. Quando i controller vengono aggiunti alla `UITabBarController`, il `UITabBarController` leggerà i `Title` per ogni controller e li visualizzerà nell'etichetta della scheda associata, come illustrato di seguito:

[![](creating-tabbed-applications-images/00-app.png "The sample app run")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Impostando il TabController come il RootViewController

L'ordine in cui vengono inseriti i controller nelle schede corrisponde all'ordine in cui vengono aggiunti alla matrice di `ViewControllers`.

Per far caricare il `UITabController` come prima schermata, è necessario impostarlo come `RootViewController`della finestra, come illustrato nel codice seguente per il `AppDelegate`:

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    TabController tabController;

    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        window = new UIWindow (UIScreen.MainScreen.Bounds);

        tabController = new TabController ();
        window.RootViewController = tabController;

        window.MakeKeyAndVisible ();

        return true;
    }
}
```

Se l'applicazione viene eseguita ora, il `UITabBarController` caricherà il primo tab selezionato per impostazione predefinita. Se si seleziona una delle altre schede, nella visualizzazione del controller associato verrà visualizzata la `UITabBarController,`, come illustrato di seguito, in cui l'utente finale ha selezionato la seconda scheda:

![Seconda scheda visualizzata](creating-tabbed-applications-images/03-secondtab-sml.png)

### <a name="modifying-tabbaritems"></a>Modifica TabBarItems

Ora che è disponibile un'applicazione di tabulazione in esecuzione, modificare il `TabBarItem` per modificare l'immagine e il testo visualizzati, nonché per aggiungere una notifica a una delle schede.

#### <a name="setting-a-system-item"></a>Impostazione di un elemento di sistema

Prima di tutto, impostiamo la prima scheda per usare un elemento di sistema. Nel costruttore del `TabController`rimuovere la riga che imposta la `Title` del controller per l'istanza di `tab1` e sostituirla con il codice seguente per impostare la proprietà `TabBarItem` del controller:

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

Quando si crea il `UITabBarItem` usando un `UITabBarSystemItem`, il titolo e l'immagine vengono forniti automaticamente da iOS, come illustrato nella schermata seguente che mostra l'icona e il titolo **Preferiti** nella prima scheda:

 ![Prima scheda con un'icona a stella](creating-tabbed-applications-images/04a-tabimage-sml.png)

#### <a name="setting-the-image"></a>Impostazione dell'immagine

Oltre a usare un elemento di sistema, il titolo e l'immagine di un `UITabBarItem` possono essere impostati su valori personalizzati. Modificare, ad esempio, il codice che imposta la proprietà `TabBarItem` del controller denominato `tab2` come indicato di seguito:

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

Il codice precedente presuppone che sia stata aggiunta un'immagine denominata `second.png` alla radice del progetto (o una directory **delle risorse** ). Per supportare tutte le densità dello schermo, sono necessarie tre immagini, come illustrato di seguito:

![Immagini aggiunte al progetto](creating-tabbed-applications-images/tabbedimages7new.png)

Per informazioni sulle dimensioni corrette, vedere la sezione relativa alle **dimensioni dell'icona della barra delle schede** della [pagina icone personalizzate di Apple](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons/) . Le dimensioni consigliate variano in base allo stile dell'immagine (circolare, quadrato, larghezza o altezza).

È necessario impostare la proprietà `Image` solo sul secondo nome file **. png** , iOS caricherà automaticamente i file di risoluzione più elevati quando necessario. Per altre informazioni, vedere la pagina relativa all'uso delle guide [Immagini](~/ios/app-fundamentals/images-icons/index.md) . Per impostazione predefinita gli elementi delle barra della scheda vengono visualizzati in grigio, con una tonalità blu quando selezionato.

#### <a name="overriding-the-title"></a>Override del titolo

Quando la proprietà `Title` viene impostata direttamente sul `TabBarItem`, eseguirà l'override di qualsiasi valore impostato per `Title` sul controller stesso.

La seconda scheda (intermedia) in questa schermata mostra un titolo e un'immagine personalizzati:

![Seconda scheda con icona quadrata](creating-tabbed-applications-images/05-customtab-sml.png)

#### <a name="setting-the-badge-value"></a>Impostazione del valore del badge

Una scheda è inoltre possibile visualizzare una notifica. Ad esempio, aggiungere la seguente riga di codice per impostare una notifica utente visuale nella terza scheda:

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

Esecuzione di questo comporta un'etichetta rossa con la stringa "Hi" nell'angolo superiore sinistro della scheda come illustrato di seguito:

![Seconda scheda con una notifica Hi](creating-tabbed-applications-images/06-badge-sml.png)

Il badge viene spesso usato per visualizzare un'indicazione numerica non letta, nuovi elementi. Per rimuovere la notifica, impostare il `BadgeValue` su null, come illustrato di seguito:

```csharp
tab3.TabBarItem.BadgeValue = null;
```

## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Schede in scenari non RootViewController

Nell'esempio precedente è stato illustrato come usare un `UITabBarController` quando è il `RootViewController` della finestra. In questo esempio verrà esaminato come utilizzare un `UITabBarController` quando non è il `RootViewController` e viene illustrato come viene creato utilizzare gli storyboard.

### <a name="initial-screen-example"></a>Esempio di schermata iniziale

Per questo scenario, la schermata iniziale viene caricata da un controller che non è un `UITabBarController`. Quando l'utente interagisce con la schermata toccando un pulsante, lo stesso controller di visualizzazione viene caricato in un `UITabBarController`, che viene quindi presentato all'utente. Lo screenshot seguente illustra il flusso dell'applicazione:

[![](creating-tabbed-applications-images/inital-screen-application.png "This screenshot shows the application flow")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

È possibile avviare una nuova applicazione per questo esempio. Anche in questo caso, verrà usato il modello **app > iPhone >C#progetto vuoto ()** , questa volta assegnando il nome `InitialScreenDemo`al progetto.

In questo esempio viene usato uno storyboard per disporre i controller di visualizzazione. Per aggiungere uno storyboard:

- Fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **aggiungi > nuovo file**.

- Quando viene visualizzata la finestra di dialogo nuovo file, passare a **iOS > storyboard iPhone vuoto**.

Chiameremo questo nuovo storyboard **file mainstoryboard** , come illustrato di seguito:

[![](creating-tabbed-applications-images/new-file-dialog.png "Add a MainStoryboard file to the project")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Ci sono alcuni passaggi importanti da tenere presente quando si aggiunge uno storyboard a un file precedentemente non Storyboard, che viene trattato nella Guida [introduttiva agli storyboard](~/ios/user-interface/storyboards/index.md) . ovvero:

1. Aggiungere il nome dello storyboard alla sezione dell' **interfaccia principale** del `Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "Set the Main Interface to MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. Nel `App Delegate`eseguire l'override del metodo Window con il codice seguente:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

Si intende necessari tre controller di visualizzazione per questo esempio. Uno, denominato `ViewController1`, verrà usato come controller di visualizzazione iniziale e nella prima scheda. Gli altri due, denominati `ViewController2` e `ViewController3`, che verranno utilizzati rispettivamente nella seconda e nella terza scheda.

Aprire la finestra di progettazione, fare doppio clic sul file Mainstoryboard e trascinare tre controller di visualizzazione sull'area di progettazione. Si vuole che ognuno di questi controller di visualizzazione abbia una propria classe corrispondente al nome riportato sopra, quindi, in **Identity > Class**, digitare il nome, come illustrato nella schermata seguente:

[![](creating-tabbed-applications-images/class-name.png "Set the Class to ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio per Mac genera automaticamente le classi e i file della finestra di progettazione necessari, può essere visualizzato nel riquadro della soluzione, come illustrato di seguito:

[![](creating-tabbed-applications-images/solution-pad2.png "Auto-generated files in the project")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

#### <a name="creating-the-ui"></a>Creare l'interfaccia utente

Successivamente, si creerà una semplice interfaccia utente per ognuna delle visualizzazioni di ViewController, uso di Xamarin iOS Designer.

Si desidera trascinare un `Label` e un `Button` su ViewController1 dalla **casella degli strumenti** sul lato destro. Successivamente si userà il riquadro delle proprietà per modificare il nome e il testo dei controlli al seguente:

- **Label** : `Text` = **uno**
- **Pulsante** : `Title` = **utente esegue un'azione iniziale**

La visibilità del pulsante verrà controllata in un evento `TouchUpInside` ed è necessario farvi riferimento nel code-behind. Verrà ora identificato con il **nome** `aButton` nel riquadro delle proprietà, come illustrato nello screenshot seguente:

[![](creating-tabbed-applications-images/abutton-properties.png "Set the Name to aButton in the Properties Pad")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

L'area di progettazione a questo punto dovrebbe essere simile allo screenshot seguente:

[![](creating-tabbed-applications-images/design-surface1.png "Your Design Surface should now look similar to this screenshot")](creating-tabbed-applications-images/design-surface1.png#lightbox)

Si aggiungono altri dettagli per `ViewController2` e `ViewController3`, aggiungendo un'etichetta a ognuno di essi e modificando rispettivamente il testo in "Two" e "Three". Ciò evidenzia all'utente quale scheda/visualizzazione si sta esaminando.

#### <a name="wiring-up-the-button"></a>Cablare il pulsante

Al primo avvio dell'applicazione verrà caricato `ViewController1`. Quando l'utente tocca il pulsante, si nasconderà il pulsante e si caricherà un `UITabBarController` con l'istanza di `ViewController1` nella prima scheda.

Quando l'utente rilascia il `aButton`, si vuole che venga attivato un evento TouchUpInside. Selezionare il pulsante e, nella **scheda eventi** del riquadro delle proprietà, dichiarare il gestore dell'evento – `InitialActionCompleted`, in modo che sia possibile farvi riferimento nel codice. Come illustrato nello screenshot seguente:

[![](creating-tabbed-applications-images/event-handler.png "When the user releases the aButton, trigger a TouchUpInside event")](creating-tabbed-applications-images/event-handler.png#lightbox)

È ora necessario indicare al controller di visualizzazione di nascondere il pulsante quando viene generato l'evento `InitialActionCompleted`. In `ViewController1`aggiungere il seguente metodo parziale:

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

Salvare il file ed eseguire l'applicazione. Dovrebbe essere visualizzato uno viene visualizzato sullo schermo e il pulsante non si ripresenta al ritocchi.

#### <a name="adding-the-tab-bar-controller"></a>Aggiunta del controller della barra schede

È ora disponibile la visualizzazione iniziale funziona come previsto. Successivamente, si desidera aggiungerlo a un `UITabBarController`, insieme alle visualizzazioni 2 e 3. È possibile aprire lo Storyboard nella finestra di progettazione.

Nella **casella degli strumenti**cercare il **controller della barra schede** in controller & oggetti e trascinarlo nella area di progettazione. Come illustrato nello screenshot seguente, il Controller di barra schede è senza interfaccia utente e di conseguenza, riporta due controller di visualizzazione con esso per impostazione predefinita:

[![](creating-tabbed-applications-images/tabbarcontroller.png "Adding a Tab Bar Controller to the layout")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Eliminare questi nuovi controller di visualizzazione selezionando la barra nera nella parte inferiore e premendo CANC.

Nel nostro Storyboard, è possibile usare segue per gestire le transizioni tra il TabBarController e ai controller di visualizzazione. Dopo l'interazione con la visualizzazione iniziale, si vuole caricarli nel TabBarController presentati all'utente. È possibile impostarlo nella finestra di progettazione.

**Premere CTRL** e **trascinare** dal pulsante a TabBarController. Nel rilascio del mouse, verrà visualizzato un menu di scelta rapida. Si vuole usare un elemento segue modale.

Per configurare ognuna delle schede, **fare clic con il pulsante destro del mouse su** TabBarController per ognuno dei controller di visualizzazione in ordine da uno a tre e selezionare la **scheda** relazione dal menu di scelta rapida, come illustrato di seguito:

[![](creating-tabbed-applications-images/context-menu.png "Select the Tab Relationship")](creating-tabbed-applications-images/context-menu.png#lightbox)

Lo Storyboard dovrebbe essere simile alla schermata riportata di seguito:

[![](creating-tabbed-applications-images/segue-layout.png "The Storyboard should resemble this screenshot")](creating-tabbed-applications-images/segue-layout.png#lightbox)

Se si fa clic su uno degli elementi della barra scheda ed Esplora Pannello proprietà, è possibile visualizzare un numero di opzioni diverse, come illustrato di seguito:

[![](creating-tabbed-applications-images/properties-panel.png "Setting the tab options in the Properties Explorer")](creating-tabbed-applications-images/properties-panel.png#lightbox)

È possibile usarlo per modificare determinati attributi, ad esempio il badge, il titolo e l'identificatore iOS, tra gli altri.

Se si salva e si esegue l'applicazione ora, verrà individuato che il pulsante viene nuovamente visualizzato quando l'istanza ViewController1 verrà caricato nel TabBarController. È possibile risolvere il problema, controllare se la visualizzazione corrente è un Controller di visualizzazione padre. In caso affermativo, sappiamo che siamo all'interno di TabBarController e pertanto il pulsante deve essere nascosto. È possibile aggiungere il codice seguente alla classe ViewController1:

```csharp
public override void ViewDidLoad ()
{
    if (ParentViewController != null){
        aButton.Hidden = true;
    }
}
```

Quando l'esecuzione dell'applicazione e l'utente tocca il pulsante nella prima schermata, il UITabBarController viene caricato, con la visualizzazione della prima schermata inseriti nella prima scheda, come illustrato di seguito:

[![l'output dell'app di esempio](creating-tabbed-applications-images/first-view-sml.png)](creating-tabbed-applications-images/first-view.png#lightbox)

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare un `UITabBarController` in un'applicazione. È stato illustrato come caricare i controller in ogni scheda e come impostare le proprietà delle schede tale titolo, immagine e badge. Abbiamo quindi esaminato, usando gli storyboard, come caricare un `UITabBarController` in fase di esecuzione quando non è il `RootViewController` della finestra.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di applicazioni a schede (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/creatingtabbedapplications)
- [Images. zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Riferimento alla classe UITabBarController](https://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
