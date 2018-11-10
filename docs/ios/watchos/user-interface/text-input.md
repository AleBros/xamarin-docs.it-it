---
title: Utilizzo di watchOS Input di testo in Xamarin
description: Questo documento descrive watchOS input di testo in Xamarin. Illustra il PresentTextInputController (metodo), scrittura manuale, testo normale, emoji e dettatura.
ms.prod: xamarin
ms.assetid: E9CDF1DE-4233-4C39-99A9-C0AA643D314D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 2092b12254008936f2c5b6a7d9dd610ff751e802
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122361"
---
# <a name="working-with-watchos-text-input-in-xamarin"></a>Utilizzo di watchOS Input di testo in Xamarin

L'Apple Watch non forniscono una tastiera per gli utenti di immettere testo, ma supporta alcune alternative di facile integrazione con espressioni di controllo:

- La selezione da un elenco predefinito delle opzioni di testo,
- Siri dettatura,
- Scelta di un emoji,
- Scribble riconoscimento della grafia lettera per lettera (introdotta in watchOS 3).

Il simulatore non supporta attualmente la dettatura ma è comunque possibile testare l'input di testo altre opzioni del controller, ad esempio Scribble, come illustrato di seguito:

![](text-input-images/textinput-sml.png "Prova l'opzione di scribble")

Per accettare l'input di testo in un'app:

1. Creare una matrice di stringhe delle opzioni predefinite.
2. Chiamare `PresentTextInputController` con la matrice, se si desidera consentire emoji oppure No e un `Action` che viene chiamato quando l'utente ha terminato.
3. Nell'azione di completamento, verificare il risultato di input ed eseguire l'azione appropriata nell'app (possibilmente impostando il valore di testo dell'etichetta).

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

Il `WKTextInputMode` enumerazione ha tre valori:

- Normale
- AllowEmoji
- AllowAnimatedEmoji

## <a name="plain"></a>Normale

Quando è impostata la modalità semplice, l'utente può scegliere:

- Dettatura,
- Scribble, o
- da un elenco pre-definito che fornisce l'applicazione.

[![](text-input-images/plain-scribble-sml.png "Dettatura, Scribble, o da un elenco pre-definito che fornisce l'app")](text-input-images/plain-scribble.png#lightbox)

Il risultato viene restituito sempre come una `NSObject` che può essere convertito in un `string`.

## <a name="emoji"></a>Emoji

Esistono due tipi di emoji:

- Emoji regolare di Unicode
- Immagini animate

Quando l'utente sceglie un emoji Unicode, viene restituito sotto forma di stringa.

Se si seleziona un emoji immagine animata la `result` nel completamento gestore conterrà un' `NSData` oggetto che contiene l'emoji `UIImage`.

## <a name="accepting-dictation-only"></a>Accetta solo la dettatura

Per rendere l'utente direttamente alla schermata di dettatura senza mostrare suggerimenti (o l'opzione di Scribble):

- passa una matrice vuota per l'elenco di suggerimenti, e
- Impostare `WatchKit.WKTextInputMode.Plain`.

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

Quando l'utente sta parlando, la schermata di espressioni di controllo viene visualizzata la schermata seguente che include il testo è inteso (ad esempio "This is a test"):

![](text-input-images/dictation.png "Quando l'utente sta parlando, la schermata di espressioni di controllo viene visualizzato il testo viene riconosciuto")

Una volta premono il verrà restituito il testo del pulsante **.**



## <a name="related-links"></a>Collegamenti correlati

- [Apple doc testo ed etichette](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/TextandLabels.html)
- [Introduzione a watchOS 3](~/ios/watchos/platform/introduction-to-watchos3/index.md)
