---
title: Classi di frammenti specializzate
ms.prod: xamarin
ms.assetid: 7A0AEB2C-EE77-63BF-652A-DA049B691C64
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 1011d74be971a3acba33c8f2f811e8f89e20cfc4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108444"
---
# <a name="specialized-fragment-classes"></a>Classi di frammenti specializzate

L'API di frammenti fornisce altri sottoclassi che incapsulano alcune delle funzionalità più comuni di applicazioni. Sono queste sottoclassi:

-   **ListFragment** &ndash; questo frammento viene usata per visualizzare un elenco di elementi associati a un'origine dati, ad esempio una matrice o un cursore.

-   **DialogFragment** &ndash; questo frammento viene usata come wrapper per una finestra di dialogo. Il frammento visualizzerà la finestra di dialogo sopra la propria attività.

-   **PreferenceFragment** &ndash; questo frammento viene usato per visualizzare gli oggetti di preferenza sotto forma di elenchi.



## <a name="the-listfragment"></a>Il ListFragment

Il `ListFragment` è molto simile nel concetto e le funzionalità per il `ListActivity`; è un wrapper che ospita un `ListView` in un frammento. L'immagine seguente mostra un `ListFragment` in esecuzione su un tablet e numero di telefono:

[![Screenshot di ListFragment su un tablet e in un telefono](specialized-fragment-classes-images/intro-screenshot-sml.png)](specialized-fragment-classes-images/intro-screenshot.png#lightbox)


### <a name="binding-data-with-the-listadapter"></a>Associazione di dati con il ListAdapter

Il `ListFragment` classe fornisce già un layout predefinito, pertanto non è necessario eseguire l'override `OnCreateView` per visualizzare il contenuto del `ListFragment`. Il `ListView` è associato a dati mediante un `ListAdapter` implementazione. Nell'esempio seguente viene illustrato come questa operazione usando una semplice matrice di stringhe:

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

Quando si imposta la `ListAdapter`, è importante usare il `ListFragment.ListAdapter` proprietà e non il `ListView.ListAdapter` proprietà. Usando `ListView.ListAdapter` causerà il codice di inizializzazione importante da ignorare.



### <a name="responding-to-user-selection"></a>Rispondere alla selezione dell'utente

Per rispondere alle selezioni dell'utente, è necessario eseguire l'override di un'applicazione di `OnListItemClick` (metodo). L'esempio seguente illustra una tale possibilità:

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

Nel codice precedente, quando l'utente seleziona un elemento di `ListFragment`, viene visualizzato un nuovo frammento nell'attività di hosting, che mostra altri dettagli sull'elemento che è stato selezionato.



## <a name="dialogfragment"></a>DialogFragment

Il *DialogFragment* è riportato un frammento che consente di visualizzare un oggetto finestra di dialogo all'interno di un frammento che verrà spostata sopra la finestra dell'attività. È destinato a sostituire la finestra di dialogo gestita API (a partire in Android 3.0). Lo screenshot seguente mostra un esempio di un `DialogFragment`:

[![Schermata di DialogFragment visualizzazione aggiungere nuovo EditBox del veicolo](specialized-fragment-classes-images/dialog-fragment-example.png)](specialized-fragment-classes-images/dialog-fragment-example.png#lightbox)

Oggetto `DialogFragment` assicura che lo stato tra il frammento e la finestra di dialogo rimanga coerente. Tutte le interazioni e controllo dell'oggetto finestra di dialogo dovrebbe verificarsi tramite il `DialogFragment` API e non essere effettuato con chiamate dirette per l'oggetto finestra di dialogo. Il `DialogFragment` API offre ogni istanza con un `Show()` metodo che consente di visualizzare un frammento. Esistono due modi per eliminare un frammento:

-  Chiamare `DialogFragment.Dismiss()` nella `DialogFragment` istanza. 

-  Visualizzare un altro `DialogFragment`.

Per creare un `DialogFragment`, una classe eredita da `Android.App.DialogFragment,` e quindi esegue l'override di uno dei due metodi seguenti:

- **OnCreateView** &ndash; ciò crea e restituisce una visualizzazione.

- **OnCreateDialog** &ndash; verrà creata una finestra di dialogo personalizzata. Viene in genere usato per mostrare un' *AlertDialog*. Quando si esegue l'override di questo metodo, non è necessario eseguire l'override `OnCreateView` .



### <a name="a-simple-dialogfragment"></a>Un semplice DialogFragment

Lo screenshot seguente mostra un semplice `DialogFragment` che ha un `TextView` e due `Button`s:

[![Esempio DialogFragment con un controllo TextView e due pulsanti](specialized-fragment-classes-images/dialog-fragment-example-2.png)](specialized-fragment-classes-images/dialog-fragment-example-2.png#lightbox)

Il `TextView` verrà visualizzato il numero di volte in cui l'utente ha fatto clic un pulsante di `DialogFragment`, mentre facendo clic sul pulsante altro chiuderà il frammento. Il codice per `DialogFragment` è:

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

Come tutti i frammenti, un `DialogFragment` viene visualizzato nel contesto di un `FragmentTransaction`.

Il `Show()` metodo su un `DialogFragment` accetta una `FragmentTransaction` e un `string` come input. La finestra di dialogo verrà aggiunto all'attività e il `FragmentTransaction` eseguito il commit.

Il codice seguente illustra uno dei possibili modi un'attività può usare la `Show()` metodo per visualizzare un `DialogFragment`:

```csharp
public void ShowDialog()
{
    var transaction = FragmentManager.BeginTransaction();
    var dialogFragment = new MyDialogFragment();
    dialogFragment.Show(transaction, "dialog_fragment");
}
```


### <a name="dismissing-a-fragment"></a>Eliminazione di un frammento

La chiamata `Dismiss()` in un'istanza di un `DialogFragment` fa sì che un frammento deve essere rimosso dall'attività ed esegue il commit della transazione.
Verranno chiamati i metodi del ciclo di vita frammento standard usati con l'eliminazione di un frammento.


### <a name="alert-dialog"></a>Finestra di dialogo Avviso

Invece di sostituire `OnCreateView`, una `DialogFragment` invece può eseguire l'override `OnCreateDialog`. In questo modo un'applicazione per creare un [AlertDialog](https://developer.xamarin.com/api/type/Android.App.AlertDialog/) gestito da un frammento. Il codice seguente è un esempio che usa il `AlertDialog.Builder` per creare un `Dialog`:

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

Per gestire le preferenze, fornisce l'API di frammenti di `PreferenceFragment` sottoclasse. Il `PreferenceFragment` è simile al [PreferenceActivity](https://developer.xamarin.com/api/type/Android.Preferences.PreferenceActivity/) &ndash; mostrerà una gerarchia di preferenze per l'utente in un frammento. Quando l'utente interagisce con le preferenze, essi verranno salvate automaticamente a [SharedPreferences](http://developer.android.com/reference/android/content/SharedPreferences.html).
In Android 3.0 o versioni successive delle applicazioni, usare il `PreferenceFragment` per gestire le preferenze nelle applicazioni. L'immagine seguente illustra un esempio di un `PreferenceFragment`:

[![Esempio PreferencesFragment inline, finestra di dialogo e le preferenze di avvio](specialized-fragment-classes-images/preferences-dialog.png)](specialized-fragment-classes-images/preferences-dialog.png#lightbox)


### <a name="create-a-preference-fragment-from-a-resource"></a>Creare un frammento di preferenza da una risorsa

La preferenza di frammento può essere sottoposto a inflating da un file di risorse XML utilizzando il [PreferenceFragment.AddPreferencesFromResource](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromResource/p/System.Int32/) (metodo). Una posizione logica per chiamare questo metodo del ciclo di vita del frammento sarebbe il `OnCreate` (metodo).

Il `PreferenceFragment` illustrato nella figura sopra è stato creato mediante il caricamento di una risorsa da XML. Il file di risorse è:

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

Il codice per la preferenza di frammento è come segue:

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



### <a name="querying-activities-to-create-a-preference-fragment"></a>L'esecuzione di query delle attività per creare un frammento di preferenza

Un'altra tecnica per la creazione di un `PreferenceFragment` comporta l'esecuzione di query le attività. Ogni attività è possono usare la [dei metadati\_chiave\_preferenza](https://developer.xamarin.com/api/field/Android.Preferences.PreferenceManager.MetadataKeyPreferences/) attributo che fanno riferimento a un file di risorse XML. In xamarin. Android, questa operazione viene eseguita la decorazione di un'attività con il `MetaDataAttribute`e quindi specificando il file di risorse da usare. Il `PreferenceFragment` classe fornisce il metodo [AddPreferenceFromIntent](https://developer.xamarin.com/api/member/Android.Preferences.PreferenceFragment.AddPreferencesFromIntent/p/Android.Content.Intent/)) che può essere utilizzato per eseguire una query per trovare la risorsa XML e una gerarchia di preferenza per tale aumento un'attività.

Un esempio di questo processo è disponibile nel frammento di codice seguente, che usa `AddPreferencesFromIntent` per creare un `PreferenceFragment`:

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

Android esaminerà la classe `MyActivityWithPreference`. La classe deve essere dotata di `MetaDataAttribute,` come illustrato nel frammento di codice seguente:

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

Il `MetaDataAttribute` dichiara che un file di risorse XML il `PreferenceFragment` userà la gerarchia delle preferenze di ingrandimento. Se il `MetatDataAttribute` non viene specificato, verrà generata un'eccezione in fase di esecuzione. Quando si esegue questo codice, il `PreferenceFragment` viene visualizzata come nello screenshot seguente:

[![Screenshot dell'app di esempio con PreferenceFragment visualizzato](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png)](specialized-fragment-classes-images/preference-fragment-getpreferencesfromintent.png#lightbox)
