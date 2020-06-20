---
title: Parte 5. Da data binding a MVVM
description: Il modello MVVM impone una separazione tra tre livelli software, ovvero l'interfaccia utente XAML, denominata vista; i dati sottostanti, detti modello; e un intermediario tra la vista e il modello, denominato ViewModel.
ms.prod: xamarin
ms.custom: video
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 047cf963394325e8f88759ffe9da7dcf2ca3ad12
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84127530"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>Parte 5. Da data binding a MVVM

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Il modello architetturale MVC (Model-View-ViewModel) è stato inventato tenendo presente XAML. Il modello impone una separazione tra tre livelli software, ovvero l'interfaccia utente XAML, denominata vista; i dati sottostanti, detti modello; e un intermediario tra la vista e il modello, denominato ViewModel. La visualizzazione e il ViewModel sono spesso connessi tramite associazioni dati definite nel file XAML. L'elemento BindingContext per la vista è in genere un'istanza dell'elemento ViewModel._

## <a name="a-simple-viewmodel"></a>Un ViewModel semplice

Come introduzione ai ViewModel, esaminiamo prima di tutto un programma senza uno.
In precedenza si è visto come definire una nuova dichiarazione dello spazio dei nomi XML per consentire a un file XAML di fare riferimento alle classi in altri assembly. Di seguito è riportato un programma che definisce una dichiarazione dello spazio dei nomi XML per lo `System` spazio dei nomi:

```csharp
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Il programma può utilizzare `x:Static` per ottenere la data e l'ora correnti dalla `DateTime.Now` proprietà statica e impostare tale `DateTime` valore `BindingContext` su su `StackLayout` :

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext`è una proprietà speciale: quando si imposta `BindingContext` su un elemento, quest'ultimo viene ereditato da tutti gli elementi figlio di tale elemento. Ciò significa che tutti gli elementi figlio di `StackLayout` hanno questo stesso `BindingContext` e possono contenere associazioni semplici alle proprietà di tale oggetto.

Nel programma **di data/ora** monouso, due elementi figlio contengono associazioni alle proprietà di tale `DateTime` valore, ma altri due elementi figlio contengono binding che sembrano mancare un percorso di associazione. Questo significa che il `DateTime` valore stesso viene usato per `StringFormat` :

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

Un file XAML può visualizzare un clock che Mostra sempre l'ora corrente, ma è necessario codice per il supporto. Quando si pensa a MVVM, il modello e ViewModel sono classi scritte interamente nel codice. La vista è spesso un file XAML che fa riferimento alle proprietà definite nel ViewModel tramite le associazioni dati.

Un modello appropriato è ignaro di ViewModel e un ViewModel appropriato è ignaro della visualizzazione. Tuttavia, spesso un programmatore ha i tipi di dati esposti dal ViewModel ai tipi di dati associati a specifiche interfacce utente. Se, ad esempio, un modello accede a un database contenente stringhe ASCII a 8 bit, il ViewModel deve eseguire la conversione tra tali stringhe in stringhe Unicode per consentire l'utilizzo esclusivo di Unicode nell'interfaccia utente.

In semplici esempi di MVVM, ad esempio quelli illustrati in questo articolo, spesso non esiste alcun modello e il modello include solo una visualizzazione e un ViewModel collegati con le associazioni dati.

Di seguito è riportato un ViewModel per un orologio con una sola proprietà denominata `DateTime` , che aggiorna la `DateTime` Proprietà ogni secondo:

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

I ViewModel implementano in genere l' `INotifyPropertyChanged` interfaccia, il che significa che la classe genera un `PropertyChanged` evento ogni volta che una delle relative proprietà viene modificata. Il meccanismo data binding in Xamarin.Forms associa un gestore a questo `PropertyChanged` evento, in modo che possa ricevere una notifica quando una proprietà viene modificata e mantiene la destinazione aggiornata con il nuovo valore.

Un orologio basato su questo ViewModel può essere semplice quanto segue:

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

Si noti come la `ClockViewModel` proprietà è impostata sull'oggetto `BindingContext` dei `Label` tag dell'elemento della proprietà using. In alternativa, è possibile creare un'istanza di `ClockViewModel` in una `Resources` raccolta e impostarla su `BindingContext` tramite un' `StaticResource` estensione di markup. In alternativa, il file code-behind può creare un'istanza dell'elemento ViewModel.

L' `Binding` estensione di markup sulla `Text` proprietà di `Label` Formatta la `DateTime` Proprietà. Ecco il video:

[![](data-bindings-to-mvvm-images/clock.png "View Displaying Date and Time via ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "View Displaying Date and Time via ViewModel")

È anche possibile accedere a singole proprietà della `DateTime` proprietà dell'elemento ViewModel separando le proprietà con i punti:

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>MVVM interattivo

MVVM viene spesso usato con le associazioni dati bidirezionali per una visualizzazione interattiva basata su un modello di dati sottostante.

Ecco una classe denominata `HslViewModel` che converte un `Color` valore in `Hue` valori, `Saturation` e `Luminosity` e viceversa:

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

Le modifiche apportate alle `Hue` proprietà, e comportano `Saturation` `Luminosity` la `Color` modifica della proprietà e la modifica di `Color` causa la modifica delle altre tre proprietà. Questo può sembrare un ciclo infinito, ad eccezione del fatto che la classe non richiama l' `PropertyChanged` evento, a meno che la proprietà non sia stata modificata. Questa operazione consente di terminare il ciclo di feedback altrimenti non controllabile.

Il file XAML seguente contiene un oggetto la `BoxView` cui `Color` proprietà è associata alla `Color` proprietà dell'elemento ViewModel e tre `Slider` e tre `Label` visualizzazioni associati alle `Hue` proprietà, `Saturation` e `Luminosity` :

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

L'associazione per ogni `Label` è l'impostazione predefinita `OneWay` . Deve solo visualizzare il valore. Tuttavia, l'associazione a ogni oggetto `Slider` è `TwoWay` . Ciò consente `Slider` di inizializzare l'oggetto dall'elemento ViewModel. Si noti che la `Color` proprietà è impostata su `Aqua` quando viene creata un'istanza dell'elemento ViewModel. Tuttavia, una modifica in `Slider` deve anche impostare un nuovo valore per la proprietà nell'elemento ViewModel, che quindi calcola un nuovo colore.

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "MVVM using Two-Way Data Bindings")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM using Two-Way Data Bindings")

## <a name="commanding-with-viewmodels"></a>Comandi con ViewModel

In molti casi, il modello MVVM è limitato alla manipolazione degli elementi di dati: oggetti dell'interfaccia utente nella visualizzazione di oggetti dati paralleli nell'elemento ViewModel.

Tuttavia, a volte la visualizzazione deve contenere pulsanti che attivano varie azioni nell'elemento ViewModel. Il ViewModel, tuttavia, non deve contenere `Clicked` gestori per i pulsanti perché il ViewModel viene associato a un paradigma dell'interfaccia utente specifico.

Per consentire la maggiore indipendenza dei ViewModel di oggetti specifici dell'interfaccia utente, ma che consentono comunque di chiamare i metodi all'interno di ViewModel, esiste un'interfaccia di *comando* . Questa interfaccia di comando è supportata dagli elementi seguenti in Xamarin.Forms :

- `Button`
- `MenuItem`
- `ToolbarItem`
- `SearchBar`
- `TextCell`(e di conseguenza `ImageCell` )
- `ListView`
- `TapGestureRecognizer`

Ad eccezione dell' `SearchBar` `ListView` elemento and, questi elementi definiscono due proprietà:

- `Command`di tipo`System.Windows.Input.ICommand`
- `CommandParameter`di tipo`Object`

`SearchBar`Definisce le `SearchCommand` `SearchCommandParameter` proprietà e, mentre `ListView` definisce una `RefreshCommand` proprietà di tipo `ICommand` .

L' `ICommand` interfaccia definisce due metodi e un evento:

- `void Execute(object arg)`
- `bool CanExecute(object arg)`
- `event EventHandler CanExecuteChanged`

Il ViewModel può definire proprietà di tipo `ICommand` . È quindi possibile associare queste proprietà alla `Command` proprietà di ogni `Button` elemento o di un altro elemento oppure a una vista personalizzata che implementa questa interfaccia. Facoltativamente, è possibile impostare la `CommandParameter` proprietà per identificare singoli `Button` oggetti (o altri elementi) associati a questa proprietà ViewModel. Internamente, `Button` chiama il `Execute` Metodo ogni volta che l'utente tocca l'oggetto `Button` , passando al `Execute` metodo `CommandParameter` .

Il `CanExecute` metodo e l' `CanExecuteChanged` evento vengono usati per i casi `Button` in cui un tap potrebbe non essere attualmente valido, nel qual caso `Button` deve disabilitare se stesso. `Button`Chiama `CanExecute` quando la `Command` proprietà viene impostata per la prima volta e ogni volta che `CanExecuteChanged` viene generato l'evento. Se `CanExecute` restituisce `false` , `Button` Disabilita se stesso e non genera `Execute` chiamate.

Per informazioni sull'aggiunta di comandi ai ViewModel, Xamarin.Forms definisce due classi che implementano `ICommand` : `Command` e, `Command<T>` dove `T` è il tipo degli argomenti per `Execute` e `CanExecute` . Queste due classi definiscono diversi costruttori, oltre a un `ChangeCanExecute` metodo che il ViewModel può chiamare per forzare l'esecuzione `Command` dell'evento da parte dell'oggetto `CanExecuteChanged` .

Di seguito è riportato un ViewModel per una semplice tastiera destinata all'immissione dei numeri di telefono. Si noti che `Execute` il `CanExecute` metodo e sono definiti come funzioni lambda direttamente nel costruttore:

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

Questo ViewModel presuppone che la `AddCharCommand` proprietà sia associata alla `Command` proprietà di diversi pulsanti (o qualsiasi altro elemento con un'interfaccia di comando), ognuno dei quali è identificato da `CommandParameter` . Questi pulsanti aggiungono caratteri a una `InputString` proprietà, che viene quindi formattata come numero di telefono per la `DisplayText` Proprietà.

Esiste anche una seconda proprietà di tipo `ICommand` denominata `DeleteCharCommand` . Questa operazione è associata a un pulsante di spaziatura interna, ma il pulsante deve essere disabilitato se non sono presenti caratteri da eliminare.

Il seguente tastierino non è visivamente sofisticato. Al contrario, il markup è stato ridotto a un minimo per dimostrare più chiaramente l'uso dell'interfaccia di comando:

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

La `Command` proprietà del primo oggetto `Button` visualizzato in questo markup è associata a `DeleteCharCommand` ; il resto è associato a `AddCharCommand` con un `CommandParameter` che corrisponde al carattere visualizzato sulla `Button` faccia. Ecco il programma in azione:

[![](data-bindings-to-mvvm-images/keypad.png "Calculator using MVVM and Commands")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "Calculator using MVVM and Commands")

### <a name="invoking-asynchronous-methods"></a>Richiamo di metodi asincroni

I comandi possono anche richiamare metodi asincroni. Questa operazione viene eseguita usando le `async` `await` parole chiave e quando si specifica il `Execute` Metodo:

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

Indica che il `DownloadAsync` metodo è un `Task` e deve essere atteso:

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

## <a name="implementing-a-navigation-menu"></a>Implementazione di un menu di navigazione

Il programma [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples) che contiene tutto il codice sorgente di questa serie di articoli usa un ViewModel per la relativa Home page. Questo ViewModel è una definizione di una classe breve con tre proprietà denominate `Type` , `Title` , e `Description` che contengono il tipo di ogni pagina di esempio, un titolo e una breve descrizione. Inoltre, il ViewModel definisce una proprietà statica denominata `All` che è una raccolta di tutte le pagine del programma:

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

Il file XAML per `MainPage` definisce un oggetto la `ListBox` cui `ItemsSource` proprietà è impostata su tale `All` proprietà e che contiene un oggetto `TextCell` per la visualizzazione delle `Title` `Description` proprietà e di ogni pagina:

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

Il gestore nel file code-behind viene attivato quando l'utente seleziona un elemento. Il gestore imposta la `SelectedItem` proprietà di `ListBox` nuovo su `null` e quindi crea un'istanza della pagina selezionata e vi passa:

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

**Novell evolve 2016: MVVM semplificato con Xamarin.Forms e Prism**

## <a name="summary"></a>Summary

XAML è uno strumento potente per la definizione di interfacce utente nelle Xamarin.Forms applicazioni, in particolare quando si utilizzano Data Binding e MVVM. Il risultato è una rappresentazione pulita, elegante e potenzialmente toolable di un'interfaccia utente con tutto il supporto in background nel codice.

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
