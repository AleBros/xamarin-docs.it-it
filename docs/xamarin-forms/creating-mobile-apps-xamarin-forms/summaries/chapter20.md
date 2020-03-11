---
title: Riepilogo del capitolo 20. / O asincrono e file
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 20. / O asincrono e file'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 283273e6ee28cc5cd1a61169f38bfcd1dd1726d8
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771043"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Riepilogo del capitolo 20. / O asincrono e file

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

 Un'interfaccia utente grafica deve rispondere a eventi di input dell'utente in modo sequenziale. Ciò implica che tutte le operazioni di elaborazione degli eventi di input dell'utente devono essere eseguite in un singolo thread, spesso denominato *thread principale* o *thread dell'interfaccia utente*.

Gli utenti si aspettano interfacce utente grafiche di essere reattiva. Ciò significa che un programma deve elaborare rapidamente gli eventi di input dell'utente. Se ciò non è possibile, quindi l'elaborazione deve essere confinato al secondario thread di esecuzione.

Molti programmi di esempio in questo manuale hanno usato la classe [`WebRequest`](xref:System.Net.WebRequest) . In questa classe il metodo [`BeginGetResponse`](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) avvia un thread di lavoro, che chiama una funzione di callback al completamento. Tuttavia, tale funzione di callback viene eseguita nel thread di lavoro, quindi il programma deve chiamare [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) metodo per accedere all'interfaccia utente.

> [!NOTE]
> I programmi Novell. Forms devono usare [`HttpClient`](xref:System.Net.Http.HttpClient) anziché [`WebRequest`](xref:System.Net.WebRequest) per l'accesso ai file tramite Internet. `HttpClient` supporta le operazioni asincrone.

Un approccio più moderno per l'elaborazione asincrona è disponibile in .NET e c#. Ciò implica le classi [`Task`](xref:System.Threading.Tasks.Task) e [`Task<TResult>`](xref:System.Threading.Tasks.Task`1) e altri tipi negli spazi dei nomi [`System.Threading`](xref:System.Threading) e [`System.Threading.Tasks`](xref:System.Threading.Tasks) , nonché le C# parole chiave `async` e `await` 5,0. Questo è ciò che in questo capitolo è incentrato sulla.

## <a name="from-callbacks-to-await"></a>Dal callback in attesa

La classe `Page` contiene tre metodi asincroni per visualizzare le caselle di avviso:

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) restituisce un oggetto `Task`
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String)) restituisce un oggetto `Task<bool>`
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) restituisce un oggetto `Task<string>`

Gli oggetti `Task` indicano che questi metodi implementano il modello asincrono basato su attività, noto come TAP. Questi oggetti `Task` vengono restituiti rapidamente dal metodo. I valori restituiti `Task<T>` costituiscono una "promessa" che un valore di tipo `TResult` sarà disponibile al termine dell'attività. Il valore restituito `Task` indica un'azione asincrona che viene completata ma senza valore restituito.

In tutti questi casi, l'`Task` viene completata quando l'utente omette la casella di avviso.  

### <a name="an-alert-with-callbacks"></a>Un avviso con callback

Nell'esempio [**AlertCallbacks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) viene illustrato come gestire `Task<bool>` restituire oggetti e `Device.BeginInvokeOnMainThread` chiamate utilizzando metodi di callback.

### <a name="an-alert-with-lambdas"></a>Un avviso per le espressioni lambda

Nell'esempio [**AlertLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) viene illustrato come utilizzare le funzioni lambda anonime per gestire `Task` e `Device.BeginInvokeOnMainThread` chiamate.  

### <a name="an-alert-with-await"></a>Un avviso con await

Un approccio più semplice riguarda le parole chiave `async` e `await` introdotte C# in 5. Nell'esempio [**AlertAwait**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) viene illustrato l'utilizzo.

### <a name="an-alert-with-nothing"></a>Un avviso senza elementi

Se il metodo asincrono restituisce `Task` anziché `Task<TResult>`, il programma non deve usare nessuna di queste tecniche se non è necessario stabilire quando l'attività asincrona è stata completata. Questa operazione è illustrata nell'esempio [**NothingAlert**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) .

### <a name="saving-program-settings-asynchronously"></a>Salvataggio delle impostazioni di programma in modo asincrono

Nell'esempio [**SaveProgramChanges**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) viene illustrato l'uso del metodo [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) di `Application` per salvare le impostazioni del programma quando cambiano senza eseguire l'override del metodo `OnSleep`.

### <a name="a-platform-independent-timer"></a>Un timer indipendente dalla piattaforma

È possibile usare [`Task.Delay`](xref:System.Threading.Tasks.Task.Delay(System.Int32)) per creare un timer indipendente dalla piattaforma. Questa operazione è illustrata nell'esempio [**TaskDelayClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) .

## <a name="file-inputoutput"></a>File di input/output

Tradizionalmente, lo spazio dei nomi .NET [`System.IO`](xref:System.IO) è stato l'origine del supporto di i/O file. Anche se alcuni metodi in questo spazio dei nomi supportano le operazioni asincrone, la maggior parte non. Lo spazio dei nomi supporta anche diverse semplici chiamate a metodi che eseguono funzioni dei / o file sofisticati.

### <a name="good-news-and-bad-news"></a>Notizie buone e notizie

Tutte le piattaforme supportate da Novell. Forms supportano l'archiviazione locale dell'applicazione &mdash; archiviazione privata per l'applicazione.

Le librerie di xamarin. IOS e xamarin. Android includono una versione di .NET che Xamarin è espressamente su misura per queste due piattaforme. Sono incluse le classi di `System.IO` che è possibile usare per eseguire l'I/O di file con l'archiviazione locale dell'applicazione in queste due piattaforme.

Tuttavia, se si cerca queste classi `System.IO` in una libreria di classi portabile Novell. Forms, non saranno disponibili. Il problema è che i/o del file di Microsoft completamente rinnovata per l'API di Runtime di Windows. I programmi destinati a Windows 8.1, Windows Phone 8,1 e il piattaforma UWP (Universal Windows Platform) non utilizzano `System.IO` per l'I/O di file.

Ciò significa che sarà necessario usare il [`DependencyService`](xref:Xamarin.Forms.DependencyService) (prima illustrato nel [**capitolo 9. Chiamate API specifiche della piattaforma**](chapter09.md) per implementare l'I/O di file.

> [!NOTE]
> La classe portabile libaries è stata sostituita con .NET Standard librerie 2,0 e .NET Standard 2,0 supporta i tipi di [`System.IO`](xref:System.IO) per tutte le piattaforme Novell. Forms. Non è più necessario usare un `DependencyService` per la maggior parte delle attività di I/O di file. Per un approccio più moderno all'I/O di file, vedere [gestione dei file in Novell. Forms](~/xamarin-forms/data-cloud/data/files.md) .

### <a name="a-first-shot-at-cross-platform-file-io"></a>Vengo prima i/o file lo sviluppo multipiattaforma

L'esempio [**TextFileTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) definisce un'interfaccia [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) per l'I/O dei file e implementazioni di questa interfaccia in tutte le piattaforme. Tuttavia, le implementazioni di Windows Runtime non funzionano con i metodi in questa interfaccia perché i metodi dei / o file di Windows Runtime sono asincroni.

### <a name="accommodating-windows-runtime-file-io"></a>Da tenere conto dei / o file di Windows Runtime

I programmi in esecuzione nell'Windows Runtime utilizzano le classi negli spazi dei nomi [`Windows.Storage`](/uwp/api/Windows.Storage) e [`Windows.Storage.Streams`](/uwp/api/Windows.Storage.Streams) per l'I/O di file, inclusa l'archiviazione locale dell'applicazione. Poiché Microsoft ha deciso che qualsiasi operazione che richiede che più di 50 millisecondi devono essere asincroni per evitare di bloccare il thread dell'interfaccia utente, questi metodi dei / o file sono prevalentemente asincroni.

Il codice che illustra questo nuovo approccio sarà in una libreria in modo che può essere utilizzato da altre applicazioni.

## <a name="platform-specific-libraries"></a>Librerie specifiche della piattaforma

È consigliabile archiviare codice riutilizzabile nelle librerie. Questo è ovviamente più difficile quando sono diverse parti del codice riutilizzabile per i sistemi operativi completamente diversi.

La soluzione [**Novell. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) illustra un approccio. Questa soluzione contiene progetti diversi sette:

- [**Novell. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), un normale PCL Novell. Forms
- [**Novell. FormsBook. Platform. iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), una libreria di classi iOS
- [**Novell. FormsBook. Platform. Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), una libreria di classi Android
- [**Novell. FormsBook. Platform. UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), una libreria di classi di Windows universale
- [**Novell. FormsBook. Platform. WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), un progetto condiviso per il codice comune a tutte le piattaforme Windows

Tutti i singoli progetti di piattaforma (ad eccezione di **Novell. FormsBook. Platform. WinRT**) contengono riferimenti a **Novell. FormsBook. Platform**. I tre progetti Windows hanno un riferimento a **Novell. FormsBook. Platform. WinRT**.

Tutti i progetti contengono un metodo di `Toolkit.Init` statico per garantire che la libreria venga caricata se non vi viene fatto riferimento direttamente da un progetto in una soluzione Novell. Forms Application.

Il progetto **Novell. FormsBook. Platform** contiene la nuova interfaccia [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) . Tutti i metodi ora hanno nomi con suffissi `Async` e restituiscono `Task` oggetti.

Il progetto **Novell. FormsBook. Platform. WinRT** contiene la classe [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) per l'Windows Runtime.

Il progetto **Novell. FormsBook. Platform. iOS** contiene la classe [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) per iOS. Questi metodi devono ora essere asincroni. Alcuni dei metodi utilizzano le versioni asincrone dei metodi definiti in `StreamWriter` e `StreamReader`: [`WriteAsync`](xref:System.IO.StreamWriter.WriteAsync(System.String)) e [`ReadToEndAsync`](xref:System.IO.StreamReader.ReadToEndAsync). Altri convertono un risultato in un oggetto `Task` usando il metodo [`FromResult`](xref:System.Threading.Tasks.Task.FromResult*) .

Il progetto **Novell. FormsBook. Platform. Android** contiene una classe [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) simile per Android.

Il progetto **Novell. FormsBook. Platform** contiene anche una classe [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) che semplifica l'uso dell'oggetto `DependencyService`.

Per usare queste librerie, una soluzione di applicazione deve includere tutti i progetti nella soluzione **Novell. FormsBook. Platform** e ogni progetto di applicazione deve avere un riferimento alla libreria corrispondente in **Novell. FormsBook. Platform**.

La soluzione [**TextFileAsync**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) illustra come usare le librerie **Novell. FormsBook. Platform** . Ogni progetto ha una chiamata a `Toolkit.Init`. L'applicazione si avvale dei / o di file asincrono funzioni.

### <a name="keeping-it-in-the-background"></a>Mantenendola in background

I metodi nelle librerie che effettuano chiamate a più metodi asincroni &mdash; ad esempio i metodi `WriteFileAsync` e `ReadFileASync` nel Windows Runtime [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe &mdash; possono essere resi leggermente più efficienti usando il metodo [`ConfigureAwait`](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) per evitare il ritorno al thread dell'interfaccia utente.

### <a name="dont-block-the-ui-thread"></a>Non bloccare il thread dell'interfaccia utente.

Talvolta è possibile evitare l'uso di `ContinueWith` o `await` usando la proprietà [`Result`](xref:System.Threading.Tasks.Task`1.Result) nei metodi. Questo è opportuno evitare per può bloccare il thread UI o persino bloccare l'applicazione.

## <a name="your-own-awaitable-methods"></a>I propri metodi awaitable

È possibile eseguire il codice in modo asincrono passandolo a uno dei metodi [`Task.Run`](xref:System.Threading.Tasks.Task.Run(System.Action)) . È possibile chiamare `Task.Run` all'interno di un metodo asincrono che gestisce parte del sovraccarico.

Di seguito sono illustrati i diversi modelli di `Task.Run`.

### <a name="the-basic-mandelbrot-set"></a>Il set di Mandelbrot base

Per creare il set di Mandelbrot in tempo reale, la libreria [**Novell. Forms. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) ha una struttura [`Complex`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) simile a quella nello spazio dei nomi `System.Numerics`.

L'esempio [**MandelbrotSet**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) include un metodo `CalculateMandeblotAsync` nel file code-behind che calcola il set di base di Mandelbrot bianco e nero e USA [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) per inserirlo in una bitmap.

### <a name="marking-progress"></a>Contrassegnare lo stato di avanzamento

Per segnalare lo stato di avanzamento da un metodo asincrono, è possibile creare un'istanza di una classe [`Progress<T>`](xref:System.Progress`1) e definire il metodo asincrono per avere un argomento di tipo [`IProgress<T>`](xref:System.IProgress`1). Questa operazione è illustrata nell'esempio [**MandelbrotProgress**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) .

### <a name="cancelling-the-job"></a>Annullamento del processo

È anche possibile scrivere un metodo asincrono per essere annullabile. Si inizia con una classe denominata [`CancellationTokenSource`](xref:System.Threading.CancellationTokenSource). La proprietà [`Token`](xref:System.Threading.CancellationTokenSource.Token) è un valore di tipo [`CancellationToken`](xref:System.Threading.CancellationToken). Questo viene passato alla funzione asincrona. Un programma chiama il metodo [`Cancel`](xref:System.Threading.CancellationTokenSource.Cancel) di `CancellationTokenSource` (in genere in risposta a un'azione da parte dell'utente) per annullare la funzione asincrona.

Il metodo asincrono può controllare periodicamente la proprietà [`IsCancellationRequested`](xref:System.Threading.CancellationToken.IsCancellationRequested) di `CancellationToken` e uscire se la proprietà è `true`o semplicemente chiamare il metodo [`ThrowIfCancellationRequested`](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) , nel qual caso il metodo termina con un [`OperationCancelledException`](xref:System.OperationCanceledException).

Nell'esempio [**MandelbrotCancellation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) viene illustrato l'utilizzo di una funzione annullabile.

### <a name="an-mvvm-mandelbrot"></a>Un Mandelbrot MVVM

L'esempio [**MandelbrotXF**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) dispone di un'interfaccia utente più estesa ed è basata principalmente su una [`MandelbrotModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) e [`MandelbrotViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs) classi:

[![Screenshot tripla di Mandelbrot X F](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "MVVM Mandelbrot")

## <a name="back-to-the-web"></a>Tornare al web

La classe [`WebRequest`](xref:System.Net.WebRequest) utilizzata in alcuni esempi utilizza un protocollo asincrono obsoleto denominato modello di programmazione asincrono o APM. È possibile convertire tale classe nel protocollo TAP moderno usando uno dei metodi `FromAsync` nella classe [`TaskFactory`](xref:System.Threading.Tasks.TaskFactory`1) . Questa operazione è illustrata nell'esempio [**ApmToTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) .

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 20 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Capitolo 20 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Uso dei file](~/xamarin-forms/data-cloud/data/files.md)
