---
title: ListView
description: "ListView è un importante componente dell'interfaccia utente delle applicazioni Android. utilizzato ovunque da breve elenco delle opzioni di menu in lunghi elenchi di contatti o Preferiti di internet. Fornisce un modo semplice per presentare un elenco di scorrimento di righe che può essere formattato con uno stile predefinito o personalizzato in modo considerevole."
ms.topic: article
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 70a7abb186c102fb803c0ab6fa38c7b2d8222292
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="listview"></a>ListView

_ListView è un importante componente dell'interfaccia utente delle applicazioni Android. utilizzato ovunque da breve elenco delle opzioni di menu in lunghi elenchi di contatti o Preferiti di internet. Fornisce un modo semplice per presentare un elenco di scorrimento di righe che può essere formattato con uno stile predefinito o personalizzato in modo considerevole._

<a name="overview" />

## <a name="overview"></a>Panoramica

Visualizzazioni elenco e le schede sono inclusi nei blocchi di compilazione più importanti di applicazioni Android. La `ListView` classe fornisce un modo flessibile per presentare i dati, se si tratta di un menu di scelta rapida o un elenco a scorrimento lungo. Fornisce le caratteristiche di utilizzabilità come indici di scorrimento, veloci e selezione di unica o più che consentono di compilare interfacce utente mobile descrittivi per le applicazioni. Un'istanza di `ListView` richiede un *Adapter* da cui ricevere i dati presenti nelle visualizzazioni di righe.

Questa guida viene illustrato come implementare `ListView` e i vari `Adapter` classi in xamarin. Viene inoltre illustrato come personalizzare l'aspetto di un `ListView`, e illustra l'importanza della riga riutilizzare per ridurre il consumo di memoria. È anche alcuni discussione di influenza il ciclo di vita di attività `ListView` e `Adapter` utilizzare. Se si sta lavorando applicazioni multipiattaforma con xamarin, il `ListView` controllo è strutturalmente simile al iOS `UITableView` (e di Android `Adapter` è simile al `UITableViewSource`).

In primo luogo, una breve esercitazione introduce il `ListView` con un esempio di codice di base. Successivamente, in cui vengono forniti collegamenti ad argomenti più avanzati che consentono di utilizzare `ListView` nelle applicazioni reali.


> [!NOTE]
> **Nota**: il `RecyclerView` widget è una versione più avanzata e flessibile di `ListView`. Poiché `RecyclerView` è progettato per essere il successore di `ListView` (e `GridView`), è consigliabile utilizzare `RecyclerView` anziché `ListView` per lo sviluppo di nuove app. Per ulteriori informazioni, vedere [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).


<a name="tutorial" />

## <a name="listview-tutorial"></a>Esercitazione di ListView

[`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) è un [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) che crea un elenco di elementi scorrevoli. Gli elementi dell'elenco vengono inseriti automaticamente all'elenco utilizzando un [ `IListAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.IListAdapter/).

In questa esercitazione si creerà un elenco di nomi di paese che vengono letti da una matrice di stringhe scorrevole. Quando viene selezionato un elemento di elenco, un messaggio di tipo avviso popup visualizzerà la posizione dell'elemento nell'elenco.


Avviare un nuovo progetto denominato **HelloListView**.

Creare un file XML denominato **list_item.xml** e salvarla all'interno di **risorse/Layout/** cartella. Inserire gli elementi seguenti:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

Questo file definisce il layout per ogni elemento che verrà inserito nella [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).

Aprire il `HelloListView.cs` e assicurarsi che estendono la classe [ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/) (anziché [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)):

```csharp
public class HelloListView : ListActivity
{
```

Inserire il codice seguente per il [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle)) metodo:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);

    ListView.TextFilterEnabled = true;

    ListView.ItemClick += delegate (object sender, ItemEventArgs args) {
        // When clicked, show a toast with the TextView text
        Toast.MakeText (Application, ((TextView)args.View).Text, ToastLength.Short).Show ();
    };
}
```

Si noti che questo non viene caricato un file di layout per l'attività (che è in genere con [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32))).
Al contrario, l'impostazione di [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/) proprietà aggiunge automaticamente un [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) per riempire l'intero schermo del [ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/).
Questo metodo accetta un [ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/), che gestisce la matrice di elementi di elenco che verrà inserito il [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).
Il [ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/) costruttore accetta l'applicazione [ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/), la descrizione di layout per ogni elemento di elenco (creato nel passaggio precedente) e un `T[]` o [ `Java.Util.IList<T>` ](https://developer.xamarin.com/api/type/Java.Util.IList/) matrice di oggetti da inserire nel [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) (definita successivamente).

Il [ `TextFilterEnabled` ](https://developer.xamarin.com/api/property/Android.Widget.AbsListView.TextFilterEnabled/) attiva testo il filtro per la proprietà di [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/), in modo che quando l'utente inizia a digitare, nell'elenco verrà filtrato.

Il [ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/) evento può essere utilizzato per sottoscrivere i gestori per i clic. Quando un elemento di [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) è selezionato, il gestore viene chiamato e un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) viene visualizzato, usando il testo dell'elemento selezionato.

È possibile utilizzare i modelli di elemento elenco forniti dalla piattaforma anziché definire il proprio file di layout per il [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/).
Ad esempio, provare a utilizzare `Android.Resource.Layout.SimpleListItem1` anziché `Resource.Layout.list_item`.

Dopo il [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle)) metodo, aggiungere la matrice di stringhe:

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

Si tratta la matrice di stringhe che verrà inserito il [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).

Eseguire l'applicazione. È possibile scorrere l'elenco, oppure digitare per filtrare, quindi fare clic su un elemento per visualizzare un messaggio. Viene visualizzato un output simile al seguente:

[ ![Schermata di esempio di ListView con nomi di paese](images/helloviews6.png)](images/helloviews6.png)

Si noti che non sono la procedura consigliata di progettazione utilizzando una matrice di stringhe hardcoded. Uno viene utilizzato in questa esercitazione per semplicità, per illustrare il [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/) widget. Si consiglia di fare riferimento a una matrice di stringhe definita da una risorsa esterna, ad esempio con un `string-array` risorse nel progetto **Resources/Values/Strings.xml** file. Ad esempio:

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
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

Per usare queste stringhe di risorsa per il [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/), sostituire l'originale [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/) riga con le operazioni seguenti:

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```

<a name="going_further" />

## <a name="going-further-with-listview"></a>Funzionalità avanzate di ListView

Gli argomenti rimanenti (riportati di seguito) un quadro completo utilizzano la `ListView` classe e i diversi tipi di Adapter, è possibile utilizzare. La struttura è la seguente:

-   **Aspetto visivo** &ndash; parti del `ListView` controllo e il relativo funzionamento.

-   **Classi** &ndash; panoramica delle classi utilizzate per visualizzare un `ListView`.

-   **La visualizzazione dei dati in un controllo ListView** &ndash; come visualizzare un elenco semplice dei dati, come implementare `ListView's` caratteristiche di utilizzabilità, come utilizzare i layout di riga predefinito diverso; e come schede di risparmiare memoria utilizzando nuovamente le viste delle righe.

-   **Aspetto personalizzato** &ndash; la modifica dello stile del `ListView` con layout personalizzati, i tipi di carattere e colori.

-   **Utilizzo di SQLite** &ndash; come visualizzare dati da un database SQLite con un `CursorAdapter`.

-   **Ciclo di vita dell'attività** &ndash; considerazioni di progettazione quando si implementa `ListView` attività, incluse nel ciclo di vita in cui è necessario popolare i dati e quando rilasciare le risorse.

La discussione (suddivisa in sei parti) inizia con una panoramica di `ListView` classe prima di introdurre progressivamente più complessi esempi su come utilizzarla.

-   [Parti e funzionalità di ListView](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
-   [Popolamento di un controllo ListView con dati](~/android/user-interface/layouts/list-view/populating.md)
-   [Personalizzazione dell'aspetto di un controllo ListView](~/android/user-interface/layouts/list-view/customizing-appearance.md)
-   [Uso di CursorAdapter](~/android/user-interface/layouts/list-view/cursor-adapters.md)
-   [Uso di un ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
-   [ListView e ciclo di vita delle attività](~/android/user-interface/layouts/list-view/activity-lifecycle.md)

<a name="summary" />

## <a name="summary"></a>Riepilogo

Questo insieme di argomenti introdotta `ListView` e forniti alcuni esempi di come utilizzare le funzionalità predefinite del `ListActivity`. È stato illustrato implementazioni personalizzate delle `ListView` che è consentito per i layout colorati utilizzando un database SQLite e brevemente interessate sulla pertinenza del ciclo di vita delle attività `ListView` implementazione.


## <a name="related-links"></a>Collegamenti correlati

- [AccessoryViews (esempio)](https://developer.xamarin.com/samples/AccessoryViews/)
- [BasicTableAndroid (esempio)](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter (esempio)](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [BuiltInViews (esempio)](https://developer.xamarin.com/samples/BuiltInViews/)
- [CustomRowView (esempio)](https://developer.xamarin.com/samples/CustomRowView/)
- [FastScroll (esempio)](https://developer.xamarin.com/samples/FastScroll/)
- [SectionIndex (esempio)](https://developer.xamarin.com/samples/SectionIndex/)
- [SimpleCursorTableAdapter (sample)](https://developer.xamarin.com/samples/SimpleCursorTableAdapter/)
- [CursorTableAdapter (esempio)](https://developer.xamarin.com/samples/CursorTableAdapter/)
- [Esercitazione del ciclo di vita di attività](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Utilizzo delle tabelle e le celle (xamarin)](~/ios/user-interface/controls/tables/index.md)
- [Riferimento alla classe di ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Riferimento alla classe ListActivity](https://developer.xamarin.com/api/type/Android.App.ListActivity/)
- [Riferimento alla classe BaseAdapter](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
- [Riferimento alla classe ArrayAdapter](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
- [Riferimento alla classe CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
