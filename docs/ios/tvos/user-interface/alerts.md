---
title: Uso degli avvisi tvOS in Novell
description: Questo documento descrive come usare gli avvisi di tvOS in Novell. Viene illustrata la visualizzazione di un avviso, l'aggiunta di campi di testo e una classe helper.
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 2578272dcd38399f23f2aac67503ea4e1b09a027
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769070"
---
# <a name="working-with-tvos-alerts-in-xamarin"></a>Uso degli avvisi tvOS in Novell

_Questo articolo illustra l'uso di UIAlertController per visualizzare un messaggio di avviso all'utente in Novell. tvOS._

Se è necessario ottenere l'attenzione dell'utente tvOS o richiedere l'autorizzazione per eseguire un'azione distruttiva, ad esempio l' `UIAlertViewController`eliminazione di un file, è possibile presentare un messaggio di avviso utilizzando:

[![](alerts-images/alert01.png "UIAlertViewController di esempio")](alerts-images/alert01.png#lightbox)

Se oltre a visualizzare un messaggio, è possibile aggiungere pulsanti e campi di testo a un avviso per consentire all'utente di rispondere alle azioni e fornire commenti e suggerimenti.

<a name="About-Alerts" />

## <a name="about-alerts"></a>Informazioni sugli avvisi

Come indicato in precedenza, gli avvisi vengono usati per attirare l'attenzione dell'utente e informare lo stato dell'app o richiedere commenti e suggerimenti. Gli avvisi devono presentare un titolo, possono facoltativamente avere un messaggio e uno o più pulsanti o campi di testo.

[![](alerts-images/alert04.png "Un avviso di esempio")](alerts-images/alert04.png#lightbox)

Apple presenta i suggerimenti seguenti per l'utilizzo degli avvisi:

- **Usare gli avvisi sporadicamente** : gli avvisi interrompono il flusso dell'utente con l'app e interrompono l'esperienza utente e, di conseguenza, devono essere usati solo per situazioni importanti come le notifiche degli errori, gli acquisti in-app e le azioni distruttive.
- **Fornisce opzioni utili** : se l'avviso presenta opzioni all'utente, è necessario assicurarsi che ogni opzione offra informazioni critiche e fornisca azioni utili da parte dell'utente.

<a name="Alert-Titles-and-Messages" />

### <a name="alert-titles-and-messages"></a>Titoli e messaggi di avviso

Apple presenta i suggerimenti seguenti per presentare il titolo di un avviso e il messaggio facoltativo:

- **Usare i titoli** con più parole: il titolo di un avviso dovrebbe ottenere il punto della situazione in modo chiaro, rimanendo comunque semplice. Una singola parola titolo fornisce raramente informazioni sufficienti.
- **Usare titoli descrittivi che non richiedono un messaggio** : laddove possibile, provare a rendere il titolo dell'avviso sufficientemente descrittivo che il testo del messaggio facoltativo non sia necessario.
- **Rendere il messaggio una frase breve e completa** : se il messaggio facoltativo è necessario per ottenere il punto dell'avviso, mantenerlo il più semplice possibile e renderlo una frase completa con maiuscole e punteggiatura corretti.

<a name="Alert-Buttons" />

### <a name="alert-buttons"></a>Pulsanti di avviso

Apple ha il suggerimento seguente per aggiungere pulsanti a un avviso:

- **Limite a due pulsanti** : laddove possibile, limitare l'avviso a un massimo di due pulsanti. Gli avvisi con pulsante singolo forniscono informazioni ma non azioni. Gli avvisi di due pulsanti forniscono una semplice scelta di azione Sì/No.
- **Usare i titoli concise, i pulsanti logici** e i titoli dei pulsanti semplici da uno a due parole che descrivono chiaramente l'azione del pulsante in modo ottimale. Per ulteriori informazioni, vedere la documentazione relativa [all'utilizzo dei pulsanti](~/ios/tvos/user-interface/buttons.md) .
- **Contrassegnare chiaramente i pulsanti distruttivi** : per i pulsanti che eseguono un'azione distruttiva (ad esempio l'eliminazione di un `UIAlertActionStyle.Destructive` file) contrassegnarli chiaramente con lo stile.

<a name="Displaying-an-Alert" />

## <a name="displaying-an-alert"></a>Visualizzazione di un avviso

Per visualizzare un avviso, creare un'istanza del `UIAlertViewController` e configurarla aggiungendo azioni (pulsanti) e selezionando lo stile dell'avviso. Il codice seguente, ad esempio, Visualizza un avviso OK/Annulla:

```csharp
const string title = "A Short Title is Best";
const string message = "A message should be a short, complete sentence.";
const string acceptButtonTitle = "OK";
const string cancelButtonTitle = "Cancel";
const string deleteButtonTitle = "Delete";
...

var alertController = UIAlertController.Create (title, message, UIAlertControllerStyle.Alert);

// Create the action.
var acceptAction = UIAlertAction.Create (acceptButtonTitle, UIAlertActionStyle.Default, _ =>
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

var cancelAction = UIAlertAction.Create (cancelButtonTitle, UIAlertActionStyle.Cancel, _ =>
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

// Add the actions.
alertController.AddAction (acceptAction);
alertController.AddAction (cancelAction);
PresentViewController (alertController, true, null);
```

Diamo uno sguardo a questo codice in dettaglio. Innanzitutto, viene creato un nuovo avviso con il titolo e il messaggio specificati:

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

Quindi, per ogni pulsante da visualizzare nell'avviso viene creata un'azione che definisce il titolo del pulsante, lo stile e l'azione che si desidera eseguire se viene premuto il pulsante:

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ =>
    // Do something when the button is pressed
    ...
);
```

L' `UIAlertActionStyle` enumerazione consente di impostare lo stile del pulsante come uno dei seguenti:

- **Impostazione predefinita** : il pulsante sarà il pulsante predefinito selezionato quando viene visualizzato l'avviso.
- **Annulla** : il pulsante è il pulsante Annulla per l'avviso.
- **Distruttivo** : evidenzia il pulsante come un'azione distruttiva, ad esempio l'eliminazione di un file. Attualmente, tvOS esegue il rendering del pulsante distruttivo con uno sfondo rosso.

Il `AddAction` metodo aggiunge l'azione specificata `UIAlertViewController` a e infine il `PresentViewController (alertController, true, null)` metodo Visualizza l'avviso specificato all'utente.

<a name="Adding-Text-Fields" />

## <a name="adding-text-fields"></a>Aggiunta di campi di testo

Oltre ad aggiungere azioni (pulsanti) all'avviso, è possibile aggiungere campi di testo all'avviso per consentire all'utente di inserire informazioni quali ID utente e password:

[![](alerts-images/alert02.png "Campo di testo in un avviso")](alerts-images/alert02.png#lightbox)

Se l'utente seleziona il campo di testo, verrà visualizzata la tastiera tvOS standard che consente di immettere un valore per il campo:

[![](alerts-images/alert03.png "Immissione di testo")](alerts-images/alert03.png#lightbox)

Nel codice seguente viene visualizzato un avviso OK/Annulla con un solo campo di testo per l'immissione di un valore:

```csharp
UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
UITextField field = null;

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;

    // Initialize field
    field.Placeholder = placeholder;
    field.Text = text;
    field.AutocorrectionType = UITextAutocorrectionType.No;
    field.KeyboardType = UIKeyboardType.Default;
    field.ReturnKeyType = UIReturnKeyType.Done;
    field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

});

// Add cancel button
alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
    // User canceled, do something
    ...
}));

// Add ok button
alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
    // User selected ok, do something
    ...
}));

// Display the alert
controller.PresentViewController(alert,true,null);
```

Il `AddTextField` metodo aggiunge un nuovo campo di testo all'avviso che è possibile configurare impostando proprietà quali il testo segnaposto (il testo visualizzato quando il campo è vuoto), il valore di testo predefinito e il tipo di tastiera. Ad esempio:

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

Per poter agire sul valore del campo di testo in un secondo momento, viene salvata anche una copia di usando il codice seguente:

```csharp
UITextField field = null;
...

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;
    ...
});
```

Dopo che l'utente ha immesso un valore nel campo di testo, è possibile `field` usare la variabile per accedere a tale valore.

<a name="Alert-View-Controller-Helper-Class" />

## <a name="alert-view-controller-helper-class"></a>Classe helper del controller di visualizzazione avvisi

Poiché la visualizzazione di tipi semplici e comuni di `UIAlertViewController` avvisi tramite può comportare un numero molto ridotto di codice duplicato, è possibile utilizzare una classe helper per ridurre la quantità di codice ripetitivo. Ad esempio:

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;

namespace UIKit
{
    /// <summary>
    /// Alert view controller is a reusable helper class that makes working with <c>UIAlertViewController</c> alerts
    /// easier in a tvOS app.
    /// </summary>
    public class AlertViewController
    {
        #region Static Methods
        public static UIAlertController PresentOKAlert(string title, string description, UIViewController controller) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Configure the alert
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(action) => {}));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentOKCancelAlert(string title, string description, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentDestructiveAlert(string title, string description, string destructiveAction, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create(destructiveAction,UIAlertActionStyle.Destructive,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentTextInputAlert(string title, string description, string placeholder, string text, UIViewController controller, AlertTextInputDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
            UITextField field = null;

            // Add and configure text field
            alert.AddTextField ((textField) => {
                // Save the field
                field = textField;

                // Initialize field
                field.Placeholder = placeholder;
                field.Text = text;
                field.AutocorrectionType = UITextAutocorrectionType.No;
                field.KeyboardType = UIKeyboardType.Default;
                field.ReturnKeyType = UIReturnKeyType.Done;
                field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

            });

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false,"");
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null && field !=null) {
                    action(true, field.Text);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }
        #endregion

        #region Delegates
        public delegate void AlertOKCancelDelegate(bool OK);
        public delegate void AlertTextInputDelegate(bool OK, string text);
        #endregion
    }
}
```

Utilizzando questa classe, la visualizzazione e la risposta a avvisi semplici possono essere eseguite come segue:

```csharp
#region Custom Actions
partial void DisplayDestructiveAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentDestructiveAlert("A Short Title is Best","The message should be a short, complete sentence.","Delete",this, (ok) => {
        Console.WriteLine("Destructive Alert: The user selected {0}",ok);
    });
}

partial void DisplayOkCancelAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKCancelAlert("A Short Title is Best","The message should be a short, complete sentence.",this, (ok) => {
        Console.WriteLine("OK/Cancel Alert: The user selected {0}",ok);
    });
}

partial void DisplaySimpleAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKAlert("A Short Title is Best","The message should be a short, complete sentence.",this);
}

partial void DisplayTextInputAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentTextInputAlert("A Short Title is Best","The message should be a short, complete sentence.","placeholder", "", this, (ok, text) => {
        Console.WriteLine("Text Input Alert: The user selected {0} and entered `{1}`",ok,text);
    });
}
#endregion
```

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha spiegato come usare `UIAlertController` per visualizzare un messaggio di avviso all'utente in Novell. tvOS. In primo luogo, è stato illustrato come visualizzare un semplice avviso e aggiungere pulsanti. Successivamente, è stato illustrato come aggiungere campi di testo a un avviso. Infine, è stato illustrato come utilizzare una classe helper per ridurre la quantità di codice ripetitivo necessario per visualizzare un avviso.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
