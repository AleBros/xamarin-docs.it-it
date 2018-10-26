---
title: Estendendo l'esempio di RecyclerView
description: Aggiunta di gestori di eventi clic elemento per l'app di esempio di RecyclerView.
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: eca0f58a470228ce8e6331defe88c1ef727cef57
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105181"
---
# <a name="extending-the-recyclerview-example"></a>Estendendo l'esempio di RecyclerView


L'app di base descritto in [un esempio di RecyclerView base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) effettivamente non serve a molto &ndash; semplicemente scorrere e visualizza un elenco fisso di elementi fotografia per facilitare l'esplorazione. Nelle applicazioni reali, gli utenti si aspettano di essere in grado di interagire con l'app toccando gli elementi nella visualizzazione. Inoltre, l'origine dati sottostante può modificare (o essere modificata dall'app) e il contenuto dello schermo deve mantenere la coerenza con queste modifiche. Nelle sezioni seguenti, si apprenderà come gestire gli eventi click di elemento e aggiornare `RecyclerView` quando le modifiche apportate all'origine dati sottostante.


### <a name="handling-item-click-events"></a>Gestione degli eventi Click di elemento

Quando un utente tocca un elemento di `RecyclerView`, viene generato un evento di elemento-fare clic per ricevere una notifica all'app quali elementi è stato aggiornato. Questo evento non viene generato dal `RecyclerView` &ndash; invece la visualizzazione elemento (che viene eseguito il wrapping nel titolare della vista) rileva i tocchi e segnala i tocchi come gli eventi click.

Per illustrare come gestire gli eventi elemento clic, i passaggi seguenti illustrano come le app di visualizzazione delle foto base viene modificato al report, quali fotografie erano stata toccata dall'utente. Quando si verifica un evento di selezione elemento nell'app di esempio, la sequenza seguente viene eseguita:

1.  La fotografia `CardView` rileva l'evento click di elemento e notifica all'adapter.

2.  L'adapter inoltra l'evento (con le informazioni sulla posizione elementi) al gestore di clic elemento dell'attività.

3.  Gestore dei clic elemento dell'attività risponde all'evento click di elemento.

In primo luogo, un membro del gestore dell'evento denominato `ItemClick` viene aggiunto al `PhotoAlbumAdapter` definizione della classe:

```csharp
public event EventHandler<int> ItemClick;
```

Successivamente, un metodo del gestore eventi clic elemento viene aggiunto a `MainActivity`.
Questo gestore viene visualizzato brevemente un avviso popup che indica quale elemento fotografia è stata aggiornata:

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

Successivamente, una riga di codice è necessaria per registrare il `OnItemClick` gestore con `PhotoAlbumAdapter`. Un buon punto di eseguire questa operazione si trova subito dopo `PhotoAlbumAdapter` creato: 

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

In questo esempio di base, registrazione del gestore viene eseguita l'attività principale `OnCreate` metodo, ma un'app di produzione potrebbe registrare il gestore nel `OnResume` e annullare la registrazione nella `OnPause` &ndash; vedere [ciclo di vita di attività ](~/android/app-fundamentals/activity-lifecycle/index.md) per altre informazioni.

`PhotoAlbumAdapter` a questo punto verrà chiamato `OnItemClick` quando riceve un evento di elemento-fare clic su. Il passaggio successivo consiste nel creare un gestore dell'adapter che genera questo `ItemClick` evento. Il metodo seguente, `OnClick`, verrà aggiunto immediatamente dopo l'adapter `ItemCount` metodo:

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

Ciò `OnClick` metodo è l'adapter *listener* per eventi clic elemento dalle viste di elemento. Prima che il listener può essere registrato con una vista di articolo (tramite il titolare della visualizzazione della visualizzazione elemento), il `PhotoViewHolder` costruttore deve essere modificato per accettare questo metodo come argomento aggiuntivo e registrare `OnClick` con la visualizzazione elemento `Click` evento.
Di seguito è modificato `PhotoViewHolder` costruttore:

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

Il `itemView` parametro contiene un riferimento di `CardView` che è stata aggiornata dall'utente. Si noti che la classe di base del titolare della vista conosca la posizione di layout dell'elemento (`CardView`) che rappresenta (tramite il `LayoutPosition` proprietà), e questa posizione viene passata all'adapter `OnClick` metodo quando si verifica un evento di elemento-fare clic su. L'adapter `OnCreateViewHolder` metodo viene modificato per passare l'adapter `OnClick` metodo al costruttore del titolare Vista:

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

Quando si creerà ed eseguirà l'app di visualizzazione delle foto di esempio, toccando una foto nella visualizzazione causerà un avviso popup da visualizzare che segnala che fotografia è stata aggiornata:

[![Tipo avviso popup di esempio che viene visualizzato quando una scheda di foto toccando](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

Questo esempio illustra solo uno degli approcci per l'implementazione di gestori di eventi con `RecyclerView`. Un altro approccio che può essere usato in questo caso consiste nel posizionare gli eventi sul server che detiene visualizzazione e dispongono della scheda di sottoscrivere questi eventi. Se l'app di foto di esempio fornito una foto di funzionalità di modifica, gli eventi separati sono necessari per il `ImageView` e il `TextView` all'interno di ogni `CardView`: fornisce degli accenni al `TextView` avvia un `EditView` finestra di dialogo che consente all'utente di modifica la didascalia e riguarda il `ImageView` uno strumento di ritocco foto che consente all'utente di ritaglio o ruotare l'immagine di avvio. A seconda delle esigenze della tua app, è necessario progettare l'approccio migliore per la gestione e risposta per eventi di tocco.

Per illustrare come `RecyclerView` possono essere aggiornati quando le modifiche di set di dati, l'app di visualizzazione delle foto di esempio possono essere modificate in modo casuale scegliere una foto nell'origine dati e scambiarlo con la foto del primo. Prima di tutto una **Scegli casuale** pulsante viene aggiunto all'app di esempio foto **Main. axml** layout:

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

Successivamente, viene aggiunto codice alla fine dell'attività principale `OnCreate` metodo per individuare il `Random Pick` nel layout del pulsante e associare un gestore a esso:

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

Questo gestore chiama dell'album di foto `RandomSwap` metodo quando il **casuale selezionare** toccando pulsante. Il `RandomSwap` metodo in modo casuale scambia una foto con la foto del primo nell'origine dati, quindi restituisce l'indice della foto scambiate in modo casuale. Quando compilare ed eseguire l'app di esempio con il seguente codice, toccando il **Scegli casuale** non comporta una modifica della visualizzazione pulsante perché il `RecyclerView` non rileva la modifica all'origine dati.

Per mantenere `RecyclerView` aggiornati dopo che i dati di origine viene modificata, il **seleziona casuale** fare clic su gestore deve essere modificato per chiamare l'adattatore `NotifyItemChanged` metodo per ogni elemento della raccolta che è stato modificato (in questo caso, due elementi hanno modificato: la foto del primo e la foto invertita). In questo modo, `RecyclerView` per aggiornare la relativa visualizzazione in modo che sia coerenza con il nuovo stato dell'origine dati:

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

A questo punto, quando il **Scegli casuale** toccando pulsante, `RecyclerView` Aggiorna la visualizzazione per mostrare che una foto ulteriormente verso il basso nella raccolta è stata scambiata con il primo foto nella raccolta:

[![Primo screenshot prima dello scambio, seconda schermata dopo lo scambio](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

Ovviamente `NotifyDataSetChanged` potrebbe essere chiamata anziché apportare le due chiamate a `NotifyItemChanged`, ma tale operazione sarebbe quindi forzare `RecyclerView` per aggiornare l'intera raccolta anche se erano stato modificato solo due elementi nella raccolta. La chiamata `NotifyItemChanged` è molto più efficiente rispetto a chiamare il metodo `NotifyDataSetChanged`.


## <a name="related-links"></a>Collegamenti correlati

- [RecyclerViewer (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Funzionalità e le parti di RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Un esempio di RecyclerView base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
