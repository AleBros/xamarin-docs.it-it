---
title: Un esempio di base RecyclerView
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: d5be838dcb5530ece76c3701d8fce10403622e8d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30770612"
---
# <a name="a-basic-recyclerview-example"></a>Un esempio di base RecyclerView


Per comprendere come `RecyclerView` funziona in una tipica applicazione, questo argomento vengono esaminate le [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) app di esempio, un esempio di codice semplice che utilizza `RecyclerView` per visualizzare un numero elevato di foto: 

[![Due schermate di un'app RecyclerView che utilizza CardViews per visualizzare le foto](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** Usa [CardView](~/android/user-interface/controls/card-view.md) per implementare ogni elemento fotografia di `RecyclerView` layout. Causa del `RecyclerView`di ottenere prestazioni migliori, questa app di esempio è in grado di scorrere rapidamente un'ampia raccolta di foto, in modo uniforme e senza ritardi notevoli.


### <a name="an-example-data-source"></a>Un'origine dati di esempio

In questa app di esempio, un'origine dati "album foto" (rappresentato dal `PhotoAlbum` classe) fornisce `RecyclerView` con il contenuto dell'elemento.
`PhotoAlbum` è una raccolta di foto con didascalie; Quando crearne un'istanza, si ottiene una raccolta predefinita di 32 foto:

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

Ogni istanza foto `PhotoAlbum` espone le proprietà che consentono di visualizzare il relativo ID risorsa immagine, `PhotoID`e la stringa della didascalia, `Caption`. La raccolta di foto è organizzata in modo che ogni foto è possibile accedere tramite un indicizzatore. Ad esempio, le righe di codice seguenti accedere l'ID risorsa immagine e la didascalia della foto nella raccolta di decimo:

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` fornisce inoltre un `RandomSwap` metodo che è possibile chiamare per scambiare la prima foto nella raccolta con una foto scelta casualmente in un' posizione nella raccolta:

```csharp
mPhotoAlbum.RandomSwap ();
```

Poiché i dettagli di implementazione di `PhotoAlbum` non sono rilevanti per informazioni sui `RecyclerView`, `PhotoAlbum` codice sorgente non è presentato in questo caso. Il codice sorgente da `PhotoAlbum` è disponibile all'indirizzo [PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs) nel [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) app di esempio.


### <a name="layout-and-initialization"></a>Layout e inizializzazione

Il file di layout, **axml**, costituito da un singolo `RecyclerView` all'interno di un `LinearLayout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

Si noti che è necessario utilizzare il nome completo **android.support.v7.widget.RecyclerView** perché `RecyclerView` viene compresso in una libreria di supporto. Il `OnCreate` metodo `MainActivity` Inizializza il layout, viene creata la scheda e preparare l'origine dati sottostante:

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Prepare the data source:
        mPhotoAlbum = new PhotoAlbum ();

        // Instantiate the adapter and pass in its data source:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);

        // Set our view from the "main" layout resource:
        SetContentView (Resource.Layout.Main);

        // Get our RecyclerView layout:
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug the adapter into the RecyclerView:
        mRecyclerView.SetAdapter (mAdapter);
```

Questo codice esegue le operazioni seguenti:

1. Crea un'istanza di `PhotoAlbum` origine dati.

2. Viene passato al costruttore dell'adattatore di origine dati album foto `PhotoAlbumAdapter` (che è definita più avanti in questa Guida). 
   Si noti che è considerato una procedura consigliata per passare l'origine dati come parametro al costruttore dell'adattatore. 

3. Ottiene il `RecyclerView` dal layout.

4. Inserisce l'adapter nel `RecyclerView` istanza chiamando il `RecyclerView` `SetAdapter` metodo come illustrato in precedenza.

### <a name="layout-manager"></a>Gestore di layout

Ogni elemento nel `RecyclerView` è costituito da un `CardView` che contiene un'immagine di foto e una didascalia foto (dettagli sono disponibili nel [visualizzazione titolare](#view-holder) sezione riportata di seguito). Predefiniti `LinearLayoutManager` viene utilizzato per visualizzare ogni `CardView` in una disposizione di scorrimento verticale:

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

Questo codice si trova l'attività principale `OnCreate` metodo. Il costruttore per la gestione del layout richiede un *contesto*, pertanto il `MainActivity` passati utilizzando `this` come nell'esempio precedente.

Anziché utilizzare il di predefind `LinearLayoutManager`, è possibile collegare un gestore di layout personalizzato che consente di visualizzare due `CardView` side-by-side, implementazione di un effetto animazione page-turning per scorrere la raccolta di foto di elementi. Più avanti in questa Guida, si verrà visualizzato un esempio di come modificare il layout scambiando nella console di gestione layout diverso.

<a name="view-holder" />

### <a name="view-holder"></a>Visualizzazione contenitore

Classe titolare di visualizzazione viene chiamata `PhotoViewHolder`. Ogni `PhotoViewHolder` istanza contiene riferimenti al `ImageView` e `TextView` di un elemento di riga associata, disposti in un `CardView` come tracciato qui:

[![Diagramma di CardView contenente un'ImageView e TextView](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` deriva da `RecyclerView.ViewHolder` e contiene le proprietà per archiviare i riferimenti al `ImageView` e `TextView` visualizzato nel layout precedente.
`PhotoViewHolder` è costituito da due proprietà e un costruttore:

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```
In questo esempio di codice, il `PhotoViewHolder` costruttore viene passato un riferimento alla visualizzazione elemento padre (il `CardView`) che `PhotoViewHolder` esegue il wrapping. Si noti che è sempre di inoltrare l'elemento padre visualizzazione elemento al costruttore di base. Il `PhotoViewHolder` chiamate al costruttore `FindViewById` nella visualizzazione elemento padre per individuare i relativi riferimenti, visualizzazione figlio `ImageView` e `TextView`, archiviare i risultati nel `Image` e `Caption` proprietà, rispettivamente. La scheda recupera successivamente visualizzare i riferimenti da queste proprietà quando viene aggiornata di questo `CardView`di visualizzazioni figlio con i nuovi dati.

Per ulteriori informazioni su `RecyclerView.ViewHolder`, vedere il [riferimento alla classe RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="adapter"></a>Adattatore

L'adapter carica ogni `RecyclerView` riga contenente dati per una fotografia particolare. Per una fotografia specificata nella posizione di riga *P*, ad esempio, l'adapter consente di individuare i dati associati nella posizione *P* all'interno dell'origine dati e le copie di questi dati alla riga di elemento nella posizione *P* nel `RecyclerView` insieme. L'adapter utilizza il titolare della vista per cercare i riferimenti per il `ImageView` e `TextView` in tale posizione, in modo da non dover chiamare ripetutamente `FindViewById` per le viste quando l'utente scorre la raccolta foto e riutilizza viste.

In **RecyclerViewer**, derivata dalla classe di adattatori `RecyclerView.Adapter` creare `PhotoAlbumAdapter`:

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;

    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }
    ...
}
```

Il `mPhotoAlbum` membro contiene l'origine dati (album foto) che viene passato al costruttore, il costruttore di copia album foto in tale variabile membro. Le operazioni seguenti necessarie `RecyclerView.Adapter` vengono implementati i metodi:

-   **`OnCreateViewHolder`** &ndash; Crea un'istanza del titolare file e Visualizza elemento di layout.

-   **`OnBindViewHolder`** &ndash; Carica i dati nella posizione specificata nelle viste a cui i riferimenti vengono archiviati nel titolare della visualizzazione specificata.

-   **`ItemCount`** &ndash; Restituisce il numero di elementi nell'origine dati.

Il gestore di layout chiama questi metodi mentre è posizionamento di elementi all'interno di `RecyclerView`. L'implementazione di questi metodi viene esaminato nelle sezioni seguenti.


#### <a name="oncreateviewholder"></a>OnCreateViewHolder

Le chiamate del gestore layout `OnCreateViewHolder` quando il `RecyclerView` necessita di un nuovo titolare di visualizzazione per rappresentare un elemento. `OnCreateViewHolder` Ingrandisce la visualizzazione elemento dal file di layout della vista ed esegue il wrapping in una nuova visualizzazione `PhotoViewHolder` istanza. Il `PhotoViewHolder` costruttore individua e archivia i riferimenti a visualizzazioni figlio nel layout, come descritto in precedenza in [titolare vista](#view-holder).

Ogni elemento di riga è rappresentato da un `CardView` che contiene un `ImageView` (per la foto) e un `TextView` (per la didascalia). Questo layout risiede nel file **PhotoCardView.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:card_view="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <android.support.v7.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        card_view:cardElevation="4dp"
        card_view:cardUseCompatPadding="true"
        card_view:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Caption"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="4dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</FrameLayout>
```

Questo layout rappresenta un elemento di singola riga nel `RecyclerView`. Il `OnBindViewHolder` (descritta di seguito) metodo copia i dati dall'origine dati nel `ImageView` e `TextView` di questo layout.
`OnCreateViewHolder` Ingrandisce questo layout di un percorso determinato foto nel `RecyclerView` e crea un nuovo `PhotoViewHolder` istanza (che individua e memorizza nella cache i riferimenti al `ImageView` e `TextView` visualizzazioni figlio associato `CardView` layout):

```csharp
public override RecyclerView.ViewHolder
    OnCreateViewHolder (ViewGroup parent, int viewType)
{
    // Inflate the CardView for the photo:
    View itemView = LayoutInflater.From (parent.Context).
                Inflate (Resource.Layout.PhotoCardView, parent, false);

    // Create a ViewHolder to hold view references inside the CardView:
    PhotoViewHolder vh = new PhotoViewHolder (itemView);
    return vh;
}

```

L'istanza di titolare visualizzazione risultante, `vh`, viene restituito al chiamante (il gestore di layout).


#### <a name="onbindviewholder"></a>OnBindViewHolder

Quando il gestore di layout è pronto per la visualizzazione in particolare il `RecyclerView`dell'area visibile dello schermo, chiama l'adapter `OnBindViewHolder` metodo da riempire l'elemento in corrispondenza della posizione di riga specificata con il contenuto dall'origine dati. `OnBindViewHolder` Ottiene le informazioni di foto per la posizione della riga specificata (risorsa immagine della foto e la stringa per la didascalia della foto) e copia i dati in viste associate. Viste si trovano tramite i riferimenti archiviati in oggetto visualizzazione contenitore (che viene passato tramite la `holder` parametro):

```csharp
public override void
    OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
{
    PhotoViewHolder vh = holder as PhotoViewHolder;

    // Load the photo image resource from the photo album:
    vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);

    // Load the photo caption from the photo album:
    vh.Caption.Text = mPhotoAlbum[position].Caption;
}
```

Oggetto contenitore passato nella vista debba prima eseguirne il cast nel tipo di contenitore vista derivata (in questo caso, `PhotoViewHolder`) prima di utilizzarlo.
L'adapter carica la risorsa immagine nella vista a cui fa riferimento il titolare di visualizzazione `Image` proprietà e copia il testo del titolo nella vista a cui fa riferimento il titolare di visualizzazione `Caption` proprietà. Questo *associa* la visualizzazione associata con i relativi dati.

Si noti che `OnBindViewHolder` è il codice che gestisce direttamente con la struttura dei dati. In questo caso, `OnBindViewHolder` siano in grado di eseguire il mapping di `RecyclerView` elemento posizione per l'elemento di dati associata nell'origine dati. Il mapping è semplice in questo caso perché la posizione può essere usata come un indice di matrice in album foto; Tuttavia, le origini dati più complesse richiedono codice aggiuntivo per stabilire questo mapping.


#### <a name="itemcount"></a>ItemCount

Il `ItemCount` metodo restituisce il numero di elementi nella raccolta dati. Nell'applicazione di Visualizzatore foto di esempio, il numero di elementi è il numero di foto contenute nell'album foto:

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

Per ulteriori informazioni su `RecyclerView.Adapter`, vedere il [riferimento alla classe RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).


### <a name="putting-it-all-together"></a>Uso combinato tutti

Il valore risultante `RecyclerView` implementazione per l'app di foto di esempio è costituito `MainActivity` codice che crea l'origine dati, gestione di layout e l'adapter. `MainActivity` Crea il `mRecyclerView` istanza, viene creata l'origine dati e l'adapter e inserisce la scheda e il gestore di layout:

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        mPhotoAlbum = new PhotoAlbum();
        SetContentView (Resource.Layout.Main);
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug in the linear layout manager:
        mLayoutManager = new LinearLayoutManager (this);
        mRecyclerView.SetLayoutManager (mLayoutManager);

        // Plug in my adapter:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
        mRecyclerView.SetAdapter (mAdapter);
    }
}

```

`PhotoViewHolder` Individua e memorizza nella cache i riferimenti di visualizzazione:

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

`PhotoAlbumAdapter` implementa l'override dei metodi necessari tre:

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;
    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }

    public override RecyclerView.ViewHolder
        OnCreateViewHolder (ViewGroup parent, int viewType)
    {
        View itemView = LayoutInflater.From (parent.Context).
                    Inflate (Resource.Layout.PhotoCardView, parent, false);
        PhotoViewHolder vh = new PhotoViewHolder (itemView);
        return vh;
    }

    public override void
        OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
    {
        PhotoViewHolder vh = holder as PhotoViewHolder;
        vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);
        vh.Caption.Text = mPhotoAlbum[position].Caption;
    }

    public override int ItemCount
    {
        get { return mPhotoAlbum.NumPhotos; }
    }
}
```

Quando questo codice viene compilato ed eseguito, viene creata la foto di base Visualizza app, come illustrato nelle schermate seguenti:

[![Due schermate dell'app con schede di foto di scorrimento di visualizzazione delle foto](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

Questa applicazione di base supporta solo la visualizzazione dell'album foto. Non risponde per l'elemento-eventi tocco, né gestisce le modifiche nei dati sottostanti. Questa funzionalità viene aggiunta in [estendendo l'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md).


### <a name="changing-the-layoutmanager"></a>Modifica il LayoutManager

Causa del `RecyclerView`della flessibilità, è facile modificare l'applicazione per utilizzare un gestore di layout diversi. Nell'esempio seguente viene modificata in modo per visualizzare album foto con un layout di griglia che scorre in senso orizzontale anziché con un layout verticale lineare. A tale scopo, la creazione di istanze di gestione di layout viene modificato per utilizzare il `GridLayoutManager` come indicato di seguito:

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

Questa modifica del codice sostituisce verticale `LinearLayoutManager` con un `GridLayoutManager` che presenta una griglia composta da due righe di scorrimento nella direzione orizzontale. Quando si compila e si esegue nuovamente l'app, si noterà che le fotografie vengono visualizzate in una griglia e che lo scorrimento orizzontale anziché verticale:

[![Schermata di esempio di app con lo scorrimento orizzontale foto in una griglia](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

Se si modifica solo una riga di codice, è possibile modificare l'applicazione di visualizzazione delle foto per utilizzare un layout diverso con un comportamento diverso.
Si noti che il codice dell'adattatore né il layout XML doveva essere modificato per modificare lo stile di layout. 

Nell'argomento successivo, [estendendo l'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md), questa app di esempio di base viene esteso per gestire eventi click di elemento e aggiornare `RecyclerView` quando le modifiche apportate all'origine dati sottostante.



## <a name="related-links"></a>Collegamenti correlati

- [RecyclerViewer (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Funzionalità e le parti RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Estensione dell'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
