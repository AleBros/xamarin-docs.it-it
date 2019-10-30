---
title: Estensione dell'esempio RecyclerView
description: Aggiunta di gestori di eventi Click di elemento all'app di esempio RecyclerView.
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: d50971c1ef0064463e576a895729ad84577e1788
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028873"
---
# <a name="extending-the-recyclerview-example"></a>Estensione dell'esempio RecyclerView

L'app di base descritta in [un esempio di RecyclerView di base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md) in realtà non esegue molto &ndash; scorre e visualizza un elenco fisso di elementi della foto per facilitare l'esplorazione. Nelle applicazioni reali, gli utenti si aspettano di poter interagire con l'app toccando gli elementi visualizzati. Inoltre, l'origine dati sottostante può cambiare (o essere modificata dall'app) e il contenuto della visualizzazione deve rimanere coerente con queste modifiche. Nelle sezioni seguenti verranno illustrate le procedure per gestire gli eventi di clic sugli elementi e aggiornare `RecyclerView` quando viene modificata l'origine dati sottostante.

### <a name="handling-item-click-events"></a>Gestione degli eventi Click di elemento

Quando un utente tocca un elemento nella `RecyclerView`, viene generato un evento di clic dell'elemento per notificare all'app quale elemento è stato toccato. Questo evento non viene generato da `RecyclerView` &ndash; al suo posto, la visualizzazione dell'elemento (che viene incapsulata nel titolare della visualizzazione) rileva i ritocchi e segnala questi tocchi come eventi click.

Per illustrare come gestire gli eventi di clic sugli elementi, i passaggi seguenti illustrano come viene modificata l'app di visualizzazione foto di base per segnalare quale fotografia è stata toccata dall'utente. Quando si verifica un evento di clic sull'elemento nell'app di esempio, viene eseguita la sequenza seguente:

1. Il `CardView` della foto rileva l'evento di clic sull'elemento e invia una notifica all'adapter.

2. L'adapter invia l'evento (con le informazioni sulla posizione dell'elemento) al gestore di clic dell'elemento dell'attività.

3. Il gestore di clic dell'elemento dell'attività risponde all'evento clic sull'elemento.

Viene innanzitutto aggiunto un membro del gestore eventi denominato `ItemClick` alla definizione della classe `PhotoAlbumAdapter`:

```csharp
public event EventHandler<int> ItemClick;
```

Viene quindi aggiunto un metodo del gestore dell'evento click-through a `MainActivity`.
Questo gestore visualizza brevemente un avviso popup che indica quale elemento della fotografia è stato toccato:

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

Quindi, è necessaria una riga di codice per registrare il gestore di `OnItemClick` con `PhotoAlbumAdapter`. Una posizione ideale per eseguire questa operazione è immediatamente dopo la creazione di `PhotoAlbumAdapter`: 

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

In questo esempio di base, la registrazione del gestore si verifica nel metodo `OnCreate` dell'attività principale, ma un'app di produzione potrebbe registrare il gestore in `OnResume` e annullarne la registrazione in `OnPause` &ndash; vedere ciclo di vita delle [attività](~/android/app-fundamentals/activity-lifecycle/index.md) per altre informazioni.

`PhotoAlbumAdapter` chiamerà ora `OnItemClick` quando riceve un evento di clic dell'elemento. Il passaggio successivo consiste nel creare un gestore nell'adapter che generi questo evento `ItemClick`. Il metodo seguente, `OnClick`, viene aggiunto immediatamente dopo il metodo di `ItemCount` dell'adapter:

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

Questo `OnClick` metodo è il *listener* dell'adapter per gli eventi di clic dell'elemento dalle visualizzazioni elemento. Prima che questo listener possa essere registrato con una visualizzazione elemento (tramite il titolare della visualizzazione della visualizzazione elemento), il costruttore di `PhotoViewHolder` deve essere modificato in modo da accettare questo metodo come argomento aggiuntivo e registrare `OnClick` con la visualizzazione elementi `Click` evento.
Ecco il costruttore `PhotoViewHolder` modificato:

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

Il parametro `itemView` contiene un riferimento al `CardView` che è stato toccato dall'utente. Si noti che la classe di base del supporto per la visualizzazione conosce la posizione di layout dell'elemento (`CardView`) che rappresenta (tramite la proprietà `LayoutPosition`) e questa posizione viene passata al metodo `OnClick` dell'adapter quando si verifica un evento di clic sull'elemento. Il metodo di `OnCreateViewHolder` dell'adapter viene modificato per passare il metodo di `OnClick` dell'adapter al costruttore del supporto di visualizzazione:

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

A questo punto, quando si compila ed esegue l'app di visualizzazione foto di esempio, toccando una foto nella visualizzazione verrà visualizzato un avviso popup che segnala la foto che è stata toccata:

[![di esempio popup visualizzato quando viene toccata una scheda foto](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

Questo esempio illustra solo un approccio per l'implementazione di gestori eventi con `RecyclerView`. Un altro approccio che può essere usato in questo caso è inserire gli eventi sul contenitore di visualizzazione e fare in modo che l'adapter sottoscriva questi eventi. Se l'app Photo di esempio ha fornito una funzionalità di modifica della foto, sarebbero necessari eventi distinti per la `ImageView` e il `TextView` all'interno di ogni `CardView`: i ritocchi sulla `TextView` avrebbero avviato una finestra di dialogo di `EditView` che consente all'utente di modificare la didascalia e tocca il `ImageView` avvia uno strumento di ritocco foto che consente all'utente di ritagliare o ruotare la foto. A seconda delle esigenze dell'applicazione, è necessario progettare l'approccio migliore per la gestione e la risposta agli eventi di tocco.

Per dimostrare in che modo `RecyclerView` possibile aggiornare quando il set di dati viene modificato, è possibile modificare l'app Photo-View di esempio per selezionare in modo casuale una foto nell'origine dati e scambiarla con la prima foto. Per prima cosa, viene aggiunto un pulsante di **selezione casuale** al layout **Main. aXML** dell'app Photo di esempio:

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

Successivamente, il codice viene aggiunto alla fine del metodo di `OnCreate` dell'attività principale per individuare il pulsante `Random Pick` nel layout e alleghirvi un gestore:

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

Questo gestore chiama il metodo di `RandomSwap` dell'album foto quando viene toccato il pulsante di **selezione casuale** . Il metodo `RandomSwap` scambia in modo casuale una foto con la prima foto nell'origine dati, quindi restituisce l'indice della foto scambiata in modo casuale. Quando si compila ed esegue l'app di esempio con questo codice, toccare il pulsante di **selezione casuale** non comporta una modifica della visualizzazione perché il `RecyclerView` non è a conoscenza della modifica apportata all'origine dati.

Per tenere `RecyclerView` aggiornati dopo la modifica dell'origine dati, è necessario modificare il gestore di **selezione casuale** per chiamare il metodo di `NotifyItemChanged` dell'adapter per ogni elemento della raccolta che è stato modificato (in questo caso, sono stati modificati due elementi: la prima foto e Foto invertita). Questo fa sì che `RecyclerView` aggiorni la visualizzazione in modo che sia coerente con il nuovo stato dell'origine dati:

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

A questo punto, quando viene toccato il pulsante di **selezione casuale** , `RecyclerView` aggiorna la visualizzazione per indicare che una foto più in basso nella raccolta è stata scambiata con la prima foto della raccolta:

[![prima screenshot prima dello scambio, seconda schermata dopo lo scambio](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

Naturalmente, è possibile che sia stato chiamato `NotifyDataSetChanged` anziché eseguire le due chiamate al `NotifyItemChanged`, ma in questo modo si impone `RecyclerView` di aggiornare l'intera raccolta anche se sono stati modificati solo due elementi della raccolta. La chiamata di `NotifyItemChanged` è significativamente più efficiente rispetto alla chiamata di `NotifyDataSetChanged`.

## <a name="related-links"></a>Collegamenti correlati

- [RecyclerViewer (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Parti e funzionalità di RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Esempio di RecyclerView di base](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
