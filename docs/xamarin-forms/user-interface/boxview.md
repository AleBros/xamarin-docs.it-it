---
title: Xamarin. Forms BoxView
description: Questo articolo illustra come usare un rettangolo colorato per decoration, grafica e l'interazione in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: 4CBF703D-84A0-4CDF-A433-5926B587782A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/26/2018
ms.openlocfilehash: 85edbf657382b7c85ab2c5af543431fb51fb0d4e
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053867"
---
# <a name="xamarinforms-boxview"></a>Xamarin. Forms BoxView

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)

[`BoxView`](xref:Xamarin.Forms.BoxView) esegue il rendering di un rettangolo semplice di una larghezza specificata, altezza e colore. È possibile usare `BoxView` decorativo, grafica rudimentale e per l'interazione con l'utente tramite la tecnologia multitouch.

Poiché xamarin. Forms non dispone di un sistema di grafica vettoriale incorporato, il `BoxView` consente di compensare. Alcuni dei programmi di esempio descritti in questo articolo usano `BoxView` per il rendering grafica. Il `BoxView` possono essere ridimensionati per essere simile a una riga di una larghezza specifica e lo spessore e quindi ruotata di qualsiasi valore angolare utilizzando la `Rotation` proprietà.

Sebbene `BoxView` può simulare elementi grafici semplici, si potrebbe voler analizzare [uso di SkiaSharp in xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) per i requisiti di grafici più sofisticati.

Questo articolo tratta gli argomenti seguenti:

- **[Impostazione BoxView colore e dimensioni](#colorandsize)**  &ndash; impostare il `BoxView` proprietà.
- **[Decorazioni di testo per il rendering](#textdecorations)**  &ndash; usano un `BoxView` per le righe per il rendering.
- **[Elenco di colori con BoxView](#listingcolors)**  &ndash; visualizzare tutti i colori di sistema in un `ListView`.
- **[A cui è assegnato il gioco della vita, dalla creazione di una sottoclasse BoxView](#subclassing)**  &ndash; implementano un famoso automaton cellulare.
- **[Creazione di un orologio digitale](#digitalclock)**  &ndash; simulare una visualizzazione matrice di punti.
- **[Creazione di un orologio analogico](#analogclock)**  &ndash; trasformarne e animarne `BoxView` elementi.

<a name="colorandsize" />

## <a name="setting-boxview-color-and-size"></a>Impostazione BoxView colore e dimensioni

In genere si imposteranno le proprietà seguenti di `BoxView`:

- [`Color`](xref:Xamarin.Forms.BoxView.Color) Per impostare il relativo colore.
- [`CornerRadius`](xref:Xamarin.Forms.BoxView.CornerRadius) Per impostare il raggio dell'angolo.
- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) Per impostare la larghezza del `BoxView` in unità indipendenti dal dispositivo.
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) Per impostare l'altezza del `BoxView`.

Il `Color` proprietà è di tipo `Color`; la proprietà può essere impostata su qualsiasi `Color` valore, inclusi i campi di sola lettura statici 141 di colori compreso tra in ordine alfabetico denominati `AliceBlue` a `YellowGreen`.

Il `CornerRadius` proprietà è di tipo [ `CornerRadius` ](xref:Xamarin.Forms.CornerRadius); la proprietà può essere impostata su un singolo `double` uniform valore raggio d'angolo, o un oggetto `CornerRadius` struttura definita da quattro `double` valori che vengono applicati a l'in alto a sinistra, in alto a destra in basso a sinistra e basso a destra il `BoxView`.

Il `WidthRequest` e `HeightRequest` proprietà svolgono un ruolo solo se il `BoxView` viene *non vincolato* nel layout. Ciò avviene quando il contenitore di layout deve conoscere l'elemento figlio della dimensione, ad esempio, quando la `BoxView` è un elemento figlio di una cella ridimensionato automaticamente nel `Grid` layout. Oggetto `BoxView` è non vincolato anche quando relativi `HorizontalOptions` e `VerticalOptions` delle proprietà vengono impostate sui valori diversi da `LayoutOptions.Fill`. Se il `BoxView` non è vincolata, ma la `WidthRequest` e `HeightRequest` non vengono impostate proprietà, quindi la larghezza o altezza sono impostate sui valori predefiniti di 40 unità, o circa 1 e 4 pollici nei dispositivi mobili.

Il `WidthRequest` e `HeightRequest` le proprietà vengono ignorate se il `BoxView` viene *vincolata* nel layout, nel quale caso il contenitore di layout impone la propria dimensione il `BoxView`.

Oggetto `BoxView` può essere vincolato in una dimensione e non vincolato in altro. Ad esempio, se il `BoxView` è un figlio di un parametro vertical `StackLayout`, la dimensione verticale dello schermo il `BoxView` è non vincolato e la dimensione orizzontale è vincolato a livello generale. Ma esistono delle eccezioni per la dimensione orizzontale: se il `BoxView` ha relativi `HorizontalOptions` proprietà è impostata su un valore diverso da `LayoutOptions.Fill`, quindi la dimensione orizzontale è inoltre non vincolata. È anche possibile che il `StackLayout` in modo da avere una dimensione orizzontale non vincolata, nel qual caso il `BoxView` saranno anche in senso orizzontale non vincolato.

Il [ **BasicBoxView** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView) nell'esempio vengono visualizzati un quello-pollice-quadrato non vincolato `BoxView` al centro della relativa pagina:

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

Ecco il risultato:

[![Base BoxView](boxview-images/basicboxview-small.png "BoxView base")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

Se il `VerticalOptions` e `HorizontalOptions` le proprietà vengono rimossi dal `BoxView` tag oppure vengono impostate su `Fill`, quindi il `BoxView` diventa limitato dalle dimensioni della pagina e si espande per riempire la pagina.

Oggetto `BoxView` può anche essere un figlio di un `AbsoluteLayout`. In tal caso, sia la posizione e dimensione dei `BoxView` vengono impostate tramite la `LayoutBounds` proprietà associabili associata. Il `AbsoluteLayout` è descritto nell'articolo [ **AbsoluteLayout**](~/xamarin-forms/user-interface/layouts/absolute-layout.md).

Sono riportati esempi di tutti questi casi i programmi di esempio che seguono.

<a name="textdecorations" />

## <a name="rendering-text-decorations"></a>Il rendering delle decorazioni di testo

È possibile usare il `BoxView` aggiungere alcune semplici decorazioni sulle pagine in forma di linee orizzontali e verticali. Il [ **TextDecoration** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration) esempio viene illustrata questa. Tutti gli oggetti visivi del programma sono definiti nel **MainPage. XAML** file, che contiene numerosi `Label` e `BoxView` elementi nel `StackLayout` illustrato di seguito:

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

Tutti i commenti che segue sono elementi figlio del `StackLayout`. Questo markup è costituito da diversi tipi di decorativa `BoxView` elementi usati con il `Label` elemento:

[![Decorazione di testo](boxview-images/textdecoration-small.png "decorazione di testo")](boxview-images/textdecoration-large.png#lightbox "decorazione di testo")

L'intestazione nella parte superiore della pagina elegante viene ottenuta con un `AbsoluteLayout` cui figli rappresentino quattro `BoxView` gli elementi e un `Label`, tutti i dei quali sono assegnati a percorsi specifici e le dimensioni:

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

Nel file XAML, il `AbsoluteLayout` è seguita da un `Label` con testo formattato che descrive il `AbsoluteLayout`.

È possibile sottolineare una stringa di testo racchiudendo entrambi il `Label` e `BoxView` in un `StackLayout` con relativo `HorizontalOptions` valore è impostato su un valore diverso da `Fill`. La larghezza del `StackLayout` viene quindi regolato in base alla larghezza del `Label`, che quindi impone tale larghezza sul `BoxView`. Il `BoxView` viene assegnato solo un'altezza esplicita:

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

È possibile utilizzare questa tecnica per sottolineare il testo singole parole all'interno di stringhe più lunghe di testo o un paragrafo.

È anche possibile usare una `BoxView` simile a quella di un elemento HTML `hr` elemento (regola orizzontale). Lasciare che sia la larghezza del `BoxView` determinato dal contenitore padre, che in questo caso è il `StackLayout`:

```xaml
<BoxView HeightRequest="3" />
```

Infine, per disegnare una linea verticale sul uno lato di un paragrafo di testo, che li racchiude entrambe le `BoxView` e il `Label` in senso orizzontale `StackLayout`. In questo caso, l'altezza del `BoxView` è uguale all'altezza delle `StackLayout`, che è regolato dall'altezza del `Label`:

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

Il `BoxView` è utile per la visualizzazione dei colori. Questo programma Usa una `ListView` per elencare tutti gli statici ReadOnly campi pubblici di xamarin. Forms `Color` struttura:

[![I colori di ListView](boxview-images/listviewcolors-small.png "ListView colori")](boxview-images/listviewcolors-large.png#lightbox "colori di ListView")

Il [ **ListViewColors** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ListViewColors/) programma include una classe denominata `NamedColor`. Il costruttore statico Usa la reflection per accedere a tutti i campi del `Color` strutturare e creare un `NamedColor` oggetto per ognuno di essi. Questi vengono archiviati in statico `All` proprietà:

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

Vengono descritti gli elementi visivi del programma nel file XAML. Il `ItemsSource` proprietà del `ListView` è impostato su statico `NamedColor.All` proprietà, che significa che il `ListView` consente di visualizzare tutti i singoli `NamedColor` oggetti:

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

Il `NamedColor` gli oggetti vengono formattati per il `ViewCell` oggetto impostato come il modello di dati del `ListView`. Questo modello include un `BoxView` cui `Color` proprietà è associata ai `Color` proprietà del `NamedColor` oggetto.

<a name="subclassing" />

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>Esecuzione del gioco del ciclo di vita, dalla creazione di una sottoclasse BoxView

Il gioco del ciclo di vita è una rete cellulare automaton ideato da si ipotizza John Conway e contribuito alla diffusione nelle pagine della *scientifici American* nel 1970s. Una buona introduzione viene fornita nell'articolo di Wikipedia [Conway Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life).

Xamarin. Forms [ **GameOfLife** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/) programma definisce una classe denominata `LifeCell` che deriva da `BoxView`. Questa classe incapsula la logica di una singola cella nel gioco del ciclo di vita:

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

`LifeCell` aggiunge altre tre proprietà per `BoxView`: il `Col` e `Row` proprietà archiviano la posizione della cella all'interno della griglia e il `IsAlive` proprietà indica il proprio stato. Il `IsAlive` proprietà imposta anche il `Color` proprietà del `BoxView` nero se la cella è attivo e bianco se la cella non è attiva.

`LifeCell` installa anche un `TapGestureRecognizer` per consentire all'utente di alternare lo stato di celle da toccandoli. La classe converte il `Tapped` evento dal riconoscimento di movimento in un proprio `Tapped` evento.

Il **GameOfLife** program include anche una `LifeGrid` classe che incapsula gran parte della logica del gioco, e un `MainPage` classe che gestisce gli oggetti visivi del programma. Ad esempio una sovrimpressione che descrive le regole del gioco. Ecco il programma in azione con un paio centinaia `LifeCell` oggetti sulla pagina:

[![Gioco del ciclo di vita](boxview-images/gameoflife-small.png "gioco del ciclo di vita")](boxview-images/gameoflife-large.png#lightbox "gioco del ciclo di vita")

<a name="digitalclock" />

## <a name="creating-a-digital-clock"></a>Creazione di un orologio digitale

Il [ **DotMatrixClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock/) programma crea 210 `BoxView` elementi per simulare i punti di una visualizzazione da-5-7 aghi vecchio. È possibile leggere l'ora in modalità verticale o orizzontale, ma è più grande in formato orizzontale:

[![Clock aghi](boxview-images/dotmatrixclock-small.png "Clock aghi")](boxview-images/dotmatrixclock-large.png#lightbox "aghi orologio")

Il file XAML poco più di creare un'istanza di `AbsoluteLayout` utilizzato per l'orologio:

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

Tutto il resto si verifica nel file code-behind. La logica di visualizzazione aghi viene notevolmente semplificata la definizione di matrici diverse che descrivono i punti corrispondenti a ciascuno dei 10 cifre e i due punti:

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

Questi campi terminano con una matrice tridimensionale di `BoxView` elementi per archiviare i modelli dot per le sei cifre.

Il costruttore crea tutti il `BoxView` elementi per le cifre e i due punti e inizializza il `Color` proprietà del `BoxView` elementi per i due punti:

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

Questo programma Usa il posizionamento relativo e la funzionalità di ridimensionamento di `AbsoluteLayout`. La larghezza e altezza della ognuno `BoxView` vengono impostati sui valori frazionari, in particolare dall'85% delle 1 diviso per il numero di punti in orizzontale e verticale. Anche le posizioni vengono impostate su valori frazionari.

Poiché tutte le posizioni e le dimensioni sono relativo alle dimensioni totali dei `AbsoluteLayout`, il `SizeChanged` gestore per la pagina solo necessario impostare un `HeightRequest` del `AbsoluteLayout`:

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

La larghezza del `AbsoluteLayout` viene impostato automaticamente poiché il metodo estende per l'intera larghezza della pagina.

Il codice finale nel `MainPage` classe elabora il callback di timer e i colori dei punti della ogni cifra. La definizione delle matrici multidimensionali all'inizio del file code-behind consente di rendere questa logica la parte più semplice del programma:

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

Un orologio aghi può sembrare un'applicazione di ovvia `BoxView`, ma `BoxView` anche gli elementi sono in grado di realizzare un orologio analogico:

[![Clock BoxView](boxview-images/boxviewclock-small.png "Clock BoxView")](boxview-images/boxviewclock-large.png#lightbox "BoxView orologio")

Tutti gli oggetti visivi nel [ **BoxViewClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/) programma sono elementi figlio di un `AbsoluteLayout`. Questi elementi vengono ridimensionati con il `LayoutBounds` proprietà associata e ruotato utilizzando i `Rotation` proprietà.

I tre `BoxView` elementi per il movimento delle lancette dell'orologio creata un'istanza nel file XAML, ma non posizionati o ridimensionati:

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

Il costruttore del file code-behind viene creata un'istanza di 60 `BoxView` elementi per i segni di graduazione lungo la circonferenza del clock di:

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

Il ridimensionamento e posizionamento di tutti i `BoxView` elementi si verifica nel `SizeChanged` gestore per il `AbsoluteLayout`. Una piccola struttura interna per la classe denominata `HandParams` descrive le dimensioni della ognuno dei tre messi a disposizione relativo alle dimensioni totali dell'orologio:

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

Il `SizeChanged` gestore determina il centro e il raggio del `AbsoluteLayout`e quindi ridimensiona e posiziona il 60 `BoxView` elementi usati come segni di graduazione. Il `for` ciclo termina impostando il `Rotation` proprietà di ognuno di questi `BoxView` elementi. Alla fine del `SizeChanged` gestore, la `LayoutHand` metodo viene chiamato per ridimensionare e posizionare il tre movimento delle lancette dell'orologio:

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

Il `LayoutHand` metodo dimensioni mentre posiziona ogni indicatore in modo da puntare direttamente fino alla posizione di 12:00. Alla fine del metodo, il `AnchorY` è impostata su una posizione corrispondente al centro dell'orologio. Ciò indica al centro della rotazione.

Il movimento delle lancette viene ruotati nella funzione di callback di timer:

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

L'icona della mano secondo viene trattato in modo leggermente diverso: una funzione di interpolazione di animazione viene applicata per semplificare lo spostamento sembrano meccanico anziché smooth. Su ciascun tick della seconda lancetta estrae un po' e quindi parcellizzazione relativa destinazione. Questo piccolo frammento di codice aggiunge molto il realismo del movimento.

## <a name="conclusion"></a>Conclusione

Il `BoxView` può sembrare semplice alla prima di tutto, ma come si è visto, può essere piuttosto versatile e possono riprodurre quasi oggetti visivi che sono in genere possibili solo con la grafica vettoriale. Per gli elementi grafici più sofisticati, consultare [uso di SkiaSharp in xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md).


## <a name="related-links"></a>Collegamenti correlati

- [BoxView Basic (esempio)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)
- [Decorazione di testo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration)
- [Casella di riepilogo di colore (esempio)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox)
- [Gioco del ciclo di vita (esempio)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife)
- [Matrice di punti orologio (esempio)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock)
- [BoxView Clock (esempio)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock)
- [BoxView](xref:Xamarin.Forms.BoxView)
