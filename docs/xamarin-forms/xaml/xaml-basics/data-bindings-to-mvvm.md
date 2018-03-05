---
title: Parte 5. Da associazioni di dati per MVVM
description: "Il modello architetturale Model-View-ViewModel (MVVM) è stato ideato con XAML presente. Il modello consente di applicare una separazione tra tre livelli di software, l'interfaccia utente XAML, chiamato della visualizzazione. i dati sottostanti, chiamati del modello. e un intermediario tra la visualizzazione e il modello di chiamata di ViewModel. La visualizzazione e l'elemento ViewModel spesso sono connessi tramite le associazioni dati definite nel file XAML. BindingContext per la vista è in genere un'istanza di ViewModel."
ms.topic: article
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: b16aa2456cdae7a08f8f9ee8adbc32c124e78e18
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>Parte 5. Da associazioni di dati per MVVM

_Il modello architetturale Model-View-ViewModel (MVVM) è stato ideato con XAML presente. Il modello consente di applicare una separazione tra tre livelli di software, l'interfaccia utente XAML, chiamato della visualizzazione. i dati sottostanti, chiamati del modello. e un intermediario tra la visualizzazione e il modello di chiamata di ViewModel. La visualizzazione e l'elemento ViewModel spesso sono connessi tramite le associazioni dati definite nel file XAML. BindingContext per la vista è in genere un'istanza di ViewModel._

## <a name="a-simple-viewmodel"></a>Un semplice ViewModel

Fornisce un'introduzione al ViewModel, innanzitutto Esaminiamo un programma senza uno.
In precedenza è stato illustrato come definire una nuova dichiarazione dello spazio dei nomi XML per consentire a un file XAML alle classi di riferimento in altri assembly. Di seguito è un programma che definisce una dichiarazione dello spazio dei nomi XML per il `System` dello spazio dei nomi:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

È possibile utilizzare `x:Static` per ottenere la data e ora correnti da statico `DateTime.Now` proprietà set e `DateTime` valore per il `BindingContext` su un `StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext` è una proprietà speciale molto: quando si imposta la `BindingContext` su un elemento, viene ereditata da tutti gli elementi figlio di tale elemento. Ciò significa che tutti gli elementi figlio del `StackLayout` questo stesso `BindingContext`, e possono contenere associazioni semplici alle proprietà dell'oggetto.

Nel **One-Shot DateTime** programma, due nodi figlio contengono le associazioni alle proprietà di tale `DateTime` valore, ma due altri elementi figlio contiene le associazioni che sembrano essere presente un percorso di associazione. Ciò significa che il `DateTime` valore stesso viene utilizzato per il `StringFormat`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="XamlSamples.OneShotDateTimePage"
             Title="One-Shot DateTime Page">

    <StackLayout BindingContext="{x:Static sys:DateTime.Now}"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">

        <Label Text="{Binding Year, StringFormat='The year is {0}'}" />
        <Label Text="{Binding StringFormat='The month is {0:MMMM}'}" />
        <Label Text="{Binding Day, StringFormat='The day is {0}'}" />
        <Label Text="{Binding StringFormat='The time is {0:T}'}" />

    </StackLayout>
</ContentPage>
```

Naturalmente, grande problema è che la data e ora sono set di una volta quando la pagina viene innanzitutto compilata e mai modifica:

[ ![](data-bindings-to-mvvm-images/oneshotdatetime.png "Visualizzazione dei data e ora")](data-bindings-to-mvvm-images/oneshotdatetime-large.png "visualizzazione dei data e ora")

Un file XAML è possibile visualizzare un clock che viene mostrato sempre l'ora corrente, ma è necessario un codice per aiutare. Quando il concetto in termini di MVVM, il modello e ViewModel sono classi scritte interamente in codice. La visualizzazione è spesso un file XAML che fa riferimento a proprietà definite nel ViewModel le associazioni di dati.

Un modello appropriato è sostanze il ViewModel e un ViewModel corretto è che non riconoscono della vista. Tuttavia, molto spesso un programmatore adatta i tipi di dati esposti dal ViewModel per i tipi di dati associati con interfacce utente specifico. Ad esempio, se un modello accede a un database che contiene le stringhe ASCII di caratteri a 8 bit, l'elemento ViewModel necessario per la conversione tra le stringhe per le stringhe Unicode per gestire l'uso esclusivo di Unicode nell'interfaccia utente.

Negli esempi semplici di MVVM (ad esempio quelli riportati), spesso è presente alcun modello, il modello include solo una visualizzazione e ViewModel collegate con data binding.

Ecco un ViewModel per un clock con solo una singola proprietà denominata `DateTime`, ma gli aggiornamenti che `DateTime` proprietà ogni secondo:

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    class ClockViewModel : INotifyPropertyChanged
    {
        DateTime dateTime;

        public event PropertyChangedEventHandler PropertyChanged;

        public ClockViewModel()
        {
            this.DateTime = DateTime.Now;

            Device.StartTimer(TimeSpan.FromSeconds(1), () =>
                {
                    this.DateTime = DateTime.Now;
                    return true;
                });
        }

        public DateTime DateTime
        {
            set
            {
                if (dateTime != value)
                {
                    dateTime = value;

                    if (PropertyChanged != null)
                    {
                        PropertyChanged(this, new PropertyChangedEventArgs("DateTime"));
                    }
                }
            }
            get
            {
                return dateTime;
            }
        }
    }
}
```

In genere implementare ViewModel di `INotifyPropertyChanged` interfaccia, il che significa che la classe genera un `PropertyChanged` evento ogni volta che una delle relative proprietà cambia. Il meccanismo di associazione dati in xamarin. Forms associa un gestore per questo `PropertyChanged` evento in modo può ricevere una notifica quando viene modificata una proprietà e mantenere la destinazione aggiornata con il nuovo valore.

Un clock in base a questa ViewModel può essere semplice come questa:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ClockPage"
             Title="Clock Page">

    <Label Text="{Binding DateTime, StringFormat='{0:T}'}"
           FontSize="Large"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Label.BindingContext>
            <local:ClockViewModel />
        </Label.BindingContext>
    </Label>
</ContentPage>
```

Si noti come `ClockViewModel` è impostato sul `BindingContext` del `Label` con il tag di elemento di proprietà. In alternativa, è possibile creare un'istanza di `ClockViewModel` in un `Resources` raccolta e impostarlo sul `BindingContext` tramite un `StaticResource` estensione di markup. In alternativa, il file code-behind può creare un'istanza di ViewModel.

Il `Binding` estensione di markup nel `Text` proprietà del `Label` formati di `DateTime` proprietà. Ecco la visualizzazione:

[ ![](data-bindings-to-mvvm-images/clock.png "Visualizzazione dei data e ora tramite ViewModel")](data-bindings-to-mvvm-images/clock-large.png "visualizzazione dei data e ora tramite ViewModel")

È anche possibile accedere alle singole proprietà del `DateTime` il ViewModel separando le proprietà con periodi di proprietà:

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>MVVM interattiva

MVVM viene spesso utilizzato con le associazioni di dati bidirezionale per una visualizzazione interattiva basata su un modello di dati sottostante.

Di seguito è una classe denominata `HslViewModel` che converte un `Color` valore in `Hue`, `Saturation`, e `Luminosity` valori e viceversa:

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    public class HslViewModel : INotifyPropertyChanged
    {
        double hue, saturation, luminosity;
        Color color;

        public event PropertyChangedEventHandler PropertyChanged;

        public double Hue
        {
            set
            {
                if (hue != value)
                {
                    hue = value;
                    OnPropertyChanged("Hue");
                    SetNewColor();
                }
            }
            get
            {
                return hue;
            }
        }

        public double Saturation
        {
            set
            {
                if (saturation != value)
                {
                    saturation = value;
                    OnPropertyChanged("Saturation");
                    SetNewColor();
                }
            }
            get
            {
                return saturation;
            }
        }

        public double Luminosity
        {
            set
            {
                if (luminosity != value)
                {
                    luminosity = value;
                    OnPropertyChanged("Luminosity");
                    SetNewColor();
                }
            }
            get
            {
                return luminosity;
            }
        }

        public Color Color
        {
            set
            {
                if (color != value)
                {
                    color = value;
                    OnPropertyChanged("Color");

                    Hue = value.Hue;
                    Saturation = value.Saturation;
                    Luminosity = value.Luminosity;
                }
            }
            get
            {
                return color;
            }
        }

        void SetNewColor()
        {
            Color = Color.FromHsla(Hue, Saturation, Luminosity);
        }

        protected virtual void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Diventa il `Hue`, `Saturation`, e `Luminosity` proprietà causa la `Color` proprietà da modificare e modifica `Color` fa sì che le tre proprietà modificare. Ciò potrebbe sembrare un ciclo infinito, ad eccezione del fatto che la classe non richiama il `PropertyChanged` evento, a meno che la proprietà viene modificata. In questo modo una fine per il ciclo di commenti e suggerimenti in caso contrario non controllabile.

Il file XAML seguente contiene un `BoxView` cui `Color` proprietà è associata al `Color` il ViewModel e tre proprietà `Slider` e tre `Label` viste associato al `Hue`, `Saturation`, e`Luminosity` proprietà:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.HslColorScrollPage"
             Title="HSL Color Scroll Page">
    <ContentPage.BindingContext>
        <local:HslViewModel Color="Aqua" />
    </ContentPage.BindingContext>

    <StackLayout Padding="10, 0">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Hue, Mode=TwoWay}" />

        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Saturation, Mode=TwoWay}" />

        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Luminosity, Mode=TwoWay}" />
    </StackLayout>
</ContentPage>
```

L'associazione in ogni `Label` il valore predefinito è `OneWay`. Sufficiente visualizzare il valore. Ma il binding in ogni `Slider` è `TwoWay`. In questo modo il `Slider` di essere inizializzata dal ViewModel. Si noti che il `Color` è impostata su `Blue` quando viene creata un'istanza di ViewModel. Una modifica, ma il `Slider` deve inoltre impostare un nuovo valore per la proprietà di ViewModel, che quindi viene calcolato un nuovo colore.

[ ![](data-bindings-to-mvvm-images/hslcolorscroll.png "Uso di associazioni di dati bidirezionale MVVM")](data-bindings-to-mvvm-images/hslcolorscroll-large.png "MVVM usando le associazioni dati bidirezionale")

## <a name="commanding-with-viewmodels"></a>Esecuzione di comandi con ViewModel

In molti casi, il modello MVVM è limitato per la modifica di elementi di dati: oggetti dell'interfaccia utente nella visualizzazione parallela di ViewModel gli oggetti dati.

Tuttavia, talvolta la vista deve contenere i pulsanti che attivano azioni diverse nel ViewModel. Ma non deve contenere l'elemento ViewModel `Clicked` gestori per i pulsanti perché che potrebbe bloccare il ViewModel a un paradigma dell'interfaccia utente specifico.

Per consentire ViewModel di essere indipendente più oggetti dell'interfaccia utente specifico, ma consente ancora metodi da chiamare all'interno di ViewModel, un *comando* interfaccia è disponibile. Questa interfaccia di comando è supportata dai seguenti elementi in xamarin. Forms:

-  `Button`
-  `MenuItem`
-  `ToolbarItem`
-  `SearchBar`
-  `TextCell` (e pertanto anche `ImageCell`)
-  `ListView`
-  `TapGestureRecognizer`

Ad eccezione del `SearchBar` e `ListView` elemento, questi elementi definiscono due proprietà:

-  `Command` di tipo  `System.Windows.Input.ICommand`
-  `CommandParameter` di tipo  `Object`

Il `SearchBar` definisce `SearchCommand` e `SearchCommandParameter` proprietà, mentre il `ListView` definisce un `RefreshCommand` proprietà di tipo `ICommand`.

Il `ICommand` interfaccia definisce un evento e i due metodi:

-  `void Execute(object arg)`
-  `bool CanExecute(object arg)`
-  `event EventHandler CanExecuteChanged`

L'elemento ViewModel può definire le proprietà di tipo `ICommand`. È possibile associare queste proprietà per il `Command` proprietà di ogni `Button` o altro elemento, o forse una visualizzazione personalizzata che implementa questa interfaccia. È possibile impostare facoltativamente il `CommandParameter` proprietà per identificare singole `Button` oggetti (o altri elementi) sono associati a questa proprietà ViewModel. Internamente, il `Button` chiamate il `Execute` metodo ogni volta che l'utente tocca il `Button`, passando al `Execute` metodo relativo `CommandParameter`.

Il `CanExecute` (metodo) e `CanExecuteChanged` evento vengono utilizzati per i casi in cui un `Button` scelta potrebbe non essere attualmente valida, nel qual caso il `Button` deve disabilita automaticamente. Il `Button` chiamate `CanExecute` quando il `Command` viene innanzitutto impostata e ogni volta che il `CanExecuteChanged` viene generato l'evento. Se `CanExecute` restituisce `false`, `Button` disabilitato automaticamente e non genera `Execute` chiamate.

Per informazioni sull'aggiunta di comandi per il ViewModel, xamarin. Forms definisce due classi che implementano `ICommand`: `Command` e `Command<T>` in `T` è il tipo degli argomenti di `Execute` e `CanExecute`. Queste due classi definiscono costruttori diverse oltre a una `ChangeCanExecute` metodo che l'elemento ViewModel può chiamare per forzare il `Command` oggetto attivano il `CanExecuteChanged` evento.

Di seguito è riportato un ViewModel per un tastierino semplice che è previsto per l'immissione di numeri di telefono. Si noti che il `Execute` e `CanExecute` metodo sono definite come destra funzioni lambda nel costruttore:

```csharp
using System;
using System.ComponentModel;
using System.Windows.Input;
using Xamarin.Forms;

namespace XamlSamples
{
    class KeypadViewModel : INotifyPropertyChanged
    {
        string inputString = "";
        string displayText = "";
        char[] specialChars = { '*', '#' };

        public event PropertyChangedEventHandler PropertyChanged;

        // Constructor
        public KeypadViewModel()
        {
            AddCharCommand = new Command<string>((key) =>
                {
                    // Add the key to the input string.
                    InputString += key;
                });

            DeleteCharCommand = new Command(() =>
                {
                    // Strip a character from the input string.
                    InputString = InputString.Substring(0, InputString.Length - 1);
                },
                () =>
                {
                    // Return true if there's something to delete.
                    return InputString.Length > 0;
                });
        }

        // Public properties
        public string InputString
        {
            protected set
            {
                if (inputString != value)
                {
                    inputString = value;
                    OnPropertyChanged("InputString");
                    DisplayText = FormatText(inputString);

                    // Perhaps the delete button must be enabled/disabled.
                    ((Command)DeleteCharCommand).ChangeCanExecute();
                }
            }

            get { return inputString; }
        }

        public string DisplayText
        {
            protected set
            {
                if (displayText != value)
                {
                    displayText = value;
                    OnPropertyChanged("DisplayText");
                }
            }
            get { return displayText; }
        }

        // ICommand implementations
        public ICommand AddCharCommand { protected set; get; }

        public ICommand DeleteCharCommand { protected set; get; }

        string FormatText(string str)
        {
            bool hasNonNumbers = str.IndexOfAny(specialChars) != -1;
            string formatted = str;

            if (hasNonNumbers || str.Length < 4 || str.Length > 10)
            {
            }
            else if (str.Length < 8)
            {
                formatted = String.Format("{0}-{1}",
                                          str.Substring(0, 3),
                                          str.Substring(3));
            }
            else
            {
                formatted = String.Format("({0}) {1}-{2}",
                                          str.Substring(0, 3),
                                          str.Substring(3, 3),
                                          str.Substring(6));
            }
            return formatted;
        }

        protected void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Questo ViewModel si presuppone che il `AddCharCommand` proprietà è associata al `Command` proprietà dei diversi pulsanti (o qualsiasi altro elemento che dispone di un'interfaccia di comando), ognuno dei quali è identificato dal `CommandParameter`. Questi pulsanti aggiungere caratteri a un `InputString` proprietà, che verrà formattato come un numero di telefono per la `DisplayText` proprietà.

È inoltre disponibile una seconda proprietà di tipo `ICommand` denominato `DeleteCharCommand`. Questo è associato a un pulsante Indietro spaziatura, ma il pulsante deve essere disabilitato se non sono presenti caratteri da eliminare.

Il tastierino seguente non è stata come visivamente sofisticato come è possibile. Al contrario, il markup è stato ridotto al minimo per illustrare più chiaramente l'uso dell'interfaccia di comando:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.KeypadPage"
             Title="Keypad Page">

    <Grid HorizontalOptions="Center"
          VerticalOptions="Center">
        <Grid.BindingContext>
            <local:KeypadViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
        </Grid.ColumnDefinitions>

        <!-- Internal Grid for top row of items -->
        <Grid Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="Auto" />
            </Grid.ColumnDefinitions>

            <Frame Grid.Column="0"
                   OutlineColor="Accent">
                <Label Text="{Binding DisplayText}" />
            </Frame>

            <Button Text="&#x21E6;"
                    Command="{Binding DeleteCharCommand}"
                    Grid.Column="1"
                    BorderWidth="0" />
        </Grid>

        <Button Text="1"
                Command="{Binding AddCharCommand}"
                CommandParameter="1"
                Grid.Row="1" Grid.Column="0" />

        <Button Text="2"
                Command="{Binding AddCharCommand}"
                CommandParameter="2"
                Grid.Row="1" Grid.Column="1" />

        <Button Text="3"
                Command="{Binding AddCharCommand}"
                CommandParameter="3"
                Grid.Row="1" Grid.Column="2" />

        <Button Text="4"
                Command="{Binding AddCharCommand}"
                CommandParameter="4"
                Grid.Row="2" Grid.Column="0" />

        <Button Text="5"
                Command="{Binding AddCharCommand}"
                CommandParameter="5"
                Grid.Row="2" Grid.Column="1" />

        <Button Text="6"
                Command="{Binding AddCharCommand}"
                CommandParameter="6"
                Grid.Row="2" Grid.Column="2" />

        <Button Text="7"
                Command="{Binding AddCharCommand}"
                CommandParameter="7"
                Grid.Row="3" Grid.Column="0" />

        <Button Text="8"
                Command="{Binding AddCharCommand}"
                CommandParameter="8"
                Grid.Row="3" Grid.Column="1" />

        <Button Text="9"
                Command="{Binding AddCharCommand}"
                CommandParameter="9"
                Grid.Row="3" Grid.Column="2" />

        <Button Text="*"
                Command="{Binding AddCharCommand}"
                CommandParameter="*"
                Grid.Row="4" Grid.Column="0" />

        <Button Text="0"
                Command="{Binding AddCharCommand}"
                CommandParameter="0"
                Grid.Row="4" Grid.Column="1" />

        <Button Text="#"
                Command="{Binding AddCharCommand}"
                CommandParameter="#"
                Grid.Row="4" Grid.Column="2" />
    </Grid>
</ContentPage>
```

Il `Command` proprietà del primo `Button` che viene visualizzato in questo tag è associato al `DeleteCharCommand`; gli altri sono associati al `AddCharCommand` con un `CommandParameter` ovvero coincide con il carattere che viene visualizzato nel `Button` faccia. Ecco il programma in azione:

[ ![](data-bindings-to-mvvm-images/keypad.png "Calcolatrice con i comandi e MVVM")](data-bindings-to-mvvm-images/keypad-large.png "calcolatrice con i comandi e MVVM")

### <a name="invoking-asynchronous-methods"></a>Richiamo di metodi asincroni

I comandi possono anche richiamare i metodi asincroni. Questo risultato viene ottenuto tramite il `async` e `await` parole chiave quando si specifica il `Execute` metodo:

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

Ciò indica che il `DownloadAsync` metodo è un `Task` e deve essere atteso:

```csharp
async Task DownloadAsync ()
{
    await Task.Run (() => Download ());
}

void Download ()
{
    ...
}
```

## <a name="implementing-a-navigation-menu"></a>Implementazione di un Menu di navigazione

Il [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/) programma contiene tutto il codice sorgente in questa serie di articoli viene utilizzato un ViewModel per la relativa home page. Questo ViewModel è una definizione di una classe breve con tre proprietà denominate `Type`, `Title`, e `Description` che contiene il tipo di ognuna delle pagine di esempio, un titolo e una breve descrizione. Inoltre, l'elemento ViewModel definisce una proprietà statica denominata `All` che è una raccolta di tutte le pagine del programma:

```csharp
public class PageDataViewModel
{
    public PageDataViewModel(Type type, string title, string description)
    {
        Type = type;
        Title = title;
        Description = description;
    }

    public Type Type { private set; get; }

    public string Title { private set; get; }

    public string Description { private set; get; }

    static PageDataViewModel()
    {
        All = new List<PageDataViewModel>
        {
            // Part 1. Getting Started with XAML
            new PageDataViewModel(typeof(HelloXamlPage), "Hello, XAML",
                                  "Display a Label with many properties set"),

            new PageDataViewModel(typeof(XamlPlusCodePage), "XAML + Code",
                                  "Interact with a Slider and Button"),

            // Part 2. Essential XAML Syntax
            new PageDataViewModel(typeof(GridDemoPage), "Grid Demo",
                                  "Explore XAML syntax with the Grid"),

            new PageDataViewModel(typeof(AbsoluteDemoPage), "Absolute Demo",
                                  "Explore XAML syntax with AbsoluteLayout"),

            // Part 3. XAML Markup Extensions
            new PageDataViewModel(typeof(SharedResourcesPage), "Shared Resources",
                                  "Using resource dictionaries to share resources"),

            new PageDataViewModel(typeof(StaticConstantsPage), "Static Constants",
                                  "Using the x:Static markup extensions"),

            new PageDataViewModel(typeof(RelativeLayoutPage), "Relative Layout",
                                  "Explore XAML markup extensions"),

            // Part 4. Data Binding Basics
            new PageDataViewModel(typeof(SliderBindingsPage), "Slider Bindings",
                                  "Bind properties of two views on the page"),

            new PageDataViewModel(typeof(SliderTransformsPage), "Slider Transforms",
                                  "Use Sliders with reverse bindings"),

            new PageDataViewModel(typeof(ListViewDemoPage), "ListView Demo",
                                  "Use a ListView with data bindings"),

            // Part 5. From Data Bindings to MVVM
            new PageDataViewModel(typeof(OneShotDateTimePage), "One-Shot DateTime",
                                  "Obtain the current DateTime and display it"),

            new PageDataViewModel(typeof(ClockPage), "Clock",
                                  "Dynamically display the current time"),

            new PageDataViewModel(typeof(HslColorScrollPage), "HSL Color Scroll",
                                  "Use a view model to select HSL colors"),

            new PageDataViewModel(typeof(KeypadPage), "Keypad",
                                  "Use a view model for numeric keypad logic")
        };
    }

    public static IList<PageDataViewModel> All { private set; get; } 
}
```

File XAML per `MainPage` definisce un `ListBox` cui `ItemsSource` è impostata su che `All` proprietà e che contiene un `TextCell` per la visualizzazione di `Title` e `Description` le proprietà di ogni pagina:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage"
             Padding="5, 0"
             Title="XAML Samples">

    <ListView ItemsSource="{x:Static local:PageDataViewModel.All}"
              ItemSelected="OnListViewItemSelected">
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding Title}"
                          Detail="{Binding Description}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Le pagine vengono visualizzate nell'elenco scorrevole:

[ ![](data-bindings-to-mvvm-images/mainpage.png "Elenco scorrevole delle pagine")](data-bindings-to-mvvm-images/mainpage-large.png "scorrevole elenco delle pagine")

Il gestore nel file code-behind viene attivato quando l'utente seleziona un elemento. Il gestore imposta la `SelectedItem` proprietà del `ListBox` al `null` crea un'istanza della pagina selezionata e quindi si passa a esso:

```csharp
private async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
{
    (sender as ListView).SelectedItem = null;

    if (args.SelectedItem != null)
    {
        PageDataViewModel pageData = args.SelectedItem as PageDataViewModel;
        Page page = (Page)Activator.CreateInstance(pageData.Type);
        await Navigation.PushAsync(page);
    }
}
```

## <a name="summary"></a>Riepilogo

XAML è un potente strumento per la definizione di interfacce utente nelle applicazioni di xamarin. Forms, in particolare quando l'associazione dati e MVVM vengono utilizzati. Il risultato è una rappresentazione parziale, elegante e potenzialmente ricco di un'interfaccia utente con tutto il supporto di background nel codice.


## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Introduzione a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Nozioni di base sul data binding](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
