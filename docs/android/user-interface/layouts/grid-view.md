---
title: GridView
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 9ec27402aefd28ded4cf53e7e9fa52eedd2b86b6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103011"
---
# <a name="gridview"></a>GridView

[`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) è un [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
che visualizza gli elementi in una griglia bidimensionale è possibile scorrere. Gli elementi presenti vengono inseriti automaticamente per il layout usando un [ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/).

In questa esercitazione si creerà una griglia di anteprime delle immagini. Quando viene selezionato un elemento, un messaggio di tipo avviso popup visualizzerà la posizione dell'immagine.

Avviare un nuovo progetto denominato **HelloGridView**.

Trovare alcuni foto da usare, oppure [scaricare queste immagini di esempio](http://developer.android.com/shareables/sample_images.zip). Aggiungere i file di immagine al progetto **risorse/Drawable** directory. Nel **delle proprietà** finestra, impostare l'azione di compilazione per ognuno di essi al **AndroidResource**.

Aprire il **Resources/Layout/Main.axml** file e inserire il codice seguente:

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

Ciò [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) riempirà l'intera schermata. Gli attributi sono piuttosto autoesplicativo. Per altre informazioni sugli attributi validi, vedere la [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) riferimento.

Apri `HelloGridView.cs` e inserire il codice seguente per il [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
metodo:

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

Dopo il **Main. axml** layout è impostato per la visualizzazione contenuto, il [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) viene acquisita dal layout con [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/). Il [`Adapter`](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/)
proprietà viene quindi utilizzata per impostare un adapter personalizzato (`ImageAdapter`) come origine per tutti gli elementi da visualizzare nella griglia. Il `ImageAdapter` viene creato nel passaggio successivo.

Per eseguire un'operazione quando viene selezionata una voce nella griglia, un delegato anonimo ha sottoscritto il [ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/) evento.
Viene illustrato un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) che visualizza la posizione di indice (in base zero) dell'elemento selezionato (in uno scenario reale, la posizione potrebbe essere usata per ottenere l'immagine con dimensione completa per un'altra attività). Si noti che è possono utilizzare classi di listener basato su Java anziché gli eventi .NET.

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

In primo luogo, si implementa alcuni metodi necessari ereditati dalla [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/). Il costruttore e il [ `Count` ](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/) proprietà sono di chiara interpretazione. In genere, [`GetItem(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/)
deve restituire l'oggetto effettivo in corrispondenza della posizione specificata nell'adapter, ma viene ignorata per questo esempio. Analogamente, [`GetItemId(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/)
dovrebbe restituire l'id di riga dell'elemento, ma non è necessaria in questo caso.

Il primo metodo necessario consiste [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/).
Questo metodo crea un nuovo [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
per ogni immagine aggiunti per il `ImageAdapter`. Quando questo viene chiamato, un [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
viene passato, che in genere è un oggetto riciclato (almeno dopo questa chiamata una sola volta), pertanto non c'è un controllo per verificare se l'oggetto è null. Se si *è* null, un [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
viene creata un'istanza e configurato con le proprietà desiderate per la presentazione di immagine:

- [`LayoutParams`](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) Imposta l'altezza e larghezza per la visualizzazione&mdash;in questo modo, indipendentemente dalle dimensioni del drawable, ogni immagine viene ridimensionata e ritagliata per rientrare in queste dimensioni, come appropriato.

- [`SetScaleType()`](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetScaleType/) dichiara che immagini devono essere ritagliate verso il centro (se necessario).

- [`SetPadding(int, int, int, int)`](https://developer.xamarin.com/api/member/Android.Views.View.SetPadding/) definisce il riempimento per tutti i lati. Si noti che, se le immagini hanno proporzioni diverse, quindi meno la spaziatura interna, causerà per ritagliare altre dell'immagine se le dimensioni fornite per il ImageView non corrisponde.

Se il [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) passati al [ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/) è *non* null, quindi locale [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
viene inizializzato con il riciclo [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) oggetto.

Alla fine del [`GetView()`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/)
metodo, il `position` integer passato al metodo viene usato per selezionare un'immagine dal `thumbIds` matrice, che viene impostata come la risorsa immagine per il [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/).

Tutto ciò che resta consiste nel definire il `thumbIds` risorse drawable.

Eseguire l'applicazione. Il layout di griglia dovrebbe essere simile al seguente:

[![Screenshot di esempio di GridView visualizzano le 15 immagini](grid-view-images/helloviews4.png)](grid-view-images/helloviews4.png#lightbox)

Provare a sperimentare con i comportamenti del [ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/) e [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
elementi modificando le relative proprietà. Ad esempio, invece di usare [ `LayoutParams` ](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) provare a usare [ `SetAdjustViewBounds()` ](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetAdjustViewBounds/).


## <a name="references"></a>Riferimenti

-   [`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) 
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/).

*Parti di questa pagina sono modifiche basate sul lavoro creato e condiviso da Android Open Source Project e usate in base a condizioni descritte nel*
[*licenza Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).
