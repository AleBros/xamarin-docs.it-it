---
title: Utilizzo del thread dell'interfaccia utente in Xamarin.iOS
description: Questo documento descrive come usare il thread dell'interfaccia utente in Xamarin.iOS. Illustra l'esecuzione del thread dell'interfaccia utente, fornisce un esempio di thread in background ed esamina async/await.
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: ee7ab7c5d0503cffd2c12a493f314f191d912e92
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002838"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>Utilizzo del thread dell'interfaccia utente in Xamarin.iOS

Le interfacce utente dell'applicazione sono sempre a thread singolo, anche nei dispositivi multithread. esiste solo una rappresentazione dello schermo e tutte le modifiche apportate agli elementi visualizzati devono essere coordinate tramite un unico punto di accesso. In questo modo si impedisce a più thread di provare ad aggiornare lo stesso pixel nello stesso momento (ad esempio).

Il codice deve apportare modifiche solo ai controlli dell'interfaccia utente dal thread principale (o interfaccia utente). Eventuali aggiornamenti dell'interfaccia utente che si verificano in un thread diverso (ad esempio un callback o un thread in background) potrebbero non essere visualizzati sullo schermo o addirittura causare un arresto anomalo del sistema.

## <a name="ui-thread-execution"></a>Esecuzione thread UI

Quando si creano controlli in una vista o si gestisce un evento avviato dall'utente, ad esempio un tocco, il codice è già in esecuzione nel contesto del thread dell'interfaccia utente.

Se il codice è in esecuzione in un thread in background, in un'attività o in un callback, è probabile che non sia in esecuzione sul thread principale dell'interfaccia utente. In questo caso è necessario eseguire il wrapping del codice in una chiamata a `InvokeOnMainThread` o `BeginInvokeOnMainThread` come segue:

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

Il metodo `InvokeOnMainThread` viene definito in `NSObject` in modo che possa essere chiamato dall'interno di metodi definiti in qualsiasi oggetto UIKit, ad esempio una vista o un controller di visualizzazione.

Durante il debug delle applicazioni Xamarin.iOS, viene generato un errore se il codice tenta di accedere a un controllo dell'interfaccia utente dal thread errato. Questo consente di rilevare e risolvere questi problemi con il metodo InvokeOnMainThread. Questo si verifica solo durante il debug e non genera un errore nelle build di rilascio. Il messaggio di errore verrà visualizzato come segue:

 ![](ui-thread-images/image10.png "UI Thread Execution")

 <a name="Background_Thread_Example" />

## <a name="background-thread-example"></a>Esempio di thread in background

Di seguito è riportato un esempio che tenta di accedere a un controllo dell'interfaccia utente (un `UILabel`) da un thread in background utilizzando un semplice thread:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

Il codice genererà il `UIKitThreadAccessException` durante il debug. Per risolvere il problema (e assicurarsi che il controllo dell'interfaccia utente sia accessibile solo dal thread principale dell'interfaccia utente), eseguire il wrapping del codice che fa riferimento ai controlli dell'interfaccia utente all'interno di un'espressione `InvokeOnMainThread` come la seguente:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

Non è necessario usarlo per il resto degli esempi in questo documento, ma è importante ricordare quando l'app esegue richieste di rete, usa il centro notifiche o altri metodi che richiedono un gestore di completamento che verrà eseguito su un altro thread.

 <a name="Async_Await_Example" />

## <a name="asyncawait-example"></a>Esempio di async/await

Quando si usa C# la 5 parole chiave async/await`InvokeOnMainThread`non è necessario perché quando un'attività attesa viene completata, il metodo continua nel thread chiamante.

Questo codice di esempio (che attende in seguito a una chiamata al metodo Delay, esclusivamente a scopo dimostrativo) Mostra un metodo asincrono chiamato sul thread UI (si tratta di un gestore TouchUpInside). Poiché il metodo contenitore viene chiamato sul thread UI, le operazioni dell'interfaccia utente come l'impostazione del testo in un `UILabel` o la visualizzazione di un `UIAlertView` possono essere chiamate in modo sicuro dopo il completamento delle operazioni asincrone sui thread in background.

```csharp
async partial void button2_TouchUpInside (UIButton sender)
{
    textfield1.ResignFirstResponder ();
    textfield2.ResignFirstResponder ();
    textview1.ResignFirstResponder ();
    label1.Text = "async method started";
    await Task.Delay(1000); // example purpose only
    label1.Text = "1 second passed";
    await Task.Delay(2000);
    label1.Text = "2 more seconds passed";
    await Task.Delay(1000);
    new UIAlertView("Async method complete", "This method", 
               null, "Cancel", null)
        .Show();
    label1.Text = "async method completed";
}
```

Se un metodo asincrono viene chiamato da un thread in background (non dal thread principale dell'interfaccia utente), `InvokeOnMainThread` sarebbero ancora necessari.

## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [Threading](~/ios/app-fundamentals/threading.md)
