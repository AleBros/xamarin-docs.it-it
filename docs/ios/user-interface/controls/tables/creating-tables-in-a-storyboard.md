---
title: Utilizzo di tabelle nella finestra di progettazione iOS
description: Nelle sezioni precedenti, è esplorare lo sviluppo di utilizzo di tabelle. In questo passaggio la quinta e ultima sezione, verrà ciò che è stato appreso fino a questo punto di aggregazione e creare un'applicazione settimanali base utilizzando uno Storyboard.
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 869719b1d3514fee4b45f61dcdb34a0bcf54f7b2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30780723"
---
# <a name="working-with-tables-in-the-ios-designer"></a>Utilizzo di tabelle nella finestra di progettazione iOS

Storyboard WYSIWYG consentono di creare applicazioni iOS e sono supportati all'interno di Visual Studio nel Mac e Windows. Per ulteriori informazioni su storyboard, consultare il [Introduzione a storyboard](~/ios/user-interface/storyboards/index.md) documento. Gli storyboard consentono inoltre di modificare il layout della cella *in* la tabella, che semplifica lo sviluppo con le tabelle e le celle

Quando si configurano le proprietà di visualizzazione di una tabella nella finestra di progettazione iOS, sono disponibili due tipi di contenuto della cella è possibile scegliere tra: **dinamica** o **statico** contenuto prototipo.

<a name="Prototype_Content" />

## <a name="dynamic-prototype-content"></a>Contenuto dinamico prototipo

Oggetto `UITableView` con prototipo contenuto in genere consente di visualizzare un elenco di dati in cui la cella prototipo (o le celle, come è possono definire più di uno) vengono riutilizzate per ogni elemento nell'elenco. Le celle non è necessario creare un'istanza, ma vengono ottenuti nel `GetView` metodo chiamando il `DequeueReusableCell` metodo del relativo `UITableViewSource`.

 <a name="Static_Content" />


## <a name="static-content"></a>Contenuto statico

`UITableView`con contenuto statico consente di progettare direttamente nell'area di progettazione tabelle. Le celle possono essere trascinate nella tabella e personalizzate modificando le proprietà e l'aggiunta di controlli.

 <a name="Creating_a_Storyboard-driven_app" />


## <a name="creating-a-storyboard-driven-app"></a>Creazione di un'applicazione basata su uno Storyboard

L'esempio StoryboardTable contiene una semplice applicazione master-dettagli che usa entrambi i tipi di UITableView in uno Storyboard. Nella parte restante di questa sezione viene descritto come creare un esempio di elenco attività di piccole dimensioni che sarà simile al seguente al termine dell'operazione:

 [![Schermate di esempio](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

Utilizzano un UITableView entrambi gli schermi l'interfaccia utente verrà compilato con uno storyboard. La schermata principale Usa *contenuto prototipo* e il layout di riga, i dettagli sullo schermo utilizza *contenuto statico* per creare un form di immissione di dati utilizzando i layout di cella personalizzato.

## <a name="walkthrough"></a>Procedura dettagliata

Creare una nuova soluzione in Visual Studio usando **(Create) nuovo progetto > singola vista App(C#)** e lo chiama _StoryboardTables_.

 [![Creare una finestra di dialogo Nuovo progetto](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

Verrà aperta la soluzione con alcuni file c# e un `Main.storyboard` file già creata. Fare doppio clic su di `Main.storyboard` file per aprirlo nella finestra di progettazione iOS.

<a name="Modifying_the_Storyboard" />

## <a name="modifying-the-storyboard"></a>Modifica lo Storyboard

Lo storyboard verrà modificato in tre passaggi:

-  Prima di tutto, layout necessarie consente di visualizzare i controller e impostare le relative proprietà.
-  In secondo luogo, creare un'interfaccia utente trascinando e rilasciando oggetti nella propria visualizzazione
-  Infine, aggiungere la classe UIKit richiesta per ogni visualizzazione e assegnare un nome diversi controlli in modo è possibile farvi riferimento nel codice.


Una volta completato lo storyboard, è possibile aggiungere codice per creare tutti gli elementi di lavoro.

<a name="Layout_The_View_Controllers" />

### <a name="layout-the-view-controllers"></a>Il controller di visualizzazione layout

La prima modifica allo storyboard è eliminazione della visualizzazione di dettaglio esistente e sostituirlo con un UITableViewController. Attenersi ai passaggi riportati di seguito.

1.  Selezionare la barra nella parte inferiore del Controller di visualizzazione ed eliminarlo.
2.  Trascinare un **navigazione Controller** e **tabella View Controller** nello Storyboard dalla casella degli strumenti. 
3.  Creare un segue dal Controller di visualizzazione principale per il secondo Controller di visualizzazione di tabella che è stato appena aggiunto. Per creare la segue, controllo e trascinare *dalla cella dettaglio* per il UITableViewController appena aggiunto. Scegliere l'opzione **Mostra*** in **selezione definire**. 
4.  Selezionare il nuovo definire creato e assegnargli un identificatore per questo definire nel codice di riferimento. Fare clic su di segue e immettere `TaskSegue` per il **identificatore** nel **proprietà riempimento**, come segue:    
  [![Definire i nomi nel Pannello proprietà](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. Configurare quindi le due visualizzazioni di tabella, selezionarli e utilizzare il riquadro proprietà. Assicurarsi di selezionare una visualizzazione e non i Controller di visualizzazione, è possibile utilizzare la struttura del documento per facilitare la selezione.

6.  Modificare il Controller di visualizzazione principale **contenuto: prototipi dinamica** (la visualizzazione nell'area di progettazione dicitura **prototipo contenuto** ):

    [![Imposta la proprietà di contenuto dinamici prototipi](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7.  Modificare il nuovo **UITableViewController** da **contenuto: celle statico**. 


8. Il nuovo UITableViewController deve avere il nome della classe e l'identificatore impostato. Selezionare il Controller di visualizzazione e il tipo _TaskDetailViewController_ per il **classe** nel **proprietà riempimento** : verrà creato un nuovo `TaskDetailViewController.cs` file della soluzione Riempimento. Immettere il **StoryboardID** come _dettaglio_, come illustrato nell'esempio riportato di seguito. Verrà utilizzato in un secondo momento per caricare la visualizzazione nel codice c#:  

    [![Impostazione dell'ID di Storyboard](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. Nella finestra di progettazione dello storyboard a questo punto dovrebbe essere simile al seguente (titolo elemento di navigazione del Controller di visualizzazione principale è stato modificato in "Utente doveva avere discussioni"):

    [![Area di progettazione](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  



<a name="Create_the_UI" />

### <a name="create-the-ui"></a>Creare l'interfaccia utente

Ora che le viste e segues sono configurati, gli elementi dell'interfaccia utente devono essere aggiunti.

#### <a name="root-view-controller"></a>Controller visualizzazione radice

Innanzitutto, selezionare la cella prototipo nel Controller di visualizzazione Master e imposta il **identificatore** come _taskcell_, come illustrato di seguito. Per recuperare un'istanza di questo UITableViewCell, questo verrà utilizzato in un secondo momento nel codice:

 [![impostare l'identificatore della cella](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

Successivamente, è necessario creare un pulsante di aggiunta di nuove attività, come illustrato di seguito:

[![elemento pulsante nella barra di spostamento della barra](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

Seguire questa procedura: 

-  Trascinare un **elemento pulsante della barra** dalla casella degli strumenti di _lato destro della barra di spostamento_.
-  Nel **proprietà riempimento**in **elemento pulsante della barra** selezionare **identificatore: aggiungere** (per renderlo un *+* plus pulsante). 
-  Assegnare un nome in modo da poter essere identificato nel codice in una fase successiva. Si noti che è necessario assegnare un nome di classe di Controller di visualizzazione principale (ad esempio **ItemViewController**) consente di impostare il nome dell'elemento del pulsante della barra.


#### <a name="taskdetail-view-controller"></a>TaskDetail View Controller

Nel riquadro Visualizzazione dettagli richiede molto più lavoro. Visualizzazione delle celle devono essere trascinato la visualizzazione e quindi popolato con le etichette, visualizzazioni di testo e i pulsanti. La schermata seguente mostra l'interfaccia utente completato con due sezioni. Una sezione ha tre celle, tre etichette, uno e due campi di testo, mentre la seconda sezione ha una cella con due pulsanti:

 [![visualizzazione dettagli](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

I passaggi per compilare il layout completo sono:

Selezionare la visualizzazione della tabella e aprire il **proprietà riempimento**. Aggiornare le proprietà seguenti:

-  **Nelle sezioni**: _2_ 
-  **Stile**: _raggruppati_
-  **Separatore**: _nessuno_
-  **Selezione**: _Nessuna selezione_

Selezionare la sezione superiore e in **proprietà > sezione visualizzazione tabella** modificare **righe** a _3_, come illustrato di seguito:


 [![l'impostazione nella sezione superiore a tre righe](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

Per ogni cella aprire il **proprietà riempimento** e impostare:

-  **Stile**: _personalizzato_
-  **Identificatore**: scegliere un identificatore univoco per ogni cella (ad es. "_titolo_","_note_","_eseguita_").
-  Trascinare i controlli necessari per produrre il layout mostrato nella schermata (inserire **UILabel**, **UITextField** e **UISwitch** le celle corrette e impostare le etichette in modo appropriato, Internet Explorer. Titolo, le note ed eseguita).


Nella seconda sezione, impostare **righe** a _1_ e trascinare il quadratino di ridimensionamento inferiore della cella per renderlo più alta.

-  **Impostare l'identificatore di**: su un valore univoco (ad es. "Salva"). 
-  **Impostare lo sfondo**: _cancellare il colore_ .
-  Trascinare due pulsanti nella cella e impostare il titolo in modo appropriato (ad esempio _salvare_ e _eliminare_), come illustrato di seguito:

   [![configurare due pulsanti nella parte inferiore](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

A questo punto si può inoltre si desidera impostare vincoli per le celle e i controlli per garantire un layout adattivo.

### <a name="adding-uikit-class-and-naming-controls"></a>Aggiunta di classe UIKit e denominazione dei controlli

Esistono alcuni passaggi finali per la creazione di questo Storyboard. Innanzitutto è necessario assegnare i controlli un nome in **identità > nome** possono essere utilizzati nel codice in un secondo momento. Questi nomi come indicato di seguito:

-  **Titolo UITextField** : _Text_
-  **Note sulla UITextField** : _NotesText_
-  **UISwitch** : _DoneSwitch_
-  **Eliminare UIButton** : _DeleteButton_
-  **Salvare UIButton** : _PulsanteSalva_


<a name="Adding_Code" />

## <a name="adding-code"></a>Aggiunta di codice

Verrà eseguito il resto del lavoro in Visual Studio nel Mac o Windows con c#. Si noti che i nomi delle proprietà utilizzate nel codice riflettano quelli impostati nella procedura dettagliata precedente.

Prima di voler creare un `Chores` (classe), che fornirà un modo per ottenere e impostare il valore di ID, nome, le note e di fatto booleano, in modo da poter utilizzare i valori in tutta l'applicazione.

Nel `Chores` classe aggiungere il codice seguente:

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

Creare quindi un `RootTableSource` classe che eredita da `UITableViewSource`. 

La differenza tra questo e visualizzazione di una tabella non Storyboard è che il `GetView` (metodo) non è necessario creare un'istanza di qualsiasi cella – `theDequeueReusableCell` metodo restituirà sempre un'istanza della cella prototipo (con un identificatore corrispondente).

Il codice seguente è dal `RootTableSource.cs` file:

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

Utilizzare il `RootTableSource` di classi, creare una nuova raccolta nel `ItemViewController`del costruttore:

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

Se si esegue l'app a questo punto, la schermata principale verrà ora caricare e visualizzare un elenco di due attività. Quando un'attività viene eseguita la segue definito dallo storyboard causerà venga visualizzata la schermata di dettaglio, ma non visualizzerà tutti i dati al momento.

Per 'inviare un parametro' in un segue, eseguire l'override di `PrepareForSegue` metodo e impostare le proprietà per il `DestinationViewController` (il `TaskDetailViewController` in questo esempio). La classe di Controller di vista di destinazione verrà creata ma non è ancora visualizzato all'utente, pertanto è possibile impostare proprietà sulla classe ma non modificare i controlli dell'interfaccia utente:

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

In `TaskDetailViewController` il `SetTask` metodo assegna i relativi parametri alle proprietà in modo è possibile farvi riferimento in ViewWillAppear. Le proprietà del controllo non possono essere modificate in `SetTask` perché potrebbe non esistere quando `PrepareForSegue` viene chiamato:

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

La segue verrà aprire la schermata di dettaglio e visualizzare le informazioni sull'attività selezionata. Purtroppo non vi è Nessuna implementazione per il **salvare** e **eliminare** pulsanti. Prima di implementare i pulsanti, aggiungere tali metodi **ItemViewController.cs** per aggiornare i dati sottostanti e chiudere la schermata di dettaglio:

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

Successivamente, è necessario aggiungere il pulsante `TouchUpInside` gestore eventi per il `ViewDidLoad` metodo **TaskDetailViewController.cs**. Il `Delegate` riferimento alla proprietà di `ItemViewController` è stata creata in modo specifico, pertanto è possibile chiamare `SaveTask` e `DeleteTask`, cui chiudere questa visualizzazione come parte del relativo funzionamento:

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

L'ultima parte rimanente di funzionalità per compilare prevede la creazione di nuove attività. In **ItemViewController.cs** aggiungere un metodo che crea nuove attività e apre la visualizzazione dei dettagli. Per creare un'istanza di una vista da un utilizzo di uno storyboard di `InstantiateViewController` metodo con il `Identifier` per la visualizzazione, in questo esempio sarà 'detail':

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

Infine, collegare il pulsante nella barra di spostamento in **ItemViewController.cs**del `ViewDidLoad` metodo da chiamare:

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

Che consente di completare l'esempio di Storyboard: l'applicazione finita ha un aspetto simile al seguente:

[![Applicazione finita](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

Nell'esempio viene illustrato:

-  Creazione di una tabella con prototipo contenuto in cui le celle vengono definite per il riutilizzo visualizzare gli elenchi di dati. 
-  Creazione di una tabella con contenuto statico per creare un form di input. Sono stati la modifica dello stile di tabella e l'aggiunta di sezioni, celle e controlli dell'interfaccia utente. 
-  Come creare un segue ed eseguire l'override di `PrepareForSegue` metodo per notificare la vista di destinazione di eventuali parametri richiesti. 
-  Il caricamento di uno storyboard viste direttamente con il `Storyboard.InstantiateViewController` metodo.



## <a name="related-links"></a>Collegamenti correlati

- [StoryboardTable (esempio)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
