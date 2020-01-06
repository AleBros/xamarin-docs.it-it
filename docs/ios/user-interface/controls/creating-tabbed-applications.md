---
title: Barre schede e controller di barra schede in xamarin. IOS
description: Questo documento descrive i controller di barra della scheda di iOS e come usarli con xamarin. IOS. Viene illustrato come configurare un UITabBarController, lavorare con immagini, impostare i valori di badge, funzionano con gli eventi e altro ancora.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: ad4682e9a3d4de2565bee54ffa159fd739572e24
ms.sourcegitcommit: d8af612b6b3218fea396d2f180e92071c4d4bf92
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663328"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Barre schede e controller della barra schede in Novell. iOS

Le applicazioni a schede vengono usate in iOS per supportare le interfacce utente in cui è possibile accedere più schermate in nessun ordine particolare. Tramite il `UITabBarController` (classe), le applicazioni possono includere facilmente il supporto per questi scenari multi-schermati. `UITabBarController` si occupa della gestione di multi-schermata, che consente allo sviluppatore di concentrarsi sui dettagli di ogni schermata dell'applicazione.

In genere, le applicazioni a schede sono compilate con il `UITabBarController` in fase di `RootViewController` della finestra principale. Tuttavia, con un po' di codice aggiuntivo, le applicazioni a schede sono anche utilizzabile in successione per alcuni altri schermata iniziale, ad esempio lo scenario in cui un'applicazione viene presentato dapprima una schermata di accesso, seguita dall'interfaccia a schede.

In questa pagina vengono illustrati entrambi gli scenari: quando le schede si trovano alla radice della gerarchia di visualizzazione dell'applicazione e anche nello scenario non`RootViewController`.

## <a name="introducing-uitabbarcontroller"></a>Introduzione a UITabBarController

Il `UITabBarController` supporta lo sviluppo di applicazioni a schede dalle condizioni seguenti:

- Che consente più controller da aggiungere ad esso.
- Fornisce un'interfaccia utente a schede, tramite il `UITabBar` (classe), per consentire all'utente di spostarsi tra i controller e le visualizzazioni. 

Controller vengono aggiunti al `UITabBarController` tramite il `ViewControllers` proprietà, ovvero un `UIViewController` matrice. Il `UITabBarController` stesso gestisce il caricamento del controller corretto e presentare la visualizzazione in base al scheda selezionata.

Le schede sono istanze del `UITabBarItem` classe, che sono contenuti in un `UITabBar` istanza. Ciascuna `UITabBar` istanza è accessibile tramite il `TabBarItem` proprietà del controller in ogni scheda.

Per ottenere le informazioni sulla modalità di utilizzo con il `UITabBarController`, esaminiamo in dettaglio la creazione di una semplice applicazione che utilizza uno.

## <a name="tabbed-application-walkthrough"></a>Procedura dettagliata per l'applicazione a schede

Per questa procedura dettagliata si userà per creare la seguente applicazione:

[![app a schede di esempio](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

Sebbene sia già presente un modello di applicazione a schede disponibile in Visual Studio per Mac, per questo esempio, queste istruzioni funzionano da un progetto vuoto per comprendere meglio la modalità di costruzione dell'applicazione.

### <a name="creating-the-application"></a>Creazione dell'applicazione

Per iniziare, creare una nuova applicazione.

Selezionare il **File > New > soluzione** voce di menu in Visual Studio per Mac e selezionare un **iOS > App > progetto vuoto** modello, nome del progetto `TabbedApplication`, come illustrato di seguito:

[![](creating-tabbed-applications-images/newsolution1.png "Select the Empty Project template")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Name the project TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)

### <a name="adding-the-uitabbarcontroller"></a>Aggiunta di UITabBarController

Successivamente, aggiungere una classe vuota selezionando **File > Nuovo File** e scegliendo il **generali: classe vuota** modello. Denominare il file `TabController` come illustrato di seguito:

[![](creating-tabbed-applications-images/02-newclass.png "Add the TabController class")](creating-tabbed-applications-images/02-newclass.png#lightbox)

Il `TabController` classe conterrà l'implementazione del `UITabBarController` che gestiranno una matrice di `UIViewControllers`. Quando l'utente seleziona una scheda, il `UITabBarController` si occuperà di presentare la visualizzazione per il controller di visualizzazione appropriata.

Per implementare il `UITabBarController` dobbiamo eseguire le operazioni seguenti:

1. Impostare la classe di base di `TabController` a `UITabBarController` . 
1. Creare `UIViewController` istanze da aggiungere al `TabController` . 
1. Aggiungere il `UIViewController` istanze in una matrice assegnata per il `ViewControllers` proprietà del `TabController` . 

Aggiungere il codice seguente per il `TabController` classe per ottenere questi passaggi:

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

Si noti che per ogni `UIViewController` istanza, impostiamo il `Title` proprietà del `UIViewController`. Quando i controller vengono aggiunte al `UITabBarController`, il `UITabBarController` leggerà il `Title` per ogni controller e visualizzarlo sull'etichetta della scheda associati, come illustrato di seguito:

[![](creating-tabbed-applications-images/00-app.png "The sample app run")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Impostando il TabController come il RootViewController

L'ordine che i controller vengono inseriti nelle schede corrisponde all'ordine vengono aggiunti al `ViewControllers` matrice.

Per ottenere il `UITabController` per caricare come prima schermata, è necessario renderlo della finestra `RootViewController`, come illustrato nel codice seguente per il `AppDelegate`:

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

Se si esegue l'applicazione a questo punto, il `UITabBarController` verrà caricato con la prima scheda selezionata per impostazione predefinita. Selezione di una delle altre schede del controller associato visualizzazione risultati presentati dal `UITabBarController,` come illustrato di seguito in cui l'utente finale è selezionata la seconda scheda:

![Seconda scheda visualizzata](creating-tabbed-applications-images/03-secondtab-sml.png)

### <a name="modifying-tabbaritems"></a>Modifica TabBarItems

Ora che abbiamo un in esecuzione sulla scheda dell'applicazione, è possibile modificare il `TabBarItem` per modificare l'immagine e testo che viene visualizzato, nonché per aggiungere un badge a una delle schede.

#### <a name="setting-a-system-item"></a>Impostazione di un elemento di sistema

Prima di tutto, impostiamo la prima scheda per usare un elemento di sistema. Nel costruttore della `TabController`, rimuovere la riga che imposta il controller `Title` per il `tab1` dell'istanza e sostituirlo con il codice seguente per impostare il controller `TabBarItem` proprietà:

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

Quando si crea il `UITabBarItem` usando un `UITabBarSystemItem`, il titolo e l'immagine vengono forniti automaticamente da iOS, come illustrato nella schermata seguente che mostra le **Preferiti** icona e il titolo nella prima scheda:

 ![Prima scheda con un'icona a stella](creating-tabbed-applications-images/04a-tabimage-sml.png)

#### <a name="setting-the-image"></a>Impostazione dell'immagine

Oltre a usare un elemento di sistema, il titolo e l'immagine di un `UITabBarItem` può essere impostato su valori personalizzati. Ad esempio, modificare il codice che imposta la `TabBarItem` proprietà del controller denominato `tab2` come indicato di seguito:

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

È necessario impostare la proprietà `Image` solo sul secondo nome file **. png** , iOS caricherà automaticamente i file di risoluzione più elevati quando necessario. È possibile leggere altre informazioni, vedere la [utilizzo di immagini](~/ios/app-fundamentals/images-icons/index.md) Guide. Per impostazione predefinita gli elementi delle barra della scheda vengono visualizzati in grigio, con una tonalità blu quando selezionato.

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

Il badge viene spesso usato per visualizzare un'indicazione numerica non letta, nuovi elementi. Per rimuovere il badge, impostare il `BadgeValue` su null come illustrato di seguito:

```csharp
tab3.TabBarItem.BadgeValue = null;
```

## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Schede in scenari non RootViewController

Nell'esempio precedente, è stato illustrato come lavorare con un `UITabBarController` quando è il `RootViewController` della finestra. In questo esempio verrà esaminato come utilizzare un `UITabBarController` quando non è il `RootViewController` e Mostra come viene creato usare gli storyboard.

### <a name="initial-screen-example"></a>Esempio di schermata iniziale

Per questo scenario, la schermata iniziale viene caricata da un controller che non è un `UITabBarController`. Quando l'utente interagisce con la schermata toccando un pulsante, verrà caricato il Controller di visualizzazione stessa in un `UITabBarController`, che viene quindi presentato all'utente. Lo screenshot seguente mostra il flusso dell'applicazione:

[![](creating-tabbed-applications-images/inital-screen-application.png "This screenshot shows the application flow")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

È possibile avviare una nuova applicazione per questo esempio. Anche in questo caso si userà il **iPhone > App > progetto vuoto (C#)** modello, questa volta il progetto di denominazione `InitialScreenDemo`.

In questo esempio viene usato uno storyboard per disporre i controller di visualizzazione. Per aggiungere uno storyboard:

- Fare doppio clic sul nome del progetto e selezionare **Aggiungi > Nuovo File**.

- Quando viene visualizzata la finestra di dialogo Nuovo File, passare a **iOS > Storyboard iPhone vuoto**.

È possibile chiamare questo nuovo Storyboard **MainStoryboard** , come illustrato di seguito: 

[![](creating-tabbed-applications-images/new-file-dialog.png "Add a MainStoryboard file to the project")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Esistono alcuni passaggi importanti da tenere presente quando si aggiunge uno Storyboard a un file, in precedenza non storyboard che sono illustrati nella [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md) Guida. ovvero:

1. Aggiungere il nome di uno Storyboard per il **interfaccia principale** sezione del `Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "Set the Main Interface to MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. Nel `App Delegate`, eseguire l'override del metodo di finestra, con il codice seguente:

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

Si intende necessari tre controller di visualizzazione per questo esempio. Uno, denominato `ViewController1`, verrà usato come controller di visualizzazione iniziale e nella prima scheda. Gli altri due, denominati `ViewController2` e `ViewController3`, che verranno utilizzati rispettivamente nella seconda e nella terza scheda.

Aprire la finestra di progettazione, fare doppio clic sul file Mainstoryboard e trascinare tre controller di visualizzazione sull'area di progettazione. Si vuole che ognuno di questi controller di visualizzazione disponga della propria classe corrispondente al nome precedente, in questo caso, sotto **identità > classe**, digitare il nome, come illustrato nello screenshot seguente:

[![](creating-tabbed-applications-images/class-name.png "Set the Class to ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio per Mac genera automaticamente le classi e i file della finestra di progettazione necessari, può essere visualizzato nel riquadro della soluzione, come illustrato di seguito:

[![](creating-tabbed-applications-images/solution-pad2.png "Auto-generated files in the project")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

#### <a name="creating-the-ui"></a>Creare l'interfaccia utente

Successivamente, si creerà una semplice interfaccia utente per ognuna delle visualizzazioni di ViewController, uso di Xamarin iOS Designer.

Si vuole trascinare un `Label` e una `Button` in ViewController1 dal **della casella degli strumenti** sul lato destro. Successivamente si userà il riquadro delle proprietà per modificare il nome e il testo dei controlli al seguente:

- **Etichetta** : `Text`  =  **uno**
- **Pulsante** : `Title`  =  **utente esegue alcune operazioni iniziali**

Si verrà controllata la visibilità del pulsante in un `TouchUpInside` evento ed è necessario fare riferimento a esso nel code-behind. È possibile identificare con il **Name** `aButton` nel riquadro delle proprietà, come illustrato nello screenshot seguente:

[![](creating-tabbed-applications-images/abutton-properties.png "Set the Name to aButton in the Properties Pad")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

L'area di progettazione a questo punto dovrebbe essere simile allo screenshot seguente:

[![](creating-tabbed-applications-images/design-surface1.png "Your Design Surface should now look similar to this screenshot")](creating-tabbed-applications-images/design-surface1.png#lightbox)

Aggiungiamo un piccolo dettaglio da `ViewController2` e `ViewController3`mediante l'aggiunta di un'etichetta a ogni e la modifica del testo per 'Due' e '3', rispettivamente. Ciò evidenzia all'utente quale scheda/visualizzazione si sta esaminando.

#### <a name="wiring-up-the-button"></a>Cablare il pulsante

Dobbiamo caricare `ViewController1` al primo avvio dell'applicazione. Quando l'utente tocca il pulsante, è possibile nascondere il pulsante e caricare un `UITabBarController` con il `ViewController1` istanza nella prima scheda.

Quando l'utente rilascia il `aButton`, vogliamo un evento TouchUpInside venga attivato. È possibile selezionare il pulsante e nel **scheda eventi** del riquadro delle proprietà, dichiarare il gestore dell'evento – `InitialActionCompleted` : in modo che sia possibile farvi riferimento nel codice. Come illustrato nello screenshot seguente:

[![](creating-tabbed-applications-images/event-handler.png "When the user releases the aButton, trigger a TouchUpInside event")](creating-tabbed-applications-images/event-handler.png#lightbox)

È ora necessario indicare il Controller di visualizzazione per nascondere il pulsante quando viene generato l'evento `InitialActionCompleted`. In `ViewController1`, aggiungere il metodo parziale seguente:

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

Salvare il file ed eseguire l'applicazione. Dovrebbe essere visualizzato uno viene visualizzato sullo schermo e il pulsante non si ripresenta al ritocchi.

#### <a name="adding-the-tab-bar-controller"></a>Aggiunta del controller della barra schede

È ora disponibile la visualizzazione iniziale funziona come previsto. Successivamente, si vuole aggiungerlo a un `UITabBarController`, insieme a visualizzazioni 2 e 3. È possibile aprire lo Storyboard nella finestra di progettazione.

Nel **casella degli strumenti**, cercare il **Controller di barra schede** sotto i controller e gli oggetti e trascinare nell'area di progettazione. Come illustrato nello screenshot seguente, il Controller di barra schede è senza interfaccia utente e di conseguenza, riporta due controller di visualizzazione con esso per impostazione predefinita:

[![](creating-tabbed-applications-images/tabbarcontroller.png "Adding a Tab Bar Controller to the layout")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Eliminare questi nuovi controller di visualizzazione selezionando la barra nera nella parte inferiore e premendo CANC.

Nel nostro Storyboard, è possibile usare segue per gestire le transizioni tra il TabBarController e ai controller di visualizzazione. Dopo l'interazione con la visualizzazione iniziale, si vuole caricarli nel TabBarController presentati all'utente. È possibile impostarlo nella finestra di progettazione.

**CTRL + clic** e **trascinamento** dal pulsante per la TabBarController. Nel rilascio del mouse, verrà visualizzato un menu di scelta rapida. Si vuole usare un elemento segue modale. 

Per configurare ognuno dei nostri schede **Ctrl + clic** dal TabBarController a ognuno dei nostri i controller di visualizzazione in ordine da uno a tre e selezionare la relazione **scheda** dal menu di scelta rapida, come illustrato di seguito:

[![](creating-tabbed-applications-images/context-menu.png "Select the Tab Relationship")](creating-tabbed-applications-images/context-menu.png#lightbox)

Lo Storyboard dovrebbe essere simile alla schermata riportata di seguito:

[![](creating-tabbed-applications-images/segue-layout.png "The Storyboard should resemble this screenshot")](creating-tabbed-applications-images/segue-layout.png#lightbox)

Se si fa clic su uno degli elementi della barra scheda ed Esplora Pannello proprietà, è possibile visualizzare un numero di opzioni diverse, come illustrato di seguito:

[![](creating-tabbed-applications-images/properties-panel.png "Setting the tab options in the Properties Explorer")](creating-tabbed-applications-images/properties-panel.png#lightbox)

Possiamo utilizzare questo per modificare determinati attributi quali la notifica, il titolo e iOS [identificatore](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/UIKitUICatalog/TabBarItem.html), tra gli altri

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

Questo articolo ha illustrato come utilizzare un `UITabBarController` in un'applicazione. È stato illustrato come caricare i controller in ogni scheda e come impostare le proprietà delle schede tale titolo, immagine e badge. Abbiamo quindi esaminato, usando gli storyboard, come caricare un `UITabBarController` in fase di esecuzione quando non è il `RootViewController` della finestra.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di applicazioni a schede (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/creatingtabbedapplications)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Riferimento alla classe UITabBarController](https://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
