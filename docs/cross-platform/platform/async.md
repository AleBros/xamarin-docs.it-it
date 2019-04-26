---
title: Panoramica del supporto asincrono
description: Questo documento descrive la programmazione con async e await, i concetti introdotti in C# 5 per renderne più semplice scrivere codice asincrono.
ms.prod: xamarin
ms.assetid: F87BF587-AB64-4C60-84B1-184CAE36ED65
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 0a72dead1b6c001f1514f1a089df9b407eb90644
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61037303"
---
# <a name="async-support-overview"></a>Panoramica del supporto asincrono

_C#5 introdotto due parole chiave per semplificare la programmazione asincrona: async e await. Queste parole chiave consentono di scrivere codice semplice che utilizza la Task Parallel Library per l'esecuzione di operazioni a esecuzione prolungata (ad esempio l'accesso di rete) in un altro thread e accedere con facilità i risultati al completamento. Le ultime versioni di xamarin. IOS e xamarin. Android supportano async e await: questo documento vengono fornite spiegazioni e un esempio dell'uso della nuova sintassi con Xamarin._

Supporto asincrono di Xamarin è basato su Mono 3.0 e consente di aggiornare il profilo di API dal da una versione per dispositivi mobili di Silverlight di essere una versione per dispositivi mobili di .NET 4.5.

## <a name="overview"></a>Panoramica

Questo documento introduce il nuovo async e await viene illustrato alcuni semplici esempi di implementazione di metodi asincroni in xamarin. IOS e xamarin. Android le parole chiave.

Per una descrizione più completa di nuove funzionalità asincrone di C# 5 (inclusi numerosi esempi e scenari di utilizzo diversi), vedere la documentazione MSDN [programmazione asincrona con Async e Await](https://msdn.microsoft.com/library/vstudio/hh191443.aspx).

L'applicazione di esempio invia una richiesta web asincrona semplice (senza bloccare il thread principale), quindi aggiorna l'interfaccia utente con il codice html scaricato e numero di caratteri.

 [![](async-images/AsyncAwait_427x368.png "L'applicazione di esempio invia una richiesta web asincrona semplice senza bloccare il thread principale, quindi aggiorna l'interfaccia utente con il codice html scaricato e numero di caratteri")](async-images/AsyncAwait.png#lightbox)

Supporto asincrono di Xamarin è basato su Mono 3.0 e consente di aggiornare il profilo di API dal da una versione per dispositivi mobili di Silverlight di essere una versione per dispositivi mobili di .NET 4.5.

## <a name="requirements"></a>Requisiti

C#funzionalità di 5 richiedono 3.0 di Mono inclusa in 6.4 di xamarin. IOS e xamarin. Android 4.8. Verrà richiesto per eseguire l'aggiornamento di Mono, xamarin. IOS, xamarin. Android e xamarin. Mac per sfruttare i vantaggi di esso.

## <a name="using-async-amp-await"></a>Uso della funzionalità async &amp; await

 `async` e `await` Usa C# funzionalità del linguaggio che funzionano in combinazione con Task Parallel Library per semplificare la scrittura di codice a thread per eseguire attività di lunga durata senza bloccare il thread principale dell'applicazione.

## <a name="async"></a>async

### <a name="declaration"></a>Dichiarazione

Il `async` (parola chiave) viene inserito in una dichiarazione di metodo (o in un'espressione lambda o un metodo anonimo) per indicare che contiene codice che è possibile eseguire in modo asincrono, Internet Explorer. non bloccano il thread del chiamante.

Un metodo contrassegnato con `async` deve contenere almeno un espressione o istruzione await. Se nessun `await`sono presenti nel metodo verrà eseguita in modo sincrono (lo stesso come se vi fosse alcun `async` modificatore). Verrà anche creato un avviso del compilatore (ma non un errore).

### <a name="return-types"></a>Tipi restituiti

Un metodo asincrono deve restituire un `Task`, `Task<TResult>` o `void`.

Specificare il `Task` tipo restituito se il metodo non restituisce qualsiasi altro valore.

Specificare `Task<TResult>` se il metodo deve restituire un valore, dove `TResult` è il tipo restituito (ad esempio un `int`, ad esempio).

Il `void` tipo restituito viene utilizzato principalmente per i gestori di eventi che lo richiedono. Non è codice che chiama i metodi asincroni che restituiscono void `await` sul risultato.

### <a name="parameters"></a>Parametri

Non è possibile dichiarare i metodi asincroni `ref` o `out` parametri.

## <a name="await"></a>await

L'operatore await può essere applicato a un'attività all'interno di un metodo contrassegnato come asincrono. Lo induce il metodo di arrestare l'esecuzione a questo punto e attendere il completamento dell'attività.

Con await non blocca il thread del chiamante, anziché il controllo venga restituito al chiamante. Ciò significa che il thread chiamante non sia bloccato, in modo se, ad esempio l'utente thread dell'interfaccia sarebbe non bloccate in attesa di un'attività.

Al completamento dell'attività, il metodo riprende l'esecuzione nel punto nel codice stesso. Ciò include la restituzione all'ambito del blocco try di un blocco try-catch-finally (se presente). Await non può essere usato in un blocco catch o blocco finally.

Altre informazioni, vedere [await in MSDN](https://msdn.microsoft.com/library/vstudio/hh156528.aspx).

## <a name="exception-handling"></a>Gestione delle eccezioni

Le eccezioni che si verificano all'interno di un metodo asincrono vengono archiviate nell'attività e generate quando l'attività è `await`ed. Queste eccezioni possono essere rilevate e gestite all'interno di un blocco try-catch.

## <a name="cancellation"></a>Annullamento

I metodi asincroni che richiedere molto tempo per completare devono supportare l'annullamento. In genere, l'annullamento viene richiamato come indicato di seguito:

- Oggetto `CancellationTokenSource` oggetto viene creato.
- Il `CancellationTokenSource.Token` istanza viene passata a un metodo asincrono annullabile.
- È richiesto l'annullamento chiamando il `CancellationTokenSource.Cancel` (metodo).

Quindi, l'attività Annulla se stessa e invia un acknowledgement per l'annullamento.

Per altre informazioni sull'annullamento, vedere [come annullare un'attività asincrona](https://msdn.microsoft.com/library/vstudio/jj155761.aspx) su MSDN.

## <a name="example"></a>Esempio

Scaricare il [esempio di soluzione Xamarin](https://developer.xamarin.com/samples/mobile/AsyncAwait/) (per iOS e Android) per visualizzare un esempio pratico `async` e `await` nelle App per dispositivi mobili. L'esempio di codice viene discusso in maggior dettaglio in questa sezione.

### <a name="writing-an-async-method"></a>La scrittura di un metodo asincrono

Il metodo seguente viene illustrato come scrivere codice una `async` metodo con un `await`attività ed:

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

Tenere presente questi punti:

-  La dichiarazione di metodo include il `async` (parola chiave).
-  Il tipo restituito è `Task<int>` in modo che il codice chiamante può accedere il `int` valore calcolato in questo metodo.
-  L'istruzione return viene `return exampleInt;` che è un oggetto di tipo integer: il fatto che il metodo restituisce `Task<int>` fa parte dei miglioramenti del linguaggio.


### <a name="calling-an-async-method-1"></a>La chiamata a un metodo asincrono 1

Evento click del pulsante questo gestore di è reperibile nell'applicazione di Android di esempio per chiamare il metodo illustrato in precedenza:

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

-  Il delegato anonimo ha il prefisso della parola chiave async.
-  Il metodo asincrono DownloadHomepage restituisce un'attività <int> che viene archiviato nella variabile sizeTask.
-  Il codice attende sulla variabile sizeTask.  *Ciò* è la posizione che il metodo viene sospeso e il controllo viene restituito al codice chiamante fino al completamento dell'attività asincrona nel proprio thread.
-  L'esecuzione viene *non* sospendere il servizio quando l'attività viene creata nella prima riga del metodo, nonostante l'attività creata non esiste. La parola chiave await indica la posizione in cui l'esecuzione viene sospesa.
-  Al termine dell'attività asincrona, intResult viene impostato e l'esecuzione continua sul thread originale, dalla riga di await.


### <a name="calling-an-async-method-2"></a>La chiamata a un metodo asincrono 2

Nell'applicazione di esempio iOS di esempio è scritto in modo leggermente diverso per illustrare un approccio alternativo. Invece di usare un delegato anonimo in questo esempio dichiara un `async` gestore dell'evento che viene assegnato come un gestore eventi regolari:

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

-  Il metodo è contrassegnato come `async` ma restituisce `void` . In genere viene eseguita solo per i gestori eventi (in caso contrario, viene restituito un `Task` o `Task<TResult>` ).
-  Il codice `await` s nella `DownloadHomepage` metodo direttamente su un'assegnazione a una variabile ( `intResult` ) a differenza dell'esempio precedente in cui è stato usato un intermedio `Task<int>` variabile per fare riferimento all'attività.  *Ciò* è la posizione in cui controllo viene restituito al chiamante fino a quando non viene completato il metodo asincrono in un altro thread.
-  Quando il metodo asincrono viene completato e restituisce, l'esecuzione riprende in corrispondenza di `await` che indica il risultato dell'intero viene restituito e quindi eseguito il rendering in un widget dell'interfaccia utente.


## <a name="summary"></a>Riepilogo

Uso della funzionalità async e await semplifica notevolmente il codice necessario per generare operazioni a esecuzione prolungata su thread in background senza bloccare il thread principale. Inoltre semplificano le operazioni accedere ai risultati quando l'attività è stata completata.

Questo documento ha presentato una panoramica delle nuove parole chiave del linguaggio e gli esempi per xamarin. IOS e xamarin. Android.



## <a name="related-links"></a>Collegamenti correlati

- [AsyncAwait (esempio)](https://developer.xamarin.com/samples/mobile/AsyncAwait/)
- [I callback come nostro generazioni passare all'istruzione](https://tirania.org/blog/archive/2013/Aug-15.html)
- [Dati (iOS) (esempio)](https://developer.xamarin.com/samples/monotouch/Data/)
- [HttpClient (iOS) (sample)](https://developer.xamarin.com/samples/monotouch/HttpClient/)
- [MapKitSearch (iOS) (sample)](https://github.com/xamarin/monotouch-samples/tree/master/MapKitSearch)
- [Webinar: C#Async in iOS e Android (video)](http://xamarin.wistia.com/medias/k27mc627xz)
- [Programmazione asincrona con Async e Await (MSDN)](https://msdn.microsoft.com/library/vstudio/hh191443.aspx)
- [Ottimizzazione dell'applicazione Async (MSDN)](https://msdn.microsoft.com/library/vstudio/jj155761.aspx)
- [Await e l'interfaccia utente e deadlock. Oh my! (MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2011/01/13/10115163.aspx)
- [L'elaborazione delle attività quando vengono completate (MSDN)](http://blogs.msdn.com/b/pfxteam/archive/2012/08/02/processing-tasks-as-they-complete.aspx)
- [Modello asincrono basato su attività (TAP)](https://msdn.microsoft.com/library/hh873175.aspx)
- [Modalità asincrona in C# 5 (blog di Lippert) – sull'introduzione delle parole chiave](http://blogs.msdn.com/b/ericlippert/archive/2010/11/11/whither-async.aspx)
