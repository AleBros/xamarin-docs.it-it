---
title: Uso di azioni di riga in Xamarin.iOS
description: Questa guida illustra come creare azioni di scorrimento personalizzate per le righe della tabella con UISwipeActionsConfiguration o UITableViewRowAction
ms.prod: xamarin
ms.assetid: 340FB633-0C46-40AA-9963-FF17D7CA6858
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 542ae6696bae8fccfa6d5ed9842bce126760da37
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021860"
---
# <a name="working-with-row-actions-in-xamarinios"></a>Uso di azioni di riga in Xamarin.iOS

_Questa guida illustra come creare azioni di scorrimento personalizzate per le righe della tabella con UISwipeActionsConfiguration o UITableViewRowAction_

![Dimostrazione di azioni di scorrimento sulle righe](row-action-images/action02.png)

iOS offre due modi per eseguire azioni su una tabella: `UISwipeActionsConfiguration` e `UITableViewRowAction`.

`UISwipeActionsConfiguration` è stato introdotto in iOS 11 e viene usato per definire un set di azioni che devono essere eseguite quando l'utente scorre _in una delle due direzioni_ su una riga in una visualizzazione tabella. Questo comportamento è simile a quello di Native mail. app

La classe `UITableViewRowAction` viene utilizzata per definire un'azione che verrà eseguita quando l'utente scorre orizzontalmente su una riga in una visualizzazione tabella.
Quando, ad esempio, si modifica una tabella, per impostazione predefinita viene visualizzato un pulsante **Elimina** per scorrere a sinistra di una riga. Se si allineano più istanze della classe `UITableViewRowAction` a una `UITableView`, è possibile definire più azioni personalizzate, ciascuna con testo, formattazione e comportamento.

## <a name="uiswipeactionsconfiguration"></a>UISwipeActionsConfiguration

Per implementare le azioni di scorrimento rapido con `UISwipeActionsConfiguration`sono necessari tre passaggi:

1. Eseguire l'override di metodi `GetLeadingSwipeActionsConfiguration` e/o `GetTrailingSwipeActionsConfiguration`. Questi metodi restituiscono un `UISwipeActionsConfiguration`.
2. Creare un'istanza della `UISwipeActionsConfiguration` da restituire. Questa classe accetta una matrice di `UIContextualAction`.
3. Creare un oggetto `UIContextualAction`.

Queste informazioni sono descritte più dettagliatamente nelle sezioni seguenti.

### <a name="1-implementing-the-swipeactionsconfigurations-methods"></a>1. implementazione dei metodi SwipeActionsConfigurations

`UITableViewController` (e anche `UITableViewSource` e `UITableViewDelegate`) contengono due metodi: `GetLeadingSwipeActionsConfiguration` e `GetTrailingSwipeActionsConfiguration`, usati per implementare un set di azioni di scorrimento in una riga di visualizzazione tabella. L'azione di scorrimento iniziali fa riferimento a un swipe dal lato sinistro dello schermo in una lingua da sinistra a destra e dal lato destro dello schermo in una lingua da destra a sinistra.

Nell'esempio seguente, dall'esempio [TableSwipeActions](https://docs.microsoft.com/samples/xamarin/ios-samples/tableswipeactions) , viene illustrata l'implementazione della configurazione swipe principale. Vengono create due azioni dalle azioni contestuali, descritte di [seguito](#create-uicontextualaction). Queste azioni vengono quindi passate a un [`UISwipeActionsConfiguration`](#create-uiswipeactionsconfigurations)appena inizializzato, che viene usato come valore restituito.

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

### <a name="2-instantiate-a-uiswipeactionsconfiguration"></a>2. creare un'istanza di un `UISwipeActionsConfiguration`

Creare un'istanza di un `UISwipeActionsConfiguration` usando il metodo `FromActions` per aggiungere una nuova matrice di `UIContextualAction`s, come illustrato nel frammento di codice seguente:

```csharp
var leadingSwipe = UISwipeActionsConfiguration.FromActions(new UIContextualAction[] { flagAction, definitionAction })

leadingSwipe.PerformsFirstActionWithFullSwipe = false;
```

È importante sottolineare che l'ordine in cui vengono visualizzate le azioni dipende dal modo in cui vengono passate nell'array. Ad esempio, il codice precedente per gli scorrimenti iniziali Visualizza le azioni così come:

![Azioni di scorrimento iniziali visualizzate in una riga di tabella](row-action-images/action03.png)

Per gli scorrimenti finali, le azioni verranno visualizzate come illustrato nell'immagine seguente:

![Azioni di scorrimento finale visualizzate in una riga di tabella](row-action-images/action04.png)

Questo frammento di codice usa anche la nuova proprietà `PerformsFirstActionWithFullSwipe`. Per impostazione predefinita, questa proprietà è impostata su true, ovvero la prima azione nella matrice viene eseguita quando un utente scorre completamente in una riga. Se si dispone di un'azione che non è distruttiva (ad esempio "Delete", questo potrebbe non essere un comportamento ideale ed è quindi necessario impostarlo su `false`.

<a name="create-uicontextualaction" />

### <a name="create-a-uicontextualaction"></a>Creare una `UIContextualAction`

L'azione contestuale è la posizione in cui si crea effettivamente l'azione che verrà visualizzata quando l'utente scorre una riga della tabella.

Per inizializzare un'azione è necessario fornire una `UIContextualActionStyle`, un titolo e una `UIContextualActionHandler`. Il `UIContextualActionHandler` accetta tre parametri: un'azione, la visualizzazione in cui l'azione è stata visualizzata e un gestore di completamento:

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

Una volta create le azioni contestuali, è possibile usare per inizializzare il `UISwipeActionsConfiguration` nel metodo `GetLeadingSwipeActionsConfiguration`.

## <a name="uitableviewrowaction"></a>UITableViewRowAction

Per definire una o più azioni di riga personalizzate per un `UITableView`, sarà necessario creare un'istanza della classe `UITableViewDelegate` ed eseguire l'override del metodo `EditActionsForRow`. Esempio:

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

Il metodo statico `UITableViewRowAction.Create` viene usato per creare un nuovo `UITableViewRowAction` che visualizzerà un **pulsante di** uscita rapido quando l'utente scorre orizzontalmente in una riga della tabella. Successivamente, una nuova istanza del `TableDelegate` viene creata e collegata al `UITableView`. Esempio:

```csharp
TableDelegate tableDelegate;
...

// Replace the standard delete button with a "Hi" button
tableDelegate = new TableDelegate ();
table.Delegate = tableDelegate;

```

Quando viene eseguito il codice precedente e l'utente scorre a sinistra in una riga della tabella, viene visualizzato il pulsante **Hi** anziché il pulsante **Elimina** visualizzato per impostazione predefinita:

[![](row-action-images/action01.png "The Hi button being displayed instead of the Delete button")](row-action-images/action01.png#lightbox)

Se l'utente tocca il pulsante **Hi** , `Hello World!` verrà scritto nella console di Visual Studio per Mac o Visual Studio quando l'applicazione viene eseguita in modalità di debug.

## <a name="related-links"></a>Collegamenti correlati

- [TableSwipeActions (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/tableswipeactions)
- [WorkingWithTables (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
