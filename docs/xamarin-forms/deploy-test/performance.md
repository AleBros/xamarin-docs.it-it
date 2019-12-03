---
title: Migliorare le prestazioni delle app Xamarin.Forms
description: Esistono molte tecniche per incrementare le prestazioni delle applicazioni Xamarin.Forms. Insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione.
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2019
ms.openlocfilehash: c57281f3fa526bb238f4a0dd6a4fad70376c742e
ms.sourcegitcommit: b4c9eb94ae2b9eae852a24d126b39ac64a6d0ffb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681340"
---
# <a name="improve-xamarinforms-app-performance"></a>Migliorare le prestazioni delle app Xamarin.Forms

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Evoluzione 2016: ottimizzazione delle prestazioni delle app con Xamarin.Forms**

Le prestazioni insoddisfacenti di un'applicazione si manifestano in molti modi. Può sembrare che l'applicazione non risponda, lo scorrimento diventa lento e si riduce la durata della batteria del dispositivo. Tuttavia, l'ottimizzazione delle prestazioni implica più della semplice implementazione di codice efficiente. Deve essere considerata anche l'esperienza dell'utente in termini di prestazioni dell'applicazione. Ad esempio, verificando che le operazioni vengano eseguite senza impedire all'utente di eseguire altre attività può contribuire a migliorare l'esperienza dell'utente.

Esistono diverse tecniche per migliorare le prestazioni, e le prestazioni percepite, delle applicazioni Xamarin.Forms. Insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione.

> [!NOTE]
> Prima di leggere questo articolo, è consigliabile vedere [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md), che illustra le tecniche non specifiche di una piattaforma che consentono di migliorare l'utilizzo della memoria e le prestazioni delle applicazioni compilate con la piattaforma Xamarin.

## <a name="enable-the-xaml-compiler"></a>Abilitare il compilatore XAML

Se necessario, il linguaggio XAML può essere compilato direttamente nel linguaggio intermedio (IL) con il compilatore XAML (XAMLC). XAMLC offre alcuni vantaggi:

- Esegue il controllo del codice XAML in fase di compilazione, notificando all'utente eventuali errori.
- Rimuove parte del tempo di carico e di creazione dell'istanza per gli elementi XAML.
- Consente di ridurre le dimensioni del file dell'assembly finale non includendo più i file XAML.

XAMLC è abilitato per impostazione predefinita nelle nuove soluzioni Xamarin.Forms, ma potrebbe essere necessario abilitarlo nelle soluzioni precedenti. Per altre informazioni, vedere l'articolo sulla [compilazione XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="use-compiled-bindings"></a>Usare binding compilati

I binding compilati migliorano le prestazioni di data binding nelle applicazioni Xamarin.Forms risolvendo le espressioni di binding in fase di compilazione invece che in fase di runtime con la reflection. La compilazione di un'espressione di binding genera codice compilato che in genere risolve un binding da 8 a 20 volte più velocemente che usando un binding classico. Per altre informazioni, vedere [Binding compilati](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="reduce-unnecessary-bindings"></a>Ridurre i binding non necessari

Non usare i binding per il contenuto che può essere impostato facilmente in modo statico. Non vi è alcun vantaggio nell'associare i dati che non richiedono l'associazione poiché i binding non hanno un costo contenuto. Ad esempio, l'impostazione di `Button.Text = "Accept"` presenta un sovraccarico minore rispetto al binding [`Button.Text`](xref:Xamarin.Forms.Button.Text) a una proprietà ViewModel `string` con il valore "Accept".

## <a name="use-fast-renderers"></a>Usare renderer veloci

I renderer veloci riducono i costi di ingrandimento e rendering dei controlli di Xamarin.Forms in Android appiattendo la gerarchia dei controllo nativi risultante. Questo migliora ulteriormente le prestazioni poiché vengono creati meno oggetti e quindi si ottiene una struttura ad albero visuale meno complessa e un minore consumo di memoria.

A partire da Xamarin.Forms 4.0, tutte le applicazioni che specificano come destinazione `FormsAppCompatActivity` usano renderer veloci per impostazione predefinita. Per altre informazioni, vedere [Renderer veloci](~/xamarin-forms/internals/fast-renderers.md).

## <a name="enable-startup-tracing-on-android"></a>Abilitare la traccia di avvio in Android

La compilazione Ahead Of Time (AOT) in Android riduce al minimo l'utilizzo della memoria e il sovraccarico all'avvio dell'applicazione JIT (Just-In-Time), ma comporta la creazione di un pacchetto dell'applicazione Android molto più grande. In alternativa, è possibile usare la traccia di avvio, che, rispetto alla compilazione AOT convenzionale, offre un compromesso tra le dimensioni del pacchetto dell'applicazione Android e il tempo di avvio.

Invece di compilare il maggior numero possibile di elementi dell'applicazione nel codice non gestito, la traccia di avvio compila solo il set di metodi gestiti che rappresentano le parti più dispendiose dell'avvio dell'applicazione in un'applicazione Xamarin.Forms vuota. Questo approccio, rispetto alla compilazione AOT convenzionale, comporta una riduzione delle dimensioni del pacchetto dell'applicazione Android, offrendo allo stesso tempo miglioramenti simili all'avvio.

## <a name="enable-layout-compression"></a>Abilitare la compressione dei layout

La compressione dei layout rimuove i layout specificati dalla struttura ad albero visuale, nel tentativo di migliorare le prestazioni del rendering della pagina. Il miglioramento delle prestazioni offerto varia in base alla complessità di una pagina, alla versione del sistema operativo in uso e al dispositivo in cui viene eseguita l'applicazione. Tuttavia, le prestazioni miglioreranno in modo più evidente nei dispositivi meno recenti. Per altre informazioni, vedere [Compressione del layout](~/xamarin-forms/user-interface/layouts/layout-compression.md).

## <a name="choose-the-correct-layout"></a>Scegliere il layout corretto

Un layout in grado di visualizzare più elementi figlio, ma che ne include solo uno è dispendioso. Ad esempio, il codice riportato di seguito indica un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) con un singolo elemento figlio:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <StackLayout>
        <Image Source="waterfront.jpg" />
    </StackLayout>
</ContentPage>
```

Questo è dispendioso e l'elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) deve essere rimosso, come illustra l'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <Image Source="waterfront.jpg" />
</ContentPage>
```

Inoltre, non tentare di riprodurre l'aspetto di un layout specifico usando combinazioni di altri layout, poiché in questo modo vengono eseguiti calcoli di layout non necessari. Ad esempio, non tentare di riprodurre un layout [`Grid`](xref:Xamarin.Forms.Grid) usando una combinazione di istanze [`StackLayout`](xref:Xamarin.Forms.StackLayout). Il codice seguente è un esempio di questa prassi non corretta:

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

L'operazione risulta dispendiosa poiché vengono eseguiti calcoli di layout non necessari. Il layout può essere invece realizzato in modo ottimale usando un elemento [`Grid`](xref:Xamarin.Forms.Grid), come illustrato nell'esempio di codice seguente:

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

- Ridurre la profondità delle gerarchie di layout specificando i valori delle proprietà [`Margin`](xref:Xamarin.Forms.View.Margin) consentendo la creazione di layout con meno visualizzazioni con wrapping. Per altre informazioni, vedere l'articolo sulle proprietà [Margin e Padding](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).
- Quando si usa un elemento [`Grid`](xref:Xamarin.Forms.Grid), provare a verificare che sia impostato il minor numero possibile di righe e colonne sulla dimensione [`Auto`](xref:Xamarin.Forms.GridLength.Auto). Ogni riga o colonna ridimensionata automaticamente causerà l'esecuzione di ulteriori calcoli di layout da parte del motore di layout. Usare invece righe e colonne con dimensioni fisse, se possibile. In alternativa, impostare righe e colonne in modo che occupino una quantità proporzionale di spazio con il valore di enumerazione [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star), purché l'albero padre segua queste linee guida relative al layout.
- Non impostare le proprietà [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) e [`HorizontalOptions`](xref:Xamarin.Forms.View.VerticalOptions) di un layout a meno che non sia richiesto. I valori predefiniti di [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) e [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) consentono la migliore ottimizzazione del layout. La modifica di queste proprietà ha un costo e consuma memoria, anche quando vengono impostate sui valori predefiniti.
- Evitare di usare un elemento [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) quando possibile. Causa un notevole aumento del lavoro per la CPU.
- Quando si usa un elemento [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), evitare di usare la proprietà [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) ovunque sia possibile.
- Se si usa un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout), verificare che sia impostato un solo elemento figlio su [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands). Questa proprietà garantisce che l'elemento figlio specificato occupi lo spazio più ampio che `StackLayout` è in grado di assegnare ed è dispendioso eseguire questi calcoli più di una volta.
- Evitare di chiamare uno dei metodi della classe [`Layout`](xref:Xamarin.Forms.Layout), perché causano l'esecuzione di calcoli dispendiosi. È invece probabile che si possa ottenere un determinato comportamento del layout impostando le proprietà [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY). In alternativa, sottoclassare la classe [`Layout<View>`](xref:Xamarin.Forms.Layout`1) per ottenere tale comportamento del layout.
- Non aggiornare le istanze di [`Label`](xref:Xamarin.Forms.Label) più spesso di quanto necessario, poiché modificando le dimensioni dell'etichetta è possibile che l'intero layout della schermata venga ricalcolato.
- Non impostare la proprietà [`Label.VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) se non viene richiesto.
- Impostare l'elemento [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) di qualsiasi istanza di [`Label`](xref:Xamarin.Forms.Label) su [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) quando possibile.

## <a name="use-asynchronous-programming"></a>USA programmazione asincrona

La velocità di risposta complessiva dell'applicazione può essere migliorata e i colli di bottiglia delle prestazioni spesso evitati, usando la programmazione asincrona. In .NET, il [modello asincrono basato su attività (TAP)](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap) è il modello di progettazione consigliato per le operazioni asincrone. Tuttavia, l'uso errato del rubinetto può comportare l'esecuzione di applicazioni non efficienti. Pertanto, quando si usa il tocco è necessario seguire le linee guida seguenti.

### <a name="fundamentals"></a>Fundamentals

- Comprendere il ciclo di vita delle attività, rappresentato dall'enumerazione `TaskStatus`. Per ulteriori informazioni, vedere [il significato di TaskStatus](https://devblogs.microsoft.com/pfxteam/the-meaning-of-taskstatus/) e [dello stato dell'attività](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap#task-status).
- Utilizzare il metodo `Task.WhenAll` per attendere il completamento di più operazioni asincrone, anziché singolarmente `await` una serie di operazioni asincrone. Per ulteriori informazioni, vedere [Task. WhenAll](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall).
- Usare il metodo `Task.WhenAny` per attendere il completamento di una di più operazioni asincrone. Per ulteriori informazioni, vedere [Task. WhenAny](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall).
- Usare il metodo `Task.Delay` per produrre un oggetto `Task` che termina dopo l'ora specificata. Questa operazione è utile per scenari come il polling dei dati e per ritardare la gestione dell'input utente per un tempo predeterminato. Per altre informazioni, vedere [Task. Delay](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskdelay).
- Eseguire operazioni di CPU sincrone complesse sul pool di thread con il metodo `Task.Run`. Questo metodo è un tasto di scelta rapida per il metodo `TaskFactory.StartNew` con gli argomenti più ottimali impostati. Per ulteriori informazioni, vedere [Task. Run](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskrun).
- Evitare di provare a creare costruttori asincroni. Usare invece gli eventi del ciclo di vita o la logica di inizializzazione separata per `await` correttamente qualsiasi inizializzazione. Per altre informazioni, vedere [costruttori asincroni](https://blog.stephencleary.com/2013/01/async-oop-2-constructors.html) in Blog.stephencleary.com.
- Usare il modello di attività Lazy per evitare l'attesa del completamento delle operazioni asincrone durante l'avvio dell'applicazione. Per ulteriori informazioni, vedere [AsyncLazy](https://devblogs.microsoft.com/pfxteam/asynclazyt/).
- Creare un wrapper di attività per le operazioni asincrone esistenti che non usano il tocco, creando `TaskCompletionSource<T>` oggetti. Questi oggetti ottengono i vantaggi della programmabilità `Task` e consentono di controllare la durata e il completamento del `Task`associato. Per ulteriori informazioni, vedere [la natura di TaskCompletionSource](https://devblogs.microsoft.com/pfxteam/the-nature-of-taskcompletionsourcetresult/).
Asynchronous-MVVM-Applications-Commands).
- Restituisce un oggetto `Task`, anziché restituire un oggetto `Task` atteso, quando non è necessario elaborare il risultato di un'operazione asincrona. Questa operazione è più efficiente a causa del minor cambio di contesto eseguito.
- Usare la libreria del flusso di dati Task Parallel Library (TPL) in scenari come l'elaborazione di dati quando diventano disponibili o quando sono presenti più operazioni che devono comunicare tra loro in modo asincrono. Per ulteriori informazioni, vedere [dataflow (Task Parallel Library)](/dotnet/standard/parallel-programming/dataflow-task-parallel-library).

### <a name="ui"></a>Interfaccia utente di

- Chiamare una versione asincrona di un'API, se disponibile. Questo consente di mantenere sbloccato il thread dell'interfaccia utente, contribuendo a migliorare l'esperienza utente dell'applicazione.
- Aggiornare gli elementi dell'interfaccia utente con i dati delle operazioni asincrone sul thread dell'interfaccia utente, per evitare che vengano generate eccezioni. Tuttavia, gli aggiornamenti alla proprietà `ListView.ItemsSource` verranno automaticamente sottoposti a marshalling nel thread UI. Per informazioni su come determinare se il codice è in esecuzione nel thread dell'interfaccia utente, vedere [Novell. Essentials: MainThread](~/essentials/main-thread.md?content=xamarin/xamarin-forms).

    > [!IMPORTANT]
    > Qualsiasi proprietà del controllo aggiornata tramite data binding verrà automaticamente marsheled al thread dell'interfaccia utente.

### <a name="error-handling"></a>Gestione degli errori

- Informazioni sulla gestione asincrona delle eccezioni. Le eccezioni non gestite generate dal codice in esecuzione in modo asincrono vengono propagate al thread chiamante, tranne che in determinati scenari. Per ulteriori informazioni, vedere [gestione delle eccezioni (Task Parallel Library)](/dotnet/standard/parallel-programming/exception-handling-task-parallel-library).
- Evitare di creare metodi di `async void` e di creare invece `async Task` metodi. Questi consentono una gestione degli errori, la composizione e la testabilità più semplici. L'eccezione a questa linea guida è rappresentata dai gestori eventi asincroni, che devono restituire `void`. Per ulteriori informazioni, vedere la pagina relativa alla [prevenzione del void asincrono](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#avoid-async-void).
- Non combinare il codice di blocco e asincrono chiamando i metodi `Task.Wait`, `Task.Result`o `GetAwaiter().GetResult`, perché possono verificarsi un deadlock. Tuttavia, se questa linea guida deve essere violata, l'approccio preferito consiste nel chiamare il metodo `GetAwaiter().GetResult` perché consente di mantenere le eccezioni dell'attività. Per ulteriori informazioni, vedere la pagina relativa alla gestione delle eccezioni in [modo asincrono](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#async-all-the-way) e delle [eccezioni delle attività in .NET 4,5](https://devblogs.microsoft.com/pfxteam/task-exception-handling-in-net-4-5/).
- Usare il metodo `ConfigureAwait`, quando possibile, per creare codice senza contesto. Il codice senza contesto offre prestazioni migliori per le applicazioni per dispositivi mobili ed è una tecnica utile per evitare deadlock quando si lavora con una codebase parzialmente asincrona. Per altre informazioni, vedere [configurare il contesto](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#configure-context).
- Utilizzare le *attività di continuazione* per le funzionalità, ad esempio la gestione delle eccezioni generate dall'operazione asincrona precedente, e l'annullamento di una continuazione prima che venga avviata o mentre è in esecuzione. Per altre informazioni, vedere [concatenamento di attività tramite attività continue](/dotnet/standard/parallel-programming/chaining-tasks-by-using-continuation-tasks).
- Utilizzare un'implementazione di `ICommand` asincrona quando vengono richiamate le operazioni asincrone dal `ICommand`. In questo modo è possibile gestire tutte le eccezioni nella logica del comando asincrono. Per altre informazioni, vedere [programmazione asincrona: modelli per le applicazioni MVVM asincrone: comandi](/archive/msdn-magazine/2014/april/async-programming-patterns-for-asynchronous-mvvm-applications-commands).

## <a name="choose-a-dependency-injection-container-carefully"></a>Scegliere con attenzione un contenitore di inserimento delle dipendenze

I contenitori di inserimento delle dipendenze introducono nelle applicazioni per dispositivi mobili ulteriori vincoli relativi alle prestazioni. La registrazione e la risoluzione dei tipi con un contenitore comportano un costo in termini di prestazioni perché il contenitore usa la reflection per la creazione di ogni tipo, soprattutto se le dipendenze vengono ricostruite per la navigazione di ogni pagina nell'app. Se le dipendenze presenti sono numerose o complete, il costo della creazione può aumentare in modo significativo. Inoltre, la registrazione dei tipi, che in genere viene eseguita durante l'avvio dell'applicazione, può avere un notevole effetto sul tempo di avvio, a seconda del contenitore usato.

In alternativa, l'inserimento delle dipendenze può essere reso più efficiente implementandolo manualmente tramite factory.

## <a name="create-shell-applications"></a>Creare applicazioni shell

Le applicazioni shell di Xamarin.Forms offrono una solida esperienza di navigazione basata su riquadri a comparsa e schede. È utile implementare con la shell l'esperienza utente dell'applicazione, se possibile. Le applicazioni shell consentono di evitare un'esperienza di avvio insoddisfacente, perché le pagine vengono create on demand in risposta alla navigazione invece che all'avvio dell'applicazione, come avviene per le applicazioni che usano una classe [TabbedPage](xref:Xamarin.Forms.TabbedPage). Per altre informazioni, vedere [Shell di Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="use-collectionview-instead-of-listview"></a>Usare CollectionView invece di ListView

[`CollectionView`](xref:Xamarin.Forms.CollectionView) è una vista per presentare elenchi di dati usando specifiche di layout diverse. Offre un'alternativa a [`ListView`](xref:Xamarin.Forms.ListView) più flessibile ed efficiente. Per altre informazioni, vedere [CollectionView di Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="optimize-listview-performance"></a>Ottimizzare le prestazioni di ListView

Se si usa [`ListView`](xref:Xamarin.Forms.ListView) esistono alcune esperienze utente che devono essere ottimizzate:

- **Inizializzazione**: l'intervallo di tempo che inizia quando si crea il controllo e termina quando gli elementi sono visualizzati sullo schermo.
- **Scorrimento**: la possibilità di scorrere l'elenco e verificare che l'interfaccia utente non resti indietro rispetto ai movimenti tocco.
- **Interazione** per l'aggiunta, l'eliminazione e la selezione di elementi.

Il controllo [`ListView`](xref:Xamarin.Forms.ListView) richiede un'applicazione per specificare i dati e i modelli di cella. Il modo in cui viene eseguita questa operazione ha un notevole impatto sulle prestazioni del controllo. Per altre informazioni, vedere [Prestazioni di ListView](~/xamarin-forms/user-interface/listview/performance.md).

## <a name="optimize-image-resources"></a>Ottimizzare le risorse immagine

Visualizzare le risorse immagine può aumentare notevolmente il footprint della memoria di un'applicazione. Di conseguenza, tali risorse devono essere create solo se necessario e rilasciate non appena l'applicazione non le richiede più. Ad esempio, se un'applicazione visualizza un'immagine leggendone i dati da un flusso, verificare che tale flusso venga creato solo quando è necessario e venga rilasciato quando non è più necessario. Questo risultato può essere ottenuto creando il flusso quando viene creata la pagina oppure quando viene generato l'evento [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) e successivamente eliminando il flusso quando viene generato l'evento [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing).

Quando si scarica un'immagine da visualizzare con il metodo [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)), per memorizzare nella cache l'immagine scaricata, verificare che la proprietà [`UriImageSource.CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) sia impostata su `true`. Per altre informazioni, vedere l'articolo sulla [gestione delle immagini](~/xamarin-forms/user-interface/images.md).

Per altre informazioni, vedere [Optimize Image Resources](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages) (Ottimizzare le risorse immagine).

## <a name="reduce-the-visual-tree-size"></a>Ridurre le dimensioni della struttura ad albero visuale

La riduzione del numero di elementi in una pagina consente di velocizzare il rendering della pagina. Esistono due tecniche principali per ottenere questo risultato. La prima consiste nel nascondere gli elementi che non sono visibili. La proprietà [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) di ogni elemento determina se l'elemento deve essere parte della struttura ad albero visuale o meno. Di conseguenza, se un elemento non è visibile perché è nascosto dietro altri elementi, rimuovere l'elemento o impostarne la proprietà `IsVisible` su `false`.

La seconda tecnica è rimuovere gli elementi non necessari. Ad esempio, il codice seguente illustra un layout di pagina contenente più oggetti [`Label`](xref:Xamarin.Forms.Label):

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

La maggior parte delle classi renderer di Xamarin.Forms espone il metodo `OnElementChanged`, che viene chiamato quando si crea un controllo personalizzato di Xamarin.Forms per il rendering del controllo nativo corrispondente. Personalizzare le classi renderer per ogni progetto della piattaforma, quindi eseguire l'override di questo metodo per creare un'istanza del controllo nativo e personalizzare il controllo. Il metodo `SetNativeControl` viene usato per creare un'istanza del controllo nativo e assegna anche il riferimento a controllo alla proprietà `Control`.

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

Per un nuovo controllo nativo l'istanza deve essere creata solo una volta, quando la proprietà `Control` è `null`. È inoltre necessario configurare il controllo e sottoscrivere i gestori degli eventi solo quando il renderer personalizzato è allegato a un nuovo elemento di Xamarin.Forms. Analogamente, per i gestori degli eventi sottoscritti l'iscrizione deve essere annullata solo in caso di modifica dell'elemento allegato al renderer. L'adozione di questo approccio consente di creare un renderer personalizzato dalle prestazioni ottimali che non subisce perdite di memoria.

> [!IMPORTANT]
> Il metodo `SetNativeControl` deve essere richiamato solo se la proprietà `e.NewElement` non è `null` e la proprietà `Control` è `null`.

Per altre informazioni sui renderer personalizzati, vedere l'articolo relativo alla [personalizzazione dei controlli su ogni piattaforma](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Compilazione di XAML](~/xamarin-forms/xaml/xamlc.md)
- [Binding compilati](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)
- [Renderer veloci](~/xamarin-forms/internals/fast-renderers.md)
- [Compressione del layout](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [Shell di Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [CollectionView di Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/index.md)
- [Prestazioni di ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [Ottimizzazione delle risorse immagine](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)
- [Stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Personalizzazione di controlli in ogni piattaforma](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
