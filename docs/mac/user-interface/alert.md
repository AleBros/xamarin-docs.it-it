---
title: Avvisi
description: In questo articolo viene descritto l'utilizzo con gli avvisi in un'applicazione Xamarin.Mac. Descrive la creazione e la visualizzazione degli avvisi dal codice c# e risponde alle interazioni dell'utente.
ms.topic: article
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 8901bb57ace4f05e8c26fdc43dfe8c476927903a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="alerts"></a>Avvisi

_In questo articolo viene descritto l'utilizzo con gli avvisi in un'applicazione Xamarin.Mac. Descrive la creazione e la visualizzazione degli avvisi dal codice c# e risponde alle interazioni dell'utente._

Quando si utilizza c# e .NET in un'applicazione Xamarin.Mac, è possibile accedere alla stessa avvisi che uno sviluppatore che lavora *Objective-C* e *Xcode* does. 

Un avviso è un tipo speciale di finestra di dialogo visualizzata quando si verifica un problema grave (ad esempio un errore) o come un avviso (ad esempio, la preparazione dell'eliminazione di un file). Poiché un avviso è una finestra di dialogo, è necessario inoltre una risposta dell'utente prima di chiuderla.

[![](alert-images/alert06.png "Un avviso di esempio")](alert-images/alert06.png#lightbox)

In questo articolo verranno descritte le nozioni fondamentali sull'utilizzo degli avvisi in un'applicazione Xamarin.Mac. 

<a name="Introduction_to_Alerts" />

## <a name="introduction-to-alerts"></a>Introduzione agli avvisi

Un avviso è un tipo speciale di finestra di dialogo visualizzata quando si verifica un problema grave (ad esempio un errore) o come un avviso (ad esempio, la preparazione dell'eliminazione di un file). Poiché gli avvisi danneggiare l'utente, poiché deve essere chiusa prima che l'utente può continuare con le attività, evitare la visualizzazione di un avviso, a meno che non sia assolutamente necessario.

Apple Suggerire le linee guida seguenti:

- Non usare un avviso solo per fornire agli utenti informazioni.
- Non viene visualizzato un avviso per le azioni comuni, annullabile. Anche se questa situazione potrebbe causare la perdita di dati.
- Se una situazione è un avviso, evitare di utilizzare qualsiasi altro elemento dell'interfaccia utente o metodo per visualizzarlo.
- L'icona di attenzione deve essere utilizzata con cautela.
- Vengono descritti dalla situazione di avviso in modo chiaro e conciso nel messaggio di avviso.
- Il nome del pulsante predefinito deve corrispondere all'azione che è descrivere nel messaggio di avviso.

Per ulteriori informazioni, vedere il [avvisi](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1) sezione di Apple [linee guida dell'interfaccia umana di OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Anatomy_of_an_Alert" />

## <a name="anatomy-of-an-alert"></a>Anatomia di un avviso

Come descritto in precedenza, gli avvisi devono essere visualizzati all'utente dell'applicazione quando si verifica un problema grave o un avviso di perdita di dati (ad esempio la chiusura di un file non salvato). In Xamarin.Mac, un avviso viene creato nel codice c#, ad esempio:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Critical,
    InformativeText = "We need to save the document here...",
    MessageText = "Save Document",
};
alert.RunModal ();
```

Il codice precedente viene visualizzato un avviso con l'icona di applicazioni sovrapposta l'icona di avviso, un titolo, un messaggio di avviso e un singolo **OK** pulsante:

[![](alert-images/alert01.png "Un avviso con un pulsante OK.")](alert-images/alert01.png#lightbox)

Apple fornisce diverse proprietà che possono essere utilizzate per personalizzare un avviso:

- **AlertStyle** definisce il tipo di un avviso come uno dei seguenti:
    - **Avviso** : utilizzato per avvertire l'utente di un evento corrente o imminente che non è critico. Questo è lo stile predefinito.
    - **Informativo** : utilizzato per avvertire l'utente corrente o imminente di un evento. Attualmente non è disponibile alcuna differenza visibile tra un **avviso** e **informativo**
    - **Critico** : utilizzato per avvertire l'utente gravi conseguenze di un imminente evento (ad esempio l'eliminazione di un file). Questo tipo di avviso deve essere utilizzato con cautela.
- **MessageText** -questo è il messaggio principale o il titolo dell'avviso e deve definire la situazione rapidamente all'utente.
- **InformativeText** -si tratta del corpo dell'avviso in cui è necessario definire chiaramente la situazione e presentare le opzioni di utilizzabile per l'utente.
- **Icona** -consente un'icona personalizzata da visualizzare all'utente.
- **HelpAnchor** & **ShowsHelp** -consente l'avviso essere correlato all'applicazione HelpBook e visualizzare la Guida per l'avviso.
- **Pulsanti** -per impostazione predefinita, un avviso dispone solo di **OK** pulsante ma la **pulsanti** raccolta consente di aggiungere le opzioni disponibili in base alle esigenze.
- **ShowsSuppressionButton** - se `true` Visualizza una casella di controllo che l'utente può utilizzare per sopprimere l'avviso per le successive occorrenze dell'evento di cui è stato attivato.
- **AccessoryView** -consente di associare un'altra visualizzazione secondaria per l'avviso per fornire informazioni aggiuntive, ad esempio aggiungendo un **campo di testo** per l'immissione dati. Se si imposta un nuovo **AccessoryView** o modificarne uno esistente, è necessario chiamare il `Layout()` metodo per adattare il layout visibile dell'avviso.

<a name="Displaying_an_Alert" />

## <a name="displaying-an-alert"></a>Visualizzazione di un avviso

Esistono due modi diversi che un avviso può essere visualizzata, tale o come un foglio. Il codice seguente viene visualizzato un avviso come mobile:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.RunModal ();
```
Se viene eseguito questo codice, viene visualizzato il seguente:

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

## <a name="working-with-alert-buttons"></a>Utilizzo di pulsanti di avviso

Per impostazione predefinita, un avviso viene visualizzato solo il **OK** pulsante. Tuttavia, non sono limitati a, è possibile creare pulsanti aggiuntivi aggiungendole al **pulsanti** insieme. Il codice seguente crea un avviso di ancoraggio con un **OK**, **Annulla** e **forse** pulsante:

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

Il primo pulsante aggiunto sarà il _pulsante predefinito_ che verrà attivata se l'utente preme il tasto INVIO. Il valore restituito sarà un numero intero che rappresenta quale pulsante l'utente selezionato. In questo caso verranno restituiti i valori seguenti:

- **OK** - 1000.
- **Annulla** - 1001.
- **Forse** - 1002.

Se si esegue il codice, di seguito verrà visualizzato:

[![](alert-images/alert04.png "Un avviso con tre opzioni pulsante")](alert-images/alert04.png#lightbox)

Di seguito è riportato il codice per lo stesso avviso come un foglio di:

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

## <a name="showing-the-suppress-button"></a>Con il pulsante Elimina

Se l'avviso `ShowSuppressButton` proprietà `true`, l'avviso consente di visualizzare una casella di controllo che l'utente può utilizzare per sopprimere l'avviso per le successive occorrenze dell'evento di cui è stato attivato. Il codice seguente viene visualizzato un avviso di ancoraggio con un pulsante Elimina:

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

Se il valore di `alert.SuppressionButton.State` è `NSCellStateValue.On`, l'utente ha selezionato la casella di controllo Elimina, in caso contrario non lo siano.

Se viene eseguito il codice, di seguito verrà visualizzato:

[![](alert-images/alert06.png "Un avviso con un pulsante Elimina")](alert-images/alert06.png#lightbox)

Di seguito è riportato il codice per lo stesso avviso come un foglio di:

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

[![](alert-images/alert07.png "Visualizzare un avviso con un pulsante Elimina come foglio")](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView" />

## <a name="adding-a-custom-subview"></a>Aggiunta di una visualizzazione personalizzata secondaria

Gli avvisi possono avere un `AccessoryView` proprietà che può essere utilizzata per personalizzare ulteriormente l'avviso e aggiungere elementi come un **campo di testo** per l'input dell'utente. Il codice seguente crea un avviso di ancoraggio con un campo di input di testo aggiunto:

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

Le righe di chiave sono `var input = new NSTextField (new CGRect (0, 0, 300, 20));` che consente di creare un nuovo **campo di testo** che verrà aggiunta l'avviso. `alert.AccessoryView = input;` che associa il **campo di testo** per l'avviso e la chiamata al `Layout()` metodo, è necessario ridimensionare l'avviso per la nuova visualizzazione secondaria.

Se si esegue il codice, di seguito verrà visualizzato:

[![](alert-images/alert08.png "Se si esegue il codice, di seguito verrà visualizzato")](alert-images/alert08.png#lightbox)

Di seguito è riportato lo stesso avviso come un foglio di:

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

In questo articolo è stato applicato l'utilizzo degli avvisi in un'applicazione Xamarin.Mac un'analisi approfondita. È stato illustrato i diversi tipi e utilizzi di avvisi, come creare e personalizzare gli avvisi e come gestire gli avvisi nel codice c#.

## <a name="related-links"></a>Collegamenti correlati

- [MacWindows (esempio)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilizzo di Windows](~/mac/user-interface/window.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)
