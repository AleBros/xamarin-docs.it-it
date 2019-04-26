---
title: La visualizzazione degli avvisi in xamarin. IOS
description: Questo documento descrive come visualizzare gli avvisi in xamarin. IOS tramite l'API introdotte in iOS 8 UIAlertController.
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 6071381daa7eedf4fa4b076ea60f2748865cf002
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896221"
---
# <a name="displaying-alerts-in-xamarinios"></a>La visualizzazione degli avvisi in xamarin. IOS

A partire da iOS 8, UIAlertController ha completato UIActionSheet sostituito e UIAlertView entrambe di cui sono stati deprecati.

A differenza delle classi che sostituisce, che rappresentano le sottoclassi di UIView, UIAlertController è una sottoclasse di UIViewController.

Usare `UIAlertControllerStyle` per indicare il tipo di avviso da visualizzare. Questi tipi di avvisi sono:

- **UIAlertControllerStyleActionSheet**
    * Pre-iOS 8 questo sarebbe stata una UIActionSheet
- **UIAlertControllerStyleAlert**
    * Pre-iOS 8 questo sarebbe stato UIAlertView 

Esistono tre passaggi necessari per prendere durante la creazione di un Controller di avvisi:

- Creare e configurare l'avviso gli elementi seguenti:
    * title
    * messaggio
    * preferredStyle
    
- (Facoltativo) Aggiungere un campo di testo
- Aggiungere le azioni necessarie
- Presentare il Controller di visualizzazione

L'avviso più semplice contiene un singolo pulsante, come illustrato in questo screenshot:

 ![Avviso con un solo pulsante](alerts-images/alert1.png)

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

La visualizzazione di un avviso con più opzioni, viene eseguito in modo simile, ma aggiungere due azioni. Ad esempio, lo screenshot seguente mostra un avviso con due pulsanti:

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

Gli avvisi possono visualizzare anche un foglio di azione, simile allo screenshot seguente:

 ![Avviso di foglio di azione](alerts-images/alert3.png)

Vengono aggiunti i pulsanti per l'avviso, usare il `AddAction` metodo:

```csharp
actionSheetButton.TouchUpInside += ((sender, e) => {

    // Create a new Alert Controller
    UIAlertController actionSheetAlert = UIAlertController.Create("Action Sheet", "Select an item from below", UIAlertControllerStyle.ActionSheet);

    // Add Actions
    actionSheetAlert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item One pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("custom button 1",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Two pressed.")));

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
- [Controller di avvisi](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
