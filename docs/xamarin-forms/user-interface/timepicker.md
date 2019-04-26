---
title: Controllo TimePicker xamarin. Forms
description: Il controllo TimePicker è una vista di xamarin. Forms che consente all'utente di selezionare un'ora. Questo articolo illustra come usare un controllo TimePicker in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: 2E99FB23-B82D-4EB4-AFB3-5002E736E7B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/16/2018
ms.openlocfilehash: 1b929b507d738cb4000bab20cfab5480b2222ed2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61024085"
---
# <a name="xamarinforms-timepicker"></a>Controllo TimePicker xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TimePicker/)

_Una vista di xamarin. Forms che consente all'utente di selezionare un'ora._

Xamarin. Forms [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) richiama controllo date time picker della piattaforma e consente all'utente di selezionare un'ora. `TimePicker` definisce le proprietà seguenti:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) typu `TimeSpan`, l'ora selezionata, che per impostazione predefinita un `TimeSpan` pari a 0. Il `TimeSpan` tipo indica un intervallo di tempo trascorsi dalla mezzanotte.
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) typu `string`, una [standard](/dotnet/standard/base-types/standard-date-and-time-format-strings/) oppure [personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings/) .NET la formattazione di stringa, impostazione predefinita è "t", il formato di ora breve.
- [`TextColor`](xref:Xamarin.Forms.TimePicker.TextColor) typu [ `Color` ](xref:Xamarin.Forms.Color), il colore utilizzato per visualizzare l'ora selezionata, che per impostazione predefinita [ `Color.Default` ](xref:Xamarin.Forms.Color.Default).
- [`FontAttributes`](xref:Xamarin.Forms.TimePicker.FontAttributes) typu [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes), che per impostazione predefinita [ `FontAtributes.None` ](xref:Xamarin.Forms.FontAttributes.None).
- [`FontFamily`](xref:Xamarin.Forms.TimePicker.FontFamily) typu `string`, che per impostazione predefinita `null`.
- [`FontSize`](xref:Xamarin.Forms.TimePicker.FontSize) di tipo `double`, che per impostazione predefinita -1,0.

Tutte queste proprietà sono supportate da [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) oggetti, che indica che essi possono essere personalizzati con stili e le proprietà possono essere destinazioni di associazioni dati. Il [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) proprietà ha una modalità di associazione predefinita [ `BindingMode.TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), il che significa che può essere una destinazione di un data binding in un'applicazione che usa il [ Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) architettura.

Il [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) non include un evento per indicare un nuovo oggetto selezionato [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) valore. Se si desidera ricevere una notifica di questo, è possibile aggiungere un gestore per il [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento.

## <a name="initializing-the-time-property"></a>La proprietà della fase di inizializzazione

Nel codice, è possibile inizializzare il [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) la proprietà su un valore di tipo `TimeSpan`:

```csharp
TimePicker timePicker = new TimePicker
{
  Time = new TimeSpan(4, 15, 26) // Time set to "04:15:26"
};
```

Quando la [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) specifica proprietà in XAML, il valore viene convertito in un `TimeSpan` e convalidate per assicurarsi che il numero di millisecondi è maggiore o uguale a 0 e che il numero di ore è minore di 24. I componenti della fase devono essere separati dai due punti:

```xaml
<TimePicker Time="4:15:26" />
```

Se il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) proprietà della [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) è impostata su un'istanza di un elemento ViewModel contenente una proprietà di tipo `TimeSpan` denominato `SelectedTime` (ad esempio), è possibile creare un'istanza il `TimePicker` simile al seguente:

```xaml
<TimePicker Time="{Binding SelectedTime}" />
```

In questo esempio, il [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) proprietà viene inizializzata la `SelectedTime` proprietà nel ViewModel. Poiché il `Time` proprietà dispone di una modalità di associazione del [ `TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay), qualsiasi nuova ora che l'utente seleziona vengono propagate automaticamente al ViewModel.

Se il [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) non contiene un'associazione nel relativo [ `Time` ](xref:Xamarin.Forms.TimePicker.Time) proprietà, un'applicazione deve associare un gestore per il [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) dell'evento Tieniti al corrente quando l'utente seleziona una nuova ora.

Per informazioni sull'impostazione delle proprietà del tipo di carattere, vedere [tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="timepicker-and-layout"></a>Controllo TimePicker e layout

È possibile usare, ad esempio un'opzione di layout orizzontale non vincolato `Center`, `Start`, o `End` con [ `TimePicker` ](xref:Xamarin.Forms.TimePicker):

```xaml
<TimePicker ···
            HorizontalOptions="Center"
            ··· />
```

Tuttavia, questa operazione è sconsigliata. A seconda dell'impostazione delle [ `Format` ](xref:Xamarin.Forms.TimePicker.Format) proprietà, selezionata volte potrebbero richiedere larghezze di visualizzazione diverse. Ad esempio, la stringa di formato "T" fa sì che il [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) visualizzazione per visualizzare i tempi in un formato esteso, e "4:15:26 AM" richiede una larghezza di visualizzazione maggiore rispetto al formato di ora breve ("t") di "4 5:15 di Mattina". A seconda della piattaforma, questa differenza potrebbe causare il `TimePicker` vista per modificare larghezza nel layout, o per la visualizzazione da troncare.

> [!TIP]
> È consigliabile usare il valore predefinito `HorizontalOptions` impostazione di `Fill` con [ `TimePicker` ](xref:Xamarin.Forms.TimePicker)e non deve usare una larghezza pari `Auto` quando si inserisce `TimePicker` in un `Grid` cella.

## <a name="timepicker-in-an-application"></a>Controllo TimePicker in un'applicazione

Il [ **SetTimer** ](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TimePicker/) l'esempio comprende [ `TimePicker` ](xref:Xamarin.Forms.TimePicker), [ `Entry` ](xref:Xamarin.Forms.Entry), e [ `Switch` ](xref:Xamarin.Forms.Switch) viste nella relativa pagina. Il `TimePicker` può essere utilizzato per selezionare un intervallo di tempo e se da allora viene generato viene visualizzata una finestra di dialogo di avviso che ricorda all'utente del testo nel `Entry`, disponibile il `Switch` è attivata o disattivata sulla. Ecco il file XAML:

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

Il [ `Entry` ](xref:Xamarin.Forms.Entry) consente di immettere testo promemoria che verrà visualizzato quando si verifica l'ora selezionata. Il [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) viene assegnato un [ `Format` ](xref:Xamarin.Forms.TimePicker.Format) proprietà dell'oggetto "T" per il formato di ora di tempo. Dispone di un gestore eventi associato ai [ `PropertyChanged` ](xref:Xamarin.Forms.BindableObject.PropertyChanged) evento e il [ `Switch` ](xref:Xamarin.Forms.Switch) ha un gestore di è collegato a relativo [ `Toggled` ](xref:Xamarin.Forms.Switch.Toggled) evento. Questi gestori eventi si trovano il file code-behind e chiamata di `SetTriggerTime` metodo:

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

Il `SetTriggerTime` metodo calcola un tempo di timer in base il `DateTime.Today` valore della proprietà e il `TimeSpan` valore restituito dal [ `TimePicker` ](xref:Xamarin.Forms.TimePicker). Questa operazione è necessaria perché il `DateTime.Today` proprietà restituisce un `DateTime` che indica la data corrente, ma con un tempo di mezzanotte. Se il timer è già trascorso oggi stesso, quindi si presuppone essere domani.

I segni di graduazione del timer ogni secondo, l'esecuzione di `OnTimerTick` metodo che verifica se il [ `Switch` ](xref:Xamarin.Forms.Switch) è d'e indica se l'ora corrente è maggiore o uguale all'ora di timer. Quando si verifica il tempo di timer, il [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) metodo presenta una finestra di dialogo di avviso all'utente un promemoria.

Alla prima esecuzione dell'esempio, il [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) visualizzazione viene inizializzata su 11 am. Se si tocca il `TimePicker` richiama la selezione di tempo della piattaforma. Le piattaforme di implementano il controllo di selezione ora in modi molto diversi, ma ogni approccio è noto agli utenti di tale piattaforma:

[![Selezionare ora](timepicker-images/timepicker-open.png "selezionare intervallo di tempo")](timepicker-images/timepicker-open-large.png#lightbox "selezionare ora")

> [!TIP]
> In Android, il [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) finestra di dialogo può essere personalizzato eseguendo l'override di `CreateTimePickerDialog` metodo in un renderer personalizzato. In questo modo, ad esempio, pulsanti aggiuntivi da aggiungere alla finestra di dialogo.

Dopo aver selezionato un'ora, l'ora selezionata viene visualizzato nei [ `TimePicker` ](xref:Xamarin.Forms.TimePicker):

[![Ora selezionata](timepicker-images/timepicker-selected.png "periodo selezionato")](timepicker-images/timepicker-selected-large.png#lightbox "periodo selezionato")

A condizione che il [ `Switch` ](xref:Xamarin.Forms.Switch) è attivata o disattivata nella posizione on, l'applicazione viene visualizzata una finestra di dialogo di avviso che indicano che l'utente del testo nel [ `Entry` ](xref:Xamarin.Forms.Entry) quando si verifica l'ora selezionata:

[![Timer Popup](timepicker-images/timer-test.png "Timer Popup")](timepicker-images/timer-test-large.png#lightbox "Timer Popup")

Non appena viene visualizzata la finestra di dialogo di avviso, il [ `Switch` ](xref:Xamarin.Forms.Switch) è attivata o disattivata sulla posizione off.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio SetTimer](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/TimePicker/)
- [Controllo TimePicker API](xref:Xamarin.Forms.TimePicker)
