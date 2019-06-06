---
title: Uso di ListView in xamarin. Android
description: ListView è un componente importante dell'interfaccia utente delle applicazioni Android. viene utilizzato ovunque da breve elenco delle opzioni di menu per lunghi elenchi di contatti o Preferiti di internet. Fornisce un modo semplice per presentare un elenco scorrevole delle righe che può essere formattato con uno stile predefinito o personalizzato in modo considerevole.
ms.prod: xamarin
ms.assetid: C2BA2705-9B20-01C2-468D-860BDFEDC157
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 2560a451f3a6e7dd09b687f9db8c0c070598def6
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740653"
---
# <a name="listview"></a>ListView

_ListView è un componente importante dell'interfaccia utente delle applicazioni Android. viene utilizzato ovunque da breve elenco delle opzioni di menu per lunghi elenchi di contatti o Preferiti di internet. Fornisce un modo semplice per presentare un elenco scorrevole delle righe che può essere formattato con uno stile predefinito o personalizzato in modo considerevole._


## <a name="overview"></a>Panoramica

Visualizzazioni elenco e gli adattatori sono inclusi nei blocchi di compilazione principali di applicazioni Android. Il `ListView` classe offre un modo flessibile per presentare i dati, se si tratta di un menu di scelta rapida o un elenco a discesa lungo. Fornisce le caratteristiche di utilizzabilità, ad esempio gli indici di scorrimento, veloci e selezione singola o multipla che consentono di compilare interfacce utente per dispositivi mobili per le applicazioni. Un'istanza di `ListView` richiede un *Adapter* da cui ricevere i dati presenti nelle visualizzazioni di righe.

Questa guida illustra come implementare `ListView` e i vari `Adapter` classi in xamarin. Android. Viene inoltre illustrato come personalizzare l'aspetto di un `ListView`, e illustra l'importanza della riga riutilizzare per ridurre il consumo di memoria. È inoltre disponibile discussione su alcune influenza il ciclo di vita di attività `ListView` e `Adapter` usare. Se si sta lavorando applicazioni multipiattaforma con xamarin. IOS, il `ListView` controllo è strutturalmente simile a iOS `UITableView` (e di Android `Adapter` è simile al `UITableViewSource`).

Prima di tutto una breve esercitazione introduce il `ListView` con un esempio di codice di base. Successivamente, vengono forniti collegamenti ad argomenti più avanzati che consentono di usare `ListView` in applicazioni reali.


> [!NOTE]
> Il `RecyclerView` widget è una versione più avanzata e flessibile di `ListView`. In quanto `RecyclerView` è progettato per essere considerato come il successore `ListView` (e `GridView`), è consigliabile utilizzare `RecyclerView` anziché `ListView` per nuove attività di sviluppo di app. Per altre informazioni, vedere [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md).



## <a name="listview-tutorial"></a>Esercitazione di ListView

[`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/) è un [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
che crea un elenco di elementi che è possibile scorrere. Gli elementi dell'elenco vengono inseriti automaticamente all'elenco utilizzando un [ `IListAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.IListAdapter/).

In questa esercitazione si creerà un elenco scorrevole dei nomi di paesi che vengono letti da una matrice di stringhe. Quando viene selezionato un elemento di elenco, un messaggio di tipo avviso popup visualizzerà la posizione dell'elemento nell'elenco.


Avviare un nuovo progetto denominato **HelloListView**.

Creare un file XML denominato **list_item.xml** e salvare il file all'interno di **risorse/Layout/** cartella. Inserire gli elementi seguenti:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp"
    android:textSize="16sp">
</TextView>
```

Questo file definisce il layout per ogni elemento che verrà inserito nel [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).

Aprire `MainActivity.cs` e modificare la classe per estendere [ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/) (anziché [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/)):

```csharp
public class MainActivity : ListActivity
{
```

Inserire il codice seguente per il [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/(Android.OS.Bundle))
metodo:

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

Si noti che questa non viene caricato un file di layout per l'attività (in genere questa operazione eseguita con [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32))).
Al contrario, l'impostazione di [`ListAdapter`](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)
proprietà aggiunge automaticamente un [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
per riempire l'intera schermata del [ `ListActivity` ](https://developer.xamarin.com/api/type/Android.App.ListActivity/).
Questo metodo accetta un [ `ArrayAdapter<T>` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/), che gestisce la matrice di elementi di elenco che viene inserita la [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/).
Il [`ArrayAdapter<T>`](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/)
costruttore accetta l'applicazione [ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/), la descrizione di layout per ogni elemento di elenco (creata nel passaggio precedente) e un `T[]` o [`Java.Util.IList<T>`](https://developer.xamarin.com/api/type/Java.Util.IList/)
Matrice di oggetti da inserire di [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
(definita successivamente).

Il [`TextFilterEnabled`](https://developer.xamarin.com/api/property/Android.Widget.AbsListView.TextFilterEnabled/)
proprietà attiva testo filtro per il [ `ListView` ](https://developer.xamarin.com/api/type/Android.Widget.ListView/), in modo che quando l'utente avvia la digitazione, verrà filtrato nell'elenco.

Il [`ItemClick`](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)
evento è utilizzabile per sottoscrivere i gestori per clic del mouse. Quando un elemento di [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
è fatto clic, viene chiamato il gestore e un oggetto [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
viene visualizzato, usando il testo dell'elemento selezionato.

È possibile usare le progettazioni di elemento di elenco fornite dalla piattaforma invece di definire il proprio file di layout per la [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/).
Ad esempio, provare a usare `Android.Resource.Layout.SimpleListItem1` invece di `Resource.Layout.list_item`.

Aggiungere il codice seguente `using` istruzione:

```csharp
using System;
```
Successivamente, aggiungere la seguente matrice di stringhe come membro di `MainActivity`:

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

Eseguire l'applicazione. È possibile scorrere l'elenco o digitare per filtrare, quindi fare clic su un elemento per visualizzare un messaggio. Viene visualizzato un output simile al seguente:

[![Screenshot di esempio di ListView con nomi di paese](images/01-listview-example-sml.png)](images/01-listview-example.png#lightbox)

Si noti che usando una matrice di stringhe hardcoded non le procedure consigliate di progettazione. Uno viene usato in questa esercitazione per semplicità, per illustrare il [`ListView`](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
widget. La procedura consigliata consiste nel fare riferimento a una matrice di stringhe definita da una risorsa esterna, ad esempio con un `string-array` risorse nel progetto **Resources/Values/Strings.xml** file. Ad esempio:

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

Usare queste stringhe di risorsa per il [ `ArrayAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter%601/), sostituire l'originale [`ListAdapter`](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)
linea con quanto segue:

```csharp
string[] countries = Resources.GetStringArray (Resource.Array.countries_array);
ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.list_item, countries);
```
Eseguire l'applicazione. Viene visualizzato un output simile al seguente:

[![Screenshot di esempio di ListView con elenco di nomi di dimensioni ridotte](images/02-smaller-example-sml.png)](images/02-smaller-example.png#lightbox)


## <a name="going-further-with-listview"></a>Funzionalità avanzate di ListView

Gli argomenti rimanenti (riportati di seguito) richiedere un quadro completo di utilizzo con il `ListView` classe e i diversi tipi di Adapter è possibile usare con esso. La struttura è la seguente:

-   **Aspetto visivo** &ndash; parti del `ListView` controllo e sul relativo funzionamento.

-   **Le classi** &ndash; panoramica delle classi usate per visualizzare un `ListView`.

-   **Visualizzazione dei dati in un ListView** &ndash; come visualizzare un elenco semplice dei dati, come implementare `ListView's` caratteristiche di utilizzabilità, come usare i layout di riga predefinito diverso; e come schede di risparmiare memoria riutilizzando le visualizzazioni delle righe.

-   **Aspetto personalizzato** &ndash; modificando lo stile del `ListView` con layout personalizzati, i tipi di carattere e colori.

-   **Utilizzo di SQLite** &ndash; come visualizzare i dati da un database SQLite con una `CursorAdapter`.

-   **Attività ciclo di vita** &ndash; considerazioni di progettazione quando si implementa `ListView` attività, inclusa nel ciclo di vita in cui è necessario popolare i dati e quando rilasciare le risorse.

La discussione (suddivisa in sei parti) inizia con una panoramica del `ListView` prima di introdurre esempi progressivamente più complessi di come usare questa classe.

-   [Parti e funzionalità di ListView](~/android/user-interface/layouts/list-view/parts-and-functionality.md)
-   [Popolamento di un ListView con dati](~/android/user-interface/layouts/list-view/populating.md)
-   [Personalizzazione dell'aspetto di un controllo ListView](~/android/user-interface/layouts/list-view/customizing-appearance.md)
-   [Uso di CursorAdapter](~/android/user-interface/layouts/list-view/cursor-adapters.md)
-   [Uso di un ContentProvider](~/android/user-interface/layouts/list-view/content-provider.md)
-   [ListView e ciclo di vita delle attività](~/android/user-interface/layouts/list-view/activity-lifecycle.md)


## <a name="summary"></a>Riepilogo

Questo set di argomenti introdotta `ListView` e forniti alcuni esempi di come usare le funzionalità integrate del `ListActivity`. È stato illustrato le implementazioni personalizzate degli `ListView` che è consentito per i layout colorati utilizzando un database SQLite e trattati brevemente la rilevanza del ciclo di vita delle attività `ListView` implementazione.


## <a name="related-links"></a>Collegamenti correlati

- [AccessoryViews (esempio)](https://developer.xamarin.com/samples/monodroid/AccessoryViews/)
- [BasicTableAndroid (sample)](https://developer.xamarin.com/samples/monodroid/BasicTableAndroid/)
- [BasicTableAdapter (esempio)](https://developer.xamarin.com/samples/monodroid/BasicTableAdapter/)
- [BuiltInViews (esempio)](https://developer.xamarin.com/samples/monodroid/BuiltInViews/)
- [CustomRowView (esempio)](https://developer.xamarin.com/samples/monodroid/CustomRowView/)
- [FastScroll (esempio)](https://developer.xamarin.com/samples/monodroid/FastScroll/)
- [SectionIndex (esempio)](https://developer.xamarin.com/samples/monodroid/SectionIndex/)
- [SimpleCursorTableAdapter (esempio)](https://developer.xamarin.com/samples/monodroid/SimpleCursorTableAdapter/)
- [CursorTableAdapter (esempio)](https://developer.xamarin.com/samples/monodroid/CursorTableAdapter/)
- [Esercitazione del ciclo di vita dell'attività](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Utilizzo delle tabelle e celle (in xamarin. IOS)](~/ios/user-interface/controls/tables/index.md)
- [Riferimento alla classe di ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Riferimento alla classe ListActivity](https://developer.xamarin.com/api/type/Android.App.ListActivity/)
- [Riferimento alla classe BaseAdapter](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
- [Riferimento alla classe ArrayAdapter](https://developer.xamarin.com/api/type/Android.Widget.ArrayAdapter/)
- [Riferimento alla classe di CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
