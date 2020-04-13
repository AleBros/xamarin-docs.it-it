---
title: Riassunto del capitolo 20. I/O asincrono e su file
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 20. I/O asincrono e su file'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 283273e6ee28cc5cd1a61169f38bfcd1dd1726d8
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771043"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Riassunto del capitolo 20. I/O asincrono e su file

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui Xamarin.Forms si è discostata dal materiale presentato nel libro.

 Un'interfaccia utente grafica deve rispondere agli eventi di input dell'utente in sequenza. Ciò implica che tutte le elaborazioni degli eventi di input dell'utente devono verificarsi in un singolo thread, spesso denominato *thread principale* o *thread dell'interfaccia utente*.

Gli utenti si aspettano che le interfacce utente grafiche siano reattive. Ciò significa che un programma deve elaborare rapidamente gli eventi di input dell'utente. Se ciò non è possibile, l'elaborazione deve essere relegata ai thread secondari di esecuzione.

Diversi programmi di esempio in [`WebRequest`](xref:System.Net.WebRequest) questo libro hanno utilizzato la classe. In questa [`BeginGetResponse`](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) classe il metodo avvia un thread di lavoro, che chiama una funzione di callback al termine. Tuttavia, tale funzione di callback viene eseguita [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) nel thread di lavoro, pertanto il programma deve chiamare il metodo per accedere all'interfaccia utente.

> [!NOTE]
> I programmi Xamarin.Forms devono utilizzare [`HttpClient`](xref:System.Net.Http.HttpClient) anziché [`WebRequest`](xref:System.Net.WebRequest) per accedere ai file tramite Internet. `HttpClient`supporta le operazioni asincrone.

Un approccio più moderno all'elaborazione asincrona è disponibile in .NET e in C. Questo include [`Task`](xref:System.Threading.Tasks.Task) [`Task<TResult>`](xref:System.Threading.Tasks.Task`1) le classi e e [`System.Threading`](xref:System.Threading) [`System.Threading.Tasks`](xref:System.Threading.Tasks) altri tipi negli spazi dei nomi `async` e `await` , nonché le parole chiave e di C. Questo è ciò su cui si concentra questo capitolo.

## <a name="from-callbacks-to-await"></a>Dai callback alle attese

La `Page` classe stessa contiene tre metodi asincroni per visualizzare le caselle di avviso:The class itself contains three asynchronous methods to display alert boxes:

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))restituisce `Task` un oggetto
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String))restituisce `Task<bool>` un oggetto
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[]))restituisce `Task<string>` un oggetto

Gli `Task` oggetti indicano che questi metodi implementano il modello asincrono basato su attività, noto come TAP. Questi `Task` oggetti vengono restituiti rapidamente dal metodo. I `Task<T>` valori restituiti costituiscono una "promessa" che un valore di tipo `TResult` sarà disponibile al completamento dell'attività. Il `Task` valore restituito indica un'azione asincrona che verrà completata ma senza alcun valore restituito.

In tutti questi `Task` casi, il è completo quando l'utente chiude la casella di avviso.  

### <a name="an-alert-with-callbacks"></a>Un avviso con callback

[**Nell'esempio AlertCallbacks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) viene `Task<bool>` illustrato `Device.BeginInvokeOnMainThread` come gestire gli oggetti restituiti e le chiamate utilizzando i metodi di callback.

### <a name="an-alert-with-lambdas"></a>Un avviso con espressioni lambdaAn alert with lambdas

[**Nell'esempio AlertLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) viene illustrato come `Task` utilizzare `Device.BeginInvokeOnMainThread` funzioni lambda anonime per la gestione e le chiamate.  

### <a name="an-alert-with-await"></a>Un avviso con await

Un approccio più `async` `await` semplice coinvolge le parole chiave e introdotte in C . Nell'esempio [**AlertAwait**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) viene illustrato l'utilizzo.

### <a name="an-alert-with-nothing"></a>Un avviso senza nulla

Se il metodo `Task` asincrono restituisce anziché `Task<TResult>`, non è necessario utilizzare una di queste tecniche se non è necessario sapere quando l'attività asincrona viene completata. [**Nell'esempio NothingAlert**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) viene illustrato questo.

### <a name="saving-program-settings-asynchronously"></a>Salvataggio delle impostazioni del programma in modo asincrono

[**Nell'esempio SaveProgramChanges**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) viene [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) illustrato `Application` l'utilizzo del metodo di salvataggio `OnSleep` delle impostazioni del programma quando vengono modificate senza eseguire l'override del metodo.

### <a name="a-platform-independent-timer"></a>Un timer indipendente dalla piattaforma

È possibile utilizzare [`Task.Delay`](xref:System.Threading.Tasks.Task.Delay(System.Int32)) per creare un timer indipendente dalla piattaforma. Il [**TaskDelayClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) esempio viene illustrato questo.

## <a name="file-inputoutput"></a>Input/output dei file

Tradizionalmente, lo [`System.IO`](xref:System.IO) spazio dei nomi .NET è stato l'origine del supporto di I/O di file. Anche se alcuni metodi in questo spazio dei nomi supportano operazioni asincrone, la maggior parte non lo fanno. Lo spazio dei nomi supporta inoltre diverse semplici chiamate al metodo che eseguono sofisticate funzioni di I/O di file.

### <a name="good-news-and-bad-news"></a>Buone notizie e cattive notizie

Tutte le piattaforme supportate da Xamarin.Forms supportano l'archiviazione &mdash; locale dell'applicazione privata per l'applicazione.

Le librerie Xamarin.iOS e Xamarin.Android includono una versione di .NET che Xamarin ha espressamente su misura per queste due piattaforme. Queste includono `System.IO` classi da cui è possibile usare per eseguire operazioni di I/O di file con l'archiviazione locale dell'applicazione in queste due piattaforme.

Tuttavia, se si `System.IO` cercano queste classi in una libreria di classi Xamarin.Forms, non le si troverà. Il problema è che Microsoft ha completamente rinnovato l'I/O di file per l'API di Windows Runtime. I programmi destinati a Windows 8.1, Windows Phone 8.1 `System.IO` e alla piattaforma Windows universale non vengono utilizzati per l'I/O di file.

Ciò significa che è necessario [`DependencyService`](xref:Xamarin.Forms.DependencyService) utilizzare il (illustrato per la prima volta nel [**capitolo 9. Chiamate API specifiche**](chapter09.md) della piattaforma per implementare l'I/O di file.

> [!NOTE]
> I libari di classe portabili sono stati sostituiti con le librerie [`System.IO`](xref:System.IO) .NET Standard 2.0 e .NET Standard 2.0 supporta i tipi per tutte le piattaforme Xamarin.Forms. Non è più necessario `DependencyService` utilizzare un oggetto per la maggior parte delle attività di I/O dei file. Vedere [Gestione dei file in Xamarin.Forms](~/xamarin-forms/data-cloud/data/files.md) per un approccio più moderno all'I/O dei file.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Una prima ripresa all'I/O di file multipiattaforma

L'esempio [**TextFileTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) definisce un'interfaccia per l'I/O di file e le implementazioni di questa interfaccia in tutte le piattaforme. Tuttavia, le implementazioni di Windows Runtime non funzionano con i metodi in questa interfaccia perché i metodi di I/O di file di Windows Runtime sono asincroni.

### <a name="accommodating-windows-runtime-file-io"></a>Accoricidante I/O di file di Windows Runtime

I programmi in esecuzione in [`Windows.Storage`](/uwp/api/Windows.Storage) Windows [`Windows.Storage.Streams`](/uwp/api/Windows.Storage.Streams) Runtime usano le classi negli spazi dei nomi e per l'I/O dei file, inclusa l'archiviazione locale dell'applicazione. Poiché Microsoft ha determinato che qualsiasi operazione che richiede più di 50 millisecondi deve essere asincrona per evitare di bloccare il thread dell'interfaccia utente, questi metodi di I/O di file sono per lo più asincroni.

Il codice che illustra questo nuovo approccio sarà in una libreria in modo che possa essere utilizzato da altre applicazioni.

## <a name="platform-specific-libraries"></a>Librerie specifiche della piattaforma

È vantaggioso archiviare codice riutilizzabile nelle librerie. Questo è ovviamente più difficile quando diversi pezzi del codice riutilizzabile sono per sistemi operativi completamente diversi.

La soluzione [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) illustra un approccio. Questa soluzione contiene sette diversi progetti:

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), una normale libreria Xamarin.Forms
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), una libreria di classi iOS
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), una libreria di classi Android
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), una libreria di classi Windows universale
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), un progetto condiviso per il codice comune a tutte le piattaforme Windows

Tutti i singoli progetti di piattaforma (ad eccezione di **Xamarin.FormsBook.Platform.WinRT**) conpossonovano riferimenti a **Xamarin.FormsBook.Platform**. I tre progetti Windows hanno un riferimento a **Xamarin.FormsBook.Platform.WinRT**.

Tutti i progetti `Toolkit.Init` contengono un metodo statico per garantire che la libreria venga caricata se non vi viene fatto riferimento direttamente da un progetto in una soluzione di applicazione Xamarin.Forms.

Il progetto **Xamarin.FormsBook.Platform** [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) contiene la nuova interfaccia. Tutti i metodi ora `Async` hanno nomi `Task` con suffissi e oggetti restituiti.

Il progetto **Xamarin.FormsBook.Platform.WinRT** contiene la [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe per Windows Runtime.

Il progetto Xamarin.FormsBook.Platform.iOS [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) contiene la classe per iOS.The **Xamarin.FormsBook.Platform.iOS** project contains the class for iOS. Questi metodi devono ora essere asincroni. Alcuni metodi utilizzano le versioni asincrone `StreamReader` [`WriteAsync`](xref:System.IO.StreamWriter.WriteAsync(System.String)) dei [`ReadToEndAsync`](xref:System.IO.StreamReader.ReadToEndAsync)metodi definiti in `StreamWriter` e : e . Altri convertono un `Task` risultato [`FromResult`](xref:System.Threading.Tasks.Task.FromResult*) in un oggetto utilizzando il metodo .

Il progetto **Xamarin.FormsBook.Platform.Android** [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) contiene una classe simile per Android.The Xamarin.FormsBook.Platform.Android project contains a similar class for Android.

Il progetto **Xamarin.FormsBook.Platform** [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) contiene anche una classe `DependencyService` che semplifica l'utilizzo dell'oggetto.

Per utilizzare queste librerie, una soluzione applicativa deve includere tutti i progetti nella soluzione **Xamarin.FormsBook.Platform** e ognuno dei progetti di applicazione deve avere un riferimento alla libreria corrispondente in **Xamarin.FormsBook.Platform**.

La soluzione [**TextFileAsync**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) illustra come utilizzare le librerie **Xamarin.FormsBook.Platform.The TextFileAsync** solution demonstrates how to use the Xamarin.FormsBook.Platform libraries. Ognuno dei progetti ha `Toolkit.Init`una chiamata a . L'applicazione utilizza le funzioni di I/O di file asincrone.

### <a name="keeping-it-in-the-background"></a>Tenerlo in background

I metodi nelle librerie che &mdash; effettuano `WriteFileAsync` `ReadFileASync` chiamate a più [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) &mdash; metodi asincroni, ad esempio [`ConfigureAwait`](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) i metodi e nella classe Windows Runtime possono essere resi più efficienti utilizzando il metodo per evitare di tornare al thread dell'interfaccia utente.

### <a name="dont-block-the-ui-thread"></a>Non bloccare il thread dell'interfaccia utente!

A volte si è tentati `ContinueWith` di `await` evitare [`Result`](xref:System.Threading.Tasks.Task`1.Result) l'uso di o utilizzando la proprietà sui metodi. Questo dovrebbe essere evitato perché può bloccare il thread dell'interfaccia utente o anche appendere l'applicazione.

## <a name="your-own-awaitable-methods"></a>I tuoi metodi awaitable

È possibile eseguire il codice in modo [`Task.Run`](xref:System.Threading.Tasks.Task.Run(System.Action)) asincrono passandolo a uno dei metodi. È possibile `Task.Run` chiamare all'interno di un metodo asincrono che gestisce parte dell'overhead.

I `Task.Run` vari modelli sono discussi di seguito.

### <a name="the-basic-mandelbrot-set"></a>Il set di base di Mandelbrot

Per disegnare il set di Mandelbrot in tempo reale, la [`Complex`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) libreria [**Xamarin.Forms.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) ha una struttura simile a quella nello `System.Numerics` spazio dei nomi.

L'esempio [**MandelbrotSet**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) include un `CalculateMandeblotAsync` metodo nel file code-behind che calcola l'insieme [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) di Mandelbrot in bianco e nero di base e lo utilizza per inserirlo in una bitmap.

### <a name="marking-progress"></a>Contrassegno dello stato di avanzamento

Per segnalare lo stato di avanzamento da un metodo asincrono, è possibile creare un'istanza di una [`Progress<T>`](xref:System.Progress`1) classe e definire il metodo asincrono in modo che disponga di un argomento di tipo [`IProgress<T>`](xref:System.IProgress`1). Ciò è illustrato nell'esempio [**MandelbrotProgress.This**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) is demonstrated in the MandelbrotProgress sample.

### <a name="cancelling-the-job"></a>Annullamento del processo

È anche possibile scrivere un metodo asincrono da annullare. Si inizia con [`CancellationTokenSource`](xref:System.Threading.CancellationTokenSource)una classe denominata . La [`Token`](xref:System.Threading.CancellationTokenSource.Token) proprietà è un [`CancellationToken`](xref:System.Threading.CancellationToken)valore di tipo . Viene passato alla funzione asincrona. Un programma [`Cancel`](xref:System.Threading.CancellationTokenSource.Cancel) chiama `CancellationTokenSource` il metodo di (in genere in risposta a un'azione da parte dell'utente) per annullare la funzione asincrona.

Il metodo asincrono può [`IsCancellationRequested`](xref:System.Threading.CancellationToken.IsCancellationRequested) periodicamente controllare la proprietà `CancellationToken` di e uscire se la proprietà è `true`, oppure chiamare semplicemente il [`ThrowIfCancellationRequested`](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) metodo , nel qual caso il metodo termina con un oggetto [`OperationCancelledException`](xref:System.OperationCanceledException).

[**Nell'esempio MandelbrotCancellation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) viene illustrato l'utilizzo di una funzione annullabile.

### <a name="an-mvvm-mandelbrot"></a>Un MVVM Mandelbrot

L'esempio [**MandelbrotXF**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) ha un'interfaccia utente più estesa [`MandelbrotModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) ed [`MandelbrotViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs) è basato principalmente su un e classi:

[![Triplo screenshot di Mandelbrot X F](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "MVVM Mandelbrot")

## <a name="back-to-the-web"></a>Torna sul Web

La [`WebRequest`](xref:System.Net.WebRequest) classe utilizzata in alcuni esempi utilizza un protocollo asincrono vecchio stile denominato Asynchronous Programming Model o APM. È possibile convertire tale classe nel protocollo TAP `FromAsync` moderno [`TaskFactory`](xref:System.Threading.Tasks.TaskFactory`1) utilizzando uno dei metodi della classe. [**L'esempio ApmToTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) viene illustrato questo.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 20 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Capitolo 20 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Uso dei file](~/xamarin-forms/data-cloud/data/files.md)
