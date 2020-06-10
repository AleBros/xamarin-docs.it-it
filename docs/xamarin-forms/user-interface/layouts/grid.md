---
title: " Xamarin.Forms Grid" Description: "la Xamarin.Forms griglia è un layout che organizza gli elementi figlio in righe e colonne di celle".
ms. prod: Novell MS. AssetID: 762B1802-D185-494C-B643-74EED55882FE ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 05/15/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-grid"></a>Xamarin.FormsGriglia

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-griddemos)

[![Xamarin.FormsGriglia](grid-images/layouts.png "[! OP. Griglia NO-LOC (Novell. Forms)]")](grid-images/layouts-large.png#lightbox "[! OP. Griglia NO-LOC (Novell. Forms)]")

[`Grid`](xref:Xamarin.Forms.Grid)È un layout che organizza gli elementi figlio in righe e colonne, che possono avere dimensioni proporzionali o assolute. Per impostazione predefinita, un oggetto `Grid` contiene una riga e una colonna. Inoltre, un oggetto `Grid` può essere utilizzato come layout padre che contiene altri layout figlio.

Il [`Grid`](xref:Xamarin.Forms.Grid) layout non deve essere confuso con le tabelle e non è destinato a presentare dati tabulari. A differenza delle tabelle HTML, un `Grid` è progettato per il layout del contenuto. Per la visualizzazione dei dati tabulari, è consigliabile usare [ListView](~/xamarin-forms/user-interface/listview/index.md), [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)o [TableView](~/xamarin-forms/user-interface/tableview.md).

La [`Grid`](xref:Xamarin.Forms.Grid) classe definisce le proprietà seguenti:

- [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty), di tipo `int` , che è una proprietà associata che indica l'allineamento della colonna di una visualizzazione all'interno di un elemento padre `Grid` . Il valore predefinito di questa proprietà è 0. Un callback di convalida garantisce che, quando la proprietà è impostata, il relativo valore è maggiore o uguale a 0.
- [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions), di tipo [`ColumnDefinitionCollection`](xref:Xamarin.Forms.ColumnDefinitionCollection) , è un elenco di [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) oggetti che definiscono la larghezza delle colonne della griglia.
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing), di tipo `double` , indica la distanza tra le colonne della griglia. Il valore predefinito di questa proprietà è 6 unità indipendenti dal dispositivo.
- [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty), di tipo `int` , che è una proprietà associata che indica il numero totale di colonne che una visualizzazione si estende all'interno di un elemento padre `Grid` . Il valore predefinito di questa proprietà è 1. Un callback di convalida garantisce che, quando la proprietà è impostata, il relativo valore è maggiore o uguale a 1.
- [`Row`](xref:Xamarin.Forms.Grid.RowProperty), di tipo `int` , che è una proprietà associata che indica l'allineamento della riga di una visualizzazione all'interno di un elemento padre `Grid` . Il valore predefinito di questa proprietà è 0. Un callback di convalida garantisce che, quando la proprietà è impostata, il relativo valore è maggiore o uguale a 0.
- [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions), di tipo [`RowDefinitionCollection`](xref:Xamarin.Forms.RowDefinitionCollection) , è un elenco di [`RowDefintion`](xref:Xamarin.Forms.RowDefinition) oggetti che definiscono l'altezza delle righe della griglia.
- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing), di tipo `double` , indica la distanza tra le righe della griglia. Il valore predefinito di questa proprietà è 6 unità indipendenti dal dispositivo.
- [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty), di tipo `int` , che è una proprietà associata che indica il numero totale di righe che una visualizzazione si estende all'interno di un elemento padre `Grid` . Il valore predefinito di questa proprietà è 1. Un callback di convalida garantisce che, quando la proprietà è impostata, il relativo valore è maggiore o uguale a 1.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di data binding e stile.

La [`Grid`](xref:Xamarin.Forms.Grid) classe deriva dalla `Layout<T>` classe, che definisce una `Children` proprietà di tipo `IList<T>` . La `Children` proprietà è la `ContentProperty` della `Layout<T>` classe e pertanto non deve essere impostata in modo esplicito da XAML.

> [!TIP]
> Per ottenere le migliori prestazioni di layout possibili, seguire le linee guida in [ottimizzare le prestazioni del layout](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

## <a name="rows-and-columns"></a>Righe e colonne

Per impostazione predefinita, un oggetto [`Grid`](xref:Xamarin.Forms.Grid) contiene una riga e una colonna:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridTutorial.MainPage">
    <Grid Margin="20,35,20,20">
        <Label Text="By default, a Grid contains one row and one column." />
    </Grid>
</ContentPage>
```

In questo esempio, [`Grid`](xref:Xamarin.Forms.Grid) contiene un singolo elemento figlio [`Label`](xref:Xamarin.Forms.Label) posizionato automaticamente in un'unica posizione:

[![Screenshot di un layout di griglia predefinito](grid-images/default.png "Layout griglia predefinito")](grid-images/default-large.png#lightbox "Layout griglia predefinito")

Il comportamento del layout di un oggetto [`Grid`](xref:Xamarin.Forms.Grid) può essere definito con le [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) proprietà e, che sono [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) rispettivamente raccolte di [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) oggetti e. Queste raccolte definiscono le caratteristiche di riga e colonna di un `Grid` oggetto e devono contenere un [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) oggetto per ogni riga in `Grid` e un [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) oggetto per ogni colonna in `Grid` .

La [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) classe definisce una [`Height`](xref:Xamarin.Forms.RowDefinition.Height) proprietà, di tipo [`GridLength`](xref:Xamarin.Forms.GridLength) , e la [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) classe definisce una [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) proprietà di tipo [`GridLength`](xref:Xamarin.Forms.GridLength) . Lo [`GridLength`](xref:Xamarin.Forms.GridLength) struct specifica un'altezza di riga o una colonna in termini di [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) enumerazione, che dispone di tre membri:

- `Absolute`: l'altezza di riga o la larghezza della colonna è un valore in unità indipendenti dal dispositivo (un numero in XAML).
- `Auto`: l'altezza di riga o la larghezza di colonna viene ridimensionata in base al contenuto della cella ( `Auto` in XAML).
- `Star`: l'altezza di riga o la larghezza di colonna rimanente viene allocata proporzionalmente (un numero seguito da `*` in XAML).

Una [`Grid`](xref:Xamarin.Forms.Grid) riga con una `Height` proprietà di `Auto` vincola l'altezza delle visualizzazioni in tale riga allo stesso modo di un oggetto verticale [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Analogamente, una colonna con una `Width` proprietà di `Auto` funziona in modo molto simile a un oggetto orizzontale `StackLayout` .

> [!CAUTION]
> Provare a verificare che il minor numero di righe e colonne possibile sia impostato su [`Auto`](xref:Xamarin.Forms.GridLength.Auto) dimensioni. Ogni riga o colonna ridimensionata automaticamente causerà l'esecuzione di ulteriori calcoli di layout da parte del motore di layout. Usare invece righe e colonne con dimensioni fisse, se possibile. In alternativa, impostare righe e colonne in modo da occupare una quantità proporzionale di spazio con il [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) valore di enumerazione.

Nel codice XAML seguente viene illustrato come creare un oggetto [`Grid`](xref:Xamarin.Forms.Grid) con tre righe e due colonne:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.BasicGridPage"
             Title="Basic Grid demo">
   <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

In questo esempio, [`Grid`](xref:Xamarin.Forms.Grid) ha un'altezza complessiva che rappresenta l'altezza della pagina. `Grid`Sa che l'altezza della terza riga è 100 unità indipendenti dal dispositivo. Sottrae tale altezza dalla propria altezza e alloca l'altezza rimanente proporzionalmente tra la prima e la seconda riga in base al numero prima della stella. In questo esempio, l'altezza della prima riga è due volte quella della seconda riga.

I due [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) oggetti impostano entrambi [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) su `*` , che è uguale a `1*` , ovvero la larghezza dello schermo è divisa equamente sotto le due colonne.

> [!IMPORTANT]
> Il valore predefinito della [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height) proprietà è `*` . Analogamente, il valore predefinito della [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) proprietà è `*` . Non è quindi necessario impostare queste proprietà nei casi in cui queste impostazioni predefinite sono accettabili.

Le visualizzazioni figlio possono essere posizionate in [`Grid`](xref:Xamarin.Forms.Grid) celle specifiche con le [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) proprietà associate e. Inoltre, per fare in modo che le visualizzazioni figlio si estendano su più righe e colonne, utilizzare le [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) proprietà associate e.

Il codice XAML seguente mostra la stessa [`Grid`](xref:Xamarin.Forms.Grid) definizione e posiziona anche le visualizzazioni figlio in `Grid` celle specifiche:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.BasicGridPage"
             Title="Basic Grid demo">
   <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <BoxView Color="Green" />
        <Label Text="Row 0, Column 0"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Column="1"
                 Color="Blue" />
        <Label Grid.Column="1"
               Text="Row 0, Column 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Color="Teal" />
        <Label Grid.Row="1"
               Text="Row 1, Column 0"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="1"
                 Color="Purple" />
        <Label Grid.Row="1"
               Grid.Column="1"
               Text="Row1, Column 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="2"
                 Grid.ColumnSpan="2"
                 Color="Red" />
        <Label Grid.Row="2"
               Grid.ColumnSpan="2"
               Text="Row 2, Columns 0 and 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

> [!NOTE]
> Le `Grid.Row` `Grid.Column` proprietà e sono entrambe indicizzate da 0, quindi `Grid.Row="2"` fa riferimento alla terza riga mentre `Grid.Column="1"` fa riferimento alla seconda colonna. Inoltre, entrambe le proprietà hanno un valore predefinito di 0, quindi non devono essere impostate sulle visualizzazioni figlio che occupano la prima riga o la prima colonna di un oggetto [`Grid`](xref:Xamarin.Forms.Grid) .

In questo esempio, tutte e tre le [`Grid`](xref:Xamarin.Forms.Grid) righe sono occupate dalle [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) viste e. La terza riga è 100 unità indipendenti dal dispositivo, con le prime due righe che occupano lo spazio rimanente (la prima riga è due volte più alta della seconda riga). Le due colonne sono uguali in larghezza e dividono la `Grid` metà. Il `BoxView` nella terza riga si estende su entrambe le colonne.

[![Screenshot di un layout di griglia di base](grid-images/basic.png "Layout griglia di base")](grid-images/basic-large.png#lightbox "Layout griglia di base")

Inoltre, le visualizzazioni figlio in [`Grid`](xref:Xamarin.Forms.Grid) possono condividere le celle. L'ordine in cui gli elementi figlio vengono visualizzati nel codice XAML è l'ordine in cui vengono inseriti gli elementi figlio `Grid` . Nell'esempio precedente, gli [`Label`](xref:Xamarin.Forms.Label) oggetti sono visibili solo perché vengono sottoposti a rendering sopra gli [`BoxView`](xref:Xamarin.Forms.BoxView) oggetti. Gli `Label` oggetti non saranno visibili se è `BoxView` stato eseguito il rendering degli oggetti su di essi.

Il codice C# equivalente è il seguente:

```csharp
public class BasicGridPageCS : ContentPage
{
    public BasicGridPageCS()
    {
        Grid grid = new Grid
        {
            RowDefinitions =
            {
                new RowDefinition { Height = new GridLength(2, GridUnitType.Star) },
                new RowDefinition(),
                new RowDefinition { Height = new GridLength(100) }
            },
            ColumnDefinitions =
            {
                new ColumnDefinition(),
                new ColumnDefinition()
            }
        };

        // Row 0
        // The BoxView and Label are in row 0 and column 0, and so only needs to be added to the
        // Grid.Children collection to get default row and column settings.
        grid.Children.Add(new BoxView
        {
            Color = Color.Green
        });
        grid.Children.Add(new Label
        {
            Text = "Row 0, Column 0",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        });

        // This BoxView and Label are in row 0 and column 1, which are specified as arguments
        // to the Add method.
        grid.Children.Add(new BoxView
        {
            Color = Color.Blue
        }, 1, 0);
        grid.Children.Add(new Label
        {
            Text = "Row 0, Column 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 0);

        // Row 1
        // This BoxView and Label are in row 1 and column 0, which are specified as arguments
        // to the Add method overload.
        grid.Children.Add(new BoxView
        {
            Color = Color.Teal
        }, 0, 1, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Row 1, Column 0",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 0, 1, 1, 2); // These arguments indicate that that the child element goes in the column starting at 0 but ending before 1.
                        // They also indicate that the child element goes in the row starting at 1 but ending before 2.

        grid.Children.Add(new BoxView
        {
            Color = Color.Purple
        }, 1, 2, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Row1, Column 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 2, 1, 2);

        // Row 2
        // Alternatively, the BoxView and Label can be positioned in cells with the Grid.SetRow
        // and Grid.SetColumn methods.
        BoxView boxView = new BoxView { Color = Color.Red };
        Grid.SetRow(boxView, 2);
        Grid.SetColumnSpan(boxView, 2);
        Label label = new Label
        {
            Text = "Row 2, Column 0 and 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        };
        Grid.SetRow(label, 2);
        Grid.SetColumnSpan(label, 2);

        grid.Children.Add(boxView);
        grid.Children.Add(label);

        Title = "Basic Grid demo";
        Content = grid;
    }
}
```

Nel codice, per specificare l'altezza di un [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) oggetto e la larghezza di un [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) oggetto, si utilizzano i valori della [`GridLength`](xref:Xamarin.Forms.GridLength) struttura, spesso in combinazione con l' [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) enumerazione.

Il codice di esempio precedente mostra anche diversi approcci per l'aggiunta di elementi figlio a [`Grid`](xref:Xamarin.Forms.Grid) e la specifica delle celle in cui risiedono. Quando si usa l' `Add` Overload che specifica gli argomenti *Left*, *right*, *Top*e *Bottom* , mentre gli argomenti *Left* e *Top* fanno sempre riferimento alle celle all'interno di `Grid` , gli argomenti *right* e *Bottom* sembrano fare riferimento alle celle esterne a `Grid` . Questo è dovuto al fatto che l'argomento a *destra* deve essere sempre maggiore dell'argomento a *sinistra* e l'argomento *Bottom* deve essere sempre maggiore dell'argomento *Top* . Nell'esempio seguente, che presuppone un oggetto 2x2 `Grid` , viene visualizzato un codice equivalente che utilizza entrambi gli `Add` Overload:

```csharp
// left, top
grid.Children.Add(topLeft, 0, 0);           // first column, first row
grid.Children.Add(topRight, 1, 0);          // second column, first tow
grid.Children.Add(bottomLeft, 0, 1);        // first column, second row
grid.Children.Add(bottomRight, 1, 1);       // second column, second row

// left, right, top, bottom
grid.Children.Add(topLeft, 0, 1, 0, 1);     // first column, first row
grid.Children.Add(topRight, 1, 2, 0, 1);    // second column, first tow
grid.Children.Add(bottomLeft, 0, 1, 1, 2);  // first column, second row
grid.Children.Add(bottomRight, 1, 2, 1, 2); // second column, second row
```

> [!NOTE]
> Inoltre, le visualizzazioni figlio possono essere aggiunte a un [`Grid`](xref:Xamarin.Forms.Grid) con i [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) [`AddVertical`](xref:Xamarin.Forms.Grid.IGridList`1.AddVertical*) metodi e, che aggiungono elementi figlio a una singola riga o a colonna singola `Grid` . Il `Grid` quindi espande le righe o le colonne quando vengono effettuate queste chiamate, nonché posizionando automaticamente gli elementi figlio nelle celle corrette.

## <a name="space-between-rows-and-columns"></a>Spazio tra righe e colonne

Per impostazione predefinita, [`Grid`](xref:Xamarin.Forms.Grid) le righe sono separate da 6 unità di spazio indipendenti dal dispositivo. Analogamente, `Grid` le colonne sono separate da 6 unità di spazio indipendenti dal dispositivo. Queste impostazioni predefinite possono essere modificate impostando [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) rispettivamente le [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) proprietà e:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.GridSpacingPage"
             Title="Grid spacing demo">
    <Grid RowSpacing="0"
          ColumnSpacing="0">
        ..
    </Grid>
</ContentPage>
```

In questo esempio viene creato un oggetto [`Grid`](xref:Xamarin.Forms.Grid) che non ha spaziatura tra le righe e le colonne:

[![Screenshot della griglia senza spaziatura tra le celle](grid-images/spacing.png "Griglia senza spaziatura tra le celle")](grid-images/spacing-large.png#lightbox "Griglia senza spaziatura tra le celle")

> [!TIP]
> Le [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) proprietà e possono essere impostate su valori negativi per la sovrapposizione del contenuto della cella.

Il codice C# equivalente è il seguente:

```csharp
public GridSpacingPageCS()
{
    Grid grid = new Grid
    {
        RowSpacing = 0,
        ColumnSpacing = 0,
        // ...
    };
    // ...

    Content = grid;
}
```

## <a name="alignment"></a>Allineamento

Le visualizzazioni figlio in un oggetto [`Grid`](xref:Xamarin.Forms.Grid) possono essere posizionate all'interno delle celle dalle [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà e. Queste proprietà possono essere impostate sui campi seguenti dallo [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) struct:

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)

> [!IMPORTANT]
> I `AndExpands` campi nello [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) struct sono applicabili solo agli [`StackLayout`](xref:Xamarin.Forms.StackLayout) oggetti.

Il codice XAML seguente crea un oggetto [`Grid`](xref:Xamarin.Forms.Grid) con nove celle di uguale dimensione e inserisce un oggetto [`Label`](xref:Xamarin.Forms.Label) in ogni cella con un allineamento diverso:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.GridAlignmentPage"
             Title="Grid alignment demo">
    <Grid RowSpacing="0"
          ColumnSpacing="0">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>

        <BoxView Color="AliceBlue" />
        <Label Text="Upper left"
               HorizontalOptions="Start"
               VerticalOptions="Start" />
        <BoxView Grid.Column="1"
                 Color="LightSkyBlue" />
        <Label Grid.Column="1"
               Text="Upper center"
               HorizontalOptions="Center"
               VerticalOptions="Start"/>
        <BoxView Grid.Column="2"
                 Color="CadetBlue" />
        <Label Grid.Column="2"
               Text="Upper right"
               HorizontalOptions="End"
               VerticalOptions="Start" />
        <BoxView Grid.Row="1"
                 Color="CornflowerBlue" />
        <Label Grid.Row="1"
               Text="Center left"
               HorizontalOptions="Start"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="1"
                 Color="DodgerBlue" />
        <Label Grid.Row="1"
               Grid.Column="1"
               Text="Center center"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="2"
                 Color="DarkSlateBlue" />
        <Label Grid.Row="1"
               Grid.Column="2"
               Text="Center right"
               HorizontalOptions="End"
               VerticalOptions="Center" />
        <BoxView Grid.Row="2"
                 Color="SteelBlue" />
        <Label Grid.Row="2"
               Text="Lower left"
               HorizontalOptions="Start"
               VerticalOptions="End" />
        <BoxView Grid.Row="2"
                 Grid.Column="1"
                 Color="LightBlue" />
        <Label Grid.Row="2"
               Grid.Column="1"
               Text="Lower center"
               HorizontalOptions="Center"
               VerticalOptions="End" />
        <BoxView Grid.Row="2"
                 Grid.Column="2"
                 Color="BlueViolet" />
        <Label Grid.Row="2"
               Grid.Column="2"
               Text="Lower right"
               HorizontalOptions="End"
               VerticalOptions="End" />
    </Grid>
</ContentPage>
```

In questo esempio, gli [`Label`](xref:Xamarin.Forms.Label) oggetti in ogni riga sono tutti allineati in modo identico verticalmente, ma utilizzano diversi allineamenti orizzontali. In alternativa, può essere considerato come gli `Label` oggetti in ogni colonna siano allineati in modo identico orizzontalmente, ma usando diversi allineamenti verticali:

[![Screenshot dell'allineamento delle celle all'interno di una griglia](grid-images/alignment.png "Allineamento delle celle in una griglia")](grid-images/alignment-large.png#lightbox "Allineamento delle celle in una griglia")

Il codice C# equivalente è il seguente:

```csharp
public class GridAlignmentPageCS : ContentPage
{
    public GridAlignmentPageCS()
    {
        Grid grid = new Grid
        {
            RowSpacing = 0,
            ColumnSpacing = 0,
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition()
            },
            ColumnDefinitions =
            {
                new ColumnDefinition(),
                new ColumnDefinition(),
                new ColumnDefinition()
            }
        };

        // Row 0
        grid.Children.Add(new BoxView
        {
            Color = Color.AliceBlue
        });
        grid.Children.Add(new Label
        {
            Text = "Upper left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Start
        });

        grid.Children.Add(new BoxView
        {
            Color = Color.LightSkyBlue
        }, 1, 0);
        grid.Children.Add(new Label
        {
            Text = "Upper center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Start
        }, 1, 0);

        grid.Children.Add(new BoxView
        {
            Color = Color.CadetBlue
        }, 2, 0);
        grid.Children.Add(new Label
        {
            Text = "Upper right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.Start
        }, 2, 0);

        // Row 1
        grid.Children.Add(new BoxView
        {
            Color = Color.CornflowerBlue
        }, 0, 1);
        grid.Children.Add(new Label
        {
            Text = "Center left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Center
        }, 0, 1);

        grid.Children.Add(new BoxView
        {
            Color = Color.DodgerBlue
        }, 1, 1);
        grid.Children.Add(new Label
        {
            Text = "Center center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 1);

        grid.Children.Add(new BoxView
        {
            Color = Color.DarkSlateBlue
        }, 2, 1);
        grid.Children.Add(new Label
        {
            Text = "Center right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.Center
        }, 2, 1);

        // Row 2
        grid.Children.Add(new BoxView
        {
            Color = Color.SteelBlue
        }, 0, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.End
        }, 0, 2);

        grid.Children.Add(new BoxView
        {
            Color = Color.LightBlue
        }, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.End
        }, 1, 2);

        grid.Children.Add(new BoxView
        {
            Color = Color.BlueViolet
        }, 2, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.End
        }, 2, 2);

        Title = "Grid alignment demo";
        Content = grid;
    }
}
```

## <a name="nested-grid-objects"></a>Oggetti griglia annidati

Un oggetto [`Grid`](xref:Xamarin.Forms.Grid) può essere utilizzato come layout padre che contiene oggetti figlio annidati `Grid` o altri layout figlio. Quando si annidano `Grid` oggetti, `Grid.Row` le `Grid.Column` `Grid.RowSpan` proprietà associate,, e `Grid.ColumnSpan` fanno sempre riferimento alla posizione delle visualizzazioni all'interno del relativo elemento padre `Grid` .

Il codice XAML seguente mostra un esempio di annidamento di [`Grid`](xref:Xamarin.Forms.Grid) oggetti:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:converters="clr-namespace:GridDemos.Converters"
             x:Class="GridDemos.Views.ColorSlidersGridPage"
             Title="Nested Grids demo">

    <ContentPage.Resources>
        <converters:DoubleToIntConverter x:Key="doubleToInt" />

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment"
                    Value="Center" />
        </Style>
    </ContentPage.Resources>

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <BoxView x:Name="boxView"
                 Color="Black" />
        <Grid Grid.Row="1"
              Margin="20">
            <Grid.RowDefinitions>
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
            </Grid.RowDefinitions>
            <Slider x:Name="redSlider"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="1"
                   Text="{Binding Source={x:Reference redSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0}'}" />
            <Slider x:Name="greenSlider"
                    Grid.Row="2"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="3"
                   Text="{Binding Source={x:Reference greenSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0}'}" />
            <Slider x:Name="blueSlider"
                    Grid.Row="4"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="5"
                   Text="{Binding Source={x:Reference blueSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Blue = {0}'}" />
        </Grid>
    </Grid>
</ContentPage>
```

In questo esempio il layout radice [`Grid`](xref:Xamarin.Forms.Grid) contiene un oggetto [`BoxView`](xref:Xamarin.Forms.BoxView) nella prima riga e un elemento figlio `Grid` nella seconda riga. L'elemento figlio `Grid` contiene [`Slider`](xref:Xamarin.Forms.Slider) oggetti che modificano il colore visualizzato dagli `BoxView` oggetti, e [`Label`](xref:Xamarin.Forms.Label) che visualizzano il valore di ciascuno di essi `Slider` :

[![Screenshot delle griglie annidate](grid-images/nesting.png "Oggetti griglia annidati")](grid-images/nesting-large.png#lightbox "Oggetti griglia annidati")

> [!IMPORTANT]
> Più a fondo nidificano [`Grid`](xref:Xamarin.Forms.Grid) gli oggetti e altri layout, più il layout annidato avrà un effetto sulle prestazioni. Per ulteriori informazioni, vedere [scegliere il layout corretto](~/xamarin-forms/deploy-test/performance.md#choose-the-correct-layout).

Il codice C# equivalente è il seguente:

```csharp
public class ColorSlidersGridPageCS : ContentPage
{
    BoxView boxView;
    Slider redSlider;
    Slider greenSlider;
    Slider blueSlider;

    public ColorSlidersGridPageCS()
    {
        // Create an implicit style for the Labels
        Style labelStyle = new Style(typeof(Label))
        {
            Setters =
            {
                new Setter { Property = Label.HorizontalTextAlignmentProperty, Value = TextAlignment.Center }
            }
        };
        Resources.Add(labelStyle);

        // Root page layout
        Grid rootGrid = new Grid
        {
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition()
            }
        };

        boxView = new BoxView { Color = Color.Black };
        rootGrid.Children.Add(boxView);

        // Child page layout
        Grid childGrid = new Grid
        {
            Margin = new Thickness(20),
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition()
            }
        };

        DoubleToIntConverter doubleToInt = new DoubleToIntConverter();

        redSlider = new Slider();
        redSlider.ValueChanged += OnSliderValueChanged;
        childGrid.Children.Add(redSlider);

        Label redLabel = new Label();
        redLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Red = {0}", source: redSlider));
        Grid.SetRow(redLabel, 1);
        childGrid.Children.Add(redLabel);

        greenSlider = new Slider();
        greenSlider.ValueChanged += OnSliderValueChanged;
        Grid.SetRow(greenSlider, 2);
        childGrid.Children.Add(greenSlider);

        Label greenLabel = new Label();
        greenLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Green = {0}", source: greenSlider));
        Grid.SetRow(greenLabel, 3);
        childGrid.Children.Add(greenLabel);

        blueSlider = new Slider();
        blueSlider.ValueChanged += OnSliderValueChanged;
        Grid.SetRow(blueSlider, 4);
        childGrid.Children.Add(blueSlider);

        Label blueLabel = new Label();
        blueLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Blue = {0}", source: blueSlider));
        Grid.SetRow(blueLabel, 5);
        childGrid.Children.Add(blueLabel);

        // Place the child Grid in the root Grid
        rootGrid.Children.Add(childGrid, 0, 1);

        Title = "Nested Grids demo";
        Content = rootGrid;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        boxView.Color = new Color(redSlider.Value, greenSlider.Value, blueSlider.Value);
    }
}
```

## <a name="related-links"></a>Collegamenti correlati

- [Demo Grid (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-griddemos)
- [Opzioni di layout inXamarin.Forms](layout-options.md)
- [Scegliere un Xamarin.Forms layout](choose-layout.md)
- [Migliorare le Xamarin.Forms prestazioni dell'app](~/xamarin-forms/deploy-test/performance.md)
