---
title: Parte 5. Da Data Binding a MVVM
description: Il modello MVVM consente di applicare una separazione tra tre livelli di software, ovvero l'interfaccia utente XAML, chiamato della visualizzazione. i dati sottostanti, chiamati del modello. e un intermediario tra la visualizzazione e il modello, chiamati ViewModel.
ms.prod: xamarin
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 11bca4bc74316f87ab7b329c897efcd4b768bc03
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657112"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>Parte 5. Da Data Binding a MVVM

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_Il modello architetturale Model-View-ViewModel (MVVM) è stato inventato presente con XAML. Il modello consente di applicare una separazione tra tre livelli di software, ovvero l'interfaccia utente XAML, chiamato della visualizzazione. i dati sottostanti, chiamati del modello. e un intermediario tra la visualizzazione e il modello, chiamati ViewModel. La visualizzazione e l'elemento ViewModel spesso sono connessi tramite le associazioni dati definite nel file XAML. L'oggetto BindingContext per la visualizzazione è in genere un'istanza del ViewModel._

## <a name="a-simple-viewmodel"></a>Un ViewModel semplice

Fornisce un'introduzione al ViewModel, prima di tutto esaminata in un programma senza uno.
In precedenza è stato illustrato come definire una nuova dichiarazione dello spazio dei nomi XML per consentire un file XAML da classi di riferimento in altri assembly. Di seguito è un programma che definisce una dichiarazione dello spazio dei nomi XML per il `System` dello spazio dei nomi:

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Il programma può usare `x:Static` per ottenere la data e ora correnti da statica `DateTime.Now` proprietà e set di `DateTime` valore per il `BindingContext` su un `StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext`è una proprietà molto speciale: Quando si imposta `BindingContext` su un elemento, questo viene ereditato da tutti gli elementi figlio di tale elemento. Ciò significa che tutti gli elementi figlio del `StackLayout` presentano questo stesso `BindingContext`, e possono contenere associazioni semplici a proprietà di tale oggetto.

Nel **DateTime One-Shot** il programma, due degli elementi figlio contiene le associazioni alle proprietà di tale `DateTime` valore, ma due altri elementi figlio contenga le associazioni che sembrano che manchi un percorso di associazione. Ciò significa che il `DateTime` stesso valore viene utilizzato per il `StringFormat`:

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

Naturalmente, il problema è che la data e ora sono set di una volta quando la pagina viene innanzitutto compilata e non cambiano mai:

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "Visualizzazione con data e ora")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "visualizzazione con data e ora")

Un file XAML può visualizzare un orologio che viene mostrato sempre l'ora corrente, ma è necessario un codice contribuire all'implementazione. Quando pensare in termini di MVVM, il modello e ViewModel sono classi scritte interamente in codice. La visualizzazione è spesso un file XAML che fa riferimento a proprietà definite nel ViewModel tramite le associazioni dati.

Un modello appropriato è assolutamente inconsapevole dei ViewModel e un elemento ViewModel corretto è che non riconoscono la della visualizzazione. Tuttavia, molto spesso un programmatore consente di adattare i tipi di dati esposti dal ViewModel per i tipi di dati associati a interfacce utente particolare. Ad esempio, se un modello accede a un database che contiene le stringhe ASCII di caratteri a 8 bit, l'elemento ViewModel necessario per la conversione tra le stringhe per le stringhe Unicode per supportare l'uso esclusivo di Unicode nell'interfaccia utente.

In semplici esempi del modello MVVM (ad esempio quelli illustrati di seguito), spesso non esiste alcun modello affatto e il modello comporta semplicemente una vista e ViewModel collegate con data binding.

Di seguito è riportato un ViewModel per un orologio con una singola proprietà denominata `DateTime`, ma gli aggiornamenti che `DateTime` proprietà ogni secondo:

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

ViewModel implementa in genere il `INotifyPropertyChanged` interfaccia, il che significa che la classe genera un `PropertyChanged` evento ogni volta che una delle sue proprietà cambia. Il meccanismo di data binding in xamarin. Forms associa un gestore per questo `PropertyChanged` eventi in modo che può ricevere notifiche quando viene modificata una proprietà e mantenere la destinazione aggiornata con il nuovo valore.

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

Si noti che il `ClockViewModel` è impostato sul `BindingContext` del `Label` usando i tag di elemento di proprietà. In alternativa, è possibile creare un'istanza di `ClockViewModel` in un `Resources` raccolta e impostarlo sul `BindingContext` tramite un `StaticResource` estensione di markup. In alternativa, il file code-behind può creare un'istanza di ViewModel.

Il `Binding` estensione di markup nel `Text` proprietà delle `Label` formati il `DateTime` proprietà. Ecco la visualizzazione:

[![](data-bindings-to-mvvm-images/clock.png "Visualizzazione con data e ora tramite ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "visualizzazione con data e ora tramite ViewModel")

È anche possibile accedere alle singole proprietà del `DateTime` proprietà dell'elemento ViewModel, separando le proprietà con un punto:

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>MVVM interattiva

MVVM è abbastanza frequente con le associazioni di dati a due vie per una vista interattiva basata su un modello di dati sottostante.

Di seguito è una classe denominata `HslViewModel` che converte un `Color` valore `Hue`, `Saturation`, e `Luminosity` valori e viceversa:

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

Diventa il `Hue`, `Saturation`, e `Luminosity` proprietà causa il `Color` proprietà da modificare e le modifiche a `Color` fa sì che le tre proprietà modificare. Ciò può sembrare un ciclo infinito, ad eccezione del fatto che la classe non richiama il `PropertyChanged` eventi, a meno che la proprietà viene effettivamente modificata. In questo modo si impedisce al ciclo di commenti e suggerimenti in caso contrario, non controllabile.

Il file XAML seguente contiene un `BoxView` cui `Color` proprietà è associata al `Color` proprietà dell'elemento ViewModel e tre `Slider` e tre `Label` viste associate ai `Hue`, `Saturation`e `Luminosity` delle proprietà:

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

L'associazione su ciascuna `Label` il valore predefinito è `OneWay`. Deve solo visualizzare il valore. Ma l'associazione su ciascuna `Slider` è `TwoWay`. In questo modo il `Slider` da inizializzare da ViewModel. Si noti che il `Color` è impostata su `Aqua` quando viene creata un'istanza l'elemento ViewModel. Una modifica, ma il `Slider` deve inoltre impostare un nuovo valore della proprietà nel ViewModel, che quindi viene calcolato un nuovo colore.

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "MVVM usando i Data Binding bidirezionale")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM usando i Data Binding bidirezionale")

## <a name="commanding-with-viewmodels"></a>Esecuzione dei comandi con ViewModel

In molti casi, il modello MVVM è limitato alla manipolazione degli elementi di dati: Oggetti dell'interfaccia utente nella visualizzazione di oggetti dati paralleli nell'elemento ViewModel.

Tuttavia, in alcuni casi la vista deve contenere i pulsanti per attivano azioni diverse in ViewModel. Ma non deve contenere l'elemento ViewModel `Clicked` gestori per i pulsanti perché che verrebbe associare i ViewModel a un paradigma dell'interfaccia utente specifico.

Per consentire ViewModel essere più indipendente di oggetti dell'interfaccia utente specifico, ma consentire comunque i metodi da chiamare all'interno di ViewModel, un *comando* interfaccia esistente. Questa interfaccia di comando è supportata dai seguenti elementi in xamarin. Forms:

-  `Button`
-  `MenuItem`
-  `ToolbarItem`
-  `SearchBar`
-  `TextCell` (e pertanto anche `ImageCell`)
-  `ListView`
-  `TapGestureRecognizer`

Fatta eccezione per il `SearchBar` e `ListView` elemento, questi elementi definiscono due proprietà:

-  `Command` di tipo  `System.Windows.Input.ICommand`
-  `CommandParameter` di tipo  `Object`

Il `SearchBar` definisce `SearchCommand` e `SearchCommandParameter` delle proprietà, mentre il `ListView` definisce una `RefreshCommand` vlastnosti typu `ICommand`.

Il `ICommand` interfaccia definisce un evento e due metodi:

-  `void Execute(object arg)`
-  `bool CanExecute(object arg)`
-  `event EventHandler CanExecuteChanged`

L'elemento ViewModel può definire le proprietà di tipo `ICommand`. È quindi possibile associare queste proprietà per il `Command` proprietà della ognuno `Button` o altro elemento, o forse una visualizzazione personalizzata che implementa questa interfaccia. È anche possibile impostare il `CommandParameter` proprietà per identificare i singoli `Button` oggetti (o altri elementi) sono associati a questa proprietà ViewModel. Internamente, il `Button` chiamate il `Execute` metodo ogni volta che l'utente tocca il `Button`, passando al `Execute` metodo relativo `CommandParameter`.

Il `CanExecute` (metodo) e `CanExecuteChanged` evento vengono utilizzati per i casi in cui un `Button` tap potrebbe essere attualmente non è valido, nel qual caso il `Button` deve disabilitarsi. Il `Button` chiamate `CanExecute` quando il `Command` prima di tutto viene impostata e ogni volta che il `CanExecuteChanged` evento. Se `CanExecute` restituisce `false`, il `Button` disabilitato automaticamente e non genera `Execute` chiamate.

Per informazioni sull'aggiunta dei comandi per i ViewModel, xamarin. Forms definisce due classi che implementano `ICommand`: `Command` e `Command<T>` in cui `T` è il tipo degli argomenti `Execute` e `CanExecute`. Queste due classi definiscono diversi costruttori oltre a una `ChangeCanExecute` metodo che l'elemento ViewModel può chiamare per forzare il `Command` oggetto attivano la `CanExecuteChanged` evento.

Ecco un ViewModel per un tastierino semplice che è previsto per l'immissione di numeri di telefono. Si noti che il `Execute` e `CanExecute` metodo sono definite come espressione lambda funzioni direttamente nel costruttore:

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

Tale elemento ViewModel si presuppone che il `AddCharCommand` è associata la proprietà per il `Command` proprietà di vari pulsanti (o qualsiasi altro elemento che dispone di un'interfaccia di comando), ognuno dei quali è identificato dal `CommandParameter`. Questi pulsanti aggiungere caratteri a un `InputString` proprietà, che verrà formattato come un numero di telefono per il `DisplayText` proprietà.

È anche una seconda proprietà typu `ICommand` denominato `DeleteCharCommand`. Questo è associato a un pulsante Indietro-spaziatura, ma il pulsante deve essere disabilitato se non sono presenti caratteri da eliminare.

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

Il `Command` proprietà del primo `Button` che viene visualizzato in questo markup è associato ai `DeleteCharCommand`; gli altri sono associati al `AddCharCommand` con un `CommandParameter` vale a dire lo stesso come il carattere che viene visualizzato nel `Button` viso. Ecco il programma in azione:

[![](data-bindings-to-mvvm-images/keypad.png "Calcolatrice utilizzando MVVM e i comandi")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "calcolatrice utilizzando MVVM e i comandi")

### <a name="invoking-asynchronous-methods"></a>Richiamo di metodi asincroni

I comandi possono anche richiamare i metodi asincroni. Questo risultato viene ottenuto usando il `async` e `await` parole chiave quando si specifica il `Execute` metodo:

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
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

Il [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples) programma che contiene tutto il codice sorgente di questa serie di articoli viene utilizzato un ViewModel per la relativa home page. Tale elemento ViewModel è una definizione di una classe brevi con tre proprietà denominate `Type`, `Title`, e `Description` che contengono il tipo di ognuna delle pagine di esempio, un titolo e una breve descrizione. Inoltre, l'elemento ViewModel definisce una proprietà statica denominata `All` vale a dire una raccolta di tutte le pagine del programma:

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

Il file XAML per `MainPage` definisce un `ListBox` cui `ItemsSource` è impostata su cui `All` delle proprietà e che contiene un `TextCell` per la visualizzazione di `Title` e `Description` le proprietà di ogni pagina:

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

[![](data-bindings-to-mvvm-images/mainpage.png "Elenco scorrevole delle pagine")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "elenco scorrevole delle pagine")

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

## <a name="video"></a>Video

> [!VIDEO https://youtube.com/embed/DYRLcqG2BAY]

**Novell evolve 2016: MVVM semplificato con Novell. Forms e Prism**

## <a name="summary"></a>Riepilogo

XAML è un potente strumento per la definizione di interfacce utente nelle applicazioni xamarin. Forms, in particolare quando data binding e MVVM vengono usati. Il risultato è una rappresentazione pulita, elegante e potenzialmente ricco di strumenti di un'interfaccia utente con tutto il supporto in background nel codice.


## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introduzione a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Nozioni di base sul data binding](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
