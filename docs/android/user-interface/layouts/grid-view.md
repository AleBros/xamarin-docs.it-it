---
title: GridView
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 6409a4cb65186cade454253e1afdd38a66f3357f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028982"
---
# <a name="xamarinandroid-gridview"></a>GridView Xamarin.Android

[`GridView`](xref:Android.Widget.GridView) è un [`ViewGroup`](xref:Android.Views.ViewGroup)
che consente di visualizzare gli elementi in una griglia bidimensionale scorrevole. Gli elementi della griglia vengono inseriti automaticamente nel layout utilizzando un [`ListAdapter`](xref:Android.App.ListActivity.ListAdapter).

In questa esercitazione verrà creata una griglia di anteprime delle immagini. Quando si seleziona un elemento, in un messaggio di avviso popup viene visualizzata la posizione dell'immagine.

Avviare un nuovo progetto denominato **HelloGridView**.

Trovare alcune foto che si vuole usare o [scaricare queste immagini di esempio](https://developer.android.com/shareables/sample_images.zip). Aggiungere i file di immagine alla directory **Resources/disegnator** del progetto. Nella finestra **Proprietà** impostare l'azione di compilazione per ogni su **AndroidResource**.

Aprire il file **Resources/layout/Main. aXML** e inserire quanto segue:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gridview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:columnWidth="90dp"
    android:numColumns="auto_fit"
    android:verticalSpacing="10dp"
    android:horizontalSpacing="10dp"
    android:stretchMode="columnWidth"
    android:gravity="center"
/>
```

Questa [`GridView`](xref:Android.Widget.GridView) compilerà l'intero schermo. Gli attributi sono piuttosto evidenti. Per ulteriori informazioni sugli attributi validi, vedere il riferimento [`GridView`](xref:Android.Widget.GridView) .

Aprire `HelloGridView.cs` e inserire il codice seguente per la [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
Metodo

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    var gridview = FindViewById<GridView> (Resource.Id.gridview);
    gridview.Adapter = new ImageAdapter (this);

    gridview.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

Dopo aver impostato il layout **Main. aXML** per la visualizzazione contenuto, l' [`GridView`](xref:Android.Widget.GridView) viene acquisita dal layout con [`FindViewById`](xref:Android.App.Activity.FindViewById*). [`Adapter`](xref:Android.Widget.AdapterView.RawAdapter)
la proprietà viene quindi utilizzata per impostare un adapter personalizzato (`ImageAdapter`) come origine per tutti gli elementi da visualizzare nella griglia. Il `ImageAdapter` viene creato nel passaggio successivo.

Per eseguire un'operazione quando si fa clic su un elemento nella griglia, viene sottoscritto un delegato anonimo per l'evento [`ItemClick`](xref:Android.Widget.AdapterView.ItemClick) .
Mostra un [`Toast`](xref:Android.Widget.Toast) che visualizza la posizione di indice (in base zero) dell'elemento selezionato (in uno scenario reale, la posizione può essere usata per ottenere l'immagine con dimensioni complete per un'altra attività). Si noti che è possibile usare le classi listener di tipo Java anziché gli eventi .NET.

Creare una nuova classe denominata `ImageAdapter` sottoclassi [`BaseAdapter`](xref:Android.Widget.BaseAdapter):

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
        context = c;
    }

    public override int Count {
        get { return thumbIds.Length; }
    }

    public override Java.Lang.Object GetItem (int position)
    {
        return null;
    }

    public override long GetItemId (int position)
    {
        return 0;
    }

    // create a new ImageView for each item referenced by the Adapter
    public override View GetView (int position, View convertView, ViewGroup parent)
    {
        ImageView imageView;

        if (convertView == null) {  // if it's not recycled, initialize some attributes
            imageView = new ImageView (context);
            imageView.LayoutParameters = new GridView.LayoutParams (85, 85);
            imageView.SetScaleType (ImageView.ScaleType.CenterCrop);
            imageView.SetPadding (8, 8, 8, 8);
        } else {
            imageView = (ImageView)convertView;
        }

        imageView.SetImageResource (thumbIds[position]);
        return imageView;
    }

    // references to our images
    int[] thumbIds = {
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7
    };
}
```

In primo luogo, implementa alcuni metodi obbligatori ereditati da [`BaseAdapter`](xref:Android.Widget.BaseAdapter). Il costruttore e la proprietà [`Count`](xref:Android.Widget.BaseAdapter.Count) sono di chiara interpretazione. In genere, [`GetItem(int)`](xref:Android.Widget.BaseAdapter.GetItem*)
deve restituire l'oggetto effettivo in corrispondenza della posizione specificata nell'adapter, ma viene ignorato per questo esempio. Allo stesso modo, [`GetItemId(int)`](xref:Android.Widget.BaseAdapter.GetItemId*)
deve restituire l'ID di riga dell'elemento, ma non è necessario.

Il primo metodo necessario è [`GetView()`](xref:Android.Widget.BaseAdapter.GetView*).
Questo metodo crea un nuovo [`View`](xref:Android.Views.View)
per ogni immagine aggiunta al `ImageAdapter`. Quando viene chiamato, un [`View`](xref:Android.Views.View)
viene passato, che in genere è un oggetto riciclato (almeno dopo che è stato chiamato una volta), quindi è presente un controllo per verificare se l'oggetto è null. Se *è* null, un [`ImageView`](xref:Android.Widget.ImageView)
viene creata un'istanza di e viene configurata con le proprietà desiderate per la presentazione dell'immagine:

- [`LayoutParams`](xref:Android.Views.View.LayoutParameters) imposta l'altezza e la larghezza della visualizzazione&mdash;in questo modo si garantisce che, a prescindere dalle dimensioni dell'oggetto disegnato, ogni immagine venga ridimensionata e ritagliata per adattarsi a queste dimensioni, a seconda delle esigenze.

- [`SetScaleType()`](xref:Android.Widget.ImageView.SetScaleType*) dichiara che le immagini devono essere ritagliate verso il centro (se necessario).

- [`SetPadding(int, int, int, int)`](xref:Android.Views.View.SetPadding*) definisce la spaziatura interna per tutti i lati. Si noti che, se le immagini hanno proporzioni diverse, il riempimento minore causerà un maggiore ritaglio dell'immagine se non corrisponde alle dimensioni specificate per ImageView.

Se il [`View`](xref:Android.Views.View) passato a [`GetView()`](xref:Android.Widget.BaseAdapter.GetView*) *non* è null, il [`ImageView`](xref:Android.Widget.ImageView) locale
viene inizializzato con l'oggetto [`View`](xref:Android.Views.View) riciclato.

Alla fine del [`GetView()`](xref:Android.Widget.BaseAdapter.GetView*)
, il `position` Integer passato nel metodo viene usato per selezionare un'immagine dalla matrice `thumbIds`, che viene impostata come risorsa immagine per il [`ImageView`](xref:Android.Widget.ImageView).

A questo proposito, è necessario definire la matrice di `thumbIds` di risorse di cui è possibile eseguire l'estrazione.

Eseguire l'applicazione. Il layout della griglia dovrebbe avere un aspetto simile al seguente:

[schermata di esempio![di GridView che visualizza 15 immagini](grid-view-images/helloviews4.png)](grid-view-images/helloviews4.png#lightbox)

Provare a sperimentare i comportamenti del [`GridView`](xref:Android.Widget.GridView) e [`ImageView`](xref:Android.Widget.ImageView)
modificando le proprietà degli elementi. Ad esempio, invece di usare [`LayoutParams`](xref:Android.Views.View.LayoutParameters) provare a usare [`SetAdjustViewBounds()`](xref:Android.Widget.ImageView.SetAdjustViewBounds*).

## <a name="references"></a>Riferimenti

- [`GridView`](xref:Android.Widget.GridView)
- [`ImageView`](xref:Android.Widget.ImageView)
- [`BaseAdapter`](xref:Android.Widget.BaseAdapter)

_Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base alle condizioni descritte nella [licenza Creative Commons 2,5 Attribution](https://creativecommons.org/licenses/by/2.5/)._
