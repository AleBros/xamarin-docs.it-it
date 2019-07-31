---
title: ActionBar per Novell. Android
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 4a0d0e46147a37da4787224e797d403ab7b1097e
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643048"
---
# <a name="actionbar-for-xamarinandroid"></a>ActionBar per Novell. Android

Quando si `TabActivity`USA, il codice per creare le icone di tabulazione non ha alcun effetto quando viene eseguito sul Framework Android 4,0. Anche se funziona come nelle versioni di Android precedenti alla 2,3, la `TabActivity` classe stessa è stata deprecata in 4,0. È stato introdotto un nuovo modo per creare un'interfaccia a schede che usa il Barra delle azioni, che verrà illustrato di seguito.


## <a name="action-bar-tabs"></a>Schede Barra delle azioni

Il Barra delle azioni include il supporto per l'aggiunta di interfacce a schede in Android 4,0.
Lo screenshot seguente mostra un esempio di tale interfaccia.

[![Screenshot dell'app in esecuzione in un emulatore; vengono visualizzate due schede](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

Per creare schede nella barra delle azioni, è prima di tutto necessario impostare la `NavigationMode` proprietà per supportare le schede. In Android 4 è disponibile `ActionBar` una proprietà nella classe Activity, che è possibile usare per `NavigationMode` impostare come segue:

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

Al termine di questa operazione, è possibile creare una scheda chiamando il `NewTab` metodo sul barra delle azioni. Con questa istanza di scheda è possibile chiamare i `SetText` metodi `SetIcon` e per impostare il testo e l'icona dell'etichetta della scheda. queste chiamate vengono eseguite nell'ordine indicato nel codice riportato di seguito:

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

Prima di poter aggiungere la scheda, tuttavia, è necessario gestire l' `TabSelected` evento. In questo gestore, è possibile creare il contenuto per la scheda. Barra delle azioni schede sono progettate per funzionare con i *frammenti*, ovvero classi che rappresentano una parte dell'interfaccia utente in un'attività. Per questo esempio, la visualizzazione del frammento contiene un `TextView`singolo oggetto, che viene ingrandito `Fragment` nella sottoclasse come indicato di seguito:

```csharp
class SampleTabFragment: Fragment
{           
    public override View OnCreateView (LayoutInflater inflater,
        ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreateView (inflater, container, savedInstanceState);

        var view = inflater.Inflate (
            Resource.Layout.Tab, container, false);

        var sampleTextView =
            view.FindViewById<TextView> (Resource.Id.sampleTextView);            
        sampleTextView.Text = "sample fragment text";


        return view;
    }
}
```

L'argomento dell'evento passato nell' `TabSelected` evento è di tipo `TabEventArgs`, che include una `FragmentTransaction` proprietà che è possibile usare per aggiungere il frammento, come illustrato di seguito:

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

Infine, è possibile aggiungere la scheda al barra delle azioni chiamando il `AddTab` metodo come illustrato nel codice seguente:

```csharp
this.ActionBar.AddTab (tab);
```

Per l'esempio completo, vedere il progetto *HelloTabsICS* nel codice di esempio per questo documento.


## <a name="shareactionprovider"></a>ShareActionProvider

La `ShareActionProvider` classe consente di eseguire un'azione di condivisione da un barra delle azioni. Si occupa della creazione di una visualizzazione azione con un elenco di app in grado di gestire uno scopo di condivisione e mantiene una cronologia delle applicazioni utilizzate in precedenza per facilitarne l'accesso in un secondo momento dalla Barra delle azioni. Questo consente alle applicazioni di condividere i dati attraverso un'esperienza utente coerente in Android.


### <a name="image-sharing-example"></a>Esempio di condivisione immagini

Ad esempio, di seguito è riportata una schermata di un Barra delle azioni con una voce di menu per condividere un'immagine (tratto dall'esempio [ShareActionProvider](https://docs.microsoft.com/samples/xamarin/monodroid-samples/shareactionproviderdemo) ). Quando l'utente tocca la voce di menu nella Barra delle azioni, ShareActionProvider carica l'applicazione per gestire uno scopo associato a `ShareActionProvider`. In questo esempio, l'applicazione di messaggistica è stata usata in precedenza, quindi viene presentata nel Barra delle azioni.

[![Screenshot dell'icona dell'applicazione di messaggistica nell'Barra delle azioni](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)


Quando l'utente fa clic sull'elemento nel Barra delle azioni, viene avviata l'app di messaggistica che contiene l'immagine condivisa, come illustrato di seguito:

[![Screenshot dell'app di messaggistica che Visualizza l'immagine Monkey](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)


### <a name="specifying-the-action-provider-class"></a>Specifica della classe del provider di azioni

Per utilizzare `ShareActionProvider`, impostare l' `android:actionProviderClass` attributo su una voce di menu nel codice XML per il menu barra delle azioni come indicato di seguito:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```


### <a name="inflating-the-menu"></a>Gonfiaggio del menu

Per ingrandire il menu, viene eseguito `OnCreateOptionsMenu` l'override nella sottoclasse Activity. Una volta ottenuto un riferimento al menu, è possibile ottenere `ShareActionProvider` `ActionProvider` dalla proprietà della voce di menu e quindi usare il metodo SetShareIntent per impostare lo `ShareActionProvider`scopo di, come illustrato di seguito:

```csharp
public override bool OnCreateOptionsMenu (IMenu menu)
{
    MenuInflater.Inflate (Resource.Menu.ActionBarMenu, menu);       

    var shareMenuItem = menu.FindItem (Resource.Id.shareMenuItem);           
    var shareActionProvider =
       (ShareActionProvider)shareMenuItem.ActionProvider;
    shareActionProvider.SetShareIntent (CreateIntent ());
}
```


### <a name="creating-the-intent"></a>Creazione della finalità

Utilizzerà lo scopo, passato `SetShareIntent` al metodo nel codice precedente, per avviare l'attività appropriata. `ShareActionProvider` In questo caso viene creata una finalità per inviare un'immagine usando il codice seguente:

```csharp
Intent CreateIntent ()
{  
    var sendPictureIntent = new Intent (Intent.ActionSend);
    sendPictureIntent.SetType ("image/*");
    var uri = Android.Net.Uri.FromFile (GetFileStreamPath ("monkey.png"));          
    sendPictureIntent.PutExtra (Intent.ExtraStream, uri);
    return sendPictureIntent;
}
```

L'immagine nell'esempio di codice precedente viene inclusa come asset con l'applicazione e copiata in una posizione accessibile pubblicamente quando viene creata l'attività, quindi sarà accessibile ad altre applicazioni, ad esempio l'app di messaggistica. Il codice di esempio che accompagna questo articolo contiene l'origine completa di questo esempio, ne illustra l'uso.



## <a name="related-links"></a>Collegamenti correlati

- [Scheda Hello ICS (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/hellotabsics)
- [Demo di ShareActionProvider (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/shareactionproviderdemo)
- [Introduzione a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4,0](https://developer.android.com/sdk/android-4.0.html)
