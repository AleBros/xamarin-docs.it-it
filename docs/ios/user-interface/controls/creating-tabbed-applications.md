---
title: Barre schede e controller di barra schede in xamarin. IOS
description: Questo documento descrive i controller di barra della scheda di iOS e come usarli con xamarin. IOS. Viene illustrato come configurare un UITabBarController, lavorare con immagini, impostare i valori di badge, funzionano con gli eventi e altro ancora.
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: e4ab46994ed25daaef95a709e4f9df94f3a21cd0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114678"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Barre schede e controller di barra schede in xamarin. IOS

Le applicazioni a schede vengono usate in iOS per supportare le interfacce utente in cui è possibile accedere più schermate in nessun ordine particolare. Tramite il `UITabBarController` (classe), le applicazioni possono includere facilmente il supporto per questi scenari multi-schermati. `UITabBarController` si occupa della gestione di multi-schermata, che consente allo sviluppatore di concentrarsi sui dettagli di ogni schermata dell'applicazione.

In genere, le applicazioni a schede sono compilate con il `UITabBarController` in fase di `RootViewController` della finestra principale. Tuttavia, con un po' di codice aggiuntivo, le applicazioni a schede sono anche utilizzabile in successione per alcuni altri schermata iniziale, ad esempio lo scenario in cui un'applicazione viene presentato dapprima una schermata di accesso, seguita dall'interfaccia a schede.

Verrà esaminato uso schede qui usando una procedura dettagliata di una semplice applicazione. Successivamente, esamineremo come lavorare con le schede in non `RootViewController` scenario.

## <a name="introducing-uitabbarcontroller"></a>Introduzione a UITabBarController

Il `UITabBarController` supporta lo sviluppo di applicazioni a schede dalle condizioni seguenti:

-  Che consente più controller da aggiungere ad esso.
-  Fornisce un'interfaccia utente a schede, tramite il `UITabBar` (classe), per consentire all'utente di spostarsi tra i controller e le visualizzazioni. 


Controller vengono aggiunti al `UITabBarController` tramite il `ViewControllers` proprietà, ovvero un `UIViewController` matrice. Il `UITabBarController` stesso gestisce il caricamento del controller corretto e presentare la visualizzazione in base al scheda selezionata.

Le schede sono istanze del `UITabBarItem` classe, che sono contenuti in un `UITabBar` istanza. Ciascuna `UITabBar` istanza è accessibile tramite il `TabBarItem` proprietà del controller in ogni scheda.

Per ottenere le informazioni sulla modalità di utilizzo con il `UITabBarController`, esaminiamo in dettaglio la creazione di una semplice applicazione che utilizza uno.

## <a name="tabbed-application-walkthrough"></a>Procedura dettagliata di App a schede

Per questa procedura dettagliata si userà per creare la seguente applicazione:

[![](creating-tabbed-applications-images/00-app.png "Esempio app a schede")](creating-tabbed-applications-images/00-app.png#lightbox)

Sebbene non esista già un modello di App a schede disponibili in Visual Studio per Mac, in questo esempio, dobbiamo lavorare da un progetto vuoto per ottenere una migliore comprensione del modo in cui viene costruita l'applicazione.

 <a name="Creating_the_Application" />


### <a name="creating-the-application"></a>Creare l'applicazione

Iniziamo creando una nuova applicazione.

Selezionare il **File > New > soluzione** voce di menu in Visual Studio per Mac e selezionare un **iOS > App > progetto vuoto** modello, nome del progetto `TabbedApplication`, come illustrato di seguito:

[![](creating-tabbed-applications-images/newsolution1.png "Selezionare il modello di progetto vuoto")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Denominare il progetto TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)



### <a name="adding-the-uitabbarcontroller"></a>Aggiunta di UITabBarController

Successivamente, aggiungere una classe vuota selezionando **File > Nuovo File** e scegliendo il **generali: classe vuota** modello. Denominare il file `TabController` come illustrato di seguito:

[![](creating-tabbed-applications-images/02-newclass.png "Aggiungere la classe TabController")](creating-tabbed-applications-images/02-newclass.png#lightbox)

Il `TabController` classe conterrà l'implementazione del `UITabBarController` che gestiranno una matrice di `UIViewControllers`. Quando l'utente seleziona una scheda, il `UITabBarController` si occuperà di presentare la visualizzazione per il controller di visualizzazione appropriata.

Per implementare il `UITabBarController` dobbiamo eseguire le operazioni seguenti:

1.  Impostare la classe di base di `TabController` a `UITabBarController` . 
1.  Creare `UIViewController` istanze da aggiungere al `TabController` . 
1.  Aggiungere il `UIViewController` istanze in una matrice assegnata per il `ViewControllers` proprietà del `TabController` . 


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

[![](creating-tabbed-applications-images/00-app.png "Eseguire l'app di esempio")](creating-tabbed-applications-images/00-app.png#lightbox)

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

                        var tabController = new TabController ();
                        window.RootViewController = tabController;

                        window.MakeKeyAndVisible ();
            
                        return true;
                }
        }
```

Se si esegue l'applicazione a questo punto, il `UITabBarController` verrà caricato con la prima scheda selezionata per impostazione predefinita. Selezione di una delle altre schede del controller associato visualizzazione risultati presentati dal `UITabBarController,` come illustrato di seguito in cui l'utente finale è selezionata la seconda scheda:

[![](creating-tabbed-applications-images/03-secondtab.png "La seconda scheda illustrata")](creating-tabbed-applications-images/03-secondtab.png#lightbox)

 <a name="Modifying_TabBarItems" />


### <a name="modifying-tabbaritems"></a>Modifica TabBarItems

Ora che abbiamo un in esecuzione sulla scheda dell'applicazione, è possibile modificare il `TabBarItem` per modificare l'immagine e testo che viene visualizzato, nonché per aggiungere un badge a una delle schede.

 <a name="Setting_a_System_Item" />


#### <a name="setting-a-system-item"></a>L'impostazione di un elemento di sistema

Prima di tutto, impostiamo la prima scheda per usare un elemento di sistema. Nel costruttore della `TabController`, rimuovere la riga che imposta il controller `Title` per il `tab1` dell'istanza e sostituirlo con il codice seguente per impostare il controller `TabBarItem` proprietà:

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

Quando si crea il `UITabBarItem` usando un `UITabBarSystemItem`, il titolo e l'immagine vengono forniti automaticamente da iOS, come illustrato nella schermata seguente che mostra le **Preferiti** icona e il titolo nella prima scheda:

 ![](creating-tabbed-applications-images/04a-tabimage.png "La prima scheda con un'icona a stella")

 <a name="Setting_the_Title_and_Image" />


#### <a name="setting-the-title-and-image"></a>Impostare il titolo e l'immagine

Oltre a usare un elemento di sistema, il titolo e l'immagine di un `UITabBarItem` può essere impostato su valori personalizzati. Ad esempio, modificare il codice che imposta la `TabBarItem` proprietà del controller denominato `tab2` come indicato di seguito:

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

Il codice sopra riportato si presuppone che un'immagine denominata `second.png` è stato aggiunto alla radice del progetto in Visual Studio per Mac. In realtà, sono stati aggiunti tre immagini al progetto, per coprire tutte le risoluzioni di dispositivo, come illustrato di seguito:

 [![](creating-tabbed-applications-images/tabbedimages7new.png "Le immagini aggiunto al progetto")](creating-tabbed-applications-images/tabbedimages7new.png#lightbox)

L'immagine di scheda deve essere 30x30 png con trasparenza per la risoluzione normale, 60 x 60 per ad alta risoluzione e 90 x 90 per iPhone 6 Plus risoluzione. Nel nostro codice, è sufficiente caricare il file denominato `second.png` e iOS lo caricherà automaticamente la risoluzione elevata uno nei dispositivi con un display Retina. È possibile leggere altre informazioni, vedere la [utilizzo di immagini](~/ios/app-fundamentals/images-icons/index.md) Guide. Per impostazione predefinita gli elementi delle barra della scheda vengono visualizzati in grigio, con una tonalità blu quando selezionato.

**Nota**

Sono anche possibile aggiungere immagini riportate in precedenza per il **risorse** directory, che è una directory speciale il cui contenuto verrà copiato automaticamente nella radice del bundle dell'applicazione:

[![](creating-tabbed-applications-images/tabbedapplication8.png "Le immagini come risorse")](creating-tabbed-applications-images/tabbedapplication8.png#lightbox)

Inoltre, quando è impostata la `Title` direttamente sulla proprietà il `TabBarItem`, avrebbe sosvrascritto qualsiasi valore impostato per `Title` nel controller di se stesso.

Quando si esegue l'applicazione a questo punto, la seconda scheda Mostra il titolo personalizzato e l'immagine come illustrato di seguito:

[![](creating-tabbed-applications-images/05-customtab.png "La seconda scheda con un'icona quadrata")](creating-tabbed-applications-images/05-customtab.png#lightbox)

 <a name="Setting_the_Badge_Value" />


#### <a name="setting-the-badge-value"></a>Impostazione del valore di Badge

Una scheda è inoltre possibile visualizzare una notifica. Ad esempio, aggiungere la seguente riga di codice per impostare una notifica utente visuale nella terza scheda:

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

Esecuzione di questo comporta un'etichetta rossa con la stringa "Hi" nell'angolo superiore sinistro della scheda come illustrato di seguito:

[![](creating-tabbed-applications-images/06-badge.png "La seconda scheda con un badge di Hi")](creating-tabbed-applications-images/06-badge.png#lightbox)

Il badge viene spesso usato per visualizzare un'indicazione numerica non letta, nuovi elementi. Per rimuovere il badge, impostare il `BadgeValue` su null come illustrato di seguito:

```csharp
tab3.TabBarItem.BadgeValue = null;
```

 <a name="Tabs_in_Non-RootViewController_Scenarios" />


## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>Schede in scenari Non RootViewController

Nell'esempio precedente, è stato illustrato come lavorare con un `UITabBarController` quando è il `RootViewController` della finestra. In questo esempio verrà esaminato come utilizzare un `UITabBarController` quando non è il `RootViewController` e Mostra come viene creato usare gli storyboard.

 <a name="Initial_Screen_Example" />


### <a name="initial-screen-example"></a>Esempio di schermata iniziale

Per questo scenario, la schermata iniziale viene caricata da un controller che non è un `UITabBarController`. Quando l'utente interagisce con la schermata toccando un pulsante, verrà caricato il Controller di visualizzazione stessa in un `UITabBarController`, che viene quindi presentato all'utente. Lo screenshot seguente mostra il flusso dell'applicazione:

[![](creating-tabbed-applications-images/inital-screen-application.png "Questo screenshot Mostra il flusso dell'applicazione")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

È possibile avviare una nuova applicazione per questo esempio. Anche in questo caso si userà il **iPhone > App > progetto vuoto (C#)** modello, questa volta il progetto di denominazione `InitialScreenDemo`.


In questo esempio è necessario uno Storyboard per contenere il controller di visualizzazione. Per aggiungere uno Storyboard:

- Fare doppio clic sul nome del progetto e selezionare **Aggiungi > Nuovo File**.

- Quando viene visualizzata la finestra di dialogo Nuovo File, passare a **iOS > Storyboard iPhone vuoto**.

È possibile chiamare questo nuovo Storyboard **MainStoryboard** , come illustrato di seguito: 

[![](creating-tabbed-applications-images/new-file-dialog.png "Aggiungere un file MainStoryboard al progetto")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

Esistono alcuni passaggi importanti da tenere presente quando si aggiunge uno Storyboard a un file, in precedenza non storyboard che sono illustrati nella [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md) Guida. Questi sono:

 
1. Aggiungere il nome di uno Storyboard per il **interfaccia principale** sezione del `Info.plist`:

    [![](creating-tabbed-applications-images/project-options.png "Impostare l'interfaccia principale per MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. Nel `App Delegate`, eseguire l'override del metodo di finestra, con il codice seguente:

    ```csharp
    public override UIWindow Window {
      get;
      set;
    }
    ```

Si intende necessari tre controller di visualizzazione per questo esempio. Uno, denominato `ViewController1`, verrà usato come Controller visualizzazione iniziale e nella prima scheda. Gli altri due, denominati `ViewController2` e `ViewController3`, che verrà usato nelle schede del seconda e terza rispettivamente.

Aprire la finestra di progettazione, fare doppio clic sul file Mainstoryboard e trascinare tre controller di visualizzazione sull'area di progettazione. Si vuole che ognuno di questi controller di visualizzazione disponga della propria classe corrispondente al nome precedente, in questo caso, sotto **identità > classe**, digitare il nome, come illustrato nello screenshot seguente:

[![](creating-tabbed-applications-images/class-name.png "Impostare la classe a ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio per Mac genera automaticamente le classi e i file della finestra di progettazione necessari, può essere visualizzato nel riquadro della soluzione, come illustrato di seguito:

[![](creating-tabbed-applications-images/solution-pad2.png "File generati automaticamente nel progetto")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

 <a name="Creating_the_UI" />


#### <a name="creating-the-ui"></a>Creare l'interfaccia utente

Successivamente, si creerà una semplice interfaccia utente per ognuna delle visualizzazioni di ViewController, uso di Xamarin iOS Designer.

Si vuole trascinare un `Label` e una `Button` in ViewController1 dal **della casella degli strumenti** sul lato destro. Successivamente si userà il riquadro delle proprietà per modificare il nome e il testo dei controlli al seguente:

-  **Etichetta** : `Text`  =  **uno**
-  **Pulsante** : `Title`  =  **utente esegue alcune operazioni iniziali**


Si verrà controllata la visibilità del pulsante in un `TouchUpInside` evento ed è necessario fare riferimento a esso nel code-behind. È possibile identificare con il **Name** `aButton` nel riquadro delle proprietà, come illustrato nello screenshot seguente:

[![](creating-tabbed-applications-images/abutton-properties.png "Impostare il nome su aButton nel riquadro delle proprietà")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

L'area di progettazione a questo punto dovrebbe essere simile allo screenshot seguente:

[![](creating-tabbed-applications-images/design-surface1.png "L'area di progettazione a questo punto dovrebbe essere simile a questo screenshot")](creating-tabbed-applications-images/design-surface1.png#lightbox)

Aggiungiamo un piccolo dettaglio da `ViewController2` e `ViewController3`mediante l'aggiunta di un'etichetta a ogni e la modifica del testo per 'Due' e '3', rispettivamente. Ciò evidenzia all'utente quale scheda/visualizzazione si sta esaminando.

#### <a name="wiring-up-the-button"></a>Collegare il pulsante

Dobbiamo caricare `ViewController1` al primo avvio dell'applicazione. Quando l'utente tocca il pulsante, è possibile nascondere il pulsante e caricare un `UITabBarController` con il `ViewController1` istanza nella prima scheda.

Quando l'utente rilascia il `aButton`, vogliamo un evento TouchUpInside venga attivato. È possibile selezionare il pulsante e nel **scheda eventi** del riquadro delle proprietà, dichiarare il gestore dell'evento – `InitialActionCompleted` : in modo che sia possibile farvi riferimento nel codice. Come illustrato nello screenshot seguente:

[![](creating-tabbed-applications-images/event-handler.png "Quando l'utente rilascia il aButton, attivare un evento TouchUpInside")](creating-tabbed-applications-images/event-handler.png#lightbox)

È ora necessario indicare il Controller di visualizzazione per nascondere il pulsante quando viene generato l'evento `InitialActionCompleted`. In `ViewController1`, aggiungere il metodo parziale seguente:

```csharp
partial void InitialActionCompleted (UIButton sender)
    {
      aButton.Hidden = true;  
    }
```

Salvare il file ed eseguire l'applicazione. Dovrebbe essere visualizzato uno viene visualizzato sullo schermo e il pulsante non si ripresenta al ritocchi.

#### <a name="adding-the-tab-bar-controller"></a>Aggiunta la barra di schede Controller

È ora disponibile la visualizzazione iniziale funziona come previsto. Successivamente, si vuole aggiungerlo a un `UITabBarController`, insieme a visualizzazioni 2 e 3. È possibile aprire lo Storyboard nella finestra di progettazione.

Nel **casella degli strumenti**, cercare il **Controller di barra schede** sotto i controller e gli oggetti e trascinare nell'area di progettazione. Come illustrato nello screenshot seguente, il Controller di barra schede è senza interfaccia utente e di conseguenza, riporta due controller di visualizzazione con esso per impostazione predefinita:

[![](creating-tabbed-applications-images/tabbarcontroller.png "Aggiunta di un Controller di barra schede per il layout")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

Eliminare questi nuovi controller di visualizzazione selezionando la barra nera nella parte inferiore e premendo CANC.

Nel nostro Storyboard, è possibile usare segue per gestire le transizioni tra il TabBarController e ai controller di visualizzazione. Dopo l'interazione con la visualizzazione iniziale, si vuole caricarli nel TabBarController presentati all'utente. È possibile impostarlo nella finestra di progettazione.

**CTRL + clic** e **trascinamento** dal pulsante per la TabBarController. Nel rilascio del mouse, verrà visualizzato un menu di scelta rapida. Si vuole usare un elemento segue modale. 
 
Per configurare ognuno dei nostri schede **Ctrl + clic** dal TabBarController a ognuno dei nostri i controller di visualizzazione in ordine da uno a tre e selezionare la relazione **scheda** dal menu di scelta rapida, come illustrato di seguito:

[![](creating-tabbed-applications-images/context-menu.png "Selezionare la scheda relazione")](creating-tabbed-applications-images/context-menu.png#lightbox)

Lo Storyboard dovrebbe essere simile alla schermata riportata di seguito:

[![](creating-tabbed-applications-images/segue-layout.png "Lo Storyboard dovrebbe essere simile a questo screenshot")](creating-tabbed-applications-images/segue-layout.png#lightbox)

Se si fa clic su uno degli elementi della barra scheda ed Esplora Pannello proprietà, è possibile visualizzare un numero di opzioni diverse, come illustrato di seguito:

[![](creating-tabbed-applications-images/properties-panel.png "Impostazione delle opzioni di scheda nella finestra di esplorazione delle proprietà")](creating-tabbed-applications-images/properties-panel.png#lightbox)

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

[![](creating-tabbed-applications-images/first-view.png "L'output di app di esempio")](creating-tabbed-applications-images/first-view.png#lightbox)

<!--Save the files and run the application:

[![](creating-tabbed-applications-images/inital-screen-application.png "Save the files and run the application")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)-->

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come utilizzare un `UITabBarController` in un'applicazione. È stato illustrato come caricare i controller in ogni scheda e come impostare le proprietà delle schede tale titolo, immagine e badge. Abbiamo quindi esaminato, usando gli storyboard, come caricare un `UITabBarController` in fase di esecuzione quando non è il `RootViewController` della finestra.


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di applicazioni a schede (esempio)](https://developer.xamarin.com/samples/monotouch/CreatingTabbedApplications/)
- [Images.zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [Riferimento alla classe UITabBarController](http://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
