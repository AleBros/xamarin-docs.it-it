---
title: Personalizzazione dell'aspetto di un controllo ListView
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: 48b23a1dce66f13efd3ad598cd61684e64e2b03c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028907"
---
# <a name="customizing-a-listviews-appearance-with-xamarinandroid"></a>Personalizzazione dell'aspetto di un controllo ListView con Xamarin.Android

L'aspetto di un controllo ListView è determinato dal layout delle righe visualizzate. Per modificare l'aspetto di una `ListView`, utilizzare un layout di riga diverso.

## <a name="built-in-row-views"></a>Visualizzazioni di righe predefinite

Sono disponibili dodici viste predefinite a cui è possibile fare riferimento usando **Android. Resource. layout**:

- **TestListItem** &ndash; singola riga di testo con formattazione minima.

- **SimpleListItem1** &ndash; riga di testo singola.

- **SimpleListItem2** &ndash; due righe di testo.

- **SimpleSelectableListItem** &ndash; singola riga di testo che supporta la selezione di una o più voci (aggiunta nel livello API 11).

- **SimpleListItemActivated1** &ndash; simile a SimpleListItem1, ma il colore di sfondo indica quando viene selezionata una riga (aggiunta nel livello API 11).

- **SimpleListItemActivated2** &ndash; simile a SimpleListItem2, ma il colore di sfondo indica quando viene selezionata una riga (aggiunta nel livello API 11).

- **SimpleListItemChecked** &ndash; Visualizza i segni di spunta per indicare la selezione.

- **SimpleListItemMultipleChoice** &ndash; Visualizza le caselle di controllo per indicare la selezione a più scelte.

- **SimpleListItemSingleChoice** &ndash; Visualizza i pulsanti di opzione per indicare la selezione che si escludono a vicenda.

- **TwoLineListItem** &ndash; due righe di testo.

- **ActivityListItem** &ndash; singola riga di testo con un'immagine.

- **SimpleExpandableListItem** &ndash; raggruppa le righe in base alle categorie e ogni gruppo può essere espanso o compresso.

Ogni visualizzazione di riga predefinita ha uno stile incorporato associato. Queste schermate mostrano come viene visualizzata ogni visualizzazione:

[![screenshot di TestListItem, SimpleSelectableListItem, SimpleListitem1 e SimpleListItem2](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png#lightbox)

[![screenshot di SimpleListItemActivated1, SimpleListItemActivated2, SimpleListItemChecked e SimpleListItemMultipleChecked](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png#lightbox)

[![screenshot di SimpleListItemSingleChoice, TwoLineListItem, ActivityListItem e SimpleExpandableListItem](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png#lightbox)

Il file di esempio **BuiltInViews/HomeScreenAdapter. cs** (nella soluzione **BuiltInViews** ) contiene il codice per produrre le schermate degli elementi elenco non espandibili. La vista viene impostata nel metodo `GetView` come segue:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

Le proprietà della vista possono quindi essere impostate facendo riferimento agli identificatori di controllo standard `Text1`, `Text2` e `Icon` in `Android.Resource.Id` (non impostare le proprietà che la vista non contiene o verrà generata un'eccezione):

```csharp
view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = item.Heading;
view.FindViewById<TextView>(Android.Resource.Id.Text2).Text = item.SubHeading;
view.FindViewById<ImageView>(Android.Resource.Id.Icon).SetImageResource(item.ImageResourceId); // only use with ActivityListItem
```

Il file di esempio **BuiltInExpandableViews/ExpandableScreenAdapter. cs** (nella soluzione **BuiltInViews** ) contiene il codice per produrre la schermata SimpleExpandableListItem. La visualizzazione gruppo è impostata nel metodo `GetGroupView` come segue:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem1, null);
```

La visualizzazione figlio viene impostata nel metodo `GetChildView` come segue:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem2, null);
```

È possibile impostare le proprietà per la visualizzazione del gruppo e la visualizzazione figlio facendo riferimento agli identificatori di controllo `Text1` e `Text2` standard, come illustrato in precedenza. Lo screenshot SimpleExpandableListItem (illustrato in precedenza) fornisce un esempio di visualizzazione di un gruppo a una riga (SimpleExpandableListItem1) e di una visualizzazione figlio a due righe (SimpleExpandableListItem2). In alternativa, la visualizzazione gruppo può essere configurata per due righe (SimpleExpandableListItem2) e la visualizzazione figlio può essere configurata per una riga (SimpleExpandableListItem1) oppure sia la visualizzazione gruppo che la visualizzazione figlio possono avere lo stesso numero di righe. 

## <a name="accessories"></a>Accessori

Le righe possono avere accessori aggiunti a destra della visualizzazione per indicare lo stato di selezione:

- **SimpleListItemChecked** &ndash; crea un elenco a selezione singola con un segno di spunta come indicatore.

- **SimpleListItemSingleChoice** &ndash; crea elenchi di tipi di pulsanti di opzione in cui è possibile scegliere solo una delle opzioni.

- **SimpleListItemMultipleChoice** &ndash; crea elenchi di tipo casella di controllo in cui sono possibili più opzioni.

Gli accessori menzionati sopra sono illustrati nelle schermate seguenti, nel rispettivo ordine:

[![screenshot di SimpleListItemChecked, SimpleListItemSingleChoice e SimpleListItemMultipleChoice con accessori](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png#lightbox)

Per visualizzare uno di questi accessori, passare l'ID della risorsa di layout richiesto alla scheda, quindi impostare manualmente lo stato di selezione per le righe richieste. Questa riga di codice Mostra come creare e assegnare un `Adapter` usando uno di questi layout:

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

Il `ListView` stesso supporta modalità di selezione diverse, indipendentemente dall'accessorio visualizzato. Per evitare confusione, usare `Single` modalità di selezione con `SingleChoice` accessori e la modalità `Checked` o `Multiple` con lo stile di `MultipleChoice`. La modalità di selezione è controllata dalla proprietà `ChoiceMode` della `ListView`.

### <a name="handling-api-level"></a>Gestione del livello API

Le versioni precedenti di Xamarin.Android implementavano le enumerazioni come proprietà Integer. La versione più recente ha introdotto i tipi di enumerazione .NET appropriati, semplificando l'individuazione delle possibili opzioni.

A seconda del livello API di destinazione, `ChoiceMode` può essere un numero intero o un'enumerazione. Il file di esempio **AccessoryViews/homescreen. cs** presenta un blocco impostato come commento se si vuole fare riferimento all'API Gingerbread:

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

L'impostazione manuale degli elementi ' selezionati ' viene eseguita con il metodo `SetItemChecked` (può essere chiamato più volte per la selezione multipla):

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

Il codice deve inoltre rilevare selezioni singole in modo diverso rispetto a selezioni multiple. Per determinare quale riga è stata selezionata in modalità `Single` usare la proprietà `CheckedItemPosition` Integer:

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

Per determinare quali righe sono state selezionate in modalità `Multiple` è necessario scorrere il `SparseBooleanArray``CheckedItemPositions`. Una matrice di tipo sparse è simile a un dizionario che contiene solo le voci in cui il valore è stato modificato, quindi è necessario attraversare l'intera matrice cercando i valori `true` per sapere cosa è stato selezionato nell'elenco, come illustrato nel frammento di codice seguente. :

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```

## <a name="creating-custom-row-layouts"></a>Creazione di layout di riga personalizzati

Le quattro visualizzazioni di righe predefinite sono molto semplici. Per visualizzare layout più complessi, ad esempio un elenco di messaggi di posta elettronica o tweet o informazioni di contatto, è necessaria una visualizzazione personalizzata. Le visualizzazioni personalizzate vengono in genere dichiarate come file AXML nella directory **Resources/layout** e quindi caricate usando il relativo ID risorsa da un adapter personalizzato. La vista può contenere un numero qualsiasi di classi di visualizzazione (ad esempio TextViews, ImageViews e altri controlli) con colori, tipi di carattere e layout personalizzati.

Questo esempio è diverso rispetto agli esempi precedenti in diversi modi:

- Eredita da `Activity`, non da `ListActivity`. È possibile personalizzare le righe per qualsiasi `ListView`, ma anche altri controlli possono essere inclusi in un layout di `Activity`, ad esempio un'intestazione, pulsanti o altri elementi dell'interfaccia utente. In questo esempio viene aggiunta un'intestazione sopra la `ListView` per illustrare.

- Richiede un file di layout AXML per la schermata. Negli esempi precedenti, il `ListActivity` non richiede un file di layout. Questo AXML contiene una dichiarazione di controllo `ListView`.

- Richiede un file di layout AXML per eseguire il rendering di ogni riga. Il file AXML contiene i controlli Text e image con impostazioni del tipo di carattere e del colore personalizzate.

- Usa un file XML selettore personalizzato facoltativo per impostare l'aspetto della riga quando viene selezionato.

- L'implementazione del `Adapter` restituisce un layout personalizzato dall'override del `GetView`.

- `ItemClick` deve essere dichiarata in modo diverso (un gestore eventi è associato a `ListView.ItemClick` anziché un `OnListItemClick` di override in `ListActivity`).

Queste modifiche sono descritte in dettaglio di seguito, a partire dalla creazione della visualizzazione dell'attività e della visualizzazione riga personalizzata, oltre a coprire le modifiche apportate alla scheda e all'attività per eseguirne il rendering.

### <a name="adding-a-listview-to-an-activity-layout"></a>Aggiunta di un controllo ListView a un layout di attività

Poiché `HomeScreen` non eredita più da `ListActivity` non ha una visualizzazione predefinita, quindi è necessario creare un file AXML di layout per la visualizzazione dell'HomeScreen. Per questo esempio, la vista avrà un'intestazione (usando un `TextView`) e una `ListView` per visualizzare i dati. Il layout viene definito nel file **Resources/layout/homescreen. aXML** , illustrato di seguito:

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

Il vantaggio di usare un `Activity` con un layout personalizzato, anziché un `ListActivity`, consiste nella possibilità di aggiungere altri controlli allo schermo, ad esempio l'intestazione `TextView` in questo esempio.

### <a name="creating-a-custom-row-layout"></a>Creazione di un layout di riga personalizzato

È necessario un altro file di layout AXML per contenere il layout personalizzato per ogni riga che verrà visualizzata nella visualizzazione elenco. In questo esempio la riga avrà uno sfondo verde, un testo marrone e un'immagine allineata a destra. Il markup XML di Android per dichiarare questo layout è descritto in **Resources/layout/CustomView. aXML**:

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

Mentre un layout di riga personalizzato può contenere molti controlli diversi, le prestazioni di scorrimento possono essere influenzate da progettazioni complesse e dall'uso di immagini, soprattutto se devono essere caricate in rete. Per ulteriori informazioni sull'indirizzamento dei problemi di prestazioni di scorrimento, vedere l'articolo di Google.

### <a name="referencing-a-custom-row-view"></a>Riferimento a una visualizzazione riga personalizzata

L'implementazione dell'esempio di adapter personalizzato è `HomeScreenAdapter.cs`. Il metodo Key è `GetView` in cui carica il AXML personalizzato usando l'ID risorsa `Resource.Layout.CustomView`, quindi imposta le proprietà in ogni controllo nella visualizzazione prima di restituirlo. Viene visualizzata la classe completa dell'adapter:

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

### <a name="referencing-the-custom-listview-in-the-activity"></a>Riferimento al controllo ListView personalizzato nell'attività

Poiché la classe `HomeScreen` ora eredita da `Activity`, un campo `ListView` viene dichiarato nella classe per mantenere un riferimento al controllo dichiarato in AXML:

```csharp
ListView listView;
```

La classe deve quindi caricare il layout personalizzato dell'attività AXML usando il metodo `SetContentView`. Può quindi trovare il controllo `ListView` nel layout e quindi crea e assegna la scheda e assegna il gestore di clic. Il codice per il metodo OnCreate è illustrato di seguito:

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

Infine, è necessario definire il gestore `ItemClick`; in questo caso viene visualizzato solo un messaggio di `Toast`:

```csharp
void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
   var listView = sender as ListView;
   var t = tableItems[e.Position];
   Android.Widget.Toast.MakeText(this, t.Heading, Android.Widget.ToastLength.Short).Show();
}
```

La schermata risultante ha un aspetto simile al seguente:

[![screenshot del CustomRowView risultante](customizing-appearance-images/customrowview.png)](customizing-appearance-images/customrowview.png#lightbox)

### <a name="customizing-the-row-selector-color"></a>Personalizzazione del colore del selettore di riga

Quando viene toccato, una riga deve essere evidenziata per i commenti degli utenti. Quando una visualizzazione personalizzata specifica come colore di sfondo come **CustomView. aXML** , sostituisce anche l'evidenziazione della selezione. Questa riga di codice in **CustomView. aXML** imposta lo sfondo su verde chiaro, ma significa anche che non è presente alcun indicatore visivo quando viene toccata la riga:

```xml
android:background="#FFDAFF7F"
```

Per riabilitare il comportamento dell'evidenziazione e anche per personalizzare il colore utilizzato, impostare l'attributo background su un selettore personalizzato. Il selettore dimostrerà sia il colore di sfondo predefinito che il colore di evidenziazione. Il file **Resources/disegnator/CustomSelector. XML** contiene la dichiarazione seguente:

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

Per fare riferimento al selettore personalizzato, modificare l'attributo background in **CustomView. aXML** in:

```xml
android:background="@drawable/CustomSelector"
```

Una riga selezionata e il messaggio di `Toast` corrispondente ora hanno un aspetto simile al seguente:

[![una riga selezionata in arancione, con messaggio popup che Visualizza il nome della riga selezionata](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png#lightbox)

### <a name="preventing-flickering-on-custom-layouts"></a>Prevenzione dello sfarfallio nei layout personalizzati

Android tenta di migliorare le prestazioni di `ListView` lo scorrimento memorizzando nella cache le informazioni di layout. Se sono presenti elenchi di dati a scorrimento prolunghi, è necessario impostare anche la proprietà `android:cacheColorHint` nella dichiarazione di `ListView` nella definizione AXML dell'attività (allo stesso valore di colore dello sfondo del layout di riga personalizzato). La mancata inclusione di questo hint può comportare un'sfarfallio ' quando l'utente scorre un elenco con colori di sfondo della riga personalizzati.

## <a name="related-links"></a>Collegamenti correlati

- [BuiltInViews (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/builtinviews)
- [AccessoryViews (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/accessoryviews)
- [CustomRowView (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/customrowview)
