---
title: Controllo raccolta Android
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/15/2018
ms.openlocfilehash: 93eb7f98da6f3fe06f288eae5823f7173e58585f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029237"
---
# <a name="xamarinandroid-gallery-control"></a>Controllo raccolta Xamarin.Android

[`Gallery`](xref:Android.Widget.Gallery) è un widget di layout usato per visualizzare gli elementi in un elenco a scorrimento orizzontale e posiziona la selezione corrente al centro della visualizzazione.

> [!IMPORTANT]
> Questo widget è stato deprecato in Android 4,1 (livello API 16). 

In questa esercitazione verrà creata una raccolta di foto e quindi verrà visualizzato un messaggio di avviso popup ogni volta che viene selezionato un elemento della raccolta.

Una volta impostato il layout `Main.axml` per la visualizzazione contenuto, il `Gallery` viene acquisito dal layout con [`FindViewById`](xref:Android.App.Activity.FindViewById*).
[`Adapter`](xref:Android.Widget.AdapterView.RawAdapter)
la proprietà viene quindi utilizzata per impostare un adapter personalizzato (`ImageAdapter`) come origine per tutti gli elementi da visualizzare nella proprietà. Il `ImageAdapter` viene creato nel passaggio successivo.

Per eseguire un'operazione quando si fa clic su un elemento della raccolta, un delegato anonimo viene sottoscritto al [`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)
. Mostra un [`Toast`](xref:Android.Widget.Toast)
che visualizza la posizione di indice (in base zero) dell'elemento theselected (in uno scenario reale, la posizione può essere usata per ottenere l'immagine con dimensioni complete per un'altra attività).

In primo luogo, sono disponibili alcune variabili membro, tra cui una matrice di ID che fanno riferimento alle immagini salvate nella directory delle risorse che è stato disegnato (**risorse/disegnatore**).

Next è il costruttore della classe, in cui il [`Context`](xref:Android.Content.Context)
per un'istanza di `ImageAdapter` viene definita e salvata in un campo locale.
Successivamente, implementa alcuni metodi obbligatori ereditati da [`BaseAdapter`](xref:Android.Widget.BaseAdapter).
Il costruttore e il [`Count`](xref:Android.Widget.BaseAdapter.Count)
le proprietà sono di chiara interpretazione. In genere, [`GetItem(int)`](xref:Android.Widget.BaseAdapter.GetItem*)
deve restituire l'oggetto effettivo in corrispondenza della posizione specificata nell'adapter, ma viene ignorato per questo esempio. Allo stesso modo, [`GetItemId(int)`](xref:Android.Widget.BaseAdapter.GetItemId*)
deve restituire l'ID di riga dell'elemento, ma non è necessario.

Il metodo esegue il lavoro per applicare un'immagine a un [`ImageView`](xref:Android.Widget.ImageView)
che verrà incorporato nel [`Gallery`](xref:Android.Widget.Gallery)
In questo metodo, il membro [`Context`](xref:Android.Content.Context)
viene usato per creare una nuova [`ImageView`](xref:Android.Widget.ImageView).
[`ImageView`](xref:Android.Widget.ImageView)
viene preparato applicando un'immagine dall'array locale di risorse che è necessario creare, impostando il [`Gallery.LayoutParams`](xref:Android.Widget.Gallery.LayoutParams)
altezza e larghezza per l'immagine, impostando la scala per adattarla al [`ImageView`](xref:Android.Widget.ImageView)
dimensioni e infine l'impostazione dello sfondo per l'utilizzo dell'attributo con stile acquisito nel costruttore.

Vedere [`ImageView.ScaleType`](xref:Android.Widget.ImageView.ScaleType) per altre opzioni di ridimensionamento delle immagini.

## <a name="walkthrough"></a>Procedura dettagliata

Avviare un nuovo progetto denominato *HelloGallery*.

[![screenshot del nuovo progetto Android nella finestra di dialogo nuova soluzione](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

Trovare alcune foto che si vuole usare o [scaricare queste immagini di esempio](https://developer.android.com/shareables/sample_images.zip).
Aggiungere i file di immagine alla directory **Resources/disegnator** del progetto. Nella finestra **Proprietà** impostare l'azione di compilazione per ogni su **AndroidResource**.

Aprire **risorse/layout/Main. aXML** e inserire quanto segue:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

Aprire `MainActivity.cs` e inserire il codice seguente per la [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
Metodo

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

Creare una nuova classe denominata `ImageAdapter` sottoclassi [`BaseAdapter`](xref:Android.Widget.BaseAdapter):

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

Eseguire l'applicazione. Dovrebbe essere simile alla schermata seguente:

![Screenshot di HelloGallery che visualizza le immagini di esempio](gallery-images/hellogallery3.png)

## <a name="references"></a>Riferimenti

- [`BaseAdapter`](xref:Android.Widget.BaseAdapter)
- [`Gallery`](xref:Android.Widget.Gallery)
- [`ImageView`](xref:Android.Widget.ImageView)

_Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base alle condizioni descritte nella [licenza Creative Commons 2,5 Attribution](https://creativecommons.org/licenses/by/2.5/)._
