---
title: Esempio di RecyclerView di base
description: App di esempio che illustra come usare RecyclerView.
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/30/2018
ms.openlocfilehash: 7266d13136dbfc858bc3c882cacb802d83c92f52
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028834"
---
# <a name="a-basic-recyclerview-example"></a>Esempio di RecyclerView di base

Per comprendere il funzionamento di `RecyclerView` in un'applicazione tipica, in questo argomento viene illustrata l'app di esempio [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) , un semplice esempio di codice che usa `RecyclerView` per visualizzare un'ampia raccolta di foto: 

[![due schermate di un'app RecyclerView che usa CardViews per visualizzare le foto](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** USA [CardView](~/android/user-interface/controls/card-view.md) per implementare ogni elemento della foto nel layout `RecyclerView`. A causa dei vantaggi in termini di prestazioni `RecyclerView`, questa app di esempio è in grado di scorrere rapidamente un'ampia raccolta di foto e senza ritardi evidenti.

### <a name="an-example-data-source"></a>Un'origine dati di esempio

In questa app di esempio, un'origine dati di "album foto" (rappresentata dalla classe `PhotoAlbum`) fornisce `RecyclerView` con il contenuto dell'elemento.
`PhotoAlbum` è una raccolta di foto con didascalie; Quando si crea un'istanza di tale raccolta, si ottiene una raccolta pronta di foto di 32:

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

Ogni istanza di Photo in `PhotoAlbum` espone proprietà che consentono di leggere l'ID risorsa immagine, `PhotoID`e la relativa stringa di didascalia, `Caption`. La raccolta di foto è organizzata in modo che ogni foto possa accedere a un indicizzatore. Ad esempio, le righe di codice seguenti accedono all'ID risorsa immagine e alla didascalia per la decima foto nella raccolta:

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` fornisce anche un metodo `RandomSwap` che è possibile chiamare per scambiare la prima foto della raccolta con una foto scelta in modo casuale altrove nella raccolta:

```csharp
mPhotoAlbum.RandomSwap ();
```

Poiché i dettagli di implementazione di `PhotoAlbum` non sono rilevanti per comprendere `RecyclerView`, il codice sorgente `PhotoAlbum` non viene presentato qui. Il codice sorgente da `PhotoAlbum` è disponibile in [PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs) nell'app di esempio [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) .

### <a name="layout-and-initialization"></a>Layout e inizializzazione

Il file di layout, **Main. aXML**, è costituito da una singola `RecyclerView` all'interno di un `LinearLayout`:

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

Si noti che è necessario usare il nome completo **Android. support. V7. widget. RecyclerView** perché `RecyclerView` è incluso in una libreria di supporto. Il metodo `OnCreate` di `MainActivity` Inizializza questo layout, crea un'istanza dell'adapter e prepara l'origine dati sottostante:

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

1. Crea un'istanza dell'origine dati `PhotoAlbum`.

2. Passa l'origine dati album foto al costruttore dell'adapter, `PhotoAlbumAdapter` (definito più avanti in questa guida). 
   Si noti che è considerata una procedura consigliata passare l'origine dati come parametro al costruttore dell'adapter. 

3. Ottiene l'`RecyclerView` dal layout.

4. Inserisce l'adapter nell'istanza di `RecyclerView` chiamando il metodo di `SetAdapter` `RecyclerView` come illustrato in precedenza.

### <a name="layout-manager"></a>Gestione layout

Ogni elemento nel `RecyclerView` è costituito da un `CardView` che contiene un'immagine di foto e una didascalia della foto. i dettagli sono descritti nella sezione [del supporto di visualizzazione](#view-holder) riportata di seguito. Il `LinearLayoutManager` predefinito viene utilizzato per il layout di ogni `CardView` in una disposizione di scorrimento verticale:

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

Questo codice risiede nel metodo di `OnCreate` dell'attività principale. Il costruttore per il gestore del layout richiede un *contesto*, quindi il `MainActivity` viene passato utilizzando `this`, come illustrato in precedenza.

Invece di usare la `LinearLayoutManager`predefinita, è possibile collegare un gestore di layout personalizzato che visualizza due elementi `CardView` affiancati, implementando un effetto di animazione di pagina per attraversare la raccolta di foto. Più avanti in questa guida verrà visualizzato un esempio di come modificare il layout scambiando in un diverso gestore di layout.

<a name="view-holder" />

### <a name="view-holder"></a>Visualizza il titolare

La classe del supporto di visualizzazione viene chiamata `PhotoViewHolder`. Ogni istanza di `PhotoViewHolder` include riferimenti al `ImageView` e `TextView` di un elemento di riga associato, che è disposto in un `CardView` come diagramma:

[![diagramma di CardView contenente un ImageView e una TextView](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` deriva da `RecyclerView.ViewHolder` e contiene le proprietà per archiviare i riferimenti al `ImageView` e `TextView` mostrati nel layout precedente.
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

In questo esempio di codice, al costruttore `PhotoViewHolder` viene passato un riferimento alla visualizzazione elemento padre (il `CardView`) che `PhotoViewHolder` a capo. Si noti che la visualizzazione dell'elemento padre viene sempre trasmessa al costruttore di base. Il costruttore `PhotoViewHolder` chiama `FindViewById` sulla visualizzazione elemento padre per individuare ogni riferimento alla visualizzazione figlio, `ImageView` e `TextView`, archiviando rispettivamente i risultati nelle proprietà `Image` e `Caption`. Successivamente, l'adapter recupera i riferimenti di visualizzazione da queste proprietà quando aggiorna le visualizzazioni figlio di questo `CardView`con nuovi dati.

Per ulteriori informazioni su `RecyclerView.ViewHolder`, vedere la pagina relativa al [riferimento alla classe RecyclerView. ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).

### <a name="adapter"></a>Adattatore

L'adapter carica ogni riga di `RecyclerView` con i dati di una particolare fotografia. Per una determinata fotografia alla posizione della riga *p*, ad esempio, l'adapter individua i dati associati nella posizione *p* all'interno dell'origine dati e copia questi dati nell'elemento riga nella posizione *P* nella raccolta di `RecyclerView`. L'adapter utilizza il titolare della visualizzazione per cercare i riferimenti per la `ImageView` e `TextView` in tale posizione, in modo che non sia necessario chiamare ripetutamente `FindViewById` per tali viste quando l'utente scorre la raccolta di foto e riutilizza le visualizzazioni.

In **RecyclerViewer**una classe Adapter deriva da `RecyclerView.Adapter` per creare `PhotoAlbumAdapter`:

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

Il membro `mPhotoAlbum` contiene l'origine dati (album foto) che viene passata al costruttore. il costruttore copia l'album foto in questa variabile membro. Vengono implementati i seguenti metodi di `RecyclerView.Adapter` obbligatori:

- **`OnCreateViewHolder`** &ndash; crea un'istanza del file di layout dell'elemento e del titolare della visualizzazione.

- **`OnBindViewHolder`** &ndash; carica i dati nella posizione specificata nelle viste i cui riferimenti sono archiviati nel contenitore di visualizzazione specificato.

- **`ItemCount`** &ndash; restituisce il numero di elementi presenti nell'origine dati.

Il gestore del layout chiama questi metodi mentre posiziona gli elementi all'interno del `RecyclerView`. L'implementazione di questi metodi viene esaminata nelle sezioni seguenti.

#### <a name="oncreateviewholder"></a>OnCreateViewHolder

Il gestore del layout chiama `OnCreateViewHolder` quando il `RecyclerView` necessita di un nuovo titolare di visualizzazione per rappresentare un elemento. `OnCreateViewHolder` ingrandisce la visualizzazione elemento dal file di layout della visualizzazione ed esegue il wrapping della vista in una nuova istanza di `PhotoViewHolder`. Il costruttore `PhotoViewHolder` individua e archivia i riferimenti alle visualizzazioni figlio nel layout, come descritto in precedenza nel [supporto di visualizzazione](#view-holder).

Ogni elemento di riga è rappresentato da una `CardView` che contiene una `ImageView` (per la foto) e da un `TextView` (per la didascalia). Questo layout risiede nel file **PhotoCardView. aXML**:

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

Questo layout rappresenta un singolo elemento di riga nel `RecyclerView`. Il metodo `OnBindViewHolder` (descritto di seguito) copia i dati dall'origine dati nella `ImageView` e `TextView` di questo layout.
`OnCreateViewHolder` ingrandisce questo layout per una determinata posizione della foto nel `RecyclerView` e crea un'istanza di una nuova istanza di `PhotoViewHolder` (che individua e memorizza nella cache i riferimenti alle visualizzazioni `ImageView` e `TextView` figlio nel layout di `CardView` associato) :

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

L'istanza del contenitore di visualizzazione risultante, `vh`, viene restituita al chiamante (gestione layout).

#### <a name="onbindviewholder"></a>OnBindViewHolder

Quando il gestore del layout è pronto per visualizzare una particolare visualizzazione nell'area dello schermo visibile del `RecyclerView`, chiama il metodo di `OnBindViewHolder` dell'adapter per riempire l'elemento in corrispondenza della posizione della riga specificata con il contenuto dell'origine dati. `OnBindViewHolder` ottiene le informazioni sulla foto per la posizione della riga specificata (la risorsa immagine della foto e la stringa per la didascalia della foto) e copia i dati nelle visualizzazioni associate. Le visualizzazioni si trovano tramite riferimenti archiviati nell'oggetto del supporto di visualizzazione (che viene passato tramite il parametro `holder`):

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

Prima di utilizzare l'oggetto contenitore della visualizzazione passato è necessario eseguire il cast nel tipo di supporto della visualizzazione derivata (in questo caso `PhotoViewHolder`).
L'adapter carica la risorsa immagine nella visualizzazione a cui fa riferimento la proprietà `Image` del contenitore di visualizzazione e copia il testo della didascalia nella visualizzazione a cui fa riferimento la proprietà `Caption` del contenitore di visualizzazione. Questa operazione *associa* la visualizzazione associata ai relativi dati.

Si noti che `OnBindViewHolder` è il codice che tratta direttamente con la struttura dei dati. In questo caso, `OnBindViewHolder` è in grado di eseguire il mapping della posizione dell'elemento `RecyclerView` all'elemento dati associato nell'origine dati. Il mapping è semplice in questo caso perché la posizione può essere usata come indice di matrice nell'album foto; Tuttavia, le origini dati più complesse potrebbero richiedere codice aggiuntivo per stabilire un mapping di questo tipo.

#### <a name="itemcount"></a>ItemCount

Il metodo `ItemCount` restituisce il numero di elementi nella raccolta di dati. Nell'app visualizzatore foto di esempio, il conteggio degli elementi è il numero di foto nell'album Foto:

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

Per ulteriori informazioni su `RecyclerView.Adapter`, vedere la pagina relativa al [riferimento alla classe RecyclerView. Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

### <a name="putting-it-all-together"></a>Riunendoli

L'implementazione del `RecyclerView` risultante per l'app Photo di esempio è costituita dal codice `MainActivity` che crea l'origine dati, il gestore del layout e l'adapter. in `MainActivity` viene creata l'istanza di `mRecyclerView`, viene creata un'istanza dell'origine dati e dell'adapter e vengono collegati il gestore del layout e l'adapter:

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

`PhotoViewHolder` individua e memorizza nella cache i riferimenti alla visualizzazione:

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

`PhotoAlbumAdapter` implementa i tre override del metodo necessari:

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

Quando il codice viene compilato ed eseguito, viene creata l'app di visualizzazione foto di base, come illustrato nelle schermate seguenti:

[![due schermate dell'app visualizzazione foto con le schede foto con scorrimento verticale](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

Se le ombre non vengono disegnate, come illustrato nella schermata precedente, modificare **Properties/file AndroidManifest. XML** e aggiungere l'impostazione dell'attributo seguente all'elemento `<application>`:

```xml
android:hardwareAccelerated="true"
```

Questa app di base supporta solo l'esplorazione dell'album di foto. Non risponde agli eventi di tocco elemento, né gestisce le modifiche nei dati sottostanti. Questa funzionalità viene aggiunta in [estensione dell'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md).

### <a name="changing-the-layoutmanager"></a>Modifica di LayoutManager

Grazie alla flessibilità di `RecyclerView`, è facile modificare l'app per l'uso di un diverso gestore del layout. Nell'esempio seguente viene modificato per visualizzare l'album di foto con un layout di griglia che scorre orizzontalmente anziché con un layout lineare verticale. A tale scopo, la creazione di un'istanza di gestione layout viene modificata in modo da utilizzare il `GridLayoutManager` come indicato di seguito:

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

Questa modifica del codice sostituisce la `LinearLayoutManager` verticale con una `GridLayoutManager` che presenta una griglia costituita da due righe che scorrono nella direzione orizzontale. Quando si compila ed esegue nuovamente l'app, si noterà che le fotografie vengono visualizzate in una griglia e lo scorrimento è orizzontale anziché verticale:

[![screenshot di esempio dell'app con scorrimento orizzontale delle foto in una griglia](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

Cambiando solo una riga di codice, è possibile modificare l'app di visualizzazione foto per usare un layout diverso con un comportamento diverso.
Si noti che non è stato necessario modificare il codice dell'adattatore né il file XML di layout per modificare lo stile del layout. 

Nell'argomento successivo, [estendendo l'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md), questa applicazione di esempio di base viene estesa per gestire gli eventi di clic dell'elemento e aggiornare `RecyclerView` quando viene modificata l'origine dati sottostante.

## <a name="related-links"></a>Collegamenti correlati

- [RecyclerViewer (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Parti e funzionalità di RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Estensione dell'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
