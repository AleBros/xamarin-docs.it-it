---
title: Panoramica del supporto asincrono
description: Questo documento descrive la programmazione con Async e await, i concetti C# introdotti in 5 per semplificare la scrittura di codice asincrono.
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 8978dbce97948d02d520b788d024fb50f4884635
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488881"
---
# <a name="async-support-overview"></a>Panoramica del supporto asincrono

_C#5 sono state introdotte due parole chiave per semplificare la programmazione asincrona: async e await. Queste parole chiave consentono di scrivere codice semplice che usa il Task Parallel Library per eseguire operazioni a esecuzione prolungata, ad esempio l'accesso alla rete, in un altro thread e accedere facilmente ai risultati al termine del completamento. Le versioni più recenti di Xamarin.iOS e Xamarin.Android supportano Async e await. questo documento fornisce spiegazioni e un esempio di utilizzo della nuova sintassi con Xamarin._

Il supporto asincrono di Xamarin è basato sulla versione mono 3,0 e aggiorna il profilo API da una versione di Silverlight intuitiva per dispositivi mobili per essere una versione per dispositivi mobili di .NET 4,5.

## <a name="overview"></a>Panoramica di

Questo documento introduce le nuove parole chiave async e await, quindi esamina alcuni semplici esempi di implementazione di metodi asincroni in Xamarin.iOS e Xamarin.Android.

Per una descrizione più completa delle nuove funzionalità asincrone di C# 5 (inclusi molti esempi e diversi scenari di utilizzo), vedere l'articolo [programmazione asincrona](https://docs.microsoft.com/dotnet/csharp/async).

L'applicazione di esempio esegue una semplice richiesta Web asincrona (senza bloccare il thread principale), quindi aggiorna l'interfaccia utente con il numero di caratteri e HTML scaricati.

 [![](async-images/AsyncAwait_427x368.png "The sample application makes a simple asynchronous web request without blocking the main thread then updates the UI with the downloaded html and character count")](async-images/AsyncAwait.png#lightbox)

Il supporto asincrono di Xamarin è basato sulla versione mono 3,0 e aggiorna il profilo API da una versione di Silverlight intuitiva per dispositivi mobili per essere una versione per dispositivi mobili di .NET 4,5.

## <a name="requirements"></a>Requisiti di

C#5 le funzionalità richiedono mono 3,0 incluso in Xamarin.iOS 6,4 e Xamarin.Android 4,8. Verrà richiesto di aggiornare i dispositivi mono, Xamarin.iOS, Xamarin.Android e Xamarin.Mac per sfruttarne i vantaggi.

## <a name="using-async-amp-await"></a>Uso di Async &amp; await

 `async` e `await` sono nuove C# funzionalità del linguaggio che funzionano insieme al Task Parallel Library per semplificare la scrittura di codice thread per eseguire attività a esecuzione prolungata senza bloccare il thread principale dell'applicazione.

## <a name="async"></a>async

### <a name="declaration"></a>Dichiarazione

La parola chiave `async` viene inserita in una dichiarazione di metodo (oppure in un metodo lambda o anonimo) per indicare che contiene codice che può essere eseguito in modo asincrono, ad esempio. non blocca il thread del chiamante.

Un metodo contrassegnato con `async` deve contenere almeno un'espressione o un'istruzione await. Se nel metodo non è presente alcuna istruzione `await`, questa verrà eseguita in modo sincrono (come se non fosse presente alcun modificatore di `async`). Viene inoltre generato un avviso del compilatore, ma non un errore.

### <a name="return-types"></a>Tipi restituiti

Un metodo asincrono deve restituire un `Task`, `Task<TResult>` o `void`.

Specificare il tipo di `Task` restituito se il metodo non restituisce alcun altro valore.

Specificare `Task<TResult>` se il metodo deve restituire un valore, dove `TResult` è il tipo restituito, ad esempio un `int`.

Il tipo di `void` restituito viene usato principalmente per i gestori eventi che lo richiedono. Il codice che chiama metodi asincroni che restituiscono void non può `await` nel risultato.

### <a name="parameters"></a>Parametri

I metodi asincroni non possono dichiarare parametri `ref` o `out`.

## <a name="await"></a>await

L'operatore await può essere applicato a un'attività all'interno di un metodo contrassegnato come Async. Fa in modo che il metodo interrompa l'esecuzione in quel momento e attenda il completamento dell'attività.

L'uso di await non blocca il thread del chiamante, ma il controllo viene restituito al chiamante. Ciò significa che il thread chiamante non è bloccato, quindi, ad esempio, il thread dell'interfaccia utente non viene bloccato durante l'attesa di un'attività.

Al termine dell'attività, il metodo riprende l'esecuzione nello stesso punto del codice. È incluso il ritorno all'ambito try di un blocco try-catch-finally (se presente). non è possibile usare await in un blocco catch o finally.

Scopri di più su [await](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/await) in Microsoft docs.

## <a name="exception-handling"></a>Gestione delle eccezioni

Le eccezioni che si verificano all'interno di un metodo asincrono vengono archiviate nell'attività e generate quando l'attività è `await`ed. Queste eccezioni possono essere intercettate e gestite all'interno di un blocco try-catch.

## <a name="cancellation"></a>Annullamento

I metodi asincroni il cui completamento richiede molto tempo devono supportare l'annullamento. In genere, l'annullamento viene richiamato come segue:

- Viene creato un oggetto `CancellationTokenSource`.
- L'istanza `CancellationTokenSource.Token` viene passata a un metodo asincrono annullabile.
- L'annullamento viene richiesto chiamando il metodo `CancellationTokenSource.Cancel`.

L'attività viene quindi annullata e viene confermato l'annullamento.

Per altre informazioni sull'annullamento, vedere [Ottimizzazione dell'app asincrona (C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application).

## <a name="example"></a>Esempio

Scaricare la [soluzione Xamarin di esempio](https://docs.microsoft.com/samples/xamarin/mobile-samples/asyncawait/) (per iOS e Android) per vedere un esempio funzionante di `async` e `await` nelle app per dispositivi mobili. Il codice di esempio viene illustrato più dettagliatamente in questa sezione.

### <a name="writing-an-async-method"></a>Scrittura di un metodo asincrono

Il metodo seguente illustra come codificare un metodo di `async` con un'attività `await`ed:

```csharp
public async Task<int> DownloadHomepage()
{
    var httpClient = new HttpClient(); // Xamarin supports HttpClient!

    Task<string> contentsTask = httpClient.GetStringAsync("https://visualstudio.microsoft.com/xamarin"); // async method!

    // await! control returns to the caller and the task continues to run on another thread
    string contents = await contentsTask;

    ResultEditText.Text += "DownloadHomepage method continues after async call. . . . .\n";

    // After contentTask completes, you can calculate the length of the string.
    int exampleInt = contents.Length;

    ResultEditText.Text += "Downloaded the html and found out the length.\n\n\n";

    ResultEditText.Text += contents; // just dump the entire HTML

    return exampleInt; // Task<TResult> returns an object of type TResult, in this case int
}
```

Si notino i seguenti punti:

- La dichiarazione di metodo include la parola chiave `async`.
- Il tipo restituito è `Task<int>` quindi il codice chiamante può accedere al valore `int` calcolato in questo metodo.
- L'istruzione return è `return exampleInt;`, ovvero un oggetto Integer, il fatto che il metodo restituisca `Task<int>` fa parte dei miglioramenti del linguaggio.

### <a name="calling-an-async-method-1"></a>Chiamata di un metodo asincrono 1

Questo gestore dell'evento click del pulsante si trova nell'applicazione di esempio Android per chiamare il metodo descritto in precedenza:

```csharp
GetButton.Click += async (sender, e) => {

    Task<int> sizeTask = DownloadHomepage();

    ResultTextView.Text = "loading...";
    ResultEditText.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await sizeTask;

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultTextView.Text = "Length: " + intResult ;
    // "returns" void, since it's an event handler
};
```

Note:

- Il delegato anonimo ha il prefisso della parola chiave async.
- Il metodo asincrono DownloadHomepage restituisce un'attività\<int > archiviato nella variabile sizeTask.
- Il codice è in attesa sulla variabile sizeTask.  *Si tratta della* posizione in cui il metodo viene sospeso e il controllo viene restituito al codice chiamante fino al termine dell'attività asincrona nel relativo thread.
- L'esecuzione *non viene sospesa* quando l'attività viene creata nella prima riga del metodo, indipendentemente dall'attività in fase di creazione. La parola chiave await indica la posizione in cui viene sospesa l'esecuzione.
- Al termine dell'attività asincrona, intResult viene impostato e l'esecuzione continua nel thread originale dalla riga await.

### <a name="calling-an-async-method-2"></a>Chiamata a un metodo asincrono 2

Nell'applicazione di esempio iOS l'esempio è scritto in modo leggermente diverso per illustrare un approccio alternativo. Anziché usare un delegato anonimo, questo esempio dichiara un gestore eventi `async` assegnato come un normale gestore eventi:

```csharp
GetButton.TouchUpInside += HandleTouchUpInside;
```

Il metodo del gestore eventi viene quindi definito come illustrato di seguito:

```csharp
async void HandleTouchUpInside (object sender, EventArgs e)
{
    ResultLabel.Text = "loading...";
    ResultTextView.Text = "loading...\n";

    // await! control returns to the caller
    var intResult = await DownloadHomepage();

    // when the Task<int> returns, the value is available and we can display on the UI
    ResultLabel.Text = "Length: " + intResult ;
}
```

Alcuni punti importanti:

- Il metodo è contrassegnato come `async` ma restituisce `void`. Questa operazione viene in genere eseguita solo per i gestori eventi. in caso contrario, viene restituito un `Task` o `Task<TResult>`).
- La parola chiave `await` nel metodo `DownloadHomepage` viene assegnata direttamente a una variabile (`intResult`), a differenza dell'esempio precedente in cui è stata usata una variabile `Task<int>` intermedia per fare riferimento all'attività.  *Si tratta della* posizione in cui il controllo viene restituito al chiamante fino al completamento del metodo asincrono su un altro thread.
- Quando il metodo asincrono viene completato e restituisce, l'esecuzione riprende in corrispondenza del `await` che indica che il risultato Integer viene restituito e quindi sottoposto a rendering in un widget dell'interfaccia utente.

## <a name="summary"></a>Riepilogo

L'utilizzo di Async e await semplifica notevolmente il codice necessario per generare operazioni con esecuzione prolungata sui thread in background senza bloccare il thread principale. Consentono inoltre di accedere facilmente ai risultati al termine dell'attività.

In questo documento è stata fornita una panoramica delle parole chiave e degli esempi del nuovo linguaggio per Xamarin.iOS e Xamarin.Android.

## <a name="related-links"></a>Collegamenti correlati

- [AsyncAwait (esempio)](https://docs.microsoft.com/samples/xamarin/mobile-samples/asyncawait/)
- [Callback come l'istruzione Go to delle generazioni](https://tirania.org/blog/archive/2013/Aug-15.html)
- [Dati (iOS) (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/data/)
- [HttpClient (iOS) (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/httpclient/)
- [MapKitSearch (iOS) (esempio)](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [Programmazione asincrona](https://docs.microsoft.com/dotnet/csharp/async)
- [Ottimizzazione dell'applicazione Async (C#)](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/fine-tuning-your-async-application)
- [Await, interfaccia utente e deadlock. Oh mio Dio!](https://devblogs.microsoft.com/pfxteam/await-and-ui-and-deadlocks-oh-my/)
- [Elaborazione delle attività quando vengono completate)](https://devblogs.microsoft.com/pfxteam/processing-tasks-as-they-complete/)
- [Modello asincrono basato su attività (TAP)](https://msdn.microsoft.com/library/hh873175.aspx)
- [Modalità asincrona in C# 5 (Blog di Eric Lippert): informazioni sull'introduzione delle parole chiave](https://blogs.msdn.microsoft.com/ericlippert/2010/11/11/asynchrony-in-c-5-part-six-whither-async/)
