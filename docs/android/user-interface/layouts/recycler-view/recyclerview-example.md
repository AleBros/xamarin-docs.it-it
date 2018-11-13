---
title: Un esempio di RecyclerView base
description: Un'app di esempio che illustra come usare RecyclerView.
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/30/2018
ms.openlocfilehash: d71c4f0f3221d06c22876329a5933273d8d6f92d
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526767"
---
# <a name="a-basic-recyclerview-example"></a>Un esempio di RecyclerView base

Per comprendere come `RecyclerView` funziona in una tipica applicazione, questo argomento esamina i [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) app di esempio, un esempio di codice semplice che usa `RecyclerView` per visualizzare un'ampia raccolta di foto: 

[![Due schermate di un'app di RecyclerView che usa CardViews per visualizzare le foto](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** utilizza [widget CardView](~/android/user-interface/controls/card-view.md) implementare ogni elemento fotografia di `RecyclerView` layout. Causa del `RecyclerView`di vantaggi di prestazioni, questa app di esempio è in grado di scorrere rapidamente un'ampia raccolta di foto, in modo uniforme e senza ritardi notevoli.


### <a name="an-example-data-source"></a>Un'origine dati di esempio

In questa app di esempio, un'origine dati "album di foto" (rappresentato dal `PhotoAlbum` classe) fornisce `RecyclerView` con contenuto dell'elemento.
`PhotoAlbum` è una raccolta di foto con sottotitoli in lingua originale; Quando si crea un'istanza, si ottiene una raccolta pronte all'uso di 32 foto:

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

Ogni istanza di foto nel `PhotoAlbum` espone le proprietà che consentono di visualizzare il relativo ID di risorsa, immagine `PhotoID`e la stringa della didascalia `Caption`. La raccolta di foto è organizzata in modo che ogni foto sono accessibili da un indicizzatore. Le seguenti righe di codice di accedono, ad esempio, l'ID risorsa dell'immagine e la didascalia per il decima foto nella raccolta:

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` fornisce inoltre un `RandomSwap` metodo che è possibile chiamare per scambiare il primo foto nella raccolta con una foto scelto casualmente in un' posizione nella raccolta:

```csharp
mPhotoAlbum.RandomSwap ();
```

Poiché i dettagli di implementazione `PhotoAlbum` non sono rilevanti per comprendere `RecyclerView`, il `PhotoAlbum` codice sorgente non viene presentato qui. Il codice sorgente `PhotoAlbum` è disponibile all'indirizzo [PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs) nel [RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/) app di esempio.


### <a name="layout-and-initialization"></a>Layout e inizializzazione

Il file di layout **Main. axml**, è costituito da una singola `RecyclerView` all'interno di un `LinearLayout`:

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

Si noti che è necessario utilizzare il nome completo **android.support.v7.widget.RecyclerView** perché `RecyclerView` viene compresso in una libreria di supporto. Il `OnCreate` metodo `MainActivity` Inizializza questo layout il nome, crea un'istanza di adattatore e prepara l'origine dati sottostante:

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

1. Crea un'istanza di `PhotoAlbum` zdroj dat.

2. Viene passato al costruttore dell'adapter, l'origine di dati di album di foto `PhotoAlbumAdapter` (che è definito più avanti in questa Guida). 
   Si noti che viene considerato una procedura consigliata per passare l'origine dati come parametro al costruttore dell'adattatore. 

3. Ottiene il `RecyclerView` dal layout.

4. Si collega l'adapter nel `RecyclerView` istanza chiamando il `RecyclerView` `SetAdapter` metodo come illustrato in precedenza.

### <a name="layout-manager"></a>Gestore di layout

Ogni elemento il `RecyclerView` è costituito da un `CardView` che contiene un'immagine di foto e la didascalia di foto (dettagli sono descritti nel [titolare della visualizzazione](#view-holder) sezione riportata di seguito). Oggetto predefinito `LinearLayoutManager` viene utilizzata per definire il layout ognuno `CardView` in una disposizione di scorrimento verticale:

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

Questo codice si trova nell'attività principale `OnCreate` (metodo). Il costruttore per la gestione di layout richiede un *contesto*, pertanto il `MainActivity` viene passato usando `this` come nell'esempio precedente.

Invece di usare l'oggetto predefinito `LinearLayoutManager`, è possibile collegare un gestore di layout personalizzato che consente di visualizzare due `CardView` side-by-side, che implementa un effetto di animazione page-turning attraversare la raccolta di foto di elementi. Più avanti in questa Guida, si noterà un esempio di come modificare il layout mediante lo scambio in un gestore di layout diversi.

<a name="view-holder" />

### <a name="view-holder"></a>Visualizzazione contenitore

La classe di contenitore di visualizzazione viene chiamata `PhotoViewHolder`. Ciascuna `PhotoViewHolder` istanza contiene riferimenti al `ImageView` e `TextView` di un elemento di riga corrispondenti, che è disposto in una `CardView` come illustrato di seguito:

[![Diagramma del widget CardView contenente un ImageView e TextView](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` deriva da `RecyclerView.ViewHolder` e contiene le proprietà per archiviare i riferimenti per il `ImageView` e `TextView` visualizzato nel layout precedente.
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
In questo esempio di codice, il `PhotoViewHolder` costruttore viene passato un riferimento alla visualizzazione elemento padre (il `CardView`) che `PhotoViewHolder` esegue il wrapping. Si noti che si invia sempre l'elemento padre visualizzazione elemento al costruttore di base. Il `PhotoViewHolder` chiamate al costruttore `FindViewById` nella visualizzazione elemento padre per individuare ciascuno dei relativi riferimenti, visualizzazione figlio `ImageView` e `TextView`, archiviando i risultati nel `Image` e `Caption` proprietà, rispettivamente. L'adapter in un secondo momento recupera visualizzare i riferimenti da queste proprietà quando si aggiorna `CardView`di visualizzazioni figlio con i nuovi dati.

Per altre informazioni sulle `RecyclerView.ViewHolder`, vedere la [riferimento alla classe RecyclerView.ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).


### <a name="adapter"></a>Adattatore

L'adapter carica ogni `RecyclerView` riga con i dati per una specifica fotografia. Per una fotografia specificata nella posizione di riga *P*, ad esempio, l'adapter consente di individuare i dati associati nella posizione *P* all'interno dell'origine dati e le copie di questi dati per la riga di elemento nella posizione *P* nella `RecyclerView` raccolta. L'adapter utilizza il contenitore di visualizzazione per cercare i riferimenti per il `ImageView` e `TextView` in tale posizione, in modo non è necessario chiamare ripetutamente `FindViewById` per quelle visualizzazioni appena l'utente scorre la raccolta di foto e riutilizza le visualizzazioni.

Nelle **RecyclerViewer**, una classe di adattatori è derivata da `RecyclerView.Adapter` creare `PhotoAlbumAdapter`:

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

Il `mPhotoAlbum` membro contiene l'origine dati (album di foto) che viene passato al costruttore, il costruttore di copia dell'album di foto in questa variabile membro. I seguenti necessari `RecyclerView.Adapter` vengono implementati i metodi:

-   **`OnCreateViewHolder`** &ndash; Crea un'istanza titolare di file e Visualizza layout dell'elemento.

-   **`OnBindViewHolder`** &ndash; Carica i dati nella posizione specificata nelle viste a cui i riferimenti vengono archiviati nel contenitore di visualizzazione specificata.

-   **`ItemCount`** &ndash; Restituisce il numero di elementi nell'origine dati.

Gestore del layout chiama questi metodi anche se è posizionamento di elementi all'interno di `RecyclerView`. L'implementazione di questi metodi viene esaminato nelle sezioni seguenti.


#### <a name="oncreateviewholder"></a>OnCreateViewHolder

Le chiamate di gestione di layout `OnCreateViewHolder` quando il `RecyclerView` richiede un nuovo titolare di visualizzazione per rappresentare un elemento. `OnCreateViewHolder` Ingrandisce la visualizzazione degli elementi dal file di layout della visualizzazione e include la visualizzazione in un nuovo `PhotoViewHolder` istanza. Il `PhotoViewHolder` costruttore individua e archivia i riferimenti alle visualizzazioni figlio nel layout, come descritto in precedenza in [titolare della visualizzazione](#view-holder).

Ogni elemento di riga è rappresentato da un `CardView` che contiene un `ImageView` (per le foto) e un `TextView` (per la didascalia). Questo layout si trova nel file **PhotoCardView.axml**:

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

Questo layout il nome rappresenta un elemento di riga singola nel `RecyclerView`. Il `OnBindViewHolder` (come descritto di seguito) metodo copia i dati dall'origine dati nel `ImageView` e `TextView` di questo layout.
`OnCreateViewHolder` incrementa un percorso determinato foto in questo layout il `RecyclerView` e crea un'istanza di un nuovo `PhotoViewHolder` istanza (che individua e memorizza nella cache i riferimenti al `ImageView` e `TextView` visualizzazioni figlio nell'oggetto associato `CardView` layout):

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

L'istanza di contenitore visualizzazione risultante, `vh`, viene restituito al chiamante (il gestore di layout).


#### <a name="onbindviewholder"></a>OnBindViewHolder

Quando è pronto per la visualizzazione di una determinata visualizzazione in gestore di layout la `RecyclerView`dell'area visibile dello schermo, chiama l'adapter `OnBindViewHolder` metodo da riempire l'elemento in corrispondenza della posizione di riga specificata con il contenuto dall'origine dati. `OnBindViewHolder` Ottiene le informazioni di foto per la posizione della riga specificata (risorsa immagine della foto e la stringa per la didascalia della foto) e copia i dati per le viste associate. Le visualizzazioni si trovano mediante i riferimenti archiviati nell'oggetto visualizzazione titolare (che viene passato tramite la `holder` parametro):

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

L'oggetto proprietario passati nella vista debba prima eseguirne il cast nel tipo di contenitore di visualizzazione derivate (in questo caso, `PhotoViewHolder`) prima di utilizzarlo.
L'adapter carica la risorsa immagine nella vista fa riferimento il contenitore di visualizzazione `Image` proprietà che copia il testo della didascalia nella vista fa riferimento il contenitore di visualizzazione `Caption` proprietà. Ciò *associa* la visualizzazione associata con i relativi dati.

Si noti che `OnBindViewHolder` è riportato il codice che gestisce direttamente la struttura dei dati. In questo caso `OnBindViewHolder` siano in grado di eseguire il mapping di `RecyclerView` posizione al relativo singolo dato associato nell'origine dei dati degli elementi. Il mapping è molto semplice in questo caso poiché la posizione può essere usata come un indice di matrice in album di foto; Tuttavia, le origini dati più complesse potrebbero richiedere codice aggiuntivo per stabilire questo mapping.


#### <a name="itemcount"></a>ItemCount

Il `ItemCount` metodo restituisce il numero di elementi nella raccolta dei dati. Nell'app Visualizzatore foto di esempio il numero di elementi è il numero di foto di album di foto:

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

Per altre informazioni sulle `RecyclerView.Adapter`, vedere la [riferimento alla classe RecyclerView.Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).


### <a name="putting-it-all-together"></a>Uso combinato tutti

L'oggetto risultante `RecyclerView` implementazione per l'app di foto di esempio è costituito `MainActivity` codice che crea l'origine dati, gestione di layout e l'adapter. `MainActivity` Crea il `mRecyclerView` istanza, crea un'istanza dell'origine dati e l'adapter e inserisce il gestore di layout e l'adapter:

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

Quando questo codice viene compilato ed eseguito, vengono creati la foto base Visualizza app come illustrato negli screenshot seguenti:

[![Due schermate delle app con scorrimento verticale schede foto di visualizzazione delle foto](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

Se le ombreggiature non vengono viene disegnate (come illustrato nello screenshot precedente), modificare **Properties/AndroidManifest.xml** e aggiungere la seguente impostazione di attributo per il `<application>` elemento:

```xml
android:hardwareAccelerated="true"
```

Questa app di base supporta solo l'esplorazione di album di foto. Non risponde per gli eventi touch elemento, né gestisce le modifiche nei dati sottostanti. Questa funzionalità viene aggiunto in [estendendo l'esempio di RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md).




### <a name="changing-the-layoutmanager"></a>Modifica il LayoutManager

Causa del `RecyclerView`della flessibilità, è facile modificare l'app per usare un gestore di layout diversi. Nell'esempio seguente viene modificata per visualizzare album di foto con un layout griglia che consente di scorrere orizzontalmente anziché con un layout verticale lineare. A tale scopo, la creazione di istanze di gestione di layout viene modificato per usare il `GridLayoutManager` come indicato di seguito:

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

Questa modifica di codice sostituisce verticale `LinearLayoutManager` con un `GridLayoutManager` che presenta una griglia costituita da due righe che scorrono nella direzione orizzontale. Quando compilare ed eseguire nuovamente l'app, si noterà che le foto viene visualizzati in una griglia e che lo scorrimento orizzontale, anziché verticale:

[![Screenshot di esempio di app con lo scorrimento orizzontale foto in una griglia](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

Se si modifica solo una riga di codice, è possibile modificare l'app di visualizzazione delle foto per usare un layout diverso con un comportamento diverso.
Si noti che il codice dell'adattatore, né il layout XML doveva essere modificato per modificare lo stile di layout. 

Nell'argomento successivo [estendendo l'esempio di RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md), questa app di esempio di base è stato esteso per la gestione degli eventi clic elemento e aggiornare `RecyclerView` quando le modifiche apportate all'origine dati sottostante.



## <a name="related-links"></a>Collegamenti correlati

- [RecyclerViewer (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Funzionalità e le parti di RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Estendendo l'esempio di RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
