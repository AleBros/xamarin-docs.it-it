---
title: Aggiunta di una seconda barra degli strumenti
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: fc95c05c1945464cd9cac8565d8a11ff1b4c7e1d
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527287"
---
# <a name="adding-a-second-toolbar"></a>Aggiunta di una seconda barra degli strumenti


## <a name="overview"></a>Panoramica 

Il `Toolbar` può eseguire più di sostituire la barra delle azioni &ndash; può essere utilizzato più volte all'interno di un'attività, può essere personalizzato per la selezione host in un punto qualsiasi sullo schermo e può essere configurato per estendersi solo una parziale larghezza dello schermo. Gli esempi seguenti illustrano come creare una seconda `Toolbar` e inserirlo nella parte inferiore della schermata. Ciò `Toolbar` implementi **copia**, **Taglia**, e **Incolla** voci di menu. 


## <a name="define-the-second-toolbar"></a>Definire la seconda barra degli strumenti 

Modificare il file di layout **Main. axml** e sostituirne il contenuto con con il codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:id="@+id/main_content"
        android:layout_below="@id/toolbar">
      <ImageView
          android:layout_width="fill_parent"
          android:layout_height="0dp"
          android:layout_weight="1" />
      <Toolbar
          android:id="@+id/edit_toolbar"
          android:minHeight="?android:attr/actionBarSize"
          android:background="?android:attr/colorAccent"
          android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
          android:layout_width="match_parent"
          android:layout_height="wrap_content" />
    </LinearLayout>
</RelativeLayout>
```

Questo codice XML viene aggiunto un secondo `Toolbar` nella parte inferiore dello schermo, con un oggetto vuoto `ImageView` la compilazione al centro dello schermo. L'altezza di questo `Toolbar` è impostato per l'altezza di una barra delle azioni: 

```xml
android:minHeight="?android:attr/actionBarSize"
```

Il colore di sfondo di questo `Toolbar` è impostata su un colore che verrà definito successivamente:

```xml
android:background="?android:attr/colorAccent
```

Si noti che da questo `Toolbar` si basa su un tema diverso (**ThemeOverlay.Material.Dark.ActionBar**) diverso da quello usato per il `Toolbar` creato nel [sostituendo la barra delle azioni](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash;non è associata a decor finestra dell'attività o al tema utilizzato nel primo `Toolbar`.

Modificare **Resources/values/styles.xml** e aggiungere il colore principale seguente alla definizione di stile: 

```xml
<item name="android:colorAccent">#C7A935</item>
```

In questo modo la barra degli strumenti nella parte inferiore di colore ambra scuro. Creazione ed esecuzione dell'app Visualizza una barra di secondo vuota nella parte inferiore della schermata: 

[![Screenshot dell'app con barra di secondo gialla nella parte inferiore della schermata](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)


 
## <a name="add-edit-menu-items"></a>Aggiungere le voci di Menu Modifica 

In questa sezione viene illustrato come aggiungere voci di menu Modifica verso il basso `Toolbar`. 

Per aggiungere voci di menu in un database secondario `Toolbar`: 

1.  Aggiungere le icone di menu per il `mipmap-` cartelle di progetto dell'app (se richiesto).

2.  Definire il contenuto delle voci di menu tramite l'aggiunta di un file di risorse di menu aggiuntive per **risorse/menu**. 

3.  Dell'attività `OnCreate` metodo, individuare il `Toolbar` (chiamando `FindViewById`) e aumento il `Toolbar`del menu.

4.  Implementare un gestore di clic in `OnCreate` per nuove voci di menu. 

Le sezioni seguenti illustrano in dettaglio questo processo: **tagliare**, **copia**, e **Incolla** vengono aggiunte voci di menu nella parte inferiore `Toolbar`. 



### <a name="define-the-edit-menu-resource"></a>Definire la risorsa di Menu Modifica

Nel **/menu risorse** sottodirectory, creare un nuovo file XML denominato **edit_menus.xml** e sostituire il contenuto con il codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       android:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

Questo codice XML crea il **tagliare**, **copia**, e **Incolla** voci di menu (usando le icone che sono stati aggiunti al `mipmap-` cartelle in [sostituendo la barra delle azioni ](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).



### <a name="inflate-the-menus"></a>Aumento i menu

Alla fine del `OnCreate` nel metodo **MainActivity.cs**, aggiungere le righe di codice seguenti: 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

Questo codice consente di individuare il `edit_toolbar` definito nella vista **Main. axml**, imposta il titolo **modifica**e incrementa le voci di menu (definito in **edit_menus.xml**). Definisce un menu fare clic sul gestore che viene visualizzato un avviso popup per indicare quale icona di modifica viene toccato. 

Compilare ed eseguire l'app. Quando si esegue l'app, verranno visualizzato il testo e icone aggiunte in precedenza come illustrato di seguito: 

[![Diagramma della parte inferiore della barra degli strumenti con le icone di Taglia, copia e Incolla](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

Se si tocca il **Taglia** icona menu fa sì che il seguente avviso popup da visualizzare: 

[![Schermata di tipo avviso popup che indica che è stato toccato l'icona di menu Taglia](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

Toccando le voci di menu su una barra degli strumenti consente di visualizzare gli di avvisi popup risultanti: 

[![Screenshot di avvisi popup per salvare, copiare e incollare le voci di menu viene toccate](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)



## <a name="the-up-button"></a>Il pulsante di scorrimento 

La maggior parte delle App per Android si basano sul **nuovamente** pulsante per lo spostamento di app, premendo il **nuovamente** pulsante, l'utente passa alla schermata precedente.
Tuttavia, è anche possibile fornire un' **backup** pulsante che rende più semplice per gli utenti possono spostarsi "massimo" alla schermata principale dell'app. Quando l'utente seleziona il **iscrizione** button, l'utente sposta verso l'alto per un livello superiore nella gerarchia di app &ndash; , ovvero l'app viene visualizzata l'utente torna più attività nello stack indietro anziché back POP per l'oggetto selezionato in precedenza Attività. 

Per abilitare la **backup** pulsante in una seconda attività che usa un `Toolbar` come la barra delle azioni, chiamare il `SetDisplayHomeAsUpEnabled` e `SetHomeButtonEnabled` metodi la seconda attività `OnCreate` metodo:

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

Il [v7 sulla barra degli strumenti di supporto](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/) esempio di codice viene illustrato il **backup** pulsante in azione. Questo esempio, che usa la libreria AppCompat descritta di seguito, viene implementata una seconda attività che usa la barra degli strumenti **backup** pulsante per la navigazione gerarchica tornare all'attività precedente. In questo esempio, il `DetailActivity` pulsante home consente la **iscrizione** pulsante effettuando quanto segue `SupportActionBar` chiamate al metodo: 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

Quando l'utente passa da `MainActivity` al `DetailActivity`, il `DetailActivity` consente di visualizzare un' **backup** pulsante (puntamento freccia sinistra) come illustrato nello screenshot:

[![Screenshot di esempio di una pulsante a sinistra freccia sulla barra degli strumenti](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

Se si tocca questo **iscrizione** pulsante fa sì che l'app tornare alla `MainActivity`. In un'app più complessa con più livelli della gerarchia, toccare questo pulsante restituirebbe l'utente al livello più alto successivo nell'app invece che alla schermata precedente. 



## <a name="related-links"></a>Collegamenti correlati

- [Barra degli strumenti lollipop (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra degli strumenti AppCompat (esempio)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
