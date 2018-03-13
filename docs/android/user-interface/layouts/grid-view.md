---
title: GridView
ms.topic: article
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 2fb3133833dbaa0b174c4611d204f6c8ceb42a2b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="gridview"></a>GridView

[`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) è un [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) che visualizza gli elementi in una griglia scorrevole, bidimensionale. Gli elementi vengono inseriti automaticamente per il layout usando un [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/).

In questa esercitazione si creerà una griglia di anteprime delle immagini. Quando viene selezionato un elemento, un messaggio di tipo avviso popup Visualizza la posizione dell'immagine.

Avviare un nuovo progetto denominato **HelloGridView**.

Trovare alcune foto che si desidera utilizzare, o [scaricare queste immagini di esempio](http://developer.android.com/shareables/sample_images.zip). Aggiungere i file di immagine al progetto **risorse/Drawable** directory. Nel **proprietà** finestra, impostare l'azione di compilazione per ognuno di essi al **AndroidResource**.

Aprire il **Resources/Layout/Main.axml** file e inserire il seguente:

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

Questo [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) riempie lo schermo intero. Gli attributi sono già di piuttosto facile comprensione. Per ulteriori informazioni sugli attributi validi, vedere il [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) riferimento.

Aprire `HelloGridView.cs` e inserire il codice seguente per il [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) metodo:

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

Dopo il **axml** layout è impostato per la visualizzazione contenuto, il [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) viene acquisito dal layout con [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/). Il [ `Adapter` ](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/) proprietà viene quindi utilizzata per impostare un adapter personalizzato (`ImageAdapter`) come origine per tutti gli elementi da visualizzare nella griglia. Il `ImageAdapter` viene creato nel passaggio successivo.

Per eseguire un'operazione quando si fa clic su un elemento della griglia, si sottoscrive un delegato anonimo la [ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/) evento.
Viene illustrato un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) che visualizza la posizione di indice (in base zero) dell'elemento selezionato (in uno scenario reale, la posizione può essere utilizzata per ottenere l'immagine di dimensione completa per un'altra attività). Si noti che è possono utilizzare le classi di listener di tipo Java anziché gli eventi di .NET.

Creare una nuova classe denominata `ImageAdapter` che rappresenta una sottoclasse [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

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

In primo luogo, si implementano alcuni metodi richiesti ereditati da [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/). Il costruttore e [ `Count` ](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/) proprietà sono di chiara interpretazione. In genere, [ `GetItem(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/) deve restituire l'oggetto in corrispondenza della posizione specificata nell'adapter effettivo, ma viene ignorato per questo esempio. Analogamente, [ `GetItemId(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/) deve restituire l'id di riga dell'elemento, ma non è necessario qui.

È il primo metodo necessario [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/).
Questo metodo crea un nuovo [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) per ciascuna immagine aggiunta per il `ImageAdapter`. Quando questo metodo viene chiamato, un [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) viene passato in cui è in genere di un oggetto riciclato (almeno dopo questa chiamata una volta), pertanto non c'è un controllo per verificare se l'oggetto è null. Se si *è* null, un [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) viene creata un'istanza e configurato con le proprietà desiderate per la presentazione di immagine:

- [`LayoutParams`](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) Imposta l'altezza e larghezza per la visualizzazione&mdash;ciò garantisce che, indipendentemente dalla dimensione del drawable ogni immagine ridimensionata e ritagliata per adattarla alle dimensioni di queste dimensioni, come appropriato.

- [`SetScaleType()`](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetScaleType/) dichiara che le immagini devono essere ritagliate verso il centro (se necessario).

- [`SetPadding(int, int, int, int)`](https://developer.xamarin.com/api/member/Android.Views.View.SetPadding/) definisce la spaziatura interna per tutti i lati. Si noti che, se le immagini proporzioni diverse, quindi meno la spaziatura interna causerà per ritagliare altre dell'immagine se le dimensioni fornite per il ImageView non corrisponde.

Se il [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) passato a [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/) è *non* null, quindi locale [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) viene inizializzato con il riciclo [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) oggetto.

Alla fine del [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/) (metodo), il `position` integer passato al metodo viene utilizzato per selezionare un'immagine dal `thumbIds` matrice, che viene impostata come la risorsa immagine per il [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/).

Tutto ciò che resta consiste nel definire il `thumbIds` matrice di risorse drawable.

Eseguire l'applicazione. Il layout di griglia dovrebbe essere simile al seguente:

[![Schermata di esempio di visualizzazione di 15 immagini GridView](grid-view-images/helloviews4.png)](grid-view-images/helloviews4.png#lightbox)

Provare a sperimentare con i comportamenti del [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) e [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) elementi regolando le relative proprietà. Ad esempio, anziché utilizzare [ `LayoutParams` ](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) provare a utilizzare [ `SetAdjustViewBounds()` ](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetAdjustViewBounds/).


## <a name="references"></a>Riferimenti

-   [`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) 
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/).

*Parti di questa pagina sono modifiche basate sul lavoro creati e condivisi per il progetto Android di origine aprire e usare in base alle condizioni descritte nel*
[*Creative Commons 2.5 attribuzione licenza* ](http://creativecommons.org/licenses/by/2.5/).
