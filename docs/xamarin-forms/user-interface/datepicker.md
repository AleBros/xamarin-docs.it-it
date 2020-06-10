---
title: " Xamarin.Forms DatePicker" Description: "DatePicker è una Xamarin.Forms vista che consente all'utente di selezionare una data. In questo articolo viene illustrato come utilizzare un oggetto DatePicker in un' Xamarin.Forms applicazione ".
ms. prod: Novell MS. AssetID: 68E8EF8A-42E7-4939-8ABE-64D060E609D9 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 06/04/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-datepicker"></a>Xamarin.FormsDatePicker

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)

_Xamarin.FormsVisualizzazione che consente all'utente di selezionare una data._

Xamarin.Forms [`DatePicker`](xref:Xamarin.Forms.DatePicker) Richiama il controllo di selezione data della piattaforma e consente all'utente di selezionare una data. `DatePicker`definisce otto proprietà:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate)di tipo [`DateTime`](xref:System.DateTime) , che per impostazione predefinita è il primo giorno dell'anno 1900.
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate)di tipo `DateTime` , che per impostazione predefinita è l'ultimo giorno dell'anno 2100.
- [`Date`](xref:Xamarin.Forms.DatePicker.Date)di tipo `DateTime` , la data selezionata, che per impostazione predefinita è il valore [`DateTime.Today`](xref:System.DateTime.Today) .
- [`Format`](xref:Xamarin.Forms.DatePicker.Format)di tipo `string` , una stringa di formattazione .NET [standard](/dotnet/standard/base-types/standard-date-and-time-format-strings/) o [personalizzata](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , che per impostazione predefinita è "D", il modello di data estesa.
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor)di tipo [`Color`](xref:Xamarin.Forms.Color) , il colore utilizzato per visualizzare la data selezionata, che per impostazione predefinita è [`Color.Default`](xref:Xamarin.Forms.Color.Default) .
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes)di tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , che per impostazione predefinita è [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) .
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily)di tipo `string` , che per impostazione predefinita è `null` .
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize)di tipo `double` , che per impostazione predefinita è-1,0.
- `CharacterSpacing`, di tipo `double` , indica la spaziatura tra i caratteri del `DatePicker` testo.

`DatePicker`Genera un [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) evento quando l'utente seleziona una data.

> [!WARNING]
> Quando `MinimumDate` si imposta e `MaximumDate` , verificare che `MinimumDate` sia sempre minore o uguale a `MaximumDate` . In caso contrario, `DatePicker` genererà un'eccezione.

Internamente, `DatePicker` garantisce che `Date` sia compreso tra `MinimumDate` e `MaximumDate` , inclusi. Se `MinimumDate` o `MaximumDate` è impostato in modo che `Date` non sia compreso tra loro, in `DatePicker` verrà modificato il valore di `Date` .

Tutte e otto le proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere con stile e le proprietà possono essere destinazioni di associazioni dati. La `Date` proprietà dispone di una modalità di associazione predefinita [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , che significa che può essere una destinazione di un data binding in un'applicazione che utilizza l'architettura [MVC (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

## <a name="initializing-the-datetime-properties"></a>Inizializzazione delle proprietà DateTime

Nel codice è possibile inizializzare le `MinimumDate` `MaximumDate` proprietà, e `Date` su valori di tipo `DateTime` :

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

Quando `DateTime` si specifica un valore in XAML, il parser XAML usa il `DateTime.Parse` metodo con un `CultureInfo.InvariantCulture` argomento per convertire la stringa in un `DateTime` valore. Le date devono essere specificate in un formato preciso, ovvero mesi a due cifre, giorni a due cifre e anni a quattro cifre separati da barre:

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

Se la `BindingContext` proprietà di `DatePicker` è impostata su un'istanza di un ViewModel che contiene le proprietà di tipo `DateTime` denominato `MinDate` , `MaxDate` e `SelectedDate` (ad esempio), è possibile creare un'istanza di `DatePicker` simile alla seguente:

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

In questo esempio, tutte e tre le proprietà vengono inizializzate sulle proprietà corrispondenti nell'elemento ViewModel. Poiché la `Date` proprietà dispone di una modalità di associazione `TwoWay` , qualsiasi nuova data selezionata dall'utente viene riflessa automaticamente nell'elemento ViewModel.

Se l'oggetto `DatePicker` non contiene un binding sulla relativa `Date` proprietà, un'applicazione deve associare un gestore all' `DateSelected` evento per essere informato quando l'utente seleziona una nuova data.

Per informazioni sull'impostazione delle proprietà dei tipi di carattere, vedere [caratteri](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="datepicker-and-layout"></a>DatePicker e layout

È possibile usare un'opzione di layout orizzontale non vincolata, ad esempio `Center` , `Start` o `End` con `DatePicker` :

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

Tuttavia, questa operazione non è consigliata. A seconda dell'impostazione della `Format` proprietà, le date selezionate potrebbero richiedere larghezze di visualizzazione diverse. La stringa di formato "D", ad esempio, consente `DateTime` di visualizzare le date in un formato esteso e "mercoledì 12 settembre 2018" richiede una larghezza di visualizzazione superiore a "venerdì 4 maggio 2018". A seconda della piattaforma, questa differenza potrebbe causare la modifica della larghezza del layout da parte della `DateTime` vista o la visualizzazione del troncamento della visualizzazione.

> [!TIP]
> È preferibile usare l' `HorizontalOptions` impostazione predefinita di `Fill` con `DatePicker` e non usare una larghezza di quando si `Auto` inserisce `DatePicker` in una `Grid` cella.

## <a name="datepicker-in-an-application"></a>DatePicker in un'applicazione

L'esempio [**DaysBetweenDates**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) include due `DatePicker` visualizzazioni nella relativa pagina. Questi possono essere usati per selezionare due date e il programma calcola il numero di giorni tra tali date. Il programma non modifica le impostazioni delle `MinimumDate` proprietà e `MaximumDate` , quindi le due date devono essere comprese tra 1900 e 2100.

Ecco il file XAML:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DaysBetweenDates"
             x:Class="DaysBetweenDates.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <StackLayout Margin="10">
        <Label Text="Days Between Dates"
               Style="{DynamicResource TitleStyle}"
               Margin="0, 20"
               HorizontalTextAlignment="Center" />

        <Label Text="Start Date:" />

        <DatePicker x:Name="startDatePicker"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <Label Text="End Date:" />

        <DatePicker x:Name="endDatePicker"
                    MinimumDate="{Binding Source={x:Reference startDatePicker},
                                          Path=Date}"
                    Format="D"
                    Margin="30, 0, 0, 30"
                    DateSelected="OnDateSelected" />

        <StackLayout Orientation="Horizontal"
                     Margin="0, 0, 0, 30">
            <Label Text="Include both days in total: "
                   VerticalOptions="Center" />
            <Switch x:Name="includeSwitch"
                    Toggled="OnSwitchToggled" />
        </StackLayout>

        <Label x:Name="resultLabel"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

A ogni `DatePicker` viene assegnata una `Format` proprietà di "D" per un formato di data estesa. Si noti inoltre che l' `endDatePicker` oggetto dispone di un'associazione destinata alla relativa `MinimumDate` Proprietà. L'origine del binding è la `Date` proprietà selezionata dell' `startDatePicker` oggetto. In questo modo si garantisce che la data di fine sia sempre successiva o uguale alla data di inizio. Oltre ai due `DatePicker` oggetti, un `Switch` è denominato "Includi entrambi i giorni in totale".

Le due `DatePicker` visualizzazioni hanno gestori collegati all' `DateSelected` evento e l'oggetto `Switch` ha un gestore associato al relativo `Toggled` evento. Questi gestori eventi si trovano nel file code-behind e attivano un nuovo calcolo dei giorni tra le due date:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();
    }

    void OnDateSelected(object sender, DateChangedEventArgs args)
    {
        Recalculate();
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        Recalculate();
    }

    void Recalculate()
    {
        TimeSpan timeSpan = endDatePicker.Date - startDatePicker.Date +
            (includeSwitch.IsToggled ? TimeSpan.FromDays(1) : TimeSpan.Zero);

        resultLabel.Text = String.Format("{0} day{1} between dates",
                                            timeSpan.Days, timeSpan.Days == 1 ? "" : "s");
    }
}
```

Quando l'esempio viene eseguito per la prima volta, entrambe le `DatePicker` visualizzazioni vengono inizializzate sulla data odierna. Lo screenshot seguente mostra il programma in esecuzione in iOS e Android:

[![Giorni tra le date di inizio](datepicker-images/DaysBetweenDatesStart.png "Giorni tra le date di inizio")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "Giorni tra le date di inizio")

Toccando una delle `DatePicker` visualizzazioni viene richiamato il selettore di data della piattaforma. Le piattaforme implementano questa selezione data in modi molto diversi, ma ogni approccio è familiare agli utenti della piattaforma:

[![Giorni tra le date selezionate](datepicker-images/DaysBetweenDatesSelect.png "Giorni tra le date selezionate")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "Giorni tra le date selezionate")

> [!TIP]
> In Android `DatePicker` è possibile personalizzare la finestra di dialogo eseguendo l'override del `CreateDatePickerDialog` metodo in un renderer personalizzato. Questo consente, ad esempio, di aggiungere pulsanti aggiuntivi alla finestra di dialogo.

Dopo aver selezionato due date, l'applicazione Visualizza il numero di giorni tra le date seguenti:

[![Risultati giorni tra date](datepicker-images/DaysBetweenDatesResult.png "Risultati giorni tra date")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "Risultati giorni tra date")

## <a name="related-links"></a>Collegamenti correlati

- [Esempio DaysBetweenDates](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)
- [API DatePicker](xref:Xamarin.Forms.DatePicker)
