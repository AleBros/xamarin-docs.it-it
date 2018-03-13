---
title: Selezione data
description: Selezione di date del calendario utilizzando le DatePickerDialog DialogFragment
ms.topic: article
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/22/2018
ms.openlocfilehash: b62af404ce0d3f5dacc479682a3002af49e968d1
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="date-picker"></a>Selezione data

## <a name="overview"></a>Panoramica

Esistono casi quando un utente deve inserire dati in un'applicazione Android. Per agevolare, fornisce il framework Android di [ `DatePicker` ](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/) widget e [ `DatePickerDialog` ](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/) . Il `DatePicker` consente agli utenti di selezionare l'anno, mese e giorno in un'interfaccia coerente tra dispositivi e applicazioni. Il `DatePickerDialog` è una classe helper che incapsula il `DatePicker` in una finestra di dialogo.

Moderne applicazioni Android deve essere visualizzato il `DatePickerDialog` in un [ `DialogFragment` ](https://developer.xamarin.com/api/type/Android.App.DialogFragment/). Ciò consentirà a un'applicazione visualizzare il DatePicker come una finestra di dialogo popup o incorporato in un'attività. Inoltre, il `DialogFragment` gestiscono il ciclo di vita e la visualizzazione della finestra di dialogo, riducendo la quantità di codice che deve essere implementata.

Questa guida viene illustrato come utilizzare il `DatePickerDialog`, è stato eseguito il wrapping in un `DialogFragment`. L'applicazione di esempio verrà visualizzato il `DatePickerDialog` come una finestra di dialogo modale quando l'utente sceglie un pulsante in un'attività. Quando la data viene impostata dall'utente, un `TextView` verrà aggiornato con la data in cui è stata selezionata.

[![Pulsante schermata di selezionare Data seguita da una finestra di dialogo di selezione data](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>Requisiti

L'applicazione di esempio di questa guida è destinata Android 4.1 (livello API
16) o versione successiva, ma è applicabile a 3.0 Android (livello API 11 o versione successiva). È possibile supportare le versioni precedenti di Android con l'aggiunta della libreria di supporto Android v4 al progetto e alcune modifiche al codice.

## <a name="using-the-datepicker"></a>Utilizzo di DatePicker

In questo esempio verrà esteso `DialogFragment`. La sottoclasse ospiterà e visualizzare un `DatePickerDialog`:

![Finestra di dialogo di primo piano del controllo selezione data](date-picker-images/image-02.png)

Quando l'utente seleziona una data e seleziona il **OK** pulsante, il `DatePickerDialog` chiamerà il metodo [ `IOnDateSetListener.OnDateSet` ](https://developer.xamarin.com/api/member/Android.App.DatePickerDialog+IOnDateSetListener.OnDateSet/p/Android.Widget.DatePicker/System.Int32/System.Int32/System.Int32/).
Questa interfaccia viene implementata dal ospita `DialogFragment`. Se l'utente sceglie il **Annulla** pulsante, quindi frammento e finestra di dialogo verrà chiuso se stessi.

Esistono diversi modi di `DialogFragment` può restituire la data selezionata per l'attività di hosting:

1. **Richiamare un metodo o impostare una proprietà** &ndash; l'attività può fornire una proprietà o metodo in modo specifico per l'impostazione di questo valore.

2. **Generare un evento** &ndash; il `DialogFragment` possibile definire un evento che verrà generato quando `OnDateSet` viene richiamato.

3. **Utilizzare un `Action`**  &ndash; il `DialogFragment` può richiamare un `Action<DateTime>` per visualizzare la data dell'attività. L'attività fornirà il `Action<DateTime` quando si crea il `DialogFragment`. In questo esempio verrà utilizzata la terza tecnica e richiedono che l'attività di fornire un `Action<DateTime>` per il `DialogFragment`.



### <a name="extending-dialogfragment"></a>Estensione DialogFragment

Il primo passaggio nella visualizzazione di un `DatePickerDialog` consiste nella sottoclasse `DialogFragment` e che implementi il `IOnDateSetListener` interfaccia:

```csharp
public class DatePickerFragment : DialogFragment, 
                                  DatePickerDialog.IOnDateSetListener
{
    // TAG can be any string of your choice.
    public static readonly string TAG = "X:" + typeof (DatePickerFragment).Name.ToUpper();
    
    // Initialize this value to prevent NullReferenceExceptions.
    Action<DateTime> _dateSelectedHandler = delegate { };
    
    public static DatePickerFragment NewInstance(Action<DateTime> onDateSelected)
    {
        DatePickerFragment frag = new DatePickerFragment();
        frag._dateSelectedHandler = onDateSelected;
        return frag;
    }
    
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        DateTime currently = DateTime.Now;
        DatePickerDialog dialog = new DatePickerDialog(Activity, 
                                                       this, 
                                                       currently.Year, 
                                                       currently.Month - 1,
                                                       currently.Day);
        return dialog;
    }
    
    public void OnDateSet(DatePicker view, int year, int monthOfYear, int dayOfMonth)
    {
        // Note: monthOfYear is a value between 0 and 11, not 1 and 12!
        DateTime selectedDate = new DateTime(year, monthOfYear + 1, dayOfMonth);
        Log.Debug(TAG, selectedDate.ToLongDateString());
        _dateSelectedHandler(selectedDate);
    }
}
```

Il `NewInstance` metodo viene richiamato per creare un'istanza di un nuovo `DatePickerFragment`. Questo metodo accetta un `Action<DateTime>` che verrà richiamato quando l'utente fa clic su di **OK** pulsante il `DatePickerDialog`.

Quando il frammento è visualizzata, Android chiamerà il metodo `OnCreateDialog`. Questo metodo crea un nuovo `DatePickerDialog` dell'oggetto e inizializzarlo con la data corrente e l'oggetto di callback (ovvero l'istanza corrente del `DatePickerFragment`).


> [!NOTE]
> Tenere presente che il valore del mese quando `IOnDateSetListener.OnDateSet` viene richiamato è compreso nell'intervallo da 0 a 11 e non da 1 a 12. Il giorno del mese sarà compreso nell'intervallo da 1 a 31 (a seconda del mese è stato selezionato).



### <a name="showing-the-datepickerfragment"></a>Visualizzazione di DatePickerFragment

Ora che il `DialogFragment` è stato implementato, in questa sezione verrà esaminato come utilizzare il frammento in un'attività. Nell'applicazione di esempio in questa Guida, l'attività verrà creata un'istanza di `DialogFragment` utilizzando il `NewInstance` metodo factory e quindi visualizzare richiamare `DialogFragment.Show`. Come parte di un'istanza di `DialogFragment`, passa all'attività un `Action<DateTime>`, che visualizzerà la data in un `TextView` ospitato dall'attività:

```csharp
[Activity(Label = "@string/app_name", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
    TextView _dateDisplay;
    Button _dateSelectButton;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SetContentView(Resource.Layout.Main);

        _dateDisplay = FindViewById<TextView>(Resource.Id.date_display);
        _dateSelectButton = FindViewById<Button>(Resource.Id.date_select_button);
        _dateSelectButton.Click += DateSelect_OnClick;
    }

    void DateSelect_OnClick(object sender, EventArgs eventArgs)
    {
        DatePickerFragment frag = DatePickerFragment.NewInstance(delegate(DateTime time)
                                                                 {
                                                                     _dateDisplay.Text = time.ToLongDateString();
                                                                 });
        frag.Show(FragmentManager, DatePickerFragment.TAG);
    }
}
```


## <a name="summary"></a>Riepilogo

In questo esempio viene illustrato come visualizzare un `DatePicker` widget come finestra di dialogo modale popup come parte di un'attività di Android. Fornito un esempio di implementazione DialogFragment e descritti i `IOnDateSetListener` interfaccia. In questo esempio è illustrato anche come il DialogFragment può interagire con l'host di attività per visualizzare la data selezionata.


## <a name="related-links"></a>Collegamenti correlati

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [DatePicker](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/)
- [DatePickerDialog](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/)
- [DatePickerDialog.IOnDateSetListener](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog+IOnDateSetListener/)
- [Selezionare una data](https://github.com/xamarinhttps://developer.xamarin.com/recipes/tree/master/android/controls/datepicker/select_a_date)
