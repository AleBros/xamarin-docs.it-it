---
title: Avvisi in Novell. Mac
description: Questo articolo illustra l'uso degli avvisi in un'applicazione Novell. Mac. Viene descritto come creare e visualizzare avvisi C# dal codice e rispondere alle interazioni dell'utente.
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 14a7fdc21c95b2febcf73497055461830b0df831
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645781"
---
# <a name="alerts-in-xamarinmac"></a>Avvisi in Novell. Mac

_Questo articolo illustra l'uso degli avvisi in un'applicazione Novell. Mac. Viene descritto come creare e visualizzare avvisi C# dal codice e rispondere alle interazioni dell'utente._

Quando si lavora C# con e .NET in un'applicazione Novell. Mac, si ha accesso agli stessi avvisi che uno sviluppatore lavora in *Objective-C* e *Xcode* . 

Un avviso è un tipo speciale di finestra di dialogo che viene visualizzato quando si verifica un problema grave, ad esempio un errore, o come un avviso, ad esempio la preparazione dell'eliminazione di un file. Poiché un avviso è una finestra di dialogo, richiede anche una risposta utente prima che possa essere chiusa.

[![](alert-images/alert06.png "Un avviso di esempio")](alert-images/alert06.png#lightbox)

In questo articolo verranno illustrate le nozioni di base sull'uso degli avvisi in un'applicazione Novell. Mac. 

<a name="Introduction_to_Alerts" />

## <a name="introduction-to-alerts"></a>Introduzione agli avvisi

Un avviso è un tipo speciale di finestra di dialogo che viene visualizzato quando si verifica un problema grave, ad esempio un errore, o come un avviso, ad esempio la preparazione dell'eliminazione di un file. Poiché gli avvisi interrompono l'utente perché devono essere rilasciati prima che l'utente possa continuare con la propria attività, evitare di visualizzare un avviso a meno che non sia assolutamente necessario.

Apple suggerisce le linee guida seguenti:

- Non usare un avviso solo per fornire informazioni agli utenti.
- Non visualizzare un avviso per le azioni comuni annullabili. Anche se questa situazione potrebbe causare la perdita di dati.
- Se una situazione è degna di un avviso, evitare di usare un altro elemento o metodo dell'interfaccia utente per visualizzarlo.
- L'icona di attenzione deve essere utilizzata con moderazione.
- Descrizione della situazione di avviso in modo chiaro e conciso nel messaggio di avviso.
- Il nome del pulsante predefinito deve corrispondere all'azione descritta nel messaggio di avviso.

Per altre informazioni, vedere la sezione [avvisi](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1) delle [linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) di Apple

<a name="Anatomy_of_an_Alert" />

## <a name="anatomy-of-an-alert"></a>Anatomia di un avviso

Come indicato in precedenza, gli avvisi devono essere visualizzati all'utente dell'applicazione quando si verifica un problema grave o come avviso alla potenziale perdita di dati, ad esempio la chiusura di un file non salvato. In Novell. Mac viene creato un avviso nel C# codice, ad esempio:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Critical,
    InformativeText = "We need to save the document here...",
    MessageText = "Save Document",
};
alert.RunModal ();
```

Il codice precedente Visualizza un avviso con l'icona delle applicazioni sovrapposta all'icona di avviso, un titolo, un messaggio di avviso e un singolo pulsante **OK** :

[![](alert-images/alert01.png "Un avviso con un pulsante OK")](alert-images/alert01.png#lightbox)

Apple fornisce diverse proprietà che possono essere usate per personalizzare un avviso:

- **AlertStyle** definisce il tipo di un avviso come uno dei seguenti:
    - **Avviso** : usato per avvisare l'utente di un evento corrente o imminente che non è critico. Questo è lo stile predefinito.
    - **Informational** : usato per avvisare l'utente di un evento corrente o imminente. Attualmente, non esiste alcuna differenza visibile tra un **avviso** e un messaggio informativo
    - **Critico** : usato per avvertire l'utente di conseguenze gravi di un evento imminente, ad esempio l'eliminazione di un file. Questo tipo di avviso deve essere usato con moderazione.
- **MessageText** : si tratta del messaggio principale o del titolo dell'avviso e dovrebbe definire rapidamente la situazione all'utente.
- **InformativeText** : questo è il corpo dell'avviso in cui è necessario definire la situazione in modo chiaro e presentare opzioni utilizzabili all'utente.
- **Icona** : consente di visualizzare un'icona personalizzata per l'utente.
- HelpAnchor & **ShowsHelp** -consente di associare l'avviso a HelpBook dell'applicazione e visualizzare la guida per l'avviso.
- **Buttons** : per impostazione predefinita, un avviso ha solo il pulsante **OK** , ma la raccolta **Buttons** consente di aggiungere altre opzioni in base alle esigenze.
- **ShowsSuppressionButton** -se `true` Visualizza una casella di controllo che può essere utilizzata dall'utente per disattivare l'avviso per le occorrenze successive dell'evento che lo ha attivato.
- **AccessoryView** : consente di aggiungere un'altra visualizzazione secondaria all'avviso per fornire informazioni aggiuntive, ad esempio l'aggiunta di un **campo di testo** per l'immissione di dati. Se si imposta un nuovo **AccessoryView** o se ne modifica uno esistente, è necessario chiamare il `Layout()` metodo per modificare il layout visibile dell'avviso.

<a name="Displaying_an_Alert" />

## <a name="displaying-an-alert"></a>Visualizzazione di un avviso

Esistono due modi diversi per visualizzare un avviso, mobile o come foglio. Il codice seguente visualizza un avviso come Floating libero:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.RunModal ();
```
Se questo codice viene eseguito, viene visualizzato quanto segue:

[![](alert-images/alert02.png "Un avviso semplice")](alert-images/alert02.png#lightbox)

Il codice seguente visualizza lo stesso avviso di un foglio:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

Se questo codice viene eseguito, verrà visualizzato quanto segue:

[![](alert-images/alert03.png "Un avviso visualizzato come foglio")](alert-images/alert03.png#lightbox)


<a name="Working_with_Alert_Buttons" />

## <a name="working-with-alert-buttons"></a>Uso dei pulsanti di avviso

Per impostazione predefinita, un avviso Visualizza solo il pulsante **OK** . Tuttavia, non si è limitati a tale scopo, è possibile creare pulsanti aggiuntivi aggiungendoli alla raccolta **Buttons** . Il codice seguente crea un avviso mobile libero con un pulsante **OK**, **Annulla** e **forse** :

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
var result = alert.RunModal ();
```

Il primo pulsante aggiunto sarà il _pulsante predefinito_ che verrà attivato se l'utente preme il tasto INVIO. Il valore restituito sarà un Integer che rappresenta il pulsante premuto dall'utente. In questo caso verranno restituiti i valori seguenti:

- **OK** - 1000.
- **Annulla** -1001.
- **Forse** -1002.

Se si esegue il codice, verrà visualizzato quanto segue:

[![](alert-images/alert04.png "Un avviso con tre opzioni di pulsante")](alert-images/alert04.png#lightbox)

Ecco il codice per lo stesso avviso di un foglio:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}", result);
});
```
Se questo codice viene eseguito, verrà visualizzato quanto segue:

[![](alert-images/alert05.png "Avviso a tre pulsanti visualizzato come foglio")](alert-images/alert05.png#lightbox)

> [!IMPORTANT]
> Non aggiungere mai più di tre pulsanti a un avviso.

<a name="Showing_the_Suppress_Button" />

## <a name="showing-the-suppress-button"></a>Visualizzazione del pulsante di eliminazione

Se la `ShowSuppressButton` proprietà dell'avviso è `true`, l'avviso Visualizza una casella di controllo che l'utente può utilizzare per disattivare l'avviso per le occorrenze successive dell'evento che l'ha attivato. Il codice seguente visualizza un avviso mobile libero con un pulsante di eliminazione:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

Se il valore di `alert.SuppressionButton.State` è `NSCellStateValue.On`, l'utente ha selezionato la casella di controllo non visualizzare, in caso contrario.

Se il codice viene eseguito, verrà visualizzato quanto segue:

[![](alert-images/alert06.png "Un avviso con un pulsante di eliminazione")](alert-images/alert06.png#lightbox)

Ecco il codice per lo stesso avviso di un foglio:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

Se questo codice viene eseguito, verrà visualizzato quanto segue:

[![](alert-images/alert07.png "Un avviso con un pulsante di eliminazione visualizzato come foglio")](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView" />

## <a name="adding-a-custom-subview"></a>Aggiunta di una visualizzazione personalizzata

Gli avvisi hanno `AccessoryView` una proprietà che può essere usata per personalizzare ulteriormente l'avviso e aggiungere elementi come un **campo di testo** per l'input dell'utente. Il codice seguente crea un avviso mobile libero con un campo di input di testo aggiunto:

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
AlertStyle = NSAlertStyle.Informational,
InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

Di seguito sono `var input = new NSTextField (new CGRect (0, 0, 300, 20));` riportate le linee chiave che consentono di creare un nuovo **campo di testo** che verrà aggiunto all'avviso. `alert.AccessoryView = input;`che connette il **campo di testo** all'avviso e la chiamata al `Layout()` metodo, che è necessario per ridimensionare l'avviso per adattarlo alla nuova visualizzazione.

Se si esegue il codice, verrà visualizzato quanto segue:

[![](alert-images/alert08.png "Se si esegue il codice, verrà visualizzato quanto segue:")](alert-images/alert08.png#lightbox)

Di seguito è riportato lo stesso avviso di un foglio:

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

Se si esegue questo codice, verranno visualizzati gli elementi seguenti:

[![](alert-images/alert09.png "Un avviso con una visualizzazione personalizzata")](alert-images/alert09.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in dettaglio l'uso degli avvisi in un'applicazione Novell. Mac. Abbiamo visto i diversi tipi e usi degli avvisi, come creare e personalizzare gli avvisi e come usare gli avvisi nel C# codice.

## <a name="related-links"></a>Collegamenti correlati

- [MacWindows (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/macwindows)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)
