---
title: Selezione ora
description: Selezione dell'ora con TimePickerDialog e DialogFragment
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: fea96ab645b2d01b774f691402a5796eec1f1dba
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644961"
---
# <a name="android-time-picker"></a>Selezione ora Android

Per consentire all'utente di selezionare un'ora, è possibile usare [TimePicker](xref:Android.Widget.TimePicker). Le app Android usano `TimePicker` in genere con [TimePickerDialog](xref:Android.App.TimePickerDialog) per la selezione &ndash; di un valore di ora in modo da garantire un'interfaccia coerente tra dispositivi e applicazioni. `TimePicker`consente agli utenti di selezionare l'ora del giorno in modalità di 24 ore o 12 ore.
`TimePickerDialog`è una classe helper che incapsula `TimePicker` in una finestra di dialogo.

[![Schermata di esempio della finestra di dialogo selezione ora in azione](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>Panoramica

Le applicazioni Android moderne visualizzano `TimePickerDialog` in un [DialogFragment](xref:Android.App.DialogFragment). In questo modo, un'applicazione può visualizzare `TimePicker` come una finestra di dialogo popup o incorporarla in un'attività. Inoltre, `DialogFragment` gestisce il ciclo di vita e la visualizzazione della finestra di dialogo, riducendo la quantità di codice che deve essere implementato.

In questa guida viene illustrato come utilizzare `TimePickerDialog`l'oggetto, incluso `DialogFragment`in un oggetto. L'applicazione `TimePickerDialog` di esempio Visualizza come finestra di dialogo modale quando l'utente fa clic su un pulsante in un'attività. Quando l'ora viene impostata dall'utente, la finestra di dialogo viene chiusa e un gestore aggiorna `TextView` un oggetto nella schermata attività con l'ora selezionata.

## <a name="requirements"></a>Requisiti

L'applicazione di esempio per questa guida è destinata a Android 4,1 (livello API
16) o versione successiva, ma è possibile usare con Android 3,0 (livello API 11 o superiore). È possibile supportare le versioni precedenti di Android con l'aggiunta della libreria del supporto Android V4 al progetto e alcune modifiche al codice.

## <a name="using-the-timepicker"></a>Uso di TimePicker

Questo esempio estende `DialogFragment`; l'implementazione della sottoclasse ( `TimePickerFragment` denominata di `DialogFragment` seguito) ospita e `TimePickerDialog`Visualizza un. Quando l'app di esempio viene avviata per la prima volta, viene visualizzato un `TextView` pulsante **Seleziona tempo** sopra a che verrà usato per visualizzare l'ora selezionata:

[![Schermata iniziale dell'app di esempio](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Quando si fa clic sul pulsante **pick Time** , l'app di esempio `TimePickerDialog` avvia il come illustrato nello screenshot seguente:

[![Screenshot della finestra di dialogo di selezione ora predefinita visualizzata dall'app](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

Nella selezionare un'ora e fare clic sul pulsante **OK** fa in modo `TimePickerDialog` che il richiami il metodo [IOnTimeSetListener. ontimet.](xref:Android.App.TimePickerDialog.IOnTimeSetListener.OnTimeSet*) `TimePickerDialog`
Questa interfaccia viene implementata dall'host `DialogFragment` (`TimePickerFragment`, descritta di seguito). Se si fa clic sul pulsante **Annulla** , il frammento e la finestra di dialogo verranno rilasciati.

`DialogFragment`Restituisce l'ora selezionata per l'attività di hosting in uno dei tre modi seguenti:

1. **Richiamo di un metodo o impostazione di una proprietà** &ndash; L'attività può fornire una proprietà o un metodo specifico per l'impostazione di questo valore.

2. **Generazione di un evento** Può definire un evento che verrà generato quando `OnTimeSet` viene richiamato. &ndash; `DialogFragment`

3. **Utilizzando un `Action`**  &ndash; `Action<DateTime>` oggetto, puòrichiamareunoggettopervisualizzarel'oradell'attività.`DialogFragment` L'attività fornirà l' `Action<DateTime` oggetto quando si crea `DialogFragment`un'istanza di.

In questo esempio verrà utilizzata la terza tecnica, che richiede che l'attività fornisca `Action<DateTime>` un gestore a. `DialogFragment`

## <a name="start-an-app-project"></a>Avviare un progetto di app

Avviare un nuovo progetto Android denominato **TimePickerDemo** (se non si ha familiarità con la creazione di progetti Novell. Android, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) per informazioni su come creare un nuovo progetto).

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

Si tratta di un [LinearLayout](xref:Android.Widget.LinearLayout) di base con un oggetto [TextView](xref:Android.Widget.TextView) che Visualizza l'ora e un [pulsante](xref:Android.Widget.Button) che consente di aprire `TimePickerDialog`. Si noti che questo layout usa stringhe e dimensioni hardcoded per semplificare e semplificare la comprensione &ndash; di un'app di produzione, che in genere usa le risorse per questi valori (come si può notare nell'esempio di codice [DatePicker](https://github.com/xamarin/recipes/blob/master/Recipes/android/controls/datepicker/select_a_date/Resources/layout/Main.axml) ).

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

[![Schermata iniziale dell'app](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Facendo clic sul pulsante **pick Time** non viene eseguita `DialogFragment` alcuna operazione perché non è ancora stata `TimePicker`implementata per visualizzare.
Il passaggio successivo consiste nel creare questo `DialogFragment`oggetto.

## <a name="extending-dialogfragment"></a>Estensione di DialogFragment

Per estendere `DialogFragment` l'utilizzo con `TimePicker`, è necessario creare una sottoclasse derivata da `DialogFragment` e implementa `TimePickerDialog.IOnTimeSetListener`. Aggiungere la classe seguente a **MainActivity.cs**:

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

Questa `TimePickerFragment` classe viene suddivisa in parti più piccole e illustrata nella sezione successiva.

### <a name="dialogfragment-implementation"></a>Implementazione di DialogFragment

`TimePickerFragment`implementa diversi metodi: un metodo factory, un metodo di creazione di istanze della finestra `OnTimeSet` di dialogo e il `TimePickerDialog.IOnTimeSetListener`metodo del gestore richiesto da.

-   `TimePickerFragment`è una sottoclasse `DialogFragment`di. Implementa anche l' `TimePickerDialog.IOnTimeSetListener` interfaccia (ovvero fornisce il metodo richiesto `OnTimeSet` ):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

-   `TAG`viene inizializzato a scopo di registrazione (*MyTimePickerFragment* può essere modificato in qualsiasi stringa che si vuole usare). L' `timeSelectedHandler` azione viene inizializzata su un delegato vuoto per impedire eccezioni di riferimento null:

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

-   Il `NewInstance` metodo factory viene chiamato per creare un'istanza di `TimePickerFragment`un nuovo oggetto. Questo metodo accetta un `Action<DateTime>` gestore che viene richiamato quando l'utente fa clic sul pulsante **OK** in `TimePickerDialog`:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

-   Quando il frammento deve essere visualizzato, Android chiama il `DialogFragment` metodo [OnCreateDialog](xref:Android.App.DialogFragment.OnCreateDialog*).
    Questo metodo crea un nuovo `TimePickerDialog` oggetto e lo inizializza con l'attività, l'oggetto callback (che rappresenta l'istanza corrente `TimePickerFragment`di) e l'ora corrente:

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

-   Quando l'utente modifica l'impostazione dell'ora nella `TimePicker` finestra di dialogo `OnTimeSet` , viene richiamato il metodo. `OnTimeSet`consente di `DateTime` creare un oggetto utilizzando la data corrente e le unioni nell'intervallo di tempo (ora e minuto) selezionato dall'utente:

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```


-   Questo `DateTime` oggetto viene passato `timeSelectedHandler` all'oggetto registrato con l' `TimePickerFragment` oggetto al momento della creazione. `OnTimeSet`richiama questo gestore per aggiornare la visualizzazione dell'ora dell'attività all'ora selezionata (questo gestore viene implementato nella sezione successiva):

    ```csharp
    timeSelectedHandler (selectedTime);
    ```

## <a name="displaying-the-timepickerfragment"></a>Visualizzazione di TimePickerFragment

Ora che `DialogFragment` è stata implementata, è possibile creare un'istanza `DialogFragment` di usando il `NewInstance` Metodo Factory e visualizzarla richiamando [DialogFragment. Show](xref:Android.App.DialogFragment.Show*):

Aggiungere il seguente metodo a `MainActivity`:

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

Dopo `TimeSelectOnClick` la creazione di `TimePickerFragment`un'istanza di, viene creato e passato un delegato per un metodo anonimo che aggiorna la visualizzazione dell'ora dell'attività con il valore dell'ora passata. Infine, avvia il `TimePicker` frammento di finestra di dialogo (via `DialogFragment.Show`) `TimePicker` per visualizzare l'oggetto all'utente.

Alla fine del `OnCreate` metodo, aggiungere la riga seguente per alleghi il gestore eventi al pulsante **pick Time** che avvia la finestra di dialogo:

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

Quando si fa clic sul pulsante **pick Time** , `TimeSelectOnClick` viene richiamato per visualizzare il `TimePicker` frammento di finestra di dialogo per l'utente.

## <a name="try-it"></a>Prova

Compilare ed eseguire l'app. Quando si fa clic sul pulsante **pick Time** , `TimePickerDialog` il viene visualizzato nel formato di ora predefinito per l'attività (in questo caso, la modalità AM/PM a 12 ore):

[![La finestra di dialogo Time viene visualizzata in modalità AM/PM](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
Quando si fa clic su OK `TimePicker` nella finestra di dialogo, il gestore `TextView` aggiorna le attività con il tempo scelto e quindi viene chiuso:

[![Nell'attività TextView viene visualizzata l'ora a/M.](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

Successivamente, aggiungere la riga di codice `OnCreateDialog` seguente immediatamente dopo che `is24HourFormat` è stata dichiarata e inizializzata:

```csharp
is24HourFormat = true;
```

Questa modifica impone il flag passato al `TimePickerDialog` costruttore `true` in modo che venga utilizzata la modalità a 24 ore anziché il formato dell'ora dell'attività host. Quando si compila e si esegue di nuovo l'app, fare clic sul pulsante **pick Time** . la `TimePicker` finestra di dialogo viene ora visualizzata nel formato 24 ore:

[![Finestra di dialogo TimePicker in formato 24 ore](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

Poiché il gestore chiama [DateTime. ToShortTimeString](xref:System.DateTime.ToShortDateString*) per stampare l'ora per l'attività `TextView`, l'ora viene ancora stampata nel formato predefinito di 12 ore AM/PM.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come visualizzare un `TimePicker` widget come finestra di dialogo modale popup da un'attività Android. Fornisce un'implementazione di `DialogFragment` esempio e illustra l' `IOnTimeSetListener` interfaccia. Questo esempio illustra anche come `DialogFragment` può interagire con l'attività host per visualizzare l'ora selezionata.

## <a name="related-links"></a>Collegamenti correlati

- [DialogFragment](xref:Android.App.DialogFragment)
- [TimePicker](xref:Android.Widget.TimePicker)
- [TimePickerDialog](xref:Android.App.TimePickerDialog)
- [TimePickerDialog.IOnTimeSetListener](xref:Android.App.TimePickerDialog.IOnTimeSetListener)
- [TimePickerDemo (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-timepickerdemo)
