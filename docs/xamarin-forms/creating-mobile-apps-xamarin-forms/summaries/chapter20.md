---
title: Riepilogo del capitolo 20. / O asincrono e file
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D595862D-64FD-4C0D-B0AD-C1F440564247
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 521a8b18e74e078b9caafc6c79c7e418e1f5e08f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-20-async-and-file-io"></a>Riepilogo del capitolo 20. / O asincrono e file

 Un'interfaccia utente grafica deve rispondere agli eventi di input dell'utente in modo sequenziale. Ciò implica che tutta l'elaborazione di eventi di input dell'utente deve verificarsi in un singolo thread, spesso definita semplicemente il *thread principale* o *thread dell'interfaccia utente*.

Gli utenti si aspettano interfacce utente grafiche di rispondere. Ciò significa che un programma necessario elaborare rapidamente gli eventi di input dell'utente. Se ciò non è possibile, quindi l'elaborazione deve relegato per thread secondari di esecuzione.

Alcuni programmi di esempio in questo manuale è sono usato il [ `WebRequest` ](https://developer.xamarin.com/api/type/System.Net.WebRequest/) classe. In questa classe di [ `BeginGetReponse` ](https://developer.xamarin.com/api/member/System.Net.WebRequest.BeginGetResponse/p/System.AsyncCallback/System.Object/) metodo avvia un thread di lavoro, che chiama una funzione di callback quando è completa. Tuttavia, tale funzione di callback viene eseguito nel thread di lavoro, il programma è necessario chiamare [ `Device.BeginInvokeOnMainThread` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.BeginInvokeOnMainThread/p/System.Action/) metodo per accedere all'interfaccia utente.

Un approccio più moderno per l'elaborazione asincrona è disponibile in .NET e c#. Questa operazione implica il [ `Task` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.Task/) e [ `Task<TResult>` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.Task%3CTResult%3E/) classi e altri tipi nel [ `System.Threading` ](https://developer.xamarin.com/api/namespace/System.Threading/) e [ `System.Threading.Tasks` ](https://developer.xamarin.com/api/namespace/System.Threading.Tasks/) spazi dei nomi, nonché 5.0 c# `async` e `await` parole chiave. È questo capitolo illustra la.

## <a name="from-callbacks-to-await"></a>Dal callback in attesa di essere

La `Page` classe contiene tre metodi asincroni per visualizzare le finestre di avviso:

- [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) Restituisce un `Task` oggetto
- [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/System.String/) Restituisce un `Task<bool>` oggetto
- [`DisplayActionSheet`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet/p/System.String/System.String/System.String/System.String[]/) Restituisce un `Task<string>` oggetto

Il `Task` gli oggetti indicano che tali metodi implementano il modello asincrono basato su attività, noto come TOCCO. Questi `Task` gli oggetti vengono restituiti rapidamente dal metodo. Il `Task<T>` restituire valori costituiscono una promessa"" un valore di tipo `TResult` sarà disponibile al completamento dell'attività. Il `Task` il valore restituito è un'azione asincrona che verrà completata ma non prevede alcun valore restituito.

In questi casi, il `Task` è completa quando l'utente chiude la finestra di avviso.  

### <a name="an-alert-with-callbacks"></a>Un avviso con callback

Il [ **AlertCallbacks** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) esempio viene illustrato come gestire `Task<bool>` restituiscono gli oggetti e `Device.BeginInvokeOnMainThread` chiamate a metodi di callback.

### <a name="an-alert-with-lambdas"></a>Un avviso con le espressioni lambda

Il [ **AlertLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) esempio viene illustrato come utilizzare le funzioni lambda anonima per la gestione `Task` e `Device.BeginInvokeOnMainThread` chiamate.  

### <a name="an-alert-with-await"></a>Un avviso con await

Un approccio più semplice prevede la `async` e `await` parole chiave introdotte in c# 5. Il [ **AlertAwait** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) esempio viene illustrato l'uso.

### <a name="an-alert-with-nothing"></a>Un avviso con nothing

Se il metodo asincrono restituisce `Task` anziché `Task<TResult>`, quindi il programma non è necessario utilizzare una di queste tecniche se non è necessario sapere quando viene completata l'attività asincrona. Il [ **NothingAlert** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) esempio viene illustrata questa.

### <a name="saving-program-settings-asynchronously"></a>Salvare le impostazioni del programma in modo asincrono

Il [ **SaveProgramChanges** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) esempio illustra l'uso del [ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/) metodo `Application` per salvare le impostazioni del programma, perché queste cambiare senza si esegue l'override di `OnSleep` metodo.

### <a name="a-platform-independent-timer"></a>Un timer indipendente dalla piattaforma

È possibile utilizzare [ `Task.Delay` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.Delay/p/System.Int32/) per creare un timer indipendente dalla piattaforma. Il [ **TaskDelayClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) esempio viene illustrata questa.

## <a name="file-inputoutput"></a>File di input/output

In genere, .NET [ `System.IO` ](https://developer.xamarin.com/api/namespace/System.IO/) dello spazio dei nomi è stato l'origine del supporto dei / o file. Anche se alcuni metodi in questo spazio dei nomi supportano le operazioni asincrone, la maggior parte non. Lo spazio dei nomi supporta anche diverse chiamate di metodo semplice che eseguono funzioni dei / o file sofisticate.

### <a name="good-news-and-bad-news"></a>Buone notizie e cattive

Tutte le piattaforme supportate da archiviazione locale di xamarin. Forms supporto applicazione &mdash; spazio di archiviazione privato dell'applicazione.

Le librerie di xamarin. IOS e xamarin includono una versione di .NET che Xamarin è espressamente progettato per queste due piattaforme. Tra le classi da `System.IO` che è possibile utilizzare per eseguire i/o file con l'archiviazione locale dell'applicazione in queste due piattaforme.

Tuttavia, se esegue la ricerca per questi `System.IO` classi in una PCL xamarin. Forms, è non trovarli. Il problema è che i/o del file di Microsoft completamente rinnovata per l'API di Runtime di Windows. Non utilizzano programmi destinati a Windows 8.1, Windows Phone 8.1 e la piattaforma Windows universale `System.IO` per i/o file.

Ciò significa che è necessario utilizzare il [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) (innanzitutto descritti in [ **capitolo 9. Chiamate API specifiche della piattaforma** ](chapter09.md) per implementare i/o file.

### <a name="a-first-shot-at-cross-platform-file-io"></a>Primo vengo multipiattaforma file i/o

Il [ **TextFileTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) esempio definisce un [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) interfaccia per le implementazioni di questa interfaccia in tutte le piattaforme e i/o file. Tuttavia, le implementazioni di Windows Runtime non funzionano con i metodi in questa interfaccia perché i metodi dei / o file di Windows Runtime sono asincroni.

### <a name="accommodating-windows-runtime-file-io"></a>L'aggiunta dei / o file di Windows Runtime

Usare le classi in programmi in esecuzione in Windows Runtime il [ `Windows.Storage` ](https://msdn.microsoft.com/library/windows/apps/windows.storage.aspx) e [ `Windows.Storage.Streams` ](https://msdn.microsoft.com/library/windows/apps/windows.storage.streams.aspx) spazi dei nomi per i file i/o, inclusa l'applicazione di archiviazione locale. Poiché Microsoft determinato che qualsiasi operazione che richiede che più di 50 millisecondi devono essere asincroni per evitare di bloccare il thread dell'interfaccia utente, questi metodi dei / o di file sono prevalentemente asincroni.

Il codice che illustra questo nuovo approccio sarà in una raccolta in modo che può essere utilizzato da altre applicazioni.

## <a name="platform-specific-libraries"></a>Librerie specifiche della piattaforma

È consigliabile archiviare codice riutilizzabile in raccolte. Questo è ovviamente più difficile quando sono diverse parti di codice riutilizzabili per i sistemi operativi completamente diversi.

Il [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) soluzione viene illustrato un approccio. Questa soluzione contiene i sette progetti diversi:

- [**Xamarin.FormsBook.Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), una normale PCL xamarin. Forms
- [**Xamarin.FormsBook.Platform.iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), una libreria di classi di iOS
- [**Xamarin.FormsBook.Platform.Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), una libreria di classi di Android
- [**Xamarin.FormsBook.Platform.UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), una libreria di classi di Windows universale
- [**Xamarin.FormsBook.Platform.Windows**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Windows), una libreria di classi Portabile per Windows 8.1.
- [**Xamarin.FormsBook.Platform.WinPhone**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinPhone), una libreria di classi Portabile per Windows Phone 8.1
- [**Xamarin.FormsBook.Platform.WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), un progetto condiviso per il codice che è comune a tutte le piattaforme Windows

Tutti i progetti per piattaforma (ad eccezione di **Xamarin.FormsBook.Platform.WinRT**) dispongono di riferimenti a **Xamarin.FormsBook.Platform**. I tre progetti di Windows contengono un riferimento a **Xamarin.FormsBook.Platform.WinRT**.

Tutti i progetti contengono un valore statico `Toolkit.Init` metodo per assicurarsi che la libreria è caricata se che non fa direttamente riferimento a un progetto in una soluzione di applicazione di xamarin. Forms.

Il **Xamarin.FormsBook.Platform** contiene il nuovo progetto [ `IFileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) interfaccia. Tutti i metodi hanno ora nomi con `Async` suffissi e restituire `Task` oggetti.

Il **Xamarin.FormsBook.Platform.WinRT** progetto contiene il [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe per Windows Runtime.

Il **Xamarin.FormsBook.Platform.iOS** progetto contiene il [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) classe per iOS. Questi metodi devono ora essere asincroni. Alcuni dei metodi di utilizzare le versioni asincrone dei metodi definiti `StreamWriter` e `StreamReader`: [ `WriteAsync` ](https://developer.xamarin.com/api/member/System.IO.StreamWriter.WriteAsync/p/System.String/) e [ `ReadToEndAsync` ](https://developer.xamarin.com/api/member/System.IO.StreamReader.ReadToEndAsync()/). Altre di convertire un risultato per un `Task` utilizzando il [ `FromResult` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.FromResult%7BTResult%7D/p/TResult/) metodo.

Il **Xamarin.FormsBook.Platform.Android** progetto contiene un simile [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) classe per Android.

Il **Xamarin.FormsBook.Platform** progetto contiene anche un [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) classe che semplifica l'utilizzo del `DependencyService` oggetto.

Per usare queste librerie, una soluzione di applicazione deve includere tutti i progetti di **Xamarin.FormsBook.Platform** soluzione e tutti i progetti di applicazione deve includere un riferimento alla libreria corrispondente in  **Xamarin.FormsBook.Platform**.

Il [ **TextFileAsync** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) soluzione viene illustrato come utilizzare il **Xamarin.FormsBook.Platform** librerie. Ciascuno dei progetti dispone di una chiamata a `Toolkit.Init`. L'applicazione viene utilizzato il / o di file asincrono funzioni.

### <a name="keeping-it-in-the-background"></a>Mantenendo in background

I metodi che effettuano chiamate a più metodi asincroni &mdash; , ad esempio il `WriteFileAsync` e `ReadFileASync` metodi in Windows Runtime [ `FileHelper` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe &mdash; può essere effettuato qualche tempo più efficiente utilizzando la [ `ConfigureAwait` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task%3CTResult%3E.ConfigureAwait/p/System.Boolean/) metodo per evitare di passare al thread dell'interfaccia utente.

### <a name="dont-block-the-ui-thread"></a>Non bloccare il thread dell'interfaccia utente.

In alcuni casi può essere tentati di evitare l'utilizzo di `ContinueWith` o `await` utilizzando il [ `Result` ](https://developer.xamarin.com/api/property/System.Threading.Tasks.Task%3CTResult%3E.Result/) proprietà ai metodi. Devono essere evitato per può bloccare il thread dell'interfaccia utente o persino blocco dell'applicazione.

## <a name="your-own-awaitable-methods"></a>I propri metodi awaitable

È possibile eseguire il codice in modo asincrono da passare a uno del [ `Task.Run` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.Run/p/System.Action/) metodi. È possibile chiamare `Task.Run` all'interno di un metodo asincrono che gestisce alcuni dell'overhead.

I vari `Task.Run` modelli sono descritti di seguito.

### <a name="the-basic-mandelbrot-set"></a>Il set di base Mandelbrot

Per disegnare il Mandelbrot impostato in tempo reale, il [ **Xamarin.Forms.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria ha un [ `Complex` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) struttura simile a quello di `System.Numerics` spazio dei nomi.

Il [ **MandelbrotSet** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) esempio ha un `CalculateMandeblotAsync` metodo nel relativo file code-behind che calcola il set di Mandelbrot base bianco e nero e Usa [ `BmpMaker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs)per inserirlo in una bitmap.

### <a name="marking-progress"></a>Contrassegnare lo stato di avanzamento

Per segnalare lo stato da un metodo asincrono, è possibile creare un'istanza di un [ `Progress<T>` ](https://developer.xamarin.com/api/type/System.Progress%3CT%3E/) classe e definire il metodo asincrono per un argomento di tipo [ `IProgress<T>` ](https://developer.xamarin.com/api/type/System.IProgress%3CT%3E/). Questa funzionalità viene illustrata la [ **MandelbrotProgress** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) esempio.

### <a name="cancelling-the-job"></a>L'annullamento del processo

È anche possibile scrivere un metodo asincrono per essere annullabile. Iniziare con una classe denominata [ `CancellationTokenSource` ](https://developer.xamarin.com/api/type/System.Threading.CancellationTokenSource/). Il [ `Token` ](https://developer.xamarin.com/api/property/System.Threading.CancellationTokenSource.Token/) proprietà è un valore di tipo [ `CancellationToken` ](https://developer.xamarin.com/api/type/System.Threading.CancellationToken/). Viene passato alla funzione asincrona. Un programma chiama il [ `Cancel` ](https://developer.xamarin.com/api/member/System.Threading.CancellationTokenSource.Cancel()/) metodo `CancellationTokenSource` (in genere in risposta a un'azione dall'utente) per annullare la funzione asincrona.

Il metodo asincrono può controllare periodicamente la [ `IsCancellationRequested` ](https://developer.xamarin.com/api/property/System.Threading.CancellationToken.IsCancellationRequested/) proprietà di `CancellationToken` e se la proprietà è interrotta `true`, o semplicemente chiamare la [ `ThrowIfCancellationRequested` ](https://developer.xamarin.com/api/member/System.Threading.CancellationToken.ThrowIfCancellationRequested()/) (metodo), in qual caso il metodo termina con un [ `OperationCancelledException` ](https://developer.xamarin.com/api/type/System.OperationCanceledException/).

Il [ **MandelbrotCancellation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) illustra l'utilizzo di una funzione annullabile.

### <a name="an-mvvm-mandelbrot"></a>Un Mandelbrot MVVM

Il [ **MandelbrotXF** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) esempio ha un'interfaccia utente più estesa, ed è basato principalmente su un [ `MandelbrotModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) e [ `MandelbrotViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs)classi:

[![La schermata di Mandelbrot X F](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "Mandelbrot MVVM")

## <a name="back-to-the-web"></a>Torna al web

Il [ `WebRequest` ](https://developer.xamarin.com/api/type/System.Net.WebRequest/) classe usata nell'alcuni esempi viene utilizzato un protocollo di asincrono datato denominato modello di programmazione asincrono o APM. È possibile convertire tale classe al protocollo TAP moderno utilizzando uno del `FromAsync` metodi di [ `TaskFactory` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.TaskFactory%3CTResult%3E/) classe. Il [ **ApmToTap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) esempio viene illustrata questa.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 20 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Esempi di capitolo 20](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Uso dei file](~/xamarin-forms/app-fundamentals/files.md)
