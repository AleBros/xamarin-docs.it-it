---
title: Panoramica della programmazione asincrona
description: "La versione più recente del linguaggio c# – versione 5 – introdotte due nuove parole chiave per esprimere le operazioni asincrone: async e await. Queste parole chiave consentono di scrivere codice semplice che prevede l'utilizzo di Task Parallel Library per eseguire operazioni a esecuzione prolungata (ad esempio l'accesso di rete) in un altro thread e accedere facilmente i risultati al completamento. Le versioni più recenti di xamarin. IOS e xamarin supportano async e await: questo documento vengono fornite spiegazioni e un esempio di utilizzo della nuova sintassi con Xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 3de0e09b15b704db5e67fbbee6ba9bac86f58557
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="async-support-overview"></a>Panoramica del supporto asincrono

_La versione più recente del linguaggio c# – versione 5 – introdotte due nuove parole chiave per esprimere le operazioni asincrone: async e await. Queste parole chiave consentono di scrivere codice semplice che prevede l'utilizzo di Task Parallel Library per eseguire operazioni a esecuzione prolungata (ad esempio l'accesso di rete) in un altro thread e accedere facilmente i risultati al completamento. Le versioni più recenti di xamarin. IOS e xamarin supportano async e await: questo documento vengono fornite spiegazioni e un esempio di utilizzo della nuova sintassi con Xamarin._

Supporto asincrono di Xamarin è basato su Mono 3.0 e aggiorna il profilo di API dal da una versione Mobile-friendly di Silverlight per essere una versione mobile di .NET 4.5.

## <a name="overview"></a>Panoramica

Questo documento introduce il nuovo async e await parole chiave quindi illustra alcuni semplici esempi di implementazione di metodi asincroni in xamarin. IOS e xamarin.

Per una descrizione più completa delle nuove funzionalità asincrone in c# 5 (incluso un numero elevato di esempi e i diversi scenari di utilizzo) consultare la documentazione MSDN [la programmazione asincrona con Async e Await](http://msdn.microsoft.com/en-us/library/vstudio/hh191443.aspx).

L'applicazione di esempio effettua una richiesta web asincrona semplice (senza bloccare il thread principale), quindi aggiorna l'interfaccia utente html scaricato e numero di caratteri.

 [ ![](async-images/AsyncAwait_427x368.png "L'applicazione di esempio effettua una richiesta web asincrona semplice senza bloccare il thread principale, quindi aggiorna l'interfaccia utente html scaricato e numero di caratteri")](async-images/AsyncAwait.png)

Supporto asincrono di Xamarin è basato su Mono 3.0 e aggiorna il profilo di API dal da una versione mobile di Silverlight per essere una versione mobile di .NET 4.5.

## <a name="requirements"></a>Requisiti

Funzionalità di c# 5 richiedono 3.0 Mono che è incluso in 6.4 xamarin. IOS e xamarin 4.8. Verrà richiesto di aggiornare le Mono, xamarin. IOS, xamarin e Xamarin.Mac per sfruttare i vantaggi di esso.

## <a name="using-async-amp-await"></a>Utilizzo di async &amp; await

 `async` e `await` sono nuove funzionalità del linguaggio c# che funzionano in combinazione con Task Parallel Library per semplificare la scrittura di codice a thread singolo per eseguire attività di lunga durata senza bloccare il thread principale dell'applicazione.

## <a name="async"></a>async

### <a name="declaration"></a>Dichiarazione

Il `async` (parola chiave) viene inserito in una dichiarazione di metodo (o in un'espressione lambda o un metodo anonimo) per indicare che contiene il codice che è possibile eseguire in modo asincrono, Internet Explorer. non blocca il thread del chiamante.

Un metodo contrassegnato con `async` deve contenere almeno un await espressione o istruzione. Se non `await`s sono presenti nel metodo, quindi verrà eseguita in modo sincrono (lo stesso come se vi fosse alcun `async` modificatore). Verrà inoltre creato un avviso del compilatore (ma non un errore).

### <a name="return-types"></a>Tipi restituiti

Un metodo asincrono deve restituire un `Task`, `Task<TResult>` o `void`.

Specificare il `Task` tipo restituito se il metodo non restituisce qualsiasi altro valore.

Specificare `Task<TResult>` se il metodo deve restituire un valore, in cui `TResult` è il tipo restituito (ad esempio un `int`, ad esempio).

Il `void` tipo restituito viene utilizzato principalmente per i gestori di eventi che lo richiede. Codice che chiama i metodi asincroni che restituiscono void non può `await` sul risultato.

### <a name="parameters"></a>Parametri

Impossibile dichiarare i metodi asincroni `ref` o `out` parametri.

## <a name="await"></a>await

L'operatore await può essere applicato a un'attività all'interno di un metodo contrassegnato come asincrono. , Il metodo interrompere l'esecuzione in quel punto e attendere il completamento dell'attività.

Utilizzando await non blocca il thread del chiamante, anziché il controllo viene restituito al chiamante. Ciò significa che il thread chiamante non è bloccato, in modo ad esempio l'utente thread dell'interfaccia sarebbe non bloccato durante l'attesa di un'attività.

Al completamento dell'attività, il metodo riprende l'esecuzione nel punto nel codice stesso. Ciò include la restituzione all'ambito del blocco try di un blocco try-catch-finally (se presente). Await non può essere utilizzato in un blocco catch o blocco finally.

Altre informazioni sui [await in MSDN](http://msdn.microsoft.com/en-us/library/vstudio/hh156528.aspx).

## <a name="exception-handling"></a>Gestione delle eccezioni

Eccezioni che si verificano all'interno di un metodo asincrono vengono archiviate nell'attività e generate quando l'attività è `await`ed. Queste eccezioni possono essere intercettate e gestite all'interno di un blocco try-catch.

## <a name="cancellation"></a>Annullamento

Metodi asincroni che richiedere molto tempo per completare devono supportare l'annullamento. Annullamento in genere, viene richiamato come indicato di seguito:

- Oggetto `CancellationTokenSource` viene creato l'oggetto.
- Il `CancellationTokenSource.Token` istanza viene passata a un metodo asincrono annullabile.
- La richiesta di annullamento chiamando il `CancellationTokenSource.Cancel` metodo.

Quindi, l'attività Annulla se stessa e riconosce l'annullamento.

Per ulteriori informazioni sull'annullamento, vedere [come annullare un'attività asincrona](http://msdn.microsoft.com/en-us/library/vstudio/jj155761.aspx) su MSDN.

## <a name="example"></a>Esempio

Scaricare il [esempio soluzione Xamarin](https://developer.xamarin.com/samples/mobile/AsyncAwait/) (per iOS e Android) per visualizzare un esempio funzionante di `async` e `await` App per dispositivi mobili. Nell'esempio di codice viene illustrato in dettaglio in questa sezione.

### <a name="writing-an-async-method"></a>Scrittura di un metodo asincrono

Il metodo seguente viene illustrato come codice un `async` metodo con un `await`attività ed:

```csharp
public async Task<int> DownloadHomepage()
{
    var httpClient = new HttpClient(); // Xamarin supports HttpClient!

    Task<string> contentsTask = httpClient.GetStringAsync("http://xamarin.com"); // async method!

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

Si notino questi punti:

-  La dichiarazione di metodo include il `async` (parola chiave).
-  Il tipo restituito è `Task<int>` in modo che il codice chiamante può accedere il `int` valore calcolato in questo metodo.
-  L'istruzione return è `return exampleInt;` che è un oggetto di tipo integer, il fatto che il metodo restituisce `Task<int>` fa parte di miglioramenti apportati al linguaggio.


### <a name="calling-an-async-method-1"></a>La chiamata a un metodo asincrono 1

Questo pulsante, fare clic su eventi gestore è reperibile nell'applicazione di esempio Android di chiamare il metodo illustrato in precedenza:

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

-  Il delegato anonimo presenta il prefisso della parola chiave async.
-  Il metodo asincrono DownloadHomepage restituisce un'attività <int> che viene archiviato nella variabile sizeTask.
-  Il codice è in attesa sulla variabile sizeTask.  *Questo* è il luogo in cui il metodo viene sospeso e il controllo viene restituito al codice chiamante fino al termine dell'attività asincrona nel proprio thread.
-  Esecuzione *non* sospendere quando l'attività viene creato nella prima riga del metodo, nonostante l'attività creata non esiste. La parola chiave await indica la posizione in cui l'esecuzione viene sospesa.
-  Al termine dell'attività asincrona, intResult viene impostato e l'esecuzione continua sul thread originale, dalla riga di await.


### <a name="calling-an-async-method-2"></a>La chiamata a un metodo asincrono 2

Nell'applicazione di esempio iOS l'esempio è scritto in modo leggermente diverso per illustrare un approccio alternativo. Invece di utilizzare un delegato anonimo in questo esempio dichiara un `async` gestore dell'evento che viene assegnato come un gestore eventi:

```csharp
GetButton.TouchUpInside += HandleTouchUpInside;
```

Metodo del gestore eventi viene quindi definito come illustrato di seguito:

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

-  Il metodo è contrassegnato come `async` ma restituisce `void` . In genere viene eseguita solo per i gestori eventi (in caso contrario viene restituito un `Task` o `Task<TResult>` ).
-  Il codice `await` s sul `DownloadHomepage` metodo direttamente in un'assegnazione a una variabile ( `intResult` ) a differenza dell'esempio precedente in cui è utilizzato intermedio `Task<int>` variabile per fare riferimento all'attività.  *Questo* è il percorso in cui controllo viene restituito al chiamante fino a quando non viene completato il metodo asincrono in un altro thread.
-  Quando il metodo asincrono viene completato e restituisce l'esecuzione riprende in corrispondenza di `await` che indica il risultato di tipo integer viene restituito e quindi eseguito il rendering in un widget dell'interfaccia utente.


## <a name="summary"></a>Riepilogo

Utilizzo di async e await semplifica notevolmente il codice necessario per generare operazioni a esecuzione prolungata su thread in background senza bloccare il thread principale. Anche rendono facilmente accessibili i risultati quando l'attività è stata completata.

Questo documento è assegnate una panoramica delle nuove parole chiave del linguaggio esempi per xamarin. IOS e xamarin.



## <a name="related-links"></a>Collegamenti correlati

- [AsyncAwait (esempio)](https://developer.xamarin.com/samples/mobile/AsyncAwait/)
- [I callback come il nostro generazioni passare all'istruzione](http://tirania.org/blog/archive/2013/Aug-15.html)
- [Data (iOS) (sample)](https://developer.xamarin.com/samples/monotouch/Data/)
- [HttpClient (iOS) (esempio)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
- [MapKitSearch (iOS) (sample)](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [Webinar: C# Async in iOS e Android (video)](http://xamarin.wistia.com/medias/k27mc627xz)
- [Programmazione asincrona con Async e Await (MSDN)](http://msdn.microsoft.com/en-us/library/vstudio/hh191443.aspx)
- [Ottimizzazione dell'applicazione Async (MSDN)](http://msdn.microsoft.com/en-us/library/vstudio/jj155761.aspx)
- [Await e dell'interfaccia utente e i deadlock. Oh mio Dio! (MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2011/01/13/10115163.aspx)
- [L'elaborazione delle attività quando vengono completate (MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2012/08/02/processing-tasks-as-they-complete.aspx)
- [Modello asincrono basato su attività (TAP)](http://msdn.microsoft.com/en-us/library/hh873175.aspx)
- [Modalità asincrona in c# 5 (blog Lippert) – sull'introduzione delle parole chiave](http://blogs.msdn.com/b/ericlippert/archive/2010/11/11/whither-async.aspx)
