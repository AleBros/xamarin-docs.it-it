---
title: Xamarin. Forms DatePicker
description: Il controllo DatePicker è una vista di xamarin. Forms che consente all'utente di selezionare una data. Questo articolo illustra come usare un controllo DatePicker in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/04/2018
ms.openlocfilehash: b5d01aa451c9ab9be081b666e8c7ce51f58a3096
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172314"
---
# <a name="xamarinforms-datepicker"></a>Xamarin. Forms DatePicker

_Una vista di xamarin. Forms che consente all'utente di selezionare una data._

Xamarin. Forms [ `DatePicker` ](xref:Xamarin.Forms.DatePicker) richiama controllo selezione data della piattaforma e consente all'utente di selezionare una data. `DatePicker` definisce otto proprietà:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) typu [ `DateTime` ](xref:System.DateTime), che per impostazione predefinita il primo giorno dell'anno 1900.
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) di tipo `DateTime`, quali il valore predefinito è l'ultimo giorno dell'anno 2100.
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) typu `DateTime`, la data selezionata, che viene impostata sul valore [ `DateTime.Today` ](xref:System.DateTime.Today).
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) di tipo `string`, un [standard](/dotnet/standard/base-types/standard-date-and-time-format-strings/) oppure [personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings/) .NET la formattazione di stringa, impostazione predefinita è "D", modello di data long.
- [`TextColor`](xref:Xamarin.Forms.DatePicker.TextColor) typu [ `Color` ](xref:Xamarin.Forms.Color), il colore utilizzato per visualizzare la data selezionata, che per impostazione predefinita [ `Color.Default` ](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.DatePicker.FontAttributes) typu [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), che per impostazione predefinita [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.DatePicker.FontFamily) typu `string`, che per impostazione predefinita `null`.
- [`FontSize`](xref:Xamarin.Forms.DatePicker.FontSize) di tipo `double`, che per impostazione predefinita -1,0.

Il `DatePicker` viene attivato un [ `DateSelected` ](xref:Xamarin.Forms.DatePicker.DateSelected) evento quando l'utente seleziona una data.

> [!WARNING]
> Quando si impostano `MinimumDate` e `MaximumDate`, verificare che l'opzione `MinimumDate` è sempre minore o uguale a `MaximumDate`. In caso contrario, `DatePicker` genererà un'eccezione.

Internamente, il `DatePicker` garantisce che `Date` è compreso tra `MinimumDate` e `MaximumDate`, inclusivo. Se `MinimumDate` o `MaximumDate` è impostata in modo che `Date` non è fra di esse `DatePicker` verrà modificato il valore di `Date`.

Tutte le proprietà di otto sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che indica che essi possono essere personalizzati con stili e le proprietà possono essere destinazioni di associazioni dati. Il `Date` proprietà dispone di una modalità di associazione predefinita del [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), il che significa che può essere una destinazione di un data binding in un'applicazione che utilizza il [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) architettura.

## <a name="initializing-the-datetime-properties"></a>L'inizializzazione delle proprietà di data/ora

Nel codice, è possibile inizializzare il `MinimumDate`, `MaximumDate`, e `Date` vengono impostate su valori di tipo `DateTime`:

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

Quando un `DateTime` valore è espresso in XAML, il parser XAML Usa le `DateTime.Parse` metodo con un `CultureInfo.InvariantCulture` argomento per convertire la stringa a un `DateTime` valore. Specificare le date in un formato preciso: mesi a due cifre, a due cifre giorni e anni a quattro cifre separati da barre:

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

Se il `BindingContext` proprietà di `DatePicker` è impostata su un'istanza di un elemento ViewModel contenente le proprietà di tipo `DateTime` denominato `MinDate`, `MaxDate`, e `SelectedDate` (ad esempio), è possibile creare un'istanza di `DatePicker` simile al seguente :

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

In questo esempio, tutte le tre proprietà vengono inizializzate per le proprietà corrispondenti nell'elemento ViewModel. Poiché il `Date` proprietà dispone di una modalità di associazione di `TwoWay`, qualsiasi nuova data che l'utente seleziona si ripercuote automaticamente l'elemento ViewModel.

Se il `DatePicker` non contiene un'associazione nel relativo `Date` proprietà, un'applicazione deve associare un gestore di `DateSelected` evento per essere informati quando l'utente seleziona una nuova data.

Per informazioni sull'impostazione delle proprietà del tipo di carattere, vedere [tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="datepicker-and-layout"></a>Layout e DatePicker

È possibile usare, ad esempio un'opzione di layout orizzontale non vincolato `Center`, `Start`, o `End` con `DatePicker`:

```xaml
<DatePicker ···
            HorizontalOptions="Center"
            ··· />
```

Tuttavia, questa operazione è sconsigliata. A seconda dell'impostazione del `Format` proprietà, selezionata le date potrebbero richiedere larghezze di visualizzazione diverse. Ad esempio, fa in modo che la stringa di formato "D" `DateTime` per visualizzare le date in un formato esteso e "Mercoledì 12 settembre 2018" è necessaria una maggiore larghezza di visualizzazione a "Venerdì 4 maggio 2018". A seconda della piattaforma, questa differenza potrebbe causare il `DateTime` vista per modificare larghezza nel layout, o per la visualizzazione da troncare.

> [!TIP]
> È consigliabile usare il valore predefinito `HorizontalOptions` impostazione `Fill` con `DatePicker`e non deve usare una larghezza pari a `Auto` quando si inserisce `DatePicker` in un `Grid` cella.

## <a name="datepicker-in-an-application"></a>DatePicker in un'applicazione

Il [ **DaysBetweenDates** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker) esempio sono inclusi due `DatePicker` viste nella relativa pagina. Possono essere usati per selezionare le due date e viene calcolato il numero di giorni tra tali date. Il programma non modifica le impostazioni del `MinimumDate` e `MaximumDate` proprietà, in modo che le due date devono essere compreso tra 1900 e 2100.

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

Ciascuna `DatePicker` viene assegnato un `Format` proprietà "D" per un formato di data estesa. Si noti inoltre che il `endDatePicker` oggetto dispone di un'associazione che ha come destinazione il `MinimumDate` proprietà. L'origine del binding è selezionato `Date` proprietà del `startDatePicker` oggetto. Ciò garantisce che la data di fine è sempre in un secondo momento o uguale alla data di inizio. Oltre ai due `DatePicker` oggetti, un `Switch` con etichetta "Include entrambi i giorni in totale".

I due `DatePicker` le visualizzazioni hanno gestori associati ai `DateSelected` evento e il `Switch` ha un gestore di è collegato a relativo `Toggled` evento. Questi gestori eventi sono nel file code-behind e attivano un nuovo calcolo i giorni compresi tra le due date:

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

Quando si esegue l'esempio prima di tutto, entrambi `DatePicker` viste vengono inizializzate alla data odierna. Lo screenshot seguente mostra il programma in esecuzione in iOS, Android e la piattaforma Windows universale:

[![Giorni tra le date di inizio](datepicker-images/DaysBetweenDatesStart.png "giorni tra le date di inizio")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "giorni tra le date di inizio")

Toccando uno del `DatePicker` Visualizza richiama selezione data platform. Le piattaforme implementano questo controllo selezione data in modi molto diversi, ma ogni approccio è noto agli utenti di tale piattaforma:

[![Selezionare giorni compresi tra date](datepicker-images/DaysBetweenDatesSelect.png "giorni compresi tra le date selezionate")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "giorni compresi tra le date selezionate")

> [!TIP]
> In Android, il `DatePicker` finestra di dialogo può essere personalizzato eseguendo l'override di `CreateDatePickerDialog` metodo in un renderer personalizzato. In questo modo, ad esempio, pulsanti aggiuntivi da aggiungere alla finestra di dialogo.

Dopo aver selezionate i due date, l'applicazione visualizza il numero di giorni tra tali date:

[![Giorni compresi tra le date risultato](datepicker-images/DaysBetweenDatesResult.png "giorni compresi tra le date risultato")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "giorni tra le date di risultati")

## <a name="related-links"></a>Collegamenti correlati

- [Esempio DaysBetweenDates](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker)
- [DatePicker API](xref:Xamarin.Forms.DatePicker)
