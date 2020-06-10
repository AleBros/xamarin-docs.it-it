---
Titolo: "Riepilogo del capitolo 20. Async and file I/O "Descrizione:" creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 20. I/O asincrono e file "ms. prod: Novell MS. Technology: Novell-Forms ms. AssetID: D595862D-64FD-4C0D-B0AD-C1F440564247 Author: davidbritch ms. Author: dabritch ms. Date: 07/18/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-20-async-and-file-io"></a>Riepilogo del capitolo 20. I/O asincrono e su file

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)

> [!NOTE] 
> Le note in questa pagina indicano Xamarin.Forms le aree in cui è stato divergente rispetto al materiale presentato nel libro.

 Un'interfaccia utente grafica deve rispondere in sequenza agli eventi di input dell'utente. Ciò implica che tutte le operazioni di elaborazione degli eventi di input dell'utente devono essere eseguite in un singolo thread, spesso denominato *thread principale* o *thread dell'interfaccia utente*.

Gli utenti si aspettano che le interfacce utente grafiche siano reattive. Questo significa che un programma deve elaborare rapidamente gli eventi di input utente. Se ciò non è possibile, l'elaborazione deve essere relegata ai thread secondari di esecuzione.

Molti programmi di esempio in questo libro hanno usato la [`WebRequest`](xref:System.Net.WebRequest) classe. In questa classe il [`BeginGetResponse`](xref:System.Net.WebRequest.BeginGetResponse(System.AsyncCallback,System.Object)) metodo avvia un thread di lavoro, che chiama una funzione di callback al completamento. Tuttavia, tale funzione di callback viene eseguita nel thread di lavoro, quindi il programma deve chiamare il [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) metodo per accedere all'interfaccia utente.

> [!NOTE]
> Xamarin.Formsi programmi devono usare [`HttpClient`](xref:System.Net.Http.HttpClient) anziché [`WebRequest`](xref:System.Net.WebRequest) per accedere ai file tramite Internet. `HttpClient`supporta le operazioni asincrone.

Un approccio più moderno all'elaborazione asincrona è disponibile in .NET e C#. Ciò comporta le [`Task`](xref:System.Threading.Tasks.Task) classi e e [`Task<TResult>`](xref:System.Threading.Tasks.Task`1) altri tipi negli [`System.Threading`](xref:System.Threading) [`System.Threading.Tasks`](xref:System.Threading.Tasks) spazi dei nomi e, nonché le `async` parole chiave C# 5,0 e `await` . Questo è lo scopo di questo capitolo.

## <a name="from-callbacks-to-await"></a>Da callback a await

La `Page` classe stessa contiene tre metodi asincroni per visualizzare le caselle di avviso:

- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))Restituisce un `Task` oggetto
- [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String,System.String))Restituisce un `Task<bool>` oggetto
- [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[]))Restituisce un `Task<string>` oggetto

Gli `Task` oggetti indicano che questi metodi implementano il modello asincrono basato su attività, noto come tap. Questi `Task` oggetti vengono restituiti rapidamente dal metodo. I `Task<T>` valori restituiti costituiscono una "promessa" che un valore di tipo `TResult` sarà disponibile al termine dell'attività. Il `Task` valore restituito indica un'azione asincrona che viene completata ma senza valore restituito.

In tutti questi casi, l'oggetto `Task` è completo quando l'utente omette la casella di avviso.  

### <a name="an-alert-with-callbacks"></a>Un avviso con callback

Nell'esempio [**AlertCallbacks**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertCallbacks) viene illustrato come gestire `Task<bool>` gli oggetti restituiti e le `Device.BeginInvokeOnMainThread` chiamate utilizzando metodi di callback.

### <a name="an-alert-with-lambdas"></a>Avviso con espressioni lambda

Nell'esempio [**AlertLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertLambdas) viene illustrato come utilizzare le funzioni lambda anonime per la gestione `Task` e le `Device.BeginInvokeOnMainThread` chiamate.  

### <a name="an-alert-with-await"></a>Avviso con await

Un approccio più semplice riguarda le `async` `await` parole chiave e introdotte in C# 5. Nell'esempio [**AlertAwait**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/AlertAwait) viene illustrato l'utilizzo.

### <a name="an-alert-with-nothing"></a>Un avviso senza alcun valore

Se il metodo asincrono restituisce `Task` anziché `Task<TResult>` , il programma non deve usare nessuna di queste tecniche se non è necessario stabilire quando l'attività asincrona è stata completata. Questa operazione è illustrata nell'esempio [**NothingAlert**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/NothingAlert) .

### <a name="saving-program-settings-asynchronously"></a>Salvataggio asincrono delle impostazioni del programma

Nell'esempio [**SaveProgramChanges**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/SaveProgramSettings) viene illustrato l'utilizzo del [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) metodo di `Application` per salvare le impostazioni del programma quando vengono modificate senza eseguire l'override del `OnSleep` metodo.

### <a name="a-platform-independent-timer"></a>Timer indipendente dalla piattaforma

È possibile usare [`Task.Delay`](xref:System.Threading.Tasks.Task.Delay(System.Int32)) per creare un timer indipendente dalla piattaforma. Questa operazione è illustrata nell'esempio [**TaskDelayClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TaskDelayClock) .

## <a name="file-inputoutput"></a>Input/output di file

Tradizionalmente, lo [`System.IO`](xref:System.IO) spazio dei nomi .NET è stato l'origine del supporto di i/O dei file. Sebbene alcuni metodi in questo spazio dei nomi supportino le operazioni asincrone, la maggior parte non lo sono. Lo spazio dei nomi supporta inoltre diverse semplici chiamate al metodo che eseguono complesse funzioni di I/O di file.

### <a name="good-news-and-bad-news"></a>Ottime notizie e notizie negative

Tutte le piattaforme supportate da Xamarin.Forms supportano l'archiviazione locale dell'applicazione &mdash; , che è privata per l'applicazione.

Le librerie Novell. iOS e Novell. Android includono una versione di .NET che Novell è stata adattata espressamente per queste due piattaforme. Sono incluse le classi da `System.IO` che è possibile usare per eseguire l'I/O di file con l'archiviazione locale dell'applicazione in queste due piattaforme.

Tuttavia, se si cerca queste `System.IO` classi in una libreria di classi portabile Xamarin.Forms , queste non saranno disponibili. Il problema è che Microsoft ha rinnovato completamente l'I/O dei file per l'API Windows Runtime. I programmi destinati a Windows 8.1, Windows Phone 8,1 e il piattaforma UWP (Universal Windows Platform) non vengono usati `System.IO` per l'i/O di file.

Ciò significa che è necessario usare il [`DependencyService`](xref:Xamarin.Forms.DependencyService) (prima illustrato nel [**capitolo 9. Chiamate API specifiche della piattaforma**](chapter09.md) per implementare l'I/O di file.

> [!NOTE]
> La classe portabile libaries è stata sostituita con .NET Standard librerie 2,0 e .NET Standard 2,0 supporta [`System.IO`](xref:System.IO) i tipi per tutte le Xamarin.Forms piattaforme. Non è più necessario usare un `DependencyService` per la maggior parte delle attività di I/O di file. Per un approccio più moderno all'I/O di file, vedere [gestione dei file in Xamarin.Forms ](~/xamarin-forms/data-cloud/data/files.md) .

### <a name="a-first-shot-at-cross-platform-file-io"></a>Primo tentativo di I/O di file multipiattaforma

L'esempio [**TextFileTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileTryout) definisce un' [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/TextFileTryout/TextFileTryout/TextFileTryout/IFileHelper.cs) interfaccia per l'I/O dei file e implementazioni di questa interfaccia in tutte le piattaforme. Tuttavia, le implementazioni di Windows Runtime non funzionano con i metodi in questa interfaccia perché i metodi i/O del file di Windows Runtime sono asincroni.

### <a name="accommodating-windows-runtime-file-io"></a>Windows Runtime l'I/O di file

I programmi in esecuzione nell'Windows Runtime utilizzano le classi [`Windows.Storage`](/uwp/api/Windows.Storage) negli [`Windows.Storage.Streams`](/uwp/api/Windows.Storage.Streams) spazi dei nomi e per l'i/O di file, inclusa l'archiviazione locale dell'applicazione. Poiché Microsoft ha determinato che qualsiasi operazione che richiede più di 50 millisecondi deve essere asincrona per evitare il blocco del thread dell'interfaccia utente, questi metodi di I/O di file sono principalmente asincroni.

Il codice che dimostra questo nuovo approccio sarà in una libreria in modo che possa essere usato da altre applicazioni.

## <a name="platform-specific-libraries"></a>Librerie specifiche della piattaforma

È vantaggioso archiviare codice riutilizzabile nelle librerie. Questo è ovviamente più difficile quando parti diverse del codice riutilizzabile sono destinate a sistemi operativi completamente diversi.

La soluzione [**Novell. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) illustra un approccio. Questa soluzione contiene sette progetti diversi:

- [**Novell. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform), una libreria Xamarin.Forms PCL normale
- [**Novell. FormsBook. Platform. iOS**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS), una libreria di classi iOS
- [**Novell. FormsBook. Platform. Android**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android), una libreria di classi Android
- [**Novell. FormsBook. Platform. UWP**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.UWP), una libreria di classi di Windows universale
- [**Novell. FormsBook. Platform. WinRT**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT), un progetto condiviso per il codice comune a tutte le piattaforme Windows

Tutti i singoli progetti di piattaforma (ad eccezione di **Novell. FormsBook. Platform. WinRT**) contengono riferimenti a **Novell. FormsBook. Platform**. I tre progetti Windows hanno un riferimento a **Novell. FormsBook. Platform. WinRT**.

Tutti i progetti contengono un `Toolkit.Init` metodo statico per garantire che la libreria venga caricata se non vi viene fatto riferimento direttamente da un progetto in una Xamarin.Forms soluzione di applicazione.

Il progetto **Novell. FormsBook. Platform** contiene la nuova [`IFileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/IFileHelper.cs) interfaccia. Tutti i metodi ora hanno nomi con `Async` suffissi e `Task` oggetti restituiti.

Il progetto **Novell. FormsBook. Platform. WinRT** contiene la [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe per la Windows Runtime.

Il progetto **Novell. FormsBook. Platform. iOS** contiene la [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/FileHelper.cs) classe per iOS. Questi metodi devono ora essere asincroni. Alcuni dei metodi utilizzano le versioni asincrone dei metodi definiti in `StreamWriter` e `StreamReader` : [`WriteAsync`](xref:System.IO.StreamWriter.WriteAsync(System.String)) e [`ReadToEndAsync`](xref:System.IO.StreamReader.ReadToEndAsync) . Altri convertono un risultato in un `Task` oggetto usando il [`FromResult`](xref:System.Threading.Tasks.Task.FromResult*) metodo.

Il progetto **Novell. FormsBook. Platform. Android** contiene una [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/FileHelper.cs) classe simile per Android.

Il progetto **Novell. FormsBook. Platform** contiene anche una [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/FileHelper.cs) classe che semplifica l'uso dell' `DependencyService` oggetto.

Per usare queste librerie, una soluzione di applicazione deve includere tutti i progetti nella soluzione **Novell. FormsBook. Platform** e ogni progetto di applicazione deve avere un riferimento alla libreria corrispondente in **Novell. FormsBook. Platform**.

La soluzione [**TextFileAsync**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/TextFileAsync) illustra come usare le librerie **Novell. FormsBook. Platform** . Ogni progetto ha una chiamata a `Toolkit.Init` . L'applicazione utilizza le funzioni di I/O di file asincrone.

### <a name="keeping-it-in-the-background"></a>Conservazione in background

I metodi nelle librerie che eseguono chiamate a più metodi asincroni &mdash; , ad esempio i `WriteFileAsync` `ReadFileASync` metodi e nella [`FileHelper`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/FileHelper.cs) classe Windows Runtime &mdash; possono essere resi più efficienti usando il [`ConfigureAwait`](xref:System.Threading.Tasks.Task`1.ConfigureAwait(System.Boolean)) metodo per evitare il ritorno al thread dell'interfaccia utente.

### <a name="dont-block-the-ui-thread"></a>Non bloccare il thread dell'interfaccia utente.

Talvolta è possibile evitare l'uso di `ContinueWith` o `await` usando la [`Result`](xref:System.Threading.Tasks.Task`1.Result) proprietà sui metodi. Questa operazione deve essere evitata perché può bloccare il thread dell'interfaccia utente o persino appendere l'applicazione.

## <a name="your-own-awaitable-methods"></a>Metodi awaitable personali

È possibile eseguire il codice in modo asincrono passandolo a uno dei [`Task.Run`](xref:System.Threading.Tasks.Task.Run(System.Action)) metodi. È possibile chiamare `Task.Run` all'interno di un metodo asincrono che gestisce parte del sovraccarico.

I vari `Task.Run` modelli sono illustrati di seguito.

### <a name="the-basic-mandelbrot-set"></a>Set Mandelbrot di base

Per creare il set di Mandelbrot in tempo reale, il [** Xamarin.Forms . **](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)La libreria Toolkit ha una [`Complex`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/Complex.cs) struttura simile a quella nello `System.Numerics` spazio dei nomi.

L'esempio [**MandelbrotSet**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotSet) include un `CalculateMandeblotAsync` metodo nel file code-behind che calcola il set di base di Mandelbrot in bianco e nero e USA [`BmpMaker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BmpMaker.cs) per inserirlo in una bitmap.

### <a name="marking-progress"></a>Contrassegno dello stato

Per segnalare lo stato di avanzamento da un metodo asincrono, è possibile creare un'istanza di una [`Progress<T>`](xref:System.Progress`1) classe e definire il metodo asincrono per avere un argomento di tipo [`IProgress<T>`](xref:System.IProgress`1) . Questa operazione è illustrata nell'esempio [**MandelbrotProgress**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotProgress) .

### <a name="cancelling-the-job"></a>Annullamento del processo

È anche possibile scrivere un metodo asincrono per essere annullabile. Si inizia con una classe denominata [`CancellationTokenSource`](xref:System.Threading.CancellationTokenSource) . La [`Token`](xref:System.Threading.CancellationTokenSource.Token) proprietà è un valore di tipo [`CancellationToken`](xref:System.Threading.CancellationToken) . Viene passato alla funzione asincrona. Un programma chiama il [`Cancel`](xref:System.Threading.CancellationTokenSource.Cancel) metodo di `CancellationTokenSource` (in genere in risposta a un'azione da parte dell'utente) per annullare la funzione asincrona.

Il metodo asincrono può verificare periodicamente la [`IsCancellationRequested`](xref:System.Threading.CancellationToken.IsCancellationRequested) proprietà di `CancellationToken` e uscire se la proprietà è `true` o semplicemente chiamare il [`ThrowIfCancellationRequested`](xref:System.Threading.CancellationToken.ThrowIfCancellationRequested) metodo, nel qual caso il metodo termina con un oggetto [`OperationCancelledException`](xref:System.OperationCanceledException) .

Nell'esempio [**MandelbrotCancellation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotCancellation) viene illustrato l'utilizzo di una funzione annullabile.

### <a name="an-mvvm-mandelbrot"></a>Un oggetto MVVM Mandelbrot

L'esempio [**MandelbrotXF**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/MandelbrotXF) dispone di un'interfaccia utente più estesa ed è basata principalmente su una [`MandelbrotModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotModel.cs) classe e [`MandelbrotViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter20/MandelbrotXF/MandelbrotXF/MandelbrotXF/MandelbrotViewModel.cs) :

[![Screenshot tripla di Mandelbrot X F](images/ch20fg13-small.png "MVVM Mandelbrot")](images/ch20fg13-large.png#lightbox "MVVM Mandelbrot")

## <a name="back-to-the-web"></a>Torna al Web

La [`WebRequest`](xref:System.Net.WebRequest) classe utilizzata in alcuni esempi utilizza un protocollo asincrono obsoleto denominato modello di programmazione asincrono o APM. È possibile convertire tale classe nel protocollo TAP moderno usando uno dei `FromAsync` metodi della [`TaskFactory`](xref:System.Threading.Tasks.TaskFactory`1) classe. Questa operazione è illustrata nell'esempio [**ApmToTap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20/ApmToTap) .

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 20 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)
- [Capitolo 20 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter20)
- [Uso dei file](~/xamarin-forms/data-cloud/data/files.md)
