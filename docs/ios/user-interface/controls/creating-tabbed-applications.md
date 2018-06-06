---
title: Barre di scheda e barra della scheda controller in xamarin. IOS
description: Questo documento descrive iOS scheda barra controller e come usarli con xamarin. IOS. Viene illustrato come impostare un UITabBarController, utilizzare le immagini, impostare i valori di badge, funzionano con gli eventi e altro ancora.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: d8b096774e60ec0e0b69e109fa5da53c25e66d25
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789758"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Barre di scheda e barra della scheda controller in xamarin. IOS

Applicazioni a schede vengono utilizzate in iOS per supportare le interfacce utente in cui è possibile accedere più schermate in nessun ordine particolare. Tramite il `UITabBarController` (classe), le applicazioni possono facilmente includono il supporto per questi scenari multi-schermati. `UITabBarController` si occupa della gestione di multi-schermata, consentendo allo sviluppatore di applicazioni di concentrarsi sui dettagli di ogni schermata.

In genere, le applicazioni a schede vengono compilate con il `UITabBarController` dal `RootViewController` della finestra principale. Tuttavia, con un bit di codice aggiuntivo, applicazioni a schede possono anche essere utilizzate in successione per alcuni altri schermata iniziale, ad esempio lo scenario in cui un'applicazione viene presentato dapprima una schermata di accesso, seguita dall'interfaccia a schede.

Si esamineranno utilizzando schede qui l'esecuzione di una procedura dettagliata di un'applicazione semplice. Quindi, verrà esaminato come utilizzare le schede non- `RootViewController` scenario.

## <a name="introducing-uitabbarcontroller"></a>Introduzione a UITabBarController

Il `UITabBarController` supporta lo sviluppo di applicazioni a schede per le operazioni seguenti:

-  Consentire l'avvio di più controller da aggiungere.
-  L'interfaccia utente a schede, tramite il `UITabBar` (classe), per consentire all'utente di passare tra i controller e le visualizzazioni. 


Controller vengono aggiunti al `UITabBarController` tramite il relativo `ViewControllers` proprietà, ovvero un `UIViewController` matrice. Il `UITabBarController` stesso gestisce il caricamento il controller appropriato e presenta la visualizzazione in base alla scheda selezionata.

Le schede sono istanze di `UITabBarItem` (classe), che sono contenuti in un `UITabBar` istanza. Ogni `UITabBar` istanza è possibile accedere tramite il `TabBarItem` proprietà del controller in ogni scheda.

Per ottenere le informazioni sulla modalità di utilizzo con il `UITabBarController`, si analizzerà la creazione di un'applicazione semplice che utilizza uno.

## <a name="tabbed-application-walkthrough"></a>Procedura dettagliata dell'applicazione a schede

Per questa procedura dettagliata verrà creato l'applicazione seguente:

[![](creating-tabbed-applications-images/00-app.png "Esempio di app a schede")](creating-tabbed-applications-images/00-app.png#lightbox)

Sebbene non esista già un modello di applicazione a schede disponibili in Visual Studio per Mac, in questo esempio, si intende utilizzare da un progetto vuoto per ottenere una migliore comprensione della modalità di costruzione dell'applicazione.

 <a name="Creating_the_Application" />


### <a name="creating-the-application"></a>Creare l'applicazione

Iniziamo creando una nuova applicazione.

Selezionare il **File > Nuovo > soluzione** voce di menu in Visual Studio per Mac e selezionare un **iOS > App > progetto vuoto** modello, denominare il progetto `TabbedApplication`, come illustrato di seguito:

[![](creating-tabbed-applications-images/newsolution1.png "Selezionare il modello di progetto vuoto")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Denominare il progetto TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)



### <a name="adding-the-uitabbarcontroller"></a>Aggiunta di UITabBarController

Successivamente, aggiungere una classe vuota selezionando **File > Nuovo File** e scegliendo il **generale: classe vuota** modello. Nome del file `TabController` come illustrato di seguito:

[![](creating-tabbed-applications-images/02-newclass.png "Aggiungere la classe TabController")](creating-tabbed-applications-images/02-newclass.png#lightbox)

Il `TabController` contiene l'implementazione della classe di `UITabBarController` che gestirà una matrice di `UIViewControllers`. Quando l'utente seleziona una scheda, il `UITabBarController` si occuperà di presentazione di visualizzazione per il controller di visualizzazione appropriata.

Per implementare il `UITabBarController` è necessario eseguire le operazioni seguenti:

1.  Impostare la classe di base del `TabController` a `UITabBarController` . 
1.  Creare `UIViewController` istanze da aggiungere al `TabController` . 
1.  Aggiungere il `UIViewController` istanze in una matrice assegnato per il `ViewControllers` proprietà del `TabController` . 


Aggiungere il codice seguente per la `TabController` classe per ottenere questi passaggi:

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

Si noti che per ogni `UIViewController` istanza, impostare il `Title` proprietà del `UIViewController`. Quando vengono aggiunti i controller di `UITabBarController`, il `UITabBarController` leggerà il `Title` per ogni controller di e visualizzarli sull'etichetta della scheda associati, come illustrato di seguito:

[![](creating-tabbed-applications-images/00-app.png "Eseguire l'app di esempio")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>Impostazione di TabController come il RootViewController

L'ordine che i controller vengono inseriti nelle schede corrisponde all'ordine in cui vengono aggiunti per il `ViewControllers` matrice.

Per ottenere il `UITabController` per caricare come prima schermata, è necessario renderlo la finestra `RootViewController`, come illustrato nel codice seguente per il `AppDelegate`:

```csharp
[Register ("AppDelegate")]
        public partial class AppDelegate : UIApplicationDelegate
        {
                UIWindow window;
                TabController tabController;

                public override bool FinishedLaunching (UIApplication app, NSDictionary options)
                {
                        window = new UIWindow (UIScreen.MainScreen.Bounds);

                        var tabController = new TabController ();
                        window.RootViewController = tabController;

                        window.MakeKeyAndVisible ();
            
                        return true;
                }
        }
```

Se si esegue l'applicazione a questo punto, il `UITabBarController` verrà caricato con la prima scheda selezionata per impostazione predefinita. Selezionare una delle altre schede, visualizzazione dei risultati del controller associata presentato dal `UITabBarController,` come illustrato di seguito in cui l'utente finale è selezionata la seconda scheda:

[![](creating-tabbed-applications-images/03-secondtab.png "La seconda scheda illustrata")](creating-tabbed-applications-images/03-secondtab.png#lightbox)

 <a name="Modifying_TabBarItems" />


### <a name="modifying-tabbaritems"></a>Modifica TabBarItems

Ora che è disponibile un in esecuzione scheda applicazione, è opportuno modificare il `TabBarItem` per modificare l'immagine e testo che viene visualizzato, nonché per aggiungere un badge a una delle schede.

 <a name="Setting_a_System_Item" />


#### <a name="setting-a-system-item"></a>L'impostazione di un elemento di sistema

In primo luogo, è necessario impostare la scheda prima di utilizzare un elemento di sistema. Nel costruttore del `TabController`, rimuovere la riga che imposta il controller `Title` per il `tab1` istanza e sostituirlo con il codice seguente per impostare il controller `TabBarItem` proprietà:

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

Quando si crea il `UITabBarItem` utilizzando un `UITabBarSystemItem`, il titolo e l'immagine vengono forniti automaticamente da iOS, come illustrato nella schermata seguente mostra il **Preferiti** icona e il titolo nella prima scheda:

 ![](creating-tabbed-applications-images/04a-tabimage.png "La prima scheda con un'icona a stella")

 <a name="Setting_the_Title_and_Image" />


#### <a name="setting-the-title-and-image"></a>Impostare il titolo e l'immagine

Oltre all'utilizzo di un elemento di sistema, il titolo e l'immagine di un `UITabBarItem` può essere impostato su valori personalizzati. Ad esempio, modificare il codice che imposta il `TabBarItem` proprietà del controller denominato `tab2` come indicato di seguito:

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

Il codice precedente presuppone un'immagine denominata `second.png` è stato aggiunto alla radice del progetto in Visual Studio per Mac. In realtà, sono state aggiunte tre immagini al progetto, per soddisfare tutte le soluzioni di dispositivo, come illustrato di seguito:

 [![](creating-tabbed-applications-images/tabbedimages7new.png "Le immagini aggiunti al progetto")](creating-tabbed-applications-images/tabbedimages7new.png#lightbox)

L'immagine di scheda deve essere png 30 x 30 con trasparenza per la risoluzione normale, 60 x 60 per ad alta risoluzione e 90 x 90 per iPhone 6 Plus risoluzione. Nel codice, è necessario solo caricare il file denominato `second.png` iOS caricherà automaticamente la risoluzione elevata uno su dispositivi con un display Retina. È possibile leggere altre informazioni di [utilizzo delle immagini](~/ios/app-fundamentals/images-icons/index.md) Guide. Per impostazione predefinita gli elementi della barra della scheda vengono visualizzati in grigio, con una tonalità blu quando selezionato.

**Nota**

Le immagini precedente è stato possibile aggiungere anche per il **risorse** directory, che è una directory speciale, il cui contenuto verrà automaticamente copiato nella radice del pacchetto di applicazione:

[![](creating-tabbed-applications-images/tabbedapplication8.png "Le immagini come risorse")](creating-tabbed-applications-images/tabbedapplication8.png#lightbox)

Inoltre, quando si imposta il `Title` direttamente sulla proprietà il `TabBarItem`, potrebbe eseguire l'override di qualsiasi valore impostato per `Title` nel controller di se stesso.

Quando si esegue l'applicazione ora, la seconda scheda Mostra il titolo personalizzato e l'immagine come illustrato di seguito:

[![](creating-tabbed-applications-images/05-customtab.png "La seconda scheda con un'icona quadrata")](creating-tabbed-applications-images/05-customtab.png#lightbox)

 <a name="Setting_the_Badge_Value" />


#### <a name="setting-the-badge-value"></a>L'impostazione del valore di Badge

Una scheda è inoltre possibile visualizzare un badge. Ad esempio, aggiungere la seguente riga di codice per impostare un badge nella terza scheda:

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

L'esecuzione di questo comporta un'etichetta rossa con la stringa "Hi" nell'angolo superiore sinistro della scheda, come illustrato di seguito:

[![](creating-tabbed-applications-images/06-badge.png "La seconda scheda con un badge di Hi")](creating-tabbed-applications-images/06-badge.png#lightbox)

Il badge è spesso utilizzato per visualizzare un'indicazione numerica non letta, nuovi elementi. Per rimuovere il badge, impostare il `BadgeValue` su null, come illustrato di seguito:

```csharp
tab3.TabBarItem.BadgeValue = null;
```

 <a name="Tabs_in_Non-RootViewController_Scenarios" />


## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Schede in scenari Non RootViewController

Nell'esempio precedente, è stato illustrato come utilizzare un `UITabBarController` quando è il `RootViewController` della finestra. In questo esempio verrà esaminato come utilizzare un `UITabBarController` quando non è il `RootViewController` e Mostra la modalità di creazione di questo utilizzo degli storyboard.

 <a name="Initial_Screen_Example" />


### <a name="initial-screen-example"></a>Esempio di schermata iniziale

Per questo scenario, la schermata iniziale viene caricata da un controller che non è un `UITabBarController`. Quando l'utente interagisce con la schermata toccando un pulsante, lo stesso Controller di visualizzazione verrà caricato in un `UITabBarController`, che viene quindi presentato all'utente. Nella schermata seguente viene illustrato il flusso dell'applicazione:

[![](creating-tabbed-applications-images/inital-screen-application.png "Questa schermata è riportato il flusso dell'applicazione")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

A questo punto iniziare una nuova applicazione per questo esempio. Nuovamente, verrà usato il **iPhone > App > progetto vuoto (c#)** modello, questa volta il progetto di denominazione `InitialScreenDemo`.


In questo esempio è necessario uno Storyboard per contenere il controller di visualizzazione. Per aggiungere uno Storyboard:

- Fare doppio clic sul nome del progetto e selezionare **Aggiungi > Nuovo File**.

- Quando viene visualizzata la finestra di dialogo Nuovo File, passare a **iOS > vuoto iPhone Storyboard**.

Consente di chiamare questo nuovo Storyboard **MainStoryboard** , come illustrato di seguito: 

[![](creating-tabbed-applications-images/new-file-dialog.png "Aggiungere un file MainStoryboard al progetto")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Esistono alcuni passaggi importanti da tenere presente quando si aggiunge uno Storyboard in un file, in precedenza non storyboard che vengono descritti nel [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md) Guida. Questi sono:

 
1. Aggiungere il nome di uno Storyboard per il **interfaccia principale** sezione il `Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "Impostare l'interfaccia principale MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. Nel `App Delegate`, eseguire l'override del metodo di finestra, con il codice seguente:

    ```csharp
    public override UIWindow Window {
      get;
      set;
    }
    ```

Verrà necessario tre controller di visualizzazione per questo esempio. Uno, denominato `ViewController1`, verrà utilizzato come il Controller di visualizzazione iniziale e nella prima scheda. Le altre due, denominate `ViewController2` e `ViewController3`, che verranno utilizzati nelle schede seconda e terza rispettivamente.

Aprire la finestra di progettazione facendo doppio clic sul file Mainstoryboard e trascinare tre controller vista sull'area di progettazione. Vogliamo ognuno di questi controller di visualizzazione per creare i propri classe corrispondente al nome precedente, in tal caso, in **identità > classe**, digitare il nome, come illustrato nella schermata seguente:

[![](creating-tabbed-applications-images/class-name.png "Impostare la classe su ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio per Mac genererà automaticamente le classi e i file della finestra di progettazione necessari, si possono essere visualizzato nel riquadro soluzione, come illustrato di seguito:

[![](creating-tabbed-applications-images/solution-pad2.png "File generato automaticamente nel progetto")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

 <a name="Creating_the_UI" />


#### <a name="creating-the-ui"></a>Creazione dell'interfaccia utente

Successivamente, si creerà una semplice interfaccia utente per ognuna delle viste del ViewController, utilizzando la finestra di progettazione di iOS Xamarin.

Si vuole trascinare un `Label` e `Button` su ViewController1 dal **della casella degli strumenti** sul lato destro. Successivamente si userà il riquadro proprietà per modificare il nome e il testo dei controlli per le operazioni seguenti:

-  **Etichetta** : `Text`  =  **uno**
-  **Pulsante** : `Title`  =  **utente intraprende un'azione iniziale**


Si verrà controllata la visibilità del pulsante in un `TouchUpInside` evento ed è necessario fare riferimento a esso nel code-behind. Consente di identificare con la **nome** `aButton` nel riquadro proprietà, come illustrato nella schermata seguente:

[![](creating-tabbed-applications-images/abutton-properties.png "Impostare il nome su aButton nel riquadro proprietà")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

L'area di progettazione dovrebbe risultare simile alla seguente:

[![](creating-tabbed-applications-images/design-surface1.png "L'area di progettazione dovrebbe essere simile a questo screenshot")](creating-tabbed-applications-images/design-surface1.png#lightbox)

Aggiungere più da vicino a `ViewController2` e `ViewController3`, aggiunta di un'etichetta a ogni e modificando il testo 'Due' e '3', rispettivamente. Questo consente di evidenziare all'utente che si sta esaminando scheda/visualizzazione.

#### <a name="wiring-up-the-button"></a>Collegamento del pulsante

Si intende caricare `ViewController1` al primo avvio dell'applicazione. Quando l'utente tocca il pulsante, è possibile nascondere il pulsante e caricare un `UITabBarController` con il `ViewController1` istanza nella prima scheda.

Quando l'utente rilascia il `aButton`, si desidera che un evento di TouchUpInside deve essere attivata. Consente di selezionare il pulsante e il **scheda eventi** del riquadro proprietà, dichiarare il gestore dell'evento: `InitialActionCompleted` : in modo sia possibile farvi riferimento nel codice. Questo comportamento è illustrato nella schermata seguente:

[![](creating-tabbed-applications-images/event-handler.png "Quando l'utente rilascia il aButton, attivare un evento TouchUpInside")](creating-tabbed-applications-images/event-handler.png#lightbox)

È ora necessario specificare il Controller di visualizzazione per nascondere il pulsante quando viene generato l'evento `InitialActionCompleted`. In `ViewController1`, aggiungere il seguente metodo parziale:

```csharp
partial void InitialActionCompleted (UIButton sender)
    {
      aButton.Hidden = true;  
    }
```

Salvare il file ed eseguire l'applicazione. Dovremmo vedere venga visualizzato uno schermo e il pulsante scompaiono su ritocchi.

#### <a name="adding-the-tab-bar-controller"></a>Aggiunta della barra della scheda Controller

È ora disponibile la visualizzazione iniziale funziona come previsto. Successivamente, si desidera aggiungerlo a un `UITabBarController`, insieme alle viste 2 e 3. Aprire lo Storyboard nella finestra di progettazione.

Nel **della casella degli strumenti**, cercare il **scheda barra Controller** nel controller di & gli oggetti e trascinare nell'area di progettazione. Come è possibile visualizzare nella schermata seguente, il Controller barra scheda è senza interfaccia utente e pertanto offre due controller di visualizzazione è per impostazione predefinita:

[![](creating-tabbed-applications-images/tabbarcontroller.png "Aggiunta di un Controller di barra di scheda al layout")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Eliminare questi nuovi controller di visualizzazione selezionando la barra di colore nero nella parte inferiore e premendo CANC.

In questo Storyboard, è possibile usare Segues per gestire le transizioni tra il TabBarController e il controller di visualizzazione. Dopo l'interazione con la visualizzazione iniziale, è necessario caricarlo il TabBarController presentato all'utente. Di seguito impostarlo nella finestra di progettazione.

**CTRL + clic** e **trascinare** dal pulsante per la TabBarController. In remoto il mouse, verrà visualizzato un menu di scelta rapida. Si desidera utilizzare un segue modale. 
 
Per impostare i nostri schede, **Ctrl + clic** dal TabBarController per ogni controller la visualizzazione in ordine da uno a tre e selezionare la relazione **scheda** dal menu di scelta rapida, come illustrato di seguito:

[![](creating-tabbed-applications-images/context-menu.png "Selezionare la scheda di relazione")](creating-tabbed-applications-images/context-menu.png#lightbox)

Lo Storyboard deve essere analogo la schermata riportata di seguito:

[![](creating-tabbed-applications-images/segue-layout.png "Lo Storyboard deve essere simile a questa schermata")](creating-tabbed-applications-images/segue-layout.png#lightbox)

Se si fa clic su uno degli elementi della barra della scheda ed Esplora il Pannello proprietà, è possibile visualizzare un numero di opzioni diverse, come illustrato di seguito:

[![](creating-tabbed-applications-images/properties-panel.png "Impostazione delle opzioni della scheda Esplora risorse di proprietà")](creating-tabbed-applications-images/properties-panel.png#lightbox)

Per modificare determinati attributi quali il badge, il titolo e iOS possiamo utilizzare questo [identificatore](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/UIKitUICatalog/TabBarItem.html), tra gli altri

Se si salvare ed eseguirla l'applicazione ora, si noterà che il pulsante viene nuovamente visualizzato quando l'istanza ViewController1 viene caricato il TabBarController. Possibile risolvere il problema, un controllo per verificare se la visualizzazione corrente ha un padre View Controller. In caso affermativo, sappiamo è in corso il TabBarController e pertanto il pulsante deve essere nascosto. Aggiungere il codice seguente alla classe ViewController1:

```csharp
public override void ViewDidLoad ()
{
     if (ParentViewController != null){
       aButton.Hidden = true;
     }

}
```

Quando l'esecuzione dell'applicazione e l'utente tocca il pulsante nella prima schermata, il UITabBarController viene caricato, con la visualizzazione della prima schermata inseriti nella prima scheda, come illustrato di seguito:

[![](creating-tabbed-applications-images/first-view.png "L'output dell'applicazione di esempio")](creating-tabbed-applications-images/first-view.png#lightbox)

<!--Save the files and run the application:

[![](creating-tabbed-applications-images/inital-screen-application.png "Save the files and run the application")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)-->

## <a name="summary"></a>Riepilogo

In questo articolo viene descritto come utilizzare un `UITabBarController` in un'applicazione. È stato illustrato come caricare i controller in ogni scheda e come impostare le proprietà delle schede tale titolo, immagine e badge. Abbiamo quindi esaminato, tramite gli storyboard come caricare un `UITabBarController` in fase di esecuzione quando non è il `RootViewController` della finestra.


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di applicazioni a schede (esempio)](https://developer.xamarin.com/samples/monotouch/CreatingTabbedApplications/)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Riferimento alla classe UITabBarController](http://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
