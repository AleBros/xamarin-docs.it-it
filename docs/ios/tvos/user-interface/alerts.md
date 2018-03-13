---
title: Utilizzo degli avvisi
description: In questo articolo viene descritto l'utilizzo con UIAlertController per visualizzare un messaggio di avviso all'utente in Xamarin.tvOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 6dabba30c5242d6e7e9ef42a4025f87826a5b89e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-alerts"></a>Utilizzo degli avvisi

_In questo articolo viene descritto l'utilizzo con UIAlertController per visualizzare un messaggio di avviso all'utente in Xamarin.tvOS._


Se è necessario ottenere l'attenzione dell'utente tvOS o richiedere l'autorizzazione per eseguire un'azione distruttiva (ad esempio l'eliminazione di un file), è possibile presentare un messaggio di avviso utilizzando il `UIAlertViewController`:

[![](alerts-images/alert01.png "Un esempio UIAlertViewController")](alerts-images/alert01.png#lightbox)

Se oltre a visualizzare un messaggio, è possibile aggiungere i pulsanti e i campi di testo a un avviso per consentire all'utente di rispondere alle azioni e fornire commenti e suggerimenti.

<a name="About-Alerts" />

## <a name="about-alerts"></a>Informazioni sugli avvisi

Come descritto in precedenza, gli avvisi vengono utilizzati per ottenere l'attenzione dell'utente e di informarli dello stato dell'app o richiesta di commenti e suggerimenti. Gli avvisi devono presentare un titolo, essi possono disporre di un messaggio e uno o più pulsanti o i campi di testo.

[![](alerts-images/alert04.png "Un avviso di esempio")](alerts-images/alert04.png#lightbox)

Apple ha i seguenti suggerimenti per l'utilizzo degli avvisi:

- **Utilizzare gli avvisi in modo sporadico** -avvisi interrompere il flusso dell'utente con l'applicazione e interrompere l'esperienza utente e di conseguenza, deve essere utilizzati solo per le situazioni importanti quali le notifiche di errore, acquisti In-App e le azioni di distruzione. 
- **Sono disponibili opzioni utili** : se l'avviso vengono presentate le opzioni per l'utente, il deve assicurarsi che ogni opzione offre informazioni critiche e rende disponibili azioni utile per richiedere all'utente.

<a name="Alert-Titles-and-Messages" />

### <a name="alert-titles-and-messages"></a>I messaggi e i titoli degli avvisi

Apple ha i seguenti suggerimenti per la presentazione di titolo e il messaggio facoltativo un avviso:

- **Usare titoli Multiword** -del titolo dell'avviso An è necessario ottenere il punto della situazione tra chiaramente pur rimanendo semplice. Un titolo singola parola raramente fornisce informazioni sufficienti.
- **Usare titoli descrittivi che non richiedono un messaggio** - laddove possibile, si consiglia di rendere descrittivo titolo dell'avviso è sufficiente che il testo del messaggio facoltativo non è necessaria. 
- **Rendere il messaggio breve frase completa** : se il messaggio facoltativo è necessario per ottenere il punto dell'avviso tra, rendere più semplice possibile e renderlo una frase completa con maiuscole/minuscole correttamente e la punteggiatura.

<a name="Alert-Buttons" />

### <a name="alert-buttons"></a>Avviso di pulsanti

Apple ha il suggerimento per l'aggiunta di pulsanti a un avviso seguente:

- **Limite di due pulsanti** - laddove possibile, limitare l'avviso a un massimo di due pulsanti. Solo gli avvisi di pulsante forniscono informazioni ma non le azioni. Gli avvisi di pulsante due forniscono una semplice sì/no scelta dell'azione.
- **Utilizzare Succinct, titoli pulsante logico** -uno o due i titoli di pulsante di word che descrivono in modo chiaro l'azione del pulsante funzionano meglio semplice. Per ulteriori informazioni, vedere il nostro [utilizzo di pulsanti](~/ios/tvos/user-interface/buttons.md) documentazione.
- **Chiaramente i pulsanti distruttiva contrassegno** : per i pulsanti che eseguono un'azione (ad esempio l'eliminazione di un file) distruttiva contrassegnarli chiaramente con il `UIAlertActionStyle.Destructive` stile.

<a name="Displaying-an-Alert" />

## <a name="displaying-an-alert"></a>Visualizzazione di un avviso

Per visualizzare un avviso, si crea un'istanza di `UIAlertViewController` e configurarlo aggiungendo azioni (pulsanti) e selezionando lo stile dell'avviso. Ad esempio, il codice seguente visualizza un avviso di annullamento/OK:

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

Diamo un'occhiata a questo codice in modo dettagliato. In primo luogo, è creare un nuovo avviso con il titolo e il messaggio:

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

Per ogni pulsante che si desidera visualizzare nell'avviso verrà quindi creata un'azione che definisce il titolo del pulsante, lo stile e l'azione che si desidera eseguire se viene premuto il pulsante:

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ => 
    // Do something when the button is pressed
    ...
);
```

Il `UIAlertActionStyle` enum consentono di impostare lo stile del pulsante come uno dei seguenti:

- **Predefinito** -pulsante sarà il pulsante predefinito quando viene visualizzato l'avviso selezionato.
- **Annulla** -il pulsante è il pulsante di annullamento per l'avviso.
- **Distruttiva** -evidenzia il pulsante come azione distruttiva, ad esempio l'eliminazione di un file. Attualmente, tvOS esegue il rendering sul pulsante distruttivo con uno sfondo rosso.

Il `AddAction` metodo aggiunge l'azione specificata per il `UIAlertViewController` e infine il `PresentViewController (alertController, true, null)` metodo visualizza l'avviso specificato per l'utente.

<a name="Adding-Text-Fields" />

## <a name="adding-text-fields"></a>Aggiunta di campi di testo

Oltre ad aggiungere azioni (pulsanti) per l'avviso, è possibile aggiungere i campi di testo dell'avviso per consentire all'utente di inserire informazioni quali ID utente e password:

[![](alerts-images/alert02.png "Campo di testo in un avviso")](alerts-images/alert02.png#lightbox)

Se l'utente seleziona il campo di testo, verrà visualizzata la tastiera standard tvOS consentendo loro di immettere un valore per il campo:

[![](alerts-images/alert03.png "Immissione di testo")](alerts-images/alert03.png#lightbox)

Il codice seguente viene visualizzato un avviso di annullamento/OK con un singolo campo di testo per l'immissione di un valore:

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

Il `AddTextField` metodo aggiunge un nuovo campo di testo all'avviso che è quindi possibile configurare impostando le proprietà come segnaposto del testo (il testo che viene visualizzato quando il campo è vuoto), il valore di testo predefinito e il tipo di tastiera. Ad esempio:

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

In modo che è possibile intervenire sul valore del campo di testo in un secondo momento, è anche stiamo salvare una copia dell'utilizzo di codice riportato di seguito:

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

Dopo che l'utente ha immesso un valore nel campo di testo, è possibile utilizzare il `field` variabile per accedere a tale valore.

<a name="Alert-View-Controller-Helper-Class" />

## <a name="alert-view-controller-helper-class"></a>Classe di supporto di Controller di visualizzazione avvisi

Poiché la visualizzazione semplici e comuni di tipi di avvisi tramite `UIAlertViewController` può provocare gran parte del codice duplicato, è possibile utilizzare una classe helper per ridurre la quantità di codice ripetitivo. Ad esempio:

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

Utilizzo di questa classe, visualizzare e rispondere agli avvisi semplici può essere eseguite nel modo seguente:

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

In questo articolo è illustrati l'utilizzo di `UIAlertController` per visualizzare un messaggio di avviso all'utente in Xamarin.tvOS. In primo luogo, è stato spiegato come visualizzare un avviso semplice e aggiungere i pulsanti. Successivamente, mostrato come aggiungere i campi di testo a un avviso. Infine, mostrato come utilizzare una classe helper per ridurre la quantità di codice ripetitivo necessaria per visualizzare un avviso.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
