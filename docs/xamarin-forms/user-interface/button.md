---
title: Xamarin.FormsPulsante
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f7309794768c01d4978aa369cd1950d4986938db
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137462"
---
# <a name="xamarinforms-button"></a>Xamarin.FormsPulsante

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)

_Il pulsante risponde a un tocco o a un clic che indirizza un'applicazione per eseguire un'attività specifica._

[`Button`](xref:Xamarin.Forms.Button)È il controllo interattivo più fondamentale in tutti gli elementi Xamarin.Forms . In `Button` genere viene visualizzata una stringa di testo breve che indica un comando, ma è anche possibile visualizzare un'immagine bitmap o una combinazione di testo e un'immagine. L'utente preme con un `Button` dito o fa clic su di esso con il mouse per avviare il comando.

La maggior parte degli argomenti illustrati di seguito corrisponde alle pagine nell'esempio [**ButtonDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) .

## <a name="handling-button-clicks"></a>Gestione dei clic dei pulsanti

`Button`definisce un [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento che viene generato quando l'utente tocca l'oggetto `Button` con un dito o un puntatore del mouse. L'evento viene generato quando il dito o il pulsante del mouse viene rilasciato dall'area di `Button` . Il `Button` deve avere la [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) proprietà impostata su `true` affinché risponda ai tocchi.

Nella pagina di **clic del pulsante di base** nell'esempio [**ButtonDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) viene illustrato come creare un'istanza di `Button` in XAML e gestirne l' `Clicked` evento. Il file **BasicButtonClickPage. XAML** contiene un oggetto con un oggetto `StackLayout` `Label` e un oggetto `Button` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.BasicButtonClickPage"
             Title="Basic Button Click">
    <StackLayout>

        <Label x:Name="label"
               Text="Click the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Click to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

`Button`Tende a occupare tutto lo spazio consentito. Se, ad esempio, non si imposta la `HorizontalOptions` proprietà di `Button` su un valore diverso da `Fill` , l'oggetto occuperà `Button` la larghezza completa del relativo elemento padre.

Per impostazione predefinita, `Button` è rettangolare, ma è possibile assegnargli angoli arrotondati usando la [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) proprietà, come descritto di seguito nell' [**aspetto del pulsante**](#button-appearance)della sezione.

La [`Text`](xref:Xamarin.Forms.Button.Text) proprietà specifica il testo visualizzato in `Button` . L' [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento è impostato su un gestore eventi denominato `OnButtonClicked` . Questo gestore si trova nel file code-behind, **BasicButtonClickPage.XAML.cs**:

```csharp
public partial class BasicButtonClickPage : ContentPage
{
    public BasicButtonClickPage ()
    {
        InitializeComponent ();
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        await label.RelRotateTo(360, 1000);
    }
}
```

Quando si tocca `Button` viene eseguito il metodo `OnButtonClicked`. L' `sender` argomento è l' `Button` oggetto responsabile di questo evento. È possibile usarlo per accedere all' `Button` oggetto o per distinguere tra più `Button` oggetti che condividono lo stesso `Clicked` evento.

Questo particolare `Clicked` gestore chiama una funzione di animazione che ruota i `Label` 360 gradi in 1000 millisecondi. Ecco il programma in esecuzione su dispositivi iOS e Android e come applicazione piattaforma UWP (Universal Windows Platform) (UWP) sul desktop di Windows 10:

[![Clic sul pulsante di base](button-images/BasicButtonClick.png "Clic sul pulsante di base")](button-images/BasicButtonClick-Large.png#lightbox "Clic sul pulsante di base")

Si noti che il `OnButtonClicked` metodo include il `async` modificatore perché `await` viene usato all'interno del gestore eventi. Un `Clicked` gestore eventi richiede il `async` modificatore solo se il corpo del gestore USA `await` .

Ogni piattaforma esegue il rendering `Button` di in modo specifico. Nella sezione relativa all' [**aspetto del pulsante**](#button-appearance) verrà illustrato come impostare i colori e rendere `Button` visibile il bordo per un aspetto più personalizzato. `Button`implementa l' [`IFontElement`](xref:Xamarin.Forms.Internals.IFontElement) interfaccia, in modo che includa le [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) [`FontSize`](xref:Xamarin.Forms.Button.FontSize) proprietà, e [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) .

## <a name="creating-a-button-in-code"></a>Creazione di un pulsante nel codice

È comune creare un'istanza di `Button` in XAML, ma è anche possibile creare un oggetto `Button` nel codice. Questa operazione può risultare utile quando l'applicazione deve creare più pulsanti basati su dati enumerabili con un `foreach` ciclo.

Nella pagina di **clic del pulsante codice** viene illustrato come creare una pagina equivalente dal punto di vista funzionale alla pagina di **clic del pulsante di base** , ma interamente in C#:

```csharp
public class CodeButtonClickPage : ContentPage
{
    public CodeButtonClickPage ()
    {
        Title = "Code Button Click";

        Label label = new Label
        {
            Text = "Click the Button below",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };

        Button button = new Button
        {
            Text = "Click to Rotate Text!",
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };
        button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);

        Content = new StackLayout
        {
            Children =
            {
                label,
                button
            }
        };
    }
}
```

Tutto viene eseguito nel costruttore della classe. Poiché il `Clicked` gestore è una sola istruzione Long, può essere collegato all'evento in modo molto semplice:

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

Naturalmente, è anche possibile definire il gestore eventi come metodo separato (proprio come il `OnButtonClick` metodo in **clic sul pulsante di base**) e alleghiare tale metodo all'evento:

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Disabilitazione del pulsante

A volte un'applicazione si trova in uno stato specifico in cui un particolare `Button` clic non è un'operazione valida. In questi casi, l'oggetto `Button` deve essere disabilitato impostando la relativa `IsEnabled` proprietà su `false` . L'esempio classico è un `Entry` controllo per un nome file accompagnato da un file-Open `Button` : `Button` deve essere abilitato solo se è stato digitato un testo in `Entry` .
È possibile usare un oggetto `DataTrigger` per questa attività, come illustrato nell'articolo [**trigger di dati**](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) .

## <a name="using-the-command-interface"></a>Utilizzo dell'interfaccia di comando

È possibile che un'applicazione risponda ai `Button` rubinetti senza gestire l' `Clicked` evento. `Button`Implementa un meccanismo di notifica alternativo denominato _comando_ o interfaccia di _comando_ . È costituito da due proprietà:

- [`Command`](xref:Xamarin.Forms.Button.Command)di tipo [`ICommand`](xref:System.Windows.Input.ICommand) , un'interfaccia definita nello [`System.Windows.Input`](xref:System.Windows.Input) spazio dei nomi.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)Proprietà di tipo [`Object`](xref:System.Object) .

Questo approccio è particolarmente adatto per la connessione al data binding e, in particolare, quando si implementa l'architettura MVC (Model-View-ViewModel). Questi argomenti sono illustrati nel [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)degli articoli, [da data binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)e [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

In un'applicazione MVVM, il ViewModel definisce le proprietà di tipo `ICommand` che vengono quindi connesse agli `Button` elementi XAML con le associazioni dati. Xamarin.Formsdefinisce inoltre [`Command`](xref:Xamarin.Forms.Command) le [`Command<T>`](xref:Xamarin.Forms.Command`1) classi e che implementano l' `ICommand` interfaccia e assistono il ViewModel nella definizione delle proprietà di tipo `ICommand` .

Il comando viene descritto più dettagliatamente nell'articolo [**l'interfaccia del comando**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) , ma la pagina di **comando del pulsante Basic** nell'esempio [**ButtonDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) illustra l'approccio di base.

La `CommandDemoViewModel` classe è un ViewModel molto semplice che definisce una proprietà di tipo `double` denominato e `Number` due proprietà di tipo `ICommand` denominate `MultiplyBy2Command` e `DivideBy2Command` :

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    double number = 1;

    public event PropertyChangedEventHandler PropertyChanged;

    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(() => Number *= 2);

        DivideBy2Command = new Command(() => Number /= 2);
    }

    public double Number
    {
        set
        {
            if (number != value)
            {
                number = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Number"));
            }
        }
        get
        {
            return number;
        }
    }

    public ICommand MultiplyBy2Command { private set; get; }

    public ICommand DivideBy2Command { private set; get; }
}
```

Le due `ICommand` proprietà vengono inizializzate nel costruttore della classe con due oggetti di tipo `Command` . I `Command` costruttori includono una piccola funzione (denominata argomento del `execute` costruttore) che raddoppia o dimezza la `Number` Proprietà.

Il file **BasicButtonCommand. XAML** imposta la proprietà `BindingContext` su un'istanza di `CommandDemoViewModel` . L' `Label` elemento e due `Button` elementi contengono associazioni alle tre proprietà in `CommandDemoViewModel` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.BasicButtonCommandPage"
             Title="Basic Button Command">

    <ContentPage.BindingContext>
        <local:CommandDemoViewModel />
    </ContentPage.BindingContext>

    <StackLayout>
        <Label Text="{Binding Number, StringFormat='Value is now {0}'}"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Multiply by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding MultiplyBy2Command}" />

        <Button Text="Divide by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding DivideBy2Command}" />
    </StackLayout>
</ContentPage>
```

Quando `Button` vengono toccati i due elementi, i comandi vengono eseguiti e il numero modifica il valore:

[![Comando pulsante di base](button-images/BasicButtonCommand.png "Comando pulsante di base")](button-images/BasicButtonCommand-Large.png#lightbox)

Il vantaggio di questo approccio rispetto ai `Clicked` gestori è che tutta la logica che coinvolge la funzionalità di questa pagina si trova nel ViewModel piuttosto che nel file code-behind, ottenendo una migliore separazione dell'interfaccia utente dalla logica di business.

È anche possibile che gli `Command` oggetti controllino l'abilitazione e la disabilitazione degli `Button` elementi. Si supponga, ad esempio, di voler limitare l'intervallo di valori numerici compreso tra 2<sup>10</sup> e 2<sup> &ndash; 10</sup>. È possibile aggiungere un'altra funzione al costruttore (denominato `canExecute` argomento) che restituisce `true` se `Button` deve essere abilitato. Di seguito viene illustrata la modifica del `CommandDemoViewModel` costruttore:

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    ···
    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(
            execute: () =>
            {
                Number *= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number < Math.Pow(2, 10));

        DivideBy2Command = new Command(
            execute: () =>
            {
                Number /= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number > Math.Pow(2, -10));
    }
    ···
}
```

Le chiamate al `ChangeCanExecute` metodo di `Command` sono necessarie in modo che il `Command` metodo possa chiamare il `canExecute` metodo e determinare se `Button` deve essere disabilitato o meno. Con questa modifica del codice, poiché il numero raggiunge il limite, `Button` è disabilitato:

[![Comando pulsante di base-modificato](button-images/BasicButtonCommandModified.png "Comando pulsante di base-modificato")](button-images/BasicButtonCommandModified-Large.png#lightbox)

È possibile che due o più `Button` elementi siano associati alla stessa `ICommand` Proprietà. Gli `Button` elementi possono essere distinti usando la [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) proprietà di `Button` . In questo caso, è consigliabile usare la classe generica [`Command<T>`](xref:Xamarin.Forms.Command`1) . L' `CommandParameter` oggetto viene quindi passato come argomento ai `execute` `canExecute` metodi e. Questa tecnica è illustrata in dettaglio nella sezione di [**comando di base**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) dell'articolo sull' [**interfaccia di comando**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) .

L'esempio [**ButtonDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) usa anche questa tecnica nella relativa `MainPage` classe. Il file **MainPage. XAML** contiene una `Button` pagina per ogni pagina dell'esempio:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.MainPage"
             Title="Button Demos">
    <ScrollView>
        <FlexLayout Direction="Column"
                    JustifyContent="SpaceEvenly"
                    AlignItems="Center">

            <Button Text="Basic Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonClickPage}" />

            <Button Text="Code Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:CodeButtonClickPage}" />

            <Button Text="Basic Button Command"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonCommandPage}" />

            <Button Text="Press and Release Button"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:PressAndReleaseButtonPage}" />

            <Button Text="Button Appearance"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ButtonAppearancePage}" />

            <Button Text="Toggle Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ToggleButtonDemoPage}" />

            <Button Text="Image Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ImageButtonDemoPage}" />

        </FlexLayout>
    </ScrollView>
</ContentPage>
```

Ogni `Button` proprietà è `Command` associata a una proprietà denominata `NavigateCommand` e l' `CommandParameter` oggetto è impostato su un [`Type`](xref:System.Type) oggetto corrispondente a una delle classi di pagina nel progetto.

Tale `NavigateCommand` proprietà è di tipo `ICommand` e viene definita nel file code-behind:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

Il costruttore inizializza la `NavigateCommand` proprietà su un `Command<Type>` oggetto perché `Type` è il tipo del `CommandParameter` set di oggetti nel file XAML. Questo significa che il `execute` metodo ha un argomento di tipo `Type` che corrisponde a questo `CommandParameter` oggetto. La funzione crea un'istanza della pagina e quindi la passa a tale pagina.

Si noti che il costruttore si conclude impostando `BindingContext` su se stesso. Questa operazione è necessaria per l'associazione delle proprietà nel file XAML alla `NavigateCommand` Proprietà.

## <a name="pressing-and-releasing-the-button"></a>Premere e rilasciare il pulsante

Oltre all' `Clicked` evento, `Button` definisce anche [`Pressed`](xref:Xamarin.Forms.Button.Pressed) [`Released`](xref:Xamarin.Forms.Button.Released) gli eventi e. L' `Pressed` evento si verifica quando si preme un dito su un oggetto `Button` o si preme un pulsante del mouse con il puntatore posizionato su `Button` . L' `Released` evento si verifica quando viene rilasciato il dito o il pulsante del mouse. In genere, un `Clicked` evento viene generato contemporaneamente all' `Released` evento, ma se il puntatore del dito o del mouse si allontana dalla superficie di `Button` prima del rilascio, l' `Clicked` evento potrebbe non verificarsi.

Gli `Pressed` `Released` eventi e non vengono spesso usati, ma possono essere usati per scopi speciali, come illustrato nella pagina **pulsante di stampa e rilascio** . Il file XAML contiene un oggetto `Label` e un oggetto `Button` con gestori collegati per `Pressed` gli `Released` eventi e:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.PressAndReleaseButtonPage"
             Title="Press and Release Button">
    <StackLayout>

        <Label x:Name="label"
               Text="Press and hold the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Press to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Pressed="OnButtonPressed"
                Released="OnButtonReleased" />

    </StackLayout>
</ContentPage>
```

Il file code-behind anima l'oggetto `Label` quando `Pressed` si verifica un evento, ma sospende la rotazione quando `Released` si verifica un evento:

```csharp
public partial class PressAndReleaseButtonPage : ContentPage
{
    bool animationInProgress = false;
    Stopwatch stopwatch = new Stopwatch();

    public PressAndReleaseButtonPage ()
    {
        InitializeComponent ();
    }

    void OnButtonPressed(object sender, EventArgs args)
    {
        stopwatch.Start();
        animationInProgress = true;

        Device.StartTimer(TimeSpan.FromMilliseconds(16), () =>
        {
            label.Rotation = 360 * (stopwatch.Elapsed.TotalSeconds % 1);

            return animationInProgress;
        });
    }

    void OnButtonReleased(object sender, EventArgs args)
    {
        animationInProgress = false;
        stopwatch.Stop();
    }
}
```

Il risultato è che l'oggetto `Label` ruota solo quando un dito è in contatto con l'oggetto `Button` e si interrompe quando viene rilasciato il dito:

[![Pulsante premi e rilascia](button-images/PressAndReleaseButton.png "Pulsante premi e rilascia")](button-images/PressAndReleaseButton-Large.png)

Questo tipo di comportamento ha applicazioni per i giochi: un dito su un `Button` oggetto potrebbe far spostare un oggetto sullo schermo in una direzione specifica.

<a name="button-appearance" />

## <a name="button-appearance"></a>Aspetto del pulsante

`Button`Eredita o definisce diverse proprietà che influiscono sull'aspetto:

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)colore del `Button` testo
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)colore dello sfondo del testo
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor)colore di un'area che circonda l'oggetto`Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)famiglia di caratteri utilizzata per il testo
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)dimensione del testo
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)indica se il testo è in corsivo o in grassetto.
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth)larghezza del bordo
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius)è il raggio dell'angolo del`Button`
- `CharacterSpacing`spaziatura tra i caratteri del `Button` testo

> [!NOTE]
> La `Button` classe dispone inoltre [`Margin`](xref:Xamarin.Forms.View.Margin) [`Padding`](xref:Xamarin.Forms.Button.Padding) di proprietà e che controllano il comportamento del layout di `Button` . Per altre informazioni, vedere l'articolo sulle proprietà [Margin e Padding](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

Gli effetti di sei di queste proprietà (ad eccezione di `FontFamily` e `FontAttributes` ) sono illustrati nella pagina relativa all' **aspetto del pulsante** . Un'altra proprietà, [`Image`](xref:Xamarin.Forms.Button.ImageSource) , viene illustrata nella sezione [**utilizzo di bitmap con Button**](#image-button).

Tutte le visualizzazioni e le associazioni dati nella pagina **aspetto pulsante** sono definite nel file XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ButtonAppearancePage"
             Title="Button Appearance">
    <StackLayout>
        <Button x:Name="button"
                Text="Button"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                TextColor="{Binding Source={x:Reference textColorPicker},
                                    Path=SelectedItem.Color}"
                BackgroundColor="{Binding Source={x:Reference backgroundColorPicker},
                                          Path=SelectedItem.Color}"
                BorderColor="{Binding Source={x:Reference borderColorPicker},
                                      Path=SelectedItem.Color}" />

        <StackLayout BindingContext="{x:Reference button}"
                     Padding="10">

            <Slider x:Name="fontSizeSlider"
                    Maximum="48"
                    Minimum="1"
                    Value="{Binding FontSize}" />

            <Label Text="{Binding Source={x:Reference fontSizeSlider},
                                  Path=Value,
                                  StringFormat='FontSize = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="borderWidthSlider"
                    Minimum="-1"
                    Maximum="12"
                    Value="{Binding BorderWidth}" />

            <Label Text="{Binding Source={x:Reference borderWidthSlider},
                                  Path=Value,
                                  StringFormat='BorderWidth = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="cornerRadiusSlider"
                    Minimum="-1"
                    Maximum="24"
                    Value="{Binding CornerRadius}" />

            <Label Text="{Binding Source={x:Reference cornerRadiusSlider},
                                  Path=Value,
                                  StringFormat='CornerRadius = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Grid.Resources>
                    <Style TargetType="Label">
                        <Setter Property="VerticalOptions" Value="Center" />
                    </Style>
                </Grid.Resources>

                <Label Text="Text Color:"
                       Grid.Row="0" Grid.Column="0" />

                <Picker x:Name="textColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="0" Grid.Column="1" />

                <Label Text="Background Color:"
                       Grid.Row="1" Grid.Column="0" />

                <Picker x:Name="backgroundColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="1" Grid.Column="1" />

                <Label Text="Border Color:"
                       Grid.Row="2" Grid.Column="0" />

                <Picker x:Name="borderColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="2" Grid.Column="1" />
            </Grid>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

Nella `Button` parte superiore della pagina sono presenti tre `Color` proprietà vincolate a `Picker` elementi nella parte inferiore della pagina. Gli elementi negli `Picker` elementi sono colori della `NamedColor` classe inclusa nel progetto. Tre `Slider` elementi contengono associazioni bidirezionali con le `FontSize` `BorderWidth` proprietà, e `CornerRadius` di `Button` .

Questo programma consente di sperimentare le combinazioni di tutte queste proprietà:

[![Aspetto del pulsante](button-images/ButtonAppearance.png "Aspetto del pulsante")](button-images/ButtonAppearance-Large.png)

Per visualizzare il `Button` bordo, è necessario impostare `BorderColor` su un valore diverso da `Default` e `BorderWidth` su un valore positivo.

In iOS si noterà che le larghezze dei bordi grandi si inseriscono nell'interno di `Button` e interferiscono con la visualizzazione del testo. Se si sceglie di usare un bordo con un iOS `Button` , è probabile che si voglia iniziare e terminare la `Text` proprietà con spazi per mantenerne la visibilità.

In UWP la selezione di un valore `CornerRadius` che supera la metà dell'altezza di `Button` genera un'eccezione.

## <a name="button-visual-states"></a>Stati di visualizzazione pulsante

[`Button`](xref:Xamarin.Forms.Button)dispone di un oggetto `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) che può essere utilizzato per avviare una modifica visiva a `Button` quando viene premuto dall'utente, a condizione che sia abilitato.

Nell'esempio di codice XAML riportato di seguito viene illustrato come definire uno stato di visualizzazione per lo `Pressed` stato:

```xaml
<Button Text="Click me!"
        ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Button>
```

`Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) Specifica che [`Button`](xref:Xamarin.Forms.Button) , quando viene premuto, la relativa [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) proprietà verrà modificata rispetto al valore predefinito da 1 a 0,8. `Normal` `VisualState` Specifica che quando `Button` è in uno stato normale, la relativa `Scale` proprietà sarà impostata su 1. Pertanto, l'effetto complessivo è che, quando `Button` viene premuto, viene ridimensionato in modo da essere leggermente più piccolo e, quando `Button` viene rilasciato, viene ridimensionato in base alle dimensioni predefinite.

Per ulteriori informazioni sugli stati visivi, vedere la pagina relativa a [ Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="creating-a-toggle-button"></a>Creazione di un interruttore

È possibile eseguire la sottoclasse in `Button` modo che funzioni come un'opzione on-off: toccare il pulsante una volta per attivare o disattivare il pulsante e toccarlo di nuovo per disattivarlo.

La `ToggleButton` classe seguente deriva da `Button` e definisce un nuovo evento denominato `Toggled` e una proprietà booleana denominata `IsToggled` . Queste sono le stesse due proprietà definite da Xamarin.Forms [`Switch`](xref:Xamarin.Forms.Switch) :

```csharp
class ToggleButton : Button
{
    public event EventHandler<ToggledEventArgs> Toggled;

    public static BindableProperty IsToggledProperty =
        BindableProperty.Create("IsToggled", typeof(bool), typeof(ToggleButton), false,
                                propertyChanged: OnIsToggledChanged);

    public ToggleButton()
    {
        Clicked += (sender, args) => IsToggled ^= true;
    }

    public bool IsToggled
    {
        set { SetValue(IsToggledProperty, value); }
        get { return (bool)GetValue(IsToggledProperty); }
    }

    protected override void OnParentSet()
    {
        base.OnParentSet();
        VisualStateManager.GoToState(this, "ToggledOff");
    }

    static void OnIsToggledChanged(BindableObject bindable, object oldValue, object newValue)
    {
        ToggleButton toggleButton = (ToggleButton)bindable;
        bool isToggled = (bool)newValue;

        // Fire event
        toggleButton.Toggled?.Invoke(toggleButton, new ToggledEventArgs(isToggled));

        // Set the visual state
        VisualStateManager.GoToState(toggleButton, isToggled ? "ToggledOn" : "ToggledOff");
    }
}
```

Il `ToggleButton` costruttore connette un gestore all'evento in `Clicked` modo che possa modificare il valore della `IsToggled` Proprietà. Il `OnIsToggledChanged` metodo genera l' `Toggled` evento.

L'ultima riga del `OnIsToggledChanged` metodo chiama il metodo statico `VisualStateManager.GoToState` con le due stringhe di testo "ToggledOn" e "ToggledOff". Per informazioni su questo metodo e sul modo in cui l'applicazione può rispondere agli stati visivi, vedere l'articolo [** Xamarin.Forms Gestione stato visuale**](~/xamarin-forms/user-interface/visual-state-manager.md).

Poiché `ToggleButton` effettua la chiamata a `VisualStateManager.GoToState` , non è necessario che la classe includa funzioni aggiuntive per modificare l'aspetto del pulsante in base al relativo `IsToggled` stato. Questo è il compito del codice XAML che ospita `ToggleButton` .

La pagina **demo del pulsante Attiva/Nascondi** contiene due istanze di `ToggleButton` , incluso il markup di Visual State Manager che imposta `Text` , `BackgroundColor` e `TextColor` del pulsante in base allo stato di visualizzazione:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ToggleButtonDemoPage"
             Title="Toggle Button Demo">

    <ContentPage.Resources>
        <Style TargetType="local:ToggleButton">
            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            <Setter Property="HorizontalOptions" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <local:ToggleButton Toggled="OnItalicButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Italic Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Italic On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <local:ToggleButton Toggled="OnBoldButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Bold Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Bold On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <Label x:Name="label"
               Text="Just a little passage of some sample text that can be formatted in italic or boldface by toggling the two buttons."
               FontSize="Large"
               HorizontalTextAlignment="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

I `Toggled` gestori eventi si trovano nel file code-behind. Sono responsabili dell'impostazione della `FontAttributes` proprietà di `Label` in base allo stato dei pulsanti:

```csharp
public partial class ToggleButtonDemoPage : ContentPage
{
    public ToggleButtonDemoPage ()
    {
        InitializeComponent ();
    }

    void OnItalicButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Italic;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Italic;
        }
    }

    void OnBoldButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Bold;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Bold;
        }
    }
}
```

Ecco il programma in esecuzione in iOS, Android e UWP:

[![Demo interruttore](button-images/ToggleButtonDemo.png "Demo interruttore")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>Uso di bitmap con pulsanti

La `Button` classe definisce una [`ImageSource`](xref:Xamarin.Forms.Button.Image) proprietà che consente di visualizzare un'immagine bitmap nell'oggetto `Button` , singolarmente o in combinazione con il testo. È inoltre possibile specificare la modalità di disposizione del testo e dell'immagine.

La `ImageSource` proprietà è di tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) , il che significa che le bitmap possono essere caricate da un file, una risorsa incorporata, un URI o un flusso.

> [!NOTE]
> Mentre un `Button` può caricare un GIF animato, verrà visualizzato solo il primo frame del GIF.

Ogni piattaforma supportata da Xamarin.Forms consente di archiviare le immagini in più dimensioni per risoluzioni pixel diverse dei diversi dispositivi su cui l'applicazione potrebbe essere eseguita. Queste più bitmap sono denominate o archiviate in modo tale che il sistema operativo possa scegliere la migliore corrispondenza per la risoluzione dello schermo video del dispositivo.

Per una bitmap in un `Button` , la dimensione migliore è in genere compresa tra 32 e 64 unità indipendenti dal dispositivo, a seconda della dimensione desiderata. Le immagini usate in questo esempio sono basate su una dimensione di 48 unità indipendenti dal dispositivo.

Nel progetto iOS la cartella **Resources** contiene tre dimensioni di questa immagine:

- Bitmap quadrata di 48 pixel archiviata come **/Resources/MonkeyFace.png**
- Bitmap quadrata di 96 pixel archiviata come**/Resource/MonkeyFace@2x.png**
- Bitmap quadrata di 144 pixel archiviata come**/Resource/MonkeyFace@3x.png**

A tutte e tre le bitmap è stata assegnata un' **azione di compilazione** di **BundleResource**.

Per il progetto Android, tutte le bitmap hanno lo stesso nome, ma vengono archiviate in sottocartelle diverse della cartella **Resources** :

- Bitmap quadrata di 72 pixel archiviata come **/Resources/drawable-HDPI/MonkeyFace.png**
- Bitmap quadrata di 96 pixel archiviata come **/Resources/drawable-xhdpi/MonkeyFace.png**
- Bitmap quadrata di 144 pixel archiviata come **/Resources/drawable-XXHDPI/MonkeyFace.png**
- Bitmap quadrata di 192 pixel archiviata come **/Resources/drawable-xxxhdpi/MonkeyFace.png**

A queste è stata assegnata un' **azione di compilazione** di **AndroidResource**.

Nel progetto UWP, le bitmap possono essere archiviate in qualsiasi punto del progetto, ma in genere sono archiviate in una cartella personalizzata o nella cartella **assets** esistente. Il progetto UWP contiene le bitmap seguenti:

- Bitmap quadrata di 48 pixel archiviata come **/assets/MonkeyFace.scale-100.png**
- Bitmap quadrata di 96 pixel archiviata come **/assets/MonkeyFace.scale-200.png**
- Bitmap quadrata di 192 pixel archiviata come **/assets/MonkeyFace.Scale-400.png**

A tutti è stata assegnata un' **azione di compilazione** del **contenuto**.

È possibile specificare il modo `Text` in cui le `ImageSource` proprietà e sono disposte in `Button` utilizzando la [`ContentLayout`](xref:Xamarin.Forms.Button.ContentLayout) proprietà di `Button` . Questa proprietà è di tipo [`ButtonContentLayout`](xref:Xamarin.Forms.Button.ButtonContentLayout) , che è una classe incorporata in `Button` . [Constructor] (xrif: Xamarin.Forms . Button. ButtonContentLayout .% 23ctor ( Xamarin.Forms . Button. ButtonContentLayout. ImagePosition, System. Double)) ha due argomenti:

- Membro dell' [`ImagePosition`](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) enumerazione: `Left` , `Top` , `Right` o `Bottom` che indica come viene visualizzata la bitmap rispetto al testo.
- `double`Valore per la spaziatura tra la bitmap e il testo.

Le impostazioni predefinite sono `Left` e 10 unità. Due proprietà di sola lettura di `ButtonContentLayout` denominate [`Position`](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) e [`Spacing`](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) forniscono i valori di tali proprietà.

Nel codice è possibile creare un oggetto `Button` e impostare la `ContentLayout` proprietà come segue:

```csharp
Button button = new Button
{
    Text = "button text",
    ImageSource = new FileImageSource
    {
        File = "image filename"
    },
    ContentLayout = new Button.ButtonContentLayout(Button.ButtonContentLayout.ImagePosition.Right, 20)
};
```

In XAML è necessario specificare solo il membro di enumerazione, la spaziatura o entrambi gli ordini separati da virgole:

```xaml
<Button Text="button text"
        ImageSource="image filename"
        ContentLayout="Right, 20" />
```

La pagina **demo del pulsante immagine** USA `OnPlatform` per specificare nomi file diversi per i file bitmap iOS, Android e UWP. Se si desidera utilizzare lo stesso nome file per ogni piattaforma ed evitare l'utilizzo di `OnPlatform` , sarà necessario archiviare le bitmap UWP nella directory radice del progetto.

Il primo `Button` nella pagina **demo del pulsante immagine** imposta la `Image` proprietà, ma non la `Text` proprietà:

```xaml
<Button>
    <Button.ImageSource>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="MonkeyFace.png" />
            <On Platform="UWP" Value="Assets/MonkeyFace.png" />
        </OnPlatform>
    </Button.ImageSource>
</Button>
```

Se le bitmap UWP vengono archiviate nella directory radice del progetto, questo markup può essere notevolmente semplificato:

```xaml
<Button ImageSource="MonkeyFace.png" />
```

Per evitare una grande quantità di markup ripetitivo nel file **ImageButtonDemo. XAML** , `Style` viene definito anche un implicito per impostare la `ImageSource` Proprietà. `Style`Viene applicato automaticamente a cinque altri `Button` elementi. Ecco il file XAML completo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.ImageButtonDemoPage">

    <FlexLayout Direction="Column"
                JustifyContent="SpaceEvenly"
                AlignItems="Center">

        <FlexLayout.Resources>
            <Style TargetType="Button">
                <Setter Property="ImageSource">
                    <OnPlatform x:TypeArguments="ImageSource">
                        <On Platform="iOS, Android" Value="MonkeyFace.png" />
                        <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                    </OnPlatform>
                </Setter>
            </Style>
        </FlexLayout.Resources>

        <Button>
            <Button.ImageSource>
                <OnPlatform x:TypeArguments="ImageSource">
                    <On Platform="iOS, Android" Value="MonkeyFace.png" />
                    <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                </OnPlatform>
            </Button.ImageSource>
        </Button>

        <Button Text="Default" />

        <Button Text="Left - 10"
                ContentLayout="Left, 10" />

        <Button Text="Top - 10"
                ContentLayout="Top, 10" />

        <Button Text="Right - 20"
                ContentLayout="Right, 20" />

        <Button Text="Bottom - 20"
                ContentLayout="Bottom, 20" />
    </FlexLayout>
</ContentPage>
```

I quattro `Button` elementi finali utilizzano la `ContentLayout` proprietà per specificare la posizione e la spaziatura del testo e della bitmap:

[![Demo sul pulsante immagine](button-images/ImageButtonDemo.png "Demo sul pulsante immagine")](button-images/ImageButtonDemo-Large.png#lightbox)

A questo punto sono stati illustrati i vari modi in cui è possibile gestire `Button` gli eventi e modificare l' `Button` aspetto.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio ButtonDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)
- [API Pulsante](xref:Xamarin.Forms.Button)
