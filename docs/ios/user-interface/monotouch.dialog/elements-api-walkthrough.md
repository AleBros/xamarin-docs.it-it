---
title: Creazione di un'applicazione di xamarin. IOS tramite l'API di elementi
description: Questo articolo si basa sulle informazioni presentate nell'introduzione di articolo MonoTouch finestra di dialogo. Viene presentata una procedura dettagliata viene illustrato come utilizzare il MonoTouch.Dialog (MT D) elementi API per iniziare rapidamente a compilazione di un'applicazione con MT D.
ms.prod: xamarin
ms.assetid: F1124734-DF44-F1F3-0832-46F52A788CDC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 64f407288da72dbf51fb3388ddf36caea377f09c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790432"
---
# <a name="creating-a-xamarinios-application-using-the-elements-api"></a>Creazione di un'applicazione di xamarin. IOS tramite l'API di elementi

_Questo articolo si basa sulle informazioni presentate nell'introduzione di articolo MonoTouch finestra di dialogo. Viene presentata una procedura dettagliata viene illustrato come utilizzare il MonoTouch.Dialog (MT D) elementi API per iniziare rapidamente a compilazione di un'applicazione con MT D._

In questa procedura dettagliata, verrà usato il MT D elementi API per creare uno stile master-dettagli dell'applicazione che visualizza un elenco di attività. Quando l'utente seleziona il <span class="ui"> + </span> pulsante nella barra di spostamento, verrà aggiunto una nuova riga alla tabella per l'attività. Selezionando la riga verrà visualizzata la schermata di dettaglio che consente di aggiornare la descrizione dell'attività e la data di scadenza, come illustrato di seguito:

 [![](elements-api-walkthrough-images/01-task-list-app.png "Selezionando la riga verrà visualizzata la schermata di dettaglio che consente di aggiornare la descrizione dell'attività e la data di scadenza")](elements-api-walkthrough-images/01-task-list-app.png#lightbox)

 <a name="Elements_API_Walkthrough" />


## <a name="elements-api-walkthrough"></a>Procedura dettagliata di API di elementi

Nel [Introduzione alla finestra di dialogo MonoTouch](~/ios/user-interface/monotouch.dialog/index.md) articolo acquisita una conoscenza approfondita delle diverse parti di MT D. Di seguito è possibile utilizzare l'API di elementi per inserirli tutti in un'applicazione.

 <a name="Setting_up_the_Multi-Screen_Application" />


## <a name="setting-up-the-multi-screen-application"></a>Configurare l'applicazione multi-schermata

Per avviare il processo di creazione della schermata, MonoTouch.Dialog crea un `DialogViewController`e quindi aggiunge un `RootElement`.

Per creare un'applicazione multi-schermata con MonoTouch.Dialog, è necessario:

1.  Creare un  `UINavigationController.`
1.  Creare un  `DialogViewController.`
1.  Aggiungere il `DialogViewController` come radice di  `UINavigationController.` 
1.  Aggiungere un `RootElement` per il  `DialogViewController.`
1.  Aggiungere `Sections` e `Elements` per il  `RootElement.` 


 <a name="Using_A_UINavigationController" />


### <a name="using-a-uinavigationcontroller"></a>Utilizzando un UINavigationController

Per creare un'applicazione di navigazione di tipo, è necessario creare un `UINavigationController`e quindi aggiungerlo come il `RootViewController` nel `FinishedLaunching` metodo il `AppDelegate`. Per rendere il `UINavigationController` utilizzano MonoTouch.Dialog, viene aggiunta una `DialogViewController` per il `UINavigationController` come illustrato di seguito:

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

Il codice precedente crea un'istanza di un `RootElement` e passa nel `DialogViewController`. Il `DialogViewController` ha sempre un `RootElement` nella parte superiore della gerarchia. In questo esempio, il `RootElement` viene creato con la stringa "Elenco attività," che funge da titolo nella barra di navigazione del controller di navigazione. A questo punto, l'esecuzione dell'applicazione verrebbero presentate la schermata illustrata di seguito:

 [![](elements-api-walkthrough-images/02-to-do-list-screen-.png "Esecuzione dell'applicazione presenta la schermata visualizzata qui")](elements-api-walkthrough-images/02-to-do-list-screen-.png#lightbox)

Di seguito viene illustrato come utilizzare gerarchica del MonoTouch.Dialog di `Sections` e `Elements` per aggiungere altre schermate.

 <a name="Creating_the_Dialog_Screens" />


### <a name="creating-the-dialog-screens"></a>Creare le schermate della finestra di dialogo

Oggetto `DialogViewController` è un `UITableViewController` sottoclasse che MonoTouch.Dialog viene utilizzato per aggiungere schermate. MonoTouch.Dialog crea schermate aggiungendo un `RootElement` per un `DialogViewController`, come illustrato sopra. Il `RootElement` può avere `Section` istanze che rappresentano le sezioni di una tabella.
Le sezioni sono costituite da elementi, sezioni o anche altri `RootElements`. Annidando `RootElements`, MonoTouch.Dialog creata automaticamente un'applicazione in stile di navigazione, come verrà illustrato più avanti.

 <a name="Using_DialogViewController" />


### <a name="using-dialogviewcontroller"></a>Utilizzando DialogViewController

Il `DialogViewController`, da un `UITableViewController` sottoclasse, ha un `UITableView` come la relativa visualizzazione. In questo esempio si desidera aggiungere gli elementi per la tabella ogni volta che il <span class="ui"> + </span> pulsante. Poiché il `DialogViewController` è stato aggiunto a un `UINavigationController`, è possibile utilizzare il `NavigationItem`del `RightBarButton` proprietà da aggiungere il <span class="ui"> + </span> pulsante, come illustrato di seguito:

```csharp
_addButton = new UIBarButtonItem (UIBarButtonSystemItem.Add);
_rootVC.NavigationItem.RightBarButtonItem = _addButton;
```

Quando abbiamo creato il `RootElement` in precedenza, si passa un singolo `Section` istanza in modo che è possibile aggiungere elementi come il <span class="ui"> + </span> pulsante verrà scelti dall'utente. È possibile utilizzare il codice seguente per eseguire questo nell'evento gestore per il pulsante:

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

Questo codice crea un nuovo `Task` oggetto ogni volta che viene toccato il pulsante. Di seguito è riportato l'implementazione semplice del `Task` classe:

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

 []()

L'attività `Name` proprietà viene utilizzata per creare il `RootElement`della didascalia insieme a una variabile contatore denominata `n` che viene incrementato per ogni nuova attività. MonoTouch.Dialog consente di trasformare gli elementi in righe che vengono aggiunti al `TableView` quando ogni `taskElement` viene aggiunto.

 <a name="Presenting_and_Managing_Dialog_Screens" />


## <a name="presenting-and-managing-dialog-screens"></a>La presentazione e la gestione delle finestre di dialogo

È stato usato un `RootElement` in modo che MonoTouch.Dialog automaticamente creano una nuova schermata per i dettagli di ogni attività e passare a tale quando è selezionata una riga.

La schermata Dettagli attività stessa è costituita da due sezioni. ognuna di queste sezioni contiene un singolo elemento. Il primo elemento viene creato da un `EntryElement` per fornire una riga modificabile per l'attività `Description` proprietà. Se l'elemento è selezionata, una tastiera per modificare il testo viene visualizzata come illustrato di seguito:

 [![](elements-api-walkthrough-images/03-create-task.png "Se l'elemento è selezionata, una tastiera per modificare il testo viene visualizzata come illustrato")](elements-api-walkthrough-images/03-create-task.png#lightbox)

La seconda sezione contiene un `DateElement` che consente di gestire l'attività `DueDate` proprietà. Selezione data caricamento automatico di un controllo selezione data come illustrato:

 [![](elements-api-walkthrough-images/04-date-picker.png "Selezione data caricamento automatico di un controllo selezione data come")](elements-api-walkthrough-images/04-date-picker.png#lightbox)

In entrambi i `EntryElement` e `DateElement` casi (o per qualsiasi elemento di immissione di dati in MonoTouch.Dialog), le modifiche ai valori vengono mantenute automaticamente. È possibile dimostrare questo modificando la data e spostarsi avanti e indietro tra la schermata principale e i vari dettagli attività, in cui vengono mantenuti i valori nelle schermate di dettaglio.

 <a name="Summary" />


## <a name="summary"></a>Riepilogo

In questo articolo è visualizzata una procedura dettagliata viene illustrato come utilizzare l'API di elementi MonoTouch.Dialog. Che trattato i passaggi di base per creare un'applicazione multi-schermata con MT P, incluse come utilizzare un `DialogViewController` e come aggiungere elementi e sezioni per creare schermate. Inoltre è stato illustrato come utilizzare MT D in combinazione con un `UINavigationController`.


## <a name="related-links"></a>Collegamenti correlati

- [MTDWalkthrough (esempio)](https://developer.xamarin.com/samples/MTDWalkthrough/)
- [Screencast - Miguel de Icaza crea una schermata di accesso di iOS con MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Screencast - creare con facilità iOS interfacce utente con MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Introduzione a MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Procedura dettagliata di API di Reflection](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Procedura dettagliata elemento JSON](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Finestra di dialogo MonoTouch su Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Applicazione TweetStation](https://github.com/migueldeicaza/TweetStation)
- [Riferimento alla classe UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Riferimento alla classe UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
