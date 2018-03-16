---
title: Utilizzo del controllo DatePicker
description: Una vista di xamarin. Forms che consente all'utente di selezionare una data
ms.topic: article
ms.prod: xamarin
ms.assetid: 68E8EF8A-42E7-4939-8ABE-64D060E609D9
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/12/2018
ms.openlocfilehash: d47499c1e309fbc67c85b55cacbbba3942188f54
ms.sourcegitcommit: 028936cd2fe547963c1cf82343c3ee16f658089a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="using-datepicker"></a>Utilizzo del controllo DatePicker

_Una vista di xamarin. Forms che consente all'utente di selezionare una data_

Il xamarin. Forms [ `DatePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) richiama controllo selezione data della piattaforma e consente all'utente di selezionare una data. `DatePicker` definisce le proprietà di cinque:

- [`MinimumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/) di tipo [ `DateTime` ](https://developer.xamarin.com/api/type/System.DateTime/), che per impostazione predefinita il primo giorno dell'anno 1900.
- [`MaximumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/) di tipo `DateTime`, il valore predefinito è l'ultimo giorno dell'anno 2100.
- [`Date`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/) di tipo `DateTime`, la data selezionata, che per impostazione predefinita il valore [ `DateTime.Today` ](https://developer.xamarin.com/api/property/System.DateTime.Today/).
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Format/) di tipo `string`, una [standard](/dotnet/standard/base-types/standard-date-and-time-format-strings/) o [personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings/) .NET formattazione stringa, che per impostazione predefinita, "D" modello di data long.
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.TextColor/) di tipo [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/), il colore utilizzato per visualizzare la data selezionata, che per impostazione predefinita [ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/).

Il `DatePicker` genera una [ `DateSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.DatePicker.DateSelected/) evento quando l'utente seleziona una data.

> [!WARNING]
> Quando si imposta `MinimumDate` e `MaximumDate`, verificare di aver `MinimumDate` è sempre minore o uguale a `MaximumDate`. In caso contrario, `DatePicker` genererà un'eccezione.

Internamente, il `DatePicker` garantisce che `Date` è compreso tra `MinimumDate` e `MaximumDate`, estremi inclusi. Se `MinimumDate` oppure `MaximumDate` è impostata in modo che `Date` non è fra di esse `DatePicker` verrà modificato il valore di `Date`.

Sono supportate da tutte le proprietà di cinque [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) oggetti, che significa che si possono applicare stili e le proprietà possono essere destinazioni dei data binding. Il `Date` proprietà dispone di una modalità di associazione predefinito di [ `BindingMode.TwoWay` ](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/), il che significa che può essere una destinazione di un'associazione di dati in un'applicazione che utilizza il [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) architettura.

## <a name="initializing-the-datetime-properties"></a>Inizializzare le proprietà di data/ora

Nel codice, è possibile inizializzare il `MinimumDate`, `MaximumDate`, e `Date` vengono impostate su valori di tipo `DateTime`:

```csharp
DatePicker datePicker = new DatePicker
{
    MinimumDate = new DateTime(2018, 1, 1),
    MaximumDate = new DateTime(2018, 12, 31),
    Date = new DateTime(2018, 6, 21)
};
```

Quando un `DateTime` valore specificato in XAML, il parser XAML utilizza il `DateTime.Parse` metodo con un `CultureInfo.InvariantCulture` argomento per convertire la stringa a un `DateTime` valore. È necessario specificare le date in un formato preciso: mesi a due cifre, a due cifre giorni e anni a quattro cifre separati da barre:

```xaml
<DatePicker MinimumDate="01/01/2018"
            MaximumDate="12/31/2018"
            Date="06/21/2018" />
```

Se il `BindingContext` proprietà di `DatePicker` è impostata su un'istanza di un elemento ViewModel contenente le proprietà di tipo `DateTime` denominato `MinDate`, `MaxDate`, e `SelectedDate` (ad esempio), è possibile creare un'istanza di `DatePicker` come segue :

```xaml
<DatePicker MinimumDate="{Binding MinDate}"
            MaximumDate="{Binding MaxDate}"
            Date="{Binding SelectedDate}" />
```

In questo esempio, le tre proprietà vengono inizializzate per le proprietà corrispondenti nella ViewModel. Poiché il `Date` proprietà dispone di una modalità di associazione di `TwoWay`, qualsiasi nuova data che l'utente seleziona automaticamente viene riflessa nel ViewModel.

Se il `DatePicker` non contiene un'associazione nel relativo `Date` proprietà, un'applicazione deve associare un gestore per il `DateSelected` evento per essere informati quando l'utente seleziona una nuova data.

## <a name="datepicker-and-layout"></a>DatePicker e layout

È possibile utilizzare un'opzione non vincolato layout orizzontale, ad esempio `Center`, `Start`, o `End` con `DatePicker`:

```xaml
<DatePicker ··· 
            HorizontalOptions="Center" 
            ··· />
```

Tuttavia, questa operazione è sconsigliata. A seconda dell'impostazione del `Format` proprietà, selezionata le date possono richiedere larghezza di visualizzazione diverse. Ad esempio, la stringa di formato "D" causa `DateTime` per visualizzare le date in un formato esteso e "Mercoledì 12 settembre 2018" richiede una larghezza di visualizzazione maggiore rispetto a "Venerdì 4 maggio 2018". A seconda della piattaforma, questa differenza potrebbe causare il `DateTime` vista per modificare larghezza nel layout, o per la visualizzazione può essere troncato.

> [!TIP]
> È consigliabile utilizzare il valore predefinito `HorizontalOptions` impostazione di `Fill` con `DatePicker`e non deve usare una larghezza pari a `Auto` quando si inserisce `DatePicker` in un `Grid` cella.

## <a name="datepicker-in-an-application"></a>DatePicker in un'applicazione

Il [ **DaysBetweenDates** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker) esempio sono inclusi due `DatePicker` viste nella relativa pagina. Possono essere usati per selezionare due date e viene calcolato il numero di giorni tra tali date. Il programma di non modifica le impostazioni del `MinimumDate` e `MaximumDate` proprietà, in modo che le due date devono essere compreso tra 1900 e 2100.

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

Ogni `DatePicker` viene assegnato un `Format` proprietà "D" per un formato di data estesa. Si noti inoltre che il `endDatePicker` oggetto ha un binding che ha come destinazione il `MinimumDate` proprietà. Origine del binding è selezionato `Date` proprietà del `startDatePicker` oggetto. Ciò garantisce che la data di fine è sempre successiva o uguale alla data di inizio. Oltre ai due `DatePicker` oggetti, un `Switch` con etichetta "Include entrambi giorno in totale". 

I due `DatePicker` viste hanno gestori associati al `DateSelected` evento e il `Switch` è associato un gestore per il relativo `Toggled` evento. Questi gestori eventi sono nel file code-behind e attivano un nuovo calcolo dei giorni tra due date:

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

Quando l'esempio viene innanzitutto eseguito, entrambi `DatePicker` viste vengono inizializzate in base data alla corrente. Nella schermata seguente è illustrato il programma in esecuzione su iOS, Android e la piattaforma Windows universale:

[![Avviare giorni complessivi compresi tra le date](datepicker-images/DaysBetweenDatesStart.png "giorni complessivi compresi tra le date avviare")](datepicker-images/DaysBetweenDatesStart-Large.png#lightbox "avviare giorni complessivi compresi tra le date")

Se si tocca uno del `DatePicker` Visualizza richiama il controllo selezione data piattaforma. Tre piattaforme implementano questa selezione data in modi molto diversi, ma ogni approccio sono familiare agli utenti di tale piattaforma:

[![Selezionare giorni complessivi compresi tra le date](datepicker-images/DaysBetweenDatesSelect.png "giorni complessivi compresi tra le date selezionate")](datepicker-images/DaysBetweenDatesSelect-Large.png#lightbox "giorni complessivi compresi tra le date selezionate")

Dopo aver selezionate i due date, l'applicazione visualizza il numero di giorni tra tali date:

[![Giorni complessivi compresi tra le date risultato](datepicker-images/DaysBetweenDatesResult.png "giorni complessivi compresi tra le date risultato")](datepicker-images/DaysBetweenDatesResult-Large.png#lightbox "giorni tra le date di risultati")

## <a name="related-links"></a>Collegamenti correlati

- [Esempio DaysBetweenDates](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/DatePicker)
- [DatePicker API](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/)
