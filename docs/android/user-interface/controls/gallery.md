---
title: Raccolta
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/15/2018
ms.openlocfilehash: cb05dfb79c00e9c6f9fa1a8d80627abdb911c36e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="gallery"></a>Raccolta

[`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/) è un widget di layout utilizzato per visualizzare gli elementi in un elenco di scorrimento orizzontale e posiziona la selezione corrente al centro della vista.

> [!IMPORTANT]
> Questo widget è stato deprecato in Android 4.1 (livello API 16). 

In questa esercitazione viene creata una raccolta di foto e quindi visualizzare un messaggio di tipo avviso popup ogni volta che viene selezionato un elemento della raccolta.

Dopo il `Main.axml` layout è impostato per la visualizzazione contenuto, il `Gallery` viene acquisito dal layout con [ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/).
Il [ `Adapter` ](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/) proprietà viene quindi utilizzata per impostare un adapter personalizzato ( `ImageAdapter`) come origine per tutti gli elementi da visualizzare nella finestra di dallery. Il `ImageAdapter` viene creato nel passaggio successivo.

Per eseguire un'operazione quando si fa clic su un elemento nella raccolta, un delegato anonimo ha sottoscritto il [ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/) evento. Viene illustrato un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) che visualizza la posizione di indice (in base zero) dell'elemento selezionati (in uno scenario reale, la posizione può essere utilizzata per ottenere l'immagine di dimensione completa per un'altra attività).

In primo luogo, esistono alcune variabili membro, inclusa una matrice di ID che fanno riferimento alle immagini salvate nella directory delle risorse drawable (**risorse/drawable**).

Di seguito è riportato il costruttore della classe, in cui il [ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/) per un `ImageAdapter` istanza viene definita e salvata in un campo locale.
Successivamente, si implementano alcuni metodi richiesti ereditati da [ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/).
Il costruttore e [ `Count` ](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/) proprietà sono di chiara interpretazione. In genere, [ `GetItem(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/p/System.Int32/) deve restituire l'oggetto in corrispondenza della posizione specificata nell'adapter effettivo, ma viene ignorato per questo esempio. Analogamente, [ `GetItemId(int)` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/p/System.Int32/) deve restituire l'id di riga dell'elemento, ma non è necessario qui.

Il metodo esegue il lavoro per applicare un'immagine da un [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) che verrà incorporato nel [ `Gallery` ](https://developer.xamarin.com/api/type/Android.Widget.Gallery/) In questo metodo, il membro [ `Context` ](https://developer.xamarin.com/api/type/Android.Content.Context/) è utilizzato per creare un nuovo [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/).
Il [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) viene preparata applicando un'immagine dalla matrice locale drawable risorse, l'impostazione di [ `Gallery.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.Gallery+LayoutParams/) altezza e la larghezza dell'immagine, impostare la scala per adattare il [ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/) dimensioni e infine l'impostazione dello sfondo per utilizzare l'attributo styleable acquisito nel costruttore.

Vedere [ `ImageView.ScaleType` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView+ScaleType/) per altre opzioni di ridimensionamento dell'immagine.

## <a name="walkthrough"></a>Procedura dettagliata

Avviare un nuovo progetto denominato *HelloGallery*.

[![Schermata del nuovo progetto Android nella finestra di dialogo nuova soluzione](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

Trovare alcune foto che si desidera utilizzare, o [scaricare queste immagini di esempio](http://developer.android.com/shareables/sample_images.zip).
Aggiungere i file di immagine al progetto **risorse/Drawable** directory. Nel **proprietà** finestra, impostare l'azione di compilazione per ognuno di essi al **AndroidResource**.

Aprire **Resources/Layout/Main.axml** e inserire il seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

Aprire `MainActivity.cs` e inserire il codice seguente per il [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) metodo:

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

Eseguire l'applicazione. Dovrebbe essere simile la schermata riportata di seguito:

![Schermata di HelloGallery la visualizzazione di immagini di esempio](gallery-images/hellogallery3.png)



## <a name="references"></a>Riferimenti

-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
-   [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)

*Parti di questa pagina sono modifiche basate sul lavoro creati e condivisi per il progetto Android di origine aprire e usare in base alle condizioni descritte nel*
[*Creative Commons 2.5 attribuzione licenza* ](http://creativecommons.org/licenses/by/2.5/).


