---
title: Pulsante di xamarin. Forms
description: Il pulsante risponde a un toccare o fare clic su un'applicazione per svolgere un'attività particolare.
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 06/01/2018
ms.openlocfilehash: 095736e77b2f502261f9b85ab73c45dce74309b9
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34734153"
---
# <a name="xamarinforms-button"></a>Pulsante di xamarin. Forms

_Il pulsante risponde a un toccare o fare clic su un'applicazione per svolgere un'attività particolare._ 

Il [ `Button` ](xref:Xamarin.Forms.Button) è il controllo interattivo più importante in tutti di xamarin. Forms. Il `Button` in genere consente di visualizzare una breve stringa di testo che indica un comando, ma è anche possibile visualizzare un'immagine bitmap, o una combinazione di testo e un'immagine. L'utente preme il `Button` con un dito o fa clic con il mouse per avviare il comando.

La maggior parte degli argomenti descritti di seguito corrispondono alle pagine nel [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) esempio.

## <a name="handling-button-clicks"></a>Gestione pulsante fa clic su

`Button` definisce una [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) evento generato quando l'utente tocca il `Button` con un puntatore del mouse o un dito. L'evento viene attivato quando viene rilasciato il pulsante del mouse o un dito della superficie del `Button`. Il `Button` deve avere il relativo [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) impostata su `true` per poter rispondere alle scelte. 

Il **base fare clic sul pulsante** pagina il [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) esempio viene illustrato come creare un'istanza di un `Button` in XAML e handle relativo `Clicked` evento. Il **BasicButtonClickPage.xaml** file contiene un `StackLayout` con un `Label` e un `Button`:

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

Il `Button` tende a occupare tutto lo spazio consentito per esso. Ad esempio, se non si imposta la `HorizontalOptions` proprietà di `Button` su un valore diverso da `Fill`, il `Button` occupano l'intera larghezza del relativo padre.

Per impostazione predefinita, il `Button` rettangolare, ma è possibile assegnare gli angoli arrotondati it tramite il [ `CornerRadius` ](xref:Xamarin.Forms.Button.CornerRadius) proprietà, come descritto di seguito nella sezione [ **pulsante aspetto** ](#button-appearance).

Il [ `Text` ](xref:Xamarin.Forms.Button.Text) proprietà specifica il testo visualizzato nel `Button`. Il [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) evento è impostato su un gestore eventi denominato `OnButtonClicked`. Questo gestore si trova nel file code-behind, **BasicButtonClickPage.xaml.cs**:

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

Quando il `Button` vengono scelti, il `OnButtonClicked` esecuzione del metodo. Il `sender` l'argomento è il `Button` oggetto responsabile di questo evento. Ciò consente di accedere il `Button` oggetto, o per distinguere tra più `Button` gli oggetti che condividono lo stesso `Clicked` evento.

Questa particolare `Clicked` gestore chiama una funzione di animazione che ruota il `Label` 360 gradi in 1000 millisecondi. Ecco il programma in esecuzione su dispositivi iOS e Android e come un'applicazione Windows della piattaforma UWP (Universal) sul desktop di Windows 10:

[![Fare clic sul pulsante di base](button-images/BasicButtonClick.png "pulsante base")](button-images/BasicButtonClick-Large.png#lightbox "clic del pulsante di base")

Si noti che il `OnButtonClicked` metodo include il `async` modificatore perché `await` viene utilizzata nel gestore eventi. Un `Clicked` gestore eventi richiede la `async` modificatore solo se il corpo del gestore utilizza `await`.

Esegue il rendering di ogni piattaforma di `Button` nel proprio modo specifico. Nel [ **pulsante aspetto** ](#button-appearance) sezione, si noterà come impostare colori e rendere il `Button` bordo visibile per gli aspetti più personalizzati. `Button` implementa il [ `IFontElement` ](xref:Xamarin.Forms.Internals.IFontElement) interfaccia, in modo da includere [ `FontFamily` ](xref:Xamarin.Forms.Button.FontFamily), [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize), e [ `FontAttributes` ](xref:Xamarin.Forms.Button.FontAttributes)delle proprietà.

## <a name="creating-a-button-in-code"></a>Creazione di un pulsante nel codice

È comune per creare un'istanza di un `Button` in XAML, ma è anche possibile creare un `Button` nel codice. Questo potrebbe essere utile quando l'applicazione deve creare più pulsanti in base ai dati che sono enumerabili con un `foreach` ciclo.

Il **fare clic sul pulsante di codice** pagina viene illustrato come creare una pagina che è funzionalmente equivalente al **fare clic sul pulsante base** pagina ma interamente in c#:

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

Tutto ciò che viene eseguita nel costruttore della classe. Poiché il `Clicked` gestore è un'unica istruzione, può essere collegato in poche parole all'evento:

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

Naturalmente, è inoltre possibile definire il gestore dell'evento come un metodo separato (esattamente come le `OnButtonClick` metodo in **fare clic sul pulsante base**) e tale metodo di collegamento all'evento:

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>Disattivazione del pulsante

A volte è un'applicazione in un determinato stato in cui un determinato `Button` fare clic non è un'operazione valida. In questi casi, il `Button` deve essere disabilitato impostando il relativo `IsEnabled` proprietà `false`. L'esempio classico è un `Entry` controllo per un nome di file accompagnato da un file e apertura `Button`: il `Button` deve essere abilitato solo se il testo digitato nel `Entry`. È possibile utilizzare un `DataTrigger` per questa attività, come illustrato nel [ **i trigger Data** ](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers) articolo.

## <a name="using-the-command-interface"></a>Tramite l'interfaccia di comando

È possibile che un'applicazione a cui rispondere `Button` scelte senza gestione il `Clicked` evento. Il `Button` implementa un meccanismo di notifica alternativo denominato il _comando_ o _comandi_ interfaccia. Ciò è costituita da due proprietà:

- [`Command`](xref:Xamarin.Forms.Button.Command) di tipo [ `ICommand` ](xref:System.Windows.Input.ICommand), un'interfaccia definita nel [ `System.Windows.Input` ](xref:System.Windows.Input) dello spazio dei nomi.
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) proprietà di tipo [ `Object` ](xref:System.Object).

Questo approccio è particolarmente adatto insieme all'associazione dati e in particolare quando si implementa l'architettura Model-View-ViewModel (MVVM). Questi argomenti vengono descritti negli articoli [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md), [da associazioni dati per MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), e [MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md).

In un'applicazione MVVM, l'elemento ViewModel definisce le proprietà di tipo `ICommand` che vengono quindi connessi a XAML `Button` gli elementi con data binding. Definisce inoltre xamarin. Forms [ `Command` ]((xref:Xamarin.Forms.Command`1)) e [ `Command<T>` ](xref:Xamarin.Forms.Command`1) le classi che implementano il `ICommand` l'interfaccia e assistere la ViewModel nella definizione di proprietà di tipo `ICommand`.

L'esecuzione di comandi è descritta in maggiore dettaglio nell'articolo [ **l'interfaccia di comando** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) ma la **base pulsante di comando** nella pagina il [  **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) esempio viene illustrato l'approccio di base.

Il `CommandDemoViewModel` classe è un ViewModel molto semplice che definisce una proprietà di tipo `double` denominata `Number`e due proprietà di tipo `ICommand` denominato `MultiplyBy2Command` e `DivideBy2Command`:

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

I due `ICommand` le proprietà vengono inizializzate nel costruttore della classe con due oggetti di tipo `Command`. Il `Command` costruttori includono una piccola funzione (chiamato il `execute` argomento del costruttore) che raddoppia o dimezzato il `Number` proprietà.

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

Come i due `Button` toccati elementi, i comandi vengono eseguiti, e il numero Modifica valore:

[![Comando di base dei pulsanti](button-images/BasicButtonCommand.png "comando di base dei pulsanti")](button-images/BasicButtonCommand-Large.png#lightbox)

Il vantaggio di questo approccio rispetto `Clicked` gestori è che tutta la logica che interessano la funzionalità di questa pagina si trova il ViewModel anziché il file code-behind, ottenere una migliore separazione dell'interfaccia utente dalla logica di business.

È anche possibile che il `Command` gli oggetti per controllare l'abilitazione e disabilitazione del `Button` elementi. Ad esempio, si supponga che si desidera limitare l'intervallo dei valori numerici tra 2<sup>10</sup> e 2<sup>&ndash;10</sup>. È possibile aggiungere un'altra funzione del costruttore (chiamato il `canExecute` argomento) che restituisce `true` se il `Button` deve essere abilitata. Ecco la modifica di `CommandDemoViewModel` costruttore:

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

Le chiamate al `ChangeCanExecute` metodo di `Command` sono necessari in modo che il `Command` metodo può chiamare il `canExecute` (metodo) e determinare se il `Button` deve essere disabilitato o abilitato. Con questa modifica del codice, come il numero raggiunge il limite, il `Button` è disabilitato:

[![Comando di base dei pulsanti - modificato](button-images/BasicButtonCommandModified.png "comando di base dei pulsanti - modificato")](button-images/BasicButtonCommandModified-Large.png#lightbox)

È possibile che due o più `Button` gli elementi da associare alla stessa `ICommand` proprietà. Il `Button` possono essere distinte in elementi utilizzando il [ `CommandParameter` ](xref:Xamarin.Forms.Button.CommandParameter) proprietà di `Button`. In questo caso, è opportuno utilizzare il tipo generico [ `Command<T>` ](xref:Xamarin.Forms.Command`1) classe. Il `CommandParameter` oggetto viene quindi passato come argomento per il `execute` e `canExecute` metodi. Questa tecnica è illustrata in dettaglio nel [ **dei comandi di base** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) sezione del [ **comando interfaccia** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding) articolo.

Il [ **ButtonDemos** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos) esempio viene inoltre utilizzata questa tecnica nel relativo `MainPage` classe. Il **MainPage. XAML** file contiene un `Button` per ogni pagina dell'esempio:

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

Ogni `Button` è il relativo `Command` proprietà associata a una proprietà denominata `NavigateCommand`e il `CommandParameter` è impostato su un [ `Type` ](xref:System.Type) oggetto corrispondente a una delle classi di pagina nel progetto.

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

Il costruttore inizializza la `NavigateCommand` proprietà per un `Command<Type>` dell'oggetto poiché `Type` è il tipo del `CommandParameter` oggetto impostato nel file XAML. Ciò significa che il `execute` metodo ha un argomento di tipo `Type` che corrisponde a questo `CommandParameter` oggetto. La funzione crea un'istanza della pagina e quindi si passa a esso.

Si noti che il costruttore si conclude con l'impostazione relativa `BindingContext` a se stessa. Questa operazione è necessaria per le proprietà nel file XAML da associare il `NavigateCommand` proprietà.

## <a name="pressing-and-releasing-the-button"></a>Premendo e rilasciare il pulsante

Oltre il `Clicked` evento `Button` definisce inoltre [ `Pressed` ](xref:Xamarin.Forms.Button.Pressed) e [ `Released` ](xref:Xamarin.Forms.Button.Released) eventi. Il `Pressed` evento si verifica quando un dito preme un `Button`, o si preme un pulsante del mouse con il puntatore posizionato il `Button`. Il `Released` evento si verifica quando viene rilasciato il pulsante del mouse o un dito. In genere, un `Clicked` evento viene generato anche nello stesso momento come il `Released` eventi, ma se il puntatore del mouse o un dito diapositive dalla superficie del `Button` prima di essere rilasciate le `Clicked` evento potrebbe non verificarsi.

Il `Pressed` e `Released` eventi non vengono utilizzati spesso, ma possono essere utilizzati per scopi speciali, come dimostrato nel **premere e bottone** pagina. Il file XAML contiene una `Label` e un `Button` con gestori associati per il `Pressed` e `Released` eventi:

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

Il file code-behind aggiunge un'animazione il `Label` quando un `Pressed` evento si verifica, ma sospende la rotazione quando un `Released` evento si verifica:

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

Il risultato è che il `Label` Ruota solo durante un dito in contatto con il `Button`e si arresta quando viene rilasciato il dito:

[![Premere e rilascio del pulsante](button-images/PressAndReleaseButton.png "premere e rilascio del pulsante")](button-images/PressAndReleaseButton-Large.png)

Questo tipo di comportamento contiene applicazioni per i giochi: un dito mantenuto su una `Button` potrebbe rendere un oggetto dello schermo su spostare in una direzione particolare. 

<a name="button-appearance" />

## <a name="button-appearance"></a>Aspetto del pulsante

Il `Button` eredita o definisce diverse proprietà che influiscono sull'aspetto:

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor) rappresenta il colore del `Button` testo
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) rappresenta il colore di sfondo per il testo
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) rappresenta il colore di un'area che circonda il `Button`
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily) la famiglia di caratteri utilizzata per il testo
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize) le dimensioni del testo
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes) indica se il testo è grassetto o corsivo
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) la larghezza del bordo 
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) Consente di arrotondare gli angoli

Gli effetti di sei di queste proprietà (escludendo `FontFamily` e `FontAttributes`) vengono illustrati il **aspetto del pulsante** pagina. Un'altra proprietà, [ `Image` ](xref:Xamarin.Forms.Button.Image), è descritto nella sezione [ **utilizzando le bitmap con pulsante**](#image-button).

Tutte le associazioni dati e viste nel **aspetto del pulsante** pagina sono definiti nel file XAML:

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

Il `Button` nella parte superiore della pagina è relativo tre `Color` le proprietà associate a `Picker` elementi nella parte inferiore della pagina. Gli elementi di `Picker` elementi sono i colori del `NamedColor` classe incluso nel progetto. Tre `Slider` elementi contengono le associazioni bidirezionali per i `FontSize`, `BorderWidth`, e `CornerRadius` delle proprietà del `Button`.

Questo programma consente di sperimentare le combinazioni di tutte queste proprietà:

[![Pulsante aspetto](button-images/ButtonAppearance.png "pulsante aspetto")](button-images/ButtonAppearance-Large.png)

Per visualizzare il `Button` bordo, sarà necessario impostare un `BorderColor` su un valore diverso da `Default`e il `BorderWidth` su un valore positivo.

In iOS, si noterà che larghezze di grandi dimensioni bordo introduce nell'area interna del `Button` e interferisce con la visualizzazione del testo. Se si sceglie di utilizzare un bordo con iOS `Button`, è opportuno iniziare e terminare la `Text` proprietà con spazi per mantenere la visibilità.

Nella piattaforma UWP, selezionando un `CornerRadius` che supera metà dell'altezza del `Button` genera un'eccezione.

## <a name="creating-a-toggle-button"></a>Creazione di un pulsante Attiva/disattiva

È possibile sottoclasse `Button` in modo che e funziona come un'opzione di attivazione / disattivazione: toccare il pulsante una volta per attivare o disattivare il pulsante nella e toccare di nuovo per disattivarlo.

I seguenti `ToggleButton` classe deriva da `Button` e definisce un nuovo evento denominato `Toggled` e una proprietà booleana denominata `IsToggled`. Questi sono le stesse due proprietà definite di xamarin. Forms [ `Switch` ](xref:Xamarin.Forms.Switch):

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

Il `ToggleButton` costruttore associa un gestore per il `Clicked` evento in modo che non possono modificare il valore della `IsToggled` proprietà. Il `OnIsToggledChanged` metodo generato il `Toggled` evento. 

L'ultima riga del `OnIsToggledChanged` metodo chiama il metodo statico `VisualStateManager.GoToState` (metodo) con il testo di due stringhe "ToggledOn" e "ToggledOff". È possibile leggere su questo metodo e modo in cui l'applicazione può rispondere a stati di visualizzazione nell'articolo [ **il gestore degli stati Visual xamarin. Forms**](~/xamarin-forms/user-interface/visual-state-manager.md). 

Poiché `ToggleButton` effettua la chiamata al `VisualStateManager.GoToState`, la classe stessa non deve necessariamente includere eventuali funzionalità aggiuntive per modificare l'aspetto del pulsante in base alle relative `IsToggled` stato. Vale a dire la responsabilità del codice XAML che ospita il `ToggleButton`. 

Il **Demo pulsante Attiva/disattiva** pagina contiene due istanze di `ToggleButton`, incluso il markup gestore dello stato che consente di impostare il `Text`, `BackgroundColor`, e `TextColor` del pulsante in base allo stato visual: 

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

Il `Toggled` gestori di eventi sono nel file code-behind. Sono responsabili per l'impostazione di `FontAttributes` proprietà del `Label` in base allo stato dei pulsanti:

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

Ecco il programma in esecuzione su iOS, Android e la piattaforma UWP:

[![Attiva/Disattiva pulsante Demo](button-images/ToggleButtonDemo.png "Attiva/Disattiva pulsante Demo")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>Utilizzo di immagini bitmap con pulsanti

Il `Button` classe definisce un [ `Image` ](xref:Xamarin.Forms.Button.Image) proprietà che consente di visualizzare un'immagine bitmap nel `Button`, singolarmente o in combinazione con il testo. È inoltre possibile specificare la modalità di disposizione testo e l'immagine.

Il `Image` proprietà è di tipo [ `FileImageSource` ](xref:Xamarin.Forms.FileImageSource), il che significa che le bitmap devono essere archiviate come risorse nei progetti di piattaforma singoli e non nel progetto di libreria .NET Standard. 

Ogni piattaforma supportata da xamarin. Forms permette di essere archiviati in più dimensioni per le soluzioni di pixel diversa dei vari dispositivi che l'applicazione può essere eseguita su immagini. Queste bitmap più vengono denominata oppure archiviate in modo tale che il sistema operativo può scegliere quello più adatto per la periferica video risoluzione dello schermo. 

Per una bitmap in un `Button`, la dimensione ottimale è in genere tra 32 e 64 unità indipendenti dal dispositivo, a seconda delle dimensioni richiesto. Le immagini utilizzate in questo esempio si basano su una dimensione di 48 unità indipendenti dal dispositivo.

Nel progetto iOS, il **risorse** cartella contiene tre dimensioni di questa immagine:

- Una bitmap quadrata 48 pixel archiviata come **/Resources/MonkeyFace.png**
- Una bitmap quadrata 96 pixel archiviata come **/Resource/MonkeyFace@2x.png**
- Una bitmap quadrata 144 pixel archiviata come **/Resource/MonkeyFace@3x.png**

Sono state passate tutte le tre bitmap una **azione di compilazione** dei **BundleResource**.

Per il progetto Android, tutte le bitmap hanno lo stesso nome, ma sono archiviati nelle sottocartelle diversi della **risorse** cartella:

- Una bitmap quadrata 72 pixel archiviata come **/Resources/drawable-hdpi/MonkeyFace.png**
- Una bitmap quadrata 96 pixel archiviata come **/Resources/drawable-xhdpi/MonkeyFace.png**
- Una bitmap quadrata 144 pixel archiviata come **/Resources/drawable-xxhdpi/MonkeyFace.png**
- Una bitmap quadrata 192 pixel archiviata come **/Resources/drawable-xxxhdpi/MonkeyFace.png**

Questi sono stato passati una **azione di compilazione** dei **AndroidResource**.

Nella bitmap possono essere archiviate in un punto qualsiasi nel progetto, il progetto UWP, ma sono in genere archiviati in una cartella personalizzata o il **asset** cartella esistente. Il progetto UWP contiene queste bitmap:

- Una bitmap quadrata 48 pixel archiviata come **/Assets/MonkeyFace.scale-100.png**
- Una bitmap quadrata 96 pixel archiviata come **/Assets/MonkeyFace.scale-200.png**
- Una bitmap quadrata 192 pixel archiviata come **/Assets/MonkeyFace.scale-400.png**

Essi sono state tutte assegnato un **azione di compilazione** dei **contenuto**.

È possibile specificare il modo in cui `Text` e `Image` proprietà sono disposte nel `Button` utilizzando la [ `ContentLayout` ](xref:Xamarin.Forms.Button.ContentLayout) proprietà di `Button`. Questa proprietà è di tipo [ `ButtonContentLayout` ](xref:Xamarin.Forms.Button.ButtonContentLayout), che è una classe incorporata in `Button`. Il [costruttore](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double)) dispone di due argomenti:

- Un membro del [ `ImagePosition` ](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition) enumerazione: `Left`, `Top`, `Right`, o `Bottom` che indica l'aspetto relativo al testo della bitmap.
- Oggetto `double` valore per la spaziatura tra la mappa di bit e il testo.

I valori predefiniti sono `Left` e a 10 unità. Due proprietà di sola lettura di `ButtonContentLayout` denominato [ `Position` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Position) e [ `Spacing` ](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing) fornire i valori di tali proprietà.

Nel codice, è possibile creare una `Button` e impostare il `ContentLayout` proprietà simile al seguente:

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

Il **immagine del pulsante Demo** pagina utilizza `OnPlatform` per specificare diversi nomi di file per file bitmap, UWP, iOS e Android. Se si desidera utilizzare lo stesso nome file per tutti e tre le piattaforme ed evitare l'utilizzo di `OnPlatform`, è necessario archiviare le bitmap UWP nella directory radice del progetto.

Il primo `Button` nella **immagine del pulsante Demo** pagina imposta il `Image` proprietà ma non il `Text` proprietà:

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

Se le bitmap UWP sono archiviate nella directory radice del progetto, questo codice può essere notevolmente semplificata:

```xaml
<Button Image="MonkeyFace.png" />
```

Per evitare molte markup ripetitive nel **ImageButtonDemo.xaml** file impliciti `Style` è definito anche per impostare il `Image` proprietà. Ciò `Style` viene applicata automaticamente a cinque altri `Button` elementi. Ecco il file XAML completo:

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

[![Immagine pulsante Demo](button-images/ImageButtonDemo.png "immagine pulsante Demo")](button-images/ImageButtonDemo-Large.png#lightbox)

Abbiamo visto i vari modi in cui è possibile gestire `Button` gli eventi e modificare il `Button` aspetto.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio ButtonDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ButtonDemos)
- [Pulsante API](xref:Xamarin.Forms.Button)
