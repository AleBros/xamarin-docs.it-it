---
title: Popolamento di un controllo ListView con dati
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2017
ms.openlocfilehash: f3162c4da092048cd409f7b32438bc85dcedff19
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740795"
---
# <a name="populating-a-listview-with-data"></a>Popolamento di un controllo ListView con dati


## <a name="overview"></a>Panoramica

Per aggiungere righe a un `ListView` è necessario aggiungerlo al layout e implementare un' `IListAdapter` con i metodi che il `ListView` chiamate a popolarsi. Android include incorporato `ListActivity` e `ArrayAdapter` classi che è possibile usare senza definire qualsiasi codice o il layout personalizzato XML. Il `ListActivity` classe crea automaticamente un `ListView` ed espone un `ListAdapter` proprietà per fornire le visualizzazioni di righe da visualizzare tramite un adapter.

L'adapter predefiniti hanno un ID di risorsa vista come un parametro che viene usato per ogni riga. È possibile usare le risorse predefinite, ad esempio quelli in `Android.Resource.Layout` in modo che non è necessario scrivere il proprio.


## <a name="using-listactivity-and-arrayadapterltstringgt"></a>Utilizzo ListActivity e ArrayAdapter&lt;stringa&gt;

L'esempio **BasicTable/HomeScreen.cs** illustra come usare queste classi per visualizzare un `ListView` in solo poche righe di codice:

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


### <a name="handling-row-clicks"></a>Gestione delle righe fa clic su

In genere un `ListView` anche consentirà all'utente di toccare una riga per eseguire un'azione (ad esempio la riproduzione di un brano, la chiamata a un contatto o visualizzazione di un'altra schermata). Per rispondere a tocchi utente deve essere uno più metodo implementato nel `ListActivity` &ndash; `OnListItemClick` &ndash; simile al seguente:

[![Screenshot di un SimpleListItem](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

A questo punto l'utente può toccare una riga e un `Toast` verrà visualizzato l'avviso:

[![Schermata di tipo avviso popup che viene visualizzato quando una riga selezionata](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)


## <a name="implementing-a-listadapter"></a>Implementazione di un ListAdapter

`ArrayAdapter<string>` è molto utile a causa la sua semplicità, ma è molto limitato. Tuttavia, spesso si ha una raccolta di entità di business, anziché semplicemente di stringhe che si desidera associare.
Ad esempio, se i dati è costituito da una raccolta di classi di Employee, quindi è possibile l'elenco per visualizzare solo i nomi di ogni dipendente. Per personalizzare il comportamento di un `ListView` per controllare quali dati vengono visualizzati è necessario implementare una sottoclasse di `BaseAdapter` viene sottoposto a override i quattro elementi seguenti:

-   **Conteggio** &ndash; per indicare il controllo è il numero di righe nei dati.

-   **GetView** &ndash; per restituire una visualizzazione per ogni riga, popolato con i dati.
    Questo metodo contiene un parametro per il `ListView` da passare in una riga esistente e non usata per essere riusata.

-   **GetItemId** &ndash; restituire un identificatore di riga (in genere la riga numero, anche se può essere qualsiasi valore long che si desidera).

-   **Questa [int]** indicizzatore &ndash; per restituire i dati associati a un numero di riga specifico.

Il codice di esempio nella **BasicTableAdapter/HomeScreenAdapter.cs** di seguito viene illustrato come sottoclasse `BaseAdapter`:

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


### <a name="using-a-custom-adapter"></a>Utilizza un Adapter personalizzato

Utilizzo dell'adapter personalizzato è simile all'elemento predefinito `ArrayAdapter`, passando un `context` e il `string[]` di valori da visualizzare:

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

Poiché questo esempio viene usato lo stesso layout di riga (`SimpleListItem1`) l'applicazione risultante sarà identico all'esempio precedente.


### <a name="row-view-re-use"></a>Riutilizzo delle visualizzazioni riga

In questo esempio esistono solo sei elementi. Poiché la schermata è possibile adattare otto alcuna riga usare di nuovo non necessari. Quando si visualizzano centinaia o migliaia di righe, tuttavia, sarebbe uno spreco di memoria creare centinaia o migliaia di `View` oggetti quando solo otto adattano alla schermata alla volta. Per evitare questa situazione, quando una riga viene rimossa dallo schermo che la visualizzazione viene inserita in una coda per essere riusata. Mentre l'utente scorre, il `ListView` chiamate `GetView` per richiedere nuove visualizzazioni da visualizzare &ndash; se disponibile passa una visualizzazione inutilizzata nel `convertView` parametro. Se questo valore è null, quindi il codice deve creare una nuova istanza di visualizzazione, in caso contrario, è possibile impostare nuovamente le proprietà di quell'oggetto e riutilizzare.

Il `GetView` metodo consigliabile seguire questo modello per riutilizzare le visualizzazioni delle righe:

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

Implementazioni dell'adattatore personalizzato dovrebbero *sempre* riutilizzare il `convertView` oggetto prima di creare nuove visualizzazioni per assicurarsi che questi non eseguono memoria insufficiente durante la visualizzazione di lunghi elenchi.

Alcune implementazioni dell'adattatore (come il `CursorAdapter`) non ha un `GetView` metodo, piuttosto richiedono due metodi diversi `NewView` e `BindView` cui applicare la riga usare di nuovo separando le responsabilità di `GetView` in due metodi. È presente un `CursorAdapter` riportato più avanti nel documento.


## <a name="enabling-fast-scrolling"></a>Abilitare lo scorrimento veloce

Scorrimento rapido consente all'utente di scorrere lunghi elenchi, fornendo un 'handle' aggiuntivo che agisce come una barra di scorrimento per accedere direttamente a una parte dell'elenco. In questo screenshot appare il quadratino di scorrimento rapido:

[![Screenshot dello scorrimento rapido con un handle di scorrimento](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

Causa il quadratino di scorrimento rapido venga visualizzato è semplice come l'impostazione di `FastScrollEnabled` proprietà `true`:

```csharp
ListView.FastScrollEnabled = true;
```


### <a name="adding-a-section-index"></a>Aggiunta di un indice di sezione

Un indice di sezione offre ulteriori commenti e suggerimenti per gli utenti quando sono lo scorrimento veloce di un lungo elenco &ndash; mostra che sono state fatte scorrere 'section'. Per fare in modo l'indice della sezione venga visualizzato nella sottoclasse Adapter deve implementare il `ISectionIndexer` interfaccia per fornire il testo di indice in base a righe visualizzato:

[![Schermata di ore visualizzato sopra la sezione che inizia con H](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

Per implementare `ISectionIndexer` è necessario aggiungere tre metodi per un adattatore:

-   **GetSections** &ndash; fornisce l'elenco completo delle sezione titoli di indice che potrebbe essere visualizzati. Questo metodo richiede una matrice di oggetti Java in modo che il codice deve creare un `Java.Lang.Object[]` da una raccolta di .NET. In questo esempio viene restituito un elenco dei caratteri iniziali nell'elenco come `Java.Lang.String` .

-   **GetPositionForSection** &ndash; restituisce la prima posizione di riga per un indice di sezione specificato.

-   **GetSectionForPosition** &ndash; restituisce l'indice della sezione da visualizzare per una determinata riga.


L'esempio `SectionIndex/HomeScreenAdapter.cs` file implementa i metodi e altro codice nel costruttore. Il costruttore si basa l'indice della sezione e scorrimento in ciclo tra ogni riga e l'estrazione il primo carattere del titolo (gli elementi devono essere già ordinati per il corretto funzionamento).

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

Con le strutture di dati create, il `ISectionIndexer` metodi sono molto semplici:

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

I titoli di indice della sezione non devono eseguire il mapping 1:1 a sezioni delle effettive. È per questo motivo il `GetPositionForSection` esiste alcun metodo.
`GetPositionForSection` offre la possibilità di eseguire il mapping di qualsiasi gli indici sono nell'elenco di indice per tutte le sezioni sono nella visualizzazione elenco. Ad esempio, potrebbe essere una "z" nell'indice, ma non si dispone di una sezione di tabella per ogni lettera, in modo invece di mapping di "z" alla 26, potrebbe eseguire il mapping a 25 o 24, indice o di qualsiasi sezione "z" deve essere mappato a.



## <a name="related-links"></a>Collegamenti correlati

- [BasicTableAndroid (sample)](https://developer.xamarin.com/samples/monodroid/BasicTableAndroid/)
- [BasicTableAdapter (esempio)](https://developer.xamarin.com/samples/monodroid/BasicTableAdapter/)
- [FastScroll (esempio)](https://developer.xamarin.com/samples/monodroid/FastScroll/)
