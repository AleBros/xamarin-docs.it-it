---
title: " Xamarin.Forms StackLayout" Description: "un StackLayout organizza le visualizzazioni figlio in uno stack unidimensionale, orizzontalmente o verticalmente".
ms. prod: Novell MS. AssetID: 6A91EA70-268C-462C-AAAF-F8DA011403F8 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 05/11/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-stacklayout"></a>Xamarin.FormsStackLayout

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stacklayoutdemos)

[![Xamarin.FormsStackLayout](stacklayout-images/layouts.png "[! OP. NO-LOC (Novell. Forms)] StackLayout")](stacklayout-images/layouts-large.png#lightbox "[! OP. NO-LOC (Novell. Forms)] StackLayout")

Un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) organizza le visualizzazioni figlio in uno stack unidimensionale, orizzontalmente o verticalmente. Per impostazione predefinita, un `StackLayout` è orientato verticalmente. Inoltre, un oggetto `StackLayout` può essere utilizzato come layout padre che contiene altri layout figlio.

La [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe definisce le proprietà seguenti:

- [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation), di tipo [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) , rappresenta la direzione in cui vengono posizionate le visualizzazioni figlio. Il valore predefinito di questa proprietà è `Vertical`.
- [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing), di tipo `double` , indica la quantità di spazio tra ogni visualizzazione figlio. Il valore predefinito di questa proprietà è sei unità indipendenti dal dispositivo.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di data binding e stile.

La [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe deriva dalla `Layout<T>` classe, che definisce una `Children` proprietà di tipo `IList<T>` . La `Children` proprietà è la `ContentProperty` della `Layout<T>` classe e pertanto non deve essere impostata in modo esplicito da XAML.

> [!TIP]
> Per ottenere le migliori prestazioni di layout possibili, seguire le linee guida in [ottimizzare le prestazioni del layout](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

## <a name="vertical-orientation"></a>Orientamento verticale

Nel codice XAML seguente viene illustrato come creare un oggetto orientato verticalmente [`StackLayout`](xref:Xamarin.Forms.StackLayout) che contiene visualizzazioni figlio diverse:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.VerticalStackLayoutPage"
             Title="Vertical StackLayout demo">
    <StackLayout Margin="20">
        <Label Text="Primary colors" />
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <Label Text="Secondary colors" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

In questo esempio viene creata una classe verticale [`StackLayout`](xref:Xamarin.Forms.StackLayout) che contiene [`Label`](xref:Xamarin.Forms.Label) [`BoxView`](xref:Xamarin.Forms.BoxView) gli oggetti e. Per impostazione predefinita, tra le visualizzazioni figlio sono presenti sei unità di spazio indipendenti dal dispositivo:

[![Screenshot di una StackLayout orientata verticalmente](stacklayout-images/vertical.png "StackLayout orientati verticalmente")](stacklayout-images/vertical-large.png#lightbox "StackLayout orientati verticalmente")

Il codice C# equivalente è il seguente:

```csharp
public class VerticalStackLayoutPageCS : ContentPage
{
    public VerticalStackLayoutPageCS()
    {
        Title = "Vertical StackLayout demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Primary colors" },
                new BoxView { Color = Color.Red },
                new BoxView { Color = Color.Yellow },
                new BoxView { Color = Color.Blue },
                new Label { Text = "Secondary colors" },
                new BoxView { Color = Color.Green },
                new BoxView { Color = Color.Orange },
                new BoxView { Color = Color.Purple }
            }
        };
    }
}
```

> [!NOTE]
> Il valore della [`Margin`](xref:Xamarin.Forms.View.Margin) proprietà rappresenta la distanza tra un elemento e i relativi elementi adiacenti. Per altre informazioni, vedere l'articolo sulle proprietà [Margin e Padding](margin-and-padding.md).

## <a name="horizontal-orientation"></a>Orientamento orizzontale

Il codice XAML seguente illustra come creare un oggetto orientato orizzontalmente impostando [`StackLayout`](xref:Xamarin.Forms.StackLayout) la [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) proprietà su `Horizontal` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.HorizontalStackLayoutPage"
             Title="Horizontal StackLayout demo">
    <StackLayout Margin="20"
                 Orientation="Horizontal"
                 HorizontalOptions="Center">
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

In questo esempio viene creato [`StackLayout`](xref:Xamarin.Forms.StackLayout) un [`BoxView`](xref:Xamarin.Forms.BoxView) oggetto contenente oggetti orizzontali, con sei unità di spazio indipendente dal dispositivo tra le visualizzazioni figlio:

[![Screenshot di un StackLayout orientato orizzontalmente](stacklayout-images/horizontal.png "StackLayout orientati orizzontalmente")](stacklayout-images/horizontal-large.png#lightbox "StackLayout orientati orizzontalmente")

Il codice C# equivalente è il seguente:

```csharp
public HorizontalStackLayoutPageCS()
{
    Title = "Horizontal StackLayout demo";
    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Orientation = StackOrientation.Horizontal,
        HorizontalOptions = LayoutOptions.Center,
        Children =
        {
            new BoxView { Color = Color.Red },
            new BoxView { Color = Color.Yellow },
            new BoxView { Color = Color.Blue },
            new BoxView { Color = Color.Green },
            new BoxView { Color = Color.Orange },
            new BoxView { Color = Color.Purple }
        }
    };
}
```

## <a name="space-between-child-views"></a>Spazio tra le visualizzazioni figlio

La spaziatura tra le visualizzazioni figlio in un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) può essere modificata impostando la [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) proprietà su un `double` valore:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.StackLayoutSpacingPage"
             Title="StackLayout Spacing demo">
    <StackLayout Margin="20"
                 Spacing="0">
        <Label Text="Primary colors" />
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <Label Text="Secondary colors" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

In questo esempio viene creato un oggetto verticale [`StackLayout`](xref:Xamarin.Forms.StackLayout) [`Label`](xref:Xamarin.Forms.Label) [`BoxView`](xref:Xamarin.Forms.BoxView) che contiene gli oggetti che non hanno spaziatura tra di essi:

[![Screenshot di un StackLayout senza spaziatura](stacklayout-images/spacing.png "StackLayout senza spaziatura")](stacklayout-images/spacing-large.png#lightbox "StackLayout senza spaziatura")

> [!TIP]
> La [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) proprietà può essere impostata su valori negativi per far sovrapporre le visualizzazioni figlio.

Il codice C# equivalente è il seguente:

```csharp
public class StackLayoutSpacingPageCS : ContentPage
{
    public StackLayoutSpacingPageCS()
    {
        Title = "StackLayout Spacing demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Spacing = 0,
            Children =
            {
                new Label { Text = "Primary colors" },
                new BoxView { Color = Color.Red },
                new BoxView { Color = Color.Yellow },
                new BoxView { Color = Color.Blue },
                new Label { Text = "Secondary colors" },
                new BoxView { Color = Color.Green },
                new BoxView { Color = Color.Orange },
                new BoxView { Color = Color.Purple }
            }
        };
    }
}
```

## <a name="position-and-size-of-child-views"></a>Posizione e dimensioni delle visualizzazioni figlio

Le dimensioni e la posizione delle visualizzazioni figlio all'interno di un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) dipendono dai valori delle visualizzazioni figlio [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) e [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) dalle proprietà e dai valori delle rispettive [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà e. In una [`StackLayout`](xref:Xamarin.Forms.StackLayout) visualizzazione verticale, le visualizzazioni figlio si espandono fino a riempire la larghezza disponibile quando le relative dimensioni non vengono impostate in modo esplicito. Analogamente, in una `StackLayout` visualizzazione orizzontale le visualizzazioni figlio si espandono per riempire l'altezza disponibile quando le relative dimensioni non vengono impostate in modo esplicito.

Le [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà e di un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) e le relative visualizzazioni figlio possono essere impostate sui campi dello [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) struct, che incapsula due preferenze di layout:

- L' *allineamento* determina la posizione e le dimensioni di una visualizzazione figlio all'interno del layout padre.
- L' *espansione* indica se la visualizzazione figlio deve usare spazio aggiuntivo, se disponibile.

> [!TIP]
> Non impostare le [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà e di un oggetto a meno che non [`StackLayout`](xref:Xamarin.Forms.StackLayout) sia necessario. I valori predefiniti di `LayoutOptions.Fill` e `LayoutOptions.FillAndExpand` consentono la migliore ottimizzazione del layout. La modifica di queste proprietà ha un costo e utilizza la memoria, anche quando vengono ripristinati i valori predefiniti.

### <a name="alignment"></a>Allineamento

Nell'esempio di codice XAML seguente vengono impostate le preferenze di allineamento per ogni visualizzazione figlio nel [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.AlignmentPage"
             Title="Alignment demo">
    <StackLayout Margin="20">
        <Label Text="Start"
               BackgroundColor="Gray"
               HorizontalOptions="Start" />
        <Label Text="Center"
               BackgroundColor="Gray"
               HorizontalOptions="Center" />
        <Label Text="End"
               BackgroundColor="Gray"
               HorizontalOptions="End" />
        <Label Text="Fill"
               BackgroundColor="Gray"
               HorizontalOptions="Fill" />
    </StackLayout>
</ContentPage>
```

In questo esempio le preferenze di allineamento sono impostate sugli [`Label`](xref:Xamarin.Forms.Label) oggetti per controllarne la posizione all'interno di [`StackLayout`](xref:Xamarin.Forms.StackLayout) . I [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) campi,, [`End`](xref:Xamarin.Forms.LayoutOptions.End) e [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) vengono utilizzati per definire l'allineamento degli [`Label`](xref:Xamarin.Forms.Label) oggetti all'interno dell'elemento padre `StackLayout` :

[![Screenshot di un StackLayout con le opzioni di allineamento impostate](stacklayout-images/alignment.png "StackLayout con opzioni di allineamento")](stacklayout-images/alignment-large.png#lightbox "StackLayout con opzioni di allineamento")

Un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) rispetta solo le preferenze di allineamento nelle viste figlio che hanno la direzione opposta all'orientamento di `StackLayout`. Pertanto, le viste figlio [`Label`](xref:Xamarin.Forms.Label) all'interno dell'elemento `StackLayout` orientato verticalmente impostano le loro proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) su uno dei campi di allineamento:

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), che posiziona l'oggetto [`Label`](xref:Xamarin.Forms.Label) sul lato sinistro dell'oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) .
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), che centra [`Label`](xref:Xamarin.Forms.Label) in [`StackLayout`](xref:Xamarin.Forms.StackLayout).
- [`End`](xref:Xamarin.Forms.LayoutOptions.End), che posiziona l'oggetto [`Label`](xref:Xamarin.Forms.Label) sul lato destro dell'oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) .
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill), con cui [`Label`](xref:Xamarin.Forms.Label) riempie la larghezza di [`StackLayout`](xref:Xamarin.Forms.StackLayout).

Il codice C# equivalente è il seguente:

```csharp
public class AlignmentPageCS : ContentPage
{
    public AlignmentPageCS()
    {
        Title = "Alignment demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
                new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
                new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
                new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
            }
        };
    }
}
```

### <a name="expansion"></a>Espansione

Nell'esempio di codice XAML seguente vengono impostate le preferenze di espansione per ogni [`Label`](xref:Xamarin.Forms.Label) in [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.ExpansionPage"
             Title="Expansion demo">
    <StackLayout Margin="20">
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Start"
               BackgroundColor="Gray"
               VerticalOptions="StartAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Center"
               BackgroundColor="Gray"
               VerticalOptions="CenterAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="End"
               BackgroundColor="Gray"
               VerticalOptions="EndAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Fill"
               BackgroundColor="Gray"
               VerticalOptions="FillAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
    </StackLayout>
</ContentPage>
```

In questo esempio le preferenze di espansione vengono impostate sugli [`Label`](xref:Xamarin.Forms.Label) oggetti per controllarne le dimensioni all'interno di [`StackLayout`](xref:Xamarin.Forms.StackLayout) . I [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand) campi,, [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) e [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) vengono usati per definire la preferenza di allineamento e se `Label` occupa più spazio se disponibile all'interno dell'elemento padre `StackLayout` :

[![Screenshot di un StackLayout con le opzioni di espansione impostate](stacklayout-images/expansion.png "StackLayout con opzioni di espansione")](stacklayout-images/expansion-large.png#lightbox "StackLayout con opzioni di espansione")

Un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) può espandere le viste figlio solo nella direzione del proprio orientamento. Pertanto, l'elemento `StackLayout` orientato verticalmente può espandere le viste figlio [`Label`](xref:Xamarin.Forms.Label) che impostano le loro proprietà [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) su uno dei campi di espansione. Ciò significa che, per l'allineamento verticale, ogni `Label` occupa la stessa quantità di spazio all'interno dell'elemento `StackLayout`. Tuttavia solo l'elemento `Label` finale che imposta la sua proprietà [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) su [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) ha una dimensione diversa.

> [!TIP]
> Quando si utilizza un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) , verificare che solo una visualizzazione figlio sia impostata su [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) . Questa proprietà garantisce che l'elemento figlio specificato occupi lo spazio più ampio che `StackLayout` è in grado di assegnare ed è dispendioso eseguire questi calcoli più di una volta.

Il codice C# equivalente è il seguente:

```csharp
public ExpansionPageCS()
{
    Title = "Expansion demo";
    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children =
        {
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
        }
    };
}
```

> [!IMPORTANT]
> Quando tutto lo spazio in un elemento [`StackLayout`](xref:Xamarin.Forms.StackLayout) è utilizzato, le preferenze di espansione non hanno alcun effetto.

Per ulteriori informazioni sull'allineamento e sull'espansione, vedere [Opzioni di Xamarin.Forms layout in ](layout-options.md).

## <a name="nested-stacklayout-objects"></a>Oggetti StackLayout annidati

Un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) può essere utilizzato come layout padre che contiene oggetti figlio annidati `StackLayout` o altri layout figlio.

Il codice XAML seguente mostra un esempio di annidamento di [`StackLayout`](xref:Xamarin.Forms.StackLayout) oggetti:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.CombinedStackLayoutPage"
             Title="Combined StackLayouts demo">
    <StackLayout Margin="20">
        ...
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Red" />
                <Label Text="Red"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Yellow" />
                <Label Text="Yellow"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Blue" />
                <Label Text="Blue"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        ...
    </StackLayout>
</ContentPage>
```

In questo esempio, l'elemento padre [`StackLayout`](xref:Xamarin.Forms.StackLayout) contiene `StackLayout` oggetti annidati all'interno di [`Frame`](xref:Xamarin.Forms.Frame) oggetti. L'elemento padre `StackLayout` è orientato verticalmente, mentre gli `StackLayout` oggetti figlio sono orientati orizzontalmente:

[![Screenshot degli oggetti StackLayout annidati](stacklayout-images/combined.png "StackLayouts annidato")](stacklayout-images/combined-large.png#lightbox "StackLayouts annidato")

> [!IMPORTANT]
> Più a fondo nidificano [`StackLayout`](xref:Xamarin.Forms.StackLayout) gli oggetti e altri layout, più il layout annidato avrà un effetto sulle prestazioni. Per ulteriori informazioni, vedere [scegliere il layout corretto](~/xamarin-forms/deploy-test/performance.md#choose-the-correct-layout).

Il codice C# equivalente è il seguente:

```csharp
public class CombinedStackLayoutPageCS : ContentPage
{
    public CombinedStackLayoutPageCS()
    {
        Title = "Combined StackLayouts demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Primary colors" },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Red },
                            new Label { Text = "Red", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Yellow },
                            new Label { Text = "Yellow", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Blue },
                            new Label { Text = "Blue", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                // ...
            }
        };
    }
}
```

## <a name="related-links"></a>Collegamenti correlati

- [Demo di StackLayout (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stacklayoutdemos)
- [Opzioni di layout inXamarin.Forms](layout-options.md)
- [Scegliere un Xamarin.Forms layout](choose-layout.md)
- [Migliorare le Xamarin.Forms prestazioni dell'app](~/xamarin-forms/deploy-test/performance.md)
