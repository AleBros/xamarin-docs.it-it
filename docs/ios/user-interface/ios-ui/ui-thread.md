---
title: Utilizzo di Thread dell'interfaccia utente in xamarin. IOS
description: Questo documento descrive come usare i Thread dell'interfaccia utente in xamarin. IOS. Illustra l'esecuzione dei thread dell'interfaccia utente, viene fornito un esempio di thread in background ed esamina async/await.
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 6dd55f5c4316ed8f1d4f16d9e282cc2647350518
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61245361"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>Utilizzo di Thread dell'interfaccia utente in xamarin. IOS

Interfacce utente delle applicazioni sono sempre a thread singolo, anche in dispositivi a thread multipli: è presente solo una rappresentazione della schermata e tutte le modifiche per gli elementi visualizzati devono essere coordinato tramite un unico 'punto di accesso'. Ciò impedisce che più thread sta tentando di aggiornare lo stesso pixel nello stesso momento (ad esempio).

Il codice deve verificare solo thread di modifiche ai controlli dell'interfaccia da principale utente (o dell'interfaccia utente). Gli aggiornamenti dell'interfaccia utente che si verificano su un thread diverso (ad esempio, un thread in background o callback) potrebbero non sottoposti a rendering sullo schermo o potrebbe provocare un arresto anomalo del sistema.

## <a name="ui-thread-execution"></a>Esecuzione del Thread dell'interfaccia utente

Quando si creano controlli in una vista o gestisce un evento avviato dall'utente, ad esempio un tocco, il codice è già in esecuzione nel contesto del thread dell'interfaccia utente.

Se il codice viene eseguito su un thread in background, in un'attività o un callback è probabile non in esecuzione sul thread principale dell'interfaccia utente. In questo caso è necessario includere il codice in una chiamata a `InvokeOnMainThread` o `BeginInvokeOnMainThread` simile al seguente:

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

Il `InvokeOnMainThread` è definito nel metodo `NSObject` in modo che può essere chiamato da all'interno di metodi definiti in qualsiasi oggetto UIKit (ad esempio una vista o un Controller di visualizzazione).

Durante il debug di applicazioni xamarin. IOS, verrà generato un errore se il codice tenta di accedere a un controllo dell'interfaccia utente dal thread errato. Ciò consente di tenere traccia e correggere questi problemi con il metodo InvokeOnMainThread. Questo solo si verifica durante il debug e non genera un errore nelle build di rilascio. Verrà visualizzato il messaggio di errore simile al seguente:

 ![](ui-thread-images/image10.png "Esecuzione del Thread dell'interfaccia utente")

 <a name="Background_Thread_Example" />


## <a name="background-thread-example"></a>Esempio di Thread in background

Di seguito è riportato un esempio che tenta di accedere a un controllo dell'interfaccia utente (un `UILabel`) da un thread in background mediante un semplice thread:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

Che codice genererà la `UIKitThreadAccessException` durante il debug. Per risolvere il problema e assicurarsi che il controllo dell'interfaccia utente è accessibile solo dal thread principale dell'interfaccia utente, eseguire il wrapping di qualsiasi codice che fa riferimento a controlli dell'interfaccia utente all'interno di un `InvokeOnMainThread` espressione simile alla seguente:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

È necessario non utilizzare questo per il resto degli esempi in questo documento, ma è un concetto importante da ricordare quando l'app effettua le richieste di rete, Usa il centro notifiche o altri metodi che richiedono un gestore completamento verrà eseguito in un altro thread.

 <a name="Async_Await_Example" />


## <a name="asyncawait-example"></a>Esempio di Async/Await

Quando si usa il C# 5 parole chiave async/await `InvokeOnMainThread` non è necessaria perché quando viene completata un'attività attesa il metodo continua sul thread chiamante.

Questo esempio di codice (che è in attesa su una chiamata al metodo ritardo esclusivamente per scopi dimostrativi) Mostra un metodo asincrono che viene chiamato sul thread UI (si tratta di un gestore TouchUpInside). Poiché il metodo che lo contiene viene chiamato sul thread UI, UI operazioni quali l'impostazione del testo in un `UILabel` o che mostra un `UIAlertView` può essere chiamato dopo aver completato le operazioni asincrone nei thread in background.

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

Se un metodo asincrono viene chiamato da un thread in background (non il thread principale della UI) quindi `InvokeOnMainThread` continuerebbero a essere necessari.


## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://developer.xamarin.com/samples/Controls/)
- [Threading](~/ios/app-fundamentals/threading.md)
