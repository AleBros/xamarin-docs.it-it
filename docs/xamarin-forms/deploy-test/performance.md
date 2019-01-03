---
title: Prestazioni di Xamarin.Forms
description: Esistono molte tecniche per incrementare le prestazioni delle applicazioni Xamarin.Forms. Insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione. Questo articolo descrive queste tecniche e le relative caratteristiche.
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 347d0eebf7340bb8dc7234275d0f58acf7ab16c6
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061031"
---
# <a name="xamarinforms-performance"></a>Prestazioni di Xamarin.Forms

_Esistono molte tecniche per incrementare le prestazioni delle applicazioni Xamarin.Forms. Nel loro insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione. Questo articolo descrive queste tecniche e le relative caratteristiche._

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Evoluzione 2016: ottimizzazione delle prestazioni delle app con Xamarin.Forms**

## <a name="overview"></a>Panoramica

Le prestazioni insoddisfacenti di un'applicazione si manifestano in molti modi. Può sembrare che l'applicazione non risponda, lo scorrimento diventa lento e si riduce la durata della batteria. Tuttavia, l'ottimizzazione delle prestazioni implica più della semplice implementazione di codice efficiente. Deve essere considerata anche l'esperienza dell'utente in termini di prestazioni dell'applicazione. Ad esempio, verificando che le operazioni vengano eseguite senza impedire all'utente di eseguire altre attività può contribuire a migliorare l'esperienza dell'utente.

Esistono diverse tecniche per migliorare le prestazioni, e le prestazioni percepite, di un'applicazione Xamarin.Forms. e comprendono:

- [Abilitare il compilatore XAML](#xamlc)
- [Scegliere il layout corretto](#correctlayout)
- [Abilitare la compressione del layout](#layoutcompression)
- [Usare renderer veloci](#fastrenderers)
- [Ridurre i binding non necessari](#databinding)
- [Ottimizzare le prestazioni dei layout](#optimizelayout)
- [Ottimizzare le prestazioni di ListView](#optimizelistview)
- [Ottimizzare le risorse immagine](#optimizeimages)
- [Ridurre le dimensioni della struttura ad albero visuale](#visualtree)
- [Ridurre le dimensioni del dizionario risorse dell'applicazione](#resourcedictionary)
- [Usare il modello di renderer personalizzato](#rendererpattern)

> [!NOTE]
>  Prima di leggere questo articolo, è consigliabile vedere [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md), che illustra le tecniche non specifiche di una piattaforma che consentono di migliorare l'utilizzo della memoria e le prestazioni delle applicazioni compilate con la piattaforma Xamarin.

<a name="xamlc" />

## <a name="enable-the-xaml-compiler"></a>Abilitare il compilatore XAML

Se necessario, il linguaggio XAML può essere compilato direttamente nel linguaggio intermedio (IL) con il compilatore XAML (XAMLC). XAMLC offre alcuni vantaggi:

- Esegue il controllo del codice XAML in fase di compilazione, notificando all'utente eventuali errori.
- Rimuove parte del tempo di carico e di creazione dell'istanza per gli elementi XAML.
- Consente di ridurre le dimensioni del file dell'assembly finale non includendo più i file XAML.

XAMLC è disabilitato per impostazione predefinita per garantire la compatibilità con le versioni precedenti. Tuttavia, può essere abilitato sia a livello di classe che di assembly. Per altre informazioni, vedere l'articolo sulla [compilazione XAML](~/xamarin-forms/xaml/xamlc.md).

<a name="correctlayout" />

## <a name="choose-the-correct-layout"></a>Scegliere il layout corretto

Un layout in grado di visualizzare più elementi figlio, ma che ne include solo uno è dispendioso. Ad esempio, il codice riportato di seguito indica un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) con un singolo elemento figlio:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <ContentPage.Content>
        <StackLayout>
            <Image Source="waterfront.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Questo è dispendioso e l'elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) deve essere rimosso, come illustra l'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <ContentPage.Content>
        <Image Source="waterfront.jpg" />
    </ContentPage.Content>
</ContentPage>
```

Inoltre, non tentare di riprodurre l'aspetto di un layout specifico usando combinazioni di altri layout, poiché in questo modo vengono eseguiti calcoli di layout non necessari. Ad esempio, non tentare di riprodurre un layout [`Grid`](xref:Xamarin.Forms.Grid) usando una combinazione di istanze [`StackLayout`](xref:Xamarin.Forms.StackLayout). Il codice seguente è un esempio di questa prassi non corretta:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <ContentPage.Content>
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
    </ContentPage.Content>
</ContentPage>
```

L'operazione risulta dispendiosa poiché vengono eseguiti calcoli di layout non necessari. Il layout può essere invece realizzato in modo ottimale usando un elemento [`Grid`](xref:Xamarin.Forms.Grid), come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <ContentPage.Content>
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
    </ContentPage.Content>
</ContentPage>
```

<a name="layoutcompression" />

## <a name="enable-layout-compression"></a>Abilitare la compressione dei layout

La compressione dei layout rimuove i layout specificati dalla struttura ad albero visuale, nel tentativo di migliorare le prestazioni del rendering della pagina. Il miglioramento delle prestazioni offerto varia in base alla complessità di una pagina, alla versione del sistema operativo in uso e al dispositivo in cui viene eseguita l'applicazione. Tuttavia, le prestazioni miglioreranno in modo più evidente nei dispositivi meno recenti. Per altre informazioni, vedere [Compressione del layout](~/xamarin-forms/user-interface/layouts/layout-compression.md).

<a name="fastrenderers" />

## <a name="use-fast-renderers"></a>Usare renderer veloci

I renderer veloci riducono i costi di ingrandimento e rendering dei controlli di Xamarin.Forms in Android appiattendo la gerarchia dei controllo nativi risultante. Questo migliora ulteriormente le prestazioni poiché vengono creati meno oggetti e quindi si ottiene una struttura ad albero visuale meno complessa e un minore consumo di memoria. Per altre informazioni, vedere [Renderer veloci](~/xamarin-forms/internals/fast-renderers.md).

<a name="databinding" />

## <a name="reduce-unnecessary-bindings"></a>Ridurre i binding non necessari

Non usare i binding per il contenuto che può essere impostato facilmente in modo statico. Non vi è alcun vantaggio nell'associare i dati che non richiedono l'associazione poiché i binding non hanno un costo contenuto. Ad esempio, l'impostazione di `Button.Text = "Accept"` presenta un overhead minore rispetto al binding di [`Button.Text`](xref:Xamarin.Forms.Button.Text) a una proprietà `string` di ViewModel con valore "Accept".

<a name="optimizelayout" />

## <a name="optimize-layout-performance"></a>Ottimizzare le prestazioni dei layout

Xamarin.Forms 2 ha introdotto un motore di layout ottimizzato che influisce sugli aggiornamenti del layout. Per ottenere le migliori prestazioni possibili del layout, attenersi alle seguenti indicazioni:

- Ridurre la profondità delle gerarchie di layout specificando i valori delle proprietà [`Margin`](xref:Xamarin.Forms.View.Margin) consentendo la creazione di layout con meno visualizzazioni con wrapping. Per altre informazioni, vedere l'articolo sulle proprietà [Margin e Padding](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).
- Quando si usa un elemento [`Grid`](xref:Xamarin.Forms.Grid), provare a verificare che sia impostato il minor numero possibile di righe e colonne sulla dimensione [`Auto`](xref:Xamarin.Forms.GridLength.Auto). Ogni riga o colonna ridimensionata automaticamente causerà l'esecuzione di ulteriori calcoli di layout da parte del motore di layout. Usare invece righe e colonne con dimensioni fisse, se possibile. In alternativa, impostare righe e colonne in modo che occupino una quantità proporzionale di spazio con il valore di enumerazione [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star), purché l'albero padre segua queste linee guida relative al layout.
- Non impostare le proprietà [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) e [`HorizontalOptions`](xref:Xamarin.Forms.View.VerticalOptions) di un layout a meno che non sia richiesto. I valori predefiniti di [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) e [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) consentono la migliore ottimizzazione del layout. La modifica di queste proprietà ha un costo e consuma memoria, anche quando vengono impostate sui valori predefiniti.
- Evitare di usare un elemento [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) quando possibile. Causa un notevole aumento del lavoro per la CPU.
- Quando si usa un elemento [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), evitare di usare la proprietà [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) ovunque sia possibile.
- Se si usa un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout), verificare che sia impostato un solo elemento figlio su [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands). Questa proprietà garantisce che l'elemento figlio specificato occupi lo spazio più ampio che `StackLayout` è in grado di assegnare ed è dispendioso eseguire questi calcoli più di una volta.
- Non chiamare uno dei metodi della classe [`Layout`](xref:Xamarin.Forms.Layout), poiché causano l'esecuzione di calcoli dispendiosi. È invece probabile che si possa ottenere un determinato comportamento del layout impostando le proprietà [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY). In alternativa, sottoclassare la classe [`Layout<View>`](xref:Xamarin.Forms.Layout`1) per ottenere tale comportamento del layout.
- Non aggiornare le istanze di [`Label`](xref:Xamarin.Forms.Label) più spesso di quanto necessario, poiché modificando le dimensioni dell'etichetta è possibile che l'intero layout della schermata venga ricalcolato.
- Non impostare la proprietà [`Label.VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) se non viene richiesto.
- Impostare l'elemento [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) di qualsiasi istanza di [`Label`](xref:Xamarin.Forms.Label) su [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) quando possibile.

<a name="optimizelistview" />

## <a name="optimize-listview-performance"></a>Ottimizzare le prestazioni di ListView

Se si usa un controllo [`ListView`](xref:Xamarin.Forms.ListView) esistono alcune esperienze utente che devono essere ottimizzate:

- **Inizializzazione**: l'intervallo di tempo che inizia quando si crea il controllo e termina quando gli elementi sono visualizzati sullo schermo.
- **Scorrimento**: la possibilità di scorrere l'elenco e verificare che l'interfaccia utente non resti indietro rispetto ai movimenti tocco.
- **Interazione** per l'aggiunta, l'eliminazione e la selezione di elementi.

Il controllo [`ListView`](xref:Xamarin.Forms.ListView) richiede un'applicazione per specificare i dati e i modelli di cella. Il modo in cui viene eseguita questa operazione ha un notevole impatto sulle prestazioni del controllo. Per altre informazioni, vedere [Prestazioni di ListView](~/xamarin-forms/user-interface/listview/performance.md).

<a name="optimizeimages" />

## <a name="optimize-image-resources"></a>Ottimizzare le risorse immagine

Visualizzare le risorse immagine può aumentare notevolmente il footprint di memoria dell'app. Di conseguenza, tali risorse devono essere create solo se necessario e rilasciate non appena l'applicazione non le richiede più. Ad esempio, se un'applicazione visualizza un'immagine leggendone i dati da un flusso, verificare che tale flusso venga creato solo quando è necessario e venga rilasciato quando non è più necessario. Questo risultato può essere ottenuto creando il flusso quando viene creata la pagina oppure quando viene generato l'evento [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) e successivamente eliminando il flusso quando viene generato l'evento [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing).

Quando si scarica un'immagine da visualizzare con il metodo [`ImageSource.FromUri`](xref:Xamarin.Forms.ImageSource.FromUri(System.Uri)), per memorizzare nella cache l'immagine scaricata, verificare che la proprietà [`UriImageSource.CachingEnabled`](xref:Xamarin.Forms.UriImageSource.CachingEnabled) sia impostata su `true`. Per altre informazioni, vedere l'articolo sulla [gestione delle immagini](~/xamarin-forms/user-interface/images.md).

Per altre informazioni, vedere [Optimize Image Resources](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages) (Ottimizzare le risorse immagine).

<a name="visualtree" />

## <a name="reduce-the-visual-tree-size"></a>Ridurre le dimensioni della struttura ad albero visuale

La riduzione del numero di elementi in una pagina consente di velocizzare il rendering della pagina. Esistono due tecniche principali per ottenere questo risultato. La prima consiste nel nascondere gli elementi che non sono visibili. La proprietà [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) di ogni elemento determina se l'elemento deve essere parte della struttura ad albero visuale o meno. Di conseguenza, se un elemento non è visibile perché è nascosto dietro altri elementi, rimuovere l'elemento o impostarne la proprietà `IsVisible` su `false`.

La seconda tecnica è rimuovere gli elementi non necessari. Ad esempio, il codice seguente illustra un layout di pagina che visualizza una serie di elementi [`Label`](xref:Xamarin.Forms.Label):

```xaml
<ContentPage.Content>
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
</ContentPage.Content>
```

Si può mantenere lo stesso layout di pagina con un numero ridotto di elementi, come illustra l'esempio di codice seguente:

```xaml
<ContentPage.Content>
  <StackLayout Padding="20,20,0,0" Spacing="25">
    <Label Text="Hello" />
    <Label Text="Welcome to the App!" />
    <Label Text="Downloading Data..." />
  </StackLayout>
</ContentPage.Content>
```

<a name="resourcedictionary" />

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

Tuttavia, il codice XAML specifico di una pagina non deve essere incluso nel dizionario risorse dell'app per evitare che le risorse vengano analizzate all'avvio dell'applicazione anziché quando richiesto da una pagina. Se una risorsa viene usata da una pagina che non è la pagina di avvio, deve essere inserita nel dizionario risorse per quella pagina e in questo modo si riduce il codice XAML analizzato all'avvio dell'applicazione. L'esempio di codice riportato di seguito illustra la risorsa `HeadingLabelStyle`, che è usata solo per una pagina e viene quindi definita nel dizionario risorse della pagina:

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
    <ContentPage.Content>
      ...
    </ContentPage.Content>
</ContentPage>

```

Per altre informazioni sulle risorse dell'applicazione, vedere [`Working with Styles`](~/xamarin-forms/user-interface/styles/index.md).

<a name="rendererpattern" />

## <a name="use-the-custom-renderer-pattern"></a>Usare il modello di renderer personalizzato

La maggior parte delle classi renderer espongono il metodo `OnElementChanged`, che viene chiamato quando si crea un controllo personalizzato di Xamarin.Forms per il rendering del controllo nativo corrispondente. Personalizzare le classi renderer per ogni classe renderer specifica della piattaforma, quindi eseguire l'override di questo metodo per creare un'istanza del controllo nativo e personalizzare il controllo. Il metodo `SetNativeControl` viene usato per creare un'istanza del controllo nativo e assegna anche il riferimento a controllo alla proprietà `Control`.

Tuttavia, in alcune circostanze il metodo `OnElementChanged` può essere chiamato più volte. Di conseguenza, per evitare perdite di memoria, che possono avere un impatto sulle prestazioni, prestare attenzione quando si crea un'istanza di un nuovo controllo nativo. L'approccio da usare quando si crea un'istanza di un nuovo controllo nativo in un renderer personalizzato è illustrato nell'esempio di codice seguente:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

Per un nuovo controllo nativo l'istanza deve essere creata solo una volta, quando la proprietà `Control` è `null`. Configurare il controllo e sottoscrivere i gestori degli eventi solo quando il renderer personalizzato è allegato a un nuovo elemento di Xamarin.Forms. Analogamente, per i gestori degli eventi sottoscritti l'iscrizione deve essere annullata solo in caso di modifica dell'elemento allegato al renderer. L'adozione di questo approccio consente di creare un renderer personalizzato dalle prestazioni ottimali che non subisce perdite di memoria.

Per altre informazioni sui renderer personalizzati, vedere l'articolo relativo alla [personalizzazione dei controlli su ogni piattaforma](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto e illustrato le tecniche per ottimizzare le prestazioni delle applicazioni Xamarin.Forms. Nel loro insieme, queste tecniche possono ridurre notevolmente il carico di lavoro di una CPU e la quantità di memoria usata da un'applicazione.


## <a name="related-links"></a>Collegamenti correlati

- [Prestazioni multipiattaforma](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Prestazioni di ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [Renderer veloci](~/xamarin-forms/internals/fast-renderers.md)
- [Compressione del layout](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [XamlCompilation](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [XamlCompilationOptions](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
