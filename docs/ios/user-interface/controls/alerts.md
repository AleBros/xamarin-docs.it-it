---
title: Visualizzazione di avvisi in Novell. iOS
description: Questo documento descrive come visualizzare gli avvisi in Novell. iOS usando le API UIAlertController introdotte in iOS 8.
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: ec2a35a33682ec7bda2840cfb4bc6b4908fc65b0
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292378"
---
# <a name="displaying-alerts-in-xamarinios"></a>Visualizzazione di avvisi in Novell. iOS

A partire da iOS 8, UIAlertController ha completato la sostituzione di UIActionSheet e UIAlertView che ora sono deprecate.

Diversamente dalle classi che ha sostituito, ovvero sottoclassi di UIView, UIAlertController è una sottoclasse di UIViewController.

Consente `UIAlertControllerStyle` di indicare il tipo di avviso da visualizzare. Questi tipi di avvisi sono:

- **UIAlertControllerStyleActionSheet**
  - Pre-iOS 8 questa sarebbe stata una UIActionSheet
- **UIAlertControllerStyleAlert**
  - Pre-iOS 8 questo sarebbe stato UIAlertView 

Quando si crea un controller di avviso, è necessario eseguire tre passaggi:

- Creare e configurare l'avviso con un:
  - title
  - message
  - preferredStyle

- Opzionale Aggiungere un campo di testo
- Aggiungere le azioni necessarie
- Presentare il controller di visualizzazione

L'avviso più semplice contiene un solo pulsante, come illustrato in questo screenshot:

 ![Avviso con un pulsante](alerts-images/alert1.png)

Il codice per la visualizzazione di un avviso semplice è il seguente:

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

La visualizzazione di un avviso con più opzioni viene eseguita in modo analogo, ma aggiunge due azioni. Ad esempio, la schermata seguente mostra un avviso con due pulsanti:

 ![Avviso con due pulsanti](alerts-images/alert2.png)

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

Gli avvisi possono anche visualizzare un foglio di azione, simile allo screenshot seguente:

 ![Avviso del foglio di azione](alerts-images/alert3.png)

I pulsanti vengono aggiunti all'avviso con il `AddAction` metodo:

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

- [Controlli (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [Controller di avviso](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
