---
title: Popolamento di un controllo ListView Novell. Android con dati
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: e92aada7be8a296baeaa9eebfb18fe906b5c3b63
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522536"
---
# <a name="populating-a-xamarinandroid-listview-with-data"></a>Popolamento di un controllo ListView Novell. Android con dati

Per aggiungere righe a un `ListView` oggetto, è necessario aggiungerlo al layout e implementare un `IListAdapter` oggetto con i metodi `ListView` che il chiama per popolare. Android include le classi `ListActivity` e `ArrayAdapter` predefinite che è possibile usare senza definire codice o XML di layout personalizzato. La `ListActivity` classe crea automaticamente un `ListView` oggetto ed espone `ListAdapter` una proprietà per fornire le visualizzazioni di riga da visualizzare tramite un adapter.

Gli adapter incorporati accettano un ID risorsa di visualizzazione come parametro che viene usato per ogni riga. È possibile usare risorse predefinite, ad esempio quelle in `Android.Resource.Layout` , per cui non è necessario scrivere.

## <a name="using-listactivity-and-arrayadapterltstringgt"></a>Uso della stringa ListActivity&lt;e classe ArrayAdapter&gt;

Nell'esempio **BasicTable/homescreen. cs** viene illustrato come utilizzare queste classi per visualizzare un `ListView` solo in poche righe di codice:

```csharp
[Activity(Label = "BasicTable", MainLauncher = true, Icon = "@drawable/icon")]
public class HomeScreen : ListActivity {
   string[] items;
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       items = new string[] { "Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers" };
       ListAdapter = new ArrayAdapter<String>(this, Android.Resource.Layout.SimpleListItem1, items);
   }
   protected override void OnListItemClick(ListView l, View v, int position, long id)
}
```


### <a name="handling-row-clicks"></a>Gestione dei clic di riga

In genere `ListView` un oggetto consente inoltre all'utente di toccare una riga per eseguire un'azione, ad esempio la riproduzione di un brano o la chiamata di un contatto o la visualizzazione di un'altra schermata. Per rispondere ai tocchi degli utenti, è necessario disporre di un altro metodo implementato `ListActivity` in &ndash; `OnListItemClick` &ndash; questo esempio:

[![Screenshot di un SimpleListItem](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

Ora l'utente può toccare una riga e verrà `Toast` visualizzato un avviso:

[![Screenshot del popup visualizzato quando viene toccata una riga](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)


## <a name="implementing-a-listadapter"></a>Implementazione di un ListAdapter

`ArrayAdapter<string>`è molto grande grazie alla sua semplicità, ma è estremamente limitato. Tuttavia, spesso si ha una raccolta di entità di business, anziché solo le stringhe che si desidera associare.
Se, ad esempio, i dati sono costituiti da una raccolta di classi Employee, è possibile che l'elenco visualizzi semplicemente i nomi di ogni dipendente. Per personalizzare il comportamento di un `ListView` oggetto per controllare quali dati vengono visualizzati, è necessario implementare una `BaseAdapter` sottoclasse dell'override dei quattro elementi seguenti:

- **Numero** di &ndash; Per indicare al controllo il numero di righe presenti nei dati.

- **GetView** &ndash; Per restituire una visualizzazione per ogni riga, popolata con i dati.
    Questo metodo dispone di un parametro affinché `ListView` l'oggetto passi una riga esistente non utilizzata per riutilizzarla.

- **GetItemID** &ndash; Restituisce un identificatore di riga, in genere il numero di riga, anche se può essere qualsiasi valore lungo desiderato.

- Questo indicizzatore &ndash; **[int]** per restituire i dati associati a un numero di riga specifico.

Il codice di esempio in **BasicTableAdapter/HomeScreenAdapter. cs** Mostra come sottoclasse `BaseAdapter`:

```csharp
public class HomeScreenAdapter : BaseAdapter<string> {
   string[] items;
   Activity context;
   public HomeScreenAdapter(Activity context, string[] items) : base() {
       this.context = context;
       this.items = items;
   }
   public override long GetItemId(int position)
  {
       return position;
   }
   public override string this[int position] {  
       get { return items[position]; }
   }
   public override int Count {
       get { return items.Length; }
   }
   public override View GetView(int position, View convertView, ViewGroup parent)
   {
       View view = convertView; // re-use an existing view, if one is available
      if (view == null) // otherwise create a new one
           view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
       view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
       return view;
   }
}
```


### <a name="using-a-custom-adapter"></a>Utilizzo di un adapter personalizzato

L'utilizzo dell'adapter personalizzato è simile a quello incorporato `ArrayAdapter`, passando un oggetto `context` e l'oggetto `string[]` di valori da visualizzare:

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

Poiché in questo esempio viene utilizzato lo stesso layout`SimpleListItem1`di riga (), l'applicazione risultante sarà identica all'esempio precedente.


### <a name="row-view-re-use"></a>Riutilizzo della visualizzazione righe

In questo esempio sono presenti solo sei elementi. Poiché la schermata può adattarsi a otto, non è necessario riutilizzare la riga. Quando si visualizzano centinaia o migliaia di righe, tuttavia, sarebbe uno spreco di memoria per creare centinaia o migliaia di `View` oggetti quando solo otto si adattano allo schermo alla volta. Per evitare questa situazione, quando una riga scompare dallo schermo, la relativa visualizzazione viene inserita in una coda per riutilizzarla. Quando l'utente scorre, `ListView` le chiamate `GetView` per richiedere nuove visualizzazioni per visualizzare &ndash; se disponibile passano una visualizzazione inutilizzata nel `convertView` parametro. Se questo valore è null, il codice deve creare una nuova istanza di visualizzazione. in caso contrario, è possibile reimpostare le proprietà dell'oggetto e riutilizzarlo.

Il `GetView` metodo deve seguire questo modello per riutilizzare le visualizzazioni di riga:

```csharp
public override View GetView(int position, View convertView, ViewGroup parent)
{
   View view = convertView; // re-use an existing view, if one is supplied
   if (view == null) // otherwise create a new one
       view = context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, null);
   // set view properties to reflect data for the given row
   view.FindViewById<TextView>(Android.Resource.Id.Text1).Text = items[position];
   // return the view, populated with data, for display
   return view;
}
```

Le implementazioni degli adapter personalizzati devono *sempre* riutilizzare `convertView` l'oggetto prima di creare nuove visualizzazioni per assicurarsi che non esaurisca la memoria durante la visualizzazione di elenchi lunghi.

Alcune implementazioni di adapter (ad esempio `CursorAdapter`) non dispongono di `GetView` un metodo, ma richiedono due metodi `NewView` diversi e `BindView` che applicano il riutilizzo delle righe separando le `GetView` responsabilità di in due Metodi. Di seguito è `CursorAdapter` riportato un esempio del documento.


## <a name="enabling-fast-scrolling"></a>Abilitazione dello scorrimento rapido

Lo scorrimento rapido consente all'utente di scorrere gli elenchi lunghi fornendo un'handle ' aggiuntivo che funge da barra di scorrimento per accedere direttamente a una parte dell'elenco. Questa schermata mostra il quadratino di scorrimento rapido:

[![Screenshot dello scorrimento rapido con un handle di scorrimento](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

La visualizzazione dell'handle di scorrimento rapido è semplice come impostare la `FastScrollEnabled` proprietà su: `true`

```csharp
ListView.FastScrollEnabled = true;
```


### <a name="adding-a-section-index"></a>Aggiunta di un indice di sezione

Un indice di sezione fornisce commenti e suggerimenti aggiuntivi per gli utenti quando eseguono lo scorrimento rapido in un &ndash; lungo elenco che mostra a quale "sezione" è stato eseguito lo scorrimento. Per far sì che l'indice della sezione venga visualizzato, la sottoclasse dell'adattatore deve implementare l' `ISectionIndexer` interfaccia per fornire il testo dell'indice a seconda delle righe visualizzate:

[![Screenshot di H visualizzato sopra la sezione che inizia con H](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

Per implementare `ISectionIndexer` è necessario aggiungere tre metodi a un adapter:

- GetSections &ndash; Fornisce l'elenco completo dei titoli degli indici di sezione che possono essere visualizzati. Questo metodo richiede una matrice di oggetti Java in modo che il codice debba creare `Java.Lang.Object[]` un oggetto da una raccolta .NET. Nell'esempio viene restituito un elenco dei caratteri iniziali nell'elenco come `Java.Lang.String` .

- **GetPositionForSection** &ndash; Restituisce la prima posizione di riga per un indice di sezione specificato.

- **GetSectionForPosition** &ndash; Restituisce l'indice della sezione da visualizzare per una determinata riga.


Il file `SectionIndex/HomeScreenAdapter.cs` di esempio implementa tali metodi e il codice aggiuntivo nel costruttore. Il costruttore compila l'indice della sezione eseguendo il ciclo di ogni riga ed estraendo il primo carattere del titolo (gli elementi devono essere già ordinati affinché questo funzioni).

```csharp
alphaIndex = new Dictionary<string, int>();
for (int i = 0; i < items.Length; i++) { // loop through items
   var key = items[i][0].ToString();
   if (!alphaIndex.ContainsKey(key))
       alphaIndex.Add(key, i); // add each 'new' letter to the index
}
sections = new string[alphaIndex.Keys.Count];
alphaIndex.Keys.CopyTo(sections, 0); // convert letters list to string[]

// Interface requires a Java.Lang.Object[], so we create one here
sectionsObjects = new Java.Lang.Object[sections.Length];
for (int i = 0; i < sections.Length; i++) {
   sectionsObjects[i] = new Java.Lang.String(sections[i]);
}
```

Con le strutture di dati create, `ISectionIndexer` i metodi sono molto semplici:

```csharp
public Java.Lang.Object[] GetSections()
{
   return sectionsObjects;
}
public int GetPositionForSection(int section)
{
   return alphaIndexer[sections[section]];
}
public int GetSectionForPosition(int position)
{   // this method isn't called in this example, but code is provided for completeness
    int prevSection = 0;
    for (int i = 0; i < sections.Length; i++)
    {
        if (GetPositionForSection(i) > position)
        {
            break;
        }
        prevSection = i;
    }
    return prevSection;
}
```

I titoli degli indici di sezione non devono eseguire il mapping di 1:1 alle sezioni effettive. Questo è il motivo `GetPositionForSection` per cui esiste il metodo.
`GetPositionForSection`offre la possibilità di eseguire il mapping di tutti gli indici presenti nell'elenco di indici a tutte le sezioni presenti nella visualizzazione elenco. Ad esempio, è possibile che nell'indice sia presente una "z", ma è possibile che non si disponga di una sezione di tabella per ogni lettera, quindi invece di "z" mapping a 26, è possibile eseguire il mapping a 25 o 24 o a qualsiasi indice di sezione "z" a cui eseguire il mapping.



## <a name="related-links"></a>Collegamenti correlati

- [BasicTableAndroid (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableandroid)
- [BasicTableAdapter (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/basictableadapter)
- [FastScroll (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fastscroll)
