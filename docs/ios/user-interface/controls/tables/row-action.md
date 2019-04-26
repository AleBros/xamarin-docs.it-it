---
title: Uso di azioni di riga in xamarin. IOS
description: Questa guida viene illustrato come creare azioni personalizzate scorrere rapidamente per le righe della tabella con UISwipeActionsConfiguration o UITableViewRowAction
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/25/2017
ms.openlocfilehash: 6d41f37d4a63db710bb04e35e6e1a4be0dd4f7a4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408281"
---
# <a name="working-with-row-actions-in-xamarinios"></a>Uso di azioni di riga in xamarin. IOS

_Questa guida viene illustrato come creare azioni personalizzate scorrere rapidamente per le righe della tabella con UISwipeActionsConfiguration o UITableViewRowAction_

![Dimostrazione della azioni scorrere rapidamente su righe](row-action-images/action02.png)

iOS offre due modi per eseguire azioni su una tabella: `UISwipeActionsConfiguration` e `UITableViewRowAction`.

`UISwipeActionsConfiguration` è stato introdotto in iOS 11 e viene usato per definire un set di azioni che devono eseguire posizionare quando tessere magnetiche l'utente _in entrambe le direzioni_ su una riga in una visualizzazione tabella. Questo comportamento è simile a quella del Mail.app nativo 

Il `UITableViewRowAction` classe viene utilizzata per definire un'azione che verrà eseguita quando le tessere magnetiche utente a sinistra in orizzontale su una riga in una visualizzazione tabella.
Ad esempio, quando la modifica di una tabella, scorrendo rapidamente su una riga sinistra visualizza un' **eliminare** pulsante per impostazione predefinita. Mediante l'aggiunta di più istanze del `UITableViewRowAction` classe a un `UITableView`, è possibile definire più azioni personalizzate, ognuno con la propria testo, formattazione e il comportamento.


## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

Esistono un tre passaggi necessari per implementare le azioni di scorrere rapidamente con `UISwipeActionsConfiguration`:

1. Eseguire l'override `GetLeadingSwipeActionsConfiguration` e/o `GetTrailingSwipeActionsConfiguration` metodi. Questi metodi restituiscono un `UISwipeActionsConfiguration`. 
2. Creare un'istanza di `UISwipeActionsConfiguration` da restituire. Questa classe accetta una matrice di `UIContextualAction`.
3. Creare un oggetto `UIContextualAction`.

Questi sono spiegati più dettagliatamente nelle sezioni seguenti.

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. Implementazione dei metodi SwipeActionsConfigurations

`UITableViewController` (e anche `UITableViewSource` e `UITableViewDelegate`) contengono due metodi: `GetLeadingSwipeActionsConfiguration` e `GetTrailingSwipeActionsConfiguration`, che vengono usate per implementare un set di azioni scorrere rapidamente su una riga della visualizzazione tabella. L'azione di scorrimento rapido iniziali fa riferimento a un passaggio del dito dal lato sinistro dello schermo in una lingua da sinistra a destra e dal lato destro dello schermo in una lingua da destra a sinistra. 

Nell'esempio seguente (dal [TableSwipeActions](https://developer.xamarin.com/samples/monotouch/TableSwipeActions) esempio) viene illustrata l'implementazione della configurazione tramite passaggio del dito iniziali. Vengono create due azioni dalle azioni contestuali, illustrati [seguito](#create-uicontextualaction). Queste azioni vengono quindi passate a un appena inizializzato [ `UISwipeActionsConfiguration` ](#create-uiswipeactionsconfigurations), che viene usato come valore restituito.


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

Creare un'istanza di un `UISwipeActionsConfiguration` usando il `FromActions` per aggiungere una nuova matrice di `UIContextualAction`s, come illustrato nel frammento di codice seguente:

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

È importante notare che l'ordine in cui visualizzano le azioni è dipende dal modo in cui vengono passati nell'array. Ad esempio, il codice precedente per tessere magnetiche iniziali vengono visualizzate le azioni così:

![scorrere rapidamente azioni iniziali visualizzato su una riga della tabella](row-action-images/action03.png)

Per i caratteri finali tessere magnetiche, le azioni verranno visualizzate come illustrato nell'immagine seguente:

![scorrere rapidamente azioni finali visualizzati su una riga della tabella](row-action-images/action04.png)

Questo frammento di codice usa anche il nuovo `PerformsFirstActionWithFullSwipe` proprietà. Per impostazione predefinita, questa proprietà è impostata su true, il che significa che la prima azione nella matrice verrà eseguito quando un utente passa completamente in una riga. Se si dispone di un'azione che non è distruttiva (ad esempio "Delete", questo potrebbe non essere comportamento ideale ed è pertanto consigliabile impostarla su `false`.

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>Creare un `UIContextualAction`

È l'azione contestuale in cui si crea effettivamente l'azione che verrà visualizzato quando l'utente passa a una riga della tabella.

Per inizializzare un'azione è necessario specificare una `UIContextualActionStyle`, un titolo e un `UIContextualActionHandler`. Il `UIContextualActionHandler` accetta tre parametri: un'azione e la vista che l'azione è stata visualizzata in un gestore di completamento:

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

Possono essere modificate varie proprietà visive, ad esempio il colore di sfondo o l'immagine dell'azione. Il frammento di codice precedente illustra l'aggiunta di un'immagine per l'azione e impostarne il colore di sfondo su blu.

Dopo avere create le azioni contestuali, possono usare per inizializzare il `UISwipeActionsConfiguration` nella `GetLeadingSwipeActionsConfiguration` (metodo).

## <a name="uitableviewrowaction"></a>UITableViewRowAction

Per definire una o più azioni di riga personalizzato per un `UITableView`, sarà necessario creare un'istanza del `UITableViewDelegate` classe ed eseguire l'override di `EditActionsForRow` (metodo). Ad esempio:

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

Il metodo statico `UITableViewRowAction.Create` metodo viene utilizzato per creare una nuova `UITableViewRowAction` che verrà visualizzato un **Hi** dopo che le tessere magnetiche utente a sinistra in orizzontale su una riga nella tabella. In un secondo momento una nuova istanza della `TableDelegate` viene creato e collegato al `UITableView`. Ad esempio:

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

Quando viene eseguito il codice riportato sopra e a sinistra di tessere magnetiche utente su una riga della tabella, il **Hi** pulsante verrà visualizzato invece il **eliminare** pulsante visualizzato per impostazione predefinita:

[![](row-action-images/action01.png "Il pulsante di Hi visualizzato invece il pulsante di eliminazione")](row-action-images/action01.png#lightbox)

Se l'utente tocca il **Hi** pulsante `Hello World!` verrà scritto nella console in Visual Studio per Mac o Visual Studio quando l'applicazione viene eseguita in modalità di debug.



## <a name="related-links"></a>Collegamenti correlati

- [TableSwipeActions (esempio)](https://developer.xamarin.com/samples/monotouch/TableSwipeActions)
- [WorkingWithTables (esempio)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
