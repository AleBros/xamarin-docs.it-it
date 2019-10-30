---
title: Utilizzo delle tabelle in iOS Designer
description: Nelle sezioni precedenti è stato illustrato lo sviluppo con le tabelle. Nella quinta e ultima sezione, verranno aggregate le informazioni apprese finora e verrà creata un'applicazione di base per l'elenco di attività usando uno storyboard.
ms.prod: xamarin
ms.assetid: D8416E10-481A-0B6E-4081-B146E6358004
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 7eff434c21b5e2330d320f2eb85174dc6fe65b34
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021937"
---
# <a name="working-with-tables-in-the-ios-designer"></a>Utilizzo delle tabelle in iOS Designer

Gli storyboard sono un modo WYSIWYG per creare applicazioni iOS e sono supportati all'interno di Visual Studio in Mac e Windows. Per ulteriori informazioni sugli storyboard, fare riferimento al documento [introduttivo per gli storyboard](~/ios/user-interface/storyboards/index.md) . Gli storyboard consentono inoltre di modificare i layout delle celle *nella* tabella, semplificando lo sviluppo con tabelle e celle

Quando si configurano le proprietà di una vista tabella in iOS designer, è possibile scegliere tra due tipi di contenuto di celle: contenuto **dinamico** o del prototipo **statico** .

<a name="Prototype_Content" />

## <a name="dynamic-prototype-content"></a>Contenuto dinamico del prototipo

Un `UITableView` con contenuto del prototipo viene in genere progettato per visualizzare un elenco di dati in cui la cella del prototipo (o le celle, che è possibile definire più di uno) vengono riutilizzate per ogni elemento nell'elenco. Non è necessario creare un'istanza delle celle, che vengono ottenute nel metodo `GetView` chiamando il metodo `DequeueReusableCell` della relativa `UITableViewSource`.

 <a name="Static_Content" />

## <a name="static-content"></a>Contenuto statico

`UITableView`s con contenuto statico consentono di progettare le tabelle direttamente nell'area di progettazione. È possibile trascinare le celle nella tabella e personalizzarle modificando le proprietà e aggiungendo i controlli.

 <a name="Creating_a_Storyboard-driven_app" />

## <a name="creating-a-storyboard-driven-app"></a>Creazione di un'app basata su storyboard

L'esempio StoryboardTable contiene una semplice app Master-Detail che usa entrambi i tipi di UITableView in uno storyboard. Nella parte restante di questa sezione viene descritto come creare un piccolo esempio di elenco attività che sarà simile al seguente:

 [schermate di esempio![](creating-tables-in-a-storyboard-images/image13a.png)](creating-tables-in-a-storyboard-images/image13a.png#lightbox)

L'interfaccia utente verrà compilata con uno storyboard ed entrambe le schermate utilizzeranno un UITableView. Nella schermata principale viene utilizzato il *contenuto del prototipo* per il layout della riga e nella schermata dei dettagli viene utilizzato il *contenuto statico* per creare un form di immissione dati utilizzando layout di celle personalizzati.

## <a name="walkthrough"></a>Procedura dettagliata

Crea una nuova soluzione in Visual Studio usando **(Crea) nuovo progetto... > App visualizzazione singola (C#)** e denominarla _StoryboardTables_.

 [![creare una finestra di dialogo nuovo progetto](creating-tables-in-a-storyboard-images/npd.png)](creating-tables-in-a-storyboard-images/npd.png#lightbox)

La soluzione si aprirà con C# alcuni file e un file di`Main.storyboard`già creato. Fare doppio clic sul file `Main.storyboard` per aprirlo in iOS designer.

<a name="Modifying_the_Storyboard" />

## <a name="modifying-the-storyboard"></a>Modifica dello storyboard

Lo storyboard verrà modificato in tre passaggi:

- Prima di tutto, impostare i controller di visualizzazione richiesti e impostare le relative proprietà.
- In secondo luogo, creare l'interfaccia utente trascinando e rilasciando gli oggetti nella visualizzazione
- Infine, aggiungere la classe UIKit necessaria a ogni visualizzazione e assegnare a vari controlli un nome in modo che sia possibile farvi riferimento nel codice.

Una volta completato lo storyboard, è possibile aggiungere il codice per eseguire tutto il lavoro.

<a name="Layout_The_View_Controllers" />

### <a name="layout-the-view-controllers"></a>Layout dei controller di visualizzazione

La prima modifica allo storyboard è l'eliminazione della visualizzazione dei dettagli esistente e la relativa sostituzione con un UITableViewController. Attenersi ai passaggi riportati di seguito.

1. Selezionare la barra nella parte inferiore del controller di visualizzazione ed eliminarla.
2. Trascinare un **controller di spostamento** e un **controller di visualizzazione tabella** nello storyboard dalla casella degli strumenti. 
3. Creare un segue dal controller di visualizzazione radice al secondo controller di visualizzazione tabella appena aggiunto. Per creare segue, controllare e trascinare *dalla cella detail* al UITableViewController appena aggiunto. Scegliere l'opzione **Mostra** in **selezione segue**. 
4. Selezionare il nuovo segue creato e assegnargli un identificatore per fare riferimento a questo segue nel codice. Fare clic su segue e immettere `TaskSegue` per l' **identificatore** nel **riquadro delle proprietà**, come segue:    
  [![denominazione segue nel pannello Proprietà](creating-tables-in-a-storyboard-images/image16a-sml.png)](creating-tables-in-a-storyboard-images/image16a.png#lightbox) 

5. Configurare quindi le due viste tabella selezionandola e usando il riquadro delle proprietà. Assicurarsi di selezionare Visualizza e non Visualizza controller: è possibile usare la struttura del documento per facilitare la selezione.

6. Modificare il controller visualizzazione radice in modo che sia **contenuto: prototipi dinamici** (la visualizzazione del area di progettazione verrà etichettata **contenuto del prototipo** ):

    [![impostazione della proprietà Content su prototipi dinamici](creating-tables-in-a-storyboard-images/image17a.png)](creating-tables-in-a-storyboard-images/image17a.png#lightbox)

7. Modificare il nuovo **UITableViewController** in modo che sia **contenuto: celle statiche**. 

8. Il nuovo UITableViewController deve avere il nome della classe e l'identificatore impostati. Selezionare il controller di visualizzazione e digitare _TaskDetailViewController_ per la **classe** nel **riquadro delle proprietà** : verrà creato un nuovo file di `TaskDetailViewController.cs` nel riquadro della soluzione. Immettere il **StoryboardID** come _dettaglio_, come illustrato nell'esempio riportato di seguito. Verrà usato in un secondo momento per caricare questa visualizzazione C# nel codice:  

    [![l'impostazione dell'ID storyboard](creating-tables-in-a-storyboard-images/image18a.png)](creating-tables-in-a-storyboard-images/image18a.png#lightbox)

9. L'area di progettazione dello storyboard dovrebbe ora essere simile alla seguente (il titolo dell'elemento di navigazione del controller visualizzazione radice è stato modificato in "lavagna di lavoro"):

    [![area di progettazione](creating-tables-in-a-storyboard-images/image20a-sml.png)](creating-tables-in-a-storyboard-images/image20a.png#lightbox)  

<a name="Create_the_UI" />

### <a name="create-the-ui"></a>Creare l'interfaccia utente

Ora che le visualizzazioni e gli elementi segue sono configurati, è necessario aggiungere gli elementi dell'interfaccia utente.

#### <a name="root-view-controller"></a>Controller visualizzazione radice

Per prima cosa, selezionare la cella Prototype nel controller di visualizzazione master e impostare l' **identificatore** come _taskcell_, come illustrato di seguito. Questa operazione verrà utilizzata in un secondo momento nel codice per recuperare un'istanza di questo UITableViewCell:

 [![l'impostazione dell'identificatore di cella](creating-tables-in-a-storyboard-images/image22a-sml.png)](creating-tables-in-a-storyboard-images/image22a.png#lightbox)

Successivamente, sarà necessario creare un pulsante che aggiungerà nuove attività, come illustrato di seguito:

[elemento del pulsante della barra![nella barra di spostamento](creating-tables-in-a-storyboard-images/image23-sml.png)](creating-tables-in-a-storyboard-images/image23.png#lightbox)

Procedere come descritto di seguito: 

- Trascinare un **elemento pulsante della barra** dalla casella degli strumenti sul _lato destro della barra di spostamento_.
- Nel **riquadro delle proprietà**, in **elemento pulsante della barra** selezionare **identificatore: Aggiungi** (per impostarlo come *+* pulsante più). 
- Assegnare un nome in modo che possa essere identificato nel codice in una fase successiva. Si noti che sarà necessario assegnare al controller di visualizzazione radice un nome di classe, ad esempio **ItemViewController**, per consentire di impostare il nome dell'elemento del pulsante a barre.

#### <a name="taskdetail-view-controller"></a>Controller di visualizzazione TaskDetail

La visualizzazione dettagli richiede molto altro lavoro. Le celle di visualizzazione tabella devono essere trascinate nella vista e quindi compilate con etichette, visualizzazioni di testo e pulsanti. La schermata seguente mostra l'interfaccia utente finita con due sezioni. Una sezione ha tre celle, tre etichette, due campi di testo e un'opzione, mentre la seconda sezione ha una cella con due pulsanti:

 [layout visualizzazione dettagli![](creating-tables-in-a-storyboard-images/image24a-sml.png)](creating-tables-in-a-storyboard-images/image24a.png#lightbox)

I passaggi per compilare il layout completo sono:

Selezionare la visualizzazione tabella e aprire il **riquadro delle proprietà**. Aggiornare le proprietà seguenti:

- **Sezioni**: _2_ 
- **Stile**: _raggruppati_
- **Separatore**: _nessuno_
- **Selezione**: _Nessuna selezione_

Selezionare la sezione superiore e nella **sezione proprietà > tabella vista** modificare le **righe** in _3_, come illustrato di seguito:

 [![l'impostazione della sezione superiore su tre righe](creating-tables-in-a-storyboard-images/image29-sml.png)](creating-tables-in-a-storyboard-images/image29.png#lightbox)

Per ogni cella aprire il **riquadro delle proprietà** e impostare:

- **Stile**: _personalizzato_
- **Identificatore**: scegliere un identificatore univoco per ogni cella, ad esempio "_title_", "_Notes_", "_done_").
- Trascinare i controlli necessari per produrre il layout visualizzato nello screenshot (posizionare **UILabel**, **UITextField** e **UISwitch** nelle celle corrette e impostare le etichette in modo appropriato, ad esempio. Titolo, note ed operazione eseguita).

Nella seconda sezione impostare le **righe** su _1_ e il quadratino di ridimensionamento inferiore della cella per renderla più alta.

- **Impostare l'identificatore**su un valore univoco, ad esempio "Salva"). 
- **Impostare lo sfondo**: _Cancella colore_ .
- Trascinare due pulsanti sulla cella e impostare i rispettivi titoli in modo appropriato, ad esempio _Save_ ed _Delete_, come illustrato di seguito:

   [![impostazione di due pulsanti nella sezione inferiore](creating-tables-in-a-storyboard-images/image30-sml.png)](creating-tables-in-a-storyboard-images/image30.png#lightbox)

A questo punto è anche possibile impostare vincoli per le celle e i controlli per garantire un layout adattivo.

### <a name="adding-uikit-class-and-naming-controls"></a>Aggiunta di controlli di denominazione e classe UIKit

Ci sono alcuni passaggi finali della creazione dello storyboard. Per prima cosa è necessario assegnare a ognuno dei controlli un nome in **Identity > nome** in modo che possano essere usati nel codice in un secondo momento. Denominarli come segue:

- **Titolo UITextField** : _TitleText_
- **Note UITextField** : _NotesText_
- **UISwitch** : _DoneSwitch_
- **Eliminare UIButton** : _DeleteButton_
- **Salva UIButton** : _pulsanteSalva_

<a name="Adding_Code" />

## <a name="adding-code"></a>Aggiunta di codice

Il resto del lavoro verrà eseguito in Visual Studio in Mac o Windows con C#. Si noti che i nomi di proprietà usati nel codice riflettono quelli impostati nella procedura dettagliata precedente.

Per prima cosa si vuole creare una classe `Chores`, che consente di ottenere e impostare il valore di ID, nome, note e il valore booleano done, in modo che sia possibile usare tali valori nell'intera applicazione.

Nella classe `Chores` aggiungere il codice seguente:

```csharp
public class Chores {
    public int Id { get; set; }
    public string Name { get; set; }
    public string Notes { get; set; }
    public bool Done { get; set; }
  }
```

Successivamente, creare una classe `RootTableSource` che erediti da `UITableViewSource`. 

La differenza tra questa e una vista della tabella non Storyboard è che il metodo di `GetView` non deve creare un'istanza delle celle: `theDequeueReusableCell` metodo restituirà sempre un'istanza della cella del prototipo (con identificatore corrispondente).

Il codice riportato di seguito è del file `RootTableSource.cs`:

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

Per usare la classe `RootTableSource`, creare una nuova raccolta nel costruttore del `ItemViewController`:

```csharp
chores = new List<Chore> {
      new Chore {Name="Groceries", Notes="Buy bread, cheese, apples", Done=false},
      new Chore {Name="Devices", Notes="Buy Nexus, Galaxy, Droid", Done=false}
    };
```

In `ViewWillAppear` passare la raccolta all'origine e assegnarla alla visualizzazione tabella:

```csharp
public override void ViewWillAppear(bool animated)
{
    base.ViewWillAppear(animated);

    TableView.Source = new RootTableSource(chores.ToArray());
}
```

Se si esegue l'app ora, la schermata principale viene caricata e viene visualizzato un elenco di due attività. Quando un'attività viene toccata, il valore di segue definito dallo storyboard comporterà la visualizzazione della schermata dei dettagli, ma al momento non verranno visualizzati dati.

Per inviare un parametro in un segue, eseguire l'override del metodo `PrepareForSegue` e impostare le proprietà nell'`DestinationViewController` (il `TaskDetailViewController` in questo esempio). È stata creata un'istanza della classe del controller di visualizzazione di destinazione, che tuttavia non è ancora visualizzata all'utente. Ciò significa che è possibile impostare le proprietà della classe, ma non modificare i controlli dell'interfaccia utente:

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

In `TaskDetailViewController` il metodo `SetTask` assegna i parametri alle proprietà in modo che sia possibile farvi riferimento in ViewWillAppear. Impossibile modificare le proprietà del controllo in `SetTask` perché potrebbe non esistere quando viene chiamato `PrepareForSegue`:

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

Segue apre ora la schermata dei dettagli e visualizza le informazioni sull'attività selezionata. Sfortunatamente non è disponibile alcuna implementazione per i pulsanti **Salva** ed **Elimina** . Prima di implementare i pulsanti, aggiungere questi metodi a **ItemViewController.cs** per aggiornare i dati sottostanti e chiudere la schermata dei dettagli:

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

Successivamente, è necessario aggiungere il gestore dell'evento `TouchUpInside` del pulsante al metodo `ViewDidLoad` di **TaskDetailViewController.cs**. Il riferimento `Delegate` proprietà al `ItemViewController` è stato creato in modo specifico per poter chiamare `SaveTask` e `DeleteTask`, che chiude la visualizzazione come parte dell'operazione:

```csharp
SaveButton.TouchUpInside += (sender, e) => {
        currentTask.Name = TitleText.Text;
        currentTask.Notes = NotesText.Text;
        currentTask.Done = DoneSwitch.On;
        Delegate.SaveTask(currentTask);
      };

DeleteButton.TouchUpInside += (sender, e) => Delegate.DeleteTask(currentTask);
```

L'ultima parte rimanente delle funzionalità da compilare è la creazione di nuove attività. In **ItemViewController.cs** aggiungere un metodo che crei nuove attività e apra la visualizzazione dettagli. Per creare un'istanza di una vista da uno storyboard, usare il metodo `InstantiateViewController` con il `Identifier` per tale visualizzazione, in questo esempio che sarà' Detail ':

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

Infine, collegare il pulsante della barra di spostamento nel metodo `ViewDidLoad` di **ItemViewController.cs**per chiamarlo:

```csharp
AddButton.Clicked += (sender, e) => CreateTask ();
```

Questa operazione completa l'esempio Storyboard. l'app completata avrà un aspetto simile al seguente:

[app![completata](creating-tables-in-a-storyboard-images/image28a.png)](creating-tables-in-a-storyboard-images/image28a.png#lightbox)

Nell'esempio viene illustrato quanto segue:

- Creazione di una tabella con contenuto del prototipo in cui le celle vengono definite per riutilizzare per visualizzare elenchi di dati. 
- Creazione di una tabella con contenuto statico per compilare un modulo di input. Inclusa la modifica dello stile della tabella e l'aggiunta di sezioni, celle e controlli dell'interfaccia utente. 
- Come creare una segue ed eseguire l'override del metodo `PrepareForSegue` per notificare la visualizzazione di destinazione di tutti i parametri richiesti. 
- Caricamento delle visualizzazioni Storyboard direttamente con il metodo `Storyboard.InstantiateViewController`.

## <a name="related-links"></a>Collegamenti correlati

- [StoryboardTable (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/storyboardtable)
- [Introduzione agli storyboard](~/ios/user-interface/storyboards/index.md)
