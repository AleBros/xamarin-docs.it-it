---
title: DatePicker di Novell. Forms
description: DatePicker è una vista Novell. Forms che consente all'utente di selezionare una data. In questo articolo viene illustrato come utilizzare un oggetto DatePicker in un'applicazione Novell. Forms.
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: 15d4159d89a463c0335d9c91b24b55151c91de8c
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695904"
---
# <a name="xamarinforms-datepicker"></a>DatePicker di Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)

_Visualizzazione Novell. Forms che consente all'utente di selezionare una data._

Il [`DatePicker`](xref:Xamarin.Forms.DatePicker) Novell. Forms richiama il controllo di selezione data della piattaforma e consente all'utente di selezionare una data. `DatePicker` definisce otto proprietà:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) di tipo [`DateTime`](xref:System.DateTime), che per impostazione predefinita è il primo giorno dell'anno 1900.
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) di tipo `DateTime`, che per impostazione predefinita è l'ultimo giorno dell'anno 2100.
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) di tipo `DateTime`, la data selezionata, che per impostazione predefinita è il valore [`DateTime.Today`](xref:System.DateTime.Today).
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) di tipo `string`, una stringa di formattazione .NET [standard](/dotnet/standard/base-types/standard-date-and-time-format-strings/) o [personalizzata](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , che per impostazione predefinita è "D", il modello di data estesa.
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor) di tipo [`Color`](xref:Xamarin.Forms.Color), il colore utilizzato per visualizzare la data selezionata, che per impostazione predefinita è [`Color.Default`](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes) di tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), il cui valore predefinito è [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily) di tipo `string`, il cui valore predefinito è `null`.
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize) di tipo `double`, il cui valore predefinito è-1,0.
- `CharacterSpacing`, di tipo `double`, è la spaziatura tra i caratteri del testo `DatePicker`.

Il `DatePicker` genera un evento di [`DateSelected`](xref:Xamarin.Forms.DatePicker.DateSelected) quando l'utente seleziona una data.

> [!WARNING]
> Quando si imposta `MinimumDate` e `MaximumDate`, assicurarsi che `MinimumDate` sia sempre minore o uguale a `MaximumDate`. In caso contrario, `DatePicker` genererà un'eccezione.

Internamente, il `DatePicker` garantisce che `Date` sia compreso tra `MinimumDate` e `MaximumDate`, inclusi. Se `MinimumDate` o `MaximumDate` è impostato in modo che `Date` non sia compreso tra di essi, `DatePicker` regola il valore di `Date`.

Tutte e otto le proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che è possibile applicare uno stile e le proprietà possono essere destinazioni di associazioni dati. La proprietà `Date` dispone di una modalità di associazione predefinita di [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), il che significa che può essere una destinazione di un data binding in un'applicazione che utilizza l'architettura [MVC (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

## <a name="initializing-the-datetime-properties"></a>Inizializzazione delle proprietà DateTime

Nel codice è possibile inizializzare le proprietà `MinimumDate`, `MaximumDate` e `Date` su valori di tipo `DateTime`:

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

Quando in XAML viene specificato un valore `DateTime`, il parser XAML usa il metodo `DateTime.Parse` con un argomento `CultureInfo.InvariantCulture` per convertire la stringa in un valore `DateTime`. Le date devono essere specificate in un formato preciso, ovvero mesi a due cifre, giorni a due cifre e anni a quattro cifre separati da barre:

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

Se la proprietà `BindingContext` di `DatePicker` è impostata su un'istanza di un ViewModel che contiene proprietà di tipo `DateTime` `MinDate`, `MaxDate` e `SelectedDate` (ad esempio), è possibile creare un'istanza del `DatePicker` come questa :

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

In questo esempio, tutte e tre le proprietà vengono inizializzate sulle proprietà corrispondenti nell'elemento ViewModel. Poiché la proprietà `Date` dispone di una modalità di binding `TwoWay`, qualsiasi nuova data selezionata dall'utente viene riflessa automaticamente nel ViewModel.

Se il `DatePicker` non contiene un binding per la relativa proprietà `Date`, un'applicazione deve associare un gestore all'evento `DateSelected` da informare quando l'utente seleziona una nuova data.

Per informazioni sull'impostazione delle proprietà dei tipi di carattere, vedere [caratteri](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="datepicker-and-layout"></a>DatePicker e layout

È possibile usare un'opzione di layout orizzontale non vincolata, ad esempio `Center`, `Start` o `End` con `DatePicker`:

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

Tuttavia, questa operazione non è consigliata. A seconda dell'impostazione della proprietà `Format`, le date selezionate potrebbero richiedere larghezze di visualizzazione diverse. La stringa di formato "D", ad esempio, fa sì che `DateTime` visualizzi le date in un formato lungo e che "mercoledì 12 settembre 2018" richieda una maggiore larghezza di visualizzazione rispetto a "Friday, 4 maggio 2018". A seconda della piattaforma, questa differenza potrebbe causare la modifica della larghezza del layout da parte della visualizzazione `DateTime` o la visualizzazione del troncamento della visualizzazione.

> [!TIP]
> È preferibile usare l'impostazione predefinita `HorizontalOptions` di `Fill` con `DatePicker` e non usare una larghezza di `Auto` quando si inserisce `DatePicker` in una cella `Grid`.

## <a name="datepicker-in-an-application"></a>DatePicker in un'applicazione

L'esempio [**DaysBetweenDates**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker) include due viste `DatePicker` nella pagina. Questi possono essere usati per selezionare due date e il programma calcola il numero di giorni tra tali date. Il programma non modifica le impostazioni delle proprietà `MinimumDate` e `MaximumDate`, quindi le due date devono essere comprese tra 1900 e 2100.

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

A ogni `DatePicker` viene assegnata una proprietà `Format` "D" per un formato di data estesa. Si noti inoltre che l'oggetto `endDatePicker` dispone di un'associazione che ha come destinazione la relativa proprietà `MinimumDate`. L'origine del binding è la proprietà `Date` selezionata dell'oggetto `startDatePicker`. In questo modo si garantisce che la data di fine sia sempre successiva o uguale alla data di inizio. Oltre ai due oggetti `DatePicker`, un `Switch` è denominato "Includi entrambi i giorni in totale".

Le due visualizzazioni `DatePicker` hanno gestori collegati all'evento `DateSelected` e il `Switch` dispone di un gestore associato al relativo evento `Toggled`. Questi gestori eventi si trovano nel file code-behind e attivano un nuovo calcolo dei giorni tra le due date:

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

Quando l'esempio viene eseguito per la prima volta, entrambe le visualizzazioni `DatePicker` vengono inizializzate sulla data odierna. Lo screenshot seguente mostra il programma in esecuzione in iOS, Android e i piattaforma UWP (Universal Windows Platform):

[![Giorni tra le date di inizio](datepicker-images/DaysBetweenDatesStart.png "Giorni tra le date di inizio")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "Giorni tra le date di inizio")

Toccando una delle `DatePicker` Visualizza viene richiamato il selettore di data della piattaforma. Le piattaforme implementano questa selezione data in modi molto diversi, ma ogni approccio è familiare agli utenti della piattaforma:

[![Giorni tra le date selezionate](datepicker-images/DaysBetweenDatesSelect.png "Giorni tra le date selezionate")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "Giorni tra le date selezionate")

> [!TIP]
> In Android è possibile personalizzare la finestra di dialogo `DatePicker` eseguendo l'override del metodo `CreateDatePickerDialog` in un renderer personalizzato. Questo consente, ad esempio, di aggiungere pulsanti aggiuntivi alla finestra di dialogo.

Dopo aver selezionato due date, l'applicazione Visualizza il numero di giorni tra le date seguenti:

[![Risultati giorni tra date](datepicker-images/DaysBetweenDatesResult.png "Risultati giorni tra date")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "Risultati giorni tra date")

## <a name="related-links"></a>Collegamenti correlati

- [Esempio DaysBetweenDates](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-datepicker)
- [API DatePicker](xref:Xamarin.Forms.DatePicker)
