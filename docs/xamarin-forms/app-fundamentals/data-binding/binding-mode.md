---
title: "Modalità di associazione"
description: Controllare il flusso di informazioni tra origine e destinazione
ms.topic: article
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 887dc3cf710fb75d05d02af179bc218c15d31f97
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="binding-mode"></a>Modalità di associazione

Nel [articolo precedente](basic-bindings.md), **alternativa codice associazione** e **associazione XAML alternativa** pagine in primo piano una `Label` con relativo `Scale` proprietà associare il `Value` proprietà di un `Slider`. Poiché il `Slider` valore iniziale è 0, questo ha causato il `Scale` proprietà del `Label` sia impostato su 0 anziché 1 e il `Label` è scomparso.

Nel [ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) esempio, il **associazione inversa** pagina è simile ai programmi nell'articolo precedente, ad eccezione del fatto che l'associazione di dati viene definita nel `Slider` anziché sul `Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.ReverseBindingPage"
             Title="Reverse Binding">
    <StackLayout Padding="10, 0">

        <Label x:Name="label" 
               Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                VerticalOptions="CenterAndExpand"
                Value="{Binding Source={x:Reference label},
                                Path=Opacity}" />
    </StackLayout>
</ContentPage>
```

Inizialmente potrebbe sembrare con le versioni precedenti: ora il `Label` è l'origine di associazione dati e `Slider` è la destinazione. I riferimenti di associazione di `Opacity` proprietà del `Label`, che presenta un valore predefinito di 1.

Come prevedibile, la `Slider` viene inizializzato sul valore 1 da iniziale `Opacity` valore `Label`. Come illustrato nella schermata iOS a sinistra:

[![Annullare l'associazione](binding-mode-images/reversebinding-small.png "inversa associazione")](binding-mode-images/reversebinding-large.png#lightbox "associazione inversa")

Ma potrebbe essere sorprendente che il `Slider` continua a funzionare, come illustrano le schermate di Android e UWP. Può sembrare un passo per indicare che l'associazione dati funziona meglio se il `Slider` è la destinazione del binding anziché `Label` perché l'inizializzazione opera come prevedibile.

La differenza tra il **associazione inversa** esempio e degli esempi precedenti comporta il *modalità di associazione*.

## <a name="the-default-binding-mode"></a>La modalità di associazione predefinita

La modalità di associazione è specificata con un membro con il [ `BindingMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingMode/) enumerazione: 

- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.Default/) 
- [`TwoWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/) &ndash; i dati vengono inseriti in entrambe le direzioni tra origine e destinazione
- [`OneWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWay/) &ndash; i dati vengono inseriti dall'origine alla destinazione
- [`OneWayToSource`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWayToSource/) &ndash; i dati vengono inseriti dalla destinazione all'origine

Tutte le proprietà associabili ha un valore predefinito modalità di associazione che viene impostata quando la proprietà associabile viene creata e disponibile dal [ `DefaultBindingMode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableProperty.DefaultBindingMode/) proprietà del `BindableProperty` oggetto. Questa modalità di associazione predefinita indica la modalità attiva quando la proprietà è una destinazione di associazione dati.

La modalità di associazione predefinita per la maggior parte delle proprietà, ad esempio `Rotation`, `Scale`, e `Opacity` è `OneWay`. Quando queste proprietà sono destinazioni di associazione di dati, la proprietà di destinazione è impostata dall'origine.

Tuttavia, la modalità di associazione predefinita per il `Value` proprietà di `Slider` è `TwoWay`. Ciò significa che quando il `Value` proprietà è una destinazione di associazione dati, quindi la destinazione viene impostata dall'origine (come di consueto) ma l'origine viene anche impostata dalla destinazione. Questo è ciò che consente di `Slider` l'impostazione da iniziale `Opacity` valore.

Questa associazione bidirezionale potrebbe sembrare per creare un ciclo infinito, ma che non si verifica. Proprietà associabili non segnalano una modifica della proprietà a meno che non viene modificata la proprietà. In questo modo si impedisce un ciclo infinito.

### <a name="two-way-bindings"></a>Associazioni bidirezionali

Più proprietà associabili hanno una modalità di associazione predefinito di `OneWay` ma le proprietà seguenti hanno una modalità di associazione predefinito di `TwoWay`:

- `Date` proprietà di `DatePicker`
- `Text` proprietà di `Editor`, `Entry`, `SearchBar`, e `EntryCell`
- `IsRefreshing` proprietà di `ListView`
- `SelectedItem` proprietà di `MultiPage`
- `SelectedIndex` e `SelectedItem` le proprietà di `Picker`
- `Value` proprietà di `Slider` e `Stepper`
- `IsToggled` proprietà di `Switch` 
- `On` proprietà di `SwitchCell`
- `Time` proprietà di `TimePicker`

Queste particolari proprietà sono definite come `TwoWay` per un motivo molto efficaci per: 

Quando le associazioni di dati vengono utilizzate con l'architettura dell'applicazione Model-View-ViewModel (MVVM), la classe ViewModel è l'origine di associazione di dati e la visualizzazione, che è costituito, ad esempio viste `Slider`, rappresentano le destinazioni di associazione dati. Associazioni MVVM simile di **associazione inversa** esempio più associazioni negli esempi precedenti. È molto probabile che si desidera che ogni visualizzazione della pagina per essere inizializzato con il valore della proprietà corrispondente nel ViewModel, ma le modifiche nella vista devono influenzare la proprietà ViewModel.

Le proprietà con modalità di associazione predefinito di `TwoWay` sono quelle più probabile essere utilizzata negli scenari MVVM.

### <a name="one-way-to-source-bindings"></a>Associazioni unico-bidirezionali all'origine

Le proprietà associabili di sola lettura hanno una modalità di associazione predefinito di `OneWayToSource`. È solo una proprietà associabile di lettura/scrittura che dispone di una modalità di associazione predefinito di `OneWayToSource`:

- `SelectedItem` proprietà di `ListView`

La logica alla base che è un'associazione di `SelectedItem` dovrebbe restituire proprietà impostando l'origine di associazione. Un esempio più avanti in questo articolo esegue l'override di questo comportamento.

## <a name="viewmodels-and-property-change-notifications"></a>ViewModel e notifiche di modifica delle proprietà

Il **selettore di colore semplice** pagina illustra l'uso di un semplice ViewModel. Data Binding consentono all'utente di selezionare un colore utilizzando tre `Slider` elementi per la tonalità, saturazione e luminosità.

L'elemento ViewModel è l'origine di associazione dati. L'elemento ViewModel *non* definire proprietà associabile, ma è implementare un meccanismo di notifica che consente all'infrastruttura di associazione ricevere una notifica quando il valore di una proprietà cambia. Questo meccanismo di notifica è il [ `INotifyPropertyChanged` ](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) interfaccia che definisce una singola proprietà denominata [ `PropertyChanged` ](https://developer.xamarin.com/api/event/System.ComponentModel.INotifyPropertyChanged.PropertyChanged/). Una classe che implementa questa interfaccia in genere viene generato l'evento quando una delle relative proprietà pubbliche cambia valore. L'evento non è necessario essere generato se la proprietà non cambia mai. (Il `INotifyPropertyChanged` interfaccia viene inoltre implementata dalla `BindableObject` e `PropertyChanged` evento viene generato ogni volta che una proprietà associabile cambia valore.)

Il `HslColorViewModel` classe definisce cinque proprietà: il `Hue`, `Saturation`, `Luminosity`, e `Color` sono correlati tra loro proprietà. Quando uno dei tre colori componenti modifica il valore, il `Color` proprietà viene ricalcolata e `PropertyChanged` gli eventi vengono attivati per tutte le quattro proprietà:

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name; 

    public event PropertyChangedEventHandler PropertyChanged;

    public double Hue
    {
        set
        {
            if (color.Hue != value)
            {
                Color = Color.FromHsla(value, color.Saturation, color.Luminosity);
            }
        }
        get 
        {
            return color.Hue;
        }
    }

    public double Saturation
    {
        set
        {
            if (color.Saturation != value)
            {
                Color = Color.FromHsla(color.Hue, value, color.Luminosity);
            }
        }
        get
        {
            return color.Saturation;
        }
    }

    public double Luminosity
    {
        set
        {
            if (color.Luminosity != value)
            {
                Color = Color.FromHsla(color.Hue, color.Saturation, value);
            }
        }
        get
        {
            return color.Luminosity;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Saturation"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Luminosity"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

Quando il `Color` le modifiche alle proprietà, il metodo statico `GetNearestColorName` metodo il `NamedColor` classe (inclusi anche nel **DataBindingDemos** soluzione) Ottiene il colore più simile denominato e imposta il `Name` proprietà. Questo `Name` proprietà ha una privata `set` funzione di accesso, pertanto non può essere impostata dall'esterno della classe.

Quando un elemento ViewModel è impostato come origine di associazione, l'infrastruttura di associazione associa un gestore per il `PropertyChanged` evento. In questo modo, l'associazione può ricevere una notifica di modifiche alle proprietà e quindi è possibile impostare le proprietà di destinazione da valori modificati.

Il **semplice selettore del colore** file XAML crea un'istanza di `HslColorViewModel` nel dizionario risorse e inizializza la pagina di `Color` proprietà. Il `BindingContext` proprietà del `Grid` è impostata su un `StaticResource` estensione per fare riferimento a tale risorsa di binding:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SimpleColorSelectorPage">

    <ContentPage.Resources>
        <ResourceDictionary>
            <local:HslColorViewModel x:Key="viewModel" 
                                     Color="MediumTurquoise" />

            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
        
    <Grid BindingContext="{StaticResource viewModel}">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <BoxView Color="{Binding Color}"
                 Grid.Row="0" />

        <StackLayout Grid.Row="1"
                     Margin="10, 0">

            <Label Text="{Binding Name}"
                   HorizontalTextAlignment="Center" />

            <Slider Value="{Binding Hue}" />
    
            <Slider Value="{Binding Saturation}" />

            <Slider Value="{Binding Luminosity}" />
        </StackLayout>
    </Grid>
</ContentPage>
```

Il `BoxView`, `Label`e tre `Slider` viste ereditano il contesto di associazione dal `Grid`. Queste viste sono tutte le destinazioni di associazione che fanno riferimento a proprietà di origine nel ViewModel. Per il `Color` proprietà del `BoxView`e `Text` proprietà del `Label`, le associazioni dati sono `OneWay`: le proprietà nella vista vengono impostate dalla proprietà di ViewModel.

Il `Value` proprietà del `Slider`, tuttavia, è `TwoWay`. In questo modo ogni `Slider` impostare l'elemento ViewModel e anche da ViewModel impostare da ogni `Slider`. 

Quando il programma viene eseguito prima, la `BoxView`, `Label`e tre `Slider` elementi sono tutti impostati dal ViewModel di base iniziale `Color` impostata quando l'elemento ViewModel è stata creata un'istanza. Come illustrato nella schermata iOS a sinistra:

[![Selettore colori semplice](binding-mode-images/simplecolorselector-small.png "selettore colori semplice")](binding-mode-images/simplecolorselector-large.png#lightbox "selettore colori semplice")

Come si modificano i dispositivi di scorrimento, il `BoxView` e `Label` vengono aggiornati di conseguenza, come illustrato per le schermate di Android e UWP.

Creazione di un'istanza ViewModel nel dizionario risorse è un approccio comune. È anche possibile creare un'istanza di ViewModel all'interno dei tag di elemento di proprietà per il `BindingContext` proprietà. Nel **selettore di colore semplice** XAML file, provare a rimuovere il `HslColorViewModel` dal dizionario risorse e impostarlo sul `BindingContext` proprietà del `Grid` come segue:

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>
        
    ···

</Grid>
```

Il contesto di associazione può essere impostato in modi diversi. In alcuni casi, il file code-behind crea un'istanza di ViewModel e lo imposta sul `BindingContext` proprietà della pagina. Questi sono tutti validi approcci.

## <a name="overriding-the-binding-mode"></a>La modalità di associazione viene sottoposto a override

Se la modalità di associazione predefinito nella proprietà di destinazione non è adatta per una determinata associazione dati, è possibile eseguirne l'override impostando il [ `Mode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.Mode/) proprietà di `Binding` (o [ `Mode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Mode/) proprietà del `Binding` estensione di markup) a uno dei membri del `BindingMode` enumerazione.

Tuttavia, l'impostazione di `Mode` proprietà `TwoWay` non sempre funziona come previsto. Ad esempio, provare a modificare il **associazione XAML alternativa** file XAML da includere `TwoWay` nella definizione di associazione:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

È possibile prevedere che il `Slider` verrebbe inizializzata sul valore iniziale di `Scale` proprietà, ovvero 1, ma che non si verifica. Quando un `TwoWay` viene inizializzata l'associazione, la destinazione viene impostata dall'origine prima di tutto, vale a dire che il `Scale` è impostata sul `Slider` il valore predefinito di 0. Quando il `TwoWay` binding è impostato sul `Slider`, quindi il `Slider` è inizialmente impostata dall'origine.

È possibile impostare la modalità di associazione `OneWayToSource` nel **associazione XAML alternativa** esempio:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

Ora il `Slider` viene inizializzato su 1 (il valore predefinito di `Scale`) ma la modifica di `Slider` non influisce sul `Scale` proprietà, pertanto non è molto utile.

Un'applicazione molto utile di override la modalità di associazione predefinito con `TwoWay` implica il `SelectedItem` proprietà `ListView`. La modalità di associazione predefinita è `OneWayToSource`. Quando un data binding è impostato sul `SelectedItem` proprietà per fare riferimento a una proprietà di origine in un elemento ViewModel, tale proprietà di origine viene impostato dal `ListView` selezione. Tuttavia, in alcuni casi, è anche possibile il `ListView` di essere inizializzata dal ViewModel.

Il **le impostazioni di esempio** pagina viene illustrata questa tecnica. Questa pagina rappresenta un'implementazione semplice delle impostazioni dell'applicazione, molto spesso definite in un elemento ViewModel, ad esempio questo `SampleSettingsViewModel` file:

```csharp
public class SampleSettingsViewModel : INotifyPropertyChanged
{
    string name;
    DateTime birthDate;
    bool codesInCSharp;
    double numberOfCopies;
    NamedColor backgroundNamedColor;

    public event PropertyChangedEventHandler PropertyChanged;

    public SampleSettingsViewModel(IDictionary<string, object> dictionary)
    {
        Name = GetDictionaryEntry<string>(dictionary, "Name");
        BirthDate = GetDictionaryEntry(dictionary, "BirthDate", new DateTime(1980, 1, 1));
        CodesInCSharp = GetDictionaryEntry<bool>(dictionary, "CodesInCSharp");
        NumberOfCopies = GetDictionaryEntry(dictionary, "NumberOfCopies", 1.0);
        BackgroundNamedColor = NamedColor.Find(GetDictionaryEntry(dictionary, "BackgroundNamedColor", "White"));
    }

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public DateTime BirthDate
    {
        set { SetProperty(ref birthDate, value); }
        get { return birthDate; }
    }

    public bool CodesInCSharp
    {
        set { SetProperty(ref codesInCSharp, value); }
        get { return codesInCSharp; }
    }

    public double NumberOfCopies
    {
        set { SetProperty(ref numberOfCopies, value); }
        get { return numberOfCopies; }
    }

    public NamedColor BackgroundNamedColor
    {
        set
        {
            if (SetProperty(ref backgroundNamedColor, value))
            {
                OnPropertyChanged("BackgroundColor");
            }
        }
        get { return backgroundNamedColor; }
    }

    public Color BackgroundColor
    {
        get { return BackgroundNamedColor?.Color ?? Color.White; }
    }

    public void SaveState(IDictionary<string, object> dictionary)
    {
        dictionary["Name"] = Name;
        dictionary["BirthDate"] = BirthDate;
        dictionary["CodesInCSharp"] = CodesInCSharp;
        dictionary["NumberOfCopies"] = NumberOfCopies;
        dictionary["BackgroundNamedColor"] = BackgroundNamedColor.Name;
    }

    T GetDictionaryEntry<T>(IDictionary<string, object> dictionary, string key, T defaultValue = default(T))
    {
        return dictionary.ContainsKey(key) ? (T)dictionary[key] : defaultValue;
    }

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

Ogni impostazione dell'applicazione è una proprietà che viene salvata il dizionario delle proprietà di xamarin. Forms in un metodo denominato `SaveState` e caricati da tale dizionario nel costruttore. Nella parte inferiore della classe sono due metodi che consentono di snellire ViewModel renderli meno soggetto a errori. Il `OnPropertyChanged` metodo nella parte inferiore è un parametro facoltativo che è stato impostato la proprietà del chiamante. Ciò consente di evitare gli errori di ortografia quando si specifica il nome della proprietà come stringa. 

Il `SetProperty` metodo nella classe esegue ulteriormente: il valore impostato per la proprietà con il valore archiviato come campo di confronto e chiama solo `OnPropertyChanged` quando due valori non sono uguali. 

Il `SampleSettingsViewModel` classe definisce due proprietà per il colore di sfondo: il `BackgroundNamedColor` proprietà è di tipo `NamedColor`, una classe anche incluso nel **DataBindingDemos** soluzione. Il `BackgroundColor` proprietà è di tipo `Color`e viene ottenuto dal `Color` proprietà del `NamedColor` oggetto.

Il `NamedColor` classe utilizza la reflection .NET per enumerare tutti i campi pubblici statici in di xamarin. Forms `Color` struttura e per archiviarli con i relativi nomi in una raccolta è accessibile da statico `All` proprietà:

```csharp
public class NamedColor : IEquatable<NamedColor>, IComparable<NamedColor>
{
    // Instance members
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    public bool Equals(NamedColor other)
    {
        return Name.Equals(other.Name);
    }

    public int CompareTo(NamedColor other)
    {
        return Name.CompareTo(other.Name);
    }

    // Static members
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof(Color))
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
        all.Sort();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }

    public static NamedColor Find(string name)
    {
        return ((List<NamedColor>)All).Find(nc => nc.Name == name);
    }

    public static string GetNearestColorName(Color color)
    {
        double shortestDistance = 1000;
        NamedColor closestColor = null;

        foreach (NamedColor namedColor in NamedColor.All)
        {
            double distance = Math.Sqrt(Math.Pow(color.R - namedColor.Color.R, 2) +
                                        Math.Pow(color.G - namedColor.Color.G, 2) +
                                        Math.Pow(color.B - namedColor.Color.B, 2));

            if (distance < shortestDistance)
            {
                shortestDistance = distance;
                closestColor = namedColor;
            }
        }
        return closestColor.Name;
    }
}
```

Il `App` classe il **DataBindingDemos** progetto definisce una proprietà denominata `Settings` di tipo `SampleSettingsViewModel`. Questa proprietà viene inizializzata quando il `App` viene creata un'istanza di classe e `SaveState` metodo viene chiamato quando il `OnSleep` metodo viene chiamato:

```csharp
public partial class App : Application
{
    public App()
    {
        InitializeComponent();

        Settings = new SampleSettingsViewModel(Current.Properties);

        MainPage = new NavigationPage(new MainPage());
    }

    public SampleSettingsViewModel Settings { private set; get; }

    protected override void OnStart()
    {
        // Handle when your app starts
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Settings.SaveState(Current.Properties);
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
    }
}
```

Per ulteriori informazioni sui metodi del ciclo di vita dell'applicazione, vedere l'articolo [ **ciclo di vita App**](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

Quasi tutto il resto è gestita nel **SampleSettingsPage.xaml** file. Il `BindingContext` della pagina viene impostato utilizzando un `Binding` estensione di markup: l'origine di associazione è statica `Application.Current` proprietà, ovvero l'istanza del `App` classe nel progetto e `Path` è impostato sul `Settings` proprietà, ovvero il `SampleSettingsViewModel` oggetto:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SampleSettingsPage"
             Title="Sample Settings"
             BindingContext="{Binding Source={x:Static Application.Current},
                                      Path=Settings}">

    <StackLayout BackgroundColor="{Binding BackgroundColor}"
                 Padding="10"
                 Spacing="10">

        <StackLayout Orientation="Horizontal">
            <Label Text="Name: "
                   VerticalOptions="Center" />

            <Entry Text="{Binding Name}"
                   Placeholder="your name"
                   HorizontalOptions="FillAndExpand"
                   VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Birth Date: "
                   VerticalOptions="Center" />

            <DatePicker Date="{Binding BirthDate}"
                        HorizontalOptions="FillAndExpand"
                        VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Do you code in C#? "
                   VerticalOptions="Center" />

            <Switch IsToggled="{Binding CodesInCSharp}"
                    VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Number of Copies: "
                   VerticalOptions="Center" />

            <Stepper Value="{Binding NumberOfCopies}"
                     VerticalOptions="Center" />

            <Label Text="{Binding NumberOfCopies}"
                   VerticalOptions="Center" />
        </StackLayout>

        <Label Text="Background Color:" />

        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
                  VerticalOptions="FillAndExpand"
                  RowHeight="40">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     HeightRequest="32"
                                     WidthRequest="32"
                                     VerticalOptions="Center" />

                            <Label Text="{Binding FriendlyName}"
                                   FontSize="24"
                                   VerticalOptions="Center" />
                        </StackLayout>                        
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Tutti gli elementi figlio della pagina ereditano il contesto di associazione. La maggior parte delle altre associazioni in questa pagina sono proprietà `SampleSettingsViewModel`. Il `BackgroundColor` proprietà viene utilizzata per impostare il `BackgroundColor` proprietà del `StackLayout`e `Entry`, `DatePicker`, `Switch`, e `Stepper` proprietà sono associate ad altre proprietà nel ViewModel.

Il `ItemsSource` proprietà del `ListView` è impostato su statica `NamedColor.All` proprietà. Questo viene compilato il `ListView` con tutte le `NamedColor` istanze. Per ogni elemento di `ListView`, il contesto di associazione per l'elemento è impostato su un `NamedColor` oggetto. Il `BoxView` e `Label` nel `ViewCell` sono associati alle proprietà in `NamedColor`.

Il `SelectedItem` proprietà del `ListView` è di tipo `NamedColor`e viene associato al `BackgroundNamedColor` proprietà di `SampleSettingsViewModel`:

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

La modalità di associazione predefinita per `SelectedItem` è `OneWayToSource`, che imposta la proprietà ViewModel dall'elemento selezionato. Il `TwoWay` modalità consente di `SelectedItem` di essere inizializzata dal ViewModel. 

Tuttavia, quando il `SelectedItem` è impostato in questo modo, il `ListView` non lo scorrimento automatico per visualizzare l'elemento selezionato. Un codice nel file code-behind è necessario:

```csharp
public partial class SampleSettingsPage : ContentPage
{
    public SampleSettingsPage()
    {
        InitializeComponent();

        if (colorListView.SelectedItem != null)
        {
            colorListView.ScrollTo(colorListView.SelectedItem, 
                                   ScrollToPosition.MakeVisible, 
                                   false);
        }
    }
}
``` 

La schermata di iOS a sinistra mostra il programma alla prima esecuzione. Il costruttore in `SampleSettingsViewModel` Inizializza il colore di sfondo sul bianco e che è l'elemento selezionato nel `ListView`:

[![Le impostazioni di esempio](binding-mode-images/samplesettings-small.png "le impostazioni di esempio")](binding-mode-images/samplesettings-large.png#lightbox "le impostazioni di esempio")

Due schermate mostrano le impostazioni modificate. Quando si provano a questa pagina, ricordare di inserire il programma allo stato di sospensione o interruzione del dispositivo o un emulatore in esecuzione. Terminare il programma dal debugger di Visual Studio non comporterà la `OnSleep` esegue l'override nel `App` classe da chiamare.

L'articolo successivo si noterà come specificare [ **la formattazione della stringa** ](string-formatting.md) delle associazioni di dati che sono impostate sul `Text` proprietà `Label`.


## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capitolo di associazione di dati dalla Rubrica di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
