---
title: Uso del controllo ListView in Novell. Android
description: ListView è un importante componente dell'interfaccia utente delle applicazioni Android. viene usato ovunque, da brevi elenchi di opzioni di menu a lunghi elenchi di contatti o Preferiti Internet. Fornisce un modo semplice per presentare un elenco di righe di scorrimento che può essere formattato con uno stile incorporato o personalizzato in modo esteso.
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 79f97382b6d8bd7875e4921c859f92cd6b321687
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646316"
---
# <a name="xamarinandroid-listview"></a>ListView Novell. Android

_ListView è un importante componente dell'interfaccia utente delle applicazioni Android. viene usato ovunque, da brevi elenchi di opzioni di menu a lunghi elenchi di contatti o Preferiti Internet. Fornisce un modo semplice per presentare un elenco di righe di scorrimento che può essere formattato con uno stile incorporato o personalizzato in modo esteso._

## <a name="overview"></a>Panoramica

Le visualizzazioni elenco e gli adapter sono inclusi nei blocchi predefiniti più importanti delle applicazioni Android. La `ListView` classe offre un modo flessibile per presentare i dati, sia che si tratti di un menu breve o di un elenco a scorrimento lungo. Fornisce funzionalità di usabilità quali scorrimento rapido, indici e selezione singola o multipla per semplificare la creazione di interfacce utente per dispositivi mobili per le applicazioni. Un'istanza di `ListView` richiede un *Adapter* da cui ricevere i dati presenti nelle visualizzazioni di righe.

Questa guida illustra come implementare `ListView` e le varie `Adapter` classi in Novell. Android. Viene inoltre illustrato come personalizzare l'aspetto di un oggetto `ListView`e viene illustrata l'importanza del riutilizzo delle righe per ridurre l'utilizzo della memoria. Si discute anche del modo in cui il ciclo di vita `ListView` dell' `Adapter` attività influisca e USA. Se si lavora `ListView` su applicazioni multipiattaforma con Novell. iOS, il controllo è strutturalmente simile a quello di iOS `UITableView` (e `UITableViewSource`Android `Adapter` è simile a).

`ListView` In primo luogo, una breve esercitazione introduce con un esempio di codice di base. Vengono quindi forniti collegamenti ad argomenti più avanzati che consentono di usare `ListView` in app reali.

> [!NOTE]
> Il `RecyclerView` widget è una versione più avanzata e flessibile di `ListView`. Poiché `RecyclerView` è progettato per essere il successore di `ListView` (e `GridView`) `ListView` , si consiglia di usare `RecyclerView` anziché per lo sviluppo di nuove app. Per ulteriori informazioni, vedere [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).

## <a name="listview-tutorial"></a>Esercitazione su ListView

[`ListView`](xref:Android.Widget.ListView)è un[`ViewGroup`](xref:Android.Views.ViewGroup)
che consente di creare un elenco di elementi scorrevoli. Gli elementi dell'elenco vengono inseriti automaticamente nell'elenco usando un [`IListAdapter`](xref:Android.Widget.IListAdapter).

In questa esercitazione verrà creato un elenco scorrevole di nomi di paese che vengono letti da una matrice di stringhe. Quando viene selezionata una voce di elenco, in un messaggio di avviso popup viene visualizzata la posizione dell'elemento nell'elenco.

Avviare un nuovo progetto denominato **HelloListView**.

Creare un file XML denominato **list_item. XML** e salvarlo all'interno di **risorse/layout/** cartella. Inserire quanto segue:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

Questo file definisce il layout per ogni elemento che verrà inserito in [`ListView`](xref:Android.Widget.ListView).

Aprire `MainActivity.cs` e modificare la classe per estendere [`ListActivity`](xref:Android.App.ListActivity) (anziché [`Activity`](xref:Android.App.Activity)):

```csharp
public class MainActivity : ListActivity
{
```

Inserire il codice seguente per il [`OnCreate()`](xref:Android.App.Activity.OnCreate*)metodo):

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);

    ListView.TextFilterEnabled = true;

    ListView.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args)
    {
        Toast.MakeText(Application, ((TextView)args.View).Text, ToastLength.Short).Show();
    };
}
```

Si noti che in questo modo non viene caricato un file di layout per l'attività (che [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)in genere si esegue con)).
Impostare invece il parametro[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
la proprietà aggiunge automaticamente un oggetto[`ListView`](xref:Android.Widget.ListView)
per riempire l'intera schermata dell'oggetto [`ListActivity`](xref:Android.App.ListActivity).
Questo metodo accetta un [`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1)oggetto che gestisce la matrice di elementi dell'elenco che verrà inserito [`ListView`](xref:Android.Widget.ListView)in.
Il[`ArrayAdapter<T>`](xref:Android.Widget.ArrayAdapter`1)
il costruttore accetta l' [`Context`](xref:Android.Content.Context)applicazione, la descrizione del layout per ogni elemento dell'elenco (creato nel passaggio precedente) e `T[]` un oggetto o[`Java.Util.IList<T>`](xref:Java.Util.IList)
matrice di oggetti da inserire nell'oggetto[`ListView`](xref:Android.Widget.ListView)
(definito successivamente).

Il[`TextFilterEnabled`](xref:Android.Widget.AbsListView.TextFilterEnabled)
la proprietà attiva il filtraggio del [`ListView`](xref:Android.Widget.ListView)testo per l'oggetto, in modo che quando l'utente inizia a digitare, l'elenco verrà filtrato.

Il[`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)
è possibile utilizzare l'evento per sottoscrivere i gestori per i clic. Quando un elemento in[`ListView`](xref:Android.Widget.ListView)
viene selezionato, il gestore viene chiamato e[`Toast`](xref:Android.Widget.Toast)
viene visualizzato il messaggio, usando il testo dell'elemento selezionato.

È possibile utilizzare la progettazione di elementi di elenco fornita dalla piattaforma anziché definire un file di layout personalizzato [`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)per.
Provare ad esempio a usare `Android.Resource.Layout.SimpleListItem1` `Resource.Layout.list_item`anziché.

Aggiungere l'istruzione `using` seguente:

```csharp
using System;
```
Aggiungere quindi la matrice di stringhe seguente come membro di `MainActivity`:

```csharp
static readonly string[] countries = new String[] {
    "Afghanistan","Albania","Algeria","American Samoa","Andorra",
    "Angola","Anguilla","Antarctica","Antigua and Barbuda","Argentina",
    "Armenia","Aruba","Australia","Austria","Azerbaijan",
    "Bahrain","Bangladesh","Barbados","Belarus","Belgium",
    "Belize","Benin","Bermuda","Bhutan","Bolivia",
    "Bosnia and Herzegovina","Botswana","Bouvet Island","Brazil","British Indian Ocean Territory",
    "British Virgin Islands","Brunei","Bulgaria","Burkina Faso","Burundi",
    "Cote d'Ivoire","Cambodia","Cameroon","Canada","Cape Verde",
    "Cayman Islands","Central African Republic","Chad","Chile","China",
    "Christmas Island","Cocos (Keeling) Islands","Colombia","Comoros","Congo",
    "Cook Islands","Costa Rica","Croatia","Cuba","Cyprus","Czech Republic",
    "Democratic Republic of the Congo","Denmark","Djibouti","Dominica","Dominican Republic",
    "East Timor","Ecuador","Egypt","El Salvador","Equatorial Guinea","Eritrea",
    "Estonia","Ethiopia","Faeroe Islands","Falkland Islands","Fiji","Finland",
    "Former Yugoslav Republic of Macedonia","France","French Guiana","French Polynesia",
    "French Southern Territories","Gabon","Georgia","Germany","Ghana","Gibraltar",
    "Greece","Greenland","Grenada","Guadeloupe","Guam","Guatemala","Guinea","Guinea-Bissau",
    "Guyana","Haiti","Heard Island and McDonald Islands","Honduras","Hong Kong","Hungary",
    "Iceland","India","Indonesia","Iran","Iraq","Ireland","Israel","Italy","Jamaica",
    "Japan","Jordan","Kazakhstan","Kenya","Kiribati","Kuwait","Kyrgyzstan","Laos",
    "Latvia","Lebanon","Lesotho","Liberia","Libya","Liechtenstein","Lithuania","Luxembourg",
    "Macau","Madagascar","Malawi","Malaysia","Maldives","Mali","Malta","Marshall Islands",
    "Martinique","Mauritania","Mauritius","Mayotte","Mexico","Micronesia","Moldova",
    "Monaco","Mongolia","Montserrat","Morocco","Mozambique","Myanmar","Namibia",
    "Nauru","Nepal","Netherlands","Netherlands Antilles","New Caledonia","New Zealand",
    "Nicaragua","Niger","Nigeria","Niue","Norfolk Island","North Korea","Northern Marianas",
    "Norway","Oman","Pakistan","Palau","Panama","Papua New Guinea","Paraguay","Peru",
    "Philippines","Pitcairn Islands","Poland","Portugal","Puerto Rico","Qatar",
    "Reunion","Romania","Russia","Rwanda","Sqo Tome and Principe","Saint Helena",
    "Saint Kitts and Nevis","Saint Lucia","Saint Pierre and Miquelon",
    "Saint Vincent and the Grenadines","Samoa","San Marino","Saudi Arabia","Senegal",
    "Seychelles","Sierra Leone","Singapore","Slovakia","Slovenia","Solomon Islands",
    "Somalia","South Africa","South Georgia and the South Sandwich Islands","South Korea",
    "Spain","Sri Lanka","Sudan","Suriname","Svalbard and Jan Mayen","Swaziland","Sweden",
    "Switzerland","Syria","Taiwan","Tajikistan","Tanzania","Thailand","The Bahamas",
    "The Gambia","Togo","Tokelau","Tonga","Trinidad and Tobago","Tunisia","Turkey",
    "Turkmenistan","Turks and Caicos Islands","Tuvalu","Virgin Islands","Uganda",
    "Ukraine","United Arab Emirates","United Kingdom",
    "United States","United States Minor Outlying Islands","Uruguay","Uzbekistan",
    "Vanuatu","Vatican City","Venezuela","Vietnam","Wallis and Futuna","Western Sahara",
    "Yemen","Yugoslavia","Zambia","Zimbabwe"
  };
```

Si tratta della matrice di stringhe che verrà inserita in [`ListView`](xref:Android.Widget.ListView).

Eseguire l'applicazione. È possibile scorrere l'elenco o digitare per filtrarlo, quindi fare clic su un elemento per visualizzare un messaggio. Viene visualizzato un output simile al seguente:

[![Schermata di esempio di ListView con i nomi dei paesi](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

Si noti che l'uso di una matrice di stringhe hardcoded non è la procedura consigliata. Uno viene usato in questa esercitazione per semplificare la dimostrazione del[`ListView`](xref:Android.Widget.ListView)
widget. La procedura migliore consiste nel fare riferimento a una matrice di stringhe definita da una risorsa esterna, ad `string-array` esempio con una risorsa nel file resources **/values/Strings. XML** del progetto. Ad esempio:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  <string name="app_name">HelloListView</string>
  <string-array name="countries_array">
    <item>Bahrain</item>
    <item>Bangladesh</item>
    <item>Barbados</item>
    <item>Belarus</item>
    <item>Belgium</item>
    <item>Belize</item>
    <item>Benin</item>
  </string-array>
</resources>
```

Per usare queste stringhe di risorse per [`ArrayAdapter`](xref:Android.Widget.ArrayAdapter`1), sostituire l'originale[`ListAdapter`](xref:Android.App.ListActivity.ListAdapter)
riga con i seguenti elementi:

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```

Eseguire l'applicazione. Viene visualizzato un output simile al seguente:

[![Schermata di esempio di ListView con un elenco di nomi più piccolo](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)

## <a name="going-further-with-listview"></a>Continua con ListView

Gli argomenti rimanenti (collegati di seguito) esaminano in maniera completa l' `ListView` utilizzo della classe e dei diversi tipi di adapter che è possibile utilizzare. La struttura è la seguente:

-   **Aspetto visivo** &ndash; Parti`ListView` del controllo e relativa modalità di funzionamento.

-   **Classi** Panoramica delle classi usate per visualizzare un oggetto `ListView`. &ndash;

-   **Visualizzazione di dati in un controllo ListView** Come visualizzare un semplice elenco di dati, come implementare `ListView's` le funzionalità di usabilità; come utilizzare diversi layout di riga predefiniti e come gli adapter salvano la memoria riutilizzando le visualizzazioni di riga. &ndash;

-   **Aspetto personalizzato** Modifica dello stile `ListView` di con layout personalizzati, tipi di carattere e colori. &ndash;

-   **Uso di SQLite** Come visualizzare i dati da un database SQLite con un `CursorAdapter`. &ndash;

-   **Ciclo** di vita delle attività Considerazioni di progettazione per `ListView` l'implementazione di attività, tra cui il punto nel ciclo di vita in cui è necessario popolare i dati e quando rilasciare le risorse. &ndash;

La discussione, suddivisa in sei parti, inizia con una panoramica della `ListView` classe stessa prima di introdurre esempi progressivamente più complessi su come usarlo.

-   [Parti e funzionalità di ListView](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
-   [Popolamento di un controllo ListView con dati](~/android/user-interface/layouts/list-view/populating.md)
-   [Personalizzazione dell'aspetto di un controllo ListView](~/android/user-interface/layouts/list-view/customizing-appearance.md)
-   [Uso di CursorAdapter](~/android/user-interface/layouts/list-view/cursor-adapters.md)
-   [Uso di un ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
-   [ListView e ciclo di vita delle attività](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>Riepilogo

In questo set di argomenti `ListView` sono stati introdotti alcuni esempi di utilizzo delle funzionalità predefinite `ListActivity`di. Sono state illustrate le implementazioni personalizzate di `ListView` che consentivano la creazione di layout colorati e l'uso di un database SQLite e si è accennato brevemente alla pertinenza del ciclo di vita delle attività `ListView` nell'implementazione.


## <a name="related-links"></a>Collegamenti correlati

- [AccessoryViews (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/accessoryviews)
- [BasicTableAndroid (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableadapter)
- [BuiltInViews (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/builtinviews)
- [CustomRowView (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/customrowview)
- [FastScroll (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fastscroll)
- [SectionIndex (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sectionindex)
- [SimpleCursorTableAdapter (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/simplecursortableadapter)
- [CursorTableAdapter (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/cursortableadapter)
- [Esercitazione sul ciclo di vita delle attività](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Utilizzo di tabelle e celle (in Novell. iOS)](~/ios/user-interface/controls/tables/index.md)
- [Riferimento alla classe ListView](xref:Android.Widget.ListView)
- [Riferimento alla classe ListActivity](xref:Android.App.ListActivity)
- [Riferimento alla classe BaseAdapter](xref:Android.Widget.BaseAdapter)
- [Riferimento alla classe classe ArrayAdapter](xref:Android.Widget.ArrayAdapter)
- [Riferimento alla classe CursorAdapter](xref:Android.Widget.CursorAdapter)
