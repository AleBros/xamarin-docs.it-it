---
title: Modalità di binding di Xamarin.Forms
description: Questo articolo illustra come controllare il flusso di informazioni tra origine e destinazione tramite una modalità di binding, che viene specificata con un membro dell'enumerazione BindingMode. Tutte le proprietà con binding presentano una modalità di binding predefinita, che indica la modalità attiva quando questa proprietà è una destinazione per il data binding.
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/01/2018
ms.openlocfilehash: acfa7bc953906654567d361b93ec1ccff22c1f1b
ms.sourcegitcommit: 191f1f3b13a14e2afadcb95126c5f653722f126f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545647"
---
# <a name="xamarinforms-binding-mode"></a>Modalità di binding di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

Nell'[articolo precedente](basic-bindings.md) le pagine **Alternative Code Binding** (Binding di codice alternativo) e **Alternative XAML Binding** (Binding XAML alternativo) presentavano un elemento `Label` con la proprietà `Scale` associata alla proprietà `Value` di un elemento `Slider`. Poiché il valore iniziale di `Slider` è 0, la proprietà `Scale` di `Label` è stata impostata su 0 anziché su 1 e `Label` è scomparso.

Nell'esempio [**DataBindingDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos), la pagina **Reverse Binding** (Binding inverso) è simile ai programmi nell'articolo precedente, ma il data binding è definito su `Slider` anziché su `Label`:

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

Inizialmente l'ordine potrebbe apparire invertito, con `Label` che è ora l'origine del data binding mentre `Slider` è la destinazione. Il binding fa riferimento alla proprietà `Opacity` di `Label`, che ha come valore predefinito 1.

Come prevedibile, l'elemento `Slider` viene inizializzato sul valore 1 in base al valore `Opacity` iniziale di `Label`. Questo è illustrato nello screenshot iOS a sinistra:

[![Associazione inversa](binding-mode-images/reversebinding-small.png "Associazione inversa")](binding-mode-images/reversebinding-large.png#lightbox "Associazione inversa")

Tuttavia, potrebbe essere sorprendente che la `Slider` continui a funzionare, come illustrato nella schermata Android. Questo potrebbe suggerire che il data binding funziona meglio quando la destinazione di binding è `Slider` anziché `Label`, perché l'inizializzazione funziona come previsto.

La differenza tra l'esempio **Reverse Binding** (Binding inverso) e gli esempi precedenti sta nella *modalità di binding*.

## <a name="the-default-binding-mode"></a>Modalità di binding predefinita

La modalità di binding viene specificata con un membro dell'enumerazione [`BindingMode`](xref:Xamarin.Forms.BindingMode):

- [`Default`](xref:Xamarin.Forms.BindingMode.Default)
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) &ndash; dati sono disponibili in entrambe le direzioni tra l'origine e la destinazione
- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) &ndash; dati passa dall'origine alla destinazione
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; dati passa dalla destinazione all'origine
- [`OneTime`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; dati passano dall'origine alla destinazione, ma solo quando viene modificato il `BindingContext` (nuovo con Xamarin.forms 3,0)

Ogni proprietà con binding ha un valore di binding predefinito che viene impostato quando viene creata la proprietà con binding, ed è disponibile nella proprietà [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) dell'oggetto `BindableProperty`. Questa modalità di binding predefinita indica la modalità attiva quando la proprietà è una destinazione per il data binding.

La modalità di binding predefinita per la maggior parte delle proprietà, come `Rotation`, `Scale` e `Opacity` è `OneWay`. Quando queste proprietà sono destinazioni per il data binding, la proprietà di destinazione è impostata dall'origine.

Tuttavia la modalità di binding predefinita per la proprietà `Value` di `Slider` è `TwoWay`. Ciò significa che quando la proprietà `Value` è una destinazione per il data binding, la destinazione è impostata dall'origine (come di consueto) ma nel contempo l'origine è impostata dalla destinazione. Questo consente l'impostazione di `Slider` dal valore `Opacity` iniziale.

Si può pensare che questo binding bidirezionale crei un ciclo infinito, ma non è così. Le proprietà con binding segnalano una modifica della proprietà solo se la proprietà viene di fatto modificata. In questo modo viene impedito il ciclo infinito.

### <a name="two-way-bindings"></a>Binding bidirezionali

Per la maggior parte delle proprietà con binding la modalità di binding predefinita è `OneWay`, ma per le proprietà seguenti la modalità di binding predefinita è `TwoWay`:

- Proprietà `Date` di `DatePicker`
- Proprietà `Text` di `Editor`, `Entry`, `SearchBar` e `EntryCell`
- Proprietà `IsRefreshing` di `ListView`
- Proprietà `SelectedItem` di `MultiPage`
- Proprietà `SelectedIndex` e `SelectedItem` di `Picker`
- Proprietà `Value` di `Slider` e `Stepper`
- Proprietà `IsToggled` di `Switch`
- Proprietà `On` di `SwitchCell`
- Proprietà `Time` di `TimePicker`

Queste particolari proprietà sono definite come `TwoWay` per un motivo molto valido:

Quando i data binding vengono usati con l'architettura dell'applicazione Model-View-ViewModel (MVVM), la classe ViewModel è l'origine del data binding e View, che è costituita da viste come `Slider`, è la destinazione del data binding. I binding MVVM sono più simili all'esempio **Reverse Binding** (Binding inverso) dei binding negli esempi precedenti. Probabilmente si vuole che ogni vista della pagina sia inizializzata con il valore della proprietà corrispondente in ViewModel, ma che anche le modifiche nella vista abbiano effetto sulla proprietà ViewModel.

Le proprietà con le modalità di binding predefinite `TwoWay` hanno le maggiori probabilità di essere usate negli scenari MVVM.

### <a name="one-way-to-source-bindings"></a>Binding OneWayToSource

Le proprietà con binding di sola lettura hanno la modalità di binding predefinita `OneWayToSource`. Una sola proprietà con binding di lettura/scrittura ha la modalità di binding predefinita `OneWayToSource`:

- Proprietà `SelectedItem` di `ListView`

La logica di base è che un binding per la proprietà `SelectedItem` dovrebbe dare come risultato l'impostazione dell'origine del binding. Un esempio più avanti in questo articolo esegue l'override di tale comportamento.

### <a name="one-time-bindings"></a>Binding OneTime

Diverse proprietà hanno come modalità di binding predefinita `OneTime`, inclusa la proprietà `IsTextPredictionEnabled` di `Entry`.

Le proprietà di destinazione con modalità di binding `OneTime` vengono aggiornate solo quando il contesto di binding cambia. Per i binding in queste proprietà di destinazione, l'operazione semplifica l'infrastruttura di binding, perché non è necessario monitorare le modifiche nelle proprietà di origine.

## <a name="viewmodels-and-property-change-notifications"></a>ViewModel e notifiche di modifica delle proprietà

La pagina **Simple Color Selector** (Selettore colori semplice) illustra l'uso di un elemento ViewModel semplice. I data binding consentono all'utente di selezionare un colore usando tre elementi `Slider` per tonalità, saturazione e luminosità.

ViewModel è l'origine del data binding. L'elemento ViewModel *non* definisce proprietà con binding, ma implementa un meccanismo di notifica che consente all'infrastruttura di binding di ricevere una notifica quando viene modificato il valore di una proprietà. Questo meccanismo di notifica è l'interfaccia [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged), che definisce un singolo evento denominato [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged). In genere una classe che implementa questa interfaccia attiva l'evento quando il valore di una delle sue proprietà pubbliche viene modificato. Non è necessario che l'evento venga attivato se la proprietà non cambia mai. (L'interfaccia `INotifyPropertyChanged` viene anche implementata da `BindableObject` e viene attivato un evento `PropertyChanged` ogni volta che il valore di una proprietà con binding cambia.)

La classe `HslColorViewModel` definisce cinque proprietà: le proprietà `Hue`, `Saturation`, `Luminosity` e `Color` sono correlate tra loro. Quando il valore di uno dei tre componenti del colore cambia, la proprietà `Color` viene ricalcolata e gli eventi `PropertyChanged` vengono attivati per tutte e quattro le proprietà:

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

Quando la proprietà `Color` cambia, il metodo statico `GetNearestColorName` nella classe `NamedColor` (incluso anche nella soluzione **DataBindingDemos**) ottiene il colore con il nome più simile e imposta la proprietà `Name`. Questa proprietà `Name` ha una funzione di accesso `set` privata, pertanto non può essere impostata dall'esterno della classe.

Quando un elemento ViewModel viene impostato come origine del binding, l'infrastruttura di binding associa un gestore all'evento `PropertyChanged`. In questo modo il binding può ricevere notifiche in caso di modifiche alle proprietà e può quindi impostare le proprietà di destinazione in base ai valori modificati.

Tuttavia, quando una proprietà di destinazione (o la definizione `Binding` in una proprietà di destinazione) ha l'elemento `BindingMode` impostato su `OneTime`, non è necessario per l'infrastruttura di binding associare un gestore all'evento `PropertyChanged`. La proprietà di destinazione viene aggiornata solo quando cambia `BindingContext` e non quando cambia la proprietà di origine stessa.

Il file XAML **Simple Color Selector** (Selettore colori semplice) crea un'istanza di `HslColorViewModel` nel dizionario risorse della pagina e inizializza la proprietà `Color`. La proprietà `BindingContext` di `Grid` è impostata su un'estensione di binding `StaticResource` per il riferimento a quella risorsa:

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

`BoxView`, `Label` e tre viste `Slider` ereditano il contesto di binding da `Grid`. Tutte queste viste sono destinazioni di binding che fanno riferimento a proprietà di origine in ViewModel. Per la proprietà `Color` di `BoxView` e la proprietà `Text` di `Label` i data binding sono `OneWay`: le proprietà nella vista vengono impostate dalle proprietà in ViewModel.

Tuttavia la proprietà `Value` di `Slider` è `TwoWay`. In questo modo ogni elemento `Slider` può essere impostato da ViewModel, e a sua volta ViewModel può essere impostato da ogni `Slider`.

Alla prima esecuzione del programma, `BoxView`, `Label` e tre elementi `Slider` vengono impostati da ViewModel sulla base della proprietà iniziale `Color` definita al momento della creazione dell'istanza di ViewModel. Questo è illustrato nello screenshot iOS a sinistra:

[![Selettore colori semplici](binding-mode-images/simplecolorselector-small.png "Selettore colori semplici")](binding-mode-images/simplecolorselector-large.png#lightbox "Selettore colori semplici")

Quando si modificano i dispositivi di scorrimento, i `BoxView` e i `Label` vengono aggiornati di conseguenza, come illustrato nello screenshot di Android.

La creazione di istanze di ViewModel nel dizionario risorse è un approccio comune. È anche possibile creare un'istanza di ViewModel nei tag elemento proprietà per la proprietà `BindingContext`. Nel file XAML **Simple Color Selector** (Selettore colori semplice), provare a rimuovere `HslColorViewModel` dal dizionario risorse e impostarlo sulla proprietà `BindingContext` di `Grid` come illustrato di seguito:

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>

    ···

</Grid>
```

Il contesto di binding può essere impostato in modi diversi. In alcuni casi il file code-behind crea un'istanza di ViewModel e la imposta sulla proprietà `BindingContext` della pagina. Tutti questi approcci sono validi.

## <a name="overriding-the-binding-mode"></a>Override della modalità di binding

Se la modalità di binding predefinita nella proprietà di destinazione non è adatta a un determinato data binding, è possibile eseguirne l'override impostando la proprietà [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) di `Binding` (o la proprietà [`Mode`](xref:Xamarin.Forms.Xaml.BindingExtension.Mode) dell'estensione di markup `Binding`) su uno dei membri dell'enumerazione `BindingMode`.

Tuttavia l'impostazione della proprietà `Mode` su `TwoWay` non funziona sempre come previsto. Ad esempio, provare a modificare il file XAML **Alternative XAML Binding** in modo da includere `TwoWay` nella definizione del binding:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

Si prevede che `Slider` sia inizializzato sul valore iniziale della proprietà `Scale`, ovvero 1, ma questo non avviene. Quando un binding `TwoWay` viene inizializzato, in primo luogo la destinazione viene impostata dall'origine, ovvero la proprietà `Scale` viene impostata sul valore predefinito di `Slider`, pari a 0. Quando il binding `TwoWay` è impostato per `Slider`, `Slider` viene inizialmente impostato dall'origine.

È possibile impostare la modalità di binding su `OneWayToSource` nel file di esempio **Alternative XAML Binding**:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

Ora `Slider` viene inizializzato con 1 (valore predefinito di `Scale`) ma l'azionamento di `Slider` non ha effetto sulla proprietà `Scale`, pertanto questa impostazione non risulta molto utile.

> [!NOTE]
> La classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) definisce anche le proprietà [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) e [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY), che possono modificare in scala `VisualElement` in modi diversi in direzione orizzontale e verticale.

Un'applicazione molto utile della sostituzione della modalità di binding predefinita con `TwoWay` include la proprietà `SelectedItem` di `ListView`. La modalità di binding predefinita è `OneWayToSource`. Quando un data binding è impostato sulla proprietà `SelectedItem` per il riferimento a una proprietà di origine in un elemento ViewModel, tale proprietà di origine viene impostata dalla selezione `ListView`. Tuttavia in alcuni casi può essere utile che `ListView` sia inizializzata da ViewModel.

La pagina **Sample Settings** (Impostazioni di esempio) illustra questa tecnica. Questa pagina rappresenta un'implementazione semplice di impostazioni dell'applicazione, molto spesso definite in un elemento ViewModel, come questo file `SampleSettingsViewModel`:

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

Ogni impostazione dell'applicazione è una proprietà che viene salvata nel dizionario delle proprietà di Xamarin.Forms in un metodo denominato `SaveState` e caricata da tale dizionario al costruttore. Nella parte inferiore della classe sono disponibili due metodi che contribuiscono a semplificare i ViewModel e a renderli meno soggetti agli errori. Il metodo `OnPropertyChanged` nella parte inferiore ha un parametro facoltativo che è impostato sulla proprietà chiamante. Ciò consente di evitare gli errori di ortografia quando si specifica il nome della proprietà sotto forma di stringa.

Il metodo `SetProperty` nella classe confronta anche il valore impostato per la proprietà con il valore archiviato come campo e chiama `OnPropertyChanged` solo quando i due valori non sono uguali.

La classe `SampleSettingsViewModel` definisce due proprietà per il colore di sfondo: la proprietà `BackgroundNamedColor` è di tipo `NamedColor`, una classe che è inclusa anche nella soluzione **DataBindingDemos**. La proprietà `BackgroundColor` è di tipo `Color` e viene ottenuta dalla proprietà `Color` dell'oggetto `NamedColor`.

La classe `NamedColor` usa la reflection .NET per enumerare tutti i campi pubblici statici nella struttura Xamarin.Forms `Color` e archiviarli con i relativi nomi in una raccolta accessibile dalla proprietà `All` statica:

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

La classe `App` nel progetto **DataBindingDemos** definisce una proprietà denominata `Settings` di tipo `SampleSettingsViewModel`. Questa proprietà viene inizializzata quando viene creata un'istanza della classe `App` e il metodo `SaveState` viene chiamato quando viene chiamato il metodo `OnSleep`:

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

Per altre informazioni sui metodi del ciclo di vita dell'applicazione, vedere l'articolo [**Ciclo di vita dell'app**](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

Quasi tutti gli altri elementi sono gestiti nel file **SampleSettingsPage.xaml**. L'elemento `BindingContext` della pagina viene impostato usando un'estensione di markup `Binding`: l'origine del binding è la proprietà statica `Application.Current`, ovvero l'istanza della classe `App` nel progetto, mentre l'elemento `Path` è impostato sulla proprietà `Settings`, che corrisponde all'oggetto `SampleSettingsViewModel`:

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

Tutti gli elementi figlio della pagina ereditano il contesto di binding. La maggior parte degli altri binding in questa pagina viene eseguita con le proprietà in `SampleSettingsViewModel`. La proprietà `BackgroundColor` viene usata per impostare la proprietà `BackgroundColor` di `StackLayout`, mentre le proprietà `Entry`, `DatePicker`, `Switch` e `Stepper` sono associate ad altre proprietà in ViewModel.

La proprietà `ItemsSource` di `ListView` è impostata sulla proprietà statica `NamedColor.All`. In tal modo `ListView` viene compilato con tutte le istanze `NamedColor`. Per ogni elemento in `ListView` il contesto di binding per l'elemento è impostato su un oggetto `NamedColor`. Gli elementi `BoxView` e `Label` in `ViewCell` sono associati a proprietà `NamedColor`.

La proprietà `SelectedItem` di `ListView` è di tipo `NamedColor` ed è associata alla proprietà `BackgroundNamedColor` di `SampleSettingsViewModel`:

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

La modalità di binding predefinita per `SelectedItem` è `OneWayToSource`, che imposta la proprietà ViewModel dall'elemento selezionato. La modalità `TwoWay` consente l'inizializzazione di `SelectedItem` da ViewModel.

Tuttavia, quando `SelectedItem` è impostato in questo modo, `ListView` non scorre automaticamente per visualizzare l'elemento selezionato. È necessario aggiungere un piccolo frammento di codice nel file code-behind:

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

La schermata iOS a sinistra visualizza il programma alla prima esecuzione. Il costruttore in `SampleSettingsViewModel` inizializza come colore di sfondo il bianco e questa impostazione è quella selezionata in `ListView`:

[![Impostazioni di esempio](binding-mode-images/samplesettings-small.png "Impostazioni di esempio")](binding-mode-images/samplesettings-large.png#lightbox "Impostazioni di esempio")

Gli altri due screenshot mostrano le impostazioni modificate. Quando si eseguono prove con questa pagina, ricordare di impostare il programma in sospensione o di terminarlo nel dispositivo o nell'emulatore in esecuzione. La terminazione del programma nel debugger di Visual Studio non genererà la chiamata della sostituzione `OnSleep` nella classe `App`.

L'articolo successivo illustra come specificare la [**Formattazione delle stringhe**](string-formatting.md) dei data binding impostate sulla proprietà `Text` di `Label`.

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demo di data binding)
- [Capitolo sul data binding della documentazione di Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
