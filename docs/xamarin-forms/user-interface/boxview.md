---
title: BoxView
description: Utilizzare un rettangolo colorato per effetto, grafica e interazione.
ms.topic: article
ms.prod: xamarin
ms.assetid: 4CBF703D-84A0-4CDF-A433-5926B587782A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2017
ms.openlocfilehash: 4d50ea5c3db0f5a141f1b48cf0a948c10b63f7f0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="boxview"></a>BoxView

[`BoxView`](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) esegue il rendering di un rettangolo di una larghezza, altezza e colore. È possibile utilizzare `BoxView` per effetto, rudimentale grafica e per l'interazione dell'utente tramite tocco.

Poiché xamarin. Forms non dispone di un sistema di grafica vettoriale incorporato, il `BoxView` consente di compensare. Alcuni dei programmi di esempio descritti in questo articolo utilizzano `BoxView` per il rendering della grafica. Il `BoxView` può essere ridimensionato per sono simili a una riga di una larghezza e lo spessore e quindi ruotata di qualsiasi valore angolare utilizzando il `Rotation` proprietà.

Sebbene `BoxView` può simulare elementi grafici semplici, è opportuno esaminare [utilizzando SkiaSharp in xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) per i requisiti di grafica più sofisticati.

In questo articolo vengono illustrati gli argomenti seguenti:

- **[Impostazione BoxView colore e dimensioni](#colorandsize)**  &ndash; impostare il `BoxView` proprietà.
- **[Effetti del testo per il rendering](#textdecorations)**  &ndash; utilizzare un `BoxView` per le righe per il rendering.
- **[Elenco di colori con BoxView](#listingcolors)**  &ndash; visualizzare tutti i colori di sistema in un `ListView`.
- **[Riproduzione di gioco di vita, dalla creazione di una sottoclasse BoxView](#subclassing)**  &ndash; implementare un famoso automa cellulare.
- **[Creazione di un orario in formato digitale](#digitalclock)**  &ndash; simulare una visualizzazione matrice di punti.
- **[Creazione di un orologio analogico](#analogclock)**  &ndash; trasformare e animare `BoxView` elementi.

<a name="colorandsize" />

## <a name="setting-boxview-color-and-size"></a>Impostazione BoxView colore e dimensioni

Molto spesso si imposteranno le tre proprietà seguenti del `BoxView`:

- [`Color`](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) Per impostare il colore.
- [`WidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) Per impostare la larghezza del `BoxView` in unità indipendenti dal dispositivo.
- [`HeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) Per impostare l'altezza del `BoxView`.

Il `Color` proprietà è di tipo `Color`; la proprietà può essere impostata su qualsiasi `Color` valore, inclusi i campi di sola lettura statici 141 di denominato colori compreso tra in ordine alfabetico `AliceBlue` a `YellowGreen`.

Il `WidthRequest` e `HeightRequest` proprietà svolgono un ruolo solo se il `BoxView` è *non vincolato* nel layout. Ciò avviene quando è necessario conoscere l'elemento figlio della dimensione, ad esempio, quando il contenitore di layout di `BoxView` è un elemento figlio di una cella di ridimensionamento automatico nel `Grid` layout. Oggetto `BoxView` è non vincolato anche quando il relativo `HorizontalOptions` e `VerticalOptions` sono impostate su valori diversi da `LayoutOptions.Fill`. Se il `BoxView` vincolata, ma la `WidthRequest` e `HeightRequest` non vengono impostate proprietà, quindi la larghezza o altezza sono impostate sui valori predefiniti di 40 unità o circa 1/4 pollici nei dispositivi mobili.

Il `WidthRequest` e `HeightRequest` proprietà vengono ignorate se la `BoxView` è *vincolata* nel layout, in cui il contenitore di layout case impone relative dimensioni nel `BoxView`. 

Oggetto `BoxView` può essere vincolato in una dimensione e non vincolato in altro. Ad esempio, se il `BoxView` è un elemento figlio di un controllo verticale `StackLayout`, la dimensione verticale del `BoxView` è non vincolato e la relativa dimensione orizzontale in genere è vincolata. Ma esistono alcune eccezioni per la dimensione orizzontale: se il `BoxView` è relativo `HorizontalOptions` proprietà impostata su un valore diverso da `LayoutOptions.Fill`, la dimensione orizzontale è anche non vincolata. È anche possibile che il `StackLayout` in modo da avere una dimensione orizzontale non vincolata, nel qual caso il `BoxView` saranno anche in senso orizzontale non vincolato.

Il [ **BasicBoxView** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView) nell'esempio viene visualizzato un uno pollici-quadrati non vincolato `BoxView` al centro della pagina:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BasicBoxView"
             x:Class="BasicBoxView.MainPage">

    <BoxView Color="CornflowerBlue"
             WidthRequest="160"
             HeightRequest="160"
             VerticalOptions="Center" 
             HorizontalOptions="Center" />

</ContentPage>
```

Di seguito è riportato il risultato:

[![Base BoxView](boxview-images/basicboxview-small.png "BoxView base")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

Se il `VerticalOptions` e `HorizontalOptions` proprietà vengono rimossi dal `BoxView` tag oppure vengono impostate su `Fill`, quindi il `BoxView` diventa vincolato dalle dimensioni della pagina e si espande per riempire la pagina.

Oggetto `BoxView` può anche essere un figlio di un `AbsoluteLayout`. In questo caso, sia la posizione e le dimensioni del `BoxView` vengono impostati utilizzando il `LayoutBounds` proprietà associabile associata. Il `AbsoluteLayout` è descritto nell'articolo [ **AbsoluteLayout**](~/xamarin-forms/user-interface/layouts/absolute-layout.md).

Si noterà che tutti questi casi i programmi di esempio che seguono alcuni esempi.

<a name="textdecorations" />

## <a name="rendering-text-decorations"></a>Effetti del testo per il rendering

È possibile utilizzare il `BoxView` aggiungere alcuni effetti semplice nelle pagine sotto forma di linee verticali e orizzontali. Il [ **TextDecoration** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration) esempio viene illustrata questa. Tutti gli oggetti visivi del programma sono definiti nel **MainPage. XAML** file che contiene diversi `Label` e `BoxView` gli elementi di `StackLayout` illustrato di seguito:

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

Tutti i commenti che segue sono figli di `StackLayout`. Questo tag è costituito da diversi tipi di decorativo `BoxView` elementi utilizzati con il `Label` elemento:

[![Effetto di testo](boxview-images/textdecoration-small.png "barrato")](boxview-images/textdecoration-large.png#lightbox "decorazione di testo")

L'intestazione nella parte superiore della pagina elegante viene ottenuta con un `AbsoluteLayout` i cui elementi figlio disponibili quattro `BoxView` elementi e un `Label`tutti dei quali sono assegnati a percorsi specifici e le dimensioni:

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

Nel file XAML, il `AbsoluteLayout` è seguita da un `Label` con in formato testo che descrive il `AbsoluteLayout`.

È possibile sottolineare una stringa di testo racchiudendo entrambi il `Label` e `BoxView` in un `StackLayout` con relativo `HorizontalOptions` valore è impostato su un valore diverso da `Fill`. La larghezza del `StackLayout` quindi è regolata dalla larghezza del `Label`, che quindi impone che la larghezza nella `BoxView`. Il `BoxView` viene assegnato solo un'altezza esplicita:

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

È possibile utilizzare questa tecnica per sottolineare singole parole all'interno di stringhe più lunghe di testo o un paragrafo.

È anche possibile usare un `BoxView` simile a quella di un elemento HTML `hr` elemento (regola orizzontale). Lasciare che la larghezza del `BoxView` determinato dal contenitore padre, ovvero in questo caso il `StackLayout`:

```xaml
<BoxView HeightRequest="3" />
```

Infine, si disegna una linea verticale sul uno lato di un paragrafo di testo racchiudendo entrambi il `BoxView` e `Label` in orizzontale `StackLayout`. In questo caso, l'altezza del `BoxView` è uguale all'altezza delle `StackLayout`, che non sia disciplinato dall'altezza del `Label`: 

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView WidthRequest="4"
             Margin="0, 0, 10, 0" />
    <Label>

        ···

    </Label>
/StackLayout>
```
<a name="listingcolors" />

## <a name="listing-colors-with-boxview"></a>Elenco di colori con BoxView

Il `BoxView` è utile per la visualizzazione dei colori. Questo programma utilizza una `ListView` per elencare tutti gli statico in sola lettura campi pubblici di xamarin. Forms `Color` struttura:

[![Colori di ListView](boxview-images/listviewcolors-small.png "colori ListView")](boxview-images/listviewcolors-large.png#lightbox "colori di ListView")

Il [ **ListViewColors** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ListViewColors/) programma include una classe denominata `NamedColor`. Il costruttore statico utilizza la reflection per accedere a tutti i campi del `Color` struttura e creare un `NamedColor` oggetto per ognuno di essi. Questi elementi sono archiviati in statica `All` proprietà:

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

Gli elementi visivi del programma sono descritte nel file XAML. Il `ItemsSource` proprietà del `ListView` è impostato su statica `NamedColor.All` proprietà, il che significa che il `ListView` Visualizza tutti i singoli `NamedColor` oggetti: 

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

Il `NamedColor` gli oggetti vengono formattati per la `ViewCell` oggetto impostato come modello di dati del `ListView`. Questo modello include un `BoxView` cui `Color` proprietà è associata al `Color` proprietà del `NamedColor` oggetto.

<a name="subclassing" />

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>Esecuzione del gioco del ciclo di vita per la creazione di sottoclassi BoxView

Il gioco di ciclo di vita è un automa cellulare ideato da si ipotizza John Conway e resa nota nelle pagine di *scientifici American* nel 1970s. Una buona introduzione viene fornita per l'articolo di Wikipedia [gioco di vita di Conway](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life).

Di xamarin. Forms [ **GameOfLife** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/) programma definisce una classe denominata `LifeCell` che deriva da `BoxView`. Questa classe incapsula la logica di una singola cella il gioco di ciclo di vita:

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

`LifeCell` aggiunge tre ulteriori proprietà per `BoxView`: il `Col` e `Row` proprietà archiviano la posizione della cella all'interno della griglia e `IsAlive` proprietà indica lo stato. Il `IsAlive` proprietà imposta inoltre il `Color` proprietà del `BoxView` su nero se la cella è attivo e bianca se la cella non è attiva.

`LifeCell` installa anche un `TapGestureRecognizer` per consentire all'utente di attivare o disattivare lo stato di celle toccando li. Converte la classe di `Tapped` evento da riconoscitore di movimento in proprio `Tapped` evento.

Il **GameOfLife** programma include anche un `LifeGrid` classe che incapsula la logica del gioco, e un `MainPage` che gestisce gli oggetti visivi del programma. Questi includono una sovrapposizione che descrive le regole del gioco. Ecco il programma in azioni che mostra a poche centinaia `LifeCell` gli oggetti della pagina:

[![Gioco del ciclo di vita](boxview-images/gameoflife-small.png "gioco del ciclo di vita")](boxview-images/gameoflife-large.png#lightbox "gioco del ciclo di vita")

<a name="digitalclock" />

## <a name="creating-a-digital-clock"></a>Creazione di un orario in formato digitale

Il [ **DotMatrixClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock/) programma crea 210 `BoxView` elementi per simulare i punti di un monitor da 5 a 7 aghi datato. È possibile leggere l'ora in modalità verticale o orizzontale, ma è maggiore in orizzontale:

[![Clock aghi](boxview-images/dotmatrixclock-small.png "aghi Clock")](boxview-images/dotmatrixclock-large.png#lightbox "aghi Clock")

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

Tutti gli altri elementi si verifica nel file code-behind. La logica di visualizzazione aghi sia notevolmente semplificata per la definizione di numerose matrici che descrivono i punti corrispondenti alle singole di 10 cifre e i due punti:

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

Questi campi terminano con una matrice tridimensionale di `BoxView` elementi per archiviare i modelli di punto per le sei cifre.

Il costruttore crea tutte le `BoxView` elementi per le cifre e i due punti, nonché consente di inizializzare il `Color` proprietà del `BoxView` elementi per i due punti:

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

Questo programma utilizza il posizionamento relativo e la funzionalità di ridimensionamento di `AbsoluteLayout`. La larghezza e l'altezza di ogni `BoxView` sono impostate sui valori frazionari, in particolare l'85% 1 diviso per il numero di punti in orizzontale e verticale. Le posizioni vengono inoltre impostate su valori frazionari. 

Perché tutte le posizioni e le dimensioni riguardano le dimensioni totali del `AbsoluteLayout`, `SizeChanged` gestore per la pagina è necessario impostare solo un `HeightRequest` del `AbsoluteLayout`:

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

La larghezza del `AbsoluteLayout` viene impostata automaticamente perché il metodo estende alla larghezza della pagina.

Il codice finale nella `MainPage` classe elabora il callback del timer e i colori dei punti della ogni cifra. La definizione delle matrici multidimensionali all'inizio del file code-behind consente di rendere questa logica la parte più semplice del programma:

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

Un clock aghi può sembrare un'applicazione di ovvio `BoxView`, ma `BoxView` anche gli elementi sono in grado di comprendere un orologio analogico:

[![Clock BoxView](boxview-images/boxviewclock-small.png "BoxView Clock")](boxview-images/boxviewclock-large.png#lightbox "BoxView Clock")

Tutti gli oggetti visivi di [ **BoxViewClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/) programma sono elementi figlio di un `AbsoluteLayout`. Questi elementi vengono ridimensionati con il `LayoutBounds` proprietà associata e ruotato utilizzando il `Rotation` proprietà. 

I tre `BoxView` elementi per lancette dell'orologio creati nel file XAML, ma non posizionati o dimensioni:

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

Il costruttore del file di codice crea un'istanza di 60 `BoxView` elementi per i segni di graduazione lungo la circonferenza del clock di:

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

Il ridimensionamento e posizionamento di tutti i `BoxView` elementi si verifica nel `SizeChanged` gestore per il `AbsoluteLayout`. Una piccola struttura interna per la classe denominata `HandParams` descrive le dimensioni di ciascuno dei tre mani relativo alle dimensioni totali dell'orologio:

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

Il `SizeChanged` gestore determina il centro e il raggio il `AbsoluteLayout`e quindi di dimensioni e posiziona il 60 `BoxView` elementi utilizzati di segni di graduazione. Il `for` ciclo si conclude con l'impostazione di `Rotation` proprietà di ciascuna di esse `BoxView` elementi. Alla fine del `SizeChanged` gestore, la `LayoutHand` metodo viene chiamato per ridimensionare e posizionare i tre lancette dell'orologio:

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

Il `LayoutHand` metodo ridimensiona e posiziona ogni indicatore in modo da puntare direttamente fino alla posizione di 12:00. Alla fine del metodo, il `AnchorY` è impostata su una posizione corrispondente al centro dell'orologio. Indica il centro di rotazione.

Gli indicatori vengono ruotati nella funzione di callback del timer:

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

L'icona della mano secondo viene trattato in modo leggermente diverso: viene applicata un'animazione, la funzione di interpolazione per rendere lo spostamento sembra meccanico anziché smooth. In ogni segno di graduazione, l'icona della mano secondo estrae un po' e quindi parcellizzazione relativa destinazione. Questo codice poco aggiunge molto realismo dello spostamento.

## <a name="conclusion"></a>Conclusione

Il `BoxView` potrebbe sembrare semplice in iniziale, ma si è visto, può essere piuttosto versatile e possono riprodurre quasi oggetti visivi che sono in genere possibili solo con la grafica vettoriale. Per gli elementi grafici più sofisticati, consultare [utilizzando SkiaSharp in xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md).


## <a name="related-links"></a>Collegamenti correlati

- [Base BoxView (esempio)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)
- [Decorazione di testo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration)
- [Colore ListBox (esempio)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox)
- [Gioco del ciclo di vita (esempio)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife)
- [Matrice di punti Clock (esempio)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock)
- [BoxView Clock (esempio)](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock)
- [BoxView](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)
