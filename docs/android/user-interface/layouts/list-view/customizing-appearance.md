---
title: Personalizzazione dell'aspetto di un controllo ListView
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: c8a6e7a9abf9e8430253b9402d69f43744bf93c0
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827663"
---
# <a name="customizing-a-listviews-appearance"></a>Personalizzazione dell'aspetto di un controllo ListView


## <a name="overview"></a>Panoramica

L'aspetto di un ListView dipende dal layout delle righe viene visualizzato. Per modificare l'aspetto di un `ListView`, usare un layout di riga diversi.


## <a name="built-in-row-views"></a>Visualizzazioni delle righe incorporato

Sono disponibili dodici visualizzazioni predefinite che è possibile farvi riferimento usando **Android.Resource.Layout**:

- **TestListItem** &ndash; singola riga di testo con formattazione minimo.

- **SimpleListItem1** &ndash; singola riga di testo.

- **SimpleListItem2** &ndash; due righe di testo.

- **SimpleSelectableListItem** &ndash; singola riga di testo che supporta la selezione di uno o più elementi (aggiunta nel livello API 11).

- **SimpleListItemActivated1** &ndash; SimpleListItem1 è simile, ma il colore di sfondo indica quando è selezionata una riga (aggiunto nel livello API 11).

- **SimpleListItemActivated2** &ndash; SimpleListItem2 è simile, ma il colore di sfondo indica quando è selezionata una riga (aggiunto nel livello API 11).

- **SimpleListItemChecked** &ndash; vengono visualizzati segni di spunta per indicare la selezione.

- **SimpleListItemMultipleChoice** &ndash; consente di visualizzare le caselle di controllo per indicare a selezione multipla.

- **SimpleListItemSingleChoice** &ndash; consente di visualizzare i pulsanti per indicare la selezione si escludono a vicenda di opzione.

- **TwoLineListItem** &ndash; due righe di testo.

- **ActivityListItem** &ndash; singola riga di testo con un'immagine.

- **SimpleExpandableListItem** &ndash; Raggruppa le righe per le categorie e ogni gruppo possono essere espansi o compressi.

Ogni vista di riga predefinito con uno stile predefinito associato. Questi screenshot mostrano come viene visualizzato ogni visualizzazione:

[![Screenshot del TestListItem SimpleSelectableListItem, SimpleListitem1 e SimpleListItem2](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png#lightbox)

[![Screenshot del SimpleListItemActivated1 SimpleListItemActivated2, SimpleListItemChecked e SimpleListItemMultipleChecked](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png#lightbox)

[![Screenshot del SimpleListItemSingleChoice TwoLineListItem, ActivityListItem e SimpleExpandableListItem](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png#lightbox)

Il **BuiltInViews/HomeScreenAdapter.cs** file di esempio (nelle **BuiltInViews** soluzione) contiene il codice per produrre le schermate di elemento di elenco non espandibili. La visualizzazione è impostata `GetView` metodo simile al seguente:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

Le proprietà della vista possono quindi essere impostate facendo riferimento gli identificatori di controllo standard `Text1`, `Text2` e `Icon` sotto `Android.Resource.Id` (non impostare le proprietà che non contiene la visualizzazione o verrà generata un'eccezione):

```csharp
view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = item.Heading;
view.FindViewById<TextView>(Android.Resource.Id.Text2).Text = item.SubHeading;
view.FindViewById<ImageView>(Android.Resource.Id.Icon).SetImageResource(item.ImageResourceId); // only use with ActivityListItem
```

Il **BuiltInExpandableViews/ExpandableScreenAdapter.cs** file di esempio (nelle **BuiltInViews** soluzione) contiene il codice per produrre la schermata SimpleExpandableListItem. La visualizzazione del gruppo è impostata `GetGroupView` metodo simile al seguente:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem1, null);
```

La visualizzazione figlio è impostata `GetChildView` metodo simile al seguente:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem2, null);
```

Le proprietà per la visualizzazione del gruppo e della visualizzazione figlio possono quindi essere impostate facendo riferimento allo standard `Text1` e `Text2` controllano gli identificatori, come illustrato in precedenza. Lo screenshot SimpleExpandableListItem (illustrato in precedenza) viene fornito un esempio di una vista gruppo una sola riga (SimpleExpandableListItem1) e una visualizzazione di due righe figlio (SimpleExpandableListItem2). In alternativa, la visualizzazione del gruppo può essere configurata per due righe (SimpleExpandableListItem2) e della visualizzazione figlio può essere configurata per una sola riga (SimpleExpandableListItem1), o entrambe di gruppo Vista e visualizzazione figlio può avere lo stesso numero di righe. 



## <a name="accessories"></a>Accessori

Le righe possono avere Accessori aggiunti a destra della visualizzazione per indicare lo stato di selezione:

- **SimpleListItemChecked** &ndash; crea un elenco di selezione singola con un controllo dell'indicatore.

- **SimpleListItemSingleChoice** &ndash; crea in cui è possibile solo scegliere gli elenchi di tipo di pulsante di opzione.

- **SimpleListItemMultipleChoice** &ndash; Crea elenchi di tipo casella di controllo in cui sono possibili più scelte.

Nelle schermate seguenti, in base all'ordine corrispondente sono illustrati gli accessori menzionati in precedenza:

[![Screenshot di SimpleListItemChecked SimpleListItemSingleChoice e SimpleListItemMultipleChoice con accessori](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png#lightbox)

Per visualizzare uno di questi pass accessori l'ID di risorsa di layout obbligatorie per l'adapter quindi manualmente imposta lo stato di selezione per le righe necessarie. Questa riga di codice viene illustrato come creare e assegnare un `Adapter` usando uno di questi layout:

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

Il `ListView` stesso supporta diverse modalità di selezione, indipendentemente dalla funzione di accesso viene visualizzato. Per evitare confusione, usare `Single` con la modalità di selezione `SingleChoice` accessori e il `Checked` o `Multiple` modalità con la `MultipleChoice` stile. La modalità di selezione è controllata dal `ChoiceMode` proprietà del `ListView`.


### <a name="handling-api-level"></a>Livello API di gestione

Le versioni precedenti di xamarin. Android implementate le enumerazioni come proprietà integer. La versione più recente ha introdotto i tipi di enumerazione appropriati .NET che rende molto più semplice individuare le possibili opzioni.

A seconda di quale livello di API di destinazione, `ChoiceMode` è un numero intero o un'enumerazione. Il file di esempio **AccessoryViews/HomeScreen.cs** è un blocco di commento per l'API Gingerbread di destinazione:

```csharp
// For targeting Gingerbread the ChoiceMode is an int, otherwise it is an
// enumeration.

lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // 1
//lv.ChoiceMode = Android.Widget.ChoiceMode.Multiple; // 2
//lv.ChoiceMode = Android.Widget.ChoiceMode.None; // 0

// Use this block if targeting Gingerbread or lower
/*
lv.ChoiceMode = 1; // Single
//lv.ChoiceMode = 0; // none
//lv.ChoiceMode = 2; // Multiple
//lv.ChoiceMode = 3; // MultipleModal
*/
```


### <a name="selecting-items-programmatically"></a>Selezione di elementi a livello di codice

Impostare manualmente gli elementi che sono 'selezionate' operazione viene eseguita con il `SetItemChecked` metodo (può essere chiamato più volte per la selezione multipla):

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

Il codice deve inoltre rilevare selezioni singole in modo diverso dai più selezioni. Per determinare quale riga è stata selezionata nel `Single` uso della modalità di `CheckedItemPosition` proprietà a valore integer:

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

Per determinare quali righe sono state selezionate nel `Multiple` modalità è necessario scorrere in ciclo il `CheckedItemPositions` `SparseBooleanArray`. Una matrice di tipo sparse è simile a un dizionario che contiene solo le voci in cui è stato modificato il valore, pertanto è necessario scorrere l'intera matrice cercando `true` valori sapere cosa è stata selezionata nell'elenco, come illustrato nel frammento di codice seguente:

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```


## <a name="creating-custom-row-layouts"></a>Creazione di layout di riga personalizzato

Le quattro viste di riga predefinito sono molto semplici. Per visualizzare i layout più complessi (ad esempio un elenco di messaggi di posta elettronica, TWEET o le informazioni di contatto) è necessaria una visualizzazione personalizzata. Visualizzazioni personalizzate vengono dichiarate in genere sotto forma di file AXML il **risorse/Layout** directory e quindi caricate usando la risorsa Id da un adapter personalizzato. La vista può contenere qualsiasi numero di classi di visualizzazione, come oggetti TextViews, ImageViews e altri controlli, layout, i tipi di carattere e colori personalizzati.

In questo esempio è diverso dagli esempi precedenti in diversi modi:

-  Eredita da `Activity` , non `ListActivity` . È possibile personalizzare le righe per qualsiasi `ListView` , ma anche altri controlli possono essere inclusi un `Activity` layout (ad esempio un titolo, i pulsanti o altri elementi dell'interfaccia utente). Questo esempio si aggiunge un'intestazione sopra le `ListView` per illustrare.

-  Richiede un file di layout AXML della schermata; Negli esempi precedenti il `ListActivity` non richiede un file di layout. Questo AXML contiene un `ListView` dichiarazione del controllo.

-  Richiede un file di layout AXML per eseguire il rendering di ogni riga. Questo file AXML contiene i controlli text e image con tipo di carattere personalizzato e le impostazioni dei colori.

-  Usa un file XML facoltativo selettore personalizzato per impostare l'aspetto della riga quando viene selezionato.

-  Il `Adapter` implementazione restituisce un layout personalizzato dal `GetView` eseguire l'override.

-  `ItemClick` deve essere dichiarato in modo diverso (un gestore di evento è associato a `ListView.ItemClick` anziché un override `OnListItemClick` in `ListActivity`).


Queste modifiche sono descritte di seguito, a partire dalla creazione di visualizzazione dell'attività e la visualizzazione di righe personalizzato e quindi illustrando le modifiche per l'Adapter e l'attività per eseguirne il rendering.


### <a name="adding-a-listview-to-an-activity-layout"></a>Aggiunta di un ListView a un Layout di attività

In quanto `HomeScreen` non erediti più da `ListActivity` non dispone di una visualizzazione predefinita, pertanto è necessario creare un file di layout AXML per la visualizzazione della schermata iniziale. Per questo esempio, la visualizzazione avrà un'intestazione (usando un `TextView`) e un `ListView` per visualizzare i dati. Il layout è definito nel **Resources/Layout/HomeScreen.axml** file illustrata di seguito:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:orientation="vertical"
   android:layout_width="fill_parent"
   android:layout_height="fill_parent">
    <TextView android:id="@+id/Heading"
        android:text="Vegetable Groups"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="#00000000"
        android:textSize="30dp"
        android:textColor="#FF267F00"
        android:textStyle="bold"
        android:padding="5dp"
    />
    <ListView android:id="@+id/List"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:cacheColorHint="#FFDAFF7F"
    />
</LinearLayout>
```

Il vantaggio dell'uso di un' `Activity` con un layout personalizzato (invece di un `ListActivity`) è la possibilità di aggiungere altri controlli nella schermata, ad esempio l'intestazione di `TextView` in questo esempio.


### <a name="creating-a-custom-row-layout"></a>Creazione di un Layout di riga personalizzato

Un altro file di layout AXML è necessario per contenere il layout personalizzato per ogni riga che verrà visualizzato nella visualizzazione elenco. In questo esempio la riga avrà uno sfondo verde, marrone testo e immagine allineato a destra. Il markup XML Android per dichiarare questo layout è descritto nella **Resources/Layout/CustomView.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout  xmlns:android="http://schemas.android.com/apk/res/android"
   android:layout_width="fill_parent"
   android:layout_height="wrap_content"
   android:background="#FFDAFF7F"
   android:padding="8dp">
    <LinearLayout android:id="@+id/Text"
       android:orientation="vertical"
       android:layout_width="wrap_content"
       android:layout_height="wrap_content"
       android:paddingLeft="10dip">
        <TextView
         android:id="@+id/Text1"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textColor="#FF7F3300"
         android:textSize="20dip"
         android:textStyle="italic"
         />
        <TextView
         android:id="@+id/Text2"
         android:layout_width="wrap_content"
         android:layout_height="wrap_content"
         android:textSize="14dip"
         android:textColor="#FF267F00"
         android:paddingLeft="100dip"
         />
    </LinearLayout>
    <ImageView
        android:id="@+id/Image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:padding="5dp"
        android:src="@drawable/icon"
        android:layout_alignParentRight="true" />
</RelativeLayout >
```

Mentre un layout di riga personalizzato può contenere molti diversi controlli, lo scorrimento delle prestazioni può essere influenzato da strutture complesse e utilizzo di immagini (in particolare se devono essere caricati in rete). Vedere l'articolo di Google per altre informazioni su risoluzione dei problemi di prestazioni dello scorrimento.


### <a name="referencing-a-custom-row-view"></a>Riferimento a una vista personalizzata riga

L'implementazione dell'esempio di adapter personalizzato è `HomeScreenAdapter.cs`. Il metodo chiave è `GetView` in cui viene caricato il AXML personalizzato Usa l'ID risorsa `Resource.Layout.CustomView`e quindi imposta le proprietà per ognuno dei controlli nella visualizzazione prima di restituirlo. La classe dell'adattatore completo è illustrata:

```csharp
public class HomeScreenAdapter : BaseAdapter<TableItem> {
   List<TableItem> items;
   Activity context;
   public HomeScreenAdapter(Activity context, List<TableItem> items)
       : base()
   {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
   {
       return position;
   }
   public override TableItem this[int position]
   {
       get { return items[position]; }
   }
   public override int Count
   {
       get { return items.Count; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       var item = items[position];
       View view = convertView;
       if (view == null) // no view to re-use, create new
           view = context.LayoutInflater.Inflate(Resource.Layout.CustomView, null);
       view.FindViewById<TextView>(Resource.Id.Text1).Text = item.Heading;
       view.FindViewById<TextView>(Resource.Id.Text2).Text = item.SubHeading;
       view.FindViewById<ImageView>(Resource.Id.Image).SetImageResource(item.ImageResourceId);
       return view;
   }
}
```


### <a name="referencing-the-custom-listview-in-the-activity"></a>Il ListView personalizzato nell'attività di riferimento

Poiché il `HomeScreen` classe ora eredita da `Activity`, un `ListView` campo viene dichiarato nella classe che contiene un riferimento al controllo dichiarato nel AXML:

```csharp
ListView listView;
```

La classe deve quindi caricare il layout personalizzato dell'attività AXML usando la `SetContentView` (metodo). Quindi possibile trovare il `ListView` nel layout del controllo quindi crea e assegna l'adapter e assegna il gestore di clic. Di seguito è riportato il codice per il metodo OnCreate:

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

Infine il `ItemClick` gestore deve essere definito; in questo caso visualizza semplicemente una `Toast` messaggio:

```csharp
void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
   var listView = sender as ListView;
   var t = tableItems[e.Position];
   Android.Widget.Toast.MakeText(this, t.Heading, Android.Widget.ToastLength.Short).Show();
}
```

La schermata risulta sarà simile al seguente:

[![Screenshot della finestra di CustomRowView risultante](customizing-appearance-images/customrowview.png)](customizing-appearance-images/customrowview.png#lightbox)



### <a name="customizing-the-row-selector-color"></a>Personalizzare il colore del selettore di riga

Quando una riga viene manipolata dovrebbe essere evidenziata per commenti degli utenti. Quando una visualizzazione personalizzata specifica come colore di sfondo **CustomView.axml** affermativo, esegue l'override anche l'evidenziazione della selezione. Questa riga di codice nel **CustomView.axml** lo sfondo a verde chiaro, ma significa anche che non vi è alcun indicatore visivo quando la riga è interessata dal set:

```xml
android:background="#FFDAFF7F"
```

Per riabilitare il comportamento dell'evidenziazione e anche per personalizzare il colore che viene usato, impostare l'attributo background a un selettore personalizzato. Il selettore dichiarano entrambi il colore di sfondo predefinito nonché il colore di evidenziazione. Il file **Resources/Drawable/CustomSelector.xml** contiene la dichiarazione seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
<item android:state_pressed="false"
  android:state_selected="false"
  android:drawable="@color/cellback" />
<item android:state_pressed="true" >
  <shape>
     <gradient
      android:startColor="#E77A26"
        android:endColor="#E77A26"
        android:angle="270" />
  </shape>
</item>
<item android:state_selected="true"
  android:state_pressed="false"
  android:drawable="@color/cellback" />
</selector>
```

Per fare riferimento il selettore personalizzato, modificare l'attributo in background nel **CustomView.axml** per:

```xml
android:background="@drawable/CustomSelector"
```

Una riga selezionata e le corrispondenti `Toast` messaggio avrà l'aspetto seguente:

[![Una riga selezionata in arancione, con messaggio di tipo avviso popup visualizzazione nome della riga selezionata](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png#lightbox)



### <a name="preventing-flickering-on-custom-layouts"></a>Impedisce lo sfarfallio in layout personalizzati

Tenta di Android migliorare le prestazioni di `ListView` lo scorrimento della memorizzazione nella cache le informazioni sul layout. Se hai tempo lo scorrimento elenchi di dati è necessario impostare anche il `android:cacheColorHint` proprietà di `ListView` dichiarazione nella definizione di AXML dell'attività (per lo stesso valore di colore di sfondo del layout di riga personalizzato). Mancata per includere questo hint può comportare un 'lo sfarfallio' come l'utente scorre un elenco con i colori di sfondo di righe personalizzato.



## <a name="related-links"></a>Collegamenti correlati

- [BuiltInViews (esempio)](https://developer.xamarin.com/samples/monodroid/BuiltInViews/)
- [AccessoryViews (esempio)](https://developer.xamarin.com/samples/monodroid/AccessoryViews/)
- [CustomRowView (esempio)](https://developer.xamarin.com/samples/monodroid/CustomRowView/)
