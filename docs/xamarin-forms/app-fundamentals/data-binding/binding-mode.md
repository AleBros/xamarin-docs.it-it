---
title: Modalità di associazione di xamarin. Forms
description: Questo articolo illustra come controllare il flusso di informazioni tra origine e di destinazione tramite una modalità di associazione, che viene specificata con un membro dell'enumerazione BindingMode. Tutte le proprietà associabili ha una modalità di associazione predefinito, che indica la modalità attivo quando tale proprietà è una destinazione di associazione dati.
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/01/2018
ms.openlocfilehash: 420c1de0691de419180dd497a9031ea5e7dd1054
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2018
ms.locfileid: "37935667"
---
# <a name="xamarinforms-binding-mode"></a>Modalità di associazione di xamarin. Forms

Nel [articolo precedente](basic-bindings.md), il **alternativa codice associazione** e **associazione XAML alternativa** pagine in primo piano una `Label` con relativo `Scale` proprietà associato ai `Value` proprietà di un `Slider`. Poiché il `Slider` valore iniziale è 0, questo ha causato il `Scale` proprietà del `Label` sia impostato su 0 anziché 1 e il `Label` è scomparso.

Nel [ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) esempio, il **associazione inversa** pagina è simile ai programmi nell'articolo precedente, ad eccezione del fatto che il data binding è definito nel `Slider` piuttosto che scegliere il `Label`:

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

Inizialmente, questo potrebbe sembrare con le versioni precedenti: ora il `Label` è l'origine di associazione dati e il `Slider` è la destinazione. I riferimenti di associazione la `Opacity` proprietà del `Label`, che ha il valore predefinito pari a 1.

Come è prevedibile, il `Slider` viene inizializzato sul valore 1 da iniziale `Opacity` pari a `Label`. Come illustrato nella schermata iOS a sinistra:

[![Annullare l'associazione](binding-mode-images/reversebinding-small.png "inverso associazione")](binding-mode-images/reversebinding-large.png#lightbox "invertire associazione")

Ma potrebbe essere sorprendente che il `Slider` continua a funzionare, come illustrano gli screenshot di Android e UWP. Che si tratti di suggerire che il data binding funziona meglio quando le `Slider` è la destinazione del binding anziché il `Label` perché può essere eseguita l'inizializzazione, come prevedibile.

La differenza tra il **associazione inversa** campione e gli esempi precedenti comporta il *modalità di associazione*.

## <a name="the-default-binding-mode"></a>La modalità di associazione predefinita

La modalità di associazione è specificata con un membro con il [ `BindingMode` ](xref:Xamarin.Forms.BindingMode) enumerazione:

- [`Default`](xref:Xamarin.Forms.BindingMode.Default)
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) &ndash; i dati vengono inseriti in entrambe le direzioni tra origine e destinazione
- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) &ndash; dati vengono inviati dall'origine alla destinazione
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; dati vengono inviati dalla destinazione all'origine
- [`OneTime`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; dati vengono inviati dall'origine alla destinazione, ma solo quando il `BindingContext` modifiche (nuove con xamarin. Forms 3.0)

Tutte le proprietà associabili ha un valore predefinito modalità di associazione che viene impostata quando la proprietà associabile viene creata e disponibile dal [ `DefaultBindingMode` ](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) proprietà del `BindableProperty` oggetto. Questa modalità di associazione predefinita indica la modalità attivo quando tale proprietà è una destinazione di associazione dati.

La modalità di associazione predefinita per la maggior parte delle proprietà, ad esempio `Rotation`, `Scale`, e `Opacity` è `OneWay`. Quando queste proprietà sono le destinazioni di data binding, quindi impostare la proprietà di destinazione dall'origine.

Tuttavia, la modalità di associazione predefinita per il `Value` proprietà di `Slider` è `TwoWay`. Ciò significa che quando il `Value` proprietà è una destinazione di associazione dati, quindi la destinazione è impostata dall'origine (come di consueto) ma l'origine viene anche impostata dalla destinazione. Ciò consente la `Slider` impostarla da iniziale `Opacity` valore.

Per creare un ciclo infinito potrebbe sembrare questa associazione bidirezionale, ma ciò non avviene. Proprietà associabili non segnalano una modifica della proprietà, a meno che la proprietà viene modificata. Ciò impedisce che un ciclo infinito.

### <a name="two-way-bindings"></a>Associazioni bidirezionali

Proprietà associabili più hanno una modalità di associazione predefinita del `OneWay` ma le proprietà seguenti hanno una modalità di associazione predefinita di `TwoWay`:

- `Date` proprietà di `DatePicker`
- `Text` proprietà del `Editor`, `Entry`, `SearchBar`, e `EntryCell`
- `IsRefreshing` proprietà di `ListView`
- `SelectedItem` proprietà di `MultiPage`
- `SelectedIndex` e `SelectedItem` le proprietà di `Picker`
- `Value` proprietà di `Slider` e `Stepper`
- `IsToggled` proprietà di `Switch`
- `On` proprietà di `SwitchCell`
- `Time` proprietà di `TimePicker`

Queste particolari proprietà sono definite come `TwoWay` per un motivo molto valido:

Quando le associazioni dati vengono usate con l'architettura dell'applicazione Model-View-ViewModel (MVVM), la classe ViewModel è l'origine di associazione dati e la visualizzazione, che è costituito, ad esempio viste `Slider`, sono le destinazioni dell'associazione dati. Sono simili alle associazioni MVVM il **inversa associazione** esempio più di associazioni negli esempi precedenti. È molto probabile che si desidera che ogni visualizzazione della pagina per essere inizializzato con il valore della proprietà corrispondente nel ViewModel, ma le modifiche nella visualizzazione devono influiscono anche sulla proprietà ViewModel.

Le proprietà con le modalità di associazione predefinito di `TwoWay` sono quelle più probabile essere usata negli scenari MVVM.

### <a name="one-way-to-source-bindings"></a>Associazioni uno-bidirezionali all'origine

Proprietà associabile in sola lettura hanno una modalità di associazione predefinito di `OneWayToSource`. È solo una proprietà associabile lettura/scrittura con una modalità di associazione predefinita di `OneWayToSource`:

- `SelectedItem` proprietà di `ListView`

La logica alla base è che un'associazione nel `SelectedItem` proprietà dovrebbe comportare impostando l'origine di associazione. Un esempio più avanti in questo articolo esegue l'override di tale comportamento.

### <a name="one-time-bindings"></a>Associazioni monouso

Molte proprietà hanno una modalità di associazione predefinito di `OneTime`. Questi sono:

- `IsTextPredictionEnabled` proprietà di `Entry`
- `Text`, `BackgroundColor`, e `Style` delle proprietà di `Span`.

Specificare come destinazione con una modalità di associazione di proprietà `OneTime` vengono aggiornati solo quando cambia il contesto di associazione. Per le associazioni su queste proprietà di destinazione, questa operazione semplifica l'infrastruttura di associazione perché non è necessario monitorare le modifiche nelle proprietà dell'origine.

## <a name="viewmodels-and-property-change-notifications"></a>ViewModel e le notifiche di modifica delle proprietà

Il **selettore colori semplice** pagina viene illustrato l'utilizzo di un elemento ViewModel semplice. Le associazioni dati consentono all'utente di selezionare un colore utilizzando tre `Slider` elementi per la tonalità, saturazione e luminosità.

L'elemento ViewModel è l'origine di associazione dati. L'elemento ViewModel viene *non* definire proprietà associabile, ma utilizzarla per implementare un meccanismo di notifica che consente all'infrastruttura di associazione ricevere una notifica quando viene modificato il valore di una proprietà. Questo meccanismo di notifica è il [ `INotifyPropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) interfaccia che definisce una singola proprietà denominata [ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged). Una classe che implementa questa interfaccia in genere viene generato l'evento quando una delle sue proprietà pubbliche modificato il valore. L'evento non dovrà essere generato se la proprietà non cambia mai. (Il `INotifyPropertyChanged` viene anche implementata mediante `BindableObject` e un `PropertyChanged` evento viene generato ogni volta che un valore della proprietà associabile.)

Il `HslColorViewModel` classe definisce cinque proprietà: il `Hue`, `Saturation`, `Luminosity`, e `Color` sono correlati tra loro proprietà. Quando uno dei tre colori componenti modifica il valore, il `Color` proprietà viene ricalcolata e `PropertyChanged` gli eventi vengono attivati per tutti i quattro proprietà:

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

Quando il `Color` le modifiche alle proprietà, il metodo statico `GetNearestColorName` metodo nel `NamedColor` classe (inclusi anche nel **DataBindingDemos** soluzione) Ottiene il colore denominato più vicino e imposta il `Name` proprietà. Ciò `Name` possieda un proprietà `set` della funzione di accesso, in modo che non può essere impostata dall'esterno della classe.

Quando un elemento ViewModel viene impostato come origine del binding, l'infrastruttura di associazione associa un gestore per il `PropertyChanged` evento. In questo modo, l'associazione può essere notificata le modifiche apportate alle proprietà e quindi è possibile impostare le proprietà di destinazione tra i valori modificati.

Tuttavia, quando una proprietà di destinazione (o la `Binding` definition per una proprietà di destinazione) ha un `BindingMode` di `OneTime`, non è necessario per l'infrastruttura di associazione associare un gestore del `PropertyChanged` evento. La proprietà di destinazione viene aggiornata solo quando il `BindingContext` le modifiche e non quando viene modificata la proprietà di origine stessa.

Il **selettore colori semplice** file XAML crea un'istanza di `HslColorViewModel` nel dizionario risorse della pagina e inizializza il `Color` proprietà. Il `BindingContext` proprietà del `Grid` è impostata su un `StaticResource` estensione a cui fare riferimento alla risorsa di binding:

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

Il `BoxView`, `Label`e tre `Slider` viste ereditano il contesto di associazione dal `Grid`. Queste viste sono tutte le destinazioni di associazione che fanno riferimento a proprietà di origine nel ViewModel. Per il `Color` proprietà del `BoxView`e il `Text` proprietà del `Label`, le associazioni dati sono `OneWay`: le proprietà nella vista vengono impostate dalle proprietà nel ViewModel.

Il `Value` proprietà del `Slider`, è tuttavia `TwoWay`. In questo modo ognuna `Slider` da impostare da ViewModel, nonché per l'elemento ViewModel da impostare da ogni `Slider`.

Quando il programma viene eseguito prima di tutto, il `BoxView`, `Label`e tre `Slider` elementi sono tutti impostati dal ViewModel basata iniziale `Color` proprietà impostata quando è stata creata un'istanza l'elemento ViewModel. Come illustrato nella schermata iOS a sinistra:

[![Selettore colori semplice](binding-mode-images/simplecolorselector-small.png "selettore colori semplice")](binding-mode-images/simplecolorselector-large.png#lightbox "selettore colori semplice")

Come si modificano i dispositivi di scorrimento, il `BoxView` e `Label` vengono aggiornati di conseguenza, come illustrato negli screenshot di Android e UWP.

Creazione di istanze di ViewModel nel dizionario risorse è un approccio comune. È anche possibile creare un'istanza l'elemento ViewModel all'interno dei tag di elemento di proprietà per il `BindingContext` proprietà. Nel **selettore colori semplice** XAML file, provare a rimuovere le `HslColorViewModel` dal dizionario risorse e impostarlo sul `BindingContext` proprietà del `Grid` simile al seguente:

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

Se la modalità di associazione predefinito nella proprietà di destinazione non è adatta per una determinata associazione dati, è possibile eseguirne l'override impostando il [ `Mode` ](xref:Xamarin.Forms.BindingBase.Mode) proprietà della `Binding` (o nella [ `Mode` ](xref:Xamarin.Forms.Xaml.BindingExtension.Mode) proprietà del `Binding` estensione di markup) a uno dei membri del `BindingMode` enumerazione.

Tuttavia, impostando il `Mode` proprietà `TwoWay` non sempre funziona come previsto. Ad esempio, provare a modificare la **associazione XAML alternativa** file XAML per includere `TwoWay` nella definizione dell'associazione:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

È possibile prevedere che il `Slider` verrebbe inizializzata sul valore iniziale del `Scale` proprietà, ovvero 1, ma ciò non avviene. Quando un `TwoWay` associazione viene inizializzata, la destinazione è impostata dall'origine prima di tutto, vale a dire che il `Scale` è impostata sul `Slider` il valore predefinito di 0. Quando il `TwoWay` binding è nastavit il `Slider`, il `Slider` è inizialmente impostato dall'origine.

È possibile impostare la modalità di associazione su `OneWayToSource` nella **associazione XAML alternativa** esempio:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

A questo punto il `Slider` viene inizializzata su 1 (il valore predefinito di `Scale`) ma la modifica la `Slider` non ha alcun effetto il `Scale` proprietà, in modo che questo non è molto utile.

> [!NOTE]
> Il [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe definisce anche [ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX) e [ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY) proprietà, che è possibile ridimensionare il `VisualElement` in modo diverso nel direzioni orizzontale e verticale.

Un'applicazione molto utile di override la modalità di associazione predefinita con `TwoWay` comporta la `SelectedItem` proprietà di `ListView`. La modalità di associazione predefinita è `OneWayToSource`. Quando un data binding è impostato sul `SelectedItem` per fare riferimento a una proprietà di origine in un elemento ViewModel, tale proprietà di origine viene impostato dalla proprietà di `ListView` selezione. Tuttavia, in alcuni casi, è anche possibile la `ListView` da inizializzare da ViewModel.

Il **le impostazioni di esempio** questa tecnica è dimostrata pagina. Questa pagina rappresenta un'implementazione semplice di impostazioni dell'applicazione, molto spesso definite in un elemento ViewModel, come questo `SampleSettingsViewModel` file:

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

Ogni impostazione dell'applicazione è una proprietà che viene salvata il dizionario delle proprietà di xamarin. Forms in un metodo denominato `SaveState` e caricato da tale dizionario nel costruttore. Nella parte inferiore della classe sono due metodi che consentono di semplificare i ViewModel e li rendono meno soggetto a errori. Il `OnPropertyChanged` metodo nella parte inferiore ha un parametro facoltativo che è stato impostato la proprietà chiama. Ciò consente di evitare gli errori di ortografia quando si specifica il nome della proprietà sotto forma di stringa.

Il `SetProperty` metodo nella classe va anche oltre: il valore che viene impostato per la proprietà con il valore archiviato come campo di confronto e chiama solo `OnPropertyChanged` quando i due valori non sono uguali.

Il `SampleSettingsViewModel` classe definisce due proprietà per il colore di sfondo: il `BackgroundNamedColor` proprietà è di tipo `NamedColor`, che una classe include anche il **DataBindingDemos** soluzione. Il `BackgroundColor` proprietà è di tipo `Color`e viene ottenuto dal `Color` proprietà del `NamedColor` oggetto.

Il `NamedColor` classe utilizza la reflection .NET per enumerare tutti i campi pubblici statici in xamarin. Forms `Color` struttura e per l'archiviazione con i relativi nomi in una raccolta accessibile da statico `All` proprietà:

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

Il `App` classe la **DataBindingDemos** progetto definisce una proprietà denominata `Settings` di tipo `SampleSettingsViewModel`. Questa proprietà viene inizializzata quando il `App` viene creata un'istanza di classe e il `SaveState` metodo viene chiamato quando il `OnSleep` viene chiamato il metodo:

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

Per altre informazioni sui metodi del ciclo di vita dell'applicazione, vedere l'articolo [ **ciclo di vita**](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

Quasi tutto il resto viene gestito nel **SampleSettingsPage.xaml** file. Il `BindingContext` della pagina viene impostato utilizzando un `Binding` estensione di markup: l'origine del binding è il metodo statico `Application.Current` proprietà, ovvero l'istanza del `App` classe nel progetto e il `Path` è impostata sul `Settings` proprietà, ovvero il `SampleSettingsViewModel` oggetto:

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

Tutti i nodi figlio della pagina ereditano il contesto di associazione. La maggior parte delle altre associazioni in questa pagina sono alle proprietà in `SampleSettingsViewModel`. Il `BackgroundColor` proprietà viene utilizzata per impostare il `BackgroundColor` proprietà delle `StackLayout`e il `Entry`, `DatePicker`, `Switch`, e `Stepper` proprietà sono associate ad altre proprietà nel ViewModel.

Il `ItemsSource` proprietà del `ListView` è impostato su statico `NamedColor.All` proprietà. Si riempie il `ListView` con tutte le `NamedColor` istanze. Per ogni elemento di `ListView`, il contesto di associazione per l'elemento è impostato su un `NamedColor` oggetto. Il `BoxView` e `Label` nel `ViewCell` sono associati alle proprietà in `NamedColor`.

Il `SelectedItem` proprietà del `ListView` JE typu `NamedColor`e viene associato al `BackgroundNamedColor` proprietà di `SampleSettingsViewModel`:

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

La modalità di associazione predefinita per `SelectedItem` è `OneWayToSource`, che imposta la proprietà ViewModel dall'elemento selezionato. Il `TwoWay` modalità consente il `SelectedItem` da inizializzare da ViewModel.

Tuttavia, quando la `SelectedItem` è impostato in questo modo, il `ListView` non scorre automaticamente per mostrare l'elemento selezionato. È necessario disporre di codice nel file code-behind:

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

La schermata iOS a sinistra mostra il programma in esecuzione prima di tutto. Il costruttore nella `SampleSettingsViewModel` Inizializza il colore di sfondo sul bianco e questo è l'elemento selezionato nel `ListView`:

[![Le impostazioni di esempio](binding-mode-images/samplesettings-small.png "impostazioni di esempio")](binding-mode-images/samplesettings-large.png#lightbox "le impostazioni di esempio")

I due screenshot Mostra le impostazioni modificate. Quando si sperimentano questa pagina, ricordare di inserire il programma in sospensione o interruzione nel dispositivo o emulatore in esecuzione. Terminare il programma dal debugger di Visual Studio non genererà il `OnSleep` esegue l'override nel `App` classe da chiamare.

Nel prossimo articolo verrà illustrato come specificare [ **formattazione della stringa** ](string-formatting.md) delle associazioni di dati che sono impostate sul `Text` proprietà `Label`.


## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capitolo di associazione di dati dal libro di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
