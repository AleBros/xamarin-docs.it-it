---
title: Xamarin.Forms TimePicker
description: TimePicker è una vista Xamarin.Forms che consente all'utente di selezionare un'ora. Questo articolo illustra come utilizzare un TimePicker in un'applicazione Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
ms.openlocfilehash: aae0791199b0e3042a3c619fcb11e7b877f52012
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695906"
---
# <a name="xamarinforms-timepicker"></a>Xamarin.Forms TimePicker

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)

_Visualizzazione Xamarin.Forms che consente all'utente di selezionare un'ora._

Il [`TimePicker`](xref:Xamarin.Forms.TimePicker) Xamarin.Forms richiama il controllo di selezione temporale della piattaforma e consente all'utente di selezionare un'ora. `TimePicker` definisce le proprietà seguenti:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) di tipo `TimeSpan`, l'ora selezionata, che per impostazione predefinita è un `TimeSpan` di 0. Il tipo di `TimeSpan` indica l'intervallo di tempo trascorso la mezzanotte.
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) di tipo `string`, una stringa di formattazione .NET [standard](/dotnet/standard/base-types/standard-date-and-time-format-strings/) o [personalizzata](/dotnet/standard/base-types/custom-date-and-time-format-strings/) , che per impostazione predefinita è "t", il modello di ora breve.
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) di tipo [`Color`](xref:Xamarin.Forms.Color), il colore utilizzato per visualizzare l'ora selezionata, che per impostazione predefinita è [`Color.Default`](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) di tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes), il cui valore predefinito è [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) di tipo `string`, il cui valore predefinito è `null`.
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) di tipo `double`, il cui valore predefinito è-1,0.
- `CharacterSpacing`, di tipo `double`, è la spaziatura tra i caratteri del testo `TimePicker`.

Tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che è possibile applicare uno stile e le proprietà possono essere destinazioni di associazioni dati. La proprietà [`Time`](xref:Xamarin.Forms.TimePicker.Time) dispone di una modalità di associazione predefinita di [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), il che significa che può essere una destinazione di un data binding in un'applicazione che utilizza l'architettura [MVC (Model-View-ViewModel)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) .

Il [`TimePicker`](xref:Xamarin.Forms.TimePicker) non include un evento per indicare un nuovo valore [`Time`](xref:Xamarin.Forms.TimePicker.Time) selezionato. Se è necessario ricevere una notifica, è possibile aggiungere un gestore per l'evento [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) .

## <a name="initializing-the-time-property"></a>Inizializzazione della proprietà Time

Nel codice è possibile inizializzare la proprietà [`Time`](xref:Xamarin.Forms.TimePicker.Time) su un valore di tipo `TimeSpan`:

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

Quando si specifica la proprietà [`Time`](xref:Xamarin.Forms.TimePicker.Time) in XAML, il valore viene convertito in una `TimeSpan` e convalidato per garantire che il numero di millisecondi sia maggiore o uguale a 0 e che il numero di ore sia inferiore a 24. I componenti temporali devono essere separati dai due punti:

```xaml
<TimePicker Time="4:15:26" />
```

Se la proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) di [`TimePicker`](xref:Xamarin.Forms.TimePicker) è impostata su un'istanza di un ViewModel contenente una proprietà di tipo `TimeSpan` `SelectedTime` denominata (ad esempio), è possibile creare un'istanza del `TimePicker` come segue:

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

In questo esempio, la proprietà [`Time`](xref:Xamarin.Forms.TimePicker.Time) viene inizializzata sulla proprietà `SelectedTime` nell'elemento ViewModel. Poiché la proprietà `Time` dispone di una modalità di associazione di [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay), qualsiasi nuova ora selezionata dall'utente viene propagata automaticamente all'elemento ViewModel.

Se il [`TimePicker`](xref:Xamarin.Forms.TimePicker) non contiene un binding sulla relativa proprietà [`Time`](xref:Xamarin.Forms.TimePicker.Time) , un'applicazione deve associare un gestore all'evento [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) per essere informato quando l'utente seleziona una nuova ora.

Per informazioni sull'impostazione delle proprietà dei tipi di carattere, vedere [caratteri](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="timepicker-and-layout"></a>TimePicker e layout

È possibile usare un'opzione di layout orizzontale non vincolata, ad esempio `Center`, `Start` o `End` con [`TimePicker`](xref:Xamarin.Forms.TimePicker):

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

Tuttavia, questa operazione non è consigliata. A seconda dell'impostazione della proprietà [`Format`](xref:Xamarin.Forms.TimePicker.Format) , le ore selezionate potrebbero richiedere larghezze di visualizzazione diverse. La stringa di formato "T", ad esempio, fa sì che la visualizzazione [`TimePicker`](xref:Xamarin.Forms.TimePicker) visualizzi le ore in un formato lungo e che "4:15:26 AM" richieda una maggiore larghezza di visualizzazione rispetto al formato di ora breve ("t") di "4:15 AM". A seconda della piattaforma, questa differenza potrebbe causare la modifica della larghezza del layout da parte della visualizzazione `TimePicker` o la visualizzazione del troncamento della visualizzazione.

> [!TIP]
> È preferibile usare l'impostazione predefinita `HorizontalOptions` di `Fill` con [`TimePicker`](xref:Xamarin.Forms.TimePicker)e non usare una larghezza di `Auto` quando si inserisce `TimePicker` in una cella `Grid`.

## <a name="timepicker-in-an-application"></a>TimePicker in un'applicazione

L'esempio [**setimer**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker) include le visualizzazioni [`TimePicker`](xref:Xamarin.Forms.TimePicker), [`Entry`](xref:Xamarin.Forms.Entry)e [`Switch`](xref:Xamarin.Forms.Switch) nella relativa pagina. Il `TimePicker` può essere utilizzato per selezionare un'ora e, quando viene visualizzata una finestra di dialogo di avviso che ricorda l'utente del testo presente nel `Entry`, purché la `Switch` sia attivata o disattivata. Ecco il file XAML:

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

Il [`Entry`](xref:Xamarin.Forms.Entry) consente di immettere il testo del promemoria che verrà visualizzato quando si verifica l'ora selezionata. Al [`TimePicker`](xref:Xamarin.Forms.TimePicker) viene assegnata una proprietà [`Format`](xref:Xamarin.Forms.TimePicker.Format) "t" per il formato di ora estesa. Dispone di un gestore eventi associato all'evento [`PropertyChanged`](xref:Xamarin.Forms.BindableObject.PropertyChanged) e il [`Switch`](xref:Xamarin.Forms.Switch) dispone di un gestore associato al relativo evento [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) . Questi gestori di eventi si trovano nel file code-behind e chiamano il metodo `SetTriggerTime`:

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

Il metodo `SetTriggerTime` calcola un'ora del timer basata sul valore della proprietà `DateTime.Today` e sul valore `TimeSpan` restituito dal [`TimePicker`](xref:Xamarin.Forms.TimePicker). Questa operazione è necessaria perché la proprietà `DateTime.Today` restituisce una `DateTime` che indica la data corrente, ma con un periodo di mezzanotte. Se l'ora del timer è già passata, si presuppone che sia domani.

Il timer esegue il ciclo ogni secondo, eseguendo il `OnTimerTick` metodo che controlla se il [`Switch`](xref:Xamarin.Forms.Switch) è attivato e se l'ora corrente è maggiore o uguale all'ora del timer. Quando si verifica l'ora del timer, il metodo [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) Visualizza una finestra di dialogo di avviso all'utente come promemoria.

Quando l'esempio viene eseguito per la prima volta, la vista [`TimePicker`](xref:Xamarin.Forms.TimePicker) viene inizializzata su 11. Toccando il `TimePicker` viene richiamato il selettore temporale della piattaforma. Le piattaforme implementano il selettore di tempo in modi molto diversi, ma ogni approccio è familiare agli utenti della piattaforma:

[![Seleziona ora](timepicker-images/timepicker-open.png "Seleziona ora")](timepicker-images/timepicker-open-large.png#lightbox "Seleziona ora")

> [!TIP]
> In Android è possibile personalizzare la finestra di dialogo [`TimePicker`](xref:Xamarin.Forms.TimePicker) eseguendo l'override del metodo `CreateTimePickerDialog` in un renderer personalizzato. Questo consente, ad esempio, di aggiungere pulsanti aggiuntivi alla finestra di dialogo.

Dopo aver selezionato un'ora, l'ora selezionata viene visualizzata nella [`TimePicker`](xref:Xamarin.Forms.TimePicker):

[![Ora selezionata](timepicker-images/timepicker-selected.png "Ora selezionata")](timepicker-images/timepicker-selected-large.png#lightbox "Ora selezionata")

Se la [`Switch`](xref:Xamarin.Forms.Switch) viene attivata o disattivata, l'applicazione visualizza una finestra di dialogo di avviso che ricorda l'utente del testo nel [`Entry`](xref:Xamarin.Forms.Entry) quando si verifica l'ora selezionata:

[![Popup timer](timepicker-images/timer-test.png "Popup timer")](timepicker-images/timer-test-large.png#lightbox "Popup timer")

Non appena viene visualizzata la finestra di dialogo di avviso, l' [`Switch`](xref:Xamarin.Forms.Switch) viene attivata o disattivata.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di setimer](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-timepicker)
- [API TimePicker](xref:Xamarin.Forms.TimePicker)
