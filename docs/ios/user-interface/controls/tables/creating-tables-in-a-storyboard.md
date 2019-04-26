---
title: Utilizzo delle tabelle in iOS Designer
description: Nelle sezioni precedenti sono stati presentati lo sviluppo con le tabelle. In questo, la quinta e ultima sezione, si verrà aggregare quanto imparato finora e creare un'applicazione elenco attività piuttosto base utilizzando uno Storyboard.
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 303c96ae6cdbc9f5b327c971f962d6eac75a6fa1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227553"
---
# <a name="working-with-tables-in-the-ios-designer"></a>Utilizzo delle tabelle in iOS Designer

Gli storyboard sono un modo WYSIWYG per creare applicazioni iOS e sono supportati in Visual Studio su Mac e Windows. Per altre informazioni su storyboard, vedere la [Introduzione a storyboard](~/ios/user-interface/storyboards/index.md) documento. Gli storyboard consentono anche di modificare il layout delle celle *in* la tabella, che semplifica lo sviluppo di tabelle e celle

Quando si configurano le proprietà di una visualizzazione tabella in iOS Designer, esistono due tipi di contenuto della cella che è possibile scegliere tra: **Dynamic** oppure **statici** contenuto prototipo.

<a name="Prototype_Content" />

## <a name="dynamic-prototype-content"></a>Contenuto dinamico prototipo

Oggetto `UITableView` con prototipo contenuto in genere deve visualizzare un elenco di dati in cui la cella prototipo (o le celle, come è possono definire più di uno) vengono poi riusati per ogni elemento nell'elenco. Le celle non necessario creare un'istanza, ma vengono ottenuti nel `GetView` metodo chiamando il `DequeueReusableCell` metodo del relativo `UITableViewSource`.

 <a name="Static_Content" />


## <a name="static-content"></a>Contenuto statico

`UITableView`s con contenuto statico consente di progettare direttamente nell'area di progettazione tabelle. Le celle possono essere trascinate nella tabella e può essere personalizzate modificando le proprietà e l'aggiunta di controlli.

 <a name="Creating_a_Storyboard-driven_app" />


## <a name="creating-a-storyboard-driven-app"></a>Creazione di un'App basata su uno Storyboard

L'esempio StoryboardTable contiene una semplice app master-dettagli che usa entrambi i tipi di UITableView in uno Storyboard. Il resto di questa sezione descrive come compilare un esempio di elenco attività da eseguire piccoli che avrà un aspetto simile al seguente al termine dell'esercitazione:

 [![Schermate di esempio](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

L'interfaccia utente verrà compilato con uno storyboard e sia schermate userà un UITableView. Nella schermata principale vengono utilizzati *contenuto prototipo* e il layout della riga, il livello di dettaglio Usa schermata *contenuto statico* per creare un form di immissione di dati utilizzando i layout di cella personalizzato.

## <a name="walkthrough"></a>Procedura dettagliata

Creare una nuova soluzione in Visual Studio usando **(Crea)... nuovo progetto > App visualizzazione singola (C#)** e chiamarlo _StoryboardTables_.

 [![Creare una finestra di dialogo Nuovo progetto](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

Verrà aperta la soluzione con alcuni C# file e una `Main.storyboard` file già creata. Fare doppio clic il `Main.storyboard` file per aprirlo in iOS Designer.

<a name="Modifying_the_Storyboard" />

## <a name="modifying-the-storyboard"></a>Modifica lo Storyboard

Lo storyboard verrà modificato in tre passaggi:

-  Layout prima di tutto il necessario visualizzare i controller e impostare le relative proprietà.
-  In secondo luogo, creare l'interfaccia utente trascinando e rilasciando oggetti nella visualizzazione
-  Infine, aggiungere la classe UIKit richiesta per ogni visualizzazione e assegnare un nome diversi controlli in modo che vi possano far riferimento nel codice.


Una volta completato lo storyboard, è possibile aggiungere codice per rendere tutti gli elementi di lavoro.

<a name="Layout_The_View_Controllers" />

### <a name="layout-the-view-controllers"></a>Il controller di visualizzazione layout

La prima modifica allo storyboard è l'eliminazione la visualizzazione dettagli esistente e sostituirlo con un UITableViewController. Attenersi ai passaggi riportati di seguito.

1.  Selezionare la barra nella parte inferiore del Controller di visualizzazione ed eliminarlo.
2.  Trascinare un **Controller di spostamento** e una **Controller di visualizzazione tabella** nello Storyboard dalla casella degli strumenti. 
3.  Creare un elemento segue da Controller visualizzazione radice per il secondo Controller di visualizzazione tabella appena aggiunto. Per creare l'elemento segue, controllo + trascina *rispetto alla cella di dettaglio* al UITableViewController appena aggiunto. Scegliere l'opzione **mostrare** sotto **Selezione elemento Segue**. 
4.  Selezionare il nuovo elemento segue creato e assegnargli un identificatore di riferimento in questo elemento segue nel codice. Fare clic sull'elemento segue e immettere `TaskSegue` per il **identificatore** nel **riquadro delle proprietà**, simile al seguente:    
  [![Denominazione segue nel Pannello proprietà](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. Configurare quindi le due viste delle tabelle selezionandoli e usando il riquadro delle proprietà. Assicurarsi di selezionare una visualizzazione e non i Controller di visualizzazione, è possibile utilizzare la struttura del documento per facilitare la selezione.

6.  Modificare il Controller visualizzazione radice per essere **contenuto: Dinamici prototipi** (la visualizzazione nell'area di progettazione verrà etichettata **prototipo contenuto** ):

    [![Impostazione della proprietà del contenuto ai prototipi dinamici](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7.  Modificare il nuovo **UITableViewController** essere **contenuto: Le celle statiche**. 


8. Il nuovo UITableViewController deve avere il nome della classe e un identificatore impostato. Selezionare il Controller di visualizzazione e il tipo _TaskDetailViewController_ per il **classe** nel **riquadro delle proprietà** : verrà creato un nuovo `TaskDetailViewController.cs` file della soluzione Riempimento. Immettere il **StoryboardID** come _detail_, come illustrato nell'esempio seguente. Verrà usato in un secondo momento per caricare questa vista in C# code:  

    [![Impostazione dell'ID di Storyboard](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. L'area di progettazione dello storyboard dovrebbe essere simile al seguente (titolo dell'elemento di navigazione del Controller visualizzazione radice è stato modificato in "Area di lavoro ingrato"):

    [![Nell'area di progettazione](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  



<a name="Create_the_UI" />

### <a name="create-the-ui"></a>Creare l'interfaccia utente

Ora che le viste e gli elementi segue sono configurate, gli elementi dell'interfaccia utente devono essere aggiunti.

#### <a name="root-view-controller"></a>Controller visualizzazione radice

In primo luogo, selezionare la cella prototipo nel Controller di visualizzazione Master e impostare il **Identifier** come _taskcell_, come illustrato di seguito. Il nome verrà utilizzato in un secondo momento nel codice per recuperare un'istanza di questa UITableViewCell:

 [![impostazione dell'identificatore di cella](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

Successivamente, è necessario creare un pulsante che aggiungerà nuove attività, come illustrato di seguito:

[![elemento pulsante nella barra di spostamento della barra](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

Seguire questa procedura: 

-  Trascinare un **elemento pulsante della barra** dalla casella degli strumenti per il _lato destro della barra di spostamento_.
-  Nel **riquadro delle proprietà**, in **elemento pulsante della barra** selezionare **identificatore: Aggiungere** (per renderlo un *+* plus pulsante). 
-  Assegnare un nome in modo da poter essere identificato nel codice in una fase successiva. Si noti che è necessario assegnare un nome di classe Controller visualizzazione radice (ad esempio **ItemViewController**) che consente di impostare il nome dell'elemento pulsante sulla barra.


#### <a name="taskdetail-view-controller"></a>Controller di visualizzazione TaskDetail

Vista dettagliata richiede molte più operazioni. Le celle della visualizzazione tabella devono essere trascinati la visualizzazione e quindi popolato con le etichette, le visualizzazioni di testo e pulsanti. La schermata seguente mostra l'interfaccia utente terminato con due sezioni. Una sezione ha tre etichette, tre celle uno switch e due campi di testo, mentre la seconda sezione ha un'unica cella con due pulsanti:

 [![layout di visualizzazione di dettaglio](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

I passaggi per compilare il layout completo sono:

Selezionare la visualizzazione di tabella e aprire il **Riempi proprietà**. Aggiornare le proprietà seguenti:

-  **Nelle sezioni**: _2_ 
-  **Stile**: _Raggruppati_
-  **Separatore**: _Nessuno_
-  **Selezione**: _Nessuna selezione_

Selezionare la sezione superiore e in **proprietà > sezione della visualizzazione tabella** cambiare **righe** al _3_, come illustrato di seguito:


 [![l'impostazione nella sezione superiore a tre righe](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

Per ogni cella aprire il **riquadro delle proprietà** e impostare:

-  **Stile**:  _Personalizzato_
-  **Identificatore**: Scegliere un identificatore univoco per ogni cella (ad es. "_title_","_note_","_eseguita_").
-  Trascinare i controlli necessari per produrre il layout mostrato nella schermata (posizionare **UILabel**, **UITextField** e **UISwitch** sulle celle corrette e impostare le etichette in modo appropriato, Internet Explorer. Titolo, note e fatto).


Nella seconda sezione, impostare **righe** al _1_ e selezionare l'handle di ridimensionamento inferiore della cella per renderlo più alta.

-  **Impostare l'identificatore**: su un valore univoco (ad es. "Salva"). 
-  **Impostare lo sfondo**:  _Cancellare il colore_ .
-  Trascinare due pulsanti sulla cella e impostare i propri titoli in modo appropriato (vale a dire _salvare_ e _eliminare_), come illustrato di seguito:

   [![configurare due pulsanti nella parte inferiore](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

A questo punto è anche possibile impostare vincoli per le celle e i controlli per garantire un layout di tipo adattivo.

### <a name="adding-uikit-class-and-naming-controls"></a>Aggiunta classe UIKit e controlli di denominazione

Esistono pochi passaggi finali nella creazione nostro Storyboard. Prima di tutto è necessario assegnare a ognuno dei nostri controlli un nome in **identità > nome** quindi possono essere usate in un secondo momento nel codice. Questi nomi come indicato di seguito:

-  **Spostare il titolo UITextField** : _TitleText_
-  **Note sulla UITextField** : _NotesText_
-  **UISwitch** : _DoneSwitch_
-  **Eliminare UIButton** : _DeleteButton_
-  **Salvare UIButton** : _SaveButton_


<a name="Adding_Code" />

## <a name="adding-code"></a>Aggiunta di codice

Verrà eseguito il resto del lavoro in Visual Studio in Mac o Windows con C#. Si noti che i nomi delle proprietà usate nel codice riflettere quelle impostate nella procedura dettagliata precedente.

Prima di tutto si creeranno un `Chores` (classe), in modo da fornire un modo per ottenere e impostare il valore di ID, Name, note e l'operazione eseguita booleano, in modo che è possibile usare tali valori in tutta l'applicazione.

Nel `Chores` classe aggiungere il codice seguente:

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

A questo punto, creare un `RootTableSource` classe che eredita da `UITableViewSource`. 

La differenza tra questo e visualizzazione di una tabella non Storyboard è che il `GetView` metodo non è necessario creare un'istanza di tutte le celle – `theDequeueReusableCell` metodo restituirà sempre un'istanza della cella prototipo (con identificatore corrispondente).

Il codice seguente proviene dal `RootTableSource.cs` file:

```csharp
public class RootTableSource : UITableViewSource
{
// there is NO database or storage of Tasks in this example, just an in-memory List<>
Chores[] tableItems;
string cellIdentifier = "taskcell"; // set in the Storyboard

    public RootTableSource(Chores[] items)
    {
        tableItems = items;
    }

public override nint RowsInSection(UITableView tableview, nint section)
{
  return tableItems.Length;
}

public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
  // in a Storyboard, Dequeue will ALWAYS return a cell, 
  var cell = tableView.DequeueReusableCell(cellIdentifier);
  // now set the properties as normal
  cell.TextLabel.Text = tableItems[indexPath.Row].Name;
  if (tableItems[indexPath.Row].Done)
    cell.Accessory = UITableViewCellAccessory.Checkmark;
  else
    cell.Accessory = UITableViewCellAccessory.None;
  return cell;
}
public Chores GetItem(int id)
{
  return tableItems[id];
}
```

Usare la `RootTableSource` classe, creare una nuova raccolta nel `ItemViewController`del costruttore:

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

In `ViewWillAppear` passi la raccolta per l'origine e assegnare alla visualizzazione tabella:

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

Se si esegue l'app a questo punto, nella schermata principale sarà ora caricare e visualizzare un elenco di due attività. Quando un'attività viene manipolata elemento definito da storyboard segue provocherà la schermata dei dettagli da visualizzare, ma non viene visualizzato nel momento in cui tutti i dati.

Per "inviare un parametro' in un elemento segue, eseguire l'override di `PrepareForSegue` (metodo) e impostare le proprietà nel `DestinationViewController` (il `TaskDetailViewController` in questo esempio). La classe di Controller di visualizzazione di destinazione verrà sia stata creata un'istanza ma non è ancora visualizzato all'utente, ciò significa che è possibile impostare proprietà sulla classe ma non modificare tutti i controlli dell'interfaccia utente:

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, NSObject sender)
    {
      if (segue.Identifier == "TaskSegue") { // set in Storyboard
        var navctlr = segue.DestinationViewController as TaskDetailViewController;
        if (navctlr != null) {
          var source = TableView.Source as RootTableSource;
          var rowPath = TableView.IndexPathForSelectedRow;
          var item = source.GetItem(rowPath.Row);
          navctlr.SetTask (this, item); // to be defined on the TaskDetailViewController
        }
      }
    }
```

Nelle `TaskDetailViewController` il `SetTask` metodo assegna i relativi parametri alle proprietà in modo che vi possano far riferimento in ViewWillAppear. Le proprietà del controllo non possono essere modificate `SetTask` perché potrebbe non esistere quando `PrepareForSegue` viene chiamato:

```csharp
Chore currentTask {get;set;}
    public ItemViewController Delegate {get;set;} // will be used to Save, Delete later

public override void ViewWillAppear (bool animated)
    {
      base.ViewWillAppear (animated);
      TitleText.Text = currentTask.Name;
      NotesText.Text = currentTask.Notes;
      DoneSwitch.On = currentTask.Done;
    }

    // this will be called before the view is displayed
    public void SetTask (ItemViewController d, Chore task) {
      Delegate = d;
      currentTask = task;
    }
```

L'elemento segue verrà ora aprire la schermata dei dettagli e visualizzare le informazioni sull'attività selezionata. Purtroppo non vi è alcuna implementazione per il **salvare** e **eliminare** pulsanti. Prima di implementare i pulsanti, questi metodi per aggiungere **ItemViewController.cs** per aggiornare i dati sottostanti e chiudere la schermata dei dettagli:

```csharp
public void SaveTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
        NavigationController.PopViewController(true);
}

public void DeleteTask(Chores chore)
{
  var oldTask = chores.Find(t => t.Id == chore.Id);
  chores.Remove(oldTask);
        NavigationController.PopViewController(true);
}
```

Successivamente, è necessario aggiungere il pulsante `TouchUpInside` gestore dell'evento per il `ViewDidLoad` metodo **TaskDetailViewController.cs**. Il `Delegate` riferimento alla proprietà di `ItemViewController` è stato creato in modo specifico in modo che possiamo chiamare `SaveTask` e `DeleteTask`, cui chiudere questa visualizzazione come parte del relativo funzionamento:

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

L'ultima parte rimanente della funzionalità per la creazione è la creazione di nuove attività. Nelle **ItemViewController.cs** aggiungere un metodo che crea nuove attività e viene aperta la visualizzazione di dettaglio. Creare un'istanza di una vista da uno storyboard, usare il `InstantiateViewController` metodo con il `Identifier` per la visualizzazione, in questo esempio che sarà 'detail':

```csharp
public void CreateTask () 
    {
      // first, add the task to the underlying data
      var newId = chores[chores.Count - 1].Id + 1;
      var newChore = new Chore{Id = newId};
      chores.Add (newChore);

      // then open the detail view to edit it
      var detail = Storyboard.InstantiateViewController("detail") as TaskDetailViewController;
      detail.SetTask (this, newChore);
      NavigationController.PushViewController (detail, true);
    }
```

Infine, collegare il pulsante nella barra di spostamento **ItemViewController.cs**del `ViewDidLoad` metodo chiamarlo:

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

Che viene completato l'esempio di Storyboard-l'app finita invece analoga alla seguente:

[![App completata](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

Nell'esempio viene illustrato:

-  Creazione di una tabella con prototipo contenuto in cui le celle vengono definite per essere riusata visualizzare gli elenchi di dati. 
-  Creazione di una tabella con contenuto statico per creare un formato di input. Sono stati la modifica dello stile di tabella e l'aggiunta di sezioni, celle e controlli dell'interfaccia utente. 
-  Come creare un elemento segue ed eseguire l'override di `PrepareForSegue` metodo per notificare la vista di destinazione di eventuali parametri richiesti. 
-  Il caricamento di viste di storyboard direttamente con il `Storyboard.InstantiateViewController` (metodo).



## <a name="related-links"></a>Collegamenti correlati

- [StoryboardTable (esempio)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
