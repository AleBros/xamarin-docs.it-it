---
title: Xamarin.FormsTimePicker
description: TimePicker è una Xamarin.Forms vista che consente all'utente di selezionare un'ora. Questo articolo illustra come utilizzare un TimePicker in un' Xamarin.Forms applicazione.
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7c2e6b97f2207ebb6543fb6a720cd430331f989b
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138229"
---
# <a name="xamarinforms-timepicker"></a>Xamarin.FormsTimePicker

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_Xamarin.FormsVisualizzazione che consente all'utente di selezionare un'ora._

Xamarin.Forms [`TimePicker`](xref:Xamarin.Forms.TimePicker) Richiama il controllo di selezione temporale della piattaforma e consente all'utente di selezionare un'ora. `TimePicker` definisce le proprietà seguenti:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time)di tipo `TimeSpan` , l'ora selezionata, il cui valore predefinito è `TimeSpan` 0. Il `TimeSpan` tipo indica una durata di tempo dalla mezzanotte.
- [`Format`](xref:Xamarin.Forms.TimePicker.Format)di tipo `string` , una stringa di formattazione .NET [standard](/dotnet/standard/base-types/standard-date-and-time-format-strings/) o [personalizzata](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , che per impostazione predefinita è "t", il modello di ora breve.
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor)di tipo [`Color`](xref:Xamarin.Forms.Color) , il colore utilizzato per visualizzare l'ora selezionata, il cui valore predefinito è [`Color.Default`](xref:Xamarin.Forms.Color.Default) .
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes)di tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , che per impostazione predefinita è [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) .
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily)di tipo `string` , che per impostazione predefinita è `null` .
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize)di tipo `double` , che per impostazione predefinita è-1,0.
- `CharacterSpacing`, di tipo `double` , indica la spaziatura tra i caratteri del `TimePicker` testo.

Tutte queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che è possibile applicare uno stile e le proprietà possono essere destinazioni di associazioni dati. La [`Time`](xref:Xamarin.Forms.TimePicker.Time) proprietà dispone di una modalità di associazione predefinita [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , che significa che può essere una destinazione di un data binding in un'applicazione che utilizza l'architettura [MVC (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

[`TimePicker`](xref:Xamarin.Forms.TimePicker)Non include un evento per indicare un nuovo valore selezionato [`Time`](xref:Xamarin.Forms.TimePicker.Time) . Se è necessario ricevere una notifica, è possibile aggiungere un gestore per l' [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento.

## <a name="initializing-the-time-property"></a>Inizializzazione della proprietà Time

Nel codice è possibile inizializzare la [`Time`](xref:Xamarin.Forms.TimePicker.Time) proprietà su un valore di tipo `TimeSpan` :

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

Quando [`Time`](xref:Xamarin.Forms.TimePicker.Time) si specifica la proprietà in XAML, il valore viene convertito in un oggetto `TimeSpan` e convalidato per garantire che il numero di millisecondi sia maggiore o uguale a 0 e che il numero di ore sia inferiore a 24. I componenti temporali devono essere separati dai due punti:

```xaml
<TimePicker Time="4:15:26" />
```

Se la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) proprietà di [`TimePicker`](xref:Xamarin.Forms.TimePicker) è impostata su un'istanza di un ViewModel contenente una proprietà di tipo `TimeSpan` denominata `SelectedTime` (ad esempio), è possibile creare un'istanza di `TimePicker` simile alla seguente:

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

In questo esempio la [`Time`](xref:Xamarin.Forms.TimePicker.Time) proprietà viene inizializzata sulla `SelectedTime` proprietà nell'elemento ViewModel. Poiché la `Time` proprietà dispone di una modalità di associazione [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , qualsiasi nuova ora selezionata dall'utente viene propagata automaticamente all'elemento ViewModel.

Se non [`TimePicker`](xref:Xamarin.Forms.TimePicker) contiene un'associazione alla relativa [`Time`](xref:Xamarin.Forms.TimePicker.Time) proprietà, un'applicazione deve associare un gestore all' [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento per essere informato quando l'utente seleziona una nuova ora.

Per informazioni sull'impostazione delle proprietà dei tipi di carattere, vedere [caratteri](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="timepicker-and-layout"></a>TimePicker e layout

È possibile usare un'opzione di layout orizzontale non vincolata, ad esempio `Center` , `Start` o `End` con [`TimePicker`](xref:Xamarin.Forms.TimePicker) :

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

Tuttavia, questa operazione non è consigliata. A seconda dell'impostazione della [`Format`](xref:Xamarin.Forms.TimePicker.Format) proprietà, le ore selezionate potrebbero richiedere larghezze di visualizzazione diverse. La stringa di formato "T", ad esempio, fa sì che la visualizzazione [`TimePicker`](xref:Xamarin.Forms.TimePicker) visualizzi le ore in un formato lungo e che "4:15:26 AM" richieda una maggiore larghezza di visualizzazione rispetto al formato di ora breve ("t") di "4:15 AM". A seconda della piattaforma, questa differenza potrebbe causare la modifica della larghezza del layout da parte della `TimePicker` vista o la visualizzazione del troncamento della visualizzazione.

> [!TIP]
> È preferibile usare l' `HorizontalOptions` impostazione predefinita di `Fill` con [`TimePicker`](xref:Xamarin.Forms.TimePicker) e non usare una larghezza di quando si `Auto` inserisce `TimePicker` in una `Grid` cella.

## <a name="timepicker-in-an-application"></a>TimePicker in un'applicazione

Nell'esempio [**setimer**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) sono incluse le [`TimePicker`](xref:Xamarin.Forms.TimePicker) [`Entry`](xref:Xamarin.Forms.Entry) Visualizzazioni, e [`Switch`](xref:Xamarin.Forms.Switch) nella relativa pagina. L'oggetto `TimePicker` può essere utilizzato per selezionare un'ora e, quando viene visualizzata una finestra di dialogo di avviso che ricorda l'utente del testo in `Entry` , a condizione che `Switch` sia attivata o disattivata. Ecco il file XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SetTimer"
             x:Class="SetTimer.MainPage">
    <StackLayout>
        ...
        <Entry x:Name="_entry"
               Placeholder="Enter event to be reminded of" />
        <Label Text="Select the time below to be reminded at." />
        <TimePicker x:Name="_timePicker"
                    Time="11:00:00"
                    Format="T"
                    PropertyChanged="OnTimePickerPropertyChanged" />
        <StackLayout Orientation="Horizontal">
            <Label Text="Enable timer:" />
            <Switch x:Name="_switch"
                    HorizontalOptions="EndAndExpand"
                    Toggled="OnSwitchToggled" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

[`Entry`](xref:Xamarin.Forms.Entry)Consente di immettere il testo del promemoria che verrà visualizzato quando si verifica l'ora selezionata. All'oggetto [`TimePicker`](xref:Xamarin.Forms.TimePicker) viene assegnata una [`Format`](xref:Xamarin.Forms.TimePicker.Format) proprietà di "T" per il formato di ora estesa. Dispone di un gestore eventi associato all' [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento e [`Switch`](xref:Xamarin.Forms.Switch) dispone di un gestore associato al relativo [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) evento. Questi gestori di eventi si trovano nel file code-behind e chiamano il `SetTriggerTime` Metodo:

```csharp
public partial class MainPage : ContentPage
{
    DateTime _triggerTime;

    public MainPage()
    {
        InitializeComponent();

        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimerTick);
    }

    bool OnTimerTick()
    {
        if (_switch.IsToggled && DateTime.Now >= _triggerTime)
        {
            _switch.IsToggled = false;
            DisplayAlert("Timer Alert", "The '" + _entry.Text + "' timer has elapsed", "OK");
        }
        return true;
    }

    void OnTimePickerPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        if (args.PropertyName == "Time")
        {
            SetTriggerTime();
        }
    }

    void OnSwitchToggled(object sender, ToggledEventArgs args)
    {
        SetTriggerTime();
    }

    void SetTriggerTime()
    {
        if (_switch.IsToggled)
        {
            _triggerTime = DateTime.Today + _timePicker.Time;
            if (_triggerTime < DateTime.Now)
            {
                _triggerTime += TimeSpan.FromDays(1);
            }
        }
    }
}
```

Il `SetTriggerTime` metodo calcola un'ora del timer in base al `DateTime.Today` valore della proprietà e al `TimeSpan` valore restituito da [`TimePicker`](xref:Xamarin.Forms.TimePicker) . Questa operazione è necessaria perché la `DateTime.Today` proprietà restituisce un valore `DateTime` che indica la data corrente, ma con l'ora della mezzanotte. Se l'ora del timer è già passata, si presuppone che sia domani.

Il timer scorre ogni secondo, eseguendo il `OnTimerTick` metodo che controlla se [`Switch`](xref:Xamarin.Forms.Switch) è on e se l'ora corrente è maggiore o uguale all'ora del timer. Quando si verifica l'ora del timer, il [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) metodo presenta una finestra di dialogo di avviso all'utente come promemoria.

Quando l'esempio viene eseguito per la prima volta, la [`TimePicker`](xref:Xamarin.Forms.TimePicker) vista viene inizializzata sulle 11.00. Toccando `TimePicker` richiama il selettore temporale della piattaforma. Le piattaforme implementano il selettore di tempo in modi molto diversi, ma ogni approccio è familiare agli utenti della piattaforma:

[![Seleziona ora](timepicker-images/timepicker-open.png "Seleziona ora")](timepicker-images/timepicker-open-large.png#lightbox "Seleziona ora")

> [!TIP]
> In Android [`TimePicker`](xref:Xamarin.Forms.TimePicker) è possibile personalizzare la finestra di dialogo eseguendo l'override del `CreateTimePickerDialog` metodo in un renderer personalizzato. Questo consente, ad esempio, di aggiungere pulsanti aggiuntivi alla finestra di dialogo.

Dopo aver selezionato un'ora, l'ora selezionata viene visualizzata nell' [`TimePicker`](xref:Xamarin.Forms.TimePicker) :

[![Ora selezionata](timepicker-images/timepicker-selected.png "Ora selezionata")](timepicker-images/timepicker-selected-large.png#lightbox "Ora selezionata")

A condizione che il [`Switch`](xref:Xamarin.Forms.Switch) sia impostato su on, l'applicazione visualizza una finestra di dialogo di avviso che ricorda l'utente del testo nel [`Entry`](xref:Xamarin.Forms.Entry) quando si verifica l'ora selezionata:

[![Popup timer](timepicker-images/timer-test.png "Popup timer")](timepicker-images/timer-test-large.png#lightbox "Popup timer")

Non appena viene visualizzata la finestra di dialogo di avviso, la [`Switch`](xref:Xamarin.Forms.Switch) viene attivata o disattivata.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di setimer](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [API TimePicker](xref:Xamarin.Forms.TimePicker)
