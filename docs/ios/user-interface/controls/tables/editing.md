---
title: Modifica delle tabelle con xamarin. IOS
description: Questo documento viene descritto come modificare le tabelle in xamarin. IOS. Vengono illustrati scorrere rapidamente da eliminare, modificare la modalità e l'inserimento di riga.
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 1267de341a88130c18254f414d2fbb1c42595a0c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61195898"
---
# <a name="editing-tables-with-xamarinios"></a>Modifica delle tabelle con xamarin. IOS

Funzionalità di modifica nella tabella sono abilitate per l'override dei metodi in un `UITableViewSource` sottoclasse. Il comportamento di modifica più semplice è il movimento scorrimento rapido-a-delete che può essere implementato con un override del metodo singolo.
Più complessa la modifica (incluso lo spostamento di righe) può essere eseguita con la tabella in modalità di modifica.

## <a name="swipe-to-delete"></a>Scorrere rapidamente per Delete

Lo sfioramento eliminare funzionalità è un movimento naturale in iOS che gli utenti si aspettano. 

 [![](editing-images/image10.png "Esempio di scorrere rapidamente da eliminare")](editing-images/image10.png#lightbox)

Vi sono tre gli override dei metodi che interessano il gesto di scorrimento rapido per visualizzare un **eliminare** pulsante in una cella:

-   **CommitEditingStyle** : l'origine della tabella viene rilevato se questo metodo viene sottoposto a override e abilita automaticamente il movimento scorrimento rapido-a-delete. L'implementazione del metodo deve chiamare `DeleteRows` nella `UITableView` per fare in modo le celle non vengono più visualizzati e rimuovere anche i dati sottostanti dal modello (ad esempio, una matrice, dizionario o database). 
-   **CanEditRow** – viene eseguito l'override se CommitEditingStyle, si presuppone che tutte le righe siano modificabili. Se questo metodo viene implementato e restituisce false (per alcune righe specifiche o per tutte le righe) quindi il movimento scorrimento rapido-a-delete non sarà disponibile in tale cella. 
-   **TitleForDeleteConfirmation** : facoltativamente, specifica il testo per il **eliminare** pulsante. Se questo metodo non è implementato il testo del pulsante sarà "Elimina". 


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

In questo esempio il `UITableViewSource` è stata aggiornata per usare un `List<TableItem>` (anziché una matrice di stringhe) come origine dati, perché supporta aggiunta ed eliminazione di elementi dalla raccolta.


## <a name="edit-mode"></a>Modalità di modifica

Quando una tabella è in modalità di modifica l'utente visualizza un widget rosso 'stop' in ogni riga, che rivela un pulsante di eliminazione quando modificate. La tabella visualizza anche un'icona di "handle" per indicare che la riga può essere trascinata per modificare l'ordine.
Il **TableEditMode** esempio implementa queste funzionalità, come illustrato.

 [![](editing-images/image11.png "L'esempio TableEditMode implementa queste funzionalità, come illustrato")](editing-images/image11.png#lightbox)

Esistono una serie di metodi diversi in `UITableViewSource` che influiscono sul comportamento in modalità di modifica di una tabella:

-   **CanEditRow** : indica se ogni riga può essere modificato. Restituisce false per impedire a scorrere rapidamente per eliminare sia l'eliminazione nella modalità di modifica. 
-   **CanMoveRow** : restituito true per abilitare il 'handle di spostamento' o false per impedire lo spostamento. 
-   **EditingStyleForRow** : quando la tabella è in modalità di modifica, il valore restituito da questo metodo determina se la cella viene visualizzata l'icona di eliminazione rossa o icona Aggiungi verde. Restituire `UITableViewCellEditingStyle.None` se la riga non deve essere modificabile. 
-   **MoveRow esposto** : chiamato quando una riga viene spostata in modo che la struttura dei dati sottostante può essere modificata per corrispondere ai dati che viene visualizzato nella tabella. 


L'implementazione per i primi tre metodi è relativamente semplice, a meno che non si vuole usare il `indexPath` per modificare il comportamento di righe specifiche, semplicemente impostare come hardcoded i valori restituiti per l'intera tabella.

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

Il `MoveRow` implementazione è leggermente più complessa perché è necessario modificare la struttura di dati sottostante in modo che corrisponda il nuovo ordine. Poiché i dati viene implementati come un `List` il codice seguente elimina l'elemento di dati nella posizione precedente e lo inserisce nella nuova posizione. Se i dati è stati archiviati in una tabella di database SQLite con una colonna 'order' (ad esempio), questo metodo invece necessario eseguire alcune operazioni SQL per riordinare i numeri nella colonna.

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

Infine, per ottenere la tabella in modalità di modifica, la **Edit** pulsante deve chiamare `SetEditing` simile al seguente

```csharp
table.SetEditing (true, true);
```

e quando l'utente ha terminato la modifica, la **Done** pulsante deve disattivare la modalità di modifica:

```csharp
table.SetEditing (false, true);
```


## <a name="row-insertion-editing-style"></a>Stile di modifica di inserimento riga

Inserimento di righe all'interno della tabella è un'interfaccia utente non comune: l'esempio principale nelle App iOS standard è il **contatto di modifica** dello schermo. Questo screenshot Mostra come funziona la funzionalità di inserimento di righe: in modalità di modifica modalità è disponibile un ulteriore riga che (quando si fa clic) consente di inserire righe aggiuntive nei dati. Quando la modifica è stata completata, il file temporaneo **(Aggiungi nuovo)** riga viene rimossa.

 [![](editing-images/image12.png "Quando la modifica è stata completata, il file temporaneo Aggiungi nuova riga viene rimossa")](editing-images/image12.png#lightbox)

Esistono una serie di metodi diversi in `UITableViewSource` che influiscono sul comportamento in modalità di modifica di una tabella. Questi metodi sono stati implementati come indicato di seguito nel codice di esempio:

-   **EditingStyleForRow** : restituisce `UITableViewCellEditingStyle.Delete` per le righe contenenti i dati e restituisce `UITableViewCellEditingStyle.Insert` per l'ultima riga (che verrà aggiunto in modo specifico a comportarsi come un pulsante Inserisci). 
-   **CustomizeMoveTarget** : mentre l'utente sta spostando una cella, il valore restituito da questo metodo facoltativo può eseguire l'override di rivalutazione della scelta del percorso. Ciò significa che è possibile impedire loro di 'eliminazione' la cella in determinate posizioni, ad esempio in questo esempio che impedisce a qualsiasi riga vengano spostati dopo il **(Aggiungi nuovo)** riga. 
-   **CanMoveRow** : restituito true per abilitare il 'handle di spostamento' o false per impedire lo spostamento. Nell'esempio, l'ultima riga presenta il 'handle di spostamento' nascosto poiché è destinato al server come un pulsante Inserisci solo. 


È anche possibile aggiungere due metodi personalizzati per aggiungere la riga 'insert' e quindi di rimuoverlo nuovamente quando non è più necessario. Vengono chiamati dal **Edit** e **eseguita** pulsanti:

-   **WillBeginTableEditing** : quando il **modificare** pulsante è interessate si chiama `SetEditing` da inserire nella tabella in modalità di modifica. Questo modo viene attivato il metodo WillBeginTableEditing in cui viene visualizzato il **(Aggiungi nuovo)** riga alla fine della tabella da usare come pulsante' insert'. 
-   **DidFinishTableEditing** : quando viene manipolato sul pulsante Fine `SetEditing` viene chiamato nuovamente per disattivare la modalità di modifica. L'esempio di codice rimuove il **(Aggiungi nuovo)** riga dalla tabella durante la modifica non è più necessario. 


Questi override dei metodi sono implementati nel file di esempio **TableEditModeAdd/Code/TableSource.cs**:

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

Questi due metodi personalizzati vengono utilizzati per aggiungere e rimuovere i **(Aggiungi nuovo)** riga quando la modalità di modifica della tabella è abilitata o disabilitata:

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

Infine, questo codice crea un'istanza di **Edit** e **eseguita** pulsanti, le espressioni lambda che abilitano o disabilitano la modalità di modifica quando sono interessate:

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

Questo modello di interfaccia utente di inserimento riga non viene usato molto spesso, ma è anche possibile usare la `UITableView.BeginUpdates` e `EndUpdates` metodi animare l'inserimento o la rimozione delle celle di qualsiasi tabella. La regola per l'uso di questi metodi è che la differenza nel valore restituito da `RowsInSection` tra il `BeginUpdates` e `EndUpdates` chiamate devono corrispondere al numero delta di celle aggiunti o eliminati con il `InsertRows` e `DeleteRows` metodi. Se l'origine dati sottostante non è stato modificato in modo da corrispondere gli inserimenti o eliminazioni nella visualizzazione della tabella che si verificherà un errore.


## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithTables (esempio)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
