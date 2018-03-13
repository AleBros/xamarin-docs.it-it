---
title: ActionBar
ms.topic: article
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 64a5ac7e0c448205da66f9790a506ca34a944140
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="actionbar"></a>ActionBar


## <a name="overview"></a>Panoramica

Quando si utilizza `TabActivity`, il codice per creare le icone di scheda non ha alcun effetto quando eseguita in framework Android 4.0. Anche se il livello funzionale funziona come accadeva nelle versioni precedenti di Android 2.3, la `TabActivity` classe stessa è stata deprecata in 4.0. È stato introdotto un nuovo modo per creare un'interfaccia a schede che utilizza la barra delle azioni, che verranno successivamente.


## <a name="action-bar-tabs"></a>Schede sulla barra delle azioni

La barra delle azioni include il supporto per l'aggiunta di schede di interfacce in Android 4.0.
Nella schermata seguente viene illustrato un esempio di tale interfaccia.

[![Schermata dell'app in esecuzione in un emulatore; vengono visualizzate due schede](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

Per creare le schede nella barra delle azioni, è innanzitutto necessario impostare il relativo `NavigationMode` proprietà per supportare le schede. Android 4, un `ActionBar` proprietà è disponibile nella classe di attività, è possibile utilizzare per impostare il `NavigationMode` come segue:

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

Al termine, è possibile creare una scheda chiamando il `NewTab` (metodo) sulla barra delle azioni. Con questa istanza di scheda, è possibile chiamare il `SetText` e `SetIcon` metodi per impostare il testo dell'etichetta della scheda e l'icona; tali chiamate vengono eseguite nell'ordine nel codice riportato di seguito:

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

Tuttavia, per poter aggiungere la scheda, è necessario gestire il `TabSelected` evento. In questo gestore, è possibile creare il contenuto per la scheda. Schede sulla barra delle azioni sono progettate per funzionare con *frammenti*, che sono classi che rappresentano una parte dell'interfaccia utente in un'attività. Per questo esempio, la visualizzazione del frammento contiene un singolo `TextView`, che è ingrandimento nel nostro `Fragment` sottoclasse simile al seguente:

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

L'argomento dell'evento passato il `TabSelected` evento è di tipo `TabEventArgs`, che include un `FragmentTransaction` proprietà che è possibile utilizzare per aggiungere il frammento, come illustrato di seguito:

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

Infine, è possibile aggiungere la scheda per la barra delle azioni chiamando il `AddTab` metodo come illustrato in questo codice:

```csharp
this.ActionBar.AddTab (tab);
```

Per un esempio completo, vedere il *HelloTabsICS* progetto nell'esempio di codice per questo documento.


## <a name="shareactionprovider"></a>ShareActionProvider

La `ShareActionProvider` classe consente a un'azione da eseguire sul posto da una barra di azione di condivisione. Si occupa di creare una vista di azione con un elenco di App che è in grado di gestire un intento di condivisione e mantiene una cronologia delle applicazioni usate in precedenza per facilitare l'accesso a essi in un secondo momento dalla barra delle azioni. Ciò consente alle applicazioni di condividere i dati tramite un'esperienza utente coerenza in Android.


### <a name="image-sharing-example"></a>Esempio di immagine di condivisione

Ad esempio, di seguito è una schermata di una barra delle azioni con una voce di menu per condividere un'immagine (ricavato il [ShareActionProvider](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/) esempio). Quando l'utente tocca la voce di menu sulla barra delle azioni, il ShareActionProvider carica l'applicazione per gestire un intento di cui è associato il `ShareActionProvider`. In questo esempio, l'applicazione di messaggistica è stato precedentemente utilizzato, pertanto viene visualizzato sulla barra delle azioni.

[![Schermata dell'icona nella barra delle azioni di applicazione di messaggistica](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)


Quando l'utente fa clic sull'elemento nella barra delle azioni, viene avviata l'app di messaggistica che contiene l'immagine condivisa, come illustrato di seguito:

[![Schermata dell'app messaggistica visualizzazione monkey immagine](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)


### <a name="specifying-the-action-provider-class"></a>Specifica l'azione di classe del Provider

Utilizzare il `ShareActionProvider`, impostare il `android:actionProviderClass` attributo su una voce di menu nel codice XML per il menu della barra delle azioni, come indicato di seguito:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```


### <a name="inflating-the-menu"></a>Il Menu eccessi nella misurazione

Per il menu di ingrandimento, viene eseguito l'override `OnCreateOptionsMenu` nella sottoclasse attività. Dopo aver ottenuto un riferimento al menu, è possibile ottenere il `ShareActionProvider` dal `ActionProvider` proprietà dell'elemento di menu e quindi utilizzare il metodo SetShareIntent per impostare il `ShareActionProvider`della finalità, come illustrato di seguito:

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

Il `ShareActionProvider` utilizzerà la finalità, passata per il `SetShareIntent` metodo nel codice precedente, per avviare l'attività appropriata. In questo caso, si crea un tentativo di inviare un'immagine utilizzando il codice seguente:

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

L'immagine nell'esempio di codice precedente è incluso come un asset con l'applicazione e copiato in un percorso accessibile pubblicamente quando viene creata l'attività, in modo che sia accessibile alle altre applicazioni, ad esempio l'applicazione di messaggistica. Il codice di esempio che accompagna questo articolo contiene il codice sorgente completo di questo esempio, che illustra l'uso.



## <a name="related-links"></a>Collegamenti correlati

- [Condivisione connessione Internet schede Hello (esempio)](https://developer.xamarin.com/samples/HelloTabsICS/)
- [Demo di ShareActionProvider (esempio)](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)
- [Introduzione a Sandwich gelato](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
