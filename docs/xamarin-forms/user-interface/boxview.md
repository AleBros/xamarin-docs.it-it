---
title: Xamarin.FormsBoxView
description: Questo articolo illustra come usare un rettangolo colorato per la decorazione, la grafica e l'interazione in un' Xamarin.Forms applicazione.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5f915955bff969ef38cdb7a89bf9cecf05401131
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136357"
---
# <a name="xamarinforms-boxview"></a>Xamarin.FormsBoxView

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)

[`BoxView`](xref:Xamarin.Forms.BoxView)esegue il rendering di un rettangolo semplice con larghezza, altezza e colore specificati. È possibile usare `BoxView` per la decorazione, la grafica rudimentale e l'interazione con l'utente tramite il tocco.

Poiché non Xamarin.Forms dispone di un sistema di grafica vettoriale incorporato, `BoxView` consente di compensare. Alcuni dei programmi di esempio descritti in questo articolo usano `BoxView` per il rendering della grafica. Il valore `BoxView` di può essere ridimensionato in modo da essere simile a una riga di larghezza e spessore specifici e quindi ruotato da qualsiasi angolo utilizzando la `Rotation` Proprietà.

Sebbene sia `BoxView` in grado di simulare immagini semplici, potrebbe essere necessario esaminare l' [uso di SkiaSharp in Xamarin.Forms ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) per requisiti grafici più sofisticati.

Questo articolo tratta gli argomenti seguenti:

- **[Impostazione del colore e delle dimensioni](#colorandsize)** &ndash; di BoxView impostare le `BoxView` Proprietà.
- **[Rendering di decorazioni](#textdecorations)** &ndash; di testo usare un oggetto `BoxView` per le linee di rendering.
- **[Elenco di colori con BoxView](#listingcolors)** &ndash; Visualizza tutti i colori di sistema in un `ListView` .
- **[Riproduzione del gioco della vita mediante la sottoclasse BoxView](#subclassing)** &ndash; implementare una nota automazione cellulare.
- **[Creazione di un orologio digitale](#digitalclock)** &ndash; simulare una visualizzazione a matrice di punti.
- **[Creazione di un orologio analogico](#analogclock)** &ndash; trasforma e anima `BoxView` gli elementi.

<a name="colorandsize" />

## <a name="setting-boxview-color-and-size"></a>Impostazione del colore e delle dimensioni di BoxView

In genere, è possibile impostare le proprietà seguenti di `BoxView` :

- [`Color`](xref:Xamarin.Forms.BoxView.Color)per impostare il colore.
- [`CornerRadius`](xref:Xamarin.Forms.BoxView.CornerRadius)per impostare il raggio dell'angolo.
- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)per impostare la larghezza di `BoxView` in unità indipendenti dal dispositivo.
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)per impostare l'altezza di `BoxView` .

La `Color` proprietà è di tipo. `Color` la proprietà può essere impostata su qualsiasi `Color` valore, inclusi i campi statici di sola lettura 141 dei colori denominati che variano in ordine alfabetico da `AliceBlue` a `YellowGreen` .

La `CornerRadius` proprietà è di tipo [`CornerRadius`](xref:Xamarin.Forms.CornerRadius) . la proprietà può essere impostata su un singolo `double` valore del raggio dell'angolo uniforme oppure su una `CornerRadius` struttura definita da quattro `double` valori applicati in alto a sinistra, in alto a destra, in basso a sinistra e in basso a destra di `BoxView` .

Le `WidthRequest` `HeightRequest` proprietà e rivestono un ruolo solo se non `BoxView` è *vincolato* nel layout. Questa situazione si verifica quando il contenitore di layout deve essere in grado di individuare le dimensioni del figlio, ad esempio quando `BoxView` è figlio di una cella di dimensioni automatiche nel `Grid` layout. Un oggetto `BoxView` viene anche non vincolato quando le relative `HorizontalOptions` `VerticalOptions` proprietà e sono impostate su valori diversi da `LayoutOptions.Fill` . Se l'oggetto `BoxView` non è vincolato, ma `WidthRequest` le `HeightRequest` proprietà e non sono impostate, la larghezza o l'altezza vengono impostate sui valori predefiniti di 40 unità o circa 1/4 pollice sui dispositivi mobili.

Le `WidthRequest` `HeightRequest` proprietà e vengono ignorate se `BoxView` è *vincolato* nel layout, nel qual caso il contenitore di layout impone le proprie dimensioni in `BoxView` .

Un oggetto `BoxView` può essere vincolato in una dimensione e non vincolato nell'altro. Se, ad esempio, `BoxView` è un elemento figlio di un oggetto verticale `StackLayout` , la dimensione verticale di non `BoxView` è vincolata e la relativa dimensione orizzontale è in genere vincolata. Esistono tuttavia eccezioni per tale dimensione orizzontale: se la `BoxView` `HorizontalOptions` proprietà della proprietà è impostata su un valore diverso da `LayoutOptions.Fill` , anche la dimensione orizzontale non è vincolata. È anche possibile che l'oggetto `StackLayout` abbia una dimensione orizzontale non vincolata, nel qual caso anche l'oggetto `BoxView` sarà non vincolato orizzontalmente.

Nell'esempio [**BasicBoxView**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview) viene visualizzato un quadrato non vincolato `BoxView` al centro della pagina:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BasicBoxView"
             x:Class="BasicBoxView.MainPage">

    <BoxView Color="CornflowerBlue"
             CornerRadius="10"
             WidthRequest="160"
             HeightRequest="160"
             VerticalOptions="Center"
             HorizontalOptions="Center" />

</ContentPage>
```

Il risultato è il seguente:

[![BoxView di base](boxview-images/basicboxview-small.png "BoxView di base")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

Se le `VerticalOptions` `HorizontalOptions` proprietà e vengono rimosse dal `BoxView` tag o sono impostate su `Fill` , il `BoxView` diventa vincolato dalle dimensioni della pagina e si espande per riempire la pagina.

Un oggetto `BoxView` può anche essere un elemento figlio di un oggetto `AbsoluteLayout` . In tal caso, la posizione e le dimensioni di `BoxView` vengono impostate utilizzando la `LayoutBounds` proprietà associabile associata. Il `AbsoluteLayout` viene illustrato nell'articolo [**AbsoluteLayout**](~/xamarin-forms/user-interface/layouts/absolute-layout.md).

Verranno visualizzati esempi di tutti questi casi nei programmi di esempio seguenti.

<a name="textdecorations" />

## <a name="rendering-text-decorations"></a>Rendering di decorazioni di testo

È possibile utilizzare `BoxView` per aggiungere alcune semplici decorazioni alle pagine sotto forma di linee orizzontali e verticali. Questa operazione è illustrata nell'esempio [**TextDecoration**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration) . Tutti gli oggetti visivi del programma sono definiti nel file **MainPage. XAML** , che contiene diversi `Label` elementi e `BoxView` nell'oggetto `StackLayout` riportato di seguito:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:TextDecoration"
             x:Class="TextDecoration.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Black" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ScrollView Margin="15">
        <StackLayout>

            ···

        </StackLayout>
    </ScrollView>
</ContentPage>
```

Tutti i markup seguenti sono elementi figlio di `StackLayout` . Questo markup è costituito da diversi tipi di elementi decorativi `BoxView` usati con l' `Label` elemento:

[![Decorazione del testo](boxview-images/textdecoration-small.png "Decorazione del testo")](boxview-images/textdecoration-large.png#lightbox "Decorazione del testo")

L'intestazione elegante nella parte superiore della pagina viene eseguita con un oggetto `AbsoluteLayout` i cui figli sono quattro `BoxView` elementi e un `Label` , a cui sono assegnati percorsi e dimensioni specifici:

```xaml
<AbsoluteLayout>
    <BoxView AbsoluteLayout.LayoutBounds="0, 10, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="0, 20, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="10, 0, 5, 65" />
    <BoxView AbsoluteLayout.LayoutBounds="20, 0, 5, 65" />
    <Label Text="Stylish Header"
           FontSize="24"
           AbsoluteLayout.LayoutBounds="30, 25, AutoSize, AutoSize"/>
</AbsoluteLayout>
```

Nel file XAML, `AbsoluteLayout` è seguito da un oggetto `Label` con testo formattato che descrive `AbsoluteLayout` .

È possibile sottolineare una stringa di testo racchiudendo `Label` sia `BoxView` che in un oggetto il `StackLayout` cui `HorizontalOptions` valore è impostato su un valore diverso da `Fill` . La larghezza di `StackLayout` viene quindi regolata in base alla larghezza di `Label` , che quindi impone tale larghezza sull'oggetto `BoxView` . Al `BoxView` viene assegnata solo un'altezza esplicita:

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

Non è possibile usare questa tecnica per sottolineare singole parole nelle stringhe di testo più lunghe o in un paragrafo.

È anche possibile usare un oggetto `BoxView` per assomigliare a un `hr` elemento HTML (regola orizzontale). È sufficiente lasciare che la larghezza di `BoxView` sia determinata dal relativo contenitore padre, che in questo caso è `StackLayout` :

```xaml
<BoxView HeightRequest="3" />
```

Infine, è possibile creare una linea verticale su un lato di un paragrafo di testo racchiudendo sia l'oggetto `BoxView` che l'oggetto `Label` in un oggetto orizzontale `StackLayout` . In questo caso, l'altezza di `BoxView` è uguale all'altezza di `StackLayout` , che è regolata dall'altezza di `Label` :

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView WidthRequest="4"
             Margin="0, 0, 10, 0" />
    <Label>

        ···

    </Label>
</StackLayout>
```

<a name="listingcolors" />

## <a name="listing-colors-with-boxview"></a>Elenco di colori con BoxView

`BoxView`È utile per la visualizzazione dei colori. Questo programma usa un oggetto `ListView` per elencare tutti i campi statici di sola lettura pubblici della Xamarin.Forms `Color` struttura:

[![Colori ListView](boxview-images/listviewcolors-small.png "Colori ListView")](boxview-images/listviewcolors-large.png#lightbox "Colori ListView")

Il programma [**ListViewColors**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors) include una classe denominata `NamedColor` . Il costruttore statico usa la reflection per accedere a tutti i campi della `Color` struttura e creare un `NamedColor` oggetto per ciascuna di esse. Questi vengono archiviati nella proprietà statica `All` :

```csharp
public class NamedColor
{
    // Instance members.
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    // Static members.
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields ())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof (Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                               (int)(255 * color.R),
                                               (int)(255 * color.G),
                                               (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }
}
```

Gli oggetti visivi del programma sono descritti nel file XAML. La `ItemsSource` proprietà di `ListView` viene impostata sulla `NamedColor.All` proprietà statica, il che significa che `ListView` Visualizza tutti i singoli `NamedColor` oggetti:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ListViewColors"
             x:Class="ListViewColors.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="10, 20, 10, 0" />
            <On Platform="Android, UWP" Value="10, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ListView SeparatorVisibility="None"
              ItemsSource="{x:Static local:NamedColor.All}">
        <ListView.RowHeight>
            <OnPlatform x:TypeArguments="x:Int32">
                <On Platform="iOS, Android" Value="80" />
                <On Platform="UWP" Value="90" />
            </OnPlatform>
        </ListView.RowHeight>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ContentView Padding="5">
                        <Frame OutlineColor="Accent"
                               Padding="10">
                            <StackLayout Orientation="Horizontal">
                                <BoxView Color="{Binding Color}"
                                         WidthRequest="50"
                                         HeightRequest="50" />
                                <StackLayout>
                                    <Label Text="{Binding FriendlyName}"
                                           FontSize="22"
                                           VerticalOptions="StartAndExpand" />
                                    <Label Text="{Binding RgbDisplay, StringFormat='RGB = {0}'}"
                                           FontSize="16"
                                           VerticalOptions="CenterAndExpand" />
                                </StackLayout>
                            </StackLayout>
                        </Frame>
                    </ContentView>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Gli `NamedColor` oggetti vengono formattati in base all' `ViewCell` oggetto impostato come modello di dati di `ListView` . Questo modello include un `BoxView` oggetto la cui `Color` proprietà è associata alla `Color` proprietà dell' `NamedColor` oggetto.

<a name="subclassing" />

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>Riproduzione del gioco della vita mediante la sottoclasse BoxView

Il gioco di vita è un automa cellulare inventato dal matematico John Conway e divulgato nelle pagine di *Scientific American* negli anni '70. Un'introduzione corretta viene fornita dall'articolo di Wikipedia [Conway ' s Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life).

Il Xamarin.Forms programma [**GameOfLife**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife) definisce una classe denominata `LifeCell` che deriva da `BoxView` . Questa classe incapsula la logica di una singola cella nel gioco di vita:

```csharp
class LifeCell : BoxView
{
    bool isAlive;

    public event EventHandler Tapped;

    public LifeCell()
    {
        BackgroundColor = Color.White;

        TapGestureRecognizer tapGesture = new TapGestureRecognizer();
        tapGesture.Tapped += (sender, args) =>
        {
            Tapped?.Invoke(this, EventArgs.Empty);
        };
        GestureRecognizers.Add(tapGesture);
    }

    public int Col { set; get; }

    public int Row { set; get; }

    public bool IsAlive
    {
        set
        {
            if (isAlive != value)
            {
                isAlive = value;
                BackgroundColor = isAlive ? Color.Black : Color.White;
            }
        }
        get
        {
            return isAlive;
        }
    }
}
```

`LifeCell`aggiunge altre tre proprietà a `BoxView` : le `Col` `Row` proprietà e archiviano la posizione della cella all'interno della griglia e la `IsAlive` Proprietà ne indica lo stato. La `IsAlive` proprietà imposta inoltre la `Color` proprietà di `BoxView` su nero se la cella è attiva e bianca se la cella non è attiva.

`LifeCell`installa anche un oggetto `TapGestureRecognizer` per consentire all'utente di impostare lo stato delle celle tramite il tocco. La classe converte l' `Tapped` evento dal riconoscimento di movimento in un proprio `Tapped` evento.

Il programma **GameOfLife** include anche una `LifeGrid` classe che incapsula gran parte della logica del gioco e una `MainPage` classe che gestisce gli oggetti visivi del programma. Sono incluse una sovrapposizione che descrive le regole del gioco. Ecco il programma in azione che mostra un centinaio di `LifeCell` oggetti nella pagina:

[![Gioco di vita](boxview-images/gameoflife-small.png "Gioco di vita")](boxview-images/gameoflife-large.png#lightbox "Gioco di vita")

<a name="digitalclock" />

## <a name="creating-a-digital-clock"></a>Creazione di un orologio digitale

Il programma [**DotMatrixClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock) crea 210 `BoxView` elementi per simulare i punti di una visualizzazione di una matrice di punti di 5 per 7 obsoleta. È possibile leggere l'ora in modalità verticale o orizzontale, ma è più grande in orizzontale:

[![Clock a matrice di punti](boxview-images/dotmatrixclock-small.png "Clock a matrice di punti")](boxview-images/dotmatrixclock-large.png#lightbox "Clock a matrice di punti")

Il file XAML non è più che creare un'istanza di `AbsoluteLayout` usata per l'orologio:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DotMatrixClock"
             x:Class="DotMatrixClock.MainPage"
             Padding="10"
             SizeChanged="OnPageSizeChanged">

    <AbsoluteLayout x:Name="absoluteLayout"
                    VerticalOptions="Center" />
</ContentPage>
```

Tutto il resto si verifica nel file code-behind. La logica di visualizzazione a matrice di punti è molto semplificata dalla definizione di più matrici che descrivono i punti corrispondenti a ognuna delle 10 cifre e dei due punti:

```csharp
public partial class MainPage : ContentPage
{
    // Total dots horizontally and vertically.
    const int horzDots = 41;
    const int vertDots = 7;

    // 5 x 7 dot matrix patterns for 0 through 9.
    static readonly int[, ,] numberPatterns = new int[10, 7, 5]
    {
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 1, 1}, { 1, 0, 1, 0, 1},
            { 1, 1, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 0, 0}, { 0, 1, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0},
            { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0},
            { 0, 0, 1, 0, 0}, { 0, 1, 0, 0, 0}, { 1, 1, 1, 1, 1}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 0, 1, 0},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 0, 1, 0}, { 0, 0, 1, 1, 0}, { 0, 1, 0, 1, 0}, { 1, 0, 0, 1, 0},
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 0, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0}, { 0, 0, 0, 0, 1},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 1, 0}, { 0, 1, 0, 0, 0}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0},
            { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 1},
            { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 1, 1, 0, 0}
        },
    };

    // Dot matrix pattern for a colon.
    static readonly int[,] colonPattern = new int[7, 2]
    {
        { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }
    };

    // BoxView colors for on and off.
    static readonly Color colorOn = Color.Red;
    static readonly Color colorOff = new Color(0.5, 0.5, 0.5, 0.25);

    // Box views for 6 digits, 7 rows, 5 columns.
    BoxView[, ,] digitBoxViews = new BoxView[6, 7, 5];

    ···

}
```

Questi campi si concludono con una matrice tridimensionale di `BoxView` elementi per archiviare i modelli punto per le sei cifre.

Il costruttore crea tutti gli `BoxView` elementi per le cifre e i due punti e inizializza anche la `Color` proprietà degli `BoxView` elementi per i due punti:

```csharp
public partial class MainPage : ContentPage
{

    ···

    public MainPage()
    {
        InitializeComponent();

        // BoxView dot dimensions.
        double height = 0.85 / vertDots;
        double width = 0.85 / horzDots;

        // Create and assemble the BoxViews.
        double xIncrement = 1.0 / (horzDots - 1);
        double yIncrement = 1.0 / (vertDots - 1);
        double x = 0;

        for (int digit = 0; digit < 6; digit++)
        {
            for (int col = 0; col < 5; col++)
            {
                double y = 0;

                for (int row = 0; row < 7; row++)
                {
                    // Create the digit BoxView and add to layout.
                    BoxView boxView = new BoxView();
                    digitBoxViews[digit, row, col] = boxView;
                    absoluteLayout.Children.Add(boxView,
                                                new Rectangle(x, y, width, height),
                                                AbsoluteLayoutFlags.All);
                    y += yIncrement;
                }
                x += xIncrement;
            }
            x += xIncrement;

            // Colons between the hours, minutes, and seconds.
            if (digit == 1 || digit == 3)
            {
                int colon = digit / 2;

                for (int col = 0; col < 2; col++)
                {
                    double y = 0;

                    for (int row = 0; row < 7; row++)
                    {
                        // Create the BoxView and set the color.
                        BoxView boxView = new BoxView
                            {
                                Color = colonPattern[row, col] == 1 ?
                                            colorOn : colorOff
                            };
                        absoluteLayout.Children.Add(boxView,
                                                    new Rectangle(x, y, width, height),
                                                    AbsoluteLayoutFlags.All);
                        y += yIncrement;
                    }
                    x += xIncrement;
                }
                x += xIncrement;
            }
        }

        // Set the timer and initialize with a manual call.
        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimer);
        OnTimer();
    }

    ···

}
```

Questo programma usa la funzionalità di posizionamento e dimensionamento relativa di `AbsoluteLayout` . La larghezza e l'altezza di ogni `BoxView` sono impostate su valori frazionari, in particolare 85% di 1 diviso per il numero di punti orizzontali e verticali. Anche le posizioni sono impostate su valori frazionari.

Poiché tutte le posizioni e le dimensioni sono relative alle dimensioni totali di `AbsoluteLayout` , il `SizeChanged` gestore della pagina deve impostare solo un valore `HeightRequest` di `AbsoluteLayout` :

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnPageSizeChanged(object sender, EventArgs args)
    {
        // No chance a display will have an aspect ratio > 41:7
        absoluteLayout.HeightRequest = vertDots * Width / horzDots;
    }

    ···

}
```

La larghezza di `AbsoluteLayout` viene impostata automaticamente perché si adatta alla larghezza intera della pagina.

Il codice finale della `MainPage` classe elabora il callback del timer e colora i punti di ogni cifra. La definizione delle matrici multidimensionali all'inizio del file code-behind consente di rendere questa logica la parte più semplice del programma:

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimer()
    {
        DateTime dateTime = DateTime.Now;

        // Convert 24-hour clock to 12-hour clock.
        int hour = (dateTime.Hour + 11) % 12 + 1;

        // Set the dot colors for each digit separately.
        SetDotMatrix(0, hour / 10);
        SetDotMatrix(1, hour % 10);
        SetDotMatrix(2, dateTime.Minute / 10);
        SetDotMatrix(3, dateTime.Minute % 10);
        SetDotMatrix(4, dateTime.Second / 10);
        SetDotMatrix(5, dateTime.Second % 10);
        return true;
    }

    void SetDotMatrix(int index, int digit)
    {
        for (int row = 0; row < 7; row++)
            for (int col = 0; col < 5; col++)
            {
                bool isOn = numberPatterns[digit, row, col] == 1;
                Color color = isOn ? colorOn : colorOff;
                digitBoxViews[index, row, col].Color = color;
            }
    }
}
```

<a name="analogclock" />

## <a name="creating-an-analog-clock"></a>Creazione di un orologio analogico

Un orologio a matrice di punti potrebbe sembrare un'applicazione ovvia di `BoxView` , ma `BoxView` gli elementi sono anche in grado di realizzare un orologio analogico:

[![BoxView Clock](boxview-images/boxviewclock-small.png "BoxView Clock")](boxview-images/boxviewclock-large.png#lightbox "BoxView Clock")

Tutti gli oggetti visivi nel programma [**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock) sono elementi figlio di un oggetto `AbsoluteLayout` . Questi elementi vengono ridimensionati utilizzando la `LayoutBounds` proprietà associata e ruotati utilizzando la `Rotation` Proprietà.

`BoxView`Viene creata un'istanza dei tre elementi per le lancette del clock nel file XAML, ma non posizionati o ridimensionati:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BoxViewClock"
             x:Class="BoxViewClock.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <AbsoluteLayout x:Name="absoluteLayout"
                    SizeChanged="OnAbsoluteLayoutSizeChanged">

        <BoxView x:Name="hourHand"
                 Color="Black" />

        <BoxView x:Name="minuteHand"
                 Color="Black" />

        <BoxView x:Name="secondHand"
                 Color="Black" />
    </AbsoluteLayout>
</ContentPage>
```

Il costruttore del file code-behind crea un'istanza degli `BoxView` elementi 60 per i segni di graduazione intorno alla circonferenza del clock:

```csharp
public partial class MainPage : ContentPage
{

    ···

    BoxView[] tickMarks = new BoxView[60];

    public MainPage()
    {
        InitializeComponent();

        // Create the tick marks (to be sized and positioned later).
        for (int i = 0; i < tickMarks.Length; i++)
        {
            tickMarks[i] = new BoxView { Color = Color.Black };
            absoluteLayout.Children.Add(tickMarks[i]);
        }

        Device.StartTimer(TimeSpan.FromSeconds(1.0 / 60), OnTimerTick);
    }

    ···

}
```

Il ridimensionamento e il posizionamento di tutti gli `BoxView` elementi si verificano nel `SizeChanged` gestore per l'oggetto `AbsoluteLayout` . Una piccola struttura interna alla classe denominata `HandParams` descrive le dimensioni di ognuna delle tre mani rispetto alla dimensione totale del clock:

```csharp
public partial class MainPage : ContentPage
{
    // Structure for storing information about the three hands.
    struct HandParams
    {
        public HandParams(double width, double height, double offset) : this()
        {
            Width = width;
            Height = height;
            Offset = offset;
        }

        public double Width { private set; get; }   // fraction of radius
        public double Height { private set; get; }  // ditto
        public double Offset { private set; get; }  // relative to center pivot
    }

    static readonly HandParams secondParams = new HandParams(0.02, 1.1, 0.85);
    static readonly HandParams minuteParams = new HandParams(0.05, 0.8, 0.9);
    static readonly HandParams hourParams = new HandParams(0.125, 0.65, 0.9);

    ···

 }
```

Il `SizeChanged` gestore determina il centro e il raggio di `AbsoluteLayout` e quindi ridimensiona e posiziona gli elementi 60 `BoxView` utilizzati come segni di graduazione. Il `for` ciclo termina impostando la `Rotation` proprietà di ognuno di questi `BoxView` elementi. Alla fine del `SizeChanged` gestore, `LayoutHand` viene chiamato il metodo per ridimensionare e posizionare le tre lancette dell'orologio:

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnAbsoluteLayoutSizeChanged(object sender, EventArgs args)
    {
        // Get the center and radius of the AbsoluteLayout.
        Point center = new Point(absoluteLayout.Width / 2, absoluteLayout.Height / 2);
        double radius = 0.45 * Math.Min(absoluteLayout.Width, absoluteLayout.Height);

        // Position, size, and rotate the 60 tick marks.
        for (int index = 0; index < tickMarks.Length; index++)
        {
            double size = radius / (index % 5 == 0 ? 15 : 30);
            double radians = index * 2 * Math.PI / tickMarks.Length;
            double x = center.X + radius * Math.Sin(radians) - size / 2;
            double y = center.Y - radius * Math.Cos(radians) - size / 2;
            AbsoluteLayout.SetLayoutBounds(tickMarks[index], new Rectangle(x, y, size, size));
            tickMarks[index].Rotation = 180 * radians / Math.PI;
        }

        // Position and size the three hands.
        LayoutHand(secondHand, secondParams, center, radius);
        LayoutHand(minuteHand, minuteParams, center, radius);
        LayoutHand(hourHand, hourParams, center, radius);
    }

    void LayoutHand(BoxView boxView, HandParams handParams, Point center, double radius)
    {
        double width = handParams.Width * radius;
        double height = handParams.Height * radius;
        double offset = handParams.Offset;

        AbsoluteLayout.SetLayoutBounds(boxView,
            new Rectangle(center.X - 0.5 * width,
                          center.Y - offset * height,
                          width, height));

        // Set the AnchorY property for rotations.
        boxView.AnchorY = handParams.Offset;
    }

    ···

}
```

Il `LayoutHand` metodo ridimensiona e posiziona ogni mano per puntare direttamente alla posizione 12:00. Alla fine del metodo, la `AnchorY` proprietà viene impostata su una posizione corrispondente al centro dell'orologio. Indica il centro della rotazione.

Le mani vengono ruotate nella funzione di callback del timer:

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimerTick()
    {
        // Set rotation angles for hour and minute hands.
        DateTime dateTime = DateTime.Now;
        hourHand.Rotation = 30 * (dateTime.Hour % 12) + 0.5 * dateTime.Minute;
        minuteHand.Rotation = 6 * dateTime.Minute + 0.1 * dateTime.Second;

        // Do an animation for the second hand.
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        secondHand.Rotation = 6 * (dateTime.Second + t);
        return true;
    }
}
```

La seconda mano viene trattata in modo leggermente diverso: viene applicata una funzione di interpolazione dell'animazione per far sembrare che lo spostamento risulti meccanico anziché smussato. A ogni ciclo, la seconda parte esegue il pull di un po' e quindi ne supera la destinazione. Questo piccolo frammento di codice aggiunge molto al realismo del movimento.

## <a name="conclusion"></a>Conclusioni

Il `BoxView` primo potrebbe sembrare semplice, ma come è stato visto, può essere piuttosto versatile e può riprodurre oggetti visivi normalmente possibili solo con la grafica vettoriale. Per la grafica più sofisticata, vedere [uso Xamarin.Forms di SkiaSharp in ](~/xamarin-forms/user-interface/graphics/skiasharp/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [BoxView Basic (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-basicboxview)
- [Decorazione di testo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-textdecoration)
- [Colori ListView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-listviewcolors/)
- [Gioco di vita (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-gameoflife)
- [Clock a matrice di punti (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-dotmatrixclock)
- [Clock BoxView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock)
- [BoxView](xref:Xamarin.Forms.BoxView)
