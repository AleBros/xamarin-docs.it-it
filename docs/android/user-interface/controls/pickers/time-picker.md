---
title: Selezione ora
description: Selezione di un'ora utilizzando TimePickerDialog e DialogFragment
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: c4261e3dccaccc4c88afe9c1033fb16b730fea6e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="time-picker"></a>Selezione ora

Per consentire all'utente di selezionare un intervallo di tempo, è possibile utilizzare [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/). In genere utilizzano le app Android `TimePicker` con [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/) per la selezione di un valore di ora &ndash; in questo modo per garantire un'interfaccia coerente tra dispositivi e applicazioni. `TimePicker` consente agli utenti di selezionare l'ora del giorno in modalità di AM/PM 24 ore o 12 ore.
`TimePickerDialog` è una classe helper che incapsula il `TimePicker` in una finestra di dialogo.

[![Schermata di esempio della finestra di dialogo di selezione ora azione](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>Panoramica

Visualizzano moderne applicazioni Android di `TimePickerDialog` in un [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/). Questo rende possibile per un'applicazione visualizzare il `TimePicker` come una finestra di dialogo popup oppure in un'attività. Inoltre, il `DialogFragment` gestisce il ciclo di vita e la visualizzazione della finestra di dialogo, riducendo la quantità di codice che deve essere implementata.

Questa guida viene illustrato come utilizzare il `TimePickerDialog`, è stato eseguito il wrapping in un `DialogFragment`. L'applicazione di esempio visualizza la `TimePickerDialog` come una finestra di dialogo modale quando l'utente sceglie un pulsante in un'attività. Quando l'ora viene impostato dall'utente, la finestra di dialogo viene chiusa e un gestore aggiorna un `TextView` nella schermata di attività con l'ora in cui è stato selezionato.

## <a name="requirements"></a>Requisiti

L'applicazione di esempio di questa guida è destinata Android 4.1 (livello API
16) o versione successiva, ma può essere usato con Android 3.0 (livello API 11 o versione successiva). È possibile supportare le versioni precedenti di Android con l'aggiunta della libreria di supporto Android v4 al progetto e alcune modifiche al codice.

## <a name="using-the-timepicker"></a>Utilizzo di TimePicker

In questo esempio estende `DialogFragment`; l'implementazione di sottoclasse di `DialogFragment` (chiamato `TimePickerFragment` sotto) ospita e visualizza un `TimePickerDialog`. Quando l'app di esempio innanzitutto viene avviata, viene visualizzato un **selezione ora** pulsante sopra un `TextView` che verrà usato per visualizzare l'ora selezionata:

[![Schermata dell'app di esempio iniziale](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Quando si fa clic il **selezione ora** pulsante, il lancio di app di esempio di `TimePickerDialog` come illustrato in questa schermata:

[![Schermata della finestra di dialogo di selezione ora predefinita visualizzata dall'app](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

Nel `TimePickerDialog`, selezionare un'ora e fare clic il **OK** pulsante cause di `TimePickerDialog` per richiamare il metodo [IOnTimeSetListener.OnTimeSet](https://developer.xamarin.com/api/member/Android.App.TimePickerDialog+IOnTimeSetListener.OnTimeSet/p/Android.Widget.TimePicker/System.Int32/System.Int32/System.Int32/).
Questa interfaccia viene implementata dal ospita `DialogFragment` (`TimePickerFragment`, descritto di seguito). Fare clic su di **Annulla** pulsante fa sì che il frammento e la finestra di dialogo è possibile ignorare.

`DialogFragment` Restituisce l'ora selezionata per l'hosting Actvity in uno dei tre modi:

1. **Richiama un metodo o l'impostazione di una proprietà** &ndash; l'attività può fornire una proprietà o metodo in modo specifico per l'impostazione di questo valore.

2. **Viene generato un evento** &ndash; il `DialogFragment` possibile definire un evento che verrà generato quando `OnTimeSet` viene richiamato.

3. **Utilizzando un `Action`**  &ndash; il `DialogFragment` può richiamare un `Action<DateTime>` per visualizzare l'ora dell'attività. L'attività fornirà il `Action<DateTime` quando si crea il `DialogFragment`. 

Questo esempio verrà utilizzato il terzo tecnica, che richiede la fornitura di attività un `Action<DateTime>` gestore per il `DialogFragment`.



## <a name="start-an-app-project"></a>Avviare un progetto di App

Avviare un nuovo progetto Android denominato **TimePickerDemo** (se non si ha familiarità con la creazione di progetti di xamarin, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) per imparare a creare un nuovo progetto).

Modifica **Resources/layout/Main.axml** e sostituirne il contenuto con il codice XML seguente:

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

Si tratta di un base [LinearLayout](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) con un [TextView](https://developer.xamarin.com/api/type/Android.Widget.TextView/) che visualizza il tempo e un [pulsante](https://developer.xamarin.com/api/type/Android.Widget.Button/) che consente di aprire il `TimePickerDialog`. Si noti che questo layout Usa stringhe hardcoded e le dimensioni per rendere l'app più semplice e più facile da comprendere &ndash; un'app di produzione di solito utilizza le risorse per questi valori (come si può notare nel [DatePicker](https://github.com/xamarin/recipes/blob/master/android/controls/datepicker/select_a_date/Resources/layout/Main.axml) esempio di codice).

Modifica **Mainactivity** e sostituirne il contenuto con il codice seguente:

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

Quando si compila e si esegue questo esempio, verrà visualizzata una schermata iniziale simile alla cattura di schermata seguente:

[![Schermata iniziale dell'App](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Fare clic sul **selezione ora** pulsante non esegue alcuna operazione perché il `DialogFragment` non è ancora stato implementato per visualizzare il `TimePicker`.
Il passaggio successivo consiste nel creare questa `DialogFragment`.



## <a name="extending-dialogfragment"></a>Estensione DialogFragment

Per estendere `DialogFragment` per l'utilizzo con `TimePicker`, è necessario creare una sottoclasse derivato da `DialogFragment` e implementa `TimePickerDialog.IOnTimeSetListener`. Aggiungere la seguente classe **Mainactivity**:

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

Questo `TimePickerFragment` classe è suddivisa in parti più piccole e descritto nella sezione successiva.


### <a name="dialogfragment-implementation"></a>Implementazione di DialogFragment

`TimePickerFragment` implementa diversi metodi: un metodo factory, un metodo di creazione dell'istanza, finestra di dialogo e `OnTimeSet` richiesto dal metodo del gestore `TimePickerDialog.IOnTimeSetListener`.

-   `TimePickerFragment` è una sottoclasse di `DialogFragment`. Implementa inoltre il `TimePickerDialog.IOnTimeSetListener` interfaccia (ovvero, fornisce la `OnTimeSet` (metodo)):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

-   `TAG` viene inizializzato per scopi di registrazione (*MyTimePickerFragment* può essere modificato per qualsiasi stringa di cui si desidera utilizzare). Il `timeSelectedHandler` azione viene inizializzata su un delegato vuoto per evitare eccezioni di riferimento null:

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

-   Il `NewInstance` viene chiamato il metodo factory per creare un'istanza di un nuovo `TimePickerFragment`. Questo metodo accetta un `Action<DateTime>` gestore che viene richiamato quando l'utente fa clic il **OK** pulsante il `TimePickerDialog`:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

-   Quando il frammento è visualizzata, Android chiama il `DialogFragment` metodo [OnCreateDialog](https://developer.xamarin.com/api/member/Android.App.DialogFragment.OnCreateDialog/p/Android.OS.Bundle/). 
    Questo metodo crea un nuovo `TimePickerDialog` dell'oggetto e la inizializza con l'attività, l'oggetto di callback (ovvero l'istanza corrente del `TimePickerFragment`) e l'ora corrente:

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

-   Quando l'utente modifica l'impostazione ora il `TimePicker` finestra di dialogo, il `OnTimeSet` metodo viene richiamato. `OnTimeSet` Crea un `DateTime` utilizzando la data corrente e l'unione nel periodo di tempo (ora e minuto) selezionati dall'utente:

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```


-   Questo `DateTime` oggetto viene passato per il `timeSelectedHandler` che è registrato con il `TimePickerFragment` oggetto al momento della creazione. `OnTimeSet` richiama il gestore per aggiornare la visualizzazione di tempo dell'attività fino all'ora selezionata (questo gestore è implementato nella sezione successiva):

    ```csharp
    timeSelectedHandler (selectedTime);
    ```



## <a name="displaying-the-timepickerfragment"></a>Visualizzazione di TimePickerFragment

Ora che il `DialogFragment` è stato implementato, è necessario creare un'istanza di `DialogFragment` utilizzando il `NewInstance` metodo factory e visualizzarla richiamando [DialogFragment.Show](https://developer.xamarin.com/api/member/Android.App.DialogFragment.Show/p/Android.App.FragmentManager/System.String/):

Aggiungere il seguente metodo alla `MainActivity`:

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

Dopo aver `TimeSelectOnClick` crea un'istanza di un `TimePickerFragment`, crea e passa un delegato per un metodo anonimo che Aggiorna visualizzazione dell'ora dell'attività con il valore passato nel tempo. Infine, viene avviato il `TimePicker` frammento finestra di dialogo (tramite `DialogFragment.Show`) per visualizzare il `TimePicker` all'utente.

Alla fine del `OnCreate` metodo, aggiungere la riga seguente per connettere il gestore dell'evento per il **selezione ora** pulsante che avvia la finestra di dialogo:

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

Quando il **selezione ora** pulsante `TimeSelectOnClick` verrà richiamato per visualizzare il `TimePicker` frammento finestra di dialogo per l'utente.



## <a name="try-it"></a>Prova

Compilare ed eseguire l'app. Quando si fa clic il **selezione ora** pulsante, il `TimePickerDialog` viene visualizzato nel formato di ora predefinito per l'attività (in modalità di AM/PM in questo caso, 12 ore):

[![Finestra di dialogo ora viene visualizzato in modalità di AM/PM](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
Quando fa clic su **OK** nel `TimePicker` finestra di dialogo, il gestore degli aggiornamenti dell'attività `TextView` con la data/ora scelta e quindi viene chiusa:

[![Viene visualizzata l'ora di A/M in TextView attività](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

Successivamente, aggiungere la seguente riga di codice per `OnCreateDialog` immediatamente dopo `is24HourFormat` viene dichiarata e inizializzata:

```csharp
is24HourFormat = true;
```

Questa modifica impone il flag passato per il `TimePickerDialog` costruttore essere `true` in modo che la modalità a 24 ore viene utilizzata anziché il formato dell'ora dell'attività di hosting. Quando si compila e si esegue nuovamente l'app, fare clic su di **selezione ora** pulsante, il `TimePicker` finestra di dialogo viene ora visualizzata nel formato 24 ore:

[![Finestra di dialogo TimePicker nel formato 24 ore](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

Poiché il gestore chiama [DateTime.ToShortTimeString](https://msdn.microsoft.com/en-us/library/system.datetime.toshortdatestring%28v=vs.110%29.aspx) per stampare il tempo necessario per l'attività `TextView`, l'ora venga stampato nel formato predefinito 12 ore AM/PM.



## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come visualizzare un `TimePicker` widget come finestra di dialogo modale popup da un'attività di Android. Fornito un esempio `DialogFragment` implementazione e discusso il `IOnTimeSetListener` interfaccia. In questo esempio viene illustrato anche come `DialogFragment` possono interagire con l'host di attività per visualizzare l'ora selezionata.


## <a name="related-links"></a>Collegamenti correlati

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/)
- [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/)
- [TimePickerDialog.IOnTimeSetListener](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog+IOnTimeSetListener/)
- [TimePickerDemo (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/TimePickerDemo)
