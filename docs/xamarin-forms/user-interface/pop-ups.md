---
title: "Visualizza popup" Description: " Xamarin.Forms fornisce tre elementi di interfaccia utente di tipo popup, ovvero un avviso, un foglio di azione e una richiesta. Questo articolo illustra l'uso dell'avviso, della finestra delle azioni e delle API di prompt per visualizzare le finestre di dialogo che consentono agli utenti di inviare semplici domande, guidare gli utenti attraverso le attività e visualizzare i prompt. "
ms. prod: Novell MS. AssetID: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 03/10/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="display-pop-ups"></a>Visualizza popup

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)

La visualizzazione di un avviso, la richiesta a un utente di effettuare una scelta o la visualizzazione di un messaggio di richiesta è un'attività comune dell'interfaccia utente. Xamarin.Formsdispone di tre metodi sulla [`Page`](xref:Xamarin.Forms.Page) classe per l'interazione con l'utente tramite un popup: [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) , [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) e `DisplayPromptAsync` . Il rendering di questi metodi viene eseguito con i controlli nativi di ogni piattaforma.

## <a name="display-an-alert"></a>Visualizzare un avviso

Tutte le Xamarin.Forms piattaforme supportate hanno un popup modale per avvisare l'utente o per porre domande semplici. Per visualizzare questi avvisi in Xamarin.Forms , utilizzare il [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) metodo su qualsiasi [`Page`](xref:Xamarin.Forms.Page) . La riga di codice seguente illustra un semplice messaggio per l'utente:

```csharp
await DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Alert Dialog with One Button")

In questo esempio non vengono raccolte informazioni dall'utente. L'avviso viene visualizzato come modale. Quando viene ignorato, l'utente continua a interagire con l'applicazione.

Il [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) metodo può essere usato anche per acquisire la risposta di un utente presentando due pulsanti e restituendo `boolean` . Per ottenere una risposta da un avviso, specificare un testo per i due pulsanti e il metodo `await`. Dopo aver selezionato una delle opzioni, la risposta dell'utente sarà restituita al codice. Si notino le parole chiave `async` e `await` nell'esempio di codice seguente:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "Finestra di dialogo di avviso con due pulsanti")](pop-ups-images/alert2.png#lightbox "Finestra di dialogo di avviso con due pulsanti")

## <a name="guide-users-through-tasks"></a>Guida per gli utenti attraverso le attività

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) è un elemento comune dell'interfaccia utente in iOS. Il Xamarin.Forms [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) metodo consente di includere questo controllo nelle app multipiattaforma, eseguendo il rendering di alternative native in Android e UWP.

Per visualizzare un foglio delle azioni, impostare `await` [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) in qualsiasi oggetto [`Page`](xref:Xamarin.Forms.Page), passando il messaggio e le etichette dei pulsanti come stringhe. Il metodo restituisce l'etichetta di stringa del pulsante che è stato selezionato dall'utente. Di seguito è riportato un semplice esempio:

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "ActionSheet Dialog")

Il rendering del pulsante `destroy` viene eseguito diversamente rispetto agli altri pulsanti. È possibile lasciare il valore `null` o è possibile specificarlo come il terzo parametro di stringa. Nell'esempio seguente viene usato il pulsante `destroy`:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "Finestra di dialogo foglio di azione con pulsante Elimina")](pop-ups-images/action2.png#lightbox "Finestra di dialogo foglio di azione con pulsante Elimina")

## <a name="display-a-prompt"></a>Visualizzare un prompt

Per visualizzare una richiesta, chiamare `DisplayPromptAsync` in qualsiasi [`Page`](xref:Xamarin.Forms.Page) , passando un titolo e un messaggio come `string` argomenti:

```csharp
string result = await DisplayPromptAsync("Question 1", "What's your name?");
```

Il prompt viene visualizzato in modalità modale:

[![Screenshot di una richiesta modale, in iOS e Android](pop-ups-images/simple-prompt.png "Prompt modale")](pop-ups-images/simple-prompt-large.png#lightbox "Prompt modale")

Se si tocca il pulsante OK, la risposta immessa viene restituita come `string` . Se il pulsante Annulla viene toccato, `null` viene restituito.

L'elenco completo di argomenti per il `DisplayPromptAsync` metodo è:

- `title`, di tipo `string` , è il titolo da visualizzare nel prompt.
- `message`, di tipo `string` , è il messaggio da visualizzare nel prompt.
- `accept`, di tipo `string` , è il testo per il pulsante Accept. Si tratta di un argomento facoltativo, il cui valore predefinito è OK.
- `cancel`, di tipo `string` , è il testo per il pulsante Annulla. Si tratta di un argomento facoltativo, il cui valore predefinito è Cancel.
- `placeholder`, di tipo `string` , è il testo segnaposto da visualizzare nel prompt. Si tratta di un argomento facoltativo, il cui valore predefinito è `null` .
- `maxLength`, di tipo `int` , è la lunghezza massima della risposta dell'utente. Si tratta di un argomento facoltativo il cui valore predefinito è-1.
- `keyboard`, di tipo `Keyboard` , è il tipo di tastiera da usare per la risposta dell'utente. Si tratta di un argomento facoltativo, il cui valore predefinito è `Keyboard.Default` .
- `initialValue`, di tipo `string` , è una risposta predefinita che verrà visualizzata e che può essere modificata. Si tratta di un argomento facoltativo, il cui valore predefinito è un oggetto vuoto `string` .

Nell'esempio seguente viene illustrata l'impostazione di alcuni argomenti facoltativi:

```csharp
string result = await DisplayPromptAsync("Question 2", "What's 5 + 5?", initialValue: "10", maxLength: 2, keyboard: Keyboard.Numeric);
```

Questo codice visualizza una risposta predefinita di 10, limita il numero di caratteri che possono essere immessi a 2 e visualizza la tastiera numerica per l'input dell'utente:

[![Screenshot di una richiesta modale, in iOS e Android](pop-ups-images/keyboard-prompt.png "Prompt modale")](pop-ups-images/keyboard-prompt-large.png#lightbox "Prompt modale")

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di popup](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)
