---
title: Input di testo in xamarin. IOS
description: Questo documento descrive l'input di testo in un'app xamarin. IOS. Descritto l'utilizzo UITextField e UITextVIew sia a livello di programmazione sia in iOS Designer.
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: b309cbdf37acaa71740a4d5d03e4824efd40f359
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61200972"
---
# <a name="text-input-in-xamarinios"></a>Input di testo in xamarin. IOS

Accettare input di testo dell'utente viene eseguita con il `UITextField` per gli input a riga singola e UITextView testo modificabile su più righe. È possibile trascinare uno di questi controlli in una schermata e fare doppio clic per impostare il testo iniziale.

Gli screenshot seguenti mostrano le icone per questi controlli, che si trova nel riquadro della casella degli strumenti in Visual Studio per Mac:

 [![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png#lightbox)

 [![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png#lightbox)

Dopo avere assegnato un nome dell'outlet e salvato il file Storyboard, Visual Studio per Mac aggiornerà il `.designer.cs` classe parziale e si possono aggiungere C# codice che fa riferimento il controllo al file di classe. Ogni controllo ha un proprio proprietà univoche e gli eventi che sono accessibili di C# codice.

 <a name="UITextField" />


## <a name="uitextfield"></a>UITextField

Il `UITextField` controllo viene spesso usato per accettare una singola riga di input di testo, ad esempio un nome utente o password. Di seguito vengono illustrate alcune delle opzioni disponibili per la personalizzazione del controllo:

 [![](text-input-images/image15a.png "Proprietà UITextField")](text-input-images/image15a.png#lightbox)

Questi controlli vengono spiegati di seguito:

-  **Segnaposto** : questa operazione è facoltativa. Se impostato, viene visualizzato quando il campo di testo è vuoto, in genere per spiegare all'utente è previsto l'input.
-  **Pulsante Cancella** : consente di controllare quando il pulsante Cancella standard (grigio cerchio (x)) viene visualizzato nel campo di testo, come un modo per cancellare il testo rapidamente all'utente. Può essere nascoste in modo permanente, in modo permanente visibile o illustrato, a seconda se il campo viene modificato.
-  **Dimensioni minime del carattere** e **Adjust adatta alla pagina** : consente le dimensioni del carattere devono essere regolate automaticamente per adattare il testo più lungo e impedire il troncamento, ma limitato su no più piccola della dimensione specificata.
-  **L'uso delle maiuscole** : se si desidera inserire l'iniziale maiuscola automaticamente parole, frasi o tutti gli input.
-  **Correzione** : indica se il controllo ortografico e i suggerimenti sono abilitati.
-  **Tastiera** : controlli lo stile della tastiera visualizzata per l'input e pertanto le chiavi sono disponibili sulla tastiera. Oltre ad altre opzioni sono inclusi tastierino numerico, tastierino telefonico, indirizzo di posta elettronica, URL.
-  **Aspetto** : controlla l'aspetto e lo stile della tastiera e verranno entrambi scuro o con tema chiaro di.
-  **Il tasto** : modificare l'etichetta per la chiave Return per riflettere meglio l'azione che verrà eseguita. I valori supportati includono Go, Join, Next, Route, operazione eseguita e ricerca.
-  **Proteggere** : indica se l'input viene mascherato (ad esempio un input di Password).


Se viene chiamato un UITextField `textfield1` è stata aggiunta a una schermata con la finestra di progettazione, è possibile impostare o modificare le proprietà in C# come indicato di seguito:

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin. IOS fornisce enumerazioni dove appropriato, rendono più semplice selezionare le impostazioni desiderate, ad esempio la `UIKeyboardType` e `UIReturnKeyType` nel frammento di codice riportato sopra.

### <a name="display-text-programmatically"></a>Visualizzare il testo a livello di codice

Se non si vuole progettare lo schermo con la finestra di progettazione o se si desidera aggiungere il testo in modo dinamico in fase di esecuzione, è possibile creare e visualizzare un livello di programmazione UITextField il `ViewDidLoad` metodo di un controller di visualizzazione come segue:

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />


## <a name="uitextview"></a>UITextView

Il `UITextView` controllo può essere usato per visualizzare il testo di sola lettura o di accettare input di testo su più righe. Ha molte delle stesse opzioni come la `UITextField` (ad esempio l'uso delle maiuscole, correzione, e così via).

 [![](text-input-images/image16a.png "Proprietà UITextView")](text-input-images/image16a.png#lightbox)

Le proprietà specifiche includono:

-  **Comportamento** : indica se il testo è modificabile o di sola lettura.
-  **Rilevamento** : rileva e converte i dati immessi in elementi selezionabili tramite clic, ad esempio i numeri di telefono che possono attivare una chiamata, gli indirizzi che diventano i collegamenti alle mappe, gli URL da aprire in Safari o date e ore che diventano gli eventi nel calendario.


Se un UITextView è stata aggiunta a una schermata con la finestra di progettazione, è possibile impostare o modificare le relative proprietà simile al seguente:

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```



## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://developer.xamarin.com/samples/Controls/)
