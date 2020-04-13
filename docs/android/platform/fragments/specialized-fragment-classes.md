---
title: Classi di frammenti specializzate
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/08/2018
ms.openlocfilehash: b004fbf121374a2bb3bf5d85f45d8cae293573bf
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027306"
---
# <a name="specialized-fragment-classes"></a>Classi di frammenti specializzate

L'API Fragments fornisce altre sottoclassi che incapsulano alcune delle funzionalità più comuni presenti nelle applicazioni. Queste sottoclassi sono:

- **ListFragment** &ndash; Questo frammento viene utilizzato per visualizzare un elenco di elementi associati a un'origine dati, ad esempio una matrice o un cursore.

- **DialogFragment** &ndash; Questo frammento viene utilizzato come wrapper intorno a una finestra di dialogo. Il frammento visualizzerà la finestra di dialogo nella parte superiore della relativa attività.

- **PreferenceFragment** &ndash; Questo frammento viene utilizzato per visualizzare gli oggetti preferenza come elenchi.

## <a name="the-listfragment"></a>Il ListFragment

Il `ListFragment` è molto simile nel `ListActivity`concetto e funzionalità al ; è un wrapper che `ListView` ospita un in un frammento. L'immagine seguente `ListFragment` mostra una corsa su un tablet e un telefono:

[![Screenshot di ListFragment in un tablet e in un telefono](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)

### <a name="binding-data-with-the-listadapter"></a>Associazione di dati con ListAdapterBinding Data With The ListAdapter

La `ListFragment` classe fornisce già un layout predefinito, `OnCreateView` pertanto non è `ListFragment`necessario eseguire l'override per visualizzare il contenuto dell'oggetto . L'oggetto `ListView` viene associato `ListAdapter` ai dati tramite un'implementazione. Nell'esempio seguente viene illustrato come eseguire questa operazione utilizzando una semplice matrice di stringhe:The following example shows how this could be done by using a simple array of strings:

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

Quando si `ListAdapter`imposta l'impostazione `ListFragment.ListAdapter` di , `ListView.ListAdapter` è importante utilizzare la proprietà e non la proprietà . L'uso `ListView.ListAdapter` causerà l'ignoradel codice di inizializzazione importante.

### <a name="responding-to-user-selection"></a>Rispondere alla selezione dell'utente

Per rispondere alle selezioni dell'utente, `OnListItemClick` un'applicazione deve eseguire l'override del metodo. L'esempio seguente mostra una di queste possibilità:The following example shows one such a possibility:

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

Nel codice precedente, quando l'utente seleziona `ListFragment`un elemento in , un nuovo frammento viene visualizzato nell'attività di hosting, che mostra ulteriori dettagli sull'elemento selezionato.

## <a name="dialogfragment"></a>DialogFragment (Frammento di dialogo

Il *DialogFragment* è un frammento che viene utilizzato per visualizzare un oggetto finestra di dialogo all'interno di un frammento che rimarrà mobile nella parte superiore della finestra dell'attività. Ha lo scopo di sostituire le API della finestra di dialogo gestite (a partire da Android 3.0). La schermata seguente mostra `DialogFragment`un esempio di :The following screenshot shows an example of a:

[![Screenshot di DialogFragment che visualizza Aggiungi nuovo veicolo EditBox](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

A `DialogFragment` assicura che lo stato tra il frammento e la finestra di dialogo rimanga coerente. Tutte le interazioni e il controllo `DialogFragment` dell'oggetto finestra di dialogo devono avvenire tramite l'API e non essere effettuati con chiamate dirette sull'oggetto finestra di dialogo. L'API `DialogFragment` fornisce a `Show()` ogni istanza un metodo utilizzato per visualizzare un frammento. Esistono due modi per sbarazzarsi di un frammento:

- Chiamata `DialogFragment.Dismiss()` sull'istanza. `DialogFragment` 

- Visualizzare `DialogFragment`un altro file .

Per creare `DialogFragment`un oggetto , `Android.App.DialogFragment,` una classe eredita da e quindi esegue l'override di uno dei due metodi seguenti:

- **OnCreateView** &ndash; Crea e restituisce una visualizzazione.

- **OnCreateDialog** &ndash; Crea una finestra di dialogo personalizzata. Viene in genere utilizzato per visualizzare un *AlertDialog*. Quando si esegue l'override di `OnCreateView` questo metodo, non è necessario eseguire l'override di .

### <a name="a-simple-dialogfragment"></a>Un Simple DialogFragment

La schermata seguente `DialogFragment` mostra `TextView` un `Button`semplice che ha a e due s:The following screenshot shows a simple that has a and two s:

[![Esempio di DialogFragment con un TextView e due pulsantiExample DialogFragment with a TextView and two buttons](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

Verrà `TextView` visualizzato il numero di volte in cui l'utente ha fatto clic su un pulsante nell'altro, `DialogFragment`mentre si fa clic sull'altro pulsante, il frammento verrà chiuso. Il codice `DialogFragment` per è:

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

### <a name="displaying-a-fragment"></a>Visualizzazione di un frammentoDisplaying a Fragment

Come tutti i `DialogFragment` frammenti, un oggetto `FragmentTransaction`viene visualizzato nel contesto di un oggetto .

Il `Show()` metodo `DialogFragment` su `FragmentTransaction` un `string` accetta e un come input. La finestra di dialogo verrà `FragmentTransaction` aggiunta all'attività e al commit.

Il codice seguente illustra un possibile `Show()` modo in `DialogFragment`cui un'attività può usare il metodo per visualizzare un:The following code demonstrates one possible way an Activity may use the method to show a :

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```

### <a name="dismissing-a-fragment"></a>Eliminazione di un frammentoReing a Fragment

La `Dismiss()` chiamata a `DialogFragment` un'istanza di un causa la rimozione di un frammento dall'attività ed esegue il commit della transazione.
Verranno chiamati i metodi del ciclo di vita del frammento standard coinvolti nella distruzione di un frammento.

### <a name="alert-dialog"></a>Finestra di dialogo Avviso

Anziché eseguire `OnCreateView`l'override `OnCreateDialog`di , è invece possibile eseguire l'override di . `DialogFragment` Ciò consente a un'applicazione di creare un AlertDialog gestito da un Frammento.This allows an application to create an [AlertDialog](xref:Android.App.AlertDialog) that is managed by a Fragment. Il codice seguente è un `AlertDialog.Builder` esempio `Dialog`che usa l'oggetto per creare un oggetto :

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

## <a name="preferencefragment"></a>PreferenzaFrammento

Per facilitare la gestione delle `PreferenceFragment` preferenze, l'API Frammenti fornisce la sottoclasse. È `PreferenceFragment` simile al [PreferenceActivity](xref:Android.Preferences.PreferenceActivity) &ndash; mostrerà una gerarchia di preferenze per l'utente in un frammento. Quando l'utente interagisce con le preferenze, verranno salvate automaticamente in [SharedPreferences](https://developer.android.com/reference/android/content/SharedPreferences.html).
Nelle applicazioni Android 3.0 o `PreferenceFragment` versioni successive, utilizzare il per gestire le preferenze nelle applicazioni. L'immagine seguente mostra `PreferenceFragment`un esempio di :

[![Preferenze di esempioFrammento con preferenze in linea, finestra di dialogo e di avvio](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)

### <a name="create-a-preference-fragment-from-a-resource"></a>Creare un frammento di preferenza da una risorsaCreate A Preference Fragment from a Resource

La preferenza Frammento può essere inflated da un file di risorse XML utilizzando il [PreferenceFragment.AddPreferencesFromResource](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromResource*) metodo. Una posizione logica per chiamare questo metodo nel ciclo `OnCreate` di vita del frammento sarebbe nel metodo.

L'immagine `PreferenceFragment` precedente è stata creata caricando una risorsa da XML. Il file di risorse è:

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

Il codice per la preferenza Frammento è il seguente:The code for the preference Fragment is as follows:

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

### <a name="querying-activities-to-create-a-preference-fragment"></a>Esecuzione di query sulle attività per creare un frammento di preferenzaQuerying Activities to Create a Preference Fragment

Un'altra tecnica `PreferenceFragment` per la creazione di un prevede l'esecuzione di query sulle attività. Ogni attività può usare l'attributo [METADATA\_KEY\_PREFERENCE](xref:Android.Preferences.PreferenceManager.MetadataKeyPreferences) che punterà a un file di risorse XML. In Xamarin.Android, questa operazione viene eseguita `MetaDataAttribute`aggiungendo un'attività con il , e quindi specificando il file di risorse da utilizzare. La `PreferenceFragment` classe fornisce il metodo [AddPreferenceFromIntent](xref:Android.Preferences.PreferenceFragment.AddPreferencesFromIntent*) che può essere utilizzato per eseguire una query su un'attività per trovare questa risorsa XML e ingrandire una gerarchia di preferenze per tale risorsa.

Un esempio di questo processo viene fornito nel `AddPreferencesFromIntent` frammento `PreferenceFragment`di codice seguente, che usa per creare un:

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

Android guarderà la `MyActivityWithPreference`classe . La classe deve essere `MetaDataAttribute,` decorata con il come illustrato nel frammento di codice seguente:The class must be adorned with the shown in the following code snippet:

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

Dichiara `MetaDataAttribute` un file di risorse `PreferenceFragment` XML che verrà utilizzato per gonfiare la gerarchia delle preferenze. Se `MetatDataAttribute` l'oggetto non viene fornito, verrà generata un'eccezione in fase di esecuzione. Quando viene eseguito `PreferenceFragment` questo codice, viene visualizzato come nella schermata seguente:When this code runs, the appears as in the following screenshot:

[![Screenshot dell'app di esempio con PreferenceFragment visualizzato](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
