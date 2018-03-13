---
title: Popolamento di un controllo ListView con dati
ms.topic: article
ms.prod: xamarin
ms.assetid: AC4F95C8-EC3F-D960-7D44-8D55D0E4F1B6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 08/21/2017
ms.openlocfilehash: 12197d238ddc6ddc2bd8f48f77aa15f5eff22a0a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="populating-a-listview-with-data"></a>Popolamento di un controllo ListView con dati


## <a name="overview"></a>Panoramica

Per aggiungere righe a un `ListView` è necessario aggiungerla al layout e implementare un `IListAdapter` con metodi che il `ListView` per popolare automaticamente le chiamate. Android include incorporato `ListActivity` e `ArrayAdapter` le classi che è possibile utilizzare senza definire un layout personalizzato XML o codice. Il `ListActivity` classe crea automaticamente un `ListView` ed espone un `ListAdapter` proprietà per fornire le visualizzazioni di righe da visualizzare tramite un adapter.

Adattatori incorporati accettano un ID di risorsa vista come un parametro che viene utilizzato per ogni riga. È possibile utilizzare le risorse predefinite, ad esempio quelli in `Android.Resource.Layout` è necessario scrivere la propria.


## <a name="using-listactivity-and-arrayadapterltstringgt"></a>Utilizzo di ListActivity e ArrayAdapter&lt;stringa&gt;

Nell'esempio **BasicTable/HomeScreen.cs** di seguito viene illustrato come utilizzare queste classi per visualizzare un `ListView` in poche righe di codice:

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

In genere un `ListView` anche consentire all'utente di toccare una riga per eseguire un'azione (ad esempio la riproduzione di un brano, la chiamata di un contatto o con un'altra schermata). Per rispondere a ritocchi utente deve essere un ulteriore metodo implementato nel `ListActivity` &ndash; `OnListItemClick` &ndash; come segue:

[![Schermata di un SimpleListItem](populating-images/simplelistitem1.png)](populating-images/simplelistitem1.png#lightbox)

```csharp
protected override void OnListItemClick(ListView l, View v, int position, long id)
{
   var t = items[position];
   Android.Widget.Toast.MakeText(this, t, Android.Widget.ToastLength.Short).Show();
}
```

Ora l'utente può toccare una riga e un `Toast` verrà visualizzato l'avviso:

[![Schermata di tipo avviso popup che viene visualizzato quando una riga è interessata dal](populating-images/basictable2.png)](populating-images/basictable2.png#lightbox)


## <a name="implementing-a-listadapter"></a>Implementazione di un ListAdapter

`ArrayAdapter<string>` è molto utile per la semplicità, ma molto limitato. Tuttavia, spesso è una raccolta di entità aziendali, anziché solo le stringhe che si desidera associare.
Ad esempio, se i dati è costituita da una raccolta di classi di dipendente, si consiglia l'elenco per visualizzare solo i nomi di ogni dipendente. Per personalizzare il comportamento di un `ListView` per controllare quali dati vengono visualizzati è necessario implementare una sottoclasse di `BaseAdapter` si esegue l'override di quattro elementi seguenti:

-   **Conteggio** &ndash; di individuare il controllo il numero di righe nei dati.

-   **GetView** &ndash; per restituire una visualizzazione per ogni riga, popolato con i dati.
    Questo metodo presenta un parametro per il `ListView` per passare in una riga esistente e inutilizzata per il riutilizzo.

-   **GetItemId** &ndash; restituire un identificatore di riga (in genere la riga numero, anche se può essere qualsiasi valore long che si desidera).

-   **[int]** indicizzatore &ndash; per restituire i dati associati a un numero di riga specifico.

Nell'esempio di codice in **BasicTableAdapter/HomeScreenAdapter.cs** di seguito viene illustrato come creare una sottoclasse `BaseAdapter`:

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

Utilizzo dell'adapter personalizzato è simile a predefinito `ArrayAdapter`, passando un `context` e `string[]` di valori da visualizzare:

```csharp
ListAdapter = new HomeScreenAdapter(this, items);
```

Poiché questo esempio viene utilizzato lo stesso layout di riga (`SimpleListItem1`) dell'applicazione risultante sarà identico all'esempio precedente.


### <a name="row-view-re-use"></a>Riutilizzo dei visualizzazione riga

In questo esempio esistono solo sei elementi. Poiché la schermata è possibile adattare otto, nessuna riga riutilizzare è obbligatorio. Quando si visualizzano centinaia o migliaia di righe, tuttavia, sarebbe uno spreco di memoria per creare centinaia o migliaia di `View` gli oggetti quando solo otto visualizzati sullo schermo contemporaneamente. Per evitare questa situazione, quando una riga viene rimosso dallo schermo che la visualizzazione viene inserita in una coda per il riutilizzo. Come l'utente scorre, il `ListView` chiamate `GetView` per richiedere nuove visualizzazioni per visualizzare &ndash; se disponibile passa una visualizzazione non utilizzata nel `convertView` parametro. Se questo valore è null, il codice è necessario creare una nuova istanza di visualizzazione, in caso contrario è possibile impostare nuovamente la proprietà dell'oggetto e riutilizzare.

Il `GetView` metodo deve seguire questo modello per riutilizzare le viste delle righe:

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

Implementazioni dell'adattatore personalizzato devono *sempre* riutilizzare il `convertView` oggetto prima di creare nuove visualizzazioni per assicurarsi che questi non eseguono memoria insufficiente durante la visualizzazione di lunghi elenchi.

Alcune implementazioni dell'adattatore (ad esempio il `CursorAdapter`) non dispone di un `GetView` metodo, piuttosto che richiedono due metodi diversi `NewView` e `BindView` riutilizza la riga che applicare la separazione delle responsabilità di `GetView` in due metodi. È presente un `CursorAdapter` riportato più avanti nel documento.


## <a name="enabling-fast-scrolling"></a>Abilitare lo scorrimento rapido

Scorrimento rapido consente all'utente di scorrere lunghi elenchi fornendo un 'handle' aggiuntivo che si comporta come una barra di scorrimento per accedere direttamente a una parte dell'elenco. Questa schermata mostra l'handle di scorrimento rapido:

[![Schermata di scorrimento rapido con un handle di scorrimento](populating-images/fastscroll.png)](populating-images/fastscroll.png#lightbox)

Causando il quadratino scorrimento rapido è semplice come l'impostazione di `FastScrollEnabled` proprietà `true`:

```csharp
ListView.FastScrollEnabled = true;
```


### <a name="adding-a-section-index"></a>Aggiunta di un indice della sezione

Un indice di sezione offre ulteriori commenti e suggerimenti per gli utenti quando sono fast-scorrere un lungo elenco &ndash; Mostra la sezione è stato effettuato lo scorrimento. Affinché l'indice della sezione vengono visualizzate la sottoclasse dell'Adapter deve implementare il `ISectionIndexer` interfaccia per fornire il testo di indice in base a righe visualizzato:

[![Schermata di H visualizzato sopra la sezione che inizia con H](populating-images/sectionindex.png)](populating-images/sectionindex.png#lightbox)

Per implementare `ISectionIndexer` è necessario aggiungere i tre metodi a un adattatore:

-   **GetSections** &ndash; fornisce l'elenco completo di sezione titoli di indice che potrebbe essere visualizzati. Questo metodo richiede una matrice di oggetti Java, pertanto è necessario creare un `Java.Lang.Object[]` da una raccolta di .NET. In questo esempio viene restituito un elenco dei caratteri iniziali nell'elenco come `Java.Lang.String` .

-   **GetPositionForSection** &ndash; restituisce la prima posizione di riga per un indice della sezione specificata.

-   **GetSectionForPosition** &ndash; restituisce l'indice della sezione da visualizzare per una determinata riga.


Nell'esempio `SectionIndex/HomeScreenAdapter.cs` file implementa i metodi e altro codice nel costruttore. Il costruttore crea l'indice di sezione scorrendo ogni riga ed estrarre il primo carattere del titolo (gli elementi devono essere già ordinati per il funzionamento).

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

Le strutture di dati creato, il `ISectionIndexer` metodi sono molto semplici:

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

I titoli di indice della sezione non necessario eseguire il mapping 1:1 per le sezioni effettive. Questo il motivo per il `GetPositionForSection` metodo esistente.
`GetPositionForSection` offre la possibilità di eseguire il mapping di tutti gli indici presenti nell'elenco dell'indice per tutte le sezioni sono nella visualizzazione elenco. Ad esempio, è possibile "z" nell'indice, ma non si dispone di una sezione di tabella per ogni lettera, in modo invece di mapping di "z" e 26, potrebbe eseguire il mapping a 25 o 24 o qualsiasi indice di sezione "z" deve essere mappato allo.



## <a name="related-links"></a>Collegamenti correlati

- [BasicTableAndroid (esempio)](https://developer.xamarin.com/samples/BasicTableAndroid/)
- [BasicTableAdapter (esempio)](https://developer.xamarin.com/samples/BasicTableAdapter/)
- [FastScroll (esempio)](https://developer.xamarin.com/samples/FastScroll/)
