---
title: Selezione ora
description: Selezione dell'ora con TimePickerDialog e DialogFragment
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 37dd57b0f3264ed25d0a53632f312d30761f747b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029205"
---
# <a name="android-time-picker"></a>Selezione ora Android

Per consentire all'utente di selezionare un'ora, è possibile usare [TimePicker](xref:Android.Widget.TimePicker). Le app Android usano in genere `TimePicker` con [TimePickerDialog](xref:Android.App.TimePickerDialog) per la selezione di un valore di ora &ndash; questo consente di garantire un'interfaccia coerente tra dispositivi e applicazioni. `TimePicker` consente agli utenti di selezionare l'ora del giorno nella modalità AM/PM di 24 ore o 12 ore.
`TimePickerDialog` è una classe helper che incapsula i `TimePicker` in una finestra di dialogo.

[schermata di esempio![della finestra di dialogo selezione ora in azione](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>Panoramica

Le applicazioni Android moderne visualizzano il `TimePickerDialog` in un [DialogFragment](xref:Android.App.DialogFragment). In questo modo, un'applicazione può visualizzare la `TimePicker` come finestra popup o incorporarla in un'attività. Inoltre, il `DialogFragment` gestisce il ciclo di vita e la visualizzazione della finestra di dialogo, riducendo la quantità di codice che deve essere implementato.

In questa guida viene illustrato come utilizzare il `TimePickerDialog`, incluso in una `DialogFragment`. L'applicazione di esempio Visualizza il `TimePickerDialog` come finestra di dialogo modale quando l'utente fa clic su un pulsante in un'attività. Quando l'ora viene impostata dall'utente, la finestra di dialogo viene chiusa e un gestore aggiorna un `TextView` nella schermata attività con l'ora selezionata.

## <a name="requirements"></a>Requisiti

L'applicazione di esempio per questa guida è destinata a Android 4,1 (livello API
16) o versione successiva, ma è possibile usare con Android 3,0 (livello API 11 o superiore). È possibile supportare le versioni precedenti di Android con l'aggiunta della libreria del supporto Android V4 al progetto e alcune modifiche al codice.

## <a name="using-the-timepicker"></a>Uso di TimePicker

Questo esempio estende `DialogFragment`; l'implementazione della sottoclasse di `DialogFragment` (denominata `TimePickerFragment` sotto) ospita e visualizza un `TimePickerDialog`. Quando l'app di esempio viene avviata per la prima volta, viene visualizzato un pulsante **Seleziona tempo** sopra un `TextView` che verrà usato per visualizzare l'ora selezionata:

[schermata dell'app di esempio iniziale![](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Quando si fa clic sul pulsante **pick Time** , l'app di esempio avvia il `TimePickerDialog` come illustrato nello screenshot seguente:

[![screenshot della finestra di dialogo di selezione ora predefinita visualizzata dall'app](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

Nel `TimePickerDialog`, se si seleziona un'ora e si fa clic sul pulsante **OK** , il `TimePickerDialog` richiama il metodo [IOnTimeSetListener. ontimet](xref:Android.App.TimePickerDialog.IOnTimeSetListener.OnTimeSet*).
Questa interfaccia viene implementata dalla `DialogFragment` di hosting (`TimePickerFragment`, descritta di seguito). Se si fa clic sul pulsante **Annulla** , il frammento e la finestra di dialogo verranno rilasciati.

`DialogFragment` restituisce l'ora selezionata per l'attività di hosting in uno dei tre modi seguenti:

1. **Richiamando un metodo o impostando una proprietà** &ndash; l'attività è in grado di fornire una proprietà o un metodo specifico per l'impostazione di questo valore.

2. La **generazione di un evento** &ndash; `DialogFragment` può definire un evento che verrà generato quando `OnTimeSet` viene richiamato.

3. **Utilizzando un `Action`** &ndash; il `DialogFragment` può richiamare un `Action<DateTime>` per visualizzare l'ora dell'attività. L'attività fornirà il `Action<DateTime` quando si crea un'istanza del `DialogFragment`.

In questo esempio verrà utilizzata la terza tecnica, che richiede che l'attività fornisca un gestore di `Action<DateTime>` al `DialogFragment`.

## <a name="start-an-app-project"></a>Avviare un progetto di app

Avviare un nuovo progetto Android denominato **TimePickerDemo** (se non si ha familiarità con la creazione di progetti Xamarin.Android, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) per informazioni su come creare un nuovo progetto).

Modificare **Resources/layout/Main. aXML** e sostituirne il contenuto con il codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center_horizontal"
    android:padding="16dp">
    <Button
        android:id="@+id/select_button"
        android:paddingLeft="24dp"
        android:paddingRight="24dp"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="PICK TIME"
        android:textSize="20dp" />
    <TextView
        android:id="@+id/time_display"
        android:layout_height="wrap_content"
        android:layout_width="match_parent"
        android:paddingTop="22dp"
        android:text="Picked time will be displayed here"
        android:textSize="24dp" />
</LinearLayout>
```

Si tratta di un [LinearLayout](xref:Android.Widget.LinearLayout) di base con un oggetto [TextView](xref:Android.Widget.TextView) che Visualizza l'ora e un [pulsante](xref:Android.Widget.Button) che consente di aprire la `TimePickerDialog`. Si noti che questo layout usa stringhe e dimensioni hardcoded per semplificare la comprensione dell'app &ndash; un'app di produzione usa normalmente le risorse per questi valori (come si può notare nell'esempio di codice [DatePicker](https://github.com/xamarin/recipes/blob/master/Recipes/android/controls/datepicker/select_a_date/Resources/layout/Main.axml) ).

Modificare **MainActivity.cs** e sostituirne il contenuto con il codice seguente:

```csharp
using Android.App;
using Android.Widget;
using Android.OS;
using System;
using Android.Util;
using Android.Text.Format;

namespace TimePickerDemo
{
    [Activity(Label = "TimePickerDemo", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        TextView timeDisplay;
        Button timeSelectButton;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            SetContentView(Resource.Layout.Main);
            timeDisplay = FindViewById<TextView>(Resource.Id.time_display);
            timeSelectButton = FindViewById<Button>(Resource.Id.select_button);
        }
    }
}
```

Quando si compila ed esegue questo esempio, viene visualizzata una schermata iniziale simile alla schermata seguente:

[schermata iniziale dell'app![](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Facendo clic sul pulsante **Seleziona tempo** non viene eseguita alcuna operazione perché il `DialogFragment` non è ancora stato implementato per visualizzare il `TimePicker`.
Il passaggio successivo consiste nel creare questo `DialogFragment`.

## <a name="extending-dialogfragment"></a>Estensione di DialogFragment

Per estendere `DialogFragment` per l'uso con `TimePicker`, è necessario creare una sottoclasse derivata da `DialogFragment` e implementi `TimePickerDialog.IOnTimeSetListener`. Aggiungere la classe seguente a **MainActivity.cs**:

```csharp
public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
{
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };

    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }

    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }

    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
        Log.Debug(TAG, selectedTime.ToLongTimeString());
        timeSelectedHandler (selectedTime);
    }
}
```

Questa classe `TimePickerFragment` viene suddivisa in parti più piccole e illustrata nella sezione successiva.

### <a name="dialogfragment-implementation"></a>Implementazione di DialogFragment

`TimePickerFragment` implementa diversi metodi: un metodo factory, un metodo di creazione di istanze della finestra di dialogo e il metodo del gestore di `OnTimeSet` richiesto da `TimePickerDialog.IOnTimeSetListener`.

- `TimePickerFragment` è una sottoclasse di `DialogFragment`. Implementa inoltre l'interfaccia `TimePickerDialog.IOnTimeSetListener` (ovvero fornisce il metodo `OnTimeSet` obbligatorio):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

- `TAG` viene inizializzato a scopo di registrazione (*MyTimePickerFragment* può essere modificato in qualsiasi stringa che si vuole usare). L'azione `timeSelectedHandler` viene inizializzata su un delegato vuoto per impedire eccezioni di riferimento null:

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

- Il metodo factory `NewInstance` viene chiamato per creare un'istanza di una nuova `TimePickerFragment`. Questo metodo accetta un gestore `Action<DateTime>` richiamato quando l'utente fa clic sul pulsante **OK** nel `TimePickerDialog`:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

- Quando il frammento deve essere visualizzato, Android chiama il metodo di `DialogFragment` [OnCreateDialog](xref:Android.App.DialogFragment.OnCreateDialog*).
    Questo metodo crea un nuovo oggetto `TimePickerDialog` e lo inizializza con l'attività, l'oggetto callback (che rappresenta l'istanza corrente del `TimePickerFragment`) e l'ora corrente:

    ```csharp
    public override Dialog OnCreateDialog (Bundle savedInstanceState)
    {
        DateTime currentTime = DateTime.Now;
        bool is24HourFormat = DateFormat.Is24HourFormat(Activity);
        TimePickerDialog dialog = new TimePickerDialog
            (Activity, this, currentTime.Hour, currentTime.Minute, is24HourFormat);
        return dialog;
    }
    ```

- Quando l'utente modifica l'impostazione dell'ora nella finestra di dialogo `TimePicker`, viene richiamato il metodo `OnTimeSet`. `OnTimeSet` crea un oggetto `DateTime` utilizzando la data corrente e unisce nell'intervallo di tempo (ora e minuto) selezionato dall'utente:

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```

- Questo oggetto `DateTime` viene passato al `timeSelectedHandler` registrato con l'oggetto `TimePickerFragment` al momento della creazione. `OnTimeSet` richiama questo gestore per aggiornare la visualizzazione dell'ora dell'attività all'ora selezionata (questo gestore viene implementato nella sezione successiva):

    ```csharp
    timeSelectedHandler (selectedTime);
    ```

## <a name="displaying-the-timepickerfragment"></a>Visualizzazione di TimePickerFragment

Ora che l'`DialogFragment` è stata implementata, è necessario creare un'istanza della `DialogFragment` usando il metodo factory `NewInstance` e visualizzarla richiamando [DialogFragment. Show](xref:Android.App.DialogFragment.Show*):

Aggiungere il metodo seguente a `MainActivity`:

```csharp
void TimeSelectOnClick (object sender, EventArgs eventArgs)
{
    TimePickerFragment frag = TimePickerFragment.NewInstance (
        delegate (DateTime time)
        {
            timeDisplay.Text = time.ToShortTimeString();
        });

    frag.Show(FragmentManager, TimePickerFragment.TAG);
}
```

Dopo che `TimeSelectOnClick` crea un'istanza di un `TimePickerFragment`, crea e passa un delegato per un metodo anonimo che aggiorna la visualizzazione dell'ora dell'attività con il valore dell'ora passata. Infine, viene avviato il frammento di finestra di dialogo `TimePicker` (tramite `DialogFragment.Show`) per visualizzare il `TimePicker` all'utente.

Alla fine del metodo `OnCreate` aggiungere la riga seguente per allineare il gestore eventi al pulsante **pick Time** che avvia la finestra di dialogo:

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

Quando si fa clic sul pulsante **pick Time** , `TimeSelectOnClick` verrà richiamato per visualizzare il frammento della finestra di dialogo `TimePicker` per l'utente.

## <a name="try-it"></a>Prova

Compilare ed eseguire l'app. Quando si fa clic sul pulsante **pick Time** , il `TimePickerDialog` viene visualizzato nel formato di ora predefinito per l'attività (in questo caso, la modalità AM/PM di 12 ore):

[la finestra di dialogo![tempo viene visualizzata nella modalità AM/PM](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
Quando si fa clic su **OK** nella finestra di dialogo `TimePicker`, il gestore aggiorna il `TextView` dell'attività con il tempo scelto e quindi viene chiuso:

[![tempo A/M viene visualizzato nella TextView dell'attività](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

Successivamente, aggiungere la seguente riga di codice per `OnCreateDialog` immediatamente dopo la dichiarazione e l'inizializzazione di `is24HourFormat`:

```csharp
is24HourFormat = true;
```

Questa modifica impone la `true` del flag passato al costruttore `TimePickerDialog` in modo che venga utilizzata la modalità a 24 ore anziché il formato dell'ora dell'attività host. Quando si compila e si esegue di nuovo l'app, fare clic sul pulsante **Seleziona tempo** . la finestra di dialogo `TimePicker` viene ora visualizzata nel formato 24 ore:

[![la finestra di dialogo TimePicker nel formato 24 ore](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

Poiché il gestore chiama [DateTime. ToShortTimeString](xref:System.DateTime.ToShortDateString*) per stampare il tempo per l'`TextView`dell'attività, l'ora viene ancora stampata nel formato predefinito di 12 ore AM/PM.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come visualizzare un widget `TimePicker` come finestra di dialogo modale popup da un'attività Android. Fornisce un'implementazione di `DialogFragment` di esempio e illustra l'interfaccia `IOnTimeSetListener`. Questo esempio illustra anche come il `DialogFragment` può interagire con l'attività host per visualizzare l'ora selezionata.

## <a name="related-links"></a>Collegamenti correlati

- [DialogFragment](xref:Android.App.DialogFragment)
- [TimePicker](xref:Android.Widget.TimePicker)
- [TimePickerDialog](xref:Android.App.TimePickerDialog)
- [TimePickerDialog.IOnTimeSetListener](xref:Android.App.TimePickerDialog.IOnTimeSetListener)
- [TimePickerDemo (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-timepickerdemo)
