---
title: La visualizzazione degli avvisi in xamarin. IOS
description: Questo documento descrive come visualizzare gli avvisi in xamarin. IOS utilizzando UIAlertController API introdotte in iOS 8.
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 88233cb1ac31b2669fdc38bbc9b0835a45c6b0ce
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789595"
---
# <a name="displaying-alerts-in-xamarinios"></a>La visualizzazione degli avvisi in xamarin. IOS

A partire da iOS 8, UIAlertController ha completato UIActionSheet sostituito e UIAlertView entrambi che ora sono deprecate.

A differenza delle classi che sostituisce, che sono sottoclassi di UIView, UIAlertController è una sottoclasse di UIViewController.

Utilizzare `UIAlertControllerStyle` per indicare il tipo di avviso da visualizzare. Questi tipi di avvisi sono:

- **UIAlertControllerStyleActionSheet**
    * Pre-iOS 8 ciò avrebbe un UIActionSheet
- **UIAlertControllerStyleAlert**
    * Pre-iOS 8 ciò avrebbe UIAlertView 

Esistono tre passaggi necessari per rendere durante la creazione di un Controller di avviso:

- Creare e configurare l'avviso con r:
    * title
    * messaggio
    * preferredStyle
    
- (Facoltativo) Aggiungere un campo di testo
- Aggiungere le azioni necessarie
- Presentare il Controller di visualizzazione

L'avviso più semplice contiene un solo pulsante, come illustrato in questo screenshot:

 ![Avviso con un pulsante](alerts-images/alert1.png)

Il codice per visualizzare un avviso semplice è come segue:

```csharp
okayButton.TouchUpInside += (sender, e) => {

    //Create Alert
    var okAlertController = UIAlertController.Create ("Title", "The message", UIAlertControllerStyle.Alert);

    //Add Action
    okAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));

    // Present Alert
    PresentViewController (okAlertController, true, null);
};
```

Visualizzazione di un avviso con più opzioni, viene eseguita in modo simile, ma aggiungere due azioni. Ad esempio, nella schermata seguente mostra un avviso con due pulsanti:

 ![ Avviso con due pulsanti](alerts-images/alert2.png)

```csharp
okayCancelButton.TouchUpInside += ((sender, e) => {

    //Create Alert
    var okCancelAlertController = UIAlertController.Create("Alert Title", "Choose from two buttons", UIAlertControllerStyle.Alert);

    //Add Actions
    okCancelAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, alert => Console.WriteLine ("Okay was clicked")));
    okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine ("Cancel was clicked")));

    //Present Alert
    PresentViewController(okCancelAlertController, true, null);
});
```

Gli avvisi è possono visualizzare anche un foglio di azione, simile alla seguente:

 ![Avviso di foglio di azione](alerts-images/alert3.png)

Vengono aggiunti i pulsanti per l'avviso con il `AddAction` metodo:

```csharp
actionSheetButton.TouchUpInside += ((sender, e) => {

    // Create a new Alert Controller
    UIAlertController actionSheetAlert = UIAlertController.Create("Action Sheet", "Select an item from below", UIAlertControllerStyle.ActionSheet);

    // Add Actions
    actionSheetAlert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item One pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("custom button 1",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Two pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Three pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel, (action) => Console.WriteLine ("Cancel button pressed.")));

    // Required for iPad - You must specify a source for the Action Sheet since it is
    // displayed as a popover
    UIPopoverPresentationController presentationPopover = actionSheetAlert.PopoverPresentationController;
    if (presentationPopover!=null) {
        presentationPopover.SourceView = this.View;
        presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Up;
    }

    // Display the alert
    this.PresentViewController(actionSheetAlert,true,null);
});
```

## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://developer.xamarin.com/samples/Controls/)
- [Avvisi Controller](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/)
