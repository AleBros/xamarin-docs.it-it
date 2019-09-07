---
title: Esempio di RecyclerView di base
description: App di esempio che illustra come usare RecyclerView.
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/30/2018
ms.openlocfilehash: 0c6568f9283491f0e55c79c77fb8efb2ae75f33c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758613"
---
# <a name="a-basic-recyclerview-example"></a>Esempio di RecyclerView di base

Per comprendere come `RecyclerView` funziona in un'applicazione tipica, in questo argomento viene illustrata l'app di esempio [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) , un semplice esempio `RecyclerView` di codice che usa per visualizzare una grande raccolta di foto: 

[![Due screenshot di un'app RecyclerView che usa CardViews per visualizzare le foto](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer** USA [CardView](~/android/user-interface/controls/card-view.md) per implementare ogni `RecyclerView` elemento della foto nel layout. `RecyclerView`Grazie ai vantaggi in termini di prestazioni, questa app di esempio è in grado di scorrere rapidamente un'ampia raccolta di foto e senza ritardi evidenti.

### <a name="an-example-data-source"></a>Un'origine dati di esempio

In questa app di esempio, un'origine dati di "album foto" (rappresentata dalla classe `RecyclerView` ) fornisce il contenuto dell' `PhotoAlbum` elemento.
`PhotoAlbum`è una raccolta di foto con didascalie; Quando si crea un'istanza di tale raccolta, si ottiene una raccolta pronta di foto di 32:

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

Ogni istanza di Photo `PhotoAlbum` in espone proprietà che consentono di leggere l'ID risorsa immagine, `PhotoID`e la relativa stringa didascalia `Caption`,. La raccolta di foto è organizzata in modo che ogni foto possa accedere a un indicizzatore. Ad esempio, le righe di codice seguenti accedono all'ID risorsa immagine e alla didascalia per la decima foto nella raccolta:

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum`fornisce anche un `RandomSwap` metodo che è possibile chiamare per scambiare la prima foto della raccolta con una foto scelta in modo casuale altrove nella raccolta:

```csharp
mPhotoAlbum.RandomSwap ();
```

Poiché i dettagli di implementazione `PhotoAlbum` di non sono rilevanti per `RecyclerView`la comprensione `PhotoAlbum` , il codice sorgente non viene presentato qui. Il codice sorgente per `PhotoAlbum` è disponibile in [PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs) nell'app di esempio [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) .

### <a name="layout-and-initialization"></a>Layout e inizializzazione

Il file di layout, **Main. aXML**, è costituito `RecyclerView` da un `LinearLayout`singolo all'interno di un:

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

Si noti che è necessario usare il nome completo **Android. support. V7. widget. RecyclerView** perché `RecyclerView` è incluso in una libreria di supporto. `OnCreate` Il`MainActivity` metodo inizializza questo layout, crea un'istanza dell'adapter e prepara l'origine dati sottostante:

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

1. Crea un'istanza `PhotoAlbum` dell'origine dati.

2. Passa l'origine dati album foto al costruttore dell'adattatore, `PhotoAlbumAdapter` definito più avanti in questa guida. 
   Si noti che è considerata una procedura consigliata passare l'origine dati come parametro al costruttore dell'adapter. 

3. `RecyclerView` Ottiene dal layout.

4. Inserisce l'adattatore nell' `RecyclerView` istanza di chiamando il `RecyclerView` `SetAdapter` metodo come illustrato in precedenza.

### <a name="layout-manager"></a>Gestione layout

Ogni elemento `RecyclerView` del è costituito da un `CardView` oggetto che contiene un'immagine foto e una didascalia della foto. i dettagli sono descritti nella sezione [del supporto di visualizzazione](#view-holder) riportata di seguito. Il valore predefinito `LinearLayoutManager` viene utilizzato per il layout di `CardView` ognuno in una disposizione di scorrimento verticale:

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

Questo codice risiede nel `OnCreate` metodo dell'attività principale. Il costruttore per il gestore del layout richiede un *contesto*, pertanto `MainActivity` viene passato utilizzando `this` come illustrato in precedenza.

Invece di usare il valore predefinito `LinearLayoutManager`, è possibile collegare un gestore di layout personalizzato che visualizza due `CardView` elementi affiancati, implementando un effetto di animazione di trasformazioni di pagina per attraversare la raccolta di foto. Più avanti in questa guida verrà visualizzato un esempio di come modificare il layout scambiando in un diverso gestore di layout.

<a name="view-holder" />

### <a name="view-holder"></a>Visualizza il titolare

Viene chiamata `PhotoViewHolder`la classe del contenitore di visualizzazione. Ogni `PhotoViewHolder` istanza include riferimenti `ImageView` all'oggetto e `TextView` di un elemento di riga associato, che è disposto in un `CardView` come diagramma qui:

[![Diagramma di CardView contenente un ImageView e una TextView](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder`deriva da `RecyclerView.ViewHolder` e contiene le proprietà per archiviare i `ImageView` riferimenti a e `TextView` mostrati nel layout precedente.
`PhotoViewHolder`è costituito da due proprietà e un costruttore:

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

In questo esempio di codice, `PhotoViewHolder` al costruttore viene passato un riferimento alla visualizzazione elemento padre `CardView`() che `PhotoViewHolder` esegue il wrapping. Si noti che la visualizzazione dell'elemento padre viene sempre trasmessa al costruttore di base. Il `PhotoViewHolder` costruttore chiama `FindViewById` sulla visualizzazione elemento padre per individuare tutti i `ImageView` relativi riferimenti di visualizzazione figlio e `TextView`, archiviando rispettivamente i risultati nelle `Image` proprietà `Caption` e. L'adapter recupera in un secondo momento i riferimenti di visualizzazione da queste `CardView`proprietà quando aggiorna le visualizzazioni figlio con nuovi dati.

Per ulteriori informazioni su `RecyclerView.ViewHolder`, vedere il [riferimento alla classe RecyclerView. ViewHolder](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html).

### <a name="adapter"></a>Adattatore

L'adapter carica ogni `RecyclerView` riga con i dati di una fotografia particolare. Per una determinata fotografia alla posizione della riga *p*, ad esempio, l'adapter individua i dati associati nella posizione *p* all'interno dell'origine dati e copia questi dati nell'elemento riga nella posizione *P* `RecyclerView` della raccolta. L'adapter utilizza il titolare della visualizzazione per cercare i riferimenti per `ImageView` e `TextView` in tale posizione, in modo che non sia necessario `FindViewById` chiamare ripetutamente tali viste quando l'utente scorre la raccolta di foto e riutilizza le visualizzazioni.

In **RecyclerViewer**, una classe Adapter deriva da `RecyclerView.Adapter` per creare: `PhotoAlbumAdapter`

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

Il `mPhotoAlbum` membro contiene l'origine dati (album foto) che viene passata al costruttore. il costruttore copia l'album di foto in questa variabile membro. Vengono implementati `RecyclerView.Adapter` i metodi obbligatori seguenti:

- **`OnCreateViewHolder`** &ndash; Crea un'istanza del file di layout dell'elemento e del titolare della visualizzazione.

- **`OnBindViewHolder`** &ndash; Carica i dati nella posizione specificata nelle viste i cui riferimenti sono archiviati nel contenitore di visualizzazione specificato.

- **`ItemCount`** &ndash; Restituisce il numero di elementi nell'origine dati.

Il gestore del layout chiama questi metodi mentre posiziona gli elementi all'interno `RecyclerView`di. L'implementazione di questi metodi viene esaminata nelle sezioni seguenti.

#### <a name="oncreateviewholder"></a>OnCreateViewHolder

Il gestore del layout `OnCreateViewHolder` chiama quando `RecyclerView` necessita di un nuovo titolare della visualizzazione per rappresentare un elemento. `OnCreateViewHolder`ingrandisce la visualizzazione elemento dal file di layout della visualizzazione ed esegue il wrapping della vista in una `PhotoViewHolder` nuova istanza. Il `PhotoViewHolder` Costruttore individua e archivia i riferimenti alle visualizzazioni figlio nel layout come descritto in precedenza nel [supporto di visualizzazione](#view-holder).

Ogni elemento di riga è rappresentato da `CardView` un oggetto che `ImageView` contiene un (per la foto) `TextView` e un (per la didascalia). Questo layout risiede nel file **PhotoCardView. aXML**:

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

Questo layout rappresenta un singolo elemento riga in `RecyclerView`. Il `OnBindViewHolder` metodo (descritto di seguito) copia i dati dall'origine dati `ImageView` in e `TextView` in questo layout.
`OnCreateViewHolder`consente di ingrandire questo layout per una determinata posizione della `RecyclerView` foto in e crea un' `PhotoViewHolder` istanza di una nuova istanza (che individua e memorizza nella `ImageView` cache `TextView` i riferimenti alle visualizzazioni figlio `CardView` e nel layout associato):

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

L'istanza del supporto di visualizzazione `vh`risultante,, viene restituita al chiamante (gestione layout).

#### <a name="onbindviewholder"></a>OnBindViewHolder

Quando il gestore del layout è pronto per visualizzare una particolare visualizzazione nell' `RecyclerView`area dello schermo visibile, chiama il `OnBindViewHolder` metodo dell'adapter per riempire l'elemento in corrispondenza della posizione della riga specificata con il contenuto dell'origine dati. `OnBindViewHolder`Ottiene le informazioni sulla foto per la posizione della riga specificata (la risorsa immagine della foto e la stringa per la didascalia della foto) e copia i dati nelle visualizzazioni associate. Le visualizzazioni si trovano tramite riferimenti archiviati nell'oggetto del supporto di visualizzazione (che viene passato tramite `holder` il parametro):

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

Prima di utilizzare l'oggetto contenitore della visualizzazione passato è necessario eseguire il cast del tipo di supporto della visualizzazione derivata ( `PhotoViewHolder`in questo caso).
L'adapter carica la risorsa immagine nella visualizzazione a cui fa riferimento la `Image` proprietà del contenitore di visualizzazione e copia il testo della didascalia nella visualizzazione a cui fa riferimento la `Caption` proprietà del contenitore di visualizzazione. Questa operazione *associa* la visualizzazione associata ai relativi dati.

Si noti `OnBindViewHolder` che è il codice che tratta direttamente con la struttura dei dati. In questo caso, `OnBindViewHolder` è in grado di eseguire `RecyclerView` il mapping della posizione dell'elemento all'elemento dati associato nell'origine dati. Il mapping è semplice in questo caso perché la posizione può essere usata come indice di matrice nell'album foto; Tuttavia, le origini dati più complesse potrebbero richiedere codice aggiuntivo per stabilire un mapping di questo tipo.

#### <a name="itemcount"></a>ItemCount

Il `ItemCount` metodo restituisce il numero di elementi nella raccolta di dati. Nell'app visualizzatore foto di esempio, il conteggio degli elementi è il numero di foto nell'album Foto:

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

Per ulteriori informazioni su `RecyclerView.Adapter`, vedere il [riferimento alla classe RecyclerView. Adapter](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html).

### <a name="putting-it-all-together"></a>Riunendoli

L'implementazione `RecyclerView` risultante per l'app Photo di `MainActivity` esempio è costituita dal codice che crea l'origine dati, il gestore del layout e l'adapter. `MainActivity`consente di `mRecyclerView` creare l'istanza, di creare un'istanza dell'origine dati e dell'adapter e di collegare il gestore del layout e l'adapter:

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

`PhotoViewHolder`individua e memorizza nella cache i riferimenti alla visualizzazione:

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

`PhotoAlbumAdapter`implementa i tre override del metodo necessari:

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

[![Due screenshot dell'app di visualizzazione foto con le schede foto con scorrimento verticale](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

Se le ombre non vengono disegnate, come illustrato nella schermata precedente, modificare **Properties/file AndroidManifest. XML** e aggiungere l'impostazione dell'attributo seguente all' `<application>` elemento:

```xml
android:hardwareAccelerated="true"
```

Questa app di base supporta solo l'esplorazione dell'album di foto. Non risponde agli eventi di tocco elemento, né gestisce le modifiche nei dati sottostanti. Questa funzionalità viene aggiunta in [estensione dell'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md).

### <a name="changing-the-layoutmanager"></a>Modifica di LayoutManager

`RecyclerView`Grazie alla flessibilità, è facile modificare l'app per l'uso di un diverso gestore del layout. Nell'esempio seguente viene modificato per visualizzare l'album di foto con un layout di griglia che scorre orizzontalmente anziché con un layout lineare verticale. A tale scopo, viene modificata la creazione di un'istanza di gestione layout `GridLayoutManager` per l'utilizzo di come indicato di seguito:

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

Questa modifica del codice sostituisce la `LinearLayoutManager` verticale con `GridLayoutManager` un oggetto che presenta una griglia costituita da due righe che scorrono nella direzione orizzontale. Quando si compila ed esegue nuovamente l'app, si noterà che le fotografie vengono visualizzate in una griglia e lo scorrimento è orizzontale anziché verticale:

[![Schermata di esempio dell'app con scorrimento orizzontale delle foto in una griglia](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

Cambiando solo una riga di codice, è possibile modificare l'app di visualizzazione foto per usare un layout diverso con un comportamento diverso.
Si noti che non è stato necessario modificare il codice dell'adattatore né il file XML di layout per modificare lo stile del layout. 

Nell'argomento successivo, [estendendo l'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md), questa applicazione di esempio di base viene estesa per gestire gli eventi di `RecyclerView` clic dell'elemento e aggiornare quando viene modificata l'origine dati sottostante.

## <a name="related-links"></a>Collegamenti correlati

- [RecyclerViewer (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [Parti e funzionalità di RecyclerView](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [Estensione dell'esempio RecyclerView](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
