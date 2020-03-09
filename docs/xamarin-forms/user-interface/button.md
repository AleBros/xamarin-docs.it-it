---
title: Pulsante di xamarin. Forms
description: Il pulsante risponde a un tocco o clic che indirizza un'applicazione per eseguire una determinata attività.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: f82d590213076f349b21ebdee2832f2bf474d2f2
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912051"
---
# <a name="xamarinforms-button"></a>Pulsante di xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)

_Il pulsante risponde a un tocco o a un clic che indirizza un'applicazione per eseguire un'attività specifica._

Il [`Button`](xref:Xamarin.Forms.Button) è il controllo interattivo più fondamentale in tutti i Novell. Forms. Il `Button` in genere Visualizza una stringa di testo breve che indica un comando, ma può anche visualizzare un'immagine bitmap o una combinazione di testo e immagine. L'utente preme il `Button` con un dito o fa clic su di esso con il mouse per avviare il comando.

La maggior parte degli argomenti illustrati di seguito corrisponde alle pagine nell'esempio [**ButtonDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) .

## <a name="handling-button-clicks"></a>Gestione pulsante fa clic su

`Button` definisce un evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) che viene generato quando l'utente tocca il `Button` con un dito o un puntatore del mouse. L'evento viene generato quando il pulsante del dito o del mouse viene rilasciato dalla superficie del `Button`. Il `Button` deve avere la proprietà [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) impostata su `true` affinché risponda ai tocchi.

Nella pagina di **clic del pulsante di base** nell'esempio [**ButtonDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) viene illustrato come creare un'istanza di un `Button` in XAML e gestirne l'evento `Clicked`. Il file **BasicButtonClickPage. XAML** contiene un `StackLayout` con `Label` e un `Button`:

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

Il `Button` tende a occupare tutto lo spazio consentito. Se, ad esempio, non si imposta la proprietà `HorizontalOptions` di `Button` su un valore diverso da `Fill`, il `Button` occuperà l'intera larghezza dell'elemento padre.

Per impostazione predefinita, il `Button` è rettangolare, ma è possibile assegnargli angoli arrotondati usando la proprietà [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) , come descritto di seguito nell' [**aspetto del pulsante**](#button-appearance)della sezione.

La proprietà [`Text`](xref:Xamarin.Forms.Button.Text) specifica il testo visualizzato nel `Button`. L'evento [`Clicked`](xref:Xamarin.Forms.Button.Clicked) viene impostato su un gestore eventi denominato `OnButtonClicked`. Questo gestore si trova nel file code-behind, **BasicButtonClickPage.XAML.cs**:

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

Quando viene toccato il `Button`, viene eseguito il metodo `OnButtonClicked`. L'argomento `sender` è l'oggetto `Button` responsabile di questo evento. È possibile usarlo per accedere all'oggetto `Button` o per distinguere tra più oggetti `Button` che condividono lo stesso evento di `Clicked`.

Questo particolare gestore di `Clicked` chiama una funzione di animazione che ruota i `Label` 360 gradi in 1000 millisecondi. Ecco il programma in esecuzione su dispositivi iOS e Android e come applicazione Universal Windows Platform (UWP) sul desktop di Windows 10:

[![Clic sul pulsante di base](button-images/BasicButtonClick.png "Clic sul pulsante di base")](button-images/BasicButtonClick-Large.png#lightbox "Clic sul pulsante di base")

Si noti che il metodo `OnButtonClicked` include il modificatore di `async` perché `await` viene usato all'interno del gestore eventi. Un gestore dell'evento `Clicked` richiede il modificatore di `async` solo se il corpo del gestore USA `await`.

Ogni piattaforma esegue il rendering del `Button` in modo specifico. Nella sezione relativa all' [**aspetto del pulsante**](#button-appearance) si vedrà come impostare i colori e rendere visibile il bordo del `Button` per ottenere un aspetto più personalizzato. `Button` implementa l'interfaccia [`IFontElement`](xref:Xamarin.Forms.Internals.IFontElement) , in modo che includa le proprietà [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily), [`FontSize`](xref:Xamarin.Forms.Button.FontSize)e [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) .

## <a name="creating-a-button-in-code"></a>Creazione di un pulsante nel codice

È comune creare un'istanza di un `Button` in XAML, ma è anche possibile creare una `Button` nel codice. Questa operazione può risultare utile quando l'applicazione deve creare più pulsanti basati su dati enumerabili con un ciclo di `foreach`.

Nella pagina di **clic del pulsante codice** viene illustrato come creare una pagina equivalente dal punto di vista funzionale alla pagina di clic del pulsante C#di **base** , ma interamente in:

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

Tutto ciò che avviene nel costruttore della classe. Poiché il gestore `Clicked` è una sola istruzione Long, può essere collegato all'evento in modo molto semplice:

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

Naturalmente, è anche possibile definire il gestore dell'evento come metodo separato (proprio come il metodo `OnButtonClick` in **clic sul pulsante di base**) e alleghiare tale metodo all'evento:

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Disattivazione del pulsante

A volte un'applicazione si trova in uno stato specifico in cui un particolare `Button` clic non è un'operazione valida. In questi casi, è necessario disabilitare il `Button` impostando la relativa proprietà `IsEnabled` su `false`. L'esempio classico è un controllo `Entry` per un nome file accompagnato da un `Button`di apertura di file: il `Button` deve essere abilitato solo se è stato digitato un testo nel `Entry`.
È possibile usare un `DataTrigger` per questa attività, come illustrato nell'articolo [**trigger di dati**](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) .

## <a name="using-the-command-interface"></a>Tramite l'interfaccia di comando

È possibile che un'applicazione risponda ai rubinetti `Button` senza gestire l'evento di `Clicked`. Il `Button` implementa un meccanismo di notifica alternativo denominato _comando_ o interfaccia di _comando_ . Questo è costituito da due proprietà:

- [`Command`](xref:Xamarin.Forms.Button.Command) di tipo [`ICommand`](xref:System.Windows.Input.ICommand), un'interfaccia definita nello spazio dei nomi [`System.Windows.Input`](xref:System.Windows.Input) .
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) proprietà di tipo [`Object`](xref:System.Object).

Questo approccio è particolarmente adatto in relazione al data binding e in particolare quando si implementa l'architettura Model-View-ViewModel (MVVM). Questi argomenti sono illustrati nel [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)degli articoli, [da data binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)e [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

In un'applicazione MVVM, il ViewModel definisce proprietà di tipo `ICommand` che vengono quindi connesse agli elementi `Button` XAML con le associazioni dati. Novell. Forms definisce anche [`Command`](xref:Xamarin.Forms.Command) e [`Command<T>`](xref:Xamarin.Forms.Command`1) classi che implementano l'interfaccia `ICommand` e assistono il ViewModel nella definizione di proprietà di tipo `ICommand`.

Il comando viene descritto più dettagliatamente nell'articolo [**l'interfaccia del comando**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) , ma la pagina di **comando del pulsante Basic** nell'esempio [**ButtonDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) illustra l'approccio di base.

La classe `CommandDemoViewModel` è un ViewModel molto semplice che definisce una proprietà di tipo `double` `Number`denominato e due proprietà di tipo `ICommand` denominate `MultiplyBy2Command` e `DivideBy2Command`:

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

Le due proprietà `ICommand` vengono inizializzate nel costruttore della classe con due oggetti di tipo `Command`. I costruttori di `Command` includono una piccola funzione (denominata argomento del costruttore `execute`) che raddoppia o dimezza la proprietà `Number`.

Il file **BasicButtonCommand. XAML** imposta la `BindingContext` su un'istanza di `CommandDemoViewModel`. L'elemento `Label` e due elementi `Button` contengono associazioni alle tre proprietà in `CommandDemoViewModel`:

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

Quando vengono toccati i due `Button` elementi, i comandi vengono eseguiti e il numero modifica il valore:

[![Comando pulsante di base](button-images/BasicButtonCommand.png "Comando pulsante di base")](button-images/BasicButtonCommand-Large.png#lightbox)

Il vantaggio di questo approccio rispetto ai gestori di `Clicked` è che tutta la logica che coinvolge la funzionalità di questa pagina si trova nel ViewModel anziché nel file code-behind, ottenendo una migliore separazione dell'interfaccia utente dalla logica di business.

È anche possibile che gli oggetti `Command` controllino l'abilitazione e la disabilitazione degli elementi di `Button`. Si supponga, ad esempio, di voler limitare l'intervallo di valori numerici compreso tra 2<sup>10</sup> e 2<sup>&ndash;10</sup>. È possibile aggiungere un'altra funzione al costruttore (denominato argomento `canExecute`) che restituisce `true` se è necessario abilitare il `Button`. Di seguito viene illustrata la modifica apportata al costruttore `CommandDemoViewModel`:

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

Le chiamate al metodo `ChangeCanExecute` di `Command` sono necessarie in modo che il metodo `Command` possa chiamare il metodo `canExecute` e determinare se il `Button` deve essere disabilitato o meno. Con questa modifica del codice, poiché il numero raggiunge il limite, il `Button` è disabilitato:

[![Comando pulsante di base-modificato](button-images/BasicButtonCommandModified.png "Comando pulsante di base-modificato")](button-images/BasicButtonCommandModified-Large.png#lightbox)

È possibile associare due o più elementi `Button` alla stessa proprietà `ICommand`. Gli elementi `Button` possono essere distinti usando la proprietà [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) di `Button`. In questo caso, è consigliabile usare la classe [`Command<T>`](xref:Xamarin.Forms.Command`1) generica. L'oggetto `CommandParameter` viene quindi passato come argomento ai metodi `execute` e `canExecute`. Questa tecnica è illustrata in dettaglio nella sezione di [**comando di base**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) dell'articolo sull' [**interfaccia di comando**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) .

L'esempio [**ButtonDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos) usa anche questa tecnica nella relativa classe `MainPage`. Il file **MainPage. XAML** contiene un `Button` per ogni pagina dell'esempio:

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

Ogni `Button` dispone di una proprietà `Command` associata a una proprietà denominata `NavigateCommand`e la `CommandParameter` è impostata su un oggetto [`Type`](xref:System.Type) corrispondente a una delle classi di pagina nel progetto.

Questo `NavigateCommand` proprietà è di tipo `ICommand` ed è definito nel file code-behind:

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

Il costruttore inizializza la proprietà `NavigateCommand` a un oggetto `Command<Type>` perché `Type` è il tipo del set di oggetti `CommandParameter` nel file XAML. Questo significa che il metodo `execute` dispone di un argomento di tipo `Type` corrispondente a questo oggetto `CommandParameter`. La funzione crea un'istanza di pagina e quindi si passa a esso.

Si noti che il costruttore termina impostando il `BindingContext` su se stesso. Questa operazione è necessaria per l'associazione delle proprietà nel file XAML alla proprietà `NavigateCommand`.

## <a name="pressing-and-releasing-the-button"></a>Premere e rilasciare il pulsante

Oltre all'evento `Clicked`, `Button` definisce anche gli eventi [`Pressed`](xref:Xamarin.Forms.Button.Pressed) e [`Released`](xref:Xamarin.Forms.Button.Released). L'evento `Pressed` si verifica quando si preme un dito su un `Button`oppure viene premuto un pulsante del mouse con il puntatore posizionato sul `Button`. L'evento `Released` si verifica quando viene rilasciato il dito o il pulsante del mouse. In genere, un evento `Clicked` viene generato contemporaneamente all'evento `Released`, ma se il puntatore del dito o del mouse fuoriesce dalla superficie del `Button` prima del rilascio, l'evento `Clicked` potrebbe non verificarsi.

Gli eventi `Pressed` e `Released` non vengono spesso usati, ma possono essere usati per scopi speciali, come illustrato nella pagina **pulsante di stampa e rilascio** . Il file XAML contiene una `Label` e una `Button` con gestori collegati per gli eventi di `Pressed` e `Released`:

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

Il file code-behind anima l'`Label` quando si verifica un evento di `Pressed`, ma sospende la rotazione quando si verifica un evento `Released`:

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

Il risultato è che il `Label` ruota solo quando un dito è in contatto con l'`Button`e si interrompe quando viene rilasciato il dito:

[![Pulsante premi e rilascia](button-images/PressAndReleaseButton.png "Pulsante premi e rilascia")](button-images/PressAndReleaseButton-Large.png)

Questo tipo di comportamento ha applicazioni per i giochi: un dito in una `Button` potrebbe far spostare un oggetto sullo schermo in una direzione specifica.

<a name="button-appearance" />

## <a name="button-appearance"></a>Aspetto del pulsante

Il `Button` eredita o definisce diverse proprietà che influiscono sull'aspetto:

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) è il colore del testo `Button`
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) è il colore dello sfondo del testo
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) è il colore di un'area che circonda l'`Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) è la famiglia di caratteri utilizzata per il testo
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) corrisponde alla dimensione del testo
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) indica se il testo è in corsivo o in grassetto.
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) è la larghezza del bordo
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) è il raggio dell'angolo della `Button`
- `CharacterSpacing` è la spaziatura tra i caratteri del testo `Button`

> [!NOTE]
> La classe `Button` dispone inoltre di proprietà [`Margin`](xref:Xamarin.Forms.View.Margin) e [`Padding`](xref:Xamarin.Forms.Button.Padding) che controllano il comportamento del layout del `Button`. Per altre informazioni, vedere l'articolo sulle proprietà [Margin e Padding](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

Gli effetti di sei di queste proprietà (esclusi `FontFamily` e `FontAttributes`) sono illustrati nella pagina relativa all' **aspetto del pulsante** . Un'altra proprietà, [`Image`](xref:Xamarin.Forms.Button.ImageSource), viene illustrata nella sezione [**utilizzo di bitmap con Button**](#image-button).

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

Il `Button` nella parte superiore della pagina dispone delle tre proprietà `Color` associata agli elementi `Picker` nella parte inferiore della pagina. Gli elementi negli elementi `Picker` sono colori della classe `NamedColor` inclusa nel progetto. Tre elementi `Slider` contengono associazioni bidirezionali per le proprietà `FontSize`, `BorderWidth`e `CornerRadius` del `Button`.

Questo programma consente di sperimentare combinazioni di tutte queste proprietà:

[![Aspetto del pulsante](button-images/ButtonAppearance.png "Aspetto del pulsante")](button-images/ButtonAppearance-Large.png)

Per visualizzare il bordo del `Button`, è necessario impostare un `BorderColor` su un valore diverso da `Default`e il `BorderWidth` a un valore positivo.

In iOS si noterà che le larghezze dei bordi grandi si inseriscono nell'interno del `Button` e interferiscono con la visualizzazione del testo. Se si sceglie di usare un bordo con un `Button`iOS, è probabile che si voglia iniziare e terminare la proprietà `Text` con spazi per mantenerne la visibilità.

In UWP la selezione di un `CornerRadius` che supera la metà dell'altezza del `Button` genera un'eccezione.

## <a name="button-visual-states"></a>Stati visivi pulsante

[`Button`](xref:Xamarin.Forms.Button) dispone di un [`VisualState`](xref:Xamarin.Forms.VisualState) `Pressed` che può essere utilizzato per avviare una modifica visiva del `Button` quando viene premuto dall'utente, a condizione che sia abilitato.

Nell'esempio di codice XAML riportato di seguito viene illustrato come definire uno stato di visualizzazione per lo stato `Pressed`:

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

Il `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) specifica che, quando viene premuto il [`Button`](xref:Xamarin.Forms.Button) , la relativa proprietà [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) verrà modificata dal valore predefinito da 1 a 0,8. Il `Normal` `VisualState` specifica che quando il `Button` è in uno stato normale, la relativa proprietà `Scale` verrà impostata su 1. Pertanto, l'effetto complessivo è che, quando viene premuto il `Button`, viene ridimensionato in modo da essere leggermente più piccolo e, quando il `Button` viene rilasciato, viene ridimensionato in base alle dimensioni predefinite.

Per ulteriori informazioni sugli stati visivi, vedere [Novell. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="creating-a-toggle-button"></a>Creazione di un pulsante di attivazione/disattivazione

È possibile sottoporre a sottoclasse `Button` in modo che funzioni come un commutatore on-off: toccare il pulsante una volta per attivare o disattivare il pulsante e toccarlo di nuovo per disattivarlo.

La classe `ToggleButton` seguente deriva da `Button` e definisce un nuovo evento denominato `Toggled` e una proprietà booleana denominata `IsToggled`. Queste sono le stesse due proprietà definite dal [`Switch`](xref:Xamarin.Forms.Switch)Novell. Forms:

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

Il costruttore `ToggleButton` connette un gestore all'evento `Clicked` in modo che possa modificare il valore della proprietà `IsToggled`. Il metodo `OnIsToggledChanged` genera l'evento `Toggled`.

L'ultima riga del metodo `OnIsToggledChanged` chiama il metodo statico `VisualStateManager.GoToState` con le due stringhe di testo "ToggledOn" e "ToggledOff". È possibile leggere informazioni su questo metodo e sul modo in cui l'applicazione può rispondere agli stati visivi nell'articolo [**Novell. Forms Visual State Manager**](~/xamarin-forms/user-interface/visual-state-manager.md).

Poiché `ToggleButton` effettua la chiamata a `VisualStateManager.GoToState`, non è necessario che la classe includa alcuna funzionalità aggiuntiva per modificare l'aspetto del pulsante in base al relativo stato `IsToggled`. Questo è il compito del codice XAML che ospita il `ToggleButton`.

La pagina **demo del pulsante Attiva/Nascondi** contiene due istanze di `ToggleButton`, tra cui il markup di Visual State Manager che imposta `Text`, `BackgroundColor`e `TextColor` del pulsante in base allo stato di visualizzazione:

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

I gestori eventi `Toggled` si trovano nel file code-behind. Sono responsabili dell'impostazione della proprietà `FontAttributes` della `Label` in base allo stato dei pulsanti:

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

## <a name="using-bitmaps-with-buttons"></a>Utilizzo di immagini bitmap con i pulsanti

La classe `Button` definisce una proprietà [`ImageSource`](xref:Xamarin.Forms.Button.Image) che consente di visualizzare un'immagine bitmap sul `Button`, singolarmente o in combinazione con il testo. È anche possibile specificare la modalità di disposizione testo e dell'immagine.

La proprietà `ImageSource` è di tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource), il che significa che le bitmap possono essere caricate da un file, una risorsa incorporata, un URI o un flusso.

> [!NOTE]
> Mentre un `Button` può caricare un GIF animato, verrà visualizzato solo il primo frame del GIF.

Ogni piattaforma supportata da xamarin. Forms permette di essere archiviati in più dimensioni per le risoluzioni diverse in pixel dei vari dispositivi che l'applicazione può essere eseguita su immagini. Queste bitmap più sono denominate o archiviate in modo che il sistema operativo può scegliere quello più adatto per il dispositivo il video di risoluzione dello schermo.

Per una bitmap in un `Button`, la dimensione migliore è in genere compresa tra 32 e 64 unità indipendenti dal dispositivo, a seconda della dimensione desiderata. Le immagini usate in questo esempio si basano su una dimensione pari a 48 unità indipendenti dal dispositivo.

Nel progetto iOS la cartella **Resources** contiene tre dimensioni di questa immagine:

- Bitmap quadrata di 48 pixel archiviata come **/Resources/MonkeyFace.png**
- Bitmap quadrata di 96 pixel archiviata come **/Resource/MonkeyFace@2x.png**
- Bitmap quadrata di 144 pixel archiviata come **/Resource/MonkeyFace@3x.png**

A tutte e tre le bitmap è stata assegnata un' **azione di compilazione** di **BundleResource**.

Per il progetto Android, tutte le bitmap hanno lo stesso nome, ma vengono archiviate in sottocartelle diverse della cartella **Resources** :

- Bitmap quadrata di 72 pixel archiviata come **/Resources/drawable-HDPI/MonkeyFace.png**
- Bitmap quadrata di 96 pixel archiviata come **/Resources/drawable-xhdpi/MonkeyFace.png**
- Bitmap quadrata di 144 pixel archiviata come **/Resources/drawable-XXHDPI/MonkeyFace.png**
- Bitmap quadrata di 192 pixel archiviata come **/Resources/drawable-xxxhdpi/MonkeyFace.png**

A queste è stata assegnata un' **azione di compilazione** di **AndroidResource**.

Nel progetto UWP, le bitmap possono essere archiviate in qualsiasi punto del progetto, ma in genere sono archiviate in una cartella personalizzata o nella cartella **assets** esistente. Il progetto UWP contiene tali bitmap:

- Bitmap quadrata di 48 pixel archiviata come **/assets/MonkeyFace.scale-100.png**
- Bitmap quadrata di 96 pixel archiviata come **/assets/MonkeyFace.scale-200.png**
- Bitmap quadrata di 192 pixel archiviata come **/assets/MonkeyFace.Scale-400.png**

A tutti è stata assegnata un' **azione di compilazione** del **contenuto**.

È possibile specificare il modo in cui le proprietà `Text` e `ImageSource` sono disposte nella `Button` utilizzando la proprietà [`ContentLayout`](xref:Xamarin.Forms.Button.ContentLayout) di `Button`. Questa proprietà è di tipo [`ButtonContentLayout`](xref:Xamarin.Forms.Button.ButtonContentLayout), ovvero una classe incorporata in `Button`. Il [Costruttore](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double)) dispone di due argomenti:

- Membro dell'enumerazione [`ImagePosition`](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) : `Left`, `Top`, `Right`o `Bottom` che indica come viene visualizzata la bitmap rispetto al testo.
- Valore `double` per la spaziatura tra la bitmap e il testo.

Le impostazioni predefinite sono `Left` e 10 unità. Due proprietà di sola lettura di `ButtonContentLayout` denominate [`Position`](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) e [`Spacing`](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) fornire i valori di tali proprietà.

Nel codice è possibile creare un `Button` e impostare la proprietà `ContentLayout` come segue:

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

In XAML, è necessario specificare solo il membro di enumerazione o la spaziatura o entrambe in qualsiasi ordine separandoli con virgole:

```xaml
<Button Text="button text"
        ImageSource="image filename"
        ContentLayout="Right, 20" />
```

La pagina **demo del pulsante immagine** USA `OnPlatform` per specificare nomi file diversi per i file bitmap iOS, Android e UWP. Se si vuole usare lo stesso nome file per ogni piattaforma ed evitare l'uso di `OnPlatform`, è necessario archiviare le bitmap UWP nella directory radice del progetto.

Il primo `Button` della pagina **demo del pulsante immagine** imposta la proprietà `Image` ma non la proprietà `Text`:

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

Per evitare una grande quantità di markup ripetitivo nel file **ImageButtonDemo. XAML** , viene definito anche un `Style` implicito per impostare la proprietà `ImageSource`. Questo `Style` viene applicato automaticamente a cinque altri elementi di `Button`. Ecco il file XAML completo:

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

Gli ultimi quattro elementi `Button` utilizzano la proprietà `ContentLayout` per specificare una posizione e una spaziatura del testo e della bitmap:

[![Demo sul pulsante immagine](button-images/ImageButtonDemo.png "Demo sul pulsante immagine")](button-images/ImageButtonDemo-Large.png#lightbox)

A questo punto sono stati illustrati i vari modi in cui è possibile gestire gli eventi `Button` e modificare l'aspetto del `Button`.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio ButtonDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)
- [API Pulsante](xref:Xamarin.Forms.Button)
