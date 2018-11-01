---
title: Creazione di un'applicazione xamarin. IOS mediante l'API Elements
description: Questo articolo si basa sulle informazioni presentate nella sezione introduttiva all'articolo di finestra di dialogo MonoTouch. Presenta una procedura dettagliata che illustra come usare il monotouch. Dialog (MT. D) gli elementi API iniziare subito a usare la creazione di un'applicazione con MT. D.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: a203975545c9f6e258505404d76e3fd5519a5a31
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674731"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>Creazione di un'applicazione xamarin. IOS mediante l'API Elements

_Questo articolo si basa sulle informazioni presentate nella sezione introduttiva all'articolo di finestra di dialogo MonoTouch. Presenta una procedura dettagliata che illustra come usare il monotouch. Dialog (MT. D) gli elementi API iniziare subito a usare la creazione di un'applicazione con MT. D._

In questa procedura dettagliata, si userà il MT. D elementi API per creare uno stile master-dettagli dell'applicazione che visualizza un elenco di attività. Quando l'utente seleziona il <span class="ui"> + </span> pulsante nella barra di spostamento, verrà aggiunto una nuova riga alla tabella per l'attività. Se si seleziona la riga verrà visualizzata la schermata dei dettagli che ci consente di aggiornare la descrizione dell'attività e la data di scadenza, come illustrato di seguito:

 [![](elements-api-walkthrough-images/01-task-list-app.png "Se si seleziona la riga verrà visualizzata la schermata dei dettagli che ci consente di aggiornare la descrizione dell'attività e la data di scadenza")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

 ## <a name="setting-up-mtd"></a>Configurazione di MT. 1!D

MT. 1!d viene distribuito con xamarin. IOS. Per usarlo, fare clic sui **riferimenti** nodo di xamarin. IOS del progetto in Visual Studio 2017 o Visual Studio per Mac e aggiungere un riferimento al **monotouch. Dialog-1** assembly. Aggiungere quindi `using MonoTouch.Dialog` istruzioni presenti nell'origine del codice in base alle esigenze.

## <a name="elements-api-walkthrough"></a>Procedura dettagliata gli elementi API

Nel [Introduzione alla finestra di dialogo MonoTouch](~/ios/user-interface/monotouch.dialog/index.md) articolo, viene acquisita una conoscenza approfondita delle diverse parti di MT. D. È possibile usare l'API di elementi da inserire tutti gli elementi in un'applicazione.

## <a name="setting-up-the-multi-screen-application"></a>Configurazione dell'applicazione multi-schermata

Per avviare il processo di creazione dello schermo, monotouch. Dialog crea una `DialogViewController`, quindi aggiunge un `RootElement`.

Per creare un'applicazione multi-schermata con monotouch. Dialog, è necessario:

1.  Creare un `UINavigationController.`
1.  Creare un `DialogViewController.`
1.  Aggiungere il `DialogViewController` come radice di  `UINavigationController.` 
1.  Aggiungere un `RootElement` per il  `DialogViewController.`
1.  Aggiungere `Sections` e `Elements` per il  `RootElement.` 

### <a name="using-a-uinavigationcontroller"></a>Usando un UINavigationController

Per creare un'applicazione di navigazione tipo, è necessario creare un `UINavigationController`e quindi aggiungerlo come il `RootViewController` nel `FinishedLaunching` metodo il `AppDelegate`. Per rendere il `UINavigationController` rivolgersi monotouch. Dialog, aggiungiamo un `DialogViewController` per il `UINavigationController` come illustrato di seguito:

```csharp
public override bool FinishedLaunching (UIApplication app, 
        NSDictionary options)
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

Il codice precedente crea un'istanza di un `RootElement` e lo passa nel `DialogViewController`. Il `DialogViewController` ha sempre un `RootElement` nella parte superiore della gerarchia. In questo esempio, il `RootElement` viene creato con la stringa "To Do List" che funge da titolo nella barra di spostamento del controller di spostamento. A questo punto, l'esecuzione dell'applicazione potrebbero presentarsi alla schermata illustrata di seguito:

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "Esecuzione dell'applicazione verrà presentato alla schermata illustrata di seguito")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

Ora verrà descritto come utilizzare una struttura gerarchica di monotouch. Dialog dei `Sections` e `Elements` aggiungono schermate.

### <a name="creating-the-dialog-screens"></a>Creare le schermate di finestra di dialogo

Oggetto `DialogViewController` è un `UITableViewController` sottoclasse che monotouch. Dialog viene utilizzato per aggiungere schermate. Monotouch. Dialog crea le schermate mediante l'aggiunta di un `RootElement` a un `DialogViewController`, come descritto in precedenza. Il `RootElement` può avere `Section` istanze che rappresentano le sezioni di una tabella.
Le sezioni sono costituite da elementi, altre sezioni, o anche altri `RootElements`. Annidando `RootElements`, monotouch. Dialog crea automaticamente un'applicazione di navigazione di tipo, come si vedrà successivamente.

### <a name="using-dialogviewcontroller"></a>Usando DialogViewController

Il `DialogViewController`, in corso una `UITableViewController` sottoclasse, ha un `UITableView` come relativa visualizzazione. In questo esempio si vuole aggiungere elementi alla tabella ogni volta che il <span class="ui"> + </span> pulsante viene toccato. Poiché il `DialogViewController` è stato aggiunto a un `UINavigationController`, è possibile usare il `NavigationItem`del `RightBarButton` proprietà da aggiungere la <span class="ui"> + </span> pulsante, come illustrato di seguito:

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

Quando è stata creata la `RootElement` in precedenza, viene passato un singolo `Section` dell'istanza in modo che è stato possibile aggiungere elementi come le <span class="ui"> + </span> pulsante toccando dall'utente. È possibile usare il codice seguente per eseguire questo nell'evento gestore per il pulsante:

```csharp
_addButton.Clicked += (sender, e) => {
                
        ++n;
                
        var task = new Task{Name = "task " + n, DueDate = DateTime.Now};
                
        var taskElement = new RootElement (task.Name){
                new Section () {
                        new EntryElement (task.Name, 
                                "Enter task description", task.Description)
                },
                new Section () {
                        new DateElement ("Due Date", task.DueDate)
                }
        };
        _rootElement [0].Add (taskElement);
};
```

Questo codice crea un nuovo `Task` oggetto ogni volta che viene usato il pulsante. Di seguito è riportato l'implementazione semplice del `Task` classe:

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

L'attività `Name` proprietà viene utilizzata per creare le `RootElement`della didascalia insieme a una variabile contatore denominata `n` che viene incrementato per ogni nuova attività. Monotouch. Dialog Trasforma gli elementi in righe che vengono aggiunti per il `TableView` quando ogni `taskElement` viene aggiunto.

## <a name="presenting-and-managing-dialog-screens"></a>Presentazione e la gestione di finestre di dialogo

È stato usato un `RootElement` in modo che monotouch. Dialog automaticamente crea una nuova schermata per i dettagli di ogni attività e passare ad esso quando è selezionata una riga.

La schermata dei dettagli attività stessa è costituita da due sezioni. ognuna di queste sezioni contiene un singolo elemento. Il primo elemento viene creato da un `EntryElement` per fornire una riga modificabile per l'attività `Description` proprietà. Quando si seleziona l'elemento, una tastiera per modificare il testo viene presentata come illustrato di seguito:

 [![](elements-api-walkthrough-images/03-create-task.png "Quando si seleziona l'elemento, una tastiera per modificare il testo viene presentata come illustrato")](elements-api-walkthrough-images/03-create-task.png#lightbox)

La seconda sezione contiene un `DateElement` che consente di gestire l'attività `DueDate` proprietà. La selezione della data automaticamente carica un controllo selezione data, come illustrato:

 [![](elements-api-walkthrough-images/04-date-picker.png "Selezionare automaticamente la data viene caricato un controllo selezione data come")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

In entrambi i `EntryElement` e `DateElement` casi (o per qualsiasi elemento di immissione dati in monotouch. Dialog), le modifiche ai valori vengono mantenute automaticamente. È possibile dimostrare questo concetto modificando la data e quindi spostarsi avanti e indietro tra la schermata di radice e i vari dettagli attività, in cui vengono mantenuti i valori alle schermate dei dettagli.

## <a name="summary"></a>Riepilogo

Questo articolo è presentata una procedura dettagliata ha illustrato come usare l'API Elements monotouch. Dialog. Illustrato i passaggi di base per creare un'applicazione multi-schermata con MT. 1!d, nonché come utilizzare un `DialogViewController` e su come aggiungere elementi e le sezioni per creare schermate. Inoltre, è stato illustrato come usare MT. 1!d in combinazione con un `UINavigationController`.

## <a name="related-links"></a>Collegamenti correlati

- [MTDWalkthrough (esempio)](https://developer.xamarin.com/samples/MTDWalkthrough/)
- [Screencast - Miguel de Icaza consente di creare una schermata di accesso di iOS con monotouch. Dialog](http://youtu.be/3butqB1EG0c)
- [Screencast - facilmente creare interfacce utente iOS con monotouch. Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introduzione a monotouch. Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Procedura dettagliata sull'API di Reflection](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Procedura dettagliata elemento JSON](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Finestra di dialogo di MonoTouch su Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Applicazione TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Riferimento alla classe UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Riferimento alla classe UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
