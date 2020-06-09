---
title: Uso di azioni di riga in Novell. iOS
description: Questa guida illustra come creare azioni di scorrimento personalizzate per le righe della tabella con UISwipeActionsConfiguration o UITableViewRowAction
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: cc83fa65fd040b30d71a30fb703da866c5d0824c
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84570817"
---
# <a name="working-with-row-actions-in-xamarinios"></a>Uso di azioni di riga in Novell. iOS

_Questa guida illustra come creare azioni di scorrimento personalizzate per le righe della tabella con UISwipeActionsConfiguration o UITableViewRowAction_

![Dimostrazione di azioni di scorrimento sulle righe](row-action-images/action02.png)

iOS offre due modi per eseguire azioni su una tabella: `UISwipeActionsConfiguration` e `UITableViewRowAction` .

`UISwipeActionsConfiguration`è stato introdotto in iOS 11 e viene usato per definire un set di azioni che devono essere eseguite quando l'utente scorre _in una delle due direzioni_ in una riga di una vista tabella. Questo comportamento è simile a quello di Native mail. app

La `UITableViewRowAction` classe viene utilizzata per definire un'azione che verrà eseguita quando l'utente scorre orizzontalmente su una riga in una visualizzazione tabella.
Quando, ad esempio, si modifica una tabella, per impostazione predefinita viene visualizzato un pulsante **Elimina** per scorrere a sinistra di una riga. Connettendo più istanze della `UITableViewRowAction` classe a un oggetto `UITableView` , è possibile definire più azioni personalizzate, ciascuna con testo, formattazione e comportamento propri.

## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

Per implementare le azioni di scorrimento rapido con sono necessari tre passaggi `UISwipeActionsConfiguration` :

1. Eseguire l'override `GetLeadingSwipeActionsConfiguration` di metodi e/o `GetTrailingSwipeActionsConfiguration` . Questi metodi restituiscono un oggetto `UISwipeActionsConfiguration` .
2. Creare un'istanza dell'oggetto `UISwipeActionsConfiguration` da restituire. Questa classe accetta una matrice di `UIContextualAction` .
3. Creare un oggetto `UIContextualAction`.

Queste informazioni sono descritte più dettagliatamente nelle sezioni seguenti.

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. implementazione dei metodi SwipeActionsConfigurations

`UITableViewController`(e anche `UITableViewSource` e `UITableViewDelegate` ) contengono due metodi: `GetLeadingSwipeActionsConfiguration` e `GetTrailingSwipeActionsConfiguration` , usati per implementare un set di azioni di scorrimento in una riga di visualizzazione tabella. L'azione di scorrimento iniziali fa riferimento a un swipe dal lato sinistro dello schermo in una lingua da sinistra a destra e dal lato destro dello schermo in una lingua da destra a sinistra.

Nell'esempio seguente, dall'esempio [TableSwipeActions](https://docs.microsoft.com/samples/xamarin/ios-samples/tableswipeactions) , viene illustrata l'implementazione della configurazione swipe principale. Vengono create due azioni dalle azioni contestuali, descritte di [seguito](#create-uicontextualaction). Queste azioni vengono quindi passate a un oggetto appena inizializzato [`UISwipeActionsConfiguration`](#create-uiswipeactionsconfigurations) , che viene usato come valore restituito.

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

<a name="create-uiswipeactionsconfigurations"></a>

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2. creare un'istanza di`UISwipeActionsConfiguration`

Creare un'istanza `UISwipeActionsConfiguration` di usando il `FromActions` metodo per aggiungere una nuova matrice di `UIContextualAction` s, come illustrato nel frammento di codice seguente:

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

È importante tenere presente che l'ordine in cui le azioni vengono visualizzate dipende dalla modalità con cui vengono passate nell'array. Ad esempio, il codice precedente per gli scorrimenti iniziali Visualizza le azioni così come:

![Azioni di scorrimento iniziali visualizzate in una riga di tabella](row-action-images/action03.png)

Per gli scorrimenti finali, le azioni verranno visualizzate come illustrato nell'immagine seguente:

![Azioni di scorrimento finale visualizzate in una riga di tabella](row-action-images/action04.png)

Questo frammento di codice usa anche la nuova `PerformsFirstActionWithFullSwipe` Proprietà. Per impostazione predefinita, questa proprietà è impostata su true, ovvero la prima azione nella matrice viene eseguita quando un utente scorre completamente in una riga. Se si dispone di un'azione che non è distruttiva (ad esempio "Delete", questo potrebbe non essere un comportamento ideale ed è quindi necessario impostarlo su `false` .

<a name="create-uicontextualaction"></a>

### <a name="create-a-uicontextualaction"></a>Creare un `UIContextualAction`

L'azione contestuale è la posizione in cui si crea effettivamente l'azione che verrà visualizzata quando l'utente scorre una riga della tabella.

Per inizializzare un'azione è necessario fornire un oggetto `UIContextualActionStyle` , un titolo e un oggetto `UIContextualActionHandler` . `UIContextualActionHandler`Accetta tre parametri: un'azione, la visualizzazione in cui l'azione è stata visualizzata e un gestore di completamento:

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

È possibile modificare varie proprietà visive, ad esempio il colore di sfondo o l'immagine dell'azione. Il frammento di codice precedente illustra l'aggiunta di un'immagine all'azione e l'impostazione del colore di sfondo su blu.

Una volta create le azioni contestuali, possono usare per inizializzare `UISwipeActionsConfiguration` nel `GetLeadingSwipeActionsConfiguration` metodo.

## <a name="uitableviewrowaction"></a>UITableViewRowAction

Per definire una o più azioni riga personalizzate per un `UITableView` , è necessario creare un'istanza della `UITableViewDelegate` classe ed eseguire l'override del `EditActionsForRow` metodo. Ad esempio:

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

Il `UITableViewRowAction.Create` metodo statico viene usato per creare un nuovo oggetto che visualizzerà `UITableViewRowAction` un pulsante **Hi** quando l'utente scorre orizzontalmente verso una riga della tabella. Successivamente, una nuova istanza di `TableDelegate` viene creata e collegata a `UITableView` . Ad esempio:

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

Quando viene eseguito il codice precedente e l'utente scorre a sinistra in una riga della tabella, viene visualizzato il pulsante **Hi** anziché il pulsante **Elimina** visualizzato per impostazione predefinita:

[![](row-action-images/action01.png "The Hi button being displayed instead of the Delete button")](row-action-images/action01.png#lightbox)

Se l'utente tocca il pulsante **Hi** , viene `Hello World!` scritto nella console di Visual Studio per Mac o Visual Studio quando l'applicazione viene eseguita in modalità di debug.

## <a name="related-links"></a>Collegamenti correlati

- [TableSwipeActions (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/tableswipeactions)
- [WorkingWithTables (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
