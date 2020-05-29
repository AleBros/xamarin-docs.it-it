---
title: " Xamarin.Forms ScrollView" Description: " Xamarin.Forms ScrollView è un layout in grado di scorrere il contenuto".
ms. prod: Novell MS. AssetID: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 05/27/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-scrollview"></a>Xamarin.FormsScrollView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-scrollviewdemos)

[![Xamarin.FormsScrollView](scrollview-images/layouts.png "[! OP. NO-LOC (Novell. Forms)] ScrollView")](scrollview-images/layouts-large.png#lightbox "[! OP. NO-LOC (Novell. Forms)] ScrollView")

[`ScrollView`](xref:Xamarin.Forms.ScrollView)è un layout in grado di scorrere il contenuto. La `ScrollView` classe deriva dalla [`Layout`](xref:Xamarin.Forms.Layout) classe e, per impostazione predefinita, scorre il relativo contenuto verticalmente. Un oggetto `ScrollView` può avere solo un singolo elemento figlio, sebbene possa trattarsi di altri layout.

> [!WARNING]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView)gli oggetti non devono essere annidati. Inoltre, `ScrollView` gli oggetti non devono essere annidati con altri controlli che forniscono lo scorrimento, ad esempio [`CollectionView`](xref:Xamarin.Forms.CollectionView) , [`ListView`](xref:Xamarin.Forms.ListView) e [`WebView`](xref:Xamarin.Forms.WebView) .

[`ScrollView`](xref:Xamarin.Forms.ScrollView)definisce le proprietà seguenti:

- [`Content`](xref:Xamarin.Forms.ScrollView.Content), di tipo [`View`](xref:Xamarin.Forms.View) , rappresenta il contenuto da visualizzare nell'oggetto [`ScrollView`](xref:Xamarin.Forms.ScrollView) .
- [`ContentSize`](xref:Xamarin.Forms.ScrollView), di tipo [`Size`](xref:Xamarin.Forms.Size) , rappresenta le dimensioni del contenuto. Questa proprietà è di sola lettura.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView), di tipo [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) , rappresenta quando la barra di scorrimento orizzontale è visibile.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation), di tipo [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) , rappresenta la direzione di scorrimento di [`ScrollView`](xref:Xamarin.Forms.ScrollView) . Il valore predefinito di questa proprietà è `Vertical`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollX), di tipo `double` , indica la posizione di scorrimento X corrente. Il valore predefinito di questa proprietà di sola lettura è 0.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollY), di tipo `double` , indica la posizione di scorrimento Y corrente. Il valore predefinito di questa proprietà di sola lettura è 0.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView), di tipo [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) , rappresenta quando la barra di scorrimento verticale è visibile.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, ad eccezione della [`Content`](xref:Xamarin.Forms.ScrollView.Content) proprietà, il che significa che possono essere destinazioni di data binding e stile.

La [`Content`](xref:Xamarin.Forms.ScrollView.Content) proprietà è la [`ContentProperty`](xref:Xamarin.Forms.ContentPropertyAttribute) della [`ScrollView`](xref:Xamarin.Forms.ScrollView) classe e pertanto non deve essere impostata in modo esplicito da XAML.

> [!TIP]
> Per ottenere le migliori prestazioni di layout possibili, seguire le linee guida in [ottimizzare le prestazioni del layout](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

## <a name="scrollview-as-a-root-layout"></a>ScrollView come layout radice

Un oggetto [`ScrollView`](xref:Xamarin.Forms.ScrollView) può avere solo un singolo elemento figlio, che può essere costituito da altri layout. È quindi comune che un `ScrollView` sia il layout radice in una pagina. Per scorrere il contenuto figlio, [`ScrollView`](xref:Xamarin.Forms.ScrollView) Calcola la differenza tra l'altezza del contenuto e la relativa altezza. Questa differenza è la quantità che `ScrollView` può scorrere il contenuto dell'oggetto.

Un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) sarà spesso l'elemento figlio di un oggetto `ScrollView` . In questo scenario, la proprietà `ScrollView` `StackLayout` è alta come la somma delle altezze degli elementi figlio. Quindi `ScrollView` , può determinare la quantità di cui è possibile scorrere il contenuto. Per ulteriori informazioni su `StackLayout` , vedere [ Xamarin.Forms StackLayout](stacklayout.md).

> [!CAUTION]
> In un oggetto verticale [`ScrollView`](xref:Xamarin.Forms.ScrollView) evitare di impostare la `VerticalOptions` proprietà su `Start` , `Center` o `End` . In questo modo si indica che l'oggetto deve essere `ScrollView` solo l'altezza, che può essere pari a zero. Sebbene Xamarin.Forms protegga da questa evenienza, è preferibile evitare il codice che suggerisce qualcosa che non si vuole eseguire.

Nell'esempio di codice XAML seguente è presente un [`ScrollView`](xref:Xamarin.Forms.ScrollView) come layout radice in una pagina:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ScrollViewDemos"
             x:Class="ScrollViewDemos.Views.ColorListPage"
             Title="ScrollView demo">
    <ScrollView>
        <StackLayout BindableLayout.ItemsSource="{x:Static local:NamedColor.All}">
            <BindableLayout.ItemTemplate>
                <DataTemplate>
                    <StackLayout Orientation="Horizontal">
                        <BoxView Color="{Binding Color}"
                                 HeightRequest="32"
                                 WidthRequest="32"
                                 VerticalOptions="Center" />
                        <Label Text="{Binding FriendlyName}"
                               FontSize="24"
                               VerticalOptions="Center" />
                    </StackLayout>
                </DataTemplate>
            </BindableLayout.ItemTemplate>
        </StackLayout>
    </ScrollView>
</ContentPage>
```

In questo esempio il [`ScrollView`](xref:Xamarin.Forms.ScrollView) relativo contenuto è impostato su un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) che utilizza un layout associabile per visualizzare i [`Color`](xref:Xamarin.Forms.Color) campi definiti da Xamarin.Forms . Per impostazione predefinita, `ScrollView` scorre verticalmente, che Mostra più contenuto:

[![Screenshot di un layout ScrollView radice](scrollview-images/root-layout.png "Layout ScrollView radice")](scrollview-images/root-layout-large.png#lightbox "Layout ScrollView radice")

Il codice C# equivalente è il seguente:

```csharp
public class ColorListPageCode : ContentPage
{
    public ColorListPageCode()
    {
        DataTemplate dataTemplate = new DataTemplate(() =>
        {
            BoxView boxView = new BoxView
            {
                HeightRequest = 32,
                WidthRequest = 32,
                VerticalOptions = LayoutOptions.Center
            };
            boxView.SetBinding(BoxView.ColorProperty, "Color");

            Label label = new Label
            {
                FontSize = 24,
                VerticalOptions = LayoutOptions.Center
            };
            label.SetBinding(Label.TextProperty, "FriendlyName");

            StackLayout horizontalStackLayout = new StackLayout
            {
                Orientation = StackOrientation.Horizontal,
                Children = { boxView, label }
            };
            return horizontalStackLayout;
        });

        StackLayout stackLayout = new StackLayout();
        BindableLayout.SetItemsSource(stackLayout, NamedColor.All);
        BindableLayout.SetItemTemplate(stackLayout, dataTemplate);

        ScrollView scrollView = new ScrollView { Content = stackLayout };

        Title = "ScrollView demo";
        Content = scrollView;
    }
}
```

Per ulteriori informazioni sui layout associabili, vedere [layout associabili in Xamarin.Forms ](bindable-layouts.md).

## <a name="scrollview-as-a-child-layout"></a>ScrollView come layout figlio

Un oggetto [`ScrollView`](xref:Xamarin.Forms.ScrollView) può essere un layout figlio a un layout padre diverso.

Un oggetto [`ScrollView`](xref:Xamarin.Forms.ScrollView) sarà spesso l'elemento figlio di un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Un oggetto `ScrollView` richiede un'altezza specifica per calcolare la differenza tra l'altezza del contenuto e la propria altezza, con la differenza che l'oggetto `ScrollView` può scorrere il contenuto. Quando un oggetto `ScrollView` è l'elemento figlio di un oggetto `StackLayout` , non riceve un'altezza specifica. Il tipo desidera che sia il più `StackLayout` `ScrollView` breve possibile, ovvero l'altezza del `ScrollView` contenuto o zero. Per gestire questo scenario, la `VerticalOptions` proprietà di `ScrollView` deve essere impostata su `FillAndExpand` . In questo modo, l'oggetto fornirà `StackLayout` a `ScrollView` tutto lo spazio aggiuntivo non richiesto dagli altri elementi figlio e `ScrollView` avrà un'altezza specifica.

Nell'esempio di codice XAML seguente è presente un oggetto [`ScrollView`](xref:Xamarin.Forms.ScrollView) come layout figlio di un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ScrollViewDemos.Views.BlackCatPage"
             Title="ScrollView as a child layout demo">
    <StackLayout Margin="20">
        <Label Text="THE BLACK CAT by Edgar Allan Poe"
               FontSize="Medium"
               FontAttributes="Bold"
               HorizontalOptions="Center" />
        <ScrollView VerticalOptions="FillAndExpand">
            <StackLayout>
                <Label Text="FOR the most wild, yet most homely narrative which I am about to pen, I neither expect nor solicit belief. Mad indeed would I be to expect it, in a case where my very senses reject their own evidence. Yet, mad am I not -- and very surely do I not dream. But to-morrow I die, and to-day I would unburthen my soul. My immediate purpose is to place before the world, plainly, succinctly, and without comment, a series of mere household events. In their consequences, these events have terrified -- have tortured -- have destroyed me. Yet I will not attempt to expound them. To me, they have presented little but Horror -- to many they will seem less terrible than barroques. Hereafter, perhaps, some intellect may be found which will reduce my phantasm to the common-place -- some intellect more calm, more logical, and far less excitable than my own, which will perceive, in the circumstances I detail with awe, nothing more than an ordinary succession of very natural causes and effects." />
                <!-- More Label objects go here -->
            </StackLayout>
        </ScrollView>
    </StackLayout>
</ContentPage>
```

In questo esempio sono presenti due [`StackLayout`](xref:Xamarin.Forms.StackLayout) oggetti. Il primo `StackLayout` è l'oggetto layout radice, che ha un [`Label`](xref:Xamarin.Forms.Label) oggetto e un [`ScrollView`](xref:Xamarin.Forms.ScrollView) come elementi figlio. `ScrollView`Dispone di un oggetto `StackLayout` come contenuto, con l'oggetto `StackLayout` che contiene più `Label` oggetti. Questa disposizione garantisce che il primo `Label` sia sempre sullo schermo, mentre è possibile scorrere il testo visualizzato dagli altri `Label` oggetti:

[![Screenshot di un layout ScrollView figlio](scrollview-images/child-layout.png "Layout ScrollView figlio")](scrollview-images/child-layout-large.png#lightbox "Layout ScrollView figlio")

Il codice C# equivalente è il seguente:

```csharp
public class BlackCatPageCS : ContentPage
{
    public BlackCatPageCS()
    {
        Label titleLabel = new Label
        {
            Text = "THE BLACK CAT by Edgar Allan Poe",
            // More properties set here to define the Label appearance
        };

        ScrollView scrollView = new ScrollView
        {
            VerticalOptions = LayoutOptions.FillAndExpand,
            Content = new StackLayout
            {
                Children =
                {
                    new Label
                    {
                        Text = "FOR the most wild, yet most homely narrative which I am about to pen, I neither expect nor solicit belief. Mad indeed would I be to expect it, in a case where my very senses reject their own evidence. Yet, mad am I not -- and very surely do I not dream. But to-morrow I die, and to-day I would unburthen my soul. My immediate purpose is to place before the world, plainly, succinctly, and without comment, a series of mere household events. In their consequences, these events have terrified -- have tortured -- have destroyed me. Yet I will not attempt to expound them. To me, they have presented little but Horror -- to many they will seem less terrible than barroques. Hereafter, perhaps, some intellect may be found which will reduce my phantasm to the common-place -- some intellect more calm, more logical, and far less excitable than my own, which will perceive, in the circumstances I detail with awe, nothing more than an ordinary succession of very natural causes and effects.",
                    },
                    // More Label objects go here
                }
            }
        };

        Title = "ScrollView as a child layout demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = { titleLabel, scrollView }
        };
    }
}
```

## <a name="orientation"></a>Orientamento

[`ScrollView`](xref:Xamarin.Forms.ScrollView)dispone [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) di una proprietà che rappresenta la direzione di scorrimento di `ScrollView` . Questa proprietà è di tipo [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) , che definisce i membri seguenti:

- `Vertical`indica che l'oggetto `ScrollView` scorre verticalmente. Questo membro è il valore predefinito della [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) Proprietà.
- `Horizontal`indica che `ScrollView` scorre orizzontalmente.
- `Both`indica che `ScrollView` scorre orizzontalmente e verticalmente.
- `Neither`indica che l'oggetto `ScrollView` non scorre.

> [!TIP]
> Lo scorrimento può essere disabilitato impostando la [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) proprietà su `Neither` .

## <a name="detect-scrolling"></a>Rileva scorrimento

[`ScrollView`](xref:Xamarin.Forms.ScrollView)definisce un [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) evento generato per indicare che si è verificato lo scorrimento. L' [`ScrolledEventArgs`](xref:Xamarin.Forms.ScrolledEventArgs) oggetto che accompagna l' `Scrolled` evento dispone `ScrollX` `ScrollY` di proprietà e, entrambe di tipo `double` .

> [!IMPORTANT]
> Le `ScrolledEventArgs.ScrollX` `ScrolledEventArgs.ScrollY` proprietà e possono avere valori negativi, a causa dell'effetto di rimbalzo che si verifica quando lo scorrimento torna all'inizio di un oggetto [`ScrollView`](xref:Xamarin.Forms.ScrollView) .

Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto [`ScrollView`](xref:Xamarin.Forms.ScrollView) che imposta un gestore eventi per l' [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) evento:

```xaml
<ScrollView Scrolled="OnScrollViewScrolled">
        ...
</ScrollView>
```

Il codice C# equivalente è il seguente:

```csharp
ScrollView scrollView = new ScrollView();
scrollView.Scrolled += OnScrollViewScrolled;
```

In questo esempio, il `OnScrollViewScrolled` gestore eventi viene eseguito quando viene [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) generato l'evento:

```csharp
void OnScrollViewScrolled(object sender, ScrolledEventArgs e)
{
    Console.WriteLine($"ScrollX: {e.ScrollX}, ScrollY: {e.ScrollY}");
}
```

In questo esempio, il `OnScrollViewScrolled` gestore eventi restituisce i valori dell' [`ScrolledEventArgs`](xref:Xamarin.Forms.ScrolledEventArgs) oggetto che accompagna l'evento.

> [!NOTE]
> L' [`Scrolled`](xref:Xamarin.Forms.ScrollView.Scrolled) evento viene generato per gli scorrimenti avviati dall'utente e per gli scorrimenti a livello di codice.

## <a name="scroll-programmatically"></a>Scorri a livello di codice

[`ScrollView`](xref:Xamarin.Forms.ScrollView)definisce due [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) metodi, che scorrono in modo asincrono `ScrollView` . Uno degli overload scorre fino a una posizione specificata nell'oggetto `ScrollView` , mentre l'altro scorre un elemento specificato nella visualizzazione. Entrambi gli overload hanno un argomento aggiuntivo che può essere usato per indicare se aggiungere un'animazione allo scorrimento.

> [!IMPORTANT]
> [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*)Se la [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) proprietà è impostata su, i metodi non comporteranno lo scorrimento `Neither` .

### <a name="scroll-a-position-into-view"></a>Scorrere una posizione nella visualizzazione

È possibile scorrere una posizione all'interno di un oggetto [`ScrollView`](xref:Xamarin.Forms.ScrollView) con il [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) metodo che accetta gli `double` `x` `y` argomenti e. Dato un `ScrollView` oggetto verticale denominato `scrollView` , l'esempio seguente mostra come scorrere fino a 150 unità indipendenti dal dispositivo dalla parte superiore di `ScrollView` :

```csharp
await scrollView.ScrollToAsync(0, 150, true);
```

Il terzo argomento di [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) è l' `animated` argomento, che determina se un'animazione di scorrimento viene visualizzata quando si scorre un oggetto a livello di codice [`ScrollView`](xref:Xamarin.Forms.ScrollView) .

### <a name="scroll-an-element-into-view"></a>Scorrere un elemento nella visualizzazione

Un elemento all'interno di un oggetto [`ScrollView`](xref:Xamarin.Forms.ScrollView) può essere spostato nella visualizzazione con il [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) metodo che accetta gli [`Element`](xref:Xamarin.Forms.Element) [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) argomenti e. Dato un `ScrollView` nome verticale `scrollView` e un oggetto [`Label`](xref:Xamarin.Forms.Label) denominato `label` , nell'esempio seguente viene illustrato come scorrere un elemento nella visualizzazione:

```csharp
await scrollView.ScrollToAsync(label, ScrollToPosition.End, true);
```

Il terzo argomento di [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) è l' `animated` argomento, che determina se un'animazione di scorrimento viene visualizzata quando si scorre un oggetto a livello di codice [`ScrollView`](xref:Xamarin.Forms.ScrollView) .

Quando si scorre un elemento nella visualizzazione, è possibile impostare la posizione esatta dell'elemento dopo il completamento dello scorrimento con il secondo argomento, `position` , del [`ScrollToAsync`](xref:Xamarin.Forms.ScrollView.ScrollToAsync*) metodo. Questo argomento accetta un [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) membro di enumerazione:

- `MakeVisible`indica che l'elemento deve essere spostato fino a quando non è visibile nell'oggetto `ScrollView` .
- `Start`indica che l'elemento deve essere spostato all'inizio dell'oggetto `ScrollView` .
- `Center`indica che l'elemento deve essere spostato al centro di `ScrollView` .
- `End`indica che l'elemento deve essere spostato alla fine di `ScrollView` .

## <a name="scroll-bar-visibility"></a>Visibilità della barra di scorrimento

[`ScrollView`](xref:Xamarin.Forms.ScrollView)definisce [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView) le [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView) proprietà e, supportate da proprietà associabili. Queste proprietà ottengono o impostano un [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibility) valore di enumerazione che indica se la barra di scorrimento orizzontale o verticale è visibile. L'enumerazione `ScrollBarVisibility` definisce i membri seguenti:

- `Default`indica il comportamento predefinito della barra di scorrimento per la piattaforma e è il valore predefinito delle `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` proprietà e.
- `Always`indica che le barre di scorrimento saranno visibili, anche quando il contenuto si adatterà alla visualizzazione.
- `Never`indica che le barre di scorrimento non saranno visibili, anche se il contenuto non rientra nella visualizzazione.

## <a name="related-links"></a>Collegamenti correlati

- [Demo di ScrollView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-scrollviewdemos)
- [Xamarin.FormsStackLayout](stacklayout.md)
- [Layout associabili inXamarin.Forms](bindable-layouts.md)
