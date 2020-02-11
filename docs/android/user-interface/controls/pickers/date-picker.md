---
title: Selezione data
description: Selezione delle date del calendario tramite DatePickerDialog e DialogFragment
ms.prod: xamarin
ms.assetid: F2BCD8D4-8957-EA53-C5A8-6BB603ADB47B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/22/2018
ms.openlocfilehash: b54a0795dee0bd7a02b419da497f9a1b3f3ee7ff
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029208"
---
# <a name="android-date-picker"></a>Selezione data Android

## <a name="overview"></a>Panoramica

In alcuni casi è necessario che un utente immetta i dati in un'applicazione Android. Per semplificare questa operazione, il Framework Android fornisce il widget [`DatePicker`](xref:Android.Widget.DatePicker) e il [`DatePickerDialog`](xref:Android.App.DatePickerDialog) . Il `DatePicker` consente agli utenti di selezionare l'anno, il mese e il giorno in un'interfaccia coerente tra dispositivi e applicazioni. Il `DatePickerDialog` è una classe helper che incapsula i `DatePicker` in una finestra di dialogo.

Le applicazioni Android moderne dovrebbero visualizzare il `DatePickerDialog` in un [`DialogFragment`](xref:Android.App.DialogFragment). Ciò consentirà a un'applicazione di visualizzare il controllo DatePicker come finestra popup o incorporata in un'attività. Inoltre, il `DialogFragment` gestirà il ciclo di vita e la visualizzazione della finestra di dialogo, riducendo la quantità di codice che deve essere implementato.

In questa guida viene illustrato come utilizzare il `DatePickerDialog`, incluso in una `DialogFragment`. L'applicazione di esempio visualizzerà il `DatePickerDialog` come finestra di dialogo modale quando l'utente fa clic su un pulsante in un'attività. Quando la data viene impostata dall'utente, un `TextView` verrà aggiornato con la data selezionata.

[![screenshot del pulsante Seleziona data seguito dalla finestra di dialogo Selezione data](date-picker-images/image-01-sml.png)](date-picker-images/image-01.png#lightbox)

## <a name="requirements"></a>Requisiti

L'applicazione di esempio per questa guida è destinata a Android 4,1 (livello API
16) o versione successiva, ma applicabile a Android 3,0 (livello API 11 o superiore). È possibile supportare le versioni precedenti di Android con l'aggiunta della libreria del supporto Android V4 al progetto e alcune modifiche al codice.

## <a name="using-the-datepicker"></a>Uso di DatePicker

Questo esempio estenderà `DialogFragment`. La sottoclasse ospita e visualizza un `DatePickerDialog`:

![Primo piano della finestra di dialogo di selezione data](date-picker-images/image-02.png)

Quando l'utente seleziona una data e fa clic sul pulsante **OK** , il `DatePickerDialog` chiamerà il metodo [`IOnDateSetListener.OnDateSet`](xref:Android.App.DatePickerDialog.IOnDateSetListener.OnDateSet*).
Questa interfaccia viene implementata dal `DialogFragment`di hosting. Se l'utente fa clic sul pulsante **Annulla** , il frammento e la finestra di dialogo si chiuderanno autonomamente.

La `DialogFragment` può restituire la data selezionata all'attività di hosting in diversi modi:

1. **Richiamare un metodo o impostare una proprietà** &ndash; l'attività può fornire una proprietà o un metodo specifico per l'impostazione di questo valore.

2. **Genera un evento** &ndash; l'`DialogFragment` può definire un evento che verrà generato quando `OnDateSet` viene richiamato.

3. **Usare un `Action`** &ndash; il `DialogFragment` può richiamare un `Action<DateTime>` per visualizzare la data nell'attività. L'attività fornirà il `Action<DateTime` quando si crea un'istanza del `DialogFragment`. In questo esempio verrà utilizzata la terza tecnica e l'attività fornirà un `Action<DateTime>` al `DialogFragment`.

### <a name="extending-dialogfragment"></a>Estensione di DialogFragment

Il primo passaggio per la visualizzazione di un `DatePickerDialog` consiste nella sottoclasse `DialogFragment` e nell'implementare l'interfaccia `IOnDateSetListener`:

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

Viene richiamato il metodo `NewInstance` per creare un'istanza di una nuova `DatePickerFragment`. Questo metodo accetta un `Action<DateTime>` che verrà richiamato quando l'utente fa clic sul pulsante **OK** nel `DatePickerDialog`.

Quando il frammento deve essere visualizzato, Android chiamerà il metodo `OnCreateDialog`. Questo metodo creerà un nuovo oggetto `DatePickerDialog` e lo inizializza con la data corrente e l'oggetto callback, ovvero l'istanza corrente del `DatePickerFragment`.

> [!NOTE]
> Tenere presente che il valore del mese in cui viene richiamato `IOnDateSetListener.OnDateSet` è compreso nell'intervallo da 0 a 11 e non da 1 a 12. Il giorno del mese sarà compreso tra 1 e 31 (a seconda del mese selezionato).

### <a name="showing-the-datepickerfragment"></a>Visualizzazione di DatePickerFragment

Ora che l'`DialogFragment` è stata implementata, in questa sezione verrà esaminato come utilizzare il frammento in un'attività. Nell'app di esempio che accompagna questa guida, l'attività creerà un'istanza del `DialogFragment` usando il metodo factory `NewInstance` e quindi visualizzerà il richiamo `DialogFragment.Show`. Nell'ambito della creazione di un'istanza del `DialogFragment`, l'attività passa una `Action<DateTime>`, che consente di visualizzare la data in una `TextView` ospitata dall'attività:

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

Questo esempio illustra come visualizzare un widget `DatePicker` come finestra di dialogo modale popup come parte di un'attività Android. Fornisce un'implementazione di DialogFragment di esempio e illustra l'interfaccia `IOnDateSetListener`. Questo esempio illustra anche come DialogFragment può interagire con l'attività host per visualizzare la data selezionata.

## <a name="related-links"></a>Collegamenti correlati

- [DialogFragment](xref:Android.App.DialogFragment)
- [DatePicker](xref:Android.Widget.DatePicker)
- [DatePickerDialog](xref:Android.App.DatePickerDialog)
- [DatePickerDialog.IOnDateSetListener](xref:Android.App.DatePickerDialog.IOnDateSetListener)
- [Selezionare una data](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/datepicker/select_a_date)
