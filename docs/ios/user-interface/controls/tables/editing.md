---
title: Modifica
ms.topic: article
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: eb8bae676a4b5c682cdb204c6d38ffc1112b483a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="editing"></a>Modifica

Funzionalità di modifica nella tabella sono abilitate per l'override dei metodi in un `UITableViewSource` sottoclasse. Il comportamento di modifica più semplice è il movimento di scorrere rapidamente per eliminare che può essere implementato con un override del metodo singolo.
La modifica più complesso (incluso lo spostamento di righe) può essere eseguita con la tabella in modalità di modifica.

## <a name="swipe-to-delete"></a>Scorrere rapidamente per eliminazione

Scorrere per eliminare una funzione è un movimento naturale in iOS che gli utenti. 

 [![](editing-images/image10.png "Esempio di scorrere di eliminazione")](editing-images/image10.png#lightbox)

Esistono tre override dei metodi che interessano il movimento di scorrere per visualizzare un **eliminare** pulsante in una cella:

-   **CommitEditingStyle** : l'origine della tabella viene rilevato se questo metodo viene sottoposto a override e abilita automaticamente la combinazione di scorrere rapidamente per eliminare. L'implementazione del metodo deve chiamare `DeleteRows` sul `UITableView` affinché le celle vengono rimossi e rimuovere anche i dati sottostanti dal modello (ad esempio, una matrice, dizionario o database). 
-   **CanEditRow** – CommitEditingStyle se viene eseguito l'override, si presuppone che tutte le righe sono modificabili. Se questo metodo viene implementato e restituisce false (per alcune righe specifiche o per tutte le righe), la combinazione di scorrere rapidamente per l'eliminazione non sarà più disponibile in tale cella. 
-   **TitleForDeleteConfirmation** : facoltativamente specifica il testo per il **eliminare** pulsante. Se questo metodo non è stato implementato il testo del pulsante sarà "Delete". 


Questi metodi sono implementati nel `TableSource` classe indicato di seguito:

```csharp
public override void CommitEditingStyle (UITableView tableView, UITableViewCellEditingStyle editingStyle, Foundation.NSIndexPath indexPath)
{
    switch (editingStyle) {
        case UITableViewCellEditingStyle.Delete:
            // remove the item from the underlying data source
            tableItems.RemoveAt(indexPath.Row);
            // delete the row from the table
            tableView.DeleteRows (new NSIndexPath[] { indexPath }, UITableViewRowAnimation.Fade);
            break;
        case UITableViewCellEditingStyle.None:
            Console.WriteLine ("CommitEditingStyle:None called");
            break;
    }
}
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override string TitleForDeleteConfirmation (UITableView tableView, NSIndexPath indexPath)
{   // Optional - default text is 'Delete'
    return "Trash (" + tableItems[indexPath.Row].SubHeading + ")";
}
```

Per questo esempio il `UITableViewSource` è stato aggiornato per utilizzare un `List<TableItem>` (anziché una matrice di stringhe) come origine dati, poiché supporta aggiunta ed eliminazione di elementi dalla raccolta.


## <a name="edit-mode"></a>Modalità di modifica

Quando una tabella è in modalità di modifica l'utente visualizza un widget rosso 'stop' in ogni riga, che rivela un pulsante Elimina quando interessate. Nella tabella visualizza anche un'icona di "handle" per indicare che è possibile trascinare la riga per modificare l'ordine.
Il **TableEditMode** esempio implementa queste funzionalità, come illustrato.

 [![](editing-images/image11.png "L'esempio TableEditMode implementa queste funzionalità, come illustrato")](editing-images/image11.png#lightbox)

Non sono presenti in una serie di metodi diversi `UITableViewSource` che influiscono sul comportamento di modalità di modifica di una tabella:

-   **CanEditRow** : indica se è possibile modificare ogni riga. Restituisce false per impedire scorrere per eliminare sia l'eliminazione nella modalità di modifica. 
-   **CanMoveRow** : restituito true per abilitare il 'quadratino di spostamento' o false per evitare lo spostamento. 
-   **EditingStyleForRow** : quando la tabella è in modalità di modifica, il valore restituito da questo metodo determina se la cella viene visualizzata l'icona di eliminazione rosso o verde icona Aggiungi. Restituire `UITableViewCellEditingStyle.None` se la riga non deve essere modificabile. 
-   **MoveRow esposto** : chiamato quando una riga viene spostata in modo che la struttura di dati sottostante può essere modificata per corrispondere ai dati così come appare nella tabella. 


L'implementazione per i primi tre metodi è relativamente semplice, a meno che non si desidera utilizzare il `indexPath` per modificare il comportamento di righe specifiche, impostare come hardcoded solo i valori restituiti per l'intera tabella.

```csharp
public override bool CanEditRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you wish to disable editing for a specific indexPath or for all rows
}
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return true; // return false if you don't allow re-ordering
}
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    return UITableViewCellEditingStyle.Delete; // this example doesn't support Insert
}
```

Il `MoveRow` implementazione è leggermente più complessa perché è necessario modificare la struttura di dati sottostante in base all'ordine di nuovo. Poiché i dati viene implementati come un `List` nel codice seguente elimina l'elemento di dati in una posizione precedente e lo inserisce nella nuova posizione. Se i dati è stati archiviati in una tabella di database SQLite con una colonna 'order' (ad esempio), questo metodo invece sarebbe necessario eseguire alcune operazioni SQL per riordinare i numeri nella colonna.

```csharp
public override void MoveRow (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath destinationIndexPath)
{
    var item = tableItems[sourceIndexPath.Row];
    var deleteAt = sourceIndexPath.Row;
    var insertAt = destinationIndexPath.Row;
    
    // are we inserting 
    if (destinationIndexPath.Row < sourceIndexPath.Row) {
        // add one to where we delete, because we're increasing the index by inserting
        deleteAt += 1;
    } else {
        // add one to where we insert, because we haven't deleted the original yet
        insertAt += 1;
    }
    tableItems.Insert (insertAt, item);
    tableItems.RemoveAt (deleteAt);
}
```

Infine, per ottenere la tabella in modalità di modifica, la **modifica** pulsante deve chiamare `SetEditing` simile

```csharp
table.SetEditing (true, true);
```

e quando l'utente termina la modifica, il **eseguita** pulsante necessario disattivare la modalità di modifica:

```csharp
table.SetEditing (false, true);
```


## <a name="row-insertion-editing-style"></a>Stile di modifica di inserimento riga

Inserimento di righe all'interno della tabella è un'interfaccia utente comune: l'esempio in applicazioni iOS standard principale è il **Modifica contatto** dello schermo. Questa schermata viene illustrato il funzionamento della funzionalità di inserimento di righe: in modalità di modifica modalità è presente un'ulteriore riga che (quando si fa clic) inserisce righe aggiuntive nei dati. Quando la modifica è stata completata, il file temporaneo **(aggiungere)** riga viene rimossa.

 [![](editing-images/image12.png "Quando la modifica è stata completata, il file temporaneo Aggiungi nuova riga viene rimossa")](editing-images/image12.png#lightbox)

Non sono presenti in una serie di metodi diversi `UITableViewSource` che influiscono sul comportamento di modalità di modifica di una tabella. Questi metodi sono stati implementati come indicato di seguito nel codice di esempio:

-   **EditingStyleForRow** – restituisce `UITableViewCellEditingStyle.Delete` per le righe contenenti dati e restituisce `UITableViewCellEditingStyle.Insert` per l'ultima riga (che verrà aggiunto in particolare si comporti come un pulsante Inserisci). 
-   **CustomizeMoveTarget** : mentre l'utente sta spostando una cella, il valore restituito da questo metodo facoltativo può eseguire l'override di loro scelta della posizione. Ciò significa che è possibile impedire il 'eliminazione' la cella in determinate posizioni, ad esempio in questo esempio che impedisce qualsiasi riga lo spostamento da dopo il **(aggiungere)** riga. 
-   **CanMoveRow** : restituito true per abilitare il 'quadratino di spostamento' o false per evitare lo spostamento. Nell'esempio, l'ultima riga presenta il 'quadratino di spostamento' nascosto in quanto si tratti di un pulsante Inserisci solo al server. 


È inoltre possibile aggiungere due metodi personalizzati per aggiungere la riga 'dell'istruzione insert' e quindi di rimuovere nuovamente quando non è più necessario. Vengono chiamati i **modifica** e **eseguita** pulsanti:

-   **WillBeginTableEditing** : quando il **modifica** pulsante interessate, chiamate `SetEditing` per inserire la tabella in modalità di modifica. Questa operazione attiva il metodo WillBeginTableEditing, in cui viene visualizzato il **(aggiungere)** riga alla fine della tabella di agire come un pulsante di' insert'. 
-   **DidFinishTableEditing** : quando è toccato il pulsante Chiudi `SetEditing` viene chiamata nuovamente per disattivare la modalità di modifica. Nell'esempio di codice rimuove il **(aggiungere)** riga dalla tabella durante la modifica non è più necessario. 


Vengono implementati questi override del metodo nel file di esempio **TableEditModeAdd/Code/TableSource.cs**:

```csharp
public override UITableViewCellEditingStyle EditingStyleForRow (UITableView tableView, NSIndexPath indexPath)
{
    if (tableView.Editing) {
        if (indexPath.Row == tableView.NumberOfRowsInSection (0) - 1)
            return UITableViewCellEditingStyle.Insert;
        else
            return UITableViewCellEditingStyle.Delete;
    } else // not in editing mode, enable swipe-to-delete for all rows
        return UITableViewCellEditingStyle.Delete;
}
public override NSIndexPath CustomizeMoveTarget (UITableView tableView, NSIndexPath sourceIndexPath, NSIndexPath proposedIndexPath)
{
    var numRows = tableView.NumberOfRowsInSection (0) - 1; // less the (add new) one
    if (proposedIndexPath.Row >= numRows)
        return NSIndexPath.FromRowSection(numRows - 1, 0);
    else
        return proposedIndexPath;
} 
public override bool CanMoveRow (UITableView tableView, NSIndexPath indexPath)
{
    return indexPath.Row < tableView.NumberOfRowsInSection (0) - 1;
}
```

Questi due metodi personalizzati consentono di aggiungere e rimuovere il **(aggiungere)** riga quando la modalità di modifica della tabella è abilitata o disabilitata:

```csharp
public void WillBeginTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // insert the 'ADD NEW' row at the end of table display
    tableView.InsertRows (new NSIndexPath[] { 
            NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0), 0) 
        }, UITableViewRowAnimation.Fade);
    // create a new item and add it to our underlying data (it is not intended to be permanent)
    tableItems.Add (new TableItem ("(add new)"));
    tableView.EndUpdates (); // applies the changes
}
public void DidFinishTableEditing (UITableView tableView)
{
    tableView.BeginUpdates ();
    // remove our 'ADD NEW' row from the underlying data
    tableItems.RemoveAt ((int)tableView.NumberOfRowsInSection (0) - 1); // zero based :)
    // remove the row from the table display
    tableView.DeleteRows (new NSIndexPath[] { NSIndexPath.FromRowSection (tableView.NumberOfRowsInSection (0) - 1, 0) }, UITableViewRowAnimation.Fade);
    tableView.EndUpdates (); // applies the changes
}
```

Infine, questo codice crea un'istanza di **modifica** e **eseguita** pulsanti, le espressioni lambda che abilitano o disabilitano la modalità di modifica quando sono interessate:

```csharp
done = new UIBarButtonItem(UIBarButtonSystemItem.Done, (s,e)=>{
    table.SetEditing (false, true);
    NavigationItem.RightBarButtonItem = edit;
    tableSource.DidFinishTableEditing(table);
});
edit = new UIBarButtonItem(UIBarButtonSystemItem.Edit, (s,e)=>{
    if (table.Editing)
        table.SetEditing (false, true); // if we've half-swiped a row
    tableSource.WillBeginTableEditing(table);
    table.SetEditing (true, true);
    NavigationItem.LeftBarButtonItem = null;
    NavigationItem.RightBarButtonItem = done;
});
```

Questo modello di interfaccia utente di inserimento riga non viene utilizzato molto spesso, ma è anche possibile utilizzare il `UITableView.BeginUpdates` e `EndUpdates` metodi per animare l'inserimento o la rimozione di celle in una tabella. La regola per l'utilizzo di questi metodi è che la differenza nel valore restituito da `RowsInSection` tra il `BeginUpdates` e `EndUpdates` chiamate devono corrispondere al numero di rete di celle aggiunti o eliminati con la `InsertRows` e `DeleteRows` metodi. Se l'origine dati sottostante non viene modificato per corrispondere gli inserimenti o eliminazioni nella visualizzazione tabella si verificherà un errore.


## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithTables (esempio)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
