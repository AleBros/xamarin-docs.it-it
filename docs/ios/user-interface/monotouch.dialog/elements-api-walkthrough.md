---
title: Creazione di un'applicazione Xamarin.iOS tramite l'API Elements
description: Questo articolo si basa sulle informazioni presentate nella finestra di dialogo Introduzione a MonoTouch. Viene presentata una procedura dettagliata che illustra come usare il MonoTouch. Dialog (MT. D) elementi API per iniziare rapidamente a creare un'applicazione con MT. D.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: 2258b2e8451f896aff59c89478833976ef7086e3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002374"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>Creazione di un'applicazione Xamarin.iOS tramite l'API Elements

_Questo articolo si basa sulle informazioni presentate nella finestra di dialogo Introduzione a MonoTouch. Viene presentata una procedura dettagliata che illustra come usare il MonoTouch. Dialog (MT. D) elementi API per iniziare rapidamente a creare un'applicazione con MT. D._

In questa procedura dettagliata verrà usato il MT. D elementi API per creare uno stile Master-Details dell'applicazione in cui viene visualizzato un elenco attività. Quando l'utente seleziona il pulsante **+** nella barra di spostamento, viene aggiunta una nuova riga alla tabella per l'attività. Selezionando la riga, si passerà alla schermata dei dettagli che consente di aggiornare la descrizione dell'attività e la data di scadenza, come illustrato di seguito:

[![](elements-api-walkthrough-images/01-task-list-app.png "Selecting the row will navigate to the detail screen that allows us to update the task description and the due date")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

## <a name="setting-up-mtd"></a>Configurazione di MT. D

Mt. D viene distribuito con Xamarin.iOS. Per usarlo, fare clic con il pulsante destro del mouse sul nodo **riferimenti** di un progetto Xamarin.iOS in Visual Studio 2017 o Visual Studio per Mac e aggiungere un riferimento all'assembly **MonoTouch. Dialog-1** . Aggiungere quindi `using MonoTouch.Dialog` istruzioni nel codice sorgente in base alle esigenze.

## <a name="elements-api-walkthrough"></a>Procedura dettagliata per l'API elementi

Nella [finestra di dialogo Introduzione a MonoTouch](~/ios/user-interface/monotouch.dialog/index.md) è stata acquisita una conoscenza approfondita delle diverse parti di mt. D. Si userà l'API Elements per riunire tutti gli elementi in un'applicazione.

## <a name="setting-up-the-multi-screen-application"></a>Configurazione dell'applicazione multischermata

Per avviare il processo di creazione della schermata, MonoTouch. Dialog crea una `DialogViewController`, quindi aggiunge una `RootElement`.

Per creare un'applicazione multischermata con MonoTouch. Dialog, è necessario:

1. Creare una `UINavigationController.`
1. Creare una `DialogViewController.`
1. Aggiungere il `DialogViewController` come radice della `UINavigationController.` 
1. Aggiungere un `RootElement` al `DialogViewController.`
1. Aggiungere `Sections` e `Elements` al `RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>Uso di un UINavigationController

Per creare un'applicazione di tipo navigazione, è necessario creare una `UINavigationController`, quindi aggiungerla come `RootViewController` nel metodo `FinishedLaunching` della `AppDelegate`. Per fare in modo che il `UINavigationController` funzioni con MonoTouch. Dialog, viene aggiunta una `DialogViewController` al `UINavigationController` come illustrato di seguito:

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    _window = new UIWindow (UIScreen.MainScreen.Bounds);
            
    _rootElement = new RootElement ("To Do List"){new Section ()};

    // code to create screens with MT.D will go here …

    _rootVC = new DialogViewController (_rootElement);
    _nav = new UINavigationController (_rootVC);
    _window.RootViewController = _nav;
    _window.MakeKeyAndVisible ();
            
    return true;
}
```

Il codice precedente crea un'istanza di un `RootElement` e la passa al `DialogViewController`. Il `DialogViewController` ha sempre un `RootElement` nella parte superiore della gerarchia. In questo esempio, il `RootElement` viene creato con la stringa "to do list", che funge da titolo nella barra di spostamento del controller di spostamento. A questo punto, l'esecuzione dell'applicazione presenta la schermata riportata di seguito:

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "Running the application will present the screen shown here")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

Per aggiungere altre schermate, vediamo come usare la struttura gerarchica di `Sections` e `Elements` della finestra di dialogo.

### <a name="creating-the-dialog-screens"></a>Creazione delle schermate della finestra di dialogo

Un `DialogViewController` è una sottoclasse `UITableViewController` utilizzata da MonoTouch. dialog per aggiungere schermate. MonoTouch. Dialog Crea schermate aggiungendo una `RootElement` a un `DialogViewController`, come illustrato in precedenza. Il `RootElement` può includere `Section` istanze che rappresentano le sezioni di una tabella.
Le sezioni sono costituite da elementi, altre sezioni o anche da altri `RootElements`. Annidando `RootElements`, MonoTouch. Dialog crea automaticamente un'applicazione di tipo navigazione, come si vedrà in seguito.

### <a name="using-dialogviewcontroller"></a>Uso di DialogViewController

Il `DialogViewController`, che è una sottoclasse `UITableViewController`, ha un `UITableView` come visualizzazione. In questo esempio, si desidera aggiungere elementi alla tabella ogni volta che viene toccato il pulsante **+** . Poiché il `DialogViewController` è stato aggiunto a una `UINavigationController`, è possibile usare la proprietà `RightBarButton` `NavigationItem`per aggiungere il pulsante **+** , come illustrato di seguito:

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

Quando abbiamo creato il `RootElement` in precedenza, abbiamo passato un'unica istanza di `Section`, in modo da poter aggiungere elementi quando il pulsante **+** viene toccato dall'utente. Per eseguire questa operazione nel gestore dell'evento per il pulsante, è possibile usare il codice seguente:

```csharp
_addButton.Clicked += (sender, e) => {                
    ++n;
                
    var task = new Task{Name = "task " + n, DueDate = DateTime.Now};
                
    var taskElement = new RootElement (task.Name) {
        new Section () {
            new EntryElement (task.Name, "Enter task description", task.Description)
        },
        new Section () {
            new DateElement ("Due Date", task.DueDate)
        }
    };
    _rootElement [0].Add (taskElement);
};
```

Questo codice crea un nuovo oggetto `Task` ogni volta che viene toccato il pulsante. Di seguito viene illustrata l'implementazione semplice della classe `Task`:

```csharp
public class Task
{   
    public Task ()
    {
    }
      
    public string Name { get; set; }
        
    public string Description { get; set; }

    public DateTime DueDate { get; set; }
}
```

La proprietà `Name` dell'attività viene utilizzata per creare la didascalia del `RootElement`insieme a una variabile contatore denominata `n` incrementata per ogni nuova attività. MonoTouch. dialog consente di trasformare gli elementi nelle righe aggiunte al `TableView` quando ogni `taskElement` viene aggiunto.

## <a name="presenting-and-managing-dialog-screens"></a>Presentazione e gestione delle schermate di dialogo

È stato usato un `RootElement` in modo che MonoTouch. Dialog crei automaticamente una nuova schermata per i dettagli di ogni attività e la selezioni quando viene selezionata una riga.

La schermata dei dettagli delle attività è costituita da due sezioni: ognuna di queste sezioni contiene un singolo elemento. Il primo elemento viene creato da un `EntryElement` per fornire una riga modificabile per la proprietà `Description` dell'attività. Quando l'elemento è selezionato, viene visualizzata una tastiera per la modifica del testo, come illustrato di seguito:

 [![](elements-api-walkthrough-images/03-create-task.png "When the element is selected, a keyboard for text editing is presented as shown")](elements-api-walkthrough-images/03-create-task.png#lightbox)

La seconda sezione contiene un `DateElement` che consente di gestire la proprietà `DueDate` dell'attività. Selezionando la data viene automaticamente caricato un selettore di data, come illustrato:

 [![](elements-api-walkthrough-images/04-date-picker.png "Selecting the date automatically loads a date picker as")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

In entrambi i casi `EntryElement` e `DateElement` (o per qualsiasi elemento di immissione di dati in MonoTouch. Dialog), tutte le modifiche apportate ai valori vengono mantenute automaticamente. Per illustrare questo problema, è possibile modificare la data e spostarsi tra la schermata radice e i dettagli dell'attività, in cui vengono conservati i valori nelle schermate dei dettagli.

## <a name="summary"></a>Riepilogo

Questo articolo ha presentato una procedura dettagliata che illustra come usare l'API MonoTouch. Dialog Elements. Sono stati illustrati i passaggi di base per creare un'applicazione con più schermate con MT. D, inclusa la modalità di utilizzo di un `DialogViewController` e come aggiungere elementi e sezioni per creare schermate. È stato inoltre illustrato come utilizzare MT. D insieme a un `UINavigationController`.

## <a name="related-links"></a>Collegamenti correlati

- [MTDWalkthrough (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/mtdwalkthrough)
- [Introduzione a MonoTouch. Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Procedura dettagliata dell'API Reflection](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Procedura dettagliata sull'elemento JSON](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Finestra di dialogo MonoTouch su GitHub](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Applicazione TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Riferimento alla classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Riferimento alla classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
