---
title: "migliorare Xamarin.Forms le prestazioni dell'app": "sono disponibili molte tecniche per aumentare le prestazioni delle Xamarin.Forms applicazioni. Complessivamente, queste tecniche possono ridurre notevolmente la quantità di lavoro eseguita da una CPU e la quantità di memoria utilizzata da un'applicazione.
ms. prod: Novell MS. AssetID: 0be84c56-6698-448d-BE5A-b4205f1caa9f ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 11/27/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="improve-xamarinforms-app-performance"></a>Migliorare le Xamarin.Forms prestazioni dell'app

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Evolve 2016: ottimizzazione delle prestazioni delle app conXamarin.Forms**

Le prestazioni insoddisfacenti di un'applicazione si manifestano in molti modi. Può sembrare che l'applicazione non risponda, lo scorrimento diventa lento e si riduce la durata della batteria del dispositivo. Tuttavia, l'ottimizzazione delle prestazioni implica più della semplice implementazione di codice efficiente. Deve essere considerata anche l'esperienza dell'utente in termini di prestazioni dell'applicazione. Ad esempio, assicurarsi che le operazioni vengano eseguite senza impedire all'utente di eseguire altre attività può contribuire a migliorare l'esperienza dell'utente.

Sono disponibili diverse tecniche per migliorare le prestazioni e le prestazioni percepite delle Xamarin.Forms applicazioni. Nel loro insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione.

> [!NOTE]
>  Prima di leggere questo articolo, è consigliabile vedere [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md), che illustra le tecniche non specifiche di una piattaforma che consentono di migliorare l'utilizzo della memoria e le prestazioni delle applicazioni compilate con la piattaforma Xamarin.

## <a name="enable-the-xaml-compiler"></a>Abilitare il compilatore XAML

Se necessario, il linguaggio XAML può essere compilato direttamente nel linguaggio intermedio (IL) con il compilatore XAML (XAMLC). XAMLC offre alcuni vantaggi:

- Esegue il controllo del codice XAML in fase di compilazione, notificando all'utente eventuali errori.
- Rimuove parte del tempo di carico e di creazione dell'istanza per gli elementi XAML.
- Consente di ridurre le dimensioni del file dell'assembly finale non includendo più i file XAML.

XAMLC è abilitato per impostazione predefinita nelle nuove Xamarin.Forms soluzioni. ma potrebbe essere necessario abilitarlo nelle soluzioni precedenti. Per altre informazioni, vedere l'articolo sulla [compilazione XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="use-compiled-bindings"></a>Usare binding compilati

Le associazioni compilate migliorano data binding le prestazioni nelle Xamarin.Forms applicazioni risolvendo le espressioni di associazione in fase di compilazione, anziché in fase di esecuzione con Reflection. La compilazione di un'espressione di binding genera codice compilato che in genere risolve un binding da 8 a 20 volte più velocemente che usando un binding classico. Per altre informazioni, vedere [Binding compilati](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="reduce-unnecessary-bindings"></a>Ridurre i binding non necessari

Non usare i binding per il contenuto che può essere impostato facilmente in modo statico. Non vi è alcun vantaggio nell'associare i dati che non richiedono l'associazione poiché i binding non hanno un costo contenuto. Ad esempio, `Button.Text = "Accept"` l'impostazione presenta un sovraccarico minore rispetto [`Button.Text`](xref:Xamarin.Forms.Button.Text) al binding a una `string` proprietà ViewModel con valore "Accept".

## <a name="use-fast-renderers"></a>Usare renderer veloci

I renderer veloci riducono l'inflazione e i costi di rendering dei Xamarin.Forms controlli in Android, rendendo flat la gerarchia dei controlli nativi risultante. Questo migliora ulteriormente le prestazioni poiché vengono creati meno oggetti e quindi si ottiene una struttura ad albero visuale meno complessa e un minore consumo di memoria.

Da Xamarin.Forms 4,0 in poi, per impostazione predefinita tutte le applicazioni destinate `FormsAppCompatActivity` a usano renderer veloci. Per altre informazioni, vedere [Renderer veloci](~/xamarin-forms/internals/fast-renderers.md).

## <a name="enable-startup-tracing-on-android"></a>Abilitare la traccia di avvio in Android

La compilazione Ahead Of Time (AOT) in Android riduce al minimo l'utilizzo della memoria e il sovraccarico all'avvio dell'applicazione JIT (Just-In-Time), ma comporta la creazione di un pacchetto dell'applicazione Android molto più grande. In alternativa, è possibile usare la traccia di avvio, che, rispetto alla compilazione AOT convenzionale, offre un compromesso tra le dimensioni del pacchetto dell'applicazione Android e il tempo di avvio.

Anziché compilare il maggior numero possibile di applicazioni nel codice non gestito, la traccia di avvio compila solo il set di metodi gestiti che rappresentano le parti più dispendiose dell'avvio dell'applicazione in un' Xamarin.Forms applicazione vuota. Questo approccio, rispetto alla compilazione AOT convenzionale, comporta una riduzione delle dimensioni del pacchetto dell'applicazione Android, offrendo allo stesso tempo miglioramenti simili all'avvio.

## <a name="enable-layout-compression"></a>Abilitare la compressione dei layout

La compressione dei layout rimuove i layout specificati dalla struttura ad albero visuale, nel tentativo di migliorare le prestazioni del rendering della pagina. Il miglioramento delle prestazioni offerto varia in base alla complessità di una pagina, alla versione del sistema operativo in uso e al dispositivo in cui viene eseguita l'applicazione. Tuttavia, le prestazioni miglioreranno in modo più evidente nei dispositivi meno recenti. Per altre informazioni, vedere [Compressione del layout](~/xamarin-forms/user-interface/layouts/layout-compression.md).

## <a name="choose-the-correct-layout"></a>Scegliere il layout corretto

Un layout in grado di visualizzare più elementi figlio, ma che ne include solo uno è dispendioso. Nell'esempio di codice seguente, ad esempio, viene illustrato un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) con un singolo elemento figlio:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <StackLayout>
        <Image Source="waterfront.jpg" />
    </StackLayout>
</ContentPage>
```

Si tratta di un'operazione inutile ed è [`StackLayout`](xref:Xamarin.Forms.StackLayout) necessario rimuovere l'elemento, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <Image Source="waterfront.jpg" />
</ContentPage>
```

Inoltre, non tentare di riprodurre l'aspetto di un layout specifico usando combinazioni di altri layout, poiché in questo modo vengono eseguiti calcoli di layout non necessari. Non tentare, ad esempio, di riprodurre un [`Grid`](xref:Xamarin.Forms.Grid) layout utilizzando una combinazione di [`StackLayout`](xref:Xamarin.Forms.StackLayout) istanze di. Il codice seguente è un esempio di questa prassi non corretta:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Name:" />
            <Entry Placeholder="Enter your name" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Age:" />
            <Entry Placeholder="Enter your age" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Occupation:" />
            <Entry Placeholder="Enter your occupation" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Address:" />
            <Entry Placeholder="Enter your address" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

L'operazione risulta dispendiosa poiché vengono eseguiti calcoli di layout non necessari. Al contrario, è possibile ottenere il layout desiderato usando un oggetto [`Grid`](xref:Xamarin.Forms.Grid) , come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="100" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
            <RowDefinition Height="30" />
        </Grid.RowDefinitions>
        <Label Text="Name:" />
        <Entry Grid.Column="1" Placeholder="Enter your name" />
        <Label Grid.Row="1" Text="Age:" />
        <Entry Grid.Row="1" Grid.Column="1" Placeholder="Enter your age" />
        <Label Grid.Row="2" Text="Occupation:" />
        <Entry Grid.Row="2" Grid.Column="1" Placeholder="Enter your occupation" />
        <Label Grid.Row="3" Text="Address:" />
        <Entry Grid.Row="3" Grid.Column="1" Placeholder="Enter your address" />
    </Grid>
</ContentPage>
```

## <a name="optimize-layout-performance"></a>Ottimizzare le prestazioni dei layout

Per ottenere le migliori prestazioni possibili del layout, attenersi alle seguenti indicazioni:

- Ridurre la profondità delle gerarchie di layout specificando i [`Margin`](xref:Xamarin.Forms.View.Margin) valori delle proprietà, consentendo la creazione di layout con meno visualizzazioni di wrapping. Per altre informazioni, vedere l'articolo sulle proprietà [Margin e Padding](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).
- Quando si usa un [`Grid`](xref:Xamarin.Forms.Grid) , provare a verificare che il minor numero di righe e colonne possibili sia impostato su [`Auto`](xref:Xamarin.Forms.GridLength.Auto) size. Ogni riga o colonna ridimensionata automaticamente causerà l'esecuzione di ulteriori calcoli di layout da parte del motore di layout. Usare invece righe e colonne con dimensioni fisse, se possibile. In alternativa, impostare righe e colonne in modo da occupare una quantità proporzionale di spazio con il [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) valore di enumerazione, purché l'albero padre segua queste linee guida per il layout.
- Non impostare le [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) [`HorizontalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà e di un layout a meno che non sia richiesto. I valori predefiniti di [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) e [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) consentono la migliore ottimizzazione del layout. La modifica di queste proprietà ha un costo e consuma memoria, anche quando vengono impostate sui valori predefiniti.
- Evitare di utilizzare un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) quando possibile. Causa un notevole aumento del lavoro per la CPU.
- Quando si usa un oggetto [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) , evitare di usare la [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) proprietà quando possibile.
- Quando si usa un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) , verificare che solo un elemento figlio sia impostato su [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) . Questa proprietà garantisce che l'elemento figlio specificato occupi lo spazio più ampio che `StackLayout` è in grado di assegnare ed è dispendioso eseguire questi calcoli più di una volta.
- Evitare di chiamare i metodi della classe, perché comportano l' [`Layout`](xref:Xamarin.Forms.Layout) esecuzione di calcoli di layout costosi. È invece probabile che sia possibile ottenere il comportamento di layout desiderato impostando le [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) proprietà e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) . In alternativa, creare una sottoclasse della [`Layout<View>`](xref:Xamarin.Forms.Layout`1) classe per ottenere il comportamento di layout desiderato.
- Non aggiornare le [`Label`](xref:Xamarin.Forms.Label) istanze più spesso del necessario, perché la modifica delle dimensioni dell'etichetta può comportare il ricalcolo dell'intero layout della schermata.
- Non impostare la [`Label.VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) proprietà a meno che non sia richiesto.
- Impostare l'oggetto [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) di qualsiasi [`Label`](xref:Xamarin.Forms.Label) istanza su [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) quando possibile.

## <a name="use-asynchronous-programming"></a>Usare la programmazione asincrona

La velocità di risposta complessiva dell'applicazione può essere migliorata e i colli di bottiglia delle prestazioni spesso evitati, usando la programmazione asincrona. In .NET, il [modello asincrono basato su attività (TAP)](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap) è il modello di progettazione consigliato per le operazioni asincrone. Tuttavia, l'uso errato del rubinetto può comportare l'esecuzione di applicazioni non efficienti. Pertanto, quando si usa il tocco è necessario seguire le linee guida seguenti.

### <a name="fundamentals"></a>Concetti fondamentali

- Comprendere il ciclo di vita delle attività, rappresentato dall' `TaskStatus` enumerazione. Per ulteriori informazioni, vedere [il significato di TaskStatus](https://devblogs.microsoft.com/pfxteam/the-meaning-of-taskstatus/) e [dello stato dell'attività](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap#task-status).
- Usare il `Task.WhenAll` metodo per attendere il completamento di più operazioni asincrone, anziché `await` una serie di operazioni asincrone. Per ulteriori informazioni, vedere [Task. WhenAll](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall).
- Usare il `Task.WhenAny` metodo per attendere il completamento di una di più operazioni asincrone. Per ulteriori informazioni, vedere [Task. WhenAny](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall).
- Usare il `Task.Delay` metodo per generare un `Task` oggetto che termina dopo l'ora specificata. Questa operazione è utile per scenari come il polling dei dati e per ritardare la gestione dell'input utente per un tempo predeterminato. Per altre informazioni, vedere [Task. Delay](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskdelay).
- Eseguire operazioni di CPU sincrone complesse sul pool di thread con il `Task.Run` metodo. Questo metodo è un collegamento per il `TaskFactory.StartNew` metodo, con il set di argomenti più ottimale. Per ulteriori informazioni, vedere [Task. Run](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskrun).
- Evitare di provare a creare costruttori asincroni. Usare invece gli eventi del ciclo di vita o la logica di inizializzazione separata per eseguire correttamente l' `await` inizializzazione. Per altre informazioni, vedere [costruttori asincroni](https://blog.stephencleary.com/2013/01/async-oop-2-constructors.html) in Blog.stephencleary.com.
- Usare il modello di attività Lazy per evitare l'attesa del completamento delle operazioni asincrone durante l'avvio dell'applicazione. Per ulteriori informazioni, vedere [AsyncLazy](https://devblogs.microsoft.com/pfxteam/asynclazyt/).
- Creare un wrapper di attività per le operazioni asincrone esistenti che non usano il tocco creando `TaskCompletionSource<T>` oggetti. Questi oggetti ottengono i vantaggi della `Task` programmabilità e consentono di controllare la durata e il completamento dell'oggetto associato `Task` . Per ulteriori informazioni, vedere [la natura di TaskCompletionSource](https://devblogs.microsoft.com/pfxteam/the-nature-of-taskcompletionsourcetresult/).
 
- Restituisce un `Task` oggetto, anziché restituire un oggetto atteso `Task` , quando non è necessario elaborare il risultato di un'operazione asincrona. Questa operazione è più efficiente a causa del minor cambio di contesto eseguito.
- Usare la libreria del flusso di dati Task Parallel Library (TPL) in scenari come l'elaborazione di dati quando diventano disponibili o quando sono presenti più operazioni che devono comunicare tra loro in modo asincrono. Per ulteriori informazioni, vedere [dataflow (Task Parallel Library)](/dotnet/standard/parallel-programming/dataflow-task-parallel-library).

### <a name="ui"></a>Interfaccia utente

- Chiamare una versione asincrona di un'API, se disponibile. Questo consente di mantenere sbloccato il thread dell'interfaccia utente, contribuendo a migliorare l'esperienza utente dell'applicazione.
- Aggiornare gli elementi dell'interfaccia utente con i dati delle operazioni asincrone sul thread dell'interfaccia utente, per evitare che vengano generate eccezioni. Gli aggiornamenti alla `ListView.ItemsSource` proprietà verranno tuttavia automaticamente sottoposti a marshalling al thread UI. Per informazioni su come determinare se il codice è in esecuzione nel thread UI, vedere [ Xamarin.Essentials : MainThread](~/essentials/main-thread.md?content=xamarin/xamarin-forms).

    > [!IMPORTANT]
    > Viene eseguito automaticamente il marshalling di tutte le proprietà del controllo aggiornate tramite data binding al thread dell'interfaccia utente.

### <a name="error-handling"></a>Gestione degli errori

- Informazioni sulla gestione asincrona delle eccezioni. Le eccezioni non gestite generate dal codice in esecuzione in modo asincrono vengono propagate al thread chiamante, tranne che in determinati scenari. Per ulteriori informazioni, vedere [gestione delle eccezioni (Task Parallel Library)](/dotnet/standard/parallel-programming/exception-handling-task-parallel-library).
- Evitare `async void` di creare metodi e creare invece `async Task` metodi. Questi consentono una gestione degli errori, la composizione e la testabilità più semplici. L'eccezione a questa linea guida è rappresentata dai gestori eventi asincroni, che devono restituire `void` . Per ulteriori informazioni, vedere la pagina relativa alla [prevenzione del void asincrono](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#avoid-async-void).
- Non combinare il codice di blocco e asincrono chiamando `Task.Wait` i `Task.Result` metodi, o `GetAwaiter().GetResult` , perché possono verificarsi un deadlock. Tuttavia, se questa linea guida deve essere violata, l'approccio preferito consiste nel chiamare il metodo in quanto consente di `GetAwaiter().GetResult` mantenere le eccezioni dell'attività. Per ulteriori informazioni, vedere la pagina relativa alla gestione delle eccezioni in [modo asincrono](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#async-all-the-way) e delle [eccezioni delle attività in .NET 4,5](https://devblogs.microsoft.com/pfxteam/task-exception-handling-in-net-4-5/).
- Usare il `ConfigureAwait` Metodo laddove possibile per creare codice privo di contesto. Il codice senza contesto offre prestazioni migliori per le applicazioni per dispositivi mobili ed è una tecnica utile per evitare deadlock quando si lavora con una codebase parzialmente asincrona. Per altre informazioni, vedere [configurare il contesto](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#configure-context).
- Utilizzare le *attività di continuazione* per le funzionalità, ad esempio la gestione delle eccezioni generate dall'operazione asincrona precedente, e l'annullamento di una continuazione prima che venga avviata o mentre è in esecuzione. Per altre informazioni, vedere [concatenamento di attività tramite attività continue](/dotnet/standard/parallel-programming/chaining-tasks-by-using-continuation-tasks).
- Utilizzare un' `ICommand` implementazione asincrona quando le operazioni asincrone vengono richiamate da `ICommand` . In questo modo è possibile gestire tutte le eccezioni nella logica del comando asincrono. Per altre informazioni, vedere [programmazione asincrona: modelli per le applicazioni MVVM asincrone: comandi](/archive/msdn-magazine/2014/april/async-programming-patterns-for-asynchronous-mvvm-applications-commands).

## <a name="choose-a-dependency-injection-container-carefully"></a>Scegliere con attenzione un contenitore di inserimento delle dipendenze

I contenitori di inserimento delle dipendenze introducono nelle applicazioni per dispositivi mobili ulteriori vincoli relativi alle prestazioni. La registrazione e la risoluzione dei tipi con un contenitore comportano un costo in termini di prestazioni perché il contenitore usa la reflection per la creazione di ogni tipo, soprattutto se le dipendenze vengono ricostruite per la navigazione di ogni pagina nell'app. Se le dipendenze presenti sono numerose o complete, il costo della creazione può aumentare in modo significativo. Inoltre, la registrazione dei tipi, che in genere viene eseguita durante l'avvio dell'applicazione, può avere un notevole effetto sul tempo di avvio, a seconda del contenitore usato.

In alternativa, l'inserimento delle dipendenze può essere reso più efficiente implementandolo manualmente tramite factory.

## <a name="create-shell-applications"></a>Creare applicazioni shell

Xamarin.FormsLe applicazioni Shell offrono un'esperienza di navigazione dogmatica basata su riquadri a comparsa e schede. È utile implementare con la shell l'esperienza utente dell'applicazione, se possibile. Le applicazioni shell consentono di evitare un'esperienza di avvio insoddisfacente, perché le pagine vengono create on demand in risposta alla navigazione invece che all'avvio dell'applicazione, come avviene per le applicazioni che usano una classe [TabbedPage](xref:Xamarin.Forms.TabbedPage). Per ulteriori informazioni, vedere [ Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="use-collectionview-instead-of-listview"></a>Usare CollectionView invece di ListView

[`CollectionView`](xref:Xamarin.Forms.CollectionView)è una vista per la presentazione di elenchi di dati con specifiche di layout diverse. Offre un'alternativa più flessibile ed efficiente a [`ListView`](xref:Xamarin.Forms.ListView) . Per ulteriori informazioni, vedere [ Xamarin.Forms CollectionView](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="optimize-listview-performance"></a>Ottimizzare le prestazioni di ListView

Quando si usa [`ListView`](xref:Xamarin.Forms.ListView) , sono disponibili diverse esperienze utente da ottimizzare:

- **Inizializzazione**: l'intervallo di tempo che inizia quando si crea il controllo e termina quando gli elementi sono visualizzati sullo schermo.
- **Scorrimento**: la possibilità di scorrere l'elenco e verificare che l'interfaccia utente non resti indietro rispetto ai movimenti tocco.
- **Interazione** per l'aggiunta, l'eliminazione e la selezione di elementi.

Il [`ListView`](xref:Xamarin.Forms.ListView) controllo richiede che un'applicazione fornisca i dati e i modelli di cella. Il modo in cui viene eseguita questa operazione ha un notevole impatto sulle prestazioni del controllo. Per altre informazioni, vedere [Prestazioni di ListView](~/xamarin-forms/user-interface/listview/performance.md).

## <a name="optimize-image-resources"></a>Ottimizzare le risorse immagine

Visualizzare le risorse immagine può aumentare notevolmente il footprint della memoria di un'applicazione. Di conseguenza, tali risorse devono essere create solo se necessario e rilasciate non appena l'applicazione non le richiede più. Ad esempio, se un'applicazione visualizza un'immagine leggendone i dati da un flusso, verificare che tale flusso venga creato solo quando è necessario e venga rilasciato quando non è più necessario. Questa operazione può essere eseguita creando il flusso quando viene creata la pagina o quando viene generato l' [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) evento e quindi eliminando il flusso quando viene [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) generato l'evento.

Quando si scarica un'immagine per la visualizzazione con il [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) metodo, memorizzare nella cache l'immagine scaricata assicurandosi che la [`UriImageSource.CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) proprietà sia impostata su `true` . Per altre informazioni, vedere l'articolo sulla [gestione delle immagini](~/xamarin-forms/user-interface/images.md).

Per altre informazioni, vedere [Optimize Image Resources](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages) (Ottimizzare le risorse immagine).

## <a name="reduce-the-visual-tree-size"></a>Ridurre le dimensioni della struttura ad albero visuale

La riduzione del numero di elementi in una pagina consente di velocizzare il rendering della pagina. Esistono due tecniche principali per ottenere questo risultato. La prima consiste nel nascondere gli elementi che non sono visibili. La [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) proprietà di ogni elemento determina se l'elemento deve essere parte della struttura ad albero visuale. Di conseguenza, se un elemento non è visibile perché è nascosto dietro altri elementi, rimuovere l'elemento o impostarne la proprietà `IsVisible` su `false`.

La seconda tecnica è rimuovere gli elementi non necessari. Nell'esempio di codice seguente, ad esempio, viene illustrato un layout di pagina contenente più [`Label`](xref:Xamarin.Forms.Label) oggetti:

```xaml
<StackLayout>
    <StackLayout Padding="20,20,0,0">
        <Label Text="Hello" />
    </StackLayout>
    <StackLayout Padding="20,20,0,0">
        <Label Text="Welcome to the App!" />
    </StackLayout>
    <StackLayout Padding="20,20,0,0">
        <Label Text="Downloading Data..." />
    </StackLayout>
</StackLayout>
```

Si può mantenere lo stesso layout di pagina con un numero ridotto di elementi, come illustra l'esempio di codice seguente:

```xaml
<StackLayout Padding="20,35,20,20" Spacing="25">
  <Label Text="Hello" />
  <Label Text="Welcome to the App!" />
  <Label Text="Downloading Data..." />
</StackLayout>
```

## <a name="reduce-the-application-resource-dictionary-size"></a>Ridurre le dimensioni del dizionario risorse dell'applicazione

Tutte le risorse usate nell'applicazione devono essere archiviate nel dizionario risorse dell'applicazione per evitare duplicati. Ciò è utile per ridurre la quantità di codice XAML da analizzare in tutta l'applicazione. L'esempio di codice riportato di seguito illustra la risorsa `HeadingLabelStyle`, che è usata a livello di applicazione e quindi viene definita nel dizionario risorse dell'applicazione:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Resources.App">
     <Application.Resources>
         <ResourceDictionary>
            <Style x:Key="HeadingLabelStyle" TargetType="Label">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
         </ResourceDictionary>
     </Application.Resources>
</Application>
```

Tuttavia, il codice XAML specifico di una pagina non deve essere incluso nel dizionario risorse dell'applicazione per evitare che le risorse vengano analizzate all'avvio dell'applicazione invece che quando richiesto da una pagina. Se una risorsa viene usata da una pagina che non è la pagina di avvio, deve essere inserita nel dizionario risorse per quella pagina e in questo modo si riduce il codice XAML analizzato all'avvio dell'applicazione. L'esempio di codice riportato di seguito illustra la risorsa `HeadingLabelStyle`, che è usata solo per una pagina e viene quindi definita nel dizionario risorse della pagina:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Test.HomePage"
             Padding="0,20,0,0">
    <ContentPage.Resources>
        <ResourceDictionary>
          <Style x:Key="HeadingLabelStyle" TargetType="Label">
              <Setter Property="HorizontalOptions" Value="Center" />
              <Setter Property="FontSize" Value="Large" />
              <Setter Property="TextColor" Value="Red" />
          </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Per altre informazioni sulle risorse dell'applicazione, vedere [Stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

## <a name="use-the-custom-renderer-pattern"></a>Usare il modello di renderer personalizzato

La maggior parte delle Xamarin.Forms classi renderer espone il `OnElementChanged` metodo, che viene chiamato quando Xamarin.Forms viene creato un controllo personalizzato per il rendering del controllo nativo corrispondente. Personalizzare le classi renderer per ogni progetto della piattaforma, quindi eseguire l'override di questo metodo per creare un'istanza del controllo nativo e personalizzare il controllo. Il metodo `SetNativeControl` viene usato per creare un'istanza del controllo nativo e assegna anche il riferimento a controllo alla proprietà `Control`.

Tuttavia, in alcune circostanze il metodo `OnElementChanged` può essere chiamato più volte. Di conseguenza, per evitare perdite di memoria, che possono avere un impatto sulle prestazioni, prestare attenzione quando si crea un'istanza di un nuovo controllo nativo. L'approccio da usare quando si crea un'istanza di un nuovo controllo nativo in un renderer personalizzato è illustrato nell'esempio di codice seguente:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null)
  {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null)
  {
    if (Control == null)
    {
      // Instantiate the native control with the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

Per un nuovo controllo nativo l'istanza deve essere creata solo una volta, quando la proprietà `Control` è `null`. Inoltre, il controllo deve essere creato, configurato e i gestori di eventi hanno sottoscritto quando il renderer personalizzato è associato a un nuovo Xamarin.Forms elemento. Analogamente, per i gestori degli eventi sottoscritti l'iscrizione deve essere annullata solo in caso di modifica dell'elemento allegato al renderer. L'adozione di questo approccio consente di creare un renderer personalizzato dalle prestazioni ottimali che non subisce perdite di memoria.

> [!IMPORTANT]
> Il metodo `SetNativeControl` deve essere richiamato solo se la proprietà `e.NewElement` non è `null` e la proprietà `Control` è `null`.

Per altre informazioni sui renderer personalizzati, vedere l'articolo relativo alla [personalizzazione dei controlli su ogni piattaforma](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Compilazione di XAML](~/xamarin-forms/xaml/xamlc.md)
- [Binding compilati](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)
- [Renderer veloci](~/xamarin-forms/internals/fast-renderers.md)
- [Compressione del layout](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [Xamarin.FormsShell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin.FormsCollectionView](~/xamarin-forms/user-interface/collectionview/index.md)
- [Prestazioni di ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [Ottimizzazione delle risorse immagine](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)
- [Stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Personalizzazione di controlli in ogni piattaforma](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
