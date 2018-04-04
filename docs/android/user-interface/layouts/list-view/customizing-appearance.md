---
title: Personalizzazione dell'aspetto di un controllo ListView
ms.prod: xamarin
ms.assetid: B09AD282-2C4F-D71E-6806-9B1EF05C2CD4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 71557cc0adb123cd4556c3d35add314f44f30da1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="customizing-a-listviews-appearance"></a>Personalizzazione dell'aspetto di un controllo ListView


## <a name="overview"></a>Panoramica

L'aspetto di un controllo ListView è determinato dal layout delle righe viene visualizzato. Per modificare l'aspetto di un `ListView`, utilizzare un layout di riga diversi.


## <a name="built-in-row-views"></a>Viste di riga predefinito

Sono disponibili dodici visualizzazioni predefinite che è possono farvi riferimento tramite **Android.Resource.Layout**:

- **TestListItem** &ndash; una riga di testo con formattazione minima.

- **SimpleListItem1** &ndash; una riga di testo.

- **SimpleListItem2** &ndash; due righe di testo.

- **SimpleSelectableListItem** &ndash; una riga di testo che supporta la selezione di uno o più elementi (aggiunta nel livello API 11).

- **SimpleListItemActivated1** &ndash; simile a SimpleListItem1, ma il colore di sfondo indica quando è selezionata una riga (aggiunto nel livello API 11).

- **SimpleListItemActivated2** &ndash; simile a SimpleListItem2, ma il colore di sfondo indica quando è selezionata una riga (aggiunto nel livello API 11).

- **SimpleListItemChecked** &ndash; consente di visualizzare i segni di spunta per indicare la selezione.

- **SimpleListItemMultipleChoice** &ndash; consente di visualizzare le caselle di controllo per indicare la selezione multipla.

- **SimpleListItemSingleChoice** &ndash; Visualizza i pulsanti per indicare la selezione reciprocamente esclusivi di opzione.

- **TwoLineListItem** &ndash; due righe di testo.

- **ActivityListItem** &ndash; una riga di testo con un'immagine.

- **SimpleExpandableListItem** &ndash; gruppi di righe da categorie e ogni gruppo possono essere espansi o compressi.

Ogni vista di riga predefinito con uno stile predefinito associato. Queste schermate mostrano l'aspetto di ogni visualizzazione:

[![Schermate di TestListItem, SimpleSelectableListItem, SimpleListitem1 e SimpleListItem2](customizing-appearance-images/builtinviews.png)](customizing-appearance-images/builtinviews.png#lightbox)

[![Schermate di SimpleListItemActivated1, SimpleListItemActivated2, SimpleListItemChecked e SimpleListItemMultipleChecked](customizing-appearance-images/builtinviews-2.png)](customizing-appearance-images/builtinviews-2.png#lightbox)

[![Schermate di SimpleListItemSingleChoice, TwoLineListItem, ActivityListItem e SimpleExpandableListItem](customizing-appearance-images/builtinviews-3.png)](customizing-appearance-images/builtinviews-3.png#lightbox)

Il **BuiltInViews/HomeScreenAdapter.cs** file di esempio (nel **BuiltInViews** soluzione) contiene il codice per produrre le schermate di elemento di elenco non espandibile. La visualizzazione è impostata `GetView` metodo simile al seguente:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
```

Quindi è possibile impostare le proprietà della vista facendo riferimento gli identificatori di controllo standard `Text1`, `Text2` e `Icon` in `Android.Resource.Id` (non impostare le proprietà che non contiene la vista oppure verrà generata un'eccezione):

```csharp
view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = item.Heading;
view.FindViewById<TextView>(Android.Resource.Id.Text2).Text = item.SubHeading;
view.FindViewById<ImageView>(Android.Resource.Id.Icon).SetImageResource(item.ImageResourceId); // only use with ActivityListItem
```

Il **BuiltInExpandableViews/ExpandableScreenAdapter.cs** file di esempio (nel **BuiltInViews** soluzione) contiene il codice per produrre la schermata di SimpleExpandableListItem. La visualizzazione del gruppo è impostata `GetGroupView` metodo simile al seguente:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem1, null);
```

La visualizzazione figlio è impostata `GetChildView` metodo simile al seguente:

```csharp
view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleExpandableListItem2, null);
```

Quindi impostare le proprietà per la visualizzazione del gruppo e della visualizzazione figlio facendo riferimento allo standard `Text1` e `Text2` gli identificatori di controllo, come illustrato in precedenza. Nella schermata di SimpleExpandableListItem (illustrata in precedenza) fornisce un esempio di una vista di una sola riga gruppo (SimpleExpandableListItem1) e una visualizzazione di due righe figlio (SimpleExpandableListItem2). In alternativa, la visualizzazione del gruppo può essere configurata per due righe (SimpleExpandableListItem2) e della visualizzazione figlio può essere configurata per una riga (SimpleExpandableListItem1), o entrambi gruppo Vista e visualizzazione figlio può avere lo stesso numero di righe. 



## <a name="accessories"></a>Accessori

Le righe possono avere Accessori aggiunti a destra della vista per indicare lo stato di selezione:

- **SimpleListItemChecked** &ndash; crea un elenco di selezione singola con un controllo dell'indicatore.

- **SimpleListItemSingleChoice** &ndash; consente di creare elenchi di tipo pulsante di opzione in cui è possibile solo scegliere.

- **SimpleListItemMultipleChoice** &ndash; consente di creare elenchi di tipo casella di controllo in cui sono possibili più opzioni.

Accessori menzionati in precedenza sono illustrati nelle schermate riportate di seguito, nel rispettivo ordine:

[![Schermate di SimpleListItemChecked SimpleListItemSingleChoice e SimpleListItemMultipleChoice con accessori](customizing-appearance-images/accessories.png)](customizing-appearance-images/accessories.png#lightbox)

Per visualizzare uno di questi passaggio accessori l'ID di risorsa di layout obbligatorie per l'adapter quindi imposta manualmente lo stato di selezione per le righe necessarie. Questa riga di codice viene illustrato come creare e assegnare un `Adapter` utilizzando uno di questi layout:

```csharp
ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItemChecked, items);
```

Il `ListView` stesso supporta diverse modalità di selezione, indipendentemente dalla funzione di accesso viene visualizzato. Per evitare confusione, utilizzare `Single` la modalità di selezione con `Checked` e `SingleChoice` accessori e `Multiple` la modalità con la `MultipleChoice` stile. La modalità di selezione è controllata dal `ChoiceMode` proprietà del `ListView`.


### <a name="handling-api-level"></a>Livello dell'API di gestione

Le versioni precedenti di xamarin implementato enumerazioni come proprietà integer. La versione più recente ha introdotto i tipi di enumerazione .NET corretti rende molto più semplice individuare le possibili opzioni.

A seconda di quale livello di API di destinazione, `ChoiceMode` è un numero intero o un'enumerazione. Il file di esempio **AccessoryViews/HomeScreen.cs** è un blocco di commento se si desidera indirizzare l'API pupazzi:

```csharp
// For targeting Gingerbread the ChoiceMode is an int, otherwise it is an
// enumeration.

lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // 1
//lv.ChoiceMode = Android.Widget.ChoiceMode.Multiple; // 2
//lv.ChoiceMode = Android.Widget.ChoiceMode.None; // 0

// Use this block if targeting Gingerbread or lower
/*
lv.ChoiceMode = Android.Widget.ChoiceMode.Single; // Single
//lv.ChoiceMode = 0; // none
//lv.ChoiceMode = 2; // Multiple
//lv.ChoiceMode = 3; // MultipleModal
*/
```


### <a name="selecting-items-programmatically"></a>Selezione di elementi a livello di codice

Impostare manualmente gli elementi che sono 'selezionata' viene eseguito con il `SetItemChecked` metodo (può essere chiamato più volte per la selezione multipla):

```csharp
// Set the initially checked row ("Fruits")
lv.SetItemChecked(1, true);
```

Inoltre, il codice deve rilevare selezioni singole in modo diverso dal più selezioni. Per determinare quale riga è stata selezionata nel `Single` modalità Usa il `CheckedItemPosition` proprietà a valore integer:

```csharp
FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPosition
```

Per determinare quali righe sono state selezionate `Multiple` modalità è necessario scorrere in ciclo i `CheckedItemPositions` `SparseBooleanArray`. Una matrice di tipo sparse è ad esempio un dizionario che contiene solo le voci in cui è stato modificato il valore, pertanto è necessario scorrere l'intera matrice cercando `true` valori sapere cosa è stata selezionata nell'elenco, come illustrato nel frammento di codice seguente:

```csharp
var sparseArray = FindViewById<ListView>(Android.Resource.Id.List).CheckedItemPositions;
for (var i = 0; i < sparseArray.Size(); i++ )
{
   Console.Write(sparseArray.KeyAt(i) + "=" + sparseArray.ValueAt(i) + ",");
}
Console.WriteLine();
```


## <a name="creating-custom-row-layouts"></a>Creazione di layout di riga personalizzato

Le quattro viste di riga predefinito sono molto semplici. Per visualizzare il layout più complessi (ad esempio un elenco di messaggi di posta elettronica, TWEET o informazioni di contatto) è richiesta una visualizzazione personalizzata. Visualizzazioni personalizzate in genere vengono dichiarate come file AXML il **risorse/Layout** directory e successivamente caricati con i relativi Id da una scheda personalizzata della risorsa. La vista può contenere qualsiasi numero di classi di visualizzazione, quali oggetti TextViews, ImageViews e altri controlli, layout, i tipi di carattere e colori personalizzati.

In questo esempio è diverso da negli esempi precedenti in diversi modi:

-  Eredita da `Activity` , non `ListActivity` . È possibile personalizzare le righe per qualsiasi `ListView` , ma anche altri controlli possono essere inclusi un `Activity` layout (ad esempio un titolo, i pulsanti o altri elementi dell'interfaccia utente). Questo esempio aggiunge un'intestazione sopra il `ListView` per illustrare.

-  Richiede un file di layout AXML per la schermata; Negli esempi precedenti la `ListActivity` non richiede un file di layout. Questo AXML contiene un `ListView` dichiarazione del controllo.

-  Richiede un file di layout AXML per eseguire il rendering di ogni riga. Questo file AXML contiene i controlli text e image con impostazioni di colore e tipo di carattere personalizzato.

-  Usa un file XML facoltativo selettore personalizzato per impostare l'aspetto della riga quando è selezionata.

-  Il `Adapter` implementazione restituisce un layout personalizzato dal `GetView` eseguire l'override.

-  `ItemClick` deve essere dichiarato in modo diverso (un gestore eventi è associato a `ListView.ItemClick` anziché un override `OnListItemClick` in `ListActivity`).


Queste modifiche sono descritte di seguito, a partire dalla creazione di visualizzazione dell'attività e la visualizzazione di riga personalizzato e quindi illustrando le modifiche per l'Adapter e il funzionamento per eseguirne il rendering.


### <a name="adding-a-listview-to-an-activity-layout"></a>Aggiunta di un controllo ListView a un Layout di attività

Poiché `HomeScreen` non erediti più da `ListActivity` non ha una visualizzazione predefinita, pertanto deve essere creato un file AXML layout per la visualizzazione dell'aggiornamento di già eseguito. Per questo esempio, la vista avranno un'intestazione (utilizzando un `TextView`) e un `ListView` per visualizzare i dati. Il layout è definito nel **Resources/Layout/HomeScreen.axml** file illustrata di seguito:

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

Il vantaggio di utilizzare un `Activity` con un layout personalizzato (anziché un `ListActivity`) consiste nella capacità di aggiungere ulteriori controlli sullo schermo, ad esempio l'intestazione `TextView` in questo esempio.


### <a name="creating-a-custom-row-layout"></a>Creazione di un Layout di riga personalizzato

Un altro file di layout AXML è necessaria per contenere il layout personalizzato per ogni riga che verrà visualizzato nella visualizzazione elenco. In questo esempio la riga avrà un sfondo verde, marrone testo e immagine allineata a destra. Viene descritto il markup XML Android per dichiarare il layout in **Resources/Layout/CustomView.axml**:

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

Mentre un layout di riga personalizzato può contenere molti diversi controlli, lo scorrimento delle prestazioni può essere influenzato da strutture complesse e con immagini (in particolare se devono essere caricati in rete). Vedere l'articolo di Google per ulteriori informazioni sull'indirizzamento di problemi relativi alle prestazioni di scorrimento.


### <a name="referencing-a-custom-row-view"></a>Riferimento a una vista personalizzata riga

L'implementazione dell'esempio di adapter personalizzato è `HomeScreenAdapter.cs`. Il metodo chiave è `GetView` in cui viene caricato il AXML personalizzato utilizzando l'ID di risorsa `Resource.Layout.CustomView`e quindi imposta le proprietà in ognuno dei controlli nella visualizzazione prima di restituirlo. La classe dell'adattatore completo è illustrata:

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


### <a name="referencing-the-custom-listview-in-the-activity"></a>ListView personalizzato nell'attività di riferimento

Poiché il `HomeScreen` ora eredita da `Activity`, `ListView` campo viene dichiarato nella classe per contenere un riferimento al controllo dichiarato nel AXML:

```csharp
ListView listView;
```

La classe deve quindi caricare il layout personalizzato dell'attività AXML utilizzando il `SetContentView` metodo. Quindi possibile trovare il `ListView` nel layout del controllo quindi crea e assegna l'adapter e assegna il gestore di fare clic su. Il codice per il metodo OnCreate è illustrato di seguito:

```csharp
SetContentView(Resource.Layout.HomeScreen); // loads the HomeScreen.axml as this activity's view
listView = FindViewById<ListView>(Resource.Id.List); // get reference to the ListView in the layout

// populate the listview with data
listView.Adapter = new HomeScreenAdapter(this, tableItems);
listView.ItemClick += OnListItemClick;  // to be defined
```

Infine il `ItemClick` deve essere definito; in questo caso verrà visualizzato un `Toast` messaggio:

```csharp
void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
   var listView = sender as ListView;
   var t = tableItems[e.Position];
   Android.Widget.Toast.MakeText(this, t.Heading, Android.Widget.ToastLength.Short).Show();
}
```

La schermata risultante è simile al seguente:

[![Schermata del CustomRowView risultante](customizing-appearance-images/customrowview.png)](customizing-appearance-images/customrowview.png#lightbox)



### <a name="customizing-the-row-selector-color"></a>Personalizzare il colore di selettore di riga

Quando una riga viene manipolata dovrebbe essere evidenziata per i commenti degli utenti. Quando una visualizzazione personalizzata specifica come il colore di sfondo come **CustomView.axml** , esegue l'override anche la selezione evidenziata. Questa riga di codice in **CustomView.axml** imposta lo sfondo a verde chiaro, ma significa inoltre non è presente alcun indicatore quando viene eseguita la riga:

```xml
android:background="#FFDAFF7F"
```

Per riabilitare il comportamento di evidenziazione e anche per personalizzare il colore che viene utilizzato, impostare l'attributo di sfondo per un selettore personalizzato. Il selettore verrà dichiarato sia il colore di sfondo predefinito così come il colore di evidenziazione. Il file **Resources/Drawable/CustomSelector.xml** contiene la dichiarazione seguente:

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

Per fare riferimento il selettore personalizzato, modificare l'attributo background **CustomView.axml** per:

```xml
android:background="@drawable/CustomSelector"
```

Una riga selezionata e i corrispondenti `Toast` messaggio avrà l'aspetto seguente:

[![Una riga selezionata in arancione, con il messaggio di tipo avviso popup visualizzazione nome della riga selezionata](customizing-appearance-images/customselectcolor.png)](customizing-appearance-images/customselectcolor.png#lightbox)



### <a name="preventing-flickering-on-custom-layouts"></a>Evitare lo sfarfallio dei layout personalizzati

Android tenta di migliorare le prestazioni di `ListView` lo scorrimento della memorizzazione nella cache le informazioni di layout. Se si dispone di scorrimento tempo gli elenchi di dati è necessario impostare anche l'il `android:cacheColorHint` proprietà il `ListView` dichiarazione nella definizione di AXML dell'attività (per lo stesso valore di colore di sfondo del layout di riga personalizzato). Per includere questo hint può verificarsi in un 'lo sfarfallio' come l'utente scorre un elenco con i colori di sfondo di riga personalizzato.



## <a name="related-links"></a>Collegamenti correlati

- [BuiltInViews (esempio)](https://developer.xamarin.com/samples/BuiltInViews/)
- [AccessoryViews (esempio)](https://developer.xamarin.com/samples/AccessoryViews/)
- [CustomRowView (esempio)](https://developer.xamarin.com/samples/CustomRowView/)
