---
title: Selezione data
description: Selezione di date del calendario utilizzando il DatePickerDialog e DialogFragment
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/22/2018
ms.openlocfilehash: 9f82317f6041de3952d11b391afffafe6fbd8761
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122972"
---
# <a name="date-picker"></a>Selezione data

## <a name="overview"></a>Panoramica

Esistono alcuni casi quando un utente deve immettere dati in un'applicazione Android. Per semplificare questo, il framework Android offre il [ `DatePicker` ](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/) widget e il [ `DatePickerDialog` ](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/) . Il `DatePicker` consente agli utenti di selezionare l'anno, mese e giorno in un'interfaccia coerente tra dispositivi e applicazioni. Il `DatePickerDialog` è una classe helper che incapsula il `DatePicker` in una finestra di dialogo.

Le applicazioni Android moderni dovrebbero visualizzare le `DatePickerDialog` in un [ `DialogFragment` ](https://developer.xamarin.com/api/type/Android.App.DialogFragment/). Ciò consentirà a un'applicazione visualizzare il controllo DatePicker come una finestra di dialogo popup o incorporato in un'attività. Inoltre, il `DialogFragment` gestiranno i ciclo di vita e la visualizzazione della finestra di dialogo, riducendo la quantità di codice che deve essere implementata.

Questa guida illustra come usare il `DatePickerDialog`, inserito in un `DialogFragment`. L'applicazione di esempio visualizzerà il `DatePickerDialog` come una finestra di dialogo modale quando l'utente fa clic su un pulsante in un'attività. Quando la data viene impostata dall'utente, un `TextView` verrà aggiornato con la data in cui è stata selezionata.

[![Pulsante schermata di prelievo data seguito dalla finestra di dialogo di selezione data](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>Requisiti

L'applicazione di esempio in questa guida ha come destinazione (livello API di Android 4.1
16) o versione successiva, ma è applicabile a 3.0 Android (livello API 11 o versione successiva). È possibile supportare versioni precedenti di Android con l'aggiunta della libreria di supporto Android v4 al progetto e alcune modifiche al codice.

## <a name="using-the-datepicker"></a>Tramite il controllo DatePicker

In questo esempio verrà estesa `DialogFragment`. Sottoclasse ospitare e verrà visualizzato un `DatePickerDialog`:

![Finestra di dialogo di primo piano del controllo selezione data](date-picker-images/image-02.png)

Quando l'utente seleziona una data e fa clic il **OK** pulsante, il `DatePickerDialog` chiamerà il metodo [ `IOnDateSetListener.OnDateSet` ](https://developer.xamarin.com/api/member/Android.App.DatePickerDialog+IOnDateSetListener.OnDateSet/p/Android.Widget.DatePicker/System.Int32/System.Int32/System.Int32/).
Questa interfaccia viene implementata per l'hosting `DialogFragment`. Se l'utente fa clic il **annullare** pulsante frammento e finestra di dialogo se si preme autonomamente.

Esistono diversi modi di `DialogFragment` può restituire la data selezionata per l'attività di hosting:

1. **Richiamare un metodo o una proprietà impostata** &ndash; l'attività può fornire una proprietà o metodo in modo specifico per l'impostazione di questo valore.

2. **Generare un evento** &ndash; le `DialogFragment` possibile definire un evento che verrà generato quando `OnDateSet` viene richiamato.

3. **Usa un' `Action`**  &ndash; il `DialogFragment` può richiamare un `Action<DateTime>` per visualizzare la data dell'attività. L'attività fornirà il `Action<DateTime` quando si creano istanze il `DialogFragment`. In questo esempio verrà usata la terza tecnica e richiedono che l'attività di fornire un' `Action<DateTime>` per il `DialogFragment`.



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

Il `NewInstance` metodo viene richiamato per creare un'istanza di un nuovo `DatePickerFragment`. Questo metodo accetta un `Action<DateTime>` che verrà richiamato quando l'utente fa clic sui **OK** pulsante il `DatePickerDialog`.

Quando il frammento deve essere visualizzato, Android chiama il metodo `OnCreateDialog`. Questo metodo creerà una nuova `DatePickerDialog` dell'oggetto e inizializzarla con la data corrente e l'oggetto di callback (ovvero l'istanza corrente del `DatePickerFragment`).


> [!NOTE]
> Tenere presente che il valore del mese quando `IOnDateSetListener.OnDateSet` viene richiamata è compreso nell'intervallo da 0 a 11 e non da 1 a 12. Il giorno del mese sarà compreso nell'intervallo da 1 a 31 (a seconda di quale è stato selezionato mese).



### <a name="showing-the-datepickerfragment"></a>Che mostra il DatePickerFragment

Ora che il `DialogFragment` è stata implementata, questa sezione verrà esaminate come usare il frammento in un'attività. Nell'app di esempio che accompagna questa Guida, l'attività verrà creata un'istanza di `DialogFragment` usando il `NewInstance` metodo factory e quindi la visualizzazione, verrà richiamata `DialogFragment.Show`. Come parte di un'istanza di `DialogFragment`, l'attività passa un' `Action<DateTime>`, che consentirà di visualizzare la data in un `TextView` ospitato dall'attività:

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

In questo esempio è stato illustrato come visualizzare un `DatePicker` widget come finestra di dialogo modale popup come parte di un'attività Android. Fornito un esempio di implementazione DialogFragment e illustrate la `IOnDateSetListener` interfaccia. In questo esempio è illustrato anche come il DialogFragment può interagire con l'host di attività per visualizzare la data selezionata.


## <a name="related-links"></a>Collegamenti correlati

- [DialogFragment](https://developer.xamarin.com/api/type/Android.App.DialogFragment/)
- [DatePicker](https://developer.xamarin.com/api/type/Android.Widget.DatePicker/)
- [DatePickerDialog](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog/)
- [DatePickerDialog.IOnDateSetListener](https://developer.xamarin.com/api/type/Android.App.DatePickerDialog+IOnDateSetListener/)
- [Selezionare una data](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/datepicker/select_a_date)
