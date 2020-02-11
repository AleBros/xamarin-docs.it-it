---
title: Riepilogo del capitolo 20. / O asincrono e file
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: Riepilogo del capitolo 20. / O asincrono e file'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 283273e6ee28cc5cd1a61169f38bfcd1dd1726d8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771043"
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Riepilogo del capitolo 20. / O asincrono e file

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui Xamarin.Forms è diversa dal materiale presentato nel libro.

 Un'interfaccia utente grafica deve rispondere a eventi di input dell'utente in modo sequenziale. Ciò implica che l'elaborazione di eventi di input dell'utente deve essere presenti in un singolo thread, spesso denominato il *thread principale* o nella *thread dell'interfaccia utente*.

Gli utenti si aspettano interfacce utente grafiche di essere reattiva. Ciò significa che un programma deve elaborare rapidamente gli eventi di input dell'utente. Se ciò non è possibile, quindi l'elaborazione deve essere confinato al secondario thread di esecuzione.

Alcuni programmi di esempio in questo libro è sono usato il [ `WebRequest` ](xref:System.Net.WebRequest) classe. In questa classe la [ `BeginGetResponse` ](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) metodo avvia un thread di lavoro, che chiama una funzione di callback quando sarà completata. Tuttavia, tale funzione di callback viene eseguito nel thread di lavoro, in modo che il programma deve chiamare [ `Device.BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) metodo per accedere all'interfaccia utente.

> [!NOTE]
> Programmi di Xamarin.Forms devono utilizzare [ `HttpClient` ](xref:System.Net.Http.HttpClient) anziché [ `WebRequest` ](xref:System.Net.WebRequest) per accedere ai file tramite internet. `HttpClient` supporta le operazioni asincrone.

Un approccio più moderno per l'elaborazione asincrona è disponibile in .NET e c#. Ciò comporta il [ `Task` ](xref:System.Threading.Tasks.Task) e [ `Task<TResult>` ](xref:System.Threading.Tasks.Task`1) classi e altri tipi all'interno di [ `System.Threading` ](xref:System.Threading) e [ `System.Threading.Tasks` ](xref:System.Threading.Tasks) spazi dei nomi, nonché il C# 5.0 `async` e `await` parole chiave. Questo è ciò che in questo capitolo è incentrato sulla.

## <a name="from-callbacks-to-await"></a>Dal callback in attesa

Il `Page` stessa classe contiene tre metodi asincroni per visualizzare le finestre di avviso:

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) Restituisce un `Task` oggetto
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String)) Restituisce un `Task<bool>` oggetto
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])) Restituisce un `Task<string>` oggetto

Il `Task` oggetti indicano che questi metodi implementano il modello asincrono basato su attività, noto come TAP. Questi `Task` gli oggetti vengono restituiti rapidamente dal metodo. Il `Task<T>` restituire i valori costituiscono una "Promessa" che un valore di tipo `TResult` saranno disponibili al completamento dell'attività. Il `Task` valore restituito indica un'azione asincrona che verrà completata ma non prevede alcun valore restituito.

In tutti questi casi, il `Task` è completa quando l'utente chiude la finestra di avviso.  

### <a name="an-alert-with-callbacks"></a>Un avviso con callback

Il [ **AlertCallbacks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) esempio viene illustrato come gestire `Task<bool>` restituiscono oggetti e `Device.BeginInvokeOnMainThread` chiamate usando i metodi di callback.

### <a name="an-alert-with-lambdas"></a>Un avviso per le espressioni lambda

Il [ **AlertLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) esempio viene illustrato come usare le funzioni lambda anonima per la gestione degli `Task` e `Device.BeginInvokeOnMainThread` chiamate.  

### <a name="an-alert-with-await"></a>Un avviso con await

Un approccio più semplice prevede la `async` e `await` le parole chiave introdotte in c# 5. Il [ **AlertAwait** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) esempio viene illustrato l'uso.

### <a name="an-alert-with-nothing"></a>Un avviso senza elementi

Se il metodo asincrono restituisce `Task` anziché `Task<TResult>`, quindi il programma non è necessario usare una di queste tecniche se non è necessario conoscere quando viene completata l'attività asincrona. Il [ **NothingAlert** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) esempio viene illustrata questa.

### <a name="saving-program-settings-asynchronously"></a>Salvataggio delle impostazioni di programma in modo asincrono

Il [ **SaveProgramChanges** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) esempio dimostra l'uso del [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync) metodo `Application` per salvare le impostazioni del programma cambiando senza si esegue l'override di `OnSleep` (metodo).

### <a name="a-platform-independent-timer"></a>Un timer indipendente dalla piattaforma

È possibile usare [ `Task.Delay` ](xref:System.Threading.Tasks.Task.Delay(System.Int32)) per creare un timer indipendente dalla piattaforma. Il [ **TaskDelayClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) esempio viene illustrata questa.

## <a name="file-inputoutput"></a>File di input/output

In genere, .NET [ `System.IO` ](xref:System.IO) dello spazio dei nomi è stato l'origine del supporto dei / o file. Anche se alcuni metodi in questo spazio dei nomi supportano le operazioni asincrone, la maggior parte non. Lo spazio dei nomi supporta anche diverse semplici chiamate a metodi che eseguono funzioni dei / o file sofisticati.

### <a name="good-news-and-bad-news"></a>Notizie buone e notizie

Tutte le piattaforme supportate da archiviazione locale dell'applicazione il supporto di Xamarin.Forms &mdash; archiviazione privato per l'applicazione.

Le librerie di Xamarin.IOS e Xamarin.Android includono una versione di .NET che Xamarin è espressamente su misura per queste due piattaforme. Queste includono le classi da `System.IO` che è possibile usare per eseguire i/o file con l'archiviazione locale dell'applicazione in queste due piattaforme.

Tuttavia, se si cerca questi `System.IO` classi in una libreria di classi Portabile Xamarin.Forms, non sono disponibili in. Il problema è che i/o del file di Microsoft completamente rinnovata per l'API di Runtime di Windows. Non utilizzano programmi destinati a Windows 8.1, Windows Phone 8.1 e la piattaforma Windows universale `System.IO` per i/o file.

Ciò significa che è necessario usare il [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) (illustrata per prima nella [ **capitolo 9. Chiamate API specifiche della piattaforma** ](chapter09.md) per implementare i/o file.

> [!NOTE]
> Librerie di classi portabili sono stati sostituiti con le librerie .NET Standard 2.0 e supporta .NET Standard 2.0 [ `System.IO` ](xref:System.IO) tipi per tutte le piattaforme di Xamarin.Forms. Non è più necessario usare un `DependencyService` per la maggior parte delle attività dei / o file. Visualizzare [gestione File in Xamarin.Forms](~/xamarin-forms/data-cloud/data/files.md) per un approccio più moderno file i/o.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Vengo prima i/o file lo sviluppo multipiattaforma

Il [ **TextFileTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) esempio definisce un [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) interfaccia per i/o file e le implementazioni di questa interfaccia in tutte le piattaforme. Tuttavia, le implementazioni di Windows Runtime non funzionano con i metodi in questa interfaccia perché i metodi dei / o file di Windows Runtime sono asincroni.

### <a name="accommodating-windows-runtime-file-io"></a>Da tenere conto dei / o file di Windows Runtime

I programmi in esecuzione in Runtime di Windows usano classi i [ `Windows.Storage` ](/uwp/api/Windows.Storage) e [ `Windows.Storage.Streams` ](/uwp/api/Windows.Storage.Streams) gli spazi dei nomi per il file i/o, inclusa l'archiviazione locale dell'applicazione. Poiché Microsoft ha deciso che qualsiasi operazione che richiede che più di 50 millisecondi devono essere asincroni per evitare di bloccare il thread dell'interfaccia utente, questi metodi dei / o file sono prevalentemente asincroni.

Il codice che illustra questo nuovo approccio sarà in una libreria in modo che può essere utilizzato da altre applicazioni.

## <a name="platform-specific-libraries"></a>Librerie specifiche della piattaforma

È consigliabile archiviare codice riutilizzabile nelle librerie. Questo è ovviamente più difficile quando sono diverse parti del codice riutilizzabile per i sistemi operativi completamente diversi.

Il [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) soluzione illustra un approccio. Questa soluzione contiene progetti diversi sette:

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), una libreria di classi Portabile Xamarin.Forms normale
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), una libreria di classi iOS
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), una libreria di classi Android
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), una libreria di classi di Windows universale
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), un progetto condiviso per il codice che è comune a tutte le piattaforme Windows

Tutti i singoli progetti di piattaforma (ad eccezione di **Xamarin.FormsBook.Platform.WinRT**) includono riferimenti ai **Xamarin.FormsBook.Platform**. I tre progetti Windows dispone di un riferimento a **Xamarin.FormsBook.Platform.WinRT**.

Tutti i progetti contengono un valore statico `Toolkit.Init` metodo per assicurarsi che la libreria viene caricata se non si fa direttamente riferimento a un progetto in una soluzione di applicazioni Xamarin.Forms.

Il **Xamarin.FormsBook.Platform** contiene il nuovo progetto [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) interfaccia. Tutti i metodi hanno ora nomi con `Async` suffissi e restituire `Task` oggetti.

Il **Xamarin.FormsBook.Platform.WinRT** progetto contiene il [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe per il Runtime di Windows.

Il **Xamarin.FormsBook.Platform.iOS** progetto contiene il [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) classe per iOS. Questi metodi devono ora essere asincroni. Alcuni dei metodi usano le versioni asincrone dei metodi definiti nella `StreamWriter` e `StreamReader`: [ `WriteAsync` ](xref:System.IO.StreamWriter.WriteAsync(System.String)) e [ `ReadToEndAsync` ](xref:System.IO.StreamReader.ReadToEndAsync). Altre di convertire un risultato a un `Task` utilizzando il [ `FromResult` ](xref:System.Threading.Tasks.Task.FromResult*) (metodo).

Il **Xamarin.FormsBook.Platform.Android** progetto contiene una simile [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) classe per Android.

Il **Xamarin.FormsBook.Platform** progetto contiene anche una [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) classe che semplifica l'uso del `DependencyService` oggetto.

Per usare queste librerie, una soluzione di applicazioni deve includere tutti i progetti di **Xamarin.FormsBook.Platform** soluzione e ognuno dei progetti dell'applicazione deve avere un riferimento alla libreria corrispondente in  **Xamarin.FormsBook.Platform**.

Il [ **TextFileAsync** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) soluzione illustra come usare il **Xamarin.FormsBook.Platform** librerie. Ogni progetto dispone di una chiamata a `Toolkit.Init`. L'applicazione si avvale dei / o di file asincrono funzioni.

### <a name="keeping-it-in-the-background"></a>Mantenendola in background

I metodi che effettuano chiamate a più metodi asincroni &mdash; , ad esempio il `WriteFileAsync` e `ReadFileASync` metodi nel Runtime di Windows [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe &mdash; possono essere apportate in qualche modo più efficienti mediante il [ `ConfigureAwait` ](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) metodo per evitare di ritorno al thread dell'interfaccia utente.

### <a name="dont-block-the-ui-thread"></a>Non bloccare il thread dell'interfaccia utente.

In alcuni casi può essere tentati di evitare di usare `ContinueWith` o `await` tramite il [ `Result` ](xref:System.Threading.Tasks.Task`1.Result) proprietà sui metodi. Questo è opportuno evitare per può bloccare il thread UI o persino bloccare l'applicazione.

## <a name="your-own-awaitable-methods"></a>I propri metodi awaitable

È possibile eseguire codice in modo asincrono, passarlo su uno dei [ `Task.Run` ](xref:System.Threading.Tasks.Task.Run(System.Action)) metodi. È possibile chiamare `Task.Run` all'interno di un metodo asincrono che gestisce alcuni del sovraccarico.

I vari `Task.Run` modelli sono illustrati di seguito.

### <a name="the-basic-mandelbrot-set"></a>Il set di Mandelbrot base

Per disegnare il Mandelbrot impostato in tempo reale, il [ **Xamarin.Forms.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria dispone di un [ `Complex` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) struttura simile a quello del `System.Numerics` spazio dei nomi.

Il [ **MandelbrotSet** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) esempio ha un `CalculateMandeblotAsync` metodo nel relativo file code-behind che calcola il set di Mandelbrot base bianco e nero e Usa [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)inserirlo in una bitmap.

### <a name="marking-progress"></a>Contrassegnare lo stato di avanzamento

Per segnalare lo stato da un metodo asincrono, è possibile creare un'istanza di un [ `Progress<T>` ](xref:System.Progress`1) classe e definire il metodo asincrono con un argomento di tipo [ `IProgress<T>` ](xref:System.IProgress`1). Questa funzionalità viene illustrata la [ **MandelbrotProgress** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) esempio.

### <a name="cancelling-the-job"></a>Annullamento del processo

È anche possibile scrivere un metodo asincrono per essere annullabile. Iniziare con una classe denominata [ `CancellationTokenSource` ](xref:System.Threading.CancellationTokenSource). Il [ `Token` ](xref:System.Threading.CancellationTokenSource.Token) proprietà è un valore di tipo [ `CancellationToken` ](xref:System.Threading.CancellationToken). Questo viene passato alla funzione asincrona. Un programma chiama il [ `Cancel` ](xref:System.Threading.CancellationTokenSource.Cancel) metodo `CancellationTokenSource` (in genere in risposta a un'azione dall'utente) per annullare la funzione asincrona.

Il metodo asincrono può verificare periodicamente la [ `IsCancellationRequested` ](xref:System.Threading.CancellationToken.IsCancellationRequested) proprietà della `CancellationToken` e l'uscita se la proprietà è `true`, o semplicemente chiamare la [ `ThrowIfCancellationRequested` ](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) (metodo), in questo caso il metodo termina con un [ `OperationCancelledException` ](xref:System.OperationCanceledException).

Il [ **MandelbrotCancellation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) esempio dimostra l'uso di una funzione annullabile.

### <a name="an-mvvm-mandelbrot"></a>Un Mandelbrot MVVM

Il [ **MandelbrotXF** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) campione ha un'interfaccia utente più estesa e si basa principalmente su un [ `MandelbrotModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) e [ `MandelbrotViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs)classi:

[![Schermata di tripla di Mandelbrot X F](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "Mandelbrot MVVM")

## <a name="back-to-the-web"></a>Tornare al web

Il [ `WebRequest` ](xref:System.Net.WebRequest) usato in alcuni esempi di classe utilizza un vecchio protocollo asincrono denominato modello di programmazione asincrona o Application Performance Monitoring. È possibile convertire tale classe al protocollo TAP moderne usando uno dei `FromAsync` metodi nel [ `TaskFactory` ](xref:System.Threading.Tasks.TaskFactory`1) classe. Il [ **ApmToTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) esempio viene illustrata questa.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 20 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Capitolo 20 campioni](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Uso dei file](~/xamarin-forms/data-cloud/data/files.md)
