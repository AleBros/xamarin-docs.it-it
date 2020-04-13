---
title: Migliorare le prestazioni delle app Xamarin.Forms
description: Esistono molte tecniche per incrementare le prestazioni delle applicazioni Xamarin.Forms. Nel loro insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione.
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2019
ms.openlocfilehash: 4427d347723284a2f8897612f10857270c9631bf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305213"
---
# <a name="improve-xamarinforms-app-performance"></a>Migliorare le prestazioni delle app Xamarin.Forms

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Evoluzione 2016: ottimizzazione delle prestazioni delle app con Xamarin.Forms**

Le prestazioni insoddisfacenti di un'applicazione si manifestano in molti modi. Può sembrare che l'applicazione non risponda, lo scorrimento diventa lento e si riduce la durata della batteria del dispositivo. Tuttavia, l'ottimizzazione delle prestazioni implica più della semplice implementazione di codice efficiente. Deve essere considerata anche l'esperienza dell'utente in termini di prestazioni dell'applicazione. Ad esempio, assicurarsi che le operazioni vengano eseguite senza impedire all'utente di eseguire altre attività può contribuire a migliorare l'esperienza dell'utente.

Esistono diverse tecniche per migliorare le prestazioni, e le prestazioni percepite, delle applicazioni Xamarin.Forms. Nel loro insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione.

> [!NOTE]
>  Prima di leggere questo articolo, è consigliabile vedere [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md), che illustra le tecniche non specifiche di una piattaforma che consentono di migliorare l'utilizzo della memoria e le prestazioni delle applicazioni compilate con la piattaforma Xamarin.

## <a name="enable-the-xaml-compiler"></a>Abilitare il compilatore XAML

Se necessario, il linguaggio XAML può essere compilato direttamente nel linguaggio intermedio (IL) con il compilatore XAML (XAMLC). XAMLC offre alcuni vantaggi:

- Esegue il controllo del codice XAML in fase di compilazione, notificando all'utente eventuali errori.
- Rimuove parte del tempo di carico e di creazione dell'istanza per gli elementi XAML.
- Consente di ridurre le dimensioni del file dell'assembly finale non includendo più i file XAML.

XAMLC è abilitato per impostazione predefinita nelle nuove soluzioni Xamarin.Forms, ma potrebbe essere necessario abilitarlo nelle soluzioni precedenti. Per altre informazioni, vedere l'articolo sulla [compilazione XAML](~/xamarin-forms/xaml/xamlc.md).

## <a name="use-compiled-bindings"></a>Usare binding compilati

I binding compilati migliorano le prestazioni di data binding nelle applicazioni Xamarin.Forms risolvendo le espressioni di binding in fase di compilazione invece che in fase di runtime con la reflection. La compilazione di un'espressione di binding genera codice compilato che in genere risolve un binding da 8 a 20 volte più velocemente che usando un binding classico. Per altre informazioni, vedere [Binding compilati](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md).

## <a name="reduce-unnecessary-bindings"></a>Ridurre i binding non necessari

Non usare i binding per il contenuto che può essere impostato facilmente in modo statico. Non vi è alcun vantaggio nell'associare i dati che non richiedono l'associazione poiché i binding non hanno un costo contenuto. Ad esempio, `Button.Text = "Accept"` l'impostazione [`Button.Text`](xref:Xamarin.Forms.Button.Text) ha meno `string` overhead rispetto all'associazione a una proprietà viewmodel con valore "Accept".

## <a name="use-fast-renderers"></a>Usare renderer veloci

I renderer veloci riducono i costi di ingrandimento e rendering dei controlli di Xamarin.Forms in Android appiattendo la gerarchia dei controllo nativi risultante. Questo migliora ulteriormente le prestazioni poiché vengono creati meno oggetti e quindi si ottiene una struttura ad albero visuale meno complessa e un minore consumo di memoria.

A partire da Xamarin.Forms 4.0, tutte le applicazioni che specificano come destinazione `FormsAppCompatActivity` usano renderer veloci per impostazione predefinita. Per altre informazioni, vedere [Renderer veloci](~/xamarin-forms/internals/fast-renderers.md).

## <a name="enable-startup-tracing-on-android"></a>Abilitare la traccia di avvio in Android

La compilazione Ahead Of Time (AOT) in Android riduce al minimo l'utilizzo della memoria e il sovraccarico all'avvio dell'applicazione JIT (Just-In-Time), ma comporta la creazione di un pacchetto dell'applicazione Android molto più grande. In alternativa, è possibile usare la traccia di avvio, che, rispetto alla compilazione AOT convenzionale, offre un compromesso tra le dimensioni del pacchetto dell'applicazione Android e il tempo di avvio.

Invece di compilare il maggior numero possibile di elementi dell'applicazione nel codice non gestito, la traccia di avvio compila solo il set di metodi gestiti che rappresentano le parti più dispendiose dell'avvio dell'applicazione in un'applicazione Xamarin.Forms vuota. Questo approccio, rispetto alla compilazione AOT convenzionale, comporta una riduzione delle dimensioni del pacchetto dell'applicazione Android, offrendo allo stesso tempo miglioramenti simili all'avvio.

## <a name="enable-layout-compression"></a>Abilitare la compressione dei layout

La compressione dei layout rimuove i layout specificati dalla struttura ad albero visuale, nel tentativo di migliorare le prestazioni del rendering della pagina. Il miglioramento delle prestazioni offerto varia in base alla complessità di una pagina, alla versione del sistema operativo in uso e al dispositivo in cui viene eseguita l'applicazione. Tuttavia, le prestazioni miglioreranno in modo più evidente nei dispositivi meno recenti. Per altre informazioni, vedere [Compressione del layout](~/xamarin-forms/user-interface/layouts/layout-compression.md).

## <a name="choose-the-correct-layout"></a>Scegliere il layout corretto

Un layout in grado di visualizzare più elementi figlio, ma che ne include solo uno è dispendioso. Ad esempio, l'esempio [`StackLayout`](xref:Xamarin.Forms.StackLayout) di codice seguente mostra un con un singolo elemento figlio:For example, the following code example shows a with a single child:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <StackLayout>
        <Image Source="waterfront.jpg" />
    </StackLayout>
</ContentPage>
```

Si tratta di [`StackLayout`](xref:Xamarin.Forms.StackLayout) uno spreco e l'elemento deve essere rimosso, come illustrato nell'esempio di codice seguente:This is distoredite and the element should be removed, as shown in the following code example:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <Image Source="waterfront.jpg" />
</ContentPage>
```

Inoltre, non tentare di riprodurre l'aspetto di un layout specifico usando combinazioni di altri layout, poiché in questo modo vengono eseguiti calcoli di layout non necessari. Ad esempio, non tentare di [`Grid`](xref:Xamarin.Forms.Grid) riprodurre un [`StackLayout`](xref:Xamarin.Forms.StackLayout) layout utilizzando una combinazione di istanze. Il codice seguente è un esempio di questa prassi non corretta:

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

L'operazione risulta dispendiosa poiché vengono eseguiti calcoli di layout non necessari. Al contrario, il layout desiderato [`Grid`](xref:Xamarin.Forms.Grid)può essere ottenuto meglio utilizzando un oggetto , come illustrato nell'esempio di codice seguente:

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

- Ridurre la profondità delle [`Margin`](xref:Xamarin.Forms.View.Margin) gerarchie di layout specificando i valori delle proprietà, consentendo la creazione di layout con un numero inferiore di visualizzazioni a capo. Per altre informazioni, vedere l'articolo sulle proprietà [Margin e Padding](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).
- Quando si [`Grid`](xref:Xamarin.Forms.Grid)utilizza un oggetto , assicurarsi che il [`Auto`](xref:Xamarin.Forms.GridLength.Auto) minor numero possibile di righe e colonne sia impostato sulle dimensioni. Ogni riga o colonna ridimensionata automaticamente causerà l'esecuzione di ulteriori calcoli di layout da parte del motore di layout. Usare invece righe e colonne con dimensioni fisse, se possibile. In alternativa, impostare righe e colonne per occupare una quantità proporzionale di spazio con il [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) valore di enumerazione, a condizione che l'albero padre segua queste linee guida di layout.
- Non impostare [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) le [`HorizontalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà e di un layout a meno che non sia necessario. I valori [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) predefiniti [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) di e consentono la migliore ottimizzazione del layout. La modifica di queste proprietà ha un costo e consuma memoria, anche quando vengono impostate sui valori predefiniti.
- Evitare [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) di utilizzare un quando possibile. Causa un notevole aumento del lavoro per la CPU.
- Quando si [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)utilizza un [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) oggetto , evitare di utilizzare la proprietà quando possibile.
- Quando si [`StackLayout`](xref:Xamarin.Forms.StackLayout)utilizza un oggetto , [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands)assicurarsi che solo un figlio sia impostato su . Questa proprietà garantisce che l'elemento figlio specificato occupi lo spazio più ampio che `StackLayout` è in grado di assegnare ed è dispendioso eseguire questi calcoli più di una volta.
- Evitare di chiamare uno [`Layout`](xref:Xamarin.Forms.Layout) dei metodi della classe, in quanto comportano l'esecuzione di calcoli di layout costosi. Al contrario, è probabile che sia possibile ottenere [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) il comportamento di layout desiderato impostando le proprietà e . In alternativa, sottoclasse della [`Layout<View>`](xref:Xamarin.Forms.Layout`1) classe per ottenere il comportamento di layout desiderato.
- Non aggiornare [`Label`](xref:Xamarin.Forms.Label) le istanze più frequentemente del necessario, poiché la modifica delle dimensioni dell'etichetta può comportare il ricalcolo dell'intero layout dello schermo.
- Non impostare [`Label.VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) la proprietà a meno che non sia necessario.
- Impostare [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) l'istanza di tutte [`Label`](xref:Xamarin.Forms.Label) le istanze su [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) quando possibile.

## <a name="use-asynchronous-programming"></a>Usare la programmazione asincrona

La velocità di risposta complessiva dell'applicazione può essere migliorata e i colli di bottiglia delle prestazioni spesso evitati, utilizzando la programmazione asincrona. In .NET, il [modello asincrono basato su attività (TAP, Task-based Asynchronous Pattern)](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap) è il modello di progettazione consigliato per le operazioni asincrone. Tuttavia, un uso non corretto del TAP può causare applicazioni non performanti. Pertanto, le seguenti linee guida devono essere seguite quando si utilizza il TAP.

### <a name="fundamentals"></a>Nozioni fondamentali

- Comprendere il ciclo di vita `TaskStatus` dell'attività, rappresentato dall'enumerazione. Per ulteriori informazioni, vedere [Il significato di TaskStatus](https://devblogs.microsoft.com/pfxteam/the-meaning-of-taskstatus/) e [Task status](/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap#task-status).
- Utilizzare `Task.WhenAll` il metodo per attendere in modo asincrono il `await` completamento di più operazioni asincrone, anziché singolarmente una serie di operazioni asincrone. Per ulteriori informazioni, vedere [Task.WhenAll](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall).
- Utilizzare `Task.WhenAny` il metodo per attendere in modo asincrono il completamento di una delle più operazioni asincrone. Per ulteriori informazioni, vedere [Task.WhenAny](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskwhenall).
- Utilizzare `Task.Delay` il metodo `Task` per produrre un oggetto che termina dopo il tempo specificato. Ciò è utile per scenari quali il polling dei dati e il ritardo nella gestione dell'input dell'utente per un tempo predeterminato. Per ulteriori informazioni, vedere [Task.Delay](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskdelay).
- Eseguire operazioni intensive della CPU sincrone nel pool di thread con il `Task.Run` metodo . Questo metodo è un `TaskFactory.StartNew` collegamento per il metodo, con gli argomenti ottimali impostati. Per ulteriori informazioni, vedere [Task.Run](/dotnet/standard/asynchronous-programming-patterns/consuming-the-task-based-asynchronous-pattern#taskrun).
- Evitare di tentare di creare costruttori asincroni. Usare invece gli eventi del `await` ciclo di vita o separare la logica di inizializzazione per eseguire correttamente qualsiasi inizializzazione. Per altre informazioni, vedere [Costruttori asincroni](https://blog.stephencleary.com/2013/01/async-oop-2-constructors.html) in blog.stephencleary.com.
- Usare il modello di attività differita per evitare l'attesa del completamento delle operazioni asincrone durante l'avvio dell'applicazione. Per ulteriori informazioni, vedere [AsyncLazy](https://devblogs.microsoft.com/pfxteam/asynclazyt/).
- Creare un wrapper di attività per le operazioni asincrone `TaskCompletionSource<T>` esistenti, che non utilizzano TAP, creando oggetti. Questi oggetti ottengono `Task` i vantaggi della programmabilità e consentono di `Task`controllare la durata e il completamento dell'oggetto associato. Per ulteriori informazioni, vedere [La natura di TaskCompletionSource](https://devblogs.microsoft.com/pfxteam/the-nature-of-taskcompletionsourcetresult/).
 
- Restituisce `Task` un oggetto, anziché `Task` restituire un oggetto atteso, quando non è necessario elaborare il risultato di un'operazione asincrona. Questo è più performante a causa di meno cambio di contesto in esecuzione.
- Utilizzare la libreria di flussi di dati di Task Parallel Library (TPL) in scenari quali l'elaborazione dei dati non appena diventano disponibili o quando si dispone di più operazioni che devono comunicare tra loro in modo asincrono. Per ulteriori informazioni, vedere [Flusso di dati (Task Parallel Library)](/dotnet/standard/parallel-programming/dataflow-task-parallel-library).

### <a name="ui"></a>UI

- Chiamare una versione asincrona di un'API, se disponibile. Questo consente di mantenere sbloccato il thread dell'interfaccia utente, contribuendo a migliorare l'esperienza utente dell'applicazione.
- Aggiornare gli elementi dell'interfaccia utente con i dati delle operazioni asincrone nel thread dell'interfaccia utente, per evitare la thrown. Tuttavia, gli `ListView.ItemsSource` aggiornamenti alla proprietà verranno automaticamente sottoposti a marshalling nel thread dell'interfaccia utente. Per informazioni su come determinare se il codice è in esecuzione nel thread dell'interfaccia utente, vedere [Xamarin.Essentials: MainThread](~/essentials/main-thread.md?content=xamarin/xamarin-forms).

    > [!IMPORTANT]
    > Tutte le proprietà del controllo aggiornate tramite l'associazione dati verrà eseguito automaticamente il marshalling nel thread dell'interfaccia utente.

### <a name="error-handling"></a>Gestione degli errori

- Informazioni sulla gestione asincrona delle eccezioni. Le eccezioni non gestite generate dal codice in esecuzione in modo asincrono vengono propagate al thread chiamante, ad eccezione di determinati scenari. Per ulteriori informazioni, vedere [Gestione delle eccezioni (Task Parallel Library)](/dotnet/standard/parallel-programming/exception-handling-task-parallel-library).
- Evitare `async void` di creare `async Task` metodi e creare invece metodi. Ciò consente una gestione più semplice degli errori, la composizione e la verificabilità. L'eccezione a questa linea guida sono `void`i gestori eventi asincroni, che devono restituire . Per ulteriori informazioni, vedere [Avoid Async Void](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#avoid-async-void).
- Non combinare il blocco e il `Task.Wait` `Task.Result`codice `GetAwaiter().GetResult` asincrono chiamando i metodi , , o , in quanto possono causare un deadlock. Tuttavia, se questa linea guida deve essere violata, l'approccio preferito consiste nel chiamare il `GetAwaiter().GetResult` metodo perché mantiene le eccezioni delle attività. Per ulteriori informazioni, vedere [Async All the Way](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#async-all-the-way) e Gestione delle [eccezioni delle attività in .NET 4.5](https://devblogs.microsoft.com/pfxteam/task-exception-handling-in-net-4-5/).
- Utilizzare `ConfigureAwait` il metodo quando possibile, per creare codice privo di contesto. Il codice senza contesto ha prestazioni migliori per le applicazioni mobili ed è una tecnica utile per evitare deadlock quando si lavora con una codebase parzialmente asincrona. Per ulteriori informazioni, vedere [Configurare il contesto](/archive/msdn-magazine/2013/march/async-await-best-practices-in-asynchronous-programming#configure-context).
- Usare le attività di *continuazione* per funzionalità quali la gestione delle eccezioni generate dall'operazione asincrona precedente e l'annullamento di una continuazione prima dell'avvio o durante l'esecuzione. Per ulteriori informazioni, vedere [Concatenamento di attività tramite attività continue](/dotnet/standard/parallel-programming/chaining-tasks-by-using-continuation-tasks).
- Utilizzare un'implementazione asincrona `ICommand` quando `ICommand`le operazioni asincrone vengono richiamate dall'oggetto . Ciò garantisce che tutte le eccezioni nella logica di comando asincrona possono essere gestite. Per altre informazioni, vedere [Programmazione asincrona: modelli per applicazioni MVVM asincrone: comandi](/archive/msdn-magazine/2014/april/async-programming-patterns-for-asynchronous-mvvm-applications-commands).

## <a name="choose-a-dependency-injection-container-carefully"></a>Scegliere con attenzione un contenitore di inserimento delle dipendenze

I contenitori di inserimento delle dipendenze introducono nelle applicazioni per dispositivi mobili ulteriori vincoli relativi alle prestazioni. La registrazione e la risoluzione dei tipi con un contenitore comportano un costo in termini di prestazioni perché il contenitore usa la reflection per la creazione di ogni tipo, soprattutto se le dipendenze vengono ricostruite per la navigazione di ogni pagina nell'app. Se le dipendenze presenti sono numerose o complete, il costo della creazione può aumentare in modo significativo. Inoltre, la registrazione dei tipi, che in genere viene eseguita durante l'avvio dell'applicazione, può avere un notevole effetto sul tempo di avvio, a seconda del contenitore usato.

In alternativa, l'inserimento delle dipendenze può essere reso più efficiente implementandolo manualmente tramite factory.

## <a name="create-shell-applications"></a>Creare applicazioni shell

Le applicazioni shell di Xamarin.Forms offrono una solida esperienza di navigazione basata su riquadri a comparsa e schede. È utile implementare con la shell l'esperienza utente dell'applicazione, se possibile. Le applicazioni shell consentono di evitare un'esperienza di avvio insoddisfacente, perché le pagine vengono create on demand in risposta alla navigazione invece che all'avvio dell'applicazione, come avviene per le applicazioni che usano una classe [TabbedPage](xref:Xamarin.Forms.TabbedPage). Per ulteriori informazioni, vedere [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="use-collectionview-instead-of-listview"></a>Usare CollectionView invece di ListView

[`CollectionView`](xref:Xamarin.Forms.CollectionView)è una vista per la presentazione di elenchi di dati utilizzando diverse specifiche di layout. Fornisce un'alternativa più flessibile ed [`ListView`](xref:Xamarin.Forms.ListView)performante a . Per altre informazioni, vedere [CollectionView di Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/index.md).

## <a name="optimize-listview-performance"></a>Ottimizzare le prestazioni di ListView

Quando [`ListView`](xref:Xamarin.Forms.ListView)si utilizza , è necessario ottimizzare una serie di esperienze utente:

- **Inizializzazione**: l'intervallo di tempo che inizia quando si crea il controllo e termina quando gli elementi sono visualizzati sullo schermo.
- **Scorrimento**: la possibilità di scorrere l'elenco e verificare che l'interfaccia utente non resti indietro rispetto ai movimenti tocco.
- **Interazione** per l'aggiunta, l'eliminazione e la selezione di elementi.

Il [`ListView`](xref:Xamarin.Forms.ListView) controllo richiede un'applicazione per fornire dati e modelli di cella. Il modo in cui viene eseguita questa operazione ha un notevole impatto sulle prestazioni del controllo. Per altre informazioni, vedere [Prestazioni di ListView](~/xamarin-forms/user-interface/listview/performance.md).

## <a name="optimize-image-resources"></a>Ottimizzare le risorse immagine

Visualizzare le risorse immagine può aumentare notevolmente il footprint della memoria di un'applicazione. Di conseguenza, tali risorse devono essere create solo se necessario e rilasciate non appena l'applicazione non le richiede più. Ad esempio, se un'applicazione visualizza un'immagine leggendone i dati da un flusso, verificare che tale flusso venga creato solo quando è necessario e venga rilasciato quando non è più necessario. Ciò può essere ottenuto creando il flusso quando [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) viene creata la pagina o quando viene [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) generato l'evento e quindi eliminando il flusso quando viene generato l'evento.

Quando si scarica un'immagine [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)) per la visualizzazione con il [`UriImageSource.CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) metodo , `true`memorizzare nella cache l'immagine scaricata assicurandosi che la proprietà sia impostata su . Per altre informazioni, vedere l'articolo sulla [gestione delle immagini](~/xamarin-forms/user-interface/images.md).

Per altre informazioni, vedere [Optimize Image Resources](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages) (Ottimizzare le risorse immagine).

## <a name="reduce-the-visual-tree-size"></a>Ridurre le dimensioni della struttura ad albero visuale

La riduzione del numero di elementi in una pagina consente di velocizzare il rendering della pagina. Esistono due tecniche principali per ottenere questo risultato. La prima consiste nel nascondere gli elementi che non sono visibili. La [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) proprietà di ogni elemento determina se l'elemento deve far parte della struttura ad albero visuale o meno. Di conseguenza, se un elemento non è visibile perché è nascosto dietro altri elementi, rimuovere l'elemento o impostarne la proprietà `IsVisible` su `false`.

La seconda tecnica è rimuovere gli elementi non necessari. Ad esempio, l'esempio di codice [`Label`](xref:Xamarin.Forms.Label) seguente mostra un layout di pagina contenente più oggetti:For example, the following code example shows a page layout containing multiple objects:

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
- [Compressione layout](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [Shell di Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [CollectionView di Xamarin.Forms](~/xamarin-forms/user-interface/collectionview/index.md)
- [Prestazioni di ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [Ottimizzazione delle risorse immagine](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages)
- [Stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Personalizzazione di controlli in ogni piattaforma](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
