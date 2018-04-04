---
title: Input di testo
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 43f1f0318bceb3da8a3d6216a49ec2af48ac155c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="text-input"></a>Input di testo

Accettare input di testo dell'utente viene eseguita con il `UITextField` per gli input a riga singola e UITextView per più righe di testo modificabile. È possibile trascinare uno di questi controlli in una schermata e fare doppio clic per impostare il testo iniziale.

Schermate riportate di seguito mostrano le icone per questi controlli, che si trova nel riquadro casella degli strumenti in Visual Studio per Mac:

 [![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png#lightbox)

 [![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png#lightbox)

Dopo avere specificato la presa e salvato il file Storyboard, Visual Studio per Mac aggiornerà la `.designer.cs` classe parziale che è possibile aggiungere il codice c# che fa riferimento al controllo al file di classe. Ogni controllo presenta proprietà univoche e gli eventi che sono accessibili nel codice c#.

 <a name="UITextField" />


## <a name="uitextfield"></a>UITextField

Il `UITextField` controllo verrà spesso usato per accettare una singola riga di input di testo, ad esempio un nome utente o password. Di seguito vengono illustrate alcune delle opzioni disponibili per la personalizzazione del controllo:

 [![](text-input-images/image15a.png "Proprietà UITextField")](text-input-images/image15a.png#lightbox)

Questi controlli vengono spiegati di seguito:

-  **Segnaposto** : questo passaggio è facoltativo. Se impostato, viene visualizzato quando il campo di testo è vuoto, in genere per indicare all'utente è previsto il tipo di input.
-  **Pulsante Cancella** : ciò consente di controllare quando il pulsante Cancella standard (grigio cerchio con (X)) nel campo di testo, come un modo per cancellare il testo rapidamente all'utente. Può essere nascosto in modo permanente, in modo permanente visibile o illustrato, a seconda che il campo viene modificato.
-  **Dimensione min** e **regolare adatta** : consente le dimensioni del carattere devono essere regolata automaticamente per adattare il testo più lungo e impedire il troncamento, ma limitato a non più piccola della dimensione specificata.
-  **Uso delle maiuscole** : se si desidera sfruttare appieno automaticamente parole, frasi o tutti gli input.
-  **Correzione** : indica se il controllo ortografico e i suggerimenti sono abilitati.
-  **Tastiera** : i controlli visualizzati lo stile della tastiera per l'input e pertanto le chiavi sono disponibili sulla tastiera. Ciò include tastierino numerico, il riempimento di telefono, posta elettronica, URL con altre opzioni.
-  **Aspetto** : controlla l'aspetto e lo stile della tastiera e verranno entrambi scuro o chiaro temi.
-  **Il tasto** : modificare l'etichetta per la chiave restituita per meglio riflettere l'azione che verrà eseguita. I valori supportati includono Go, Join, successivo, Route, di fatto e la ricerca.
-  **Proteggere** – indica se l'input viene mascherato (quali per un input di Password).


Se viene chiamato un UITextField `textfield1` è stata aggiunta a una schermata con la finestra di progettazione, è possibile impostare o modificare le proprietà in c# come segue:

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin offre enumerazioni dove appropriato, rendono più semplice selezionare le impostazioni desiderate, ad esempio il `UIKeyboardType` e `UIReturnKeyType` nel frammento di codice precedente.

### <a name="display-text-programmatically"></a>Visualizzare il testo a livello di codice

Se non si desidera progettare lo schermo con la finestra di progettazione o se si desidera aggiungere in modo dinamico un testo in fase di esecuzione, è possibile creare e visualizzare un livello di programmazione UITextField il `ViewDidLoad` metodo del controller visualizzazione simile al seguente:

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />


## <a name="uitextview"></a>UITextView

Il `UITextView` controllo può essere utilizzato per visualizzare il testo di sola lettura o per accettare l'input di testo a più righe. Dispone di molte delle stesse opzioni come la `UITextField` (ad esempio l'uso delle maiuscole, correzione, e così via).

 [![](text-input-images/image16a.png "Proprietà UITextView")](text-input-images/image16a.png#lightbox)

Le proprietà specifiche includono:

-  **Comportamento** : indica se il testo è modificabile o di sola lettura.
-  **Rilevamento** – rileva e converte i dati immessi in elementi selezionabili, ad esempio numeri di telefono che possono attivare una chiamata, gli indirizzi che diventano i collegamenti alle mappe, gli URL che apre in Safari o di data e ora che diventano gli eventi nel calendario.


Se un UITextView è stato aggiunto a una schermata con la finestra di progettazione, è possibile impostare o modificare le proprietà simile al seguente:

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```



## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://developer.xamarin.com/samples/Controls/)
