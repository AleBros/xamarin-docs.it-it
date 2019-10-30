---
title: Uso dell'input di testo watchos in Novell
description: Questo documento descrive l'input di testo watchos in Novell. Viene illustrato il metodo PresentTextInputController, Scribble, testo normale, emoji e la dettatura.
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 156a31e37d14ce3e3cbe7173ae97b608e9d4c32e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032658"
---
# <a name="working-with-watchos-text-input-in-xamarin"></a>Uso dell'input di testo watchos in Novell

Il Apple Watch non fornisce una tastiera per consentire agli utenti di inserire il testo, tuttavia supporta alcune alternative intuitive:

- Selezione da un elenco predefinito di opzioni di testo
- Dettatura Siri,
- Scelta di un emoji,
- Riconoscimento della grafia lettera per lettera Scribble (introdotto in watchos 3).

Il simulatore attualmente non supporta la dettatura, ma è comunque possibile testare le altre opzioni del controller di input di testo, ad esempio Scribble, come illustrato di seguito:

![](text-input-images/textinput-sml.png "Testing the scribble option")

Per accettare l'input di testo in un'app Watch:

1. Creare una matrice di stringhe di opzioni predefinite.
2. Chiamare `PresentTextInputController` con la matrice, se consentire o meno emoji e un `Action` chiamato al termine dell'utente.
3. Nell'azione di completamento, testare il risultato di input e intraprendere l'azione appropriata nell'app (possibilmente impostando il valore di testo di un'etichetta).

Il frammento di codice seguente presenta tre opzioni predefinite per l'utente:

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

L'enumerazione `WKTextInputMode` dispone di tre valori:

- Pianura
- AllowEmoji
- AllowAnimatedEmoji

## <a name="plain"></a>Pianura

Quando viene impostata la modalità normale, l'utente può scegliere:

- Dettatura
- Scribble o
- da un elenco predefinito fornito dall'applicazione.

[![](text-input-images/plain-scribble-sml.png "Dictation, Scribble, or from a pre-defined list that the app supplies")](text-input-images/plain-scribble.png#lightbox)

Il risultato viene sempre restituito come `NSObject` di cui è possibile eseguire il cast a una `string`.

## <a name="emoji"></a>Emoji

Sono disponibili due tipi di emoji:

- Emoji Unicode normale
- Immagini animate

Quando l'utente sceglie un emoji Unicode, viene restituito sotto forma di stringa.

Se viene selezionata un'immagine animata emoji, il `result` nel gestore di completamento conterrà un oggetto `NSData` che contiene la `UIImage`emoji.

## <a name="accepting-dictation-only"></a>Accettazione solo della dettatura

Per portare l'utente direttamente alla schermata di dettatura senza visualizzare alcun suggerimento (o l'opzione Scribble):

- passare una matrice vuota per l'elenco di suggerimenti e
- imposta `WatchKit.WKTextInputMode.Plain`.

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

Quando l'utente sta parlando, nella schermata espressioni di controllo viene visualizzata la schermata seguente, che include il testo inteso (ad esempio "questo è un test"):

![](text-input-images/dictation.png "When the user is speaking, the watch screen displays the text as it is understood")

Una volta premuto il pulsante **done** , viene restituito il testo.

## <a name="related-links"></a>Collegamenti correlati

- [Documento di Apple Text and labels](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [Introduzione a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
