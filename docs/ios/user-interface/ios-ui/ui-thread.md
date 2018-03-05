---
title: Utilizzo di Thread dell'interfaccia utente
ms.topic: article
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 8f348511861c21d25c3e54164bedae3baad2d4d0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-the-ui-thread"></a>Utilizzo di Thread dell'interfaccia utente

Interfacce utente delle applicazioni sono sempre a thread singolo, anche nei dispositivi a thread multipli: è presente solo una rappresentazione dello schermo e modifiche di contenuto devono essere coordinate tramite un singolo 'punto di accesso di '. Ciò impedisce che più thread tentano di aggiornare lo stesso pixel allo stesso tempo, ad esempio.

Il codice deve verificare solo thread di modifiche ai controlli dell'interfaccia da principale utente (o dell'interfaccia utente). Eventuali aggiornamenti dell'interfaccia utente che si verificano in un thread diverso (ad esempio, un thread di callback o in background) potrebbero non sottoposti a rendering sullo schermo oppure potrebbe provocare un arresto anomalo.

## <a name="ui-thread-execution"></a>Esecuzione del Thread dell'interfaccia utente

Quando si sono la creazione di controlli in una vista o la gestione di un evento avviato dall'utente, ad esempio un tocco, il codice è già in esecuzione nel contesto del thread dell'interfaccia utente.

Se il codice viene eseguito in un thread in background, in un'attività o un callback è probabile che non è in esecuzione nel thread principale dell'interfaccia utente. In questo caso è necessario includere il codice in una chiamata a `InvokeOnMainThread` o `BeginInvokeOnMainThread` come segue:

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

Il `InvokeOnMainThread` definito nel metodo `NSObject` pertanto può essere chiamato all'interno di metodi definiti per qualsiasi oggetto UIKit (ad esempio una vista o View Controller).

Durante il debug di applicazioni di xamarin. IOS, verrà generato un errore se il codice tenta di accedere a un controllo dell'interfaccia utente dal thread errato. Questo permette di tenere traccia e risolvere tali problemi con il metodo InvokeOnMainThread. Questo solo si verifica durante il debug e non genera un errore nelle build di rilascio. Verrà visualizzato il messaggio di errore simile al seguente:

 ![](ui-thread-images/image10.png "Esecuzione del Thread dell'interfaccia utente")

 <a name="Background_Thread_Example" />


## <a name="background-thread-example"></a>Esempio di Thread di background

Di seguito è riportato un esempio che tenta di accedere a un controllo dell'interfaccia utente (un `UILabel`) da un thread in background mediante un semplice thread:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

Che viene generata la `UIKitThreadAccessException` durante il debug. Per risolvere il problema e assicurarsi che il controllo dell'interfaccia utente è accessibile solo dal thread principale dell'interfaccia utente, eseguire il wrapping di qualsiasi codice che fa riferimento a controlli dell'interfaccia utente all'interno di un `InvokeOnMainThread` espressione simile alla seguente:

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

È necessario non viene utilizzato per il resto degli esempi in questo documento, ma è un concetto importante da ricordare quando l'applicazione effettua richieste di rete, utilizzare il centro notifiche o altri metodi che richiedono un gestore di completamento che verrà eseguito in un altro thread.

 <a name="Async_Await_Example" />


## <a name="asyncawait-example"></a>Esempio Async/Await

Quando si usano le parole chiave di c# 5 async/await `InvokeOnMainThread` non è necessaria perché quando viene completata un'attività di attesa il metodo continua sul thread chiamante.

Questo codice di esempio (che è in attesa su una chiamata al metodo ritardo esclusivamente a scopo dimostrativo) viene illustrato un metodo asincrono che viene chiamato sul thread UI (si tratta di un gestore TouchUpInside). Poiché il metodo che lo contiene viene chiamato sul thread dell'interfaccia utente, le operazioni dell'interfaccia utente come impostazione del testo in un `UILabel` o visualizzando un `UIAlertView` può essere chiamato dopo aver completato le operazioni asincrone nel thread in background.

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

Se un metodo asincrono viene chiamato da un thread in background (non il thread dell'interfaccia utente principale) quindi `InvokeOnMainThread` sarà comunque necessario.


## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://developer.xamarin.com/samples/Controls/)
- [Threading](~/ios/app-fundamentals/threading.md)
