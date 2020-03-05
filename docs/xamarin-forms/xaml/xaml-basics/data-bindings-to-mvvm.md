---
title: Parte 5. Da data binding a MVVM
description: Il modello MVVM consente di applicare una separazione tra tre livelli di software, ovvero l'interfaccia utente XAML, chiamato della visualizzazione. i dati sottostanti, chiamati del modello. e un intermediario tra la visualizzazione e il modello, chiamati ViewModel.
ms.prod: xamarin
ms.custom: video
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 1a6ab1393cbcd8224411aeea2af2aca27381bba3
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "78293088"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>Parte 5. Da data binding a MVVM

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Il modello architetturale MVC (Model-View-ViewModel) è stato inventato tenendo presente XAML. Il modello impone una separazione tra tre livelli software, ovvero l'interfaccia utente XAML, denominata vista; i dati sottostanti, detti modello; e un intermediario tra la vista e il modello, denominato ViewModel. La visualizzazione e il ViewModel sono spesso connessi tramite associazioni dati definite nel file XAML. L'elemento BindingContext per la vista è in genere un'istanza dell'elemento ViewModel._

## <a name="a-simple-viewmodel"></a>Un ViewModel semplice

Fornisce un'introduzione al ViewModel, prima di tutto esaminata in un programma senza uno.
In precedenza è stato illustrato come definire una nuova dichiarazione dello spazio dei nomi XML per consentire un file XAML da classi di riferimento in altri assembly. Di seguito è riportato un programma che definisce una dichiarazione dello spazio dei nomi XML per lo spazio dei nomi `System`:

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Il programma può utilizzare `x:Static` per ottenere la data e l'ora correnti dalla proprietà `DateTime.Now` statica e impostare tale valore `DateTime` sul `BindingContext` in una `StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext` è una proprietà speciale: quando si imposta il `BindingContext` su un elemento, questo viene ereditato da tutti gli elementi figlio di tale elemento. Ciò significa che tutti gli elementi figlio del `StackLayout` hanno lo stesso `BindingContext`e possono contenere associazioni semplici alle proprietà di tale oggetto.

Nel programma **di data/ora** monouso, due elementi figlio contengono associazioni alle proprietà di tale valore `DateTime`, ma altri due elementi figlio contengono binding che sembrano privo di un percorso di associazione. Ciò significa che il valore `DateTime` stesso viene usato per la `StringFormat`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
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

Il problema è che la data e l'ora vengono impostate una volta quando la pagina viene compilata per la prima volta e non cambiano mai:

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "View Displaying Date and Time")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "View Displaying Date and Time")

Un file XAML può visualizzare un clock che Mostra sempre l'ora corrente, ma è necessario codice per il supporto. Quando si pensa a MVVM, il modello e ViewModel sono classi scritte interamente nel codice. La visualizzazione è spesso un file XAML che fa riferimento a proprietà definite nel ViewModel tramite le associazioni dati.

Un modello appropriato è assolutamente inconsapevole dei ViewModel e un elemento ViewModel corretto è che non riconoscono la della visualizzazione. Tuttavia, spesso un programmatore ha i tipi di dati esposti dal ViewModel ai tipi di dati associati a specifiche interfacce utente. Ad esempio, se un modello accede a un database che contiene le stringhe ASCII di caratteri a 8 bit, l'elemento ViewModel necessario per la conversione tra le stringhe per le stringhe Unicode per supportare l'uso esclusivo di Unicode nell'interfaccia utente.

In semplici esempi del modello MVVM (ad esempio quelli illustrati di seguito), spesso non esiste alcun modello affatto e il modello comporta semplicemente una vista e ViewModel collegate con data binding.

Di seguito è riportato un ViewModel per un orologio con una sola proprietà denominata `DateTime`, che aggiorna la proprietà `DateTime` ogni secondo:

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

I ViewModel implementano in genere l'interfaccia `INotifyPropertyChanged`, il che significa che la classe genera un evento di `PropertyChanged` ogni volta che una delle relative proprietà viene modificata. Il meccanismo data binding in Novell. Forms associa un gestore a questo evento `PropertyChanged` in modo che possa ricevere una notifica quando una proprietà viene modificata e mantiene la destinazione aggiornata con il nuovo valore.

Un orologio in base a tale elemento ViewModel può essere semplice come questa:

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

Si noti come il `ClockViewModel` sia impostato sul `BindingContext` della `Label` usando i tag dell'elemento proprietà. In alternativa, è possibile creare un'istanza della `ClockViewModel` in una raccolta di `Resources` e impostarla sulla `BindingContext` tramite un'estensione di markup `StaticResource`. In alternativa, il file code-behind può creare un'istanza di ViewModel.

L'estensione di markup `Binding` nella proprietà `Text` del `Label` formatta la proprietà `DateTime`. Ecco la visualizzazione:

[![](data-bindings-to-mvvm-images/clock.png "View Displaying Date and Time via ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "View Displaying Date and Time via ViewModel")

È anche possibile accedere a singole proprietà della proprietà `DateTime` dell'elemento ViewModel separando le proprietà con i punti:

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>MVVM interattiva

MVVM viene spesso usato con le associazioni dati bidirezionali per una visualizzazione interattiva basata su un modello di dati sottostante.

Ecco una classe denominata `HslViewModel` che converte un valore `Color` in valori `Hue`, `Saturation`e `Luminosity` e viceversa:

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

Le modifiche apportate alle proprietà `Hue`, `Saturation`e `Luminosity` provocano la modifica della proprietà `Color` e le modifiche apportate a `Color` causano la modifica delle altre tre proprietà. Questo può sembrare un ciclo infinito, ad eccezione del fatto che la classe non richiama l'evento `PropertyChanged`, a meno che la proprietà non sia stata modificata. In questo modo si impedisce al ciclo di commenti e suggerimenti in caso contrario, non controllabile.

Il file XAML seguente contiene un `BoxView` la cui proprietà `Color` è associata alla proprietà `Color` dell'elemento ViewModel e tre `Slider` e tre visualizzazioni `Label` associato alle proprietà `Hue`, `Saturation`e `Luminosity`:

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

Il binding in ogni `Label` è il `OneWay`predefinito. Deve solo visualizzare il valore. Tuttavia, l'associazione a ogni `Slider` è `TwoWay`. Questo consente l'inizializzazione dell'`Slider` dal ViewModel. Si noti che la proprietà `Color` è impostata su `Aqua` quando viene creata un'istanza di ViewModel. Tuttavia, una modifica nel `Slider` deve anche impostare un nuovo valore per la proprietà nell'elemento ViewModel, che quindi calcola un nuovo colore.

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "MVVM using Two-Way Data Bindings")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM using Two-Way Data Bindings")

## <a name="commanding-with-viewmodels"></a>Esecuzione dei comandi con ViewModel

In molti casi, il modello MVVM è limitato alla manipolazione degli elementi di dati: oggetti dell'interfaccia utente nella visualizzazione di oggetti dati in ViewModel in parallelo.

Tuttavia, in alcuni casi la vista deve contenere i pulsanti per attivano azioni diverse in ViewModel. Il ViewModel, tuttavia, non deve contenere gestori di `Clicked` per i pulsanti perché il ViewModel viene associato a un paradigma dell'interfaccia utente specifico.

Per consentire la maggiore indipendenza dei ViewModel di oggetti specifici dell'interfaccia utente, ma che consentono comunque di chiamare i metodi all'interno di ViewModel, esiste un'interfaccia di *comando* . Questa interfaccia di comando è supportata dai seguenti elementi in xamarin. Forms:

- `Button`
- `MenuItem`
- `ToolbarItem`
- `SearchBar`
- `TextCell` (e di conseguenza `ImageCell`)
- `ListView`
- `TapGestureRecognizer`

Ad eccezione dell'elemento `SearchBar` e `ListView`, questi elementi definiscono due proprietà:

- `Command` di tipo `System.Windows.Input.ICommand`
- `CommandParameter` di tipo `Object`

Il `SearchBar` definisce `SearchCommand` e `SearchCommandParameter` proprietà, mentre il `ListView` definisce una proprietà di `RefreshCommand` di tipo `ICommand`.

L'interfaccia `ICommand` definisce due metodi e un evento:

- `void Execute(object arg)`
- `bool CanExecute(object arg)`
- `event EventHandler CanExecuteChanged`

Il ViewModel può definire proprietà di tipo `ICommand`. È quindi possibile associare queste proprietà alla proprietà `Command` di ogni `Button` o altro elemento oppure a una vista personalizzata che implementa questa interfaccia. Facoltativamente, è possibile impostare la proprietà `CommandParameter` per identificare singoli oggetti `Button` (o altri elementi) associati a questa proprietà ViewModel. Internamente, il `Button` chiama il metodo `Execute` ogni volta che l'utente tocca il `Button`, passando al metodo `Execute` `CommandParameter`.

Il metodo `CanExecute` e l'evento `CanExecuteChanged` vengono usati per i casi in cui un tocco di `Button` potrebbe non essere attualmente valido, nel qual caso il `Button` dovrebbe disabilitare se stesso. Il `Button` chiama `CanExecute` quando la proprietà `Command` è impostata per la prima volta e ogni volta che viene generato l'evento `CanExecuteChanged`. Se `CanExecute` restituisce `false`, il `Button` viene disabilitato e non genera chiamate `Execute`.

Per informazioni sull'aggiunta di comandi ai ViewModel, Novell. Forms definisce due classi che implementano `ICommand`: `Command` e `Command<T>` dove `T` è il tipo degli argomenti per `Execute` e `CanExecute`. Queste due classi definiscono diversi costruttori, oltre a un metodo di `ChangeCanExecute` che il ViewModel può chiamare per forzare l'oggetto `Command` a generare l'evento di `CanExecuteChanged`.

Ecco un ViewModel per un tastierino semplice che è previsto per l'immissione di numeri di telefono. Si noti che il `Execute` e il metodo `CanExecute` sono definiti come funzioni lambda direttamente nel costruttore:

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

Questo ViewModel presuppone che la proprietà `AddCharCommand` sia associata alla proprietà `Command` di diversi pulsanti (o qualsiasi altro elemento con un'interfaccia di comando), ognuno dei quali è identificato dal `CommandParameter`. Questi pulsanti aggiungono caratteri a una proprietà `InputString`, che viene quindi formattata come numero di telefono per la proprietà `DisplayText`.

Esiste anche una seconda proprietà di tipo `ICommand` `DeleteCharCommand`denominata. Questo è associato a un pulsante Indietro-spaziatura, ma il pulsante deve essere disabilitato se non sono presenti caratteri da eliminare.

Il tastierino seguente non è come visivamente sofisticato poiché potrebbe essere. Al contrario, il markup è stato ridotto al minimo per illustrare più chiaramente l'uso dell'interfaccia di comando:

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

La `Command` proprietà della prima `Button` visualizzata in questo markup è associata al `DeleteCharCommand`; il resto viene associato al `AddCharCommand` con un `CommandParameter` che corrisponde al carattere visualizzato sulla `Button` viso. Ecco il programma in azione:

[![](data-bindings-to-mvvm-images/keypad.png "Calculator using MVVM and Commands")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "Calculator using MVVM and Commands")

### <a name="invoking-asynchronous-methods"></a>Richiamo di metodi asincroni

I comandi possono anche richiamare i metodi asincroni. Questa operazione viene eseguita usando le parole chiave `async` e `await` quando si specifica il metodo di `Execute`:

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

Ciò indica che il metodo `DownloadAsync` è un `Task` e deve essere atteso:

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

Il programma [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples) che contiene tutto il codice sorgente di questa serie di articoli usa un ViewModel per la relativa Home page. Questo ViewModel è una definizione di una classe breve con tre proprietà denominate `Type`, `Title`e `Description` che contengono il tipo di ogni pagina di esempio, un titolo e una breve descrizione. Il ViewModel definisce inoltre una proprietà statica denominata `All` che è una raccolta di tutte le pagine del programma:

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

Il file XAML per `MainPage` definisce una `ListBox` la cui proprietà `ItemsSource` è impostata su tale proprietà `All` e che contiene un `TextCell` per la visualizzazione delle proprietà `Title` e `Description` di ogni pagina:

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

Le pagine vengono visualizzate in un elenco scorrevole:

[![](data-bindings-to-mvvm-images/mainpage.png "Scrollable list of pages")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "Scrollable list of pages")

Il gestore nel file code-behind viene attivato quando l'utente seleziona un elemento. Il gestore imposta la proprietà `SelectedItem` del `ListBox` di nuovo su `null`, quindi crea un'istanza della pagina selezionata e vi passa:

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

## <a name="video"></a>Video

> [!VIDEO https://youtube.com/embed/DYRLcqG2BAY]

**Novell evolve 2016: MVVM semplificato con Novell. Forms e Prism**

## <a name="summary"></a>Riepilogo

XAML è un potente strumento per la definizione di interfacce utente nelle applicazioni xamarin. Forms, in particolare quando data binding e MVVM vengono usati. Il risultato è una rappresentazione pulita, elegante e potenzialmente ricco di strumenti di un'interfaccia utente con tutto il supporto in background nel codice.

## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introduzione con XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Nozioni fondamentali sull'associazione dati](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)

## <a name="related-videos"></a>Video correlati

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-MVVM-with-XAML-6-of-11/player]

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-Navigation-with-XAML-7-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
