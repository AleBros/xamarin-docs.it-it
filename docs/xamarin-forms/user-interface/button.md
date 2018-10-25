---
title: Pulsante di xamarin. Forms
description: Il pulsante risponde a un tocco o clic che indirizza un'applicazione per eseguire una determinata attività.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 07/26/2018
ms.openlocfilehash: ed711efe7f4b138b3ba61437dc96f8aa07d17aeb
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "35244916"
---
# <a name="xamarinforms-button"></a>Pulsante di xamarin. Forms

_Il pulsante risponde a un tocco o clic che indirizza un'applicazione per eseguire una determinata attività._

Il [ `Button` ](xref:Xamarin.Forms.Button) è il controllo interattivo più importante in tutti di xamarin. Forms. Il `Button` in genere consente di visualizzare una breve stringa di testo che indica un comando, ma è anche possibile visualizzare un'immagine bitmap, o una combinazione di testo e un'immagine. L'utente preme il `Button` con un dito o fa clic con il mouse per avviare tale comando.

La maggior parte degli argomenti illustrati di seguito corrispondono alle pagine di [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) esempio.

## <a name="handling-button-clicks"></a>Gestione pulsante fa clic su

`Button` definisce un [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) evento generato quando l'utente tocca il `Button` con un puntatore con un dito o passare il mouse. L'evento viene generato quando il pulsante con un dito o passare il mouse viene rilasciato dalla superficie del `Button`. Il `Button` deve avere relativi [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) impostata su `true` per poter rispondere alle scelte.

Il **fare clic sul pulsante di base** nella pagina il [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) esempio viene illustrato come creare un'istanza di un `Button` in XAML e handle relativo `Clicked` evento. Il **BasicButtonClickPage.xaml** file contiene una `StackLayout` entrambi una `Label` e un `Button`:

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

Il `Button` tende a occupare tutto lo spazio che è consentito. Ad esempio, se non si imposta la `HorizontalOptions` proprietà di `Button` su un valore diverso da `Fill`, il `Button` occupa l'intera larghezza del relativo padre.

Per impostazione predefinita, il `Button` rettangolare, ma è possibile assegnare gli angoli arrotondati it tramite il [ `CornerRadius` ](xref:Xamarin.Forms.Button.CornerRadius) proprietà, come descritto di seguito nella sezione [ **pulsante aspetto** ](#button-appearance).

Il [ `Text` ](xref:Xamarin.Forms.Button.Text) proprietà specifica il testo visualizzato nei `Button`. Il [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) è impostato per un gestore eventi denominato `OnButtonClicked`. Questo gestore si trova nel file code-behind **BasicButtonClickPage.xaml.cs**:

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

Quando la `Button` toccando, il `OnButtonClicked` esecuzione del metodo. Il `sender` argomento è il `Button` oggetto responsabile di questo evento. Ciò consente di accedere il `Button` oggetto, o per distinguere tra più `Button` gli oggetti che condividono lo stesso `Clicked` evento.

Questa particolare `Clicked` gestore chiama una funzione di animazione che ruota il `Label` 360 gradi in 1000 millisecondi. Ecco il programma in esecuzione su dispositivi iOS e Android e come applicazione Universal Windows Platform (UWP) sul desktop di Windows 10:

[![Fare clic sul pulsante di base](button-images/BasicButtonClick.png "fare clic sul pulsante di base")](button-images/BasicButtonClick-Large.png#lightbox "fare clic sul pulsante di base")

Si noti che il `OnButtonClicked` metodo include le `async` modificatore perché `await` viene usata all'interno del gestore eventi. Oggetto `Clicked` gestore eventi richiede la `async` modificatore solo se il corpo del gestore Usa `await`.

Esegue il rendering di ogni piattaforma di `Button` nel proprio modo specifico. Nel [ **pulsante aspetto** ](#button-appearance) sezione verrà illustrato come impostare colori e rendere il `Button` bordo visibile per gli aspetti più personalizzati. `Button` implementa il [ `IFontElement` ](xref:Xamarin.Forms.Internals.IFontElement) interfaccia, in modo da includere [ `FontFamily` ](xref:Xamarin.Forms.Button.FontFamily), [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize), e [ `FontAttributes` ](xref:Xamarin.Forms.Button.FontAttributes)delle proprietà.

## <a name="creating-a-button-in-code"></a>Creazione di un pulsante nel codice

È comune per creare un'istanza di un `Button` in XAML, ma è anche possibile creare un `Button` nel codice. Questo potrebbe essere utile quando l'applicazione deve creare più pulsanti basati sui dati che sono enumerabili con un `foreach` ciclo.

Il **fare clic sul pulsante di codice** pagina viene illustrato come creare una pagina che è funzionalmente equivalente al **base fare clic sul pulsante** ma interamente nella pagina C#:

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

Tutto ciò che avviene nel costruttore della classe. Poiché il `Clicked` gestore di è un'unica istruzione, può essere associato all'evento in poche parole:

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

Naturalmente, è possibile definire anche il gestore dell'evento come un metodo separato (esattamente come le `OnButtonClick` metodo nella **fare clic sul pulsante base**) e associare tale metodo per l'evento:

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Disattivazione del pulsante

In alcuni casi un'applicazione è in un determinato stato in cui un determinato `Button` clic non è un'operazione valida. In questi casi, il `Button` deve essere disabilitata impostando relativi `IsEnabled` proprietà `false`. L'esempio classico è un `Entry` controllo per un nome di file accompagnato da un file-Apri `Button`: le `Button` deve essere abilitata solo se è stato digitato del testo nella `Entry`.
È possibile usare una `DataTrigger` per questa attività, come illustrato nella [ **i trigger Data** ](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) articolo.

## <a name="using-the-command-interface"></a>Tramite l'interfaccia di comando

È possibile che un'applicazione a cui rispondere `Button` scelte senza gestione il `Clicked` evento. Il `Button` implementa un meccanismo di notifica alternativo denominato il _comando_ oppure _l'esecuzione di comandi_ interfaccia. Questo è costituito da due proprietà:

- [`Command`](xref:Xamarin.Forms.Button.Command) typu [ `ICommand` ](xref:System.Windows.Input.ICommand), un'interfaccia definita nel [ `System.Windows.Input` ](xref:System.Windows.Input) dello spazio dei nomi.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) vlastnosti typu [ `Object` ](xref:System.Object).

Questo approccio è particolarmente adatto in relazione al data binding e in particolare quando si implementa l'architettura Model-View-ViewModel (MVVM). Questi argomenti sono illustrati negli articoli [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md), [dal Data Binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), e [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

In un'applicazione MVVM, l'elemento ViewModel definisce le proprietà di tipo `ICommand` che vengono quindi connessi al XAML `Button` gli elementi con data binding. Xamarin. Forms definisce inoltre [ `Command` ]((xref:Xamarin.Forms.Command`1)) e [ `Command<T>` ](xref:Xamarin.Forms.Command`1) le classi che implementano il `ICommand` l'interfaccia e utili per l'elemento ViewModel che definisce le proprietà di tipo `ICommand`.

L'esecuzione di comandi è descritto più dettagliatamente nell'articolo [ **l'interfaccia di comando** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) ma la **base pulsante di comando** nella pagina di [  **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) esempio viene illustrato l'approccio di base.

Il `CommandDemoViewModel` classe è un elemento ViewModel molto semplice che definisce una proprietà di tipo `double` denominate `Number`e due proprietà di tipo `ICommand` denominato `MultiplyBy2Command` e `DivideBy2Command`:

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

I due `ICommand` vengono inizializzate nel costruttore della classe con due oggetti di tipo `Command`. Il `Command` costruttori includono una piccola funzione (chiamato il `execute` argomento del costruttore) che raddoppia o si dimezza il `Number` proprietà.

Il **BasicButtonCommand.xaml** set di file relativo `BindingContext` a un'istanza di `CommandDemoViewModel`. Il `Label` elemento e due `Button` elementi contengono le associazioni alle tre proprietà `CommandDemoViewModel`:

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

Come i due `Button` elementi toccati, vengono eseguiti i comandi, e il numero viene modificato valore:

[![Comando di base dei pulsanti](button-images/BasicButtonCommand.png "comandi di base dei pulsanti")](button-images/BasicButtonCommand-Large.png#lightbox)

Il vantaggio di questo approccio rispetto `Clicked` gestori è che tutta la logica che interessano la funzionalità di questa pagina si trova l'elemento ViewModel, anziché il file code-behind, ottenere una migliore separazione dell'interfaccia utente dalla logica di business.

È anche possibile che il `Command` gli oggetti per controllare l'abilitazione e disabilitazione del `Button` elementi. Ad esempio, si supponga che si desidera limitare l'intervallo dei valori numerici compresi tra 2<sup>10</sup> e 2<sup>&ndash;10</sup>. È possibile aggiungere un'altra funzione del costruttore (denominato il `canExecute` argomento) che restituisce `true` se il `Button` deve essere abilitata. Ecco la modifica per il `CommandDemoViewModel` costruttore:

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

Le chiamate al `ChangeCanExecute` metodo di `Command` sono necessarie in modo che le `Command` metodo può essere chiamato il `canExecute` (metodo) e determinare se il `Button` deve essere disabilitata o non. Con questa modifica del codice, come il numero raggiunge il limite, il `Button` è disabilitato:

[![Comando di base dei pulsanti - modificato](button-images/BasicButtonCommandModified.png "pulsante Basic Command - modificato")](button-images/BasicButtonCommandModified-Large.png#lightbox)

È possibile che due o più `Button` elementi da associare allo stesso `ICommand` proprietà. Il `Button` gli elementi possono essere distinti usando la [ `CommandParameter` ](xref:Xamarin.Forms.Button.CommandParameter) proprietà di `Button`. In questo caso, è opportuno usare il tipo generico [ `Command<T>` ](xref:Xamarin.Forms.Command`1) classe. Il `CommandParameter` oggetto viene quindi passato come argomento per il `execute` e `canExecute` metodi. Questa tecnica è illustrata in dettaglio nel [ **esecuzione dei comandi di base** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) sezione la [ **interfaccia di comando** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) articolo.

Il [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) esempio Usa inoltre questa tecnica nel relativo `MainPage` classe. Il **MainPage. XAML** file contiene un `Button` per ogni pagina dell'esempio:

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

Ogni `Button` ha relativi `Command` proprietà associata a una proprietà denominata `NavigateCommand`e il `CommandParameter` è impostata su un [ `Type` ](xref:System.Type) oggetto corrispondente a una delle classi nel progetto di pagina.

Che `NavigateCommand` proprietà è di tipo `ICommand` e viene definito nel file code-behind:

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

Il costruttore inizializza la `NavigateCommand` proprietà per un `Command<Type>` dell'oggetto poiché `Type` è il tipo del `CommandParameter` oggetto impostato nel file XAML. Ciò significa che il `execute` metodo ha un argomento di tipo `Type` che corrisponde a questo `CommandParameter` oggetto. La funzione crea un'istanza di pagina e quindi si passa a esso.

Si noti che il costruttore termina impostando relativo `BindingContext` a se stesso. Questa operazione è necessaria per le proprietà nel file XAML per associare il `NavigateCommand` proprietà.

## <a name="pressing-and-releasing-the-button"></a>Premere e rilasciare il pulsante

Altre origini oltre al `Clicked` evento `Button` definisce inoltre [ `Pressed` ](xref:Xamarin.Forms.Button.Pressed) e [ `Released` ](xref:Xamarin.Forms.Button.Released) gli eventi. Il `Pressed` evento si verifica quando si preme un dito in un `Button`, o si preme un pulsante del mouse con il puntatore posizionato in corrispondenza di `Button`. Il `Released` evento si verifica quando viene rilasciato il pulsante con un dito o passare il mouse. In genere, un `Clicked` evento viene generato allo stesso tempo come i `Released` eventi, ma se il puntatore del mouse o del dito diapositive dalla superficie della `Button` prima di essere rilasciate, il `Clicked` eventi potrebbero non venire eseguiti.

Il `Pressed` e `Released` gli eventi non vengono usati spesso, ma possono essere usate per scopi speciali, come illustrato nel **premere e rilasciare il pulsante** pagina. Il file XAML contiene una `Label` e una `Button` con gestori associati per il `Pressed` e `Released` eventi:

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

Il file code-behind anima la `Label` quando un `Pressed` evento si verifica, ma sospende la rotazione quando un `Released` evento si verifica:

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

Il risultato è che il `Label` rotazione solo quando un dito è a contatto con il `Button`e si interrompe quando viene rilasciato il dito:

[![Premere e rilasciare sul pulsante](button-images/PressAndReleaseButton.png "premere e rilasciare pulsante")](button-images/PressAndReleaseButton-Large.png)

Questo tipo di comportamento dispone di applicazioni per i giochi: un dito mantenuto su un `Button` potrebbe rendere un oggetto schermata su Sposta in una direzione particolare.

<a name="button-appearance" />

## <a name="button-appearance"></a>Aspetto del pulsante

Il `Button` eredita o definisce diverse proprietà che interessano l'aspetto del controllo:

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) rappresenta il colore del `Button` testo
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) rappresenta il colore dello sfondo al testo
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) rappresenta il colore di un'area che circonda il `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) la famiglia di caratteri viene utilizzata per il testo
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) è la dimensione del testo
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) indica se il testo è grassetto o corsivo
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) la larghezza del bordo
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) Consente di arrotondare gli angoli

> [!NOTE]
> Il `Button` classe dispone inoltre [ `Margin` ](xref:Xamarin.Forms.View.Margin) e [ `Padding` ](xref:Xamarin.Forms.Button.Padding) proprietà che controllano il comportamento di layout del `Button`. Per altre informazioni, vedere [margine e spaziatura interna](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

Gli effetti di sei di queste proprietà (escluso `FontFamily` e `FontAttributes`) vengono illustrati il **aspetto del pulsante** pagina. Un'altra proprietà, [ `Image` ](xref:Xamarin.Forms.Button.Image), è descritto nella sezione [ **utilizzando le bitmap con il pulsante**](#image-button).

Tutte le associazioni dati e viste nel **aspetto del pulsante** pagina definiti nel file XAML:

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

Il `Button` nella parte superiore della pagina ha tre `Color` le proprietà associate a `Picker` elementi nella parte inferiore della pagina. Gli elementi nel `Picker` gli elementi sono i colori dal `NamedColor` classe incluso nel progetto. Tre `Slider` elementi contengono le associazioni bidirezionali per i `FontSize`, `BorderWidth`, e `CornerRadius` le proprietà del `Button`.

Questo programma consente di sperimentare combinazioni di tutte queste proprietà:

[![Pulsante aspetto](button-images/ButtonAppearance.png "pulsante aspetto")](button-images/ButtonAppearance-Large.png)

Per vedere le `Button` bordo, è necessario impostare una `BorderColor` su un valore diverso da `Default`e il `BorderWidth` su un valore positivo.

In iOS, si noterà che ostacolano larghezze bordo di grandi dimensioni nell'area interna del `Button` e interferire con la visualizzazione del testo. Se si sceglie di usare un bordo con iOS `Button`, è opportuno iniziare e terminare il `Text` proprietà con spazi per mantenere la visibilità.

Nella piattaforma UWP, la selezione di un `CornerRadius` superiore a metà dell'altezza del `Button` genera un'eccezione.

## <a name="creating-a-toggle-button"></a>Creazione di un pulsante di attivazione/disattivazione

È possibile per creare una sottoclasse `Button` così da poterla utilizzare ad esempio un interruttore on / off: toccare il pulsante di una sola volta per attivare il pulsante e toccare di nuovo per essere disattivata.

Quanto segue `ToggleButton` deriva dalla classe `Button` e definisce un nuovo evento denominato `Toggled` e una proprietà booleana denominata `IsToggled`. Queste sono le stesse due proprietà definite da xamarin. Forms [ `Switch` ](xref:Xamarin.Forms.Switch):

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

Il `ToggleButton` costruttore collega un gestore per il `Clicked` eventi in modo che sia possibile modificare il valore della `IsToggled` proprietà. Il `OnIsToggledChanged` metodo generato il `Toggled` evento.

L'ultima riga del `OnIsToggledChanged` metodo chiama il metodo statico `VisualStateManager.GoToState` metodo con il testo di due stringhe "ToggledOn" e "ToggledOff". È possibile leggere su questo metodo e modo in cui l'applicazione può rispondere a stati di visualizzazione nell'articolo [ **The xamarin. Forms Visual State Manager**](~/xamarin-forms/user-interface/visual-state-manager.md).

In quanto `ToggleButton` effettua la chiamata al `VisualStateManager.GoToState`, senza dover includere eventuali funzionalità aggiuntive per modificare l'aspetto del pulsante in base alla classe stessa relativo `IsToggled` dello stato. Vale a dire la responsabilità di XAML che ospita il `ToggleButton`.

Il **Demo pulsante Attiva/disattiva** pagina contiene due istanze di `ToggleButton`, incluso il markup Visual State Manager che consente di impostare il `Text`, `BackgroundColor`, e `TextColor` del pulsante di base dello stato di visualizzazione:

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

Il `Toggled` gestori di eventi sono nel file code-behind. Si occupano di impostazione il `FontAttributes` proprietà del `Label` basata sullo stato dei pulsanti:

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

[![Attiva/Disattiva pulsante Demo](button-images/ToggleButtonDemo.png "Attiva/Disattiva pulsante Demo")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>Utilizzo di immagini bitmap con i pulsanti

Il `Button` classe definisce un [ `Image` ](xref:Xamarin.Forms.Button.Image) proprietà che consente di visualizzare un'immagine bitmap nel `Button`, singolarmente o in combinazione con il testo. È anche possibile specificare la modalità di disposizione testo e dell'immagine.

Il `Image` proprietà è di tipo [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource), il che significa che le bitmap devono essere archiviate come risorse nei singoli progetti di piattaforma e non nel progetto della libreria .NET Standard.

Ogni piattaforma supportata da xamarin. Forms permette di essere archiviati in più dimensioni per le risoluzioni diverse in pixel dei vari dispositivi che l'applicazione può essere eseguita su immagini. Queste bitmap più sono denominate o archiviate in modo che il sistema operativo può scegliere quello più adatto per il dispositivo il video di risoluzione dello schermo.

Per una bitmap in un `Button`, la dimensione ottimale è in genere compreso tra 32 e 64 device independent unit, a seconda delle dimensioni si desidera che questo sia. Le immagini usate in questo esempio si basano su una dimensione pari a 48 unità indipendenti dal dispositivo.

Nel progetto iOS, il **risorse** cartella contiene tre dimensioni di questa immagine:

- 48 pixel quadrate bitmap archiviata come **/Resources/MonkeyFace.png**
- 96 pixel quadrate bitmap archiviata come **/Resource/MonkeyFace@2x.png**
- 144 pixel quadrate bitmap archiviata come **/Resource/MonkeyFace@3x.png**

Tutte e tre immagini bitmap hanno avuta un **Build Action** dei **BundleResource**.

Per il progetto Android, tutte le mappe di bit hanno lo stesso nome, ma sono archiviati nelle sottocartelle diverse della **risorse** cartella:

- 72 pixel quadrate bitmap archiviata come **/Resources/drawable-hdpi/MonkeyFace.png**
- 96 pixel quadrate bitmap archiviata come **/Resources/drawable-xhdpi/MonkeyFace.png**
- 144 pixel quadrate bitmap archiviata come **/Resources/drawable-xxhdpi/MonkeyFace.png**
- 192 pixel quadrate bitmap archiviata come **/Resources/drawable-xxxhdpi/MonkeyFace.png**

Queste hanno avute un **Build Action** dei **AndroidResource**.

Nel progetto UWP, bitmap possono essere archiviate in un punto qualsiasi nel progetto, ma sono in genere archiviati in una cartella personalizzata o il **asset** cartella esistente. Il progetto UWP contiene tali bitmap:

- 48 pixel quadrate bitmap archiviata come **/Assets/MonkeyFace.scale-100.png**
- 96 pixel quadrate bitmap archiviata come **/Assets/MonkeyFace.scale-200.png**
- 192 pixel quadrate bitmap archiviata come **/Assets/MonkeyFace.scale-400.png**

Essi tutti assegnati un **Build Action** dei **contenuto**.

È possibile specificare come il `Text` e `Image` le proprietà sono disposte nel `Button` utilizzando la [ `ContentLayout` ](xref:Xamarin.Forms.Button.ContentLayout) proprietà di `Button`. Questa proprietà è di tipo [ `ButtonContentLayout` ](xref:Xamarin.Forms.Button.ButtonContentLayout), che è una classe incorporata in `Button`. Il [costruttore](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double)) dispone di due argomenti:

- Un membro del [ `ImagePosition` ](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) enumerazione: `Left`, `Top`, `Right`, o `Bottom` che indica l'aspetto della bitmap rispetto al testo.
- Oggetto `double` valore per la spaziatura tra il testo e bitmap.

I valori predefiniti sono `Left` e 10 unità. Due proprietà di sola lettura della `ButtonContentLayout` denominate [ `Position` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) e [ `Spacing` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) fornire i valori di tali proprietà.

Nel codice, è possibile creare un `Button` e impostare il `ContentLayout` proprietà simile al seguente:

```csharp
Button button = new Button
{
    Text = "button text",
    Image = new FileImageSource
    {
        File = "image filename"
    },
    ContentLayout = new Button.ButtonContentLayout(Button.ButtonContentLayout.ImagePosition.Right, 20)
};
```

In XAML, è necessario specificare solo il membro di enumerazione o la spaziatura o entrambe in qualsiasi ordine separandoli con virgole:

```xaml
<Button Text="button text"
        Image="image filename"
        ContentLayout="Right, 20" />
```

Il **immagine del pulsante Demo** pagina Usa `OnPlatform` per specificare i nomi di file diversi per iOS, Android e UWP file bitmap. Se si desidera usare lo stesso nome file per tutti e tre le piattaforme ed evitare l'uso di `OnPlatform`, è necessario archiviare le bitmap UWP nella directory radice del progetto.

Il primo `Button` nella **Demo pulsante immagine** pagina set il `Image` proprietà ma non il `Text` proprietà:

```xaml
<Button>
    <Button.Image>
        <OnPlatform x:TypeArguments="FileImageSource">
            <On Platform="iOS, Android" Value="MonkeyFace.png" />
            <On Platform="UWP" Value="Assets/MonkeyFace.png" />
        </OnPlatform>
    </Button.Image>
</Button>
```

Se le bitmap UWP vengono archiviate nella directory radice del progetto, questo markup può essere notevolmente semplificato:

```xaml
<Button Image="MonkeyFace.png" />
```

Per evitare numerose markup ripetitive nel **ImageButtonDemo.xaml** file implicita `Style` è definito anche per impostare il `Image` proprietà. Ciò `Style` viene applicata automaticamente a cinque altri `Button` elementi. Ecco il file XAML completo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.ImageButtonDemoPage">

    <FlexLayout Direction="Column"
                JustifyContent="SpaceEvenly"
                AlignItems="Center">

        <FlexLayout.Resources>
            <Style TargetType="Button">
                <Setter Property="Image">
                    <OnPlatform x:TypeArguments="FileImageSource">
                        <On Platform="iOS, Android" Value="MonkeyFace.png" />
                        <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                    </OnPlatform>
                </Setter>
            </Style>
        </FlexLayout.Resources>

        <Button>
            <Button.Image>
                <OnPlatform x:TypeArguments="FileImageSource">
                    <On Platform="iOS, Android" Value="MonkeyFace.png" />
                    <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                </OnPlatform>
            </Button.Image>
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

I quattro finali `Button` gli elementi di utilizzare il `ContentLayout` proprietà per specificare una posizione e la spaziatura del testo e bitmap:

[![Demo di pulsante di immagine](button-images/ImageButtonDemo.png "Demo di pulsante di immagine")](button-images/ImageButtonDemo-Large.png#lightbox)

Abbiamo visto i vari modi in cui è possibile gestire `Button` eventi e modifiche di `Button` aspetto.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio ButtonDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)
- [Pulsante API](xref:Xamarin.Forms.Button)
