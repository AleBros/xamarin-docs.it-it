---
title: Estensione dell'esempio RecyclerView
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 83147261a2d5458272f7e2bc105154da4308f4b0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="extending-the-recyclerview-example"></a>Estensione dell'esempio RecyclerView


L'applicazione di base descritte in [A base RecyclerView esempio](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) effettivamente non serve a molto &ndash; semplicemente scorre e visualizza un elenco fisso di elementi fotografia per facilitare l'esplorazione. Nelle applicazioni reali, gli utenti dovrebbero essere in grado di interagire con l'app, toccare gli elementi nella visualizzazione. Inoltre, l'origine dati sottostante può modificare (o essere modificato dall'app) e il contenuto dello schermo deve mantenere la coerenza con le modifiche. Nelle sezioni seguenti verrà illustrato come gestire eventi click di elemento e aggiornare `RecyclerView` quando le modifiche apportate all'origine dati sottostante.


### <a name="handling-item-click-events"></a>Gestione degli eventi Click di elemento

Quando un utente tocca un elemento di `RecyclerView`, viene generato un evento di elemento fare clic per notificare l'app per determinare quali elementi è stato interessati. Questo evento non viene generato da `RecyclerView` &ndash; invece la visualizzazione elemento (che viene eseguito il wrapping in contenitore Vista) rileva ritocchi e i report di questi elementi come eventi click.

Per illustrare in che modo gestire gli eventi click di elemento, i passaggi seguenti illustrano come la visualizzazione delle foto app di base viene modificato al report cui fotografia fosse stato modificato dall'utente. Quando si verifica un evento di elemento fare clic nell'app di esempio, la sequenza seguente viene eseguita:

1.  La fotografia `CardView` rileva l'evento click di elemento e notifica all'adapter.

2.  L'adapter inoltra l'evento (con informazioni sulla posizione di elementi) al gestore dell'evento click di elemento dell'attività.

3.  Gestore dell'evento click di elemento dell'attività risponde all'evento click di elemento.

Innanzitutto, un membro del gestore eventi denominato `ItemClick` viene aggiunto al `PhotoAlbumAdapter` definizione di classe:

```csharp
public event EventHandler<int> ItemClick;
```

Successivamente, aggiungere un metodo del gestore eventi clic elemento `MainActivity`.
Questo gestore brevemente visualizzato un avviso popup che indica quale elemento fotografia è stato toccato per:

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

Successivamente, una riga di codice è necessario registrare il `OnItemClick` gestore con `PhotoAlbumAdapter`. È un ottimo strumento per eseguire questa operazione immediatamente successiva `PhotoAlbumAdapter` viene creato (dell'attività principale `OnCreate` (metodo)):

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

`PhotoAlbumAdapter` verrà ora chiamato `OnItemClick` quando riceve un evento di elemento-fare clic su. Il passaggio successivo consiste nel creare un gestore dell'adapter che genera questo `ItemClick` evento. Il seguente metodo `OnClick`, viene aggiunto immediatamente dopo l'adapter `ItemCount` metodo:

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

Questo `OnClick` metodo è l'adapter *listener* per eventi clic elemento dalle viste di elemento. Prima che il listener può essere registrato con una vista di articolo (tramite visualizzazione detentore della visualizzazione elemento) il `PhotoViewHolder` costruttore deve essere modificato per accettare questo metodo come argomento aggiuntivo e registrare `OnClick` con la visualizzazione elemento `Click` evento.
Di seguito viene modificato `PhotoViewHolder` costruttore:

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

Il `itemView` parametro contiene un riferimento al `CardView` che è stato modificato dall'utente. Si noti che la classe di base titolare visualizzazione conosce la posizione dell'elemento di layout (`CardView`) che rappresenta (tramite il `LayoutPosition` proprietà), e questa posizione viene passata per l'adapter `OnClick` metodo quando si verifica un evento di elemento-fare clic su. L'adapter `OnCreateViewHolder` metodo viene modificato per passare l'adapter `OnClick` metodo al costruttore del titolare Vista:

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

Quando si compila e si esegue l'app di visualizzazione delle foto di esempio, se si tocca una foto nella visualizzazione causerà un avviso popup da visualizzare che segnala che fotografia è stato toccato per:

[![È possibile chiudere Toast di esempio che viene visualizzato quando una scheda di foto](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

Questo esempio viene illustrato solo uno degli approcci disponibili per l'implementazione di gestori eventi con `RecyclerView`. Un altro approccio che può essere utilizzato in questo caso è per inserire il titolare della visualizzazione eventi e disporre dell'adattatore di sottoscrivere questi eventi. Se l'app di foto di esempio fornito di una funzionalità di modifica di foto, eventi separati sarebbe necessari per il `ImageView` e `TextView` all'interno di ogni `CardView`: interessa la `TextView` avvia un `EditView` finestra di dialogo che consente all'utente di modifica la didascalia e tocco sul `ImageView` avvia uno strumento di ritocco foto che consente di ritagliare o ruotare la foto dell'utente. A seconda delle esigenze della propria applicazione, è necessario progettare l'approccio migliore per la gestione e risposta agli eventi di tocco.

Per illustrare come `RecyclerView` possono essere aggiornati quando le modifiche del set di dati, l'app di visualizzazione delle foto di esempio possono essere modificate per scegliere una foto nell'origine dati in modo casuale e scambia con la foto del primo. Innanzitutto, un **Pick casuale** pulsante viene aggiunto all'app di foto di esempio **Main. axml** layout:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/randPickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:text="Random Pick" />
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

Successivamente, viene aggiunto codice alla fine dell'attività principale `OnCreate` metodo per individuare il `Random Pick` nel layout del pulsante e collegare un gestore:

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        // Randomly swap a photo with the first photo:
        int idx = mPhotoAlbum.RandomSwap();
    }
};

```

Questo gestore chiama dell'album foto `RandomSwap` metodo quando il **Pick casuale** pulsante. Il `RandomSwap` metodo in modo casuale scambia una foto con la foto del primo nell'origine dati, quindi restituisce l'indice della foto scambiate in modo casuale. Quando si compila e si esegue l'app di esempio con questo codice, toccando il **Pick casuale** pulsante non produce una visualizzazione di modifica perché il `RecyclerView` non riconosce la modifica all'origine dati.

Per mantenere `RecyclerView` aggiornati dopo cambia, l'origine dati di **Pick casuale** fare clic su gestore deve essere modificato per chiamare l'adapter `NotifyItemChanged` metodo per ogni elemento della raccolta che è stata modificata (in questo caso, due elementi hanno modificato: la foto prima e la foto invertita). In questo modo `RecyclerView` per aggiornare la visualizzazione in modo che sia coerenza con il nuovo stato dell'origine dati:

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        int idx = mPhotoAlbum.RandomSwap();

        // First photo has changed:
        mAdapter.NotifyItemChanged(0);

        // Swapped photo has changed:
        mAdapter.NotifyItemChanged(idx);
    }
};

```

A questo punto, quando il **Pick casuale** pulsante verrà scelti, `RecyclerView` Aggiorna la visualizzazione per mostrare che una foto ulteriormente verso il basso nella raccolta è stata invertita con la prima foto nella raccolta:

[![Prima schermata prima di scambio, seconda schermata dopo lo scambio](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

Naturalmente, `NotifyDataSetChanged` sia stato chiamato anziché apportare le due chiamate a `NotifyItemChanged`, ma in caso contrario sarebbe così forzare `RecyclerView` per aggiornare l'intera raccolta anche se solo due elementi nella raccolta è stata modificata. La chiamata `NotifyItemChanged` è notevolmente più efficiente chiamata `NotifyDataSetChanged`.


## <a name="related-links"></a>Collegamenti correlati

- [RecyclerViewer (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Funzionalità e le parti RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Un esempio di base RecyclerView](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
