---
title: Utilizzo di Input di testo
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 9dec6f754590abf6db8829f555376b423b7a7da7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-text-input"></a>Utilizzo di Input di testo

L'Apple Watch non fornisce una tastiera per gli utenti di immettere testo, ma supporta descrittivi di espressioni di controllo sono disponibili alcune alternative:

- La selezione da un elenco predefinito delle opzioni di testo,
- Siri dettatura,
- Scelta di un emoji,
- Scribble riconoscimento della grafia lettera per lettera (a cui è stato introdotto in watchOS 3).

Il simulatore non supporta attualmente la dettatura ma è comunque possibile testare l'input di testo altre opzioni del controller, ad esempio Scribble, come illustrato di seguito:

![](text-input-images/textinput-sml.png "L'opzione di scribble di test")

Per accettare l'input di testo in un'applicazione di espressioni di controllo:

1. Creare una matrice di stringhe delle opzioni predefinite.
2. Chiamare `PresentTextInputController` con la matrice, se si desidera consentire emoji oppure No e un `Action` che viene chiamato quando l'utente viene terminato.
3. Nell'azione di completamento, verificare il risultato di input e intraprendere l'azione appropriata nell'app (possibilmente impostando il valore di testo di un'etichetta).

Il seguente frammento di codice presenta tre opzioni predefinite per l'utente:

```csharp
var suggest = new string[] {"Get groceries", "Buy gas", "Post letter"};

PresentTextInputController (suggest, WatchKit.WKTextInputMode.AllowEmoji, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
    // this only works if result is a text response (Plain or AllowEmoji)
        enteredText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (enteredText);
        // do something, such as myLabel.SetText(enteredText);
    }
});
```

Il `WKTextInputMode` enumerazione dispone di tre valori:

- Normale
- AllowEmoji
- AllowAnimatedEmoji

## <a name="plain"></a>Normale

Quando è impostata la modalità semplice, l'utente può scegliere:

- Dettatura,
- Scribble, o
- da un elenco predefinito che fornisce l'applicazione.

[![](text-input-images/plain-scribble-sml.png "Dettatura, Scribble, o da un elenco che fornisce l'app")](text-input-images/plain-scribble.png#lightbox)

Il risultato viene restituito sempre come un `NSObject` che può essere convertito in un `string`.

## <a name="emoji"></a>Emoji

Esistono due tipi di emoji:

- Regular Unicode emoji
- Immagini animate

Quando l'utente sceglie un emoji Unicode, viene restituito sotto forma di stringa.

Se è selezionata un'immagine animata di emoji il `result` nel completamento gestore conterrà un `NSData` oggetto che contiene il emoji `UIImage`.

## <a name="accepting-dictation-only"></a>Accettare solo dettatura

Per richiedere all'utente direttamente la schermata di dettatura senza visualizzare suggerimenti (o l'opzione di Scribble):

- passa una matrice vuota per l'elenco di suggerimenti, e
- Set `WatchKit.WKTextInputMode.Plain`.

```csharp
PresentTextInputController (new string[0], WatchKit.WKTextInputMode.Plain, (result) => {
    // action when the "text input" is complete
    if (result != null && result.Count > 0) {
        dictatedText = result.GetItem<NSObject>(0).ToString();
        Console.WriteLine (dictatedText);
        // do something, such as myLabel.SetText(dictatedText);
    }
});
```

Quando l'utente è generale, finestra Espressioni di controllo consente di visualizzare la schermata seguente che include il testo è inteso (ad esempio "This is a un test"):

![](text-input-images/dictation.png "Quando l'utente è generale, finestra Espressioni di controllo viene visualizzato il testo come viene riconosciuto")

Una volta premono i **eseguita** verrà restituito il testo del pulsante.



## <a name="related-links"></a>Collegamenti correlati

- [Apple documento di testo ed etichette](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [Introduzione a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
