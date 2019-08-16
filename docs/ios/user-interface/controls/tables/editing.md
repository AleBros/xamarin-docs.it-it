---
title: Modifica di tabelle con Novell. iOS
description: Questo documento descrive come modificare le tabelle in Novell. iOS. Viene illustrato il scorrimento per eliminare, modificare la modalità e l'inserimento di righe.
ms.prod: xamarin
ms.assetid: EC197F25-E865-AFA3-E5CF-B33FAB7744A0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: f82057957e76ee683e2a649fdf6c2350bf282c18
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528644"
---
# <a name="editing-tables-with-xamarinios"></a>Modifica di tabelle con Novell. iOS

Le funzionalità di modifica della tabella sono abilitate eseguendo l' `UITableViewSource` override dei metodi in una sottoclasse. Il comportamento di modifica più semplice è il gesto di scorrimento da eliminare che può essere implementato con un solo override del metodo.
La modifica più complessa (incluse le righe di trasferimento) può essere eseguita con la tabella in modalità di modifica.

## <a name="swipe-to-delete"></a>Scorrere rapidamente per eliminare

La funzionalità di scorrimento da eliminare è un gesto naturale in iOS previsto dagli utenti. 

 [![](editing-images/image10.png "Esempio di swipe da eliminare")](editing-images/image10.png#lightbox)

Sono disponibili tre override dei metodi che influiscono sul gesto di scorrimento rapido per visualizzare un pulsante **Elimina** in una cella:

- **CommitEditingStyle** : l'origine della tabella rileva se questo metodo viene sottoposto a override e Abilita automaticamente il gesto di scorrimento rapido. L'implementazione del metodo deve chiamare `DeleteRows` `UITableView` su per far scomparire le celle e rimuovere anche i dati sottostanti dal modello, ad esempio una matrice, un dizionario o un database. 
- **CanEditRow** : se viene eseguito l'override di CommitEditingStyle, si presuppone che tutte le righe siano modificabili. Se questo metodo viene implementato e restituisce false (per alcune righe specifiche o per tutte le righe), il movimento di scorrimento rapido non sarà disponibile in tale cella. 
- **TitleForDeleteConfirmation** : consente di specificare il testo per il pulsante **Elimina** . Se questo metodo non viene implementato, il testo del pulsante sarà "Delete". 


Questi metodi sono implementati nella `TableSource` classe seguente:

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

Per questo esempio `UITableViewSource` è stato aggiornato per usare un oggetto `List<TableItem>` (anziché una matrice di stringhe) come origine dati perché supporta l'aggiunta e l'eliminazione di elementi dalla raccolta.


## <a name="edit-mode"></a>Modalità di modifica

Quando una tabella è in modalità di modifica, l'utente visualizza un widget "Stop" rosso in ogni riga, che rivela un pulsante Elimina quando viene toccato. La tabella Visualizza anche un'icona "handle" per indicare che la riga può essere trascinata per modificare l'ordine.
L'esempio **TableEditMode** implementa queste funzionalità come illustrato.

 [![](editing-images/image11.png "L'esempio TableEditMode implementa queste funzionalità come illustrato")](editing-images/image11.png#lightbox)

Sono disponibili diversi metodi che influiscono sul `UITableViewSource` comportamento della modalità di modifica di una tabella:

- **CanEditRow** : indica se è possibile modificare ogni riga. Restituisce false per impedire l'esecuzione di scorrimenti a eliminazione ed eliminazione in modalità di modifica. 
- **CanMoveRow** : restituisce true per abilitare lo spostamento ' handle ' o false per impedire lo spostamento. 
- **EditingStyleForRow** : quando la tabella è in modalità di modifica, il valore restituito da questo metodo determina se la cella Visualizza l'icona di eliminazione rossa o l'icona Aggiungi verde. Restituisce `UITableViewCellEditingStyle.None` se la riga non deve essere modificabile. 
- **MoveRow** : viene chiamato quando viene spostata una riga in modo che sia possibile modificare la struttura dei dati sottostante in modo che corrisponda ai dati visualizzati nella tabella. 


L'implementazione per i primi tre metodi è relativamente semplice, a meno che non si desideri usare `indexPath` per modificare il comportamento di righe specifiche, ma è sufficiente impostare come hardcoded i valori restituiti per l'intera tabella.

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

L' `MoveRow` implementazione è un po' più complicata perché è necessario modificare la struttura dei dati sottostante in modo che corrisponda al nuovo ordine. Poiché i dati vengono implementati come `List` il codice seguente elimina l'elemento dati nella posizione precedente e lo inserisce nella nuova posizione. Se i dati sono stati archiviati in una tabella di database SQLite con una colonna ' Order ' (ad esempio), questo metodo avrebbe dovuto eseguire alcune operazioni SQL per riordinare i numeri in tale colonna.

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

Infine, per ottenere la tabella in modalità di modifica, il pulsante **modifica** deve chiamare `SetEditing` come segue

```csharp
table.SetEditing (true, true);
```

Quando l'utente ha terminato la modifica, il pulsante **fine** dovrebbe disattivare la modalità di modifica:

```csharp
table.SetEditing (false, true);
```


## <a name="row-insertion-editing-style"></a>Stile di modifica inserimento righe

L'inserimento di righe dall'interno della tabella è un'interfaccia utente non comune. l'esempio principale nelle app iOS standard è la schermata **Modifica contatto** . In questa schermata viene illustrato il funzionamento della funzionalità di inserimento delle righe. in modalità di modifica è presente una riga aggiuntiva che, quando viene selezionato, inserisce righe aggiuntive nei dati. Al termine della modifica, viene rimossa la riga temporanea **(Aggiungi nuovo)** .

 [![](editing-images/image12.png "Al termine della modifica, viene rimossa la nuova riga aggiunta temporanea")](editing-images/image12.png#lightbox)

Sono disponibili diversi metodi che influiscono sul `UITableViewSource` comportamento della modalità di modifica di una tabella. Questi metodi sono stati implementati come indicato di seguito nel codice di esempio:

- **EditingStyleForRow** : restituisce `UITableViewCellEditingStyle.Delete` per le righe contenenti dati e restituisce `UITableViewCellEditingStyle.Insert` per l'ultima riga, che verrà aggiunta in modo specifico per comportarsi come pulsante di inserimento. 
- **CustomizeMoveTarget** -mentre l'utente sta muovendo una cella, il valore restituito da questo metodo facoltativo può ignorare la scelta della posizione. Ciò significa che è possibile impedire che venga eliminato la cella in determinate posizioni, ad esempio questo esempio che impedisce lo spostamento di una riga dopo la riga **(Aggiungi nuovo)** . 
- **CanMoveRow** : restituisce true per abilitare lo spostamento ' handle ' o false per impedire lo spostamento. Nell'esempio, l'ultima riga ha lo spostamento ' handle ' nascosto perché è destinato al server come solo pulsante di inserimento. 


Vengono inoltre aggiunti due metodi personalizzati per aggiungere la riga "Insert" e quindi rimuoverla nuovamente quando non è più necessario. Vengono chiamati dai pulsanti **modifica** e **fine** :

- **WillBeginTableEditing** : quando viene toccato il pulsante **modifica** , chiama `SetEditing` per inserire la tabella in modalità di modifica. Viene attivato il metodo WillBeginTableEditing in cui viene visualizzata la riga **(Aggiungi nuovo)** alla fine della tabella per fungere da pulsante di inserimento. 
- **DidFinishTableEditing** : quando viene toccato `SetEditing` il pulsante Done, viene chiamato di nuovo per disattivare la modalità di modifica. Il codice di esempio rimuove la riga **(Aggiungi nuovo)** dalla tabella quando la modifica non è più necessaria. 


Queste sostituzioni dei metodi sono implementate nel file di esempio **TableEditModeAdd/code/TableSource. cs**:

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

Questi due metodi personalizzati vengono utilizzati per aggiungere e rimuovere la riga **(Aggiungi nuovo)** quando la modalità di modifica della tabella è abilitata o disabilitata:

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

Infine, questo codice crea un'istanza dei pulsanti **modifica** e **fine** , con espressioni lambda che abilitano o disabilitano la modalità di modifica quando vengono modificate:

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

Questo modello di interfaccia utente per l'inserimento di righe non viene usato molto spesso, ma `UITableView.BeginUpdates` è `EndUpdates` anche possibile usare i metodi e per animare l'inserimento o la rimozione di celle in qualsiasi tabella. La regola per l'utilizzo di questi metodi è che la differenza nel valore `RowsInSection` restituito da `BeginUpdates` tra `EndUpdates` le chiamate e deve corrispondere al numero netto di celle aggiunte/eliminate `DeleteRows` con i `InsertRows` metodi e. Se l'origine dati sottostante non viene modificata in modo che corrisponda alle operazioni di inserimento/eliminazione nella visualizzazione tabella si verificherà un errore.


## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithTables (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
