---
title: Utilizzo di azioni di riga
description: Questa guida viene illustrato come creare azioni scorrere personalizzata per le righe della tabella con UISwipeActionsConfiguration o UITableViewRowAction
ms.topic: article
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/25/2017
ms.openlocfilehash: e9d3e2eecd4c03e7b3046e1ad86dd8a0d70a7f73
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-row-actions"></a>Utilizzo di azioni di riga

_Questa guida viene illustrato come creare azioni scorrere personalizzata per le righe della tabella con UISwipeActionsConfiguration o UITableViewRowAction_

![Dimostrazione azioni scorrere rapidamente su righe](row-action-images/action02.png)

iOS fornisce due modi per eseguire azioni su una tabella: `UISwipeActionsConfiguration` e `UITableViewRowAction`.

`UISwipeActionsConfiguration` è stata introdotta in iOS 11 e viene utilizzato per definire un set di azioni da eseguire inserire quando le tessere magnetiche utente _in entrambe le direzioni_ su una riga in una visualizzazione tabella. Questo comportamento è simile a quello del Mail.app nativo 

La `UITableViewRowAction` classe viene utilizzata per definire un'azione che verrà eseguita quando le tessere magnetiche utente lasciato orizzontalmente su una riga in una visualizzazione tabella.
Ad esempio, quando la modifica di una tabella, passaggio di sinistra in una riga viene visualizzato un **eliminare** per impostazione predefinita. Mediante l'aggiunta di più istanze del `UITableViewRowAction` classe per un `UITableView`, è possono definire più azioni personalizzate, ognuno con un testo, formattazione e comportamento.


## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

Sono disponibili un tre passaggi necessari per implementare azioni scorrere con `UISwipeActionsConfiguration`:

1. Eseguire l'override `GetLeadingSwipeActionsConfiguration` e/o `GetTrailingSwipeActionsConfiguration` metodi. Questi metodi restituiscono un `UISwipeActionsConfiguration`. 
2. Creare un'istanza di `UISwipeActionsConfiguration` da restituire. Questa classe accetta una matrice di `UIContextualAction`.
3. Creare un oggetto `UIContextualAction`.

Questi sono spiegati in dettaglio nelle sezioni seguenti.

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. Implementazione dei metodi di SwipeActionsConfigurations

`UITableViewController` (e anche `UITableViewSource` e `UITableViewDelegate`) sono contenuti due metodi: `GetLeadingSwipeActionsConfiguration` e `GetTrailingSwipeActionsConfiguration`, che vengono utilizzate per implementare un set di azioni scorrere rapidamente su una riga di vista della tabella. L'azione di spostamento iniziale fa riferimento a un scorrere rapidamente dal lato sinistro dello schermo in una lingua da sinistra a destra e verso il lato destro dello schermo in una lingua da destra a sinistra. 

Nell'esempio seguente (dal [TableSwipeActions](https://developer.xamarin.com/samples/monotouch/TableSwipeActions) esempio) viene illustrata l'implementazione della configurazione scorrere iniziali. Vengono create due azioni dalle azioni contestuale, che vengono descritte [sotto](#create-uicontextualaction). Queste azioni vengono quindi passate a un oggetto appena inizializzato [ `UISwipeActionsConfiguration` ](#create-uiswipeactionsconfigurations), che viene utilizzato come valore restituito.


```csharp
public override UISwipeActionsConfiguration GetLeadingSwipeActionsConfiguration(UITableView tableView, NSIndexPath indexPath)
{
    //UIContextualActions
    var definitionAction = ContextualDefinitionAction(indexPath.Row);
    var flagAction = ContextualFlagAction(indexPath.Row);

    //UISwipeActionsConfiguration
    var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction });
    
    leadingSwipe.PerformsFirstActionWithFullSwipe = false;
    
    return leadingSwipe;
}  
```

<a name="create-uiswipeactionsconfigurations" />

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2. Creare un'istanza di un `UISwipeActionsConfiguration`

Creare un'istanza di un `UISwipeActionsConfiguration` utilizzando il `FromActions` metodo per aggiungere una nuova matrice di `UIContextualAction`s, come illustrato nel frammento di codice seguente:

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

È importante notare che l'ordine in cui visualizzano le azioni dipende dal modo in cui sono passati nella matrice. Ad esempio, il codice precedente per tessere magnetiche iniziali vengono visualizzate le azioni così:

![scorrere rapidamente azioni iniziali visualizzati su una riga di tabella](row-action-images/action03.png)

Per i caratteri finali tessere magnetiche, le azioni verranno visualizzate come illustrato nella figura seguente:

![scorrere rapidamente azioni finali visualizzato su una riga di tabella](row-action-images/action04.png)

Questo frammento di codice si avvale anche della nuova `PerformsFirstActionWithFullSwipe` proprietà. Per impostazione predefinita, questa proprietà è impostata su true, vale a dire che la prima azione nella matrice si verificherà quando un utente passa completamente in una riga. Se si dispone di un'azione che non distruttiva (ad esempio "Delete", questo potrebbe non essere comportamento ideale e deve pertanto essere impostata su `false`.

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>Creare un `UIContextualAction`

L'azione contestuale è punto si creerà l'azione che verrà visualizzato quando l'utente passa a una riga della tabella.

Per inizializzare un'azione è necessario fornire un `UIContextualActionStyle`, un titolo e un `UIContextualActionHandler`. Il `UIContextualActionHandler` accetta tre parametri: un'azione e la visualizzazione che l'azione è stata visualizzata in un gestore di completamento:

```csharp
public UIContextualAction ContextualFlagAction(int row)
{
    var action = UIContextualAction.FromContextualActionStyle
                    (UIContextualActionStyle.Normal,
                        "Flag",
                        (FlagAction, view, success) => {
                            var alertController = UIAlertController.Create($"Report {words[row]}?", "", UIAlertControllerStyle.Alert);
                            alertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, null)); 
                            alertController.AddAction(UIAlertAction.Create("Yes", UIAlertActionStyle.Destructive, null));
                            PresentViewController(alertController, true, null);
                            
                            success(true);
                        });

    action.Image = UIImage.FromFile("feedback.png");
    action.BackgroundColor = UIColor.Blue;

    return action;
}
```

Varie proprietà visive, ad esempio il colore di sfondo o l'immagine dell'azione può essere modificata. Il frammento di codice sopra riportato di seguito viene illustrato come aggiungere un'immagine per l'azione e impostare il colore di sfondo su blu.

Dopo avere create le azioni di scelta rapida, è possibile utilizzare per inizializzare il `UISwipeActionsConfiguration` nel `GetLeadingSwipeActionsConfiguration` metodo.

## <a name="uitableviewrowaction"></a>UITableViewRowAction

Per definire una o più azioni di riga personalizzato per un `UITableView`, è necessario creare un'istanza del `UITableViewDelegate` classe ed eseguire l'override di `EditActionsForRow` metodo. Ad esempio:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using Foundation;
using UIKit;

namespace BasicTable
{
    public class TableDelegate : UITableViewDelegate
    {
        #region Constructors
        public TableDelegate ()
        {
        }

        public TableDelegate (IntPtr handle) : base (handle)
        {
        }

        public TableDelegate (NSObjectFlag t) : base (t)
        {
        }

        #endregion

        #region Override Methods
        public override UITableViewRowAction[] EditActionsForRow (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewRowAction hiButton = UITableViewRowAction.Create (
                UITableViewRowActionStyle.Default,
                "Hi",
                delegate {
                    Console.WriteLine ("Hello World!");
                });
            return new UITableViewRowAction[] { hiButton };
        }
        #endregion
    }
}
```

Il metodo statico `UITableViewRowAction.Create` metodo viene utilizzato per creare un nuovo `UITableViewRowAction` che verrà visualizzato un **Hi** pulsante quando le tessere magnetiche utente rimangono orizzontalmente su una riga nella tabella. In un secondo momento una nuova istanza di `TableDelegate` viene creato e collegato al `UITableView`. Ad esempio:

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

Quando si esegue il codice sopra riportato e a sinistra di tessere magnetiche utente su una riga di tabella, la **Hi** pulsante verrà visualizzato al posto di **eliminare** pulsante visualizzato per impostazione predefinita:

[ ![](row-action-images/action01.png "Pulsante Hi visualizzato invece il pulsante Elimina")](row-action-images/action01.png)

Se l'utente tocca il **Hi** pulsante `Hello World!` verrà scritto nella console in Visual Studio per Mac o Visual Studio quando l'applicazione viene eseguita in modalità di debug.



## <a name="related-links"></a>Collegamenti correlati

- [TableSwipeActions (sample)](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)
- [WorkingWithTables (esempio)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
