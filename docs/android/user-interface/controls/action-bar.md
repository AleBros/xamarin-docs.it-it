---
title: ActionBar
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 9d9bef6d1a0817abc12b5a9bd266b1e1e7d38348
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667548"
---
# <a name="actionbar"></a>ActionBar


## <a name="overview"></a>Panoramica

Quando si usa `TabActivity`, il codice per creare le icone di scheda non ha alcun effetto quando vengono eseguito su framework Android 4.0. Anche se a livello funzionale funziona come accadeva nelle versioni di Android precedenti la 2.3, il `TabActivity` classe stessa è stata deprecata in 4.0. È stato introdotto un nuovo modo per creare un'interfaccia a schede che usa la barra delle azioni, che verranno illustrati di seguito.


## <a name="action-bar-tabs"></a>Schede della barra delle azioni

Barra delle azioni include il supporto per l'aggiunta di interfacce a schede in Android 4.0.
Lo screenshot seguente mostra un esempio di tale interfaccia.

[![Screenshot dell'app in esecuzione in un emulatore. vengono visualizzate due schede](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

Per creare le schede nella barra delle azioni, è necessario innanzitutto impostare relativo `NavigationMode` proprietà per supportare le schede. In Android 4, un' `ActionBar` proprietà è disponibile nella classe di attività, è possibile usare per impostare il `NavigationMode` simile al seguente:

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

Al termine, è possibile creare una scheda tramite una chiamata di `NewTab` (metodo) sulla barra delle azioni. Con questa istanza di scheda, possiamo chiamare il `SetText` e `SetIcon` metodi per impostare un'icona e il testo dell'etichetta della scheda tali chiamate vengono eseguite nell'ordine nel codice riportato di seguito:

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

Prima di poter aggiungere la scheda, tuttavia, è necessario gestire il `TabSelected` evento. In questo gestore, è possibile creare il contenuto della scheda. Le schede della barra delle azioni sono progettate per funzionare con *frammenti*, che sono classi che rappresentano una parte dell'interfaccia utente in un'attività. Per questo esempio, la visualizzazione del frammento contiene un unico `TextView`, che sono l'aumento nel nostro `Fragment` sottoclasse simile al seguente:

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

L'argomento dell'evento passato il `TabSelected` evento è di tipo `TabEventArgs`, che include un `FragmentTransaction` proprietà che è possibile usare per aggiungere il frammento come illustrato di seguito:

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

Infine, è possibile aggiungere la scheda alla barra delle azioni tramite una chiamata di `AddTab` metodo come illustrato nel codice seguente:

```csharp
this.ActionBar.AddTab (tab);
```

Per un esempio completo, vedere la *HelloTabsICS* progetto nel codice di esempio per questo documento.


## <a name="shareactionprovider"></a>ShareActionProvider

Il `ShareActionProvider` classe consente a un'azione di condivisione in modo da una barra delle azioni. Si occupa della creazione di una vista di azione con un elenco di App che può gestire un programma di condivisione e mantiene una cronologia delle applicazioni usate in precedenza per facilitare l'accesso ad essi in un secondo momento dalla barra delle azioni. Ciò consente alle applicazioni di condividere i dati tramite un'esperienza utente che sia coerenza in Android.


### <a name="image-sharing-example"></a>Esempio di condivisione dell'immagine

Ad esempio, di seguito è riportata una schermata di una barra delle azioni con una voce di menu per condividere un'immagine (ricavare il [ShareActionProvider](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/) esempio). Quando l'utente tocca la voce di menu sulla barra delle azioni, di ShareActionProvider carica l'applicazione debba gestire un Intent che è associato il `ShareActionProvider`. In questo esempio, l'applicazione di messaggistica è stato utilizzato in precedenza, in modo che viene presentato sulla barra delle azioni.

[![Screenshot dell'icona nella barra delle azioni di messaggistica](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)


Quando l'utente fa clic sull'elemento nella barra delle azioni, viene avviata l'app di messaggistica che contiene l'immagine condiviso, come illustrato di seguito:

[![Schermata di visualizzazione immagine monkey app di messaggistica](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)


### <a name="specifying-the-action-provider-class"></a>Specifica l'azione di classe del Provider

Usare la `ShareActionProvider`, impostare il `android:actionProviderClass` attributo su una voce di menu nel codice XML per il menu della barra delle azioni, come indicato di seguito:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```


### <a name="inflating-the-menu"></a>Ciò fa aumentare erroneamente il menu di scelta

Per aumento il menu di scelta, viene eseguito l'override `OnCreateOptionsMenu` nella sottoclasse attività. Dopo aver ottenuto un riferimento al menu di scelta, è possibile ottenere il `ShareActionProvider` dal `ActionProvider` proprietà della voce di menu e quindi usare il metodo SetShareIntent per impostare il `ShareActionProvider`dell'intento, come illustrato di seguito:

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


### <a name="creating-the-intent"></a>Lo scopo di creazione

Il `ShareActionProvider` userà la finalità, passata per il `SetShareIntent` metodo nel codice precedente, per avviare l'attività appropriata. In questo caso si crea un Intent per inviare un'immagine usando il codice seguente:

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

L'immagine nell'esempio di codice precedente è incluso come un asset con l'applicazione e copiato in una posizione accessibile pubblicamente quando viene creata l'attività, in modo che sia accessibile ad altre applicazioni, ad esempio le app di messaggistica. Il codice di esempio che accompagna questo articolo contiene il codice sorgente completo di questo esempio, che illustra l'uso.



## <a name="related-links"></a>Collegamenti correlati

- [Hello schede ICS (esempio)](https://developer.xamarin.com/samples/HelloTabsICS/)
- [ShareActionProvider Demo (esempio)](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)
- [Introduzione a Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma Android 4.0](https://developer.android.com/sdk/android-4.0.html)
