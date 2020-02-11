---
title: Input di testo in Xamarin.iOS
description: Questo documento descrive l'input di testo in un'app Xamarin.iOS. Viene illustrato l'uso di UITextField e UITextVIew a livello di codice e in iOS designer.
ms.prod: xamarin
ms.assetid: 03A7F1DC-017D-4501-91FD-82C78272CDB1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 4833d8a03649341cb5c6d9f2692410b89e6cea4c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021826"
---
# <a name="text-input-in-xamarinios"></a>Input di testo in Xamarin.iOS

L'accettazione dell'input di testo utente viene eseguita con la `UITextField` per gli input a riga singola e UITextView per il testo modificabile su più righe. È possibile trascinare uno di questi controlli su una schermata e fare doppio clic per impostare il testo iniziale.

Gli screenshot seguenti illustrano le icone per questi controlli, che si trovano nel riquadro Casella degli strumenti in Visual Studio per Mac:

 [![](text-input-images/image11a.png "UITextField")](text-input-images/image11a.png#lightbox)

 [![](text-input-images/image13a.png "UITextView")](text-input-images/image13a.png#lightbox)

Dopo aver denominato l'Outlet e salvato il file storyboard, Visual Studio per Mac aggiornerà la classe parziale `.designer.cs` ed è possibile aggiungere C# il codice che fa riferimento al controllo al file di classe. Ogni controllo dispone di proprietà ed eventi univoci a cui è possibile accedere nel C# codice.

 <a name="UITextField" />

## <a name="uitextfield"></a>UITextField

Il controllo `UITextField` viene usato più spesso per accettare una singola riga di input di testo, ad esempio un nome utente o una password. Di seguito sono illustrate alcune delle opzioni disponibili per la personalizzazione del controllo:

 [![](text-input-images/image15a.png "UITextField Properties")](text-input-images/image15a.png#lightbox)

Questi controlli sono illustrati di seguito:

- **Segnaposto** : si tratta di un parametro facoltativo. Se impostato, viene visualizzato quando il campo di testo è vuoto, in genere per spiegare all'utente l'input previsto.
- **Pulsante Cancella** : consente di controllare quando viene visualizzato il pulsante Cancella standard (il cerchio grigio con (X)) nel campo di testo, in modo che l'utente cancelli rapidamente il testo. Può essere nascosto, in modo permanente, visibile o visualizzato in modo permanente, a seconda che il campo venga modificato o meno.
- **Dimensioni minime del carattere** e adatta **alla** finestra: consente di adattare automaticamente le dimensioni del carattere per adattarle a un testo più lungo e impedire il troncamento, ma con un limite di dimensioni inferiori a quelle specificate.
- **Maiuscole** : indica se sfruttare automaticamente le parole, le frasi o tutti gli input.
- **Correzione** : indica se il controllo ortografico e i suggerimenti sono abilitati.
- **Tastiera** : controlla lo stile della tastiera visualizzato per l'input e quindi le chiavi disponibili sulla tastiera. Sono inclusi il tastierino numerico, il tastierino telefonico, la posta elettronica e l'URL insieme ad altre opzioni.
- **Aspetto** : controlla lo stile di aspetto della tastiera e sarà scuro o chiaro.
- **Chiave restituita** : modificare l'etichetta della chiave restituita in modo da riflettere meglio l'azione che verrà eseguita. I valori supportati includono go, join, Next, Route, done e search.
- **Secure** : indica se l'input è mascherato, ad esempio per un input di password.

Se un UITextField chiamato `textfield1` è stato aggiunto a una schermata con la finestra di progettazione, è possibile impostare o modificare le C# proprietà in come indicato di seguito:

```csharp
textfield1.Placeholder = "type email here...";
textfield1.KeyboardType = UIKeyboardType.EmailAddress;
textfield1.ReturnKeyType = UIReturnKeyType.Send;
textfield1.MinimumFontSize = 17f;
textfield1.AdjustsFontSizeToFitWidth = true;
```

Xamarin.iOS fornisce le enumerazioni laddove appropriato per facilitare la selezione delle impostazioni desiderate, ad esempio il `UIKeyboardType` e `UIReturnKeyType` nel frammento di codice precedente.

### <a name="display-text-programmatically"></a>Visualizzare il testo a livello di codice

Se non si vuole progettare la schermata con la finestra di progettazione o se si vuole aggiungere in modo dinamico un testo in fase di esecuzione, è possibile creare e visualizzare un UITextField a livello di codice nel metodo `ViewDidLoad` di un controller di visualizzazione simile al seguente:

```csharp
var frame = new CGRect(10, 10, 300, 40);
textfield1 = new UITextField(frame);
View.Add(textfield1);
```

 <a name="UITextView" />

## <a name="uitextview"></a>UITextView

Il controllo `UITextView` può essere usato per visualizzare il testo di sola lettura o per accettare l'input di testo su più righe. Dispone di molte delle stesse opzioni di `UITextField` (ad esempio, maiuscole, correzioni e così via).

 [![](text-input-images/image16a.png "UITextView Properties")](text-input-images/image16a.png#lightbox)

Le proprietà specifiche includono:

- **Comportamento** : indica se il testo è modificabile o di sola lettura.
- **Rilevamento** : rileva e converte i dati inseriti in elementi selezionabili, ad esempio numeri di telefono che possono attivare una chiamata, indirizzi che diventano collegamenti a mappe, URL aperti in Safari o date e ore che diventano eventi nel calendario.

Se un UITextView è stato aggiunto a una schermata con la finestra di progettazione, è possibile impostare o modificare le proprietà come segue:

```csharp
textview1.Text = "Lorem ipsum..."; // lots of text can go here
textview1.Editable = true;
textview1.DataDetectorTypes = UIDataDetectorType.PhoneNumber | UIDataDetectorType.Link;
```

## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
