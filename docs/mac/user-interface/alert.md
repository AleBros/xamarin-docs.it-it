---
title: Avvisi in xamarin. Mac
description: Questo articolo descrive come usare gli avvisi in un'applicazione xamarin. Mac. Viene descritta la creazione e la visualizzazione degli avvisi da C# codice e risposta alle interazioni dell'utente.
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 8f84b688998251db52c8c2be71949e1a2e665dc0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103959"
---
# <a name="alerts-in-xamarinmac"></a>Avvisi in xamarin. Mac

_Questo articolo descrive come usare gli avvisi in un'applicazione xamarin. Mac. Viene descritta la creazione e la visualizzazione degli avvisi da C# codice e risposta alle interazioni dell'utente._

Quando si lavora con C# e .NET in un'applicazione xamarin. Mac, è possibile utilizzare lo stesso avvisa che gli sviluppatori che lavorano *Objective-C* e *Xcode* viene. 

Un avviso è un tipo speciale di finestra di dialogo che viene visualizzata quando si verifica un problema grave (ad esempio un errore) o come un avviso (ad esempio, la preparazione dell'eliminazione di un file). Poiché un avviso è una finestra di dialogo, è necessario inoltre una risposta dell'utente prima di chiuderla.

[![](alert-images/alert06.png "Un avviso di esempio")](alert-images/alert06.png#lightbox)

In questo articolo, si affronterà le nozioni di base dell'utilizzo degli avvisi in un'applicazione xamarin. Mac. 

<a name="Introduction_to_Alerts" />

## <a name="introduction-to-alerts"></a>Introduzione agli avvisi

Un avviso è un tipo speciale di finestra di dialogo che viene visualizzata quando si verifica un problema grave (ad esempio un errore) o come un avviso (ad esempio, la preparazione dell'eliminazione di un file). Poiché gli avvisi interferire con l'utente poiché deve essere chiusa prima che l'utente può continuare con la propria attività, evitare la visualizzazione di un avviso a meno che non sia assolutamente necessario.

Apple consiglia le linee guida seguenti:

- Non usare un avviso solo per fornire agli utenti informazioni.
- Non visualizzare un avviso per le azioni comuni, annullabile. Anche se questa situazione potrebbe causare la perdita di dati.
- Se una situazione è degna di un avviso, evitare di usare qualsiasi altro elemento dell'interfaccia utente o metodo per visualizzarlo.
- L'icona di avviso deve essere utilizzato solo se necessario.
- Descrivere la situazione di avviso chiara e illustrare sinteticamente nel messaggio di avviso.
- Il nome del pulsante predefinito deve corrispondere all'azione che è descrivere il messaggio di avviso.

Per altre informazioni, vedere la [Alerts](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Anatomy_of_an_Alert" />

## <a name="anatomy-of-an-alert"></a>Anatomia di un avviso

Come indicato in precedenza, gli avvisi devono essere visualizzati all'utente dell'applicazione quando si verifica un problema grave o un avviso di potenziale perdita di dati (ad esempio la chiusura di un file non salvato). In xamarin. Mac, viene creato un avviso C# code, ad esempio:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Critical,
    InformativeText = "We need to save the document here...",
    MessageText = "Save Document",
};
alert.RunModal ();
```

Il codice precedente viene visualizzato un avviso con l'icona di applicazioni sovrapposta sull'icona di avviso, un titolo, un messaggio di avviso e un singolo **OK** pulsante:

[![](alert-images/alert01.png "Un avviso con un pulsante OK")](alert-images/alert01.png#lightbox)

Apple offre diverse proprietà che può essere utilizzata per personalizzare un avviso:

- **AlertStyle** definisce il tipo di un avviso come uno dei seguenti:
    - **Avviso** : utilizzato per avvertire l'utente di un evento imminente o corrente che non è critico. Questo è lo stile predefinito.
    - **Informativo** : utilizzato per avvertire l'utente su un evento imminente o corrente. Attualmente non c'è alcuna differenza visibile tra un **avviso** e un **informativo**
    - **Critico** : consente di avvertire l'utente gravi conseguenze di un evento imminente (ad esempio, l'eliminazione di un file). Questo tipo di avviso deve essere utilizzato solo se necessario.
- **MessageText** -questo è il messaggio principale o il titolo dell'avviso e la situazione dovrebbe definire rapidamente all'utente.
- **InformativeText** -si tratta del corpo dell'avviso in cui è necessario definire chiaramente la situazione e presentare opzioni utilizzabile per ottenere l'utente.
- **Icona** -consente a un'icona personalizzata da visualizzare all'utente.
- **HelpAnchor** & **ShowsHelp** -consente l'avviso a essere associate all'applicazione HelpBook e visualizzare la Guida per l'avviso.
- **I pulsanti** -per impostazione predefinita, un avviso ha solo le **OK** pulsante ma il **pulsanti** raccolta consente di aggiungere altre opzioni in base alle esigenze.
- **ShowsSuppressionButton** - se `true` Visualizza una casella di controllo che l'utente può usare per sopprimere l'avviso per le successive occorrenze dell'evento di cui è stato attivato.
- **AccessoryView** -consente di associare un'altra visualizzazione secondaria all'avviso per fornire informazioni aggiuntive, quali l'aggiunta di un **campo di testo** per immissione dati. Se si imposta una nuova **AccessoryView** o modificarne uno esistente, è necessario chiamare il `Layout()` metodo per modificare il layout visibile dell'avviso.

<a name="Displaying_an_Alert" />

## <a name="displaying-an-alert"></a>Visualizzazione di un avviso

Esistono due modi diversi, che un avviso può essere visualizzato, mobile o come un foglio. Il codice seguente visualizza un avviso come i:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.RunModal ();
```
Se questo codice viene eseguito, verrà visualizzato quanto segue:

[![](alert-images/alert02.png "Un avviso semplice")](alert-images/alert02.png#lightbox)

Il codice seguente consente di visualizzare lo stesso avviso come un foglio di:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

Se viene eseguito questo codice, di seguito verrà visualizzato:

[![](alert-images/alert03.png "Un avviso visualizzato come un foglio")](alert-images/alert03.png#lightbox)


<a name="Working_with_Alert_Buttons" />

## <a name="working-with-alert-buttons"></a>Uso di pulsanti degli avvisi

Per impostazione predefinita, un avviso viene visualizzato solo il **OK** pulsante. Tuttavia, non si è limitati a, è possibile creare pulsanti aggiuntivi aggiungendole al **pulsanti** raccolta. Il codice seguente crea un avviso liberamente con un **OK**, **Cancel** e **forse** pulsante:

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

Il primo pulsante aggiunto sarà il _pulsante predefiniti_ che verrà attivata se l'utente preme il tasto INVIO. Il valore restituito sarà un intero che rappresenta il pulsante su cui l'utente ha premuto. In questo caso verranno restituiti i valori seguenti:

- **OK** - 1000.
- **Annulla** : 1001.
- **Forse** - 1002.

Se eseguiamo il codice, di seguito verrà visualizzato:

[![](alert-images/alert04.png "Un avviso con tre opzioni di pulsante")](alert-images/alert04.png#lightbox)

Ecco il codice per lo stesso avviso come un foglio di:

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
Se viene eseguito questo codice, di seguito verrà visualizzato:

[![](alert-images/alert05.png "Un avviso di tre pulsante visualizzato come un foglio")](alert-images/alert05.png#lightbox)

> [!IMPORTANT]
> Non aggiungere mai più di tre pulsanti a un avviso.

<a name="Showing_the_Suppress_Button" />

## <a name="showing-the-suppress-button"></a>Che mostra il pulsante Elimina

Se l'avviso `ShowSuppressButton` è di proprietà `true`, una casella di controllo che l'utente può usare per sopprimere l'avviso per le successive occorrenze dell'evento che lo ha attivato viene visualizzato l'avviso. Il codice seguente viene visualizzato un avviso liberamente con un pulsante Elimina:

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

Se il valore della `alert.SuppressionButton.State` è `NSCellStateValue.On`, l'utente ha selezionato la casella di controllo non visualizzare, altrimenti non lo sono.

Se viene eseguito il codice, di seguito verrà visualizzato:

[![](alert-images/alert06.png "Un avviso con un pulsante Elimina")](alert-images/alert06.png#lightbox)

Ecco il codice per lo stesso avviso come un foglio di:

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

Se viene eseguito questo codice, di seguito verrà visualizzato:

[![](alert-images/alert07.png "Vengono visualizzati come un foglio di un avviso con un pulsante Elimina")](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView" />

## <a name="adding-a-custom-subview"></a>Aggiunta di una visualizzazione secondaria personalizzata

Gli avvisi possono avere un `AccessoryView` proprietà che può essere usata per personalizzare ulteriormente l'avviso e aggiungere ad esempio, una **campo di testo** input dell'utente. Il codice seguente crea un avviso liberamente con un campo di input di testo aggiunto:

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

Le righe di chiave sono `var input = new NSTextField (new CGRect (0, 0, 300, 20));` che consente di creare una nuova **campo di testo** che verrà aggiunta l'avviso. `alert.AccessoryView = input;` che consente di collegare il **campo di testo** per l'avviso e la chiamata al `Layout()` (metodo), che è necessaria per ridimensionare l'avviso in modo da adattarlo la nuova visualizzazione secondaria.

Se eseguiamo il codice, di seguito verrà visualizzato:

[![](alert-images/alert08.png "Se eseguiamo il codice, di seguito verrà visualizzato")](alert-images/alert08.png#lightbox)

Ecco lo stesso avviso come un foglio di:

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

Se si esegue questo codice, di seguito verrà visualizzato:

[![](alert-images/alert09.png "Un avviso con una visualizzazione personalizzata")](alert-images/alert09.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di utilizzo degli avvisi in un'applicazione xamarin. Mac. Abbiamo visto i diversi tipi e utilizzi degli avvisi, come creare e personalizzare gli avvisi e come utilizzare gli avvisi in C# codice.

## <a name="related-links"></a>Collegamenti correlati

- [MacWindows (esempio)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)
