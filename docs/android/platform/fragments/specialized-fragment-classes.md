---
title: Classi di frammenti specializzate
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 67fb12fd66d4d218f6e8bd40e21499e6c07e9e15
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510596"
---
# <a name="specialized-fragment-classes"></a>Classi di frammenti specializzate

L'API Fragments fornisce altre sottoclassi che incapsulano alcune delle funzionalità più comuni rilevate nelle applicazioni. Queste sottoclassi sono:

-   **ListFragment** &ndash; Questo frammento viene utilizzato per visualizzare un elenco di elementi associati a un'origine dati, ad esempio una matrice o un cursore.

-   **DialogFragment** &ndash; Questo frammento viene usato come wrapper intorno a una finestra di dialogo. Il frammento visualizzerà la finestra di dialogo sopra l'attività.

-   **PreferenceFragment** &ndash; Questo frammento viene utilizzato per mostrare gli oggetti preferenza come elenchi.



## <a name="the-listfragment"></a>ListFragment

È molto simile ai concetti e `ListActivity`alle funzionalità di. si tratta di un wrapper che ospita un `ListView` oggetto in un frammento. `ListFragment` L'immagine seguente mostra un `ListFragment` in esecuzione su un tablet e un telefono:

[![Screenshot di ListFragment su un tablet e su un telefono](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)


### <a name="binding-data-with-the-listadapter"></a>Associazione di dati a ListAdapter

La `ListFragment` classe fornisce già un layout predefinito, pertanto non è necessario eseguire l'override `OnCreateView` di per `ListFragment`visualizzare il contenuto di. L' `ListView` oggetto è associato ai dati tramite un' `ListAdapter` implementazione di. Nell'esempio seguente viene illustrato come eseguire questa operazione utilizzando una semplice matrice di stringhe:

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    string[] values = new[] { "Android", "iPhone", "WindowsMobile",
                "Blackberry", "WebOS", "Ubuntu", "Windows7", "Max OS X",
                "Linux", "OS/2" };
    this.ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleExpandableListItem1, values);
}
```

Quando si imposta `ListAdapter`, è importante usare la `ListFragment.ListAdapter` proprietà e non la `ListView.ListAdapter` proprietà. L' `ListView.ListAdapter` uso di provocherà l'omissione di un codice di inizializzazione importante.



### <a name="responding-to-user-selection"></a>Risposta alla selezione dell'utente

Per rispondere alle selezioni degli utenti, un'applicazione deve eseguire l' `OnListItemClick` override del metodo. Nell'esempio seguente viene illustrata una di queste possibilità:

```csharp
public override void OnListItemClick(ListView l, View v, int index, long id)
{
    // We can display everything in place with fragments.
    // Have the list highlight this item and show the data.
    ListView.SetItemChecked(index, true);

    // Check what fragment is shown, replace if needed.
    var details = FragmentManager.FindFragmentById<DetailsFragment>(Resource.Id.details);
    if (details == null || details.ShownIndex != index)
    {
        // Make new fragment to show this selection.
        details = DetailsFragment.NewInstance(index);

        // Execute a transaction, replacing any existing
        // fragment with this one inside the frame.
        var ft = FragmentManager.BeginTransaction();
        ft.Replace(Resource.Id.details, details);
        ft.SetTransition(FragmentTransit.FragmentFade);
        ft.Commit();
    }
}
```

Nel codice precedente, quando l'utente seleziona un elemento in `ListFragment`, viene visualizzato un nuovo frammento nell'attività host, che Mostra ulteriori dettagli sull'elemento selezionato.



## <a name="dialogfragment"></a>DialogFragment

*DialogFragment* è un frammento utilizzato per visualizzare un oggetto finestra di dialogo all'interno di un frammento che verrà spostato nella parte superiore della finestra dell'attività. Ha lo scopo di sostituire le API della finestra di dialogo gestita (a partire da Android 3,0). Lo screenshot seguente mostra un esempio di `DialogFragment`:

[![Screenshot di DialogFragment visualizzazione di Aggiungi nuovo veicolo casella](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

Un `DialogFragment` oggetto garantisce che lo stato tra il frammento e la finestra di dialogo rimanga coerente. Tutte le interazioni e il controllo dell'oggetto finestra di dialogo devono `DialogFragment` essere eseguiti tramite l'API e non devono essere eseguiti con chiamate dirette sull'oggetto finestra di dialogo. L' `DialogFragment` API fornisce a ogni istanza un `Show()` metodo utilizzato per visualizzare un frammento. Esistono due modi per eliminare un frammento:

-  `DialogFragment.Dismiss()` Chiamare`DialogFragment` sull'istanza. 

-  Visualizzare un `DialogFragment`altro.

Per creare un `DialogFragment`oggetto, una classe eredita `Android.App.DialogFragment,` da e quindi esegue l'override di uno dei due metodi seguenti:

- **OnCreateView** &ndash; Viene creata e restituita una visualizzazione.

- **OnCreateDialog** &ndash; Viene creata una finestra di dialogo personalizzata. Viene in genere usato per visualizzare un *AlertDialog*. Quando si esegue l'override di questo metodo, non è necessario `OnCreateView` eseguire l'override di.



### <a name="a-simple-dialogfragment"></a>DialogFragment semplice

Lo screenshot seguente mostra un semplice `DialogFragment` con un `TextView` e due `Button`s:

[![Esempio di DialogFragment con TextView e due pulsanti](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

Consente di visualizzare il numero di volte `DialogFragment`in cui l'utente ha fatto clic su un pulsante in, mentre facendo clic sul pulsante altro il frammento viene chiuso. `TextView` Il codice per `DialogFragment` è:

```csharp
public class MyDialogFragment : DialogFragment
{
    private int _clickCount;
    public override void OnCreate(Bundle savedInstanceState)
    {
        _clickCount = 0;
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState)
        
        var view = inflater.Inflate(Resource.Layout.dialog_fragment_layout, container, false);
        var textView = view.FindViewById<TextView>(Resource.Id.dialog_text_view);
            
        view.FindViewById<Button>(Resource.Id.dialog_button).Click += delegate
            {
                textView.Text = "You clicked the button " + _clickCount++ + " times.";
            };

        // Set up a handler to dismiss this DialogFragment when this button is clicked.
        view.FindViewById<Button>(Resource.Id.dismiss_dialog_button).Click += (sender, args) => Dismiss();
        return view;
        }
    }
}
```


### <a name="displaying-a-fragment"></a>Visualizzazione di un frammento

Come tutti i frammenti, un `DialogFragment` oggetto viene visualizzato nel contesto di un `FragmentTransaction`oggetto.

Il `Show()` metodo su un `DialogFragment` oggetto accetta `FragmentTransaction` un oggetto `string` e un oggetto come input. La finestra di dialogo verrà aggiunta all'attività e l'oggetto `FragmentTransaction` di cui è stato eseguito il commit.

Il codice seguente illustra un possibile modo in cui un'attività può `Show()` usare il metodo per `DialogFragment`visualizzare un oggetto:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```


### <a name="dismissing-a-fragment"></a>Chiusura di un frammento

La `Dismiss()` chiamata a su un'istanza `DialogFragment` di un oggetto comporta la rimozione di un frammento dall'attività e il commit della transazione.
Verranno chiamati i metodi del ciclo di vita dei frammenti standard interessati dalla distruzione di un frammento.


### <a name="alert-dialog"></a>Finestra di dialogo di avviso

Anziché eseguire l'override `OnCreateView`di, `DialogFragment` un oggetto può `OnCreateDialog`invece eseguire l'override di. Questo consente a un'applicazione di creare un [AlertDialog](xref:Android.App.AlertDialog) gestito da un frammento. Il codice seguente è un esempio che usa `AlertDialog.Builder` per creare un oggetto: `Dialog`

```csharp
public class AlertDialogFragment : DialogFragment
{
    public override Dialog OnCreateDialog(Bundle savedInstanceState)
    {
        EventHandler<DialogClickEventArgs> okhandler;
        var builder = new AlertDialog.Builder(Activity)
            .SetMessage("This is my dialog.")
            .SetPositiveButton("Ok", (sender, args) =>
                                         {
                                             // Do something when this button is clicked.
                                         })
            .SetTitle("Custom Dialog");
        return builder.Create();
    }
}
```



## <a name="preferencefragment"></a>PreferenceFragment

Per semplificare la gestione delle preferenze, l'API dei frammenti `PreferenceFragment` fornisce la sottoclasse. È simile a [PreferenceActivity](xref:Android.Preferences.PreferenceActivity) &ndash; , in cui verrà visualizzata una gerarchia di preferenze per l'utente in un frammento. `PreferenceFragment` Quando l'utente interagisce con le preferenze, viene salvato automaticamente in [SharedPreferences](https://developer.android.com/reference/android/content/SharedPreferences.html).
In applicazioni Android 3,0 o versioni successive, usare `PreferenceFragment` per gestire le preferenze nelle applicazioni. Nell'immagine seguente viene illustrato un esempio di `PreferenceFragment`:

[![Esempio di PreferencesFragment con preferenze inline, Dialog e Launch](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)


### <a name="create-a-preference-fragment-from-a-resource"></a>Creazione di un frammento di preferenza da una risorsa

Il frammento di preferenza può essere ingrandito da un file di risorse XML usando il metodo [PreferenceFragment. AddPreferencesFromResource](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromResource*) . Una posizione logica per chiamare questo metodo nel ciclo di vita del frammento sarà nel `OnCreate` metodo.

Il `PreferenceFragment` disegno precedente è stato creato caricando una risorsa da XML. Il file di risorse è:

```xml
<?xml version="1.0" encoding="utf-8"?>

<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">

  <PreferenceCategory android:title="Inline Preferences">
    <CheckBoxPreference android:key="checkbox_preference"
                        android:title="Checkbox Preference Title"
                        android:summary="Checkbox Preference Summary" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Dialog Based Preferences">

    <EditTextPreference android:key="edittext_preference"
                        android:title="EditText Preference Title"
                        android:summary="EditText Preference Summary"
                        android:dialogTitle="Edit Text Preferrence Dialog Title" />

  </PreferenceCategory>

  <PreferenceCategory android:title="Launch Preferences">

    <!-- This PreferenceScreen tag serves as a screen break (similar to page break
             in word processing). Like for other preference types, we assign a key
             here so it is able to save and restore its instance state. -->
    <PreferenceScreen android:key="screen_preference"
                      android:title="Title Screen Preferences"
                      android:summary="Summary Screen Preferences">

      <!-- You can place more preferences here that will be shown on the next screen. -->

      <CheckBoxPreference android:key="next_screen_checkbox_preference"
                          android:title="Next Screen Toggle Preference Title"
                          android:summary="Next Screen Toggle Preference Summary" />

    </PreferenceScreen>

    <PreferenceScreen android:title="Intent Preference Title"
                      android:summary="Intent Preference Summary">

      <intent android:action="android.intent.action.VIEW"
              android:data="http://www.android.com" />

    </PreferenceScreen>

  </PreferenceCategory>

</PreferenceScreen>
```

Il codice per il frammento di preferenza è il seguente:

```csharp
public class PrefFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        AddPreferencesFromResource(Resource.Xml.preferences);
    }
}
```



### <a name="querying-activities-to-create-a-preference-fragment"></a>Esecuzione di query sulle attività per creare un frammento di preferenza

Un'altra tecnica per la `PreferenceFragment` creazione di un oggetto consiste nell'esecuzione di query sulle attività. Ogni attività può utilizzare l'attributo di [preferenza della\_chiave\_dei metadati](xref:Android.Preferences.PreferenceManager.MetadataKeyPreferences) che punterà a un file di risorse XML. In Novell. Android questa operazione viene eseguita decorando un'attività con `MetaDataAttribute`e quindi specificando il file di risorse da usare. La `PreferenceFragment` classe fornisce il metodo [AddPreferenceFromIntent](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromIntent*) che può essere usato per eseguire una query su un'attività per trovare questa risorsa XML e per ingrandire una gerarchia di preferenze.

Un esempio di questo processo è disponibile nel frammento di codice seguente, che `AddPreferencesFromIntent` USA per creare `PreferenceFragment`un oggetto:

```csharp
public class MyPreferenceFragment : PreferenceFragment
{
    public override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        var intent = new Intent(this.Activity, typeof (MyActivityWithPreferences));
        AddPreferencesFromIntent(intent);
    }
}
```

Android esaminerà la classe `MyActivityWithPreference`. La classe deve essere decorata con `MetaDataAttribute,` , come illustrato nel frammento di codice seguente:

```csharp
[Activity(Label = "My Activity with Preferences")]
[MetaData(PreferenceManager.MetadataKeyPreferences, Resource = "@xml/preference_from_intent")]
public class MyActivityWithPreferences : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        // This is deliberately blank
    }
}
```

Dichiara un file di risorse XML `PreferenceFragment` che userà da per ingrandire la gerarchia delle preferenze. `MetaDataAttribute` Se non `MetatDataAttribute` viene fornito, verrà generata un'eccezione in fase di esecuzione. Quando viene eseguito questo codice, `PreferenceFragment` viene visualizzato come nello screenshot seguente:

[![Screenshot dell'app di esempio con PreferenceFragment visualizzato](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
