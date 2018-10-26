---
title: Selezione ora
description: Selezione ora utilizzando TimePickerDialog e DialogFragment
ms.prod: xamarin
ms.assetid: EB4E8206-E8AD-9F04-AC1C-82AC9364A9DD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 911101d44e3ae401910b35b93d85ba3f79ddd35a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103985"
---
# <a name="time-picker"></a>Selezione ora

Per fornire un modo l'utente può selezionare un intervallo di tempo, è possibile usare [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/). Le app Android usano in genere `TimePicker` con [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/) per la selezione di un valore di ora &ndash; questa funzionalità garantisce un'interfaccia coerente tra dispositivi e applicazioni. `TimePicker` consente agli utenti di selezionare l'ora del giorno in modalità di 24 ore o a 12 ore AM/PM.
`TimePickerDialog` è una classe helper che incapsula il `TimePicker` in una finestra di dialogo.

[![Screenshot di esempio della finestra di dialogo di selezione ora in azione](time-picker-images/01-example-screen-sml.png)](time-picker-images/01-example-screen.png#lightbox)

## <a name="overview"></a>Panoramica

Visualizzano le applicazioni Android moderni il `TimePickerDialog` in un [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/). Questo rende possibile per un'applicazione visualizzare il `TimePicker` come finestra di dialogo popup o incorporarla in un'attività. Inoltre, il `DialogFragment` gestisce il ciclo di vita e la visualizzazione della finestra di dialogo, riducendo la quantità di codice che deve essere implementata.

Questa guida illustra come usare il `TimePickerDialog`, inserito in un `DialogFragment`. L'applicazione di esempio visualizza la `TimePickerDialog` come una finestra di dialogo modale quando l'utente fa clic su un pulsante in un'attività. Quando il tempo è impostato dall'utente, la finestra di dialogo viene chiusa e un gestore viene aggiornata una `TextView` nella schermata attività con l'ora in cui è stato selezionato.

## <a name="requirements"></a>Requisiti

L'applicazione di esempio in questa guida ha come destinazione (livello API di Android 4.1
16) o versione successiva, ma è utilizzabile con Android 3.0 (API livello 11 o versione successiva). È possibile supportare versioni precedenti di Android con l'aggiunta della libreria di supporto Android v4 al progetto e alcune modifiche al codice.

## <a name="using-the-timepicker"></a>Tramite il controllo TimePicker

In questo esempio estende `DialogFragment`; l'implementazione di sottoclasse di `DialogFragment` (chiamato `TimePickerFragment` di sotto) ospita e visualizza un `TimePickerDialog`. Quando l'app di esempio viene avviata prima di tutto, viene visualizzato un **ora PICK** pulsante sopra un `TextView` che verrà utilizzato per visualizzare l'ora selezionata:

[![Schermata di esempio iniziale dell'app](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Quando si fa clic il **PICK ora** pulsante avvii dell'app di esempio di `TimePickerDialog` come illustrato in questo screenshot:

[![Screenshot della finestra di dialogo di selezione di tempo predefiniti visualizzato dall'app](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)

Nel `TimePickerDialog`, selezionando un'ora e facendo clic sui **OK** pulsante causa il `TimePickerDialog` per richiamare il metodo [IOnTimeSetListener.OnTimeSet](https://developer.xamarin.com/api/member/Android.App.TimePickerDialog+IOnTimeSetListener.OnTimeSet/p/Android.Widget.TimePicker/System.Int32/System.Int32/System.Int32/).
Questa interfaccia viene implementata per l'hosting `DialogFragment` (`TimePickerFragment`, come descritto di seguito). Facendo clic sui **annullare** pulsante fa sì che il frammento e la finestra di dialogo per essere chiusa.

`DialogFragment` Restituisce l'ora selezionata per l'hosting Actvity in uno dei tre modi:

1. **Richiama un metodo o una proprietà di impostazione** &ndash; l'attività può fornire una proprietà o metodo in modo specifico per l'impostazione di questo valore.

2. **Generazione di un evento** &ndash; le `DialogFragment` possibile definire un evento che verrà generato quando `OnTimeSet` viene richiamato.

3. **Usando un `Action`**  &ndash; il `DialogFragment` può richiamare un `Action<DateTime>` per visualizzare l'ora nell'attività. L'attività fornirà il `Action<DateTime` quando si creano istanze il `DialogFragment`. 

Questo esempio verrà utilizzato la terza tecnica, che richiede la fornitura di attività un' `Action<DateTime>` gestore per il `DialogFragment`.



## <a name="start-an-app-project"></a>Avviare un progetto di App

Avviare un nuovo progetto Android chiamato **TimePickerDemo** (se non ha familiarità con la creazione di progetti xamarin. Android, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) per imparare a creare un nuovo progetto).

Modificare **Resources/layout/Main.axml** e sostituirne il contenuto con il codice XML seguente:

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

Si tratta di una semplice [LinearLayout](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) con un [TextView](https://developer.xamarin.com/api/type/Android.Widget.TextView/) che viene visualizzata l'ora e un [pulsante](https://developer.xamarin.com/api/type/Android.Widget.Button/) che si apre il `TimePickerDialog`. Si noti che questo layout Usa stringhe hardcoded e dimensioni per rendere l'app più semplice e facile da comprendere &ndash; un'app di produzione in genere Usa le risorse per questi valori (come si può notare nel [DatePicker](https://github.com/xamarin/recipes/blob/master/Recipes/android/controls/datepicker/select_a_date/Resources/layout/Main.axml) esempio di codice).

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

Quando si compila e si esegue questo esempio, verrà visualizzata una schermata iniziale simile allo screenshot seguente:

[![Schermata iniziale dell'App](time-picker-images/02-initial-app-screen-sml.png)](time-picker-images/02-initial-app-screen.png#lightbox)

Facendo clic sui **ora PICK** pulsante non esegue alcuna operazione perché il `DialogFragment` non è ancora stata implementata per visualizzare il `TimePicker`.
Il passaggio successivo consiste nel creare questo `DialogFragment`.



## <a name="extending-dialogfragment"></a>Estensione DialogFragment

Per estendere `DialogFragment` per l'utilizzo con `TimePicker`, è necessario creare una sottoclasse derivata da `DialogFragment` e implementa `TimePickerDialog.IOnTimeSetListener`. Aggiungere la classe seguente alla **MainActivity.cs**:

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

Ciò `TimePickerFragment` classe è suddivisa in parti più piccole e spiegato nella sezione successiva.


### <a name="dialogfragment-implementation"></a>Implementazione DialogFragment

`TimePickerFragment` implementa diversi metodi: un metodo factory, un metodo di creazione di istanze di dialogo e il `OnTimeSet` richiesto dal metodo del gestore `TimePickerDialog.IOnTimeSetListener`.

-   `TimePickerFragment` è una sottoclasse di `DialogFragment`. Implementa inoltre il `TimePickerDialog.IOnTimeSetListener` interface (vale a dire, fornisce la `OnTimeSet` (metodo)):

    ```csharp
    public class TimePickerFragment : DialogFragment, TimePickerDialog.IOnTimeSetListener
    ```

-   `TAG` viene inizializzato per scopi di registrazione (*MyTimePickerFragment* possono essere modificate in qualsiasi stringa da usare). Il `timeSelectedHandler` azione viene inizializzata su un delegato vuoto per evitare eccezioni di riferimento null:

    ```csharp
    public static readonly string TAG = "MyTimePickerFragment";
    Action<DateTime> timeSelectedHandler = delegate { };
    ```

-   Il `NewInstance` viene chiamato metodo factory per creare un'istanza di un nuovo `TimePickerFragment`. Questo metodo accetta un `Action<DateTime>` gestore che viene richiamato quando l'utente fa clic il **OK** pulsante la `TimePickerDialog`:

    ```csharp
    public static TimePickerFragment NewInstance(Action<DateTime> onTimeSelected)
    {
        TimePickerFragment frag = new TimePickerFragment();
        frag.timeSelectedHandler = onTimeSelected;
        return frag;
    }
    ```

-   Quando il frammento deve essere visualizzato, Android chiama il `DialogFragment` metodo [OnCreateDialog](https://developer.xamarin.com/api/member/Android.App.DialogFragment.OnCreateDialog/p/Android.OS.Bundle/). 
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

-   Quando l'utente modifica l'impostazione dell'ora nel `TimePicker` finestra di dialogo di `OnTimeSet` metodo viene richiamato. `OnTimeSet` Crea un `DateTime` utilizzando la data corrente e i merge nel periodo di tempo (ora e minuto) selezionati dall'utente:

    ```csharp
    public void OnTimeSet(TimePicker view, int hourOfDay, int minute)
    {
        DateTime currentTime = DateTime.Now;
        DateTime selectedTime = new DateTime(currentTime.Year, currentTime.Month, currentTime.Day, hourOfDay, minute, 0);
    ```


-   Ciò `DateTime` oggetto viene passato al `timeSelectedHandler` che viene registrato con il `TimePickerFragment` oggetto al momento della creazione. `OnTimeSet` richiama il gestore per aggiornare una visualizzazione cronologica dell'attività fino all'ora selezionata (questo gestore di è implementato nella sezione successiva):

    ```csharp
    timeSelectedHandler (selectedTime);
    ```



## <a name="displaying-the-timepickerfragment"></a>Visualizzazione di TimePickerFragment

Ora che il `DialogFragment` è stato implementato, è possibile creare un'istanza di `DialogFragment` usando il `NewInstance` metodo factory e visualizzarlo richiamando [DialogFragment.Show](https://developer.xamarin.com/api/member/Android.App.DialogFragment.Show/p/Android.App.FragmentManager/System.String/):

Aggiungere il metodo seguente alla `MainActivity`:

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

Dopo aver `TimeSelectOnClick` crea un'istanza di un `TimePickerFragment`, crea e passa un delegato per un metodo anonimo che Aggiorna visualizzazione cronologica dell'attività con il valore di ora passata. Infine, viene avviata il `TimePicker` frammento di finestra di dialogo (tramite `DialogFragment.Show`) per visualizzare il `TimePicker` all'utente.

Alla fine del `OnCreate` metodo, aggiungere la riga seguente per associare il gestore eventi per il **PICK ora** pulsante che avvia la finestra di dialogo:

```csharp
timeSelectButton.Click += TimeSelectOnClick;
```

Quando la **ora PICK** si fa clic sul pulsante, `TimeSelectOnClick` verrà richiamato per visualizzare il `TimePicker` frammento finestra di dialogo per l'utente.



## <a name="try-it"></a>Prova

Compilare ed eseguire l'app. Quando si fa clic il **ora PICK** pulsante, il `TimePickerDialog` viene visualizzata nel formato di ora predefinito per l'attività (in questo caso, 12 ore AM/PM in modalità):

[![Finestra di dialogo ora viene visualizzato in modalità AM/PM](time-picker-images/03-am-pm-time-dialog-sml.png)](time-picker-images/03-am-pm-time-dialog.png#lightbox)
   
Quando fa clic su **OK** nel `TimePicker` finestra di dialogo, il gestore degli aggiornamenti dell'attività `TextView` con il tempo selezionato e quindi viene chiusa:

[![Oggetto/M ora vengono visualizzate nei TextView attività](time-picker-images/04-after-time-dialog-sml.png)](time-picker-images/04-after-time-dialog.png#lightbox)

Successivamente, aggiungere la seguente riga di codice `OnCreateDialog` immediatamente dopo `is24HourFormat` viene dichiarata e inizializzata:

```csharp
is24HourFormat = true;
```

Questa modifica forza il flag passato per il `TimePickerDialog` costruttore sia `true` in modo che la modalità di 24 ore viene utilizzata anziché il formato di tempo dell'attività di hosting. Quando si compila ed eseguire nuovamente l'app, fare clic sui **ora PICK** pulsante, il `TimePicker` finestra di dialogo viene ora visualizzata nel formato 24 ore:

[![Finestra di dialogo di controllo TimePicker in formato 24 ore](time-picker-images/05-24hr-time-dialog-sml.png)](time-picker-images/05-24hr-time-dialog.png#lightbox)

Poiché il gestore chiama [DateTime.ToShortTimeString](https://msdn.microsoft.com/en-us/library/system.datetime.toshortdatestring%28v=vs.110%29.aspx) per stampare il tempo necessario per l'attività `TextView`, il tempo è ancora stampato nel formato predefinito 12 ore AM/PM.



## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come visualizzare un `TimePicker` widget come finestra di dialogo modale popup da un'attività Android. Fornito un esempio `DialogFragment` implementazione e illustrate la `IOnTimeSetListener` interfaccia. In questo esempio viene illustrato anche come il `DialogFragment` può interagire con l'host di attività per visualizzare l'ora selezionata.


## <a name="related-links"></a>Collegamenti correlati

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [TimePicker](https://developer.xamarin.com/api/type/Android.Widget.TimePicker/)
- [TimePickerDialog](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog/)
- [TimePickerDialog.IOnTimeSetListener](https://developer.xamarin.com/api/type/Android.App.TimePickerDialog+IOnTimeSetListener/)
- [TimePickerDemo (sample)](https://developer.xamarin.com/samples/monodroid/UserInterface/TimePickerDemo)
