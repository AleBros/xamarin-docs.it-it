---
title: Raccolta
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: f9b73428531deeacc7bdea271cdc0c2872038e99
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218534"
---
# <a name="gallery"></a>Raccolta

[`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/) è un widget di layout utilizzato per visualizzare gli elementi in un elenco di scorrimento orizzontale e posiziona la selezione corrente al centro della visualizzazione.

> [!IMPORTANT]
> Questo widget è stato deprecato in Android 4.1 (livello API 16). 

In questa esercitazione, è creata una raccolta di foto e quindi visualizzare un messaggio di tipo avviso popup ogni volta che viene selezionato un elemento della raccolta.

Dopo il `Main.axml` layout è impostato per la visualizzazione contenuto, il `Gallery` viene acquisita dal layout con [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/).
Il [`Adapter`](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/)
proprietà viene quindi utilizzata per impostare un adapter personalizzato ( `ImageAdapter`) come origine per tutti gli elementi da visualizzare nella finestra di dallery. Il `ImageAdapter` viene creato nel passaggio successivo.

Per eseguire un'operazione quando si fa clic su un elemento nella raccolta, un delegato anonimo ha sottoscritto il [`ItemClick`](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)
. Viene illustrato un [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
che visualizza la posizione di indice (in base zero) dell'elemento selezionati (in uno scenario reale, la posizione potrebbe essere usata per ottenere l'immagine con dimensione completa per un'altra attività).

In primo luogo, esistono alcune variabili di membro, inclusa un'ampia gamma di ID che fanno riferimento le immagini salvate nella directory delle risorse drawable (**risorse/drawable**).

Di seguito è il costruttore della classe, in cui il [`Context`](https://developer.xamarin.com/api/type/Android.Content.Context/)
per un `ImageAdapter` istanza viene definita e salvata un campo locale.
Successivamente, si implementa alcuni metodi necessari ereditati dalla [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/).
Il costruttore e il [`Count`](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/)
proprietà sono di chiara interpretazione. In genere, [`GetItem(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/p/System.Int32/)
deve restituire l'oggetto effettivo in corrispondenza della posizione specificata nell'adapter, ma viene ignorata per questo esempio. Likewise, [`GetItemId(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/p/System.Int32/)
dovrebbe restituire l'id di riga dell'elemento, ma non è necessaria in questo caso.

Il metodo esegue il lavoro per applicare un'immagine a un [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
che verrà incorporato di [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
In questo metodo, il membro [`Context`](https://developer.xamarin.com/api/type/Android.Content.Context/)
è possibile creare una nuova [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/).
Il [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
viene preparata applicando un'immagine dalla matrice locale delle risorse drawable, impostando il [`Gallery.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.Gallery+LayoutParams/)
altezza e la larghezza dell'immagine, l'impostazione di scalabilità per adattare il [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
le dimensioni e quindi impostando infine lo sfondo per usare l'attributo API acquisito nel costruttore.

Visualizzare [ `ImageView.ScaleType` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView+ScaleType/) per altre opzioni di ridimensionamento dell'immagine.

## <a name="walkthrough"></a>Procedura dettagliata

Avviare un nuovo progetto denominato *HelloGallery*.

[![Schermata del nuovo progetto Android nella finestra di dialogo nuova soluzione](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

Trovare alcuni foto da usare, oppure [scaricare queste immagini di esempio](https://developer.android.com/shareables/sample_images.zip).
Aggiungere i file di immagine al progetto **risorse/Drawable** directory. Nel **delle proprietà** finestra, impostare l'azione di compilazione per ognuno di essi al **AndroidResource**.

Aprire **Resources/Layout/Main.axml** e inserire il codice seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

Apri `MainActivity.cs` e inserire il codice seguente per il [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
metodo:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    Gallery gallery = (Gallery) FindViewById<Gallery>(Resource.Id.gallery);

    gallery.Adapter = new ImageAdapter (this);

    gallery.ItemClick += delegate (object sender, Android.Widget.AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

Creare una nuova classe denominata `ImageAdapter` che rappresenta una sottoclasse [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
          context = c;
    }

    public override int Count { get { return thumbIds.Length; } }

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
          ImageView i = new ImageView (context);

          i.SetImageResource (thumbIds[position]);
          i.LayoutParameters = new Gallery.LayoutParams (150, 100);
          i.SetScaleType (ImageView.ScaleType.FitXy);

          return i;
    }

    // references to our images
    int[] thumbIds = {
            Resource.Drawable.sample_1,
            Resource.Drawable.sample_2,
            Resource.Drawable.sample_3,
            Resource.Drawable.sample_4,
            Resource.Drawable.sample_5,
            Resource.Drawable.sample_6,
            Resource.Drawable.sample_7
     };
}

```

Eseguire l'applicazione. Dovrebbe essere simile allo screenshot seguente:

![Schermata di HelloGallery visualizzano le immagini di esempio](gallery-images/hellogallery3.png)



## <a name="references"></a>Riferimenti

-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
-   [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)

*Parti di questa pagina sono modifiche basate sul lavoro creato e condiviso da Android Open Source Project e usate in base a condizioni descritte nel*
[*licenza Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).


