---
title: ActionBar per Xamarin.Android
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: f64b57e73b69b3111087ca1352f5fb9536f855e5
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488959"
---
# <a name="actionbar-for-xamarinandroid"></a>ActionBar per Xamarin.Android

Quando si usa `TabActivity`, il codice per creare le icone di tabulazione non ha alcun effetto quando viene eseguito sul Framework Android 4,0. Sebbene funzioni come nelle versioni di Android precedenti alla 2,3, il `TabActivity` classe è stato deprecato in 4,0. È stato introdotto un nuovo modo per creare un'interfaccia a schede che usa il Barra delle azioni, che verrà illustrato di seguito.

## <a name="action-bar-tabs"></a>Schede Barra delle azioni

Il Barra delle azioni include il supporto per l'aggiunta di interfacce a schede in Android 4,0.
Lo screenshot seguente mostra un esempio di tale interfaccia.

[![screenshot dell'app in esecuzione in un emulatore; vengono visualizzate due schede](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

Per creare schede nella Barra delle azioni, prima di tutto è necessario impostare la relativa proprietà `NavigationMode` per supportare le schede. In Android 4 è disponibile una proprietà `ActionBar` nella classe Activity, che è possibile usare per impostare il `NavigationMode` come segue:

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

Al termine di questa operazione, è possibile creare una scheda chiamando il metodo `NewTab` sull'Barra delle azioni. Con questa istanza di scheda è possibile chiamare i metodi `SetText` e `SetIcon` per impostare il testo e l'icona dell'etichetta della scheda; queste chiamate vengono effettuate in ordine nel codice riportato di seguito:

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

Prima di poter aggiungere la scheda, tuttavia, è necessario gestire l'evento `TabSelected`. In questo gestore, è possibile creare il contenuto per la scheda. Barra delle azioni schede sono progettate per funzionare con i *frammenti*, ovvero classi che rappresentano una parte dell'interfaccia utente in un'attività. Per questo esempio, la visualizzazione del frammento contiene un singolo `TextView`, che viene ingrandito nella sottoclasse `Fragment`, come indicato di seguito:

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

L'argomento dell'evento passato nell'evento `TabSelected` è di tipo `TabEventArgs`, che include una proprietà `FragmentTransaction` che è possibile usare per aggiungere il frammento, come illustrato di seguito:

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

Infine, è possibile aggiungere la scheda al Barra delle azioni chiamando il metodo `AddTab`, come illustrato nel codice seguente:

```csharp
this.ActionBar.AddTab (tab);
```

Per l'esempio completo, vedere il progetto *HelloTabsICS* nel codice di esempio per questo documento.

## <a name="shareactionprovider"></a>ShareActionProvider

La classe `ShareActionProvider` consente di eseguire un'azione di condivisione da una Barra delle azioni. Si occupa della creazione di una visualizzazione azione con un elenco di app in grado di gestire uno scopo di condivisione e mantiene una cronologia delle applicazioni utilizzate in precedenza per facilitarne l'accesso in un secondo momento dalla Barra delle azioni. Questo consente alle applicazioni di condividere i dati attraverso un'esperienza utente coerente in Android.

### <a name="image-sharing-example"></a>Esempio di condivisione immagini

Ad esempio, di seguito è riportata una schermata di un Barra delle azioni con una voce di menu per condividere un'immagine (tratto dall'esempio [ShareActionProvider](https://docs.microsoft.com/samples/xamarin/monodroid-samples/shareactionproviderdemo) ). Quando l'utente tocca la voce di menu nella Barra delle azioni, ShareActionProvider carica l'applicazione per gestire uno scopo associato al `ShareActionProvider`. In questo esempio, l'applicazione di messaggistica è stata usata in precedenza, quindi viene presentata nel Barra delle azioni.

[![screenshot dell'icona dell'applicazione di messaggistica nell'Barra delle azioni](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)

Quando l'utente fa clic sull'elemento nel Barra delle azioni, viene avviata l'app di messaggistica che contiene l'immagine condivisa, come illustrato di seguito:

[![screenshot dell'app di messaggistica che Visualizza l'immagine Monkey](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)

### <a name="specifying-the-action-provider-class"></a>Specifica della classe del provider di azioni

Per usare la `ShareActionProvider`, impostare l'attributo `android:actionProviderClass` in una voce di menu nel codice XML per il menu del Barra delle azioni come indicato di seguito:

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

Per ingrandire il menu, viene eseguito l'override di `OnCreateOptionsMenu` nella sottoclasse Activity. Una volta ottenuto un riferimento al menu, è possibile ottenere il `ShareActionProvider` dalla proprietà `ActionProvider` della voce di menu e quindi usare il metodo SetShareIntent per impostare la finalità del `ShareActionProvider`, come illustrato di seguito:

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

Il `ShareActionProvider` utilizzerà lo scopo, passato al metodo `SetShareIntent` nel codice precedente, per avviare l'attività appropriata. In questo caso viene creata una finalità per inviare un'immagine usando il codice seguente:

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
