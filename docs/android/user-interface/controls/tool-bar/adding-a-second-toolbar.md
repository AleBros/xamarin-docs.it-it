---
title: Aggiunta di una seconda barra degli strumenti
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: f2255ab5ac7e153266093877a1c52bfc08927a09
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766637"
---
# <a name="adding-a-second-toolbar"></a>Aggiunta di una seconda barra degli strumenti


## <a name="overview"></a>Panoramica 

Il `Toolbar` può eseguire altre operazioni di sostituzione sulla barra delle azioni &ndash; e può essere utilizzato più volte all'interno di un'attività, può essere personalizzato per la selezione in un punto qualsiasi sullo schermo, e può essere configurato per coprire solo una parziale larghezza dello schermo. Gli esempi seguenti viene illustrato come creare un secondo `Toolbar` e inserirlo nella parte inferiore della schermata. Questo `Toolbar` implementa **copia**, **Taglia**, e **Incolla** voci di menu. 


## <a name="define-the-second-toolbar"></a>Definire la seconda barra degli strumenti 

Modificare il file di layout **axml** e sostituirne il contenuto con con il codice XML seguente:

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

Questo codice XML aggiunge un secondo `Toolbar` nella parte inferiore della schermata con un oggetto vuoto `ImageView` riempimento al centro dello schermo. L'altezza di questa `Toolbar` è impostato per l'altezza della barra delle azioni: 

```xml
android:minHeight="?android:attr/actionBarSize"
```

Il colore di sfondo di questo `Toolbar` è impostata su un colore che verrà definito successivamente:

```xml
android:background="?android:attr/colorAccent
```

Si noti che questo `Toolbar` si basa su un tema diverso (**ThemeOverlay.Material.Dark.ActionBar**) rispetto a quello utilizzato dal `Toolbar` creato in [sostituendo la barra delle azioni](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash;non è associata a Progettazione finestra dell'attività o al tema utilizzato nel primo `Toolbar`.

Modifica **Resources/values/styles.xml** e aggiungere il seguente colore alla definizione di stile: 

```xml
<item name="android:colorAccent">#C7A935</item>
```

In questo modo la barra degli strumenti nella parte inferiore di colore giallo scuro. Compilazione ed esecuzione dell'applicazione consente di visualizzare una barra degli strumenti secondo vuoto nella parte inferiore dello schermo: 

[![Schermata dell'app con giallo seconda barra degli strumenti nella parte inferiore della schermata](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)


 
## <a name="add-edit-menu-items"></a>Aggiungere voci di Menu Modifica 

In questa sezione viene illustrato come aggiungere voci di menu di modifica nella parte inferiore `Toolbar`. 

Per aggiungere voci di menu a un database secondario `Toolbar`: 

1.  Icone del menu per aggiungere il `mipmap-` cartelle di progetto di applicazione (se richiesto).

2.  Definire il contenuto delle voci di menu tramite l'aggiunta di un file di risorse di menu aggiuntive per **risorse/menu**. 

3.  Dell'attività `OnCreate` metodo, trovare il `Toolbar` (chiamando `FindViewById`) e l'ingrandimento di `Toolbar`del menu.

4.  Implementare un gestore click in `OnCreate` per nuove voci di menu. 

Nelle sezioni seguenti illustrano in dettaglio il processo: **Taglia**, **copia**, e **Incolla** vengono aggiunte voci di menu nella parte inferiore `Toolbar`. 



### <a name="define-the-edit-menu-resource"></a>Definire la risorsa di Menu Modifica

Nel **risorse/menu** sottodirectory, creare un nuovo file XML denominato **edit_menus.xml** e sostituire il contenuto con il codice XML seguente:

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

Questo codice XML crea il **Taglia**, **copia**, e **Incolla** voci di menu (utilizzando le icone che sono stati aggiunti al `mipmap-` cartelle in [sostituendo la barra delle azioni ](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).



### <a name="inflate-the-menus"></a>I menu di ingrandimento

Alla fine del `OnCreate` metodo **Mainactivity**, aggiungere le righe di codice seguente: 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

Questo codice consente di individuare il `edit_toolbar` le viste definite in **axml**, imposta il titolo **modifica**e incrementa le voci di menu (definito in **edit_menus.xml**). Definisce un menu fare clic su gestore che visualizza un avviso popup per indicare che è stato toccato l'icona di modifica. 

Compilare ed eseguire l'app. Quando si esegue l'app, verranno visualizzato il testo e icone aggiunte in precedenza come illustrato di seguito: 

[![Diagramma della parte inferiore della barra degli strumenti con icone di Taglia, copia e Incolla](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

Toccando il **Taglia** sull'icona del menu fa sì che il seguente avviso popup da visualizzare: 

[![Schermata di tipo avviso popup che indica che è stato toccato l'icona del menu Taglia](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

Toccare le voci di menu su una barra degli strumenti Visualizza l'avvisi popup risultante: 

[![Schermate di avvisi popup per salvare, copia e Incolla vengano utilizzate le voci di menu](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)



## <a name="the-up-button"></a>Pulsante 

La maggior parte delle App per Android si basano sul **nuovamente** pulsante per la navigazione delle app, premendo il **nuovamente** pulsante, l'utente passa alla schermata precedente.
Tuttavia, è anche possibile fornire un **backup** pulsante che rende più semplice per gli utenti possono spostarsi "massimo" alla schermata principale dell'applicazione. Quando l'utente seleziona il **backup** pulsante, l'utente sposta in alto a un livello superiore nella gerarchia di app &ndash; , ovvero l'app viene visualizzata l'utente torna più attività nello stack indietro anziché back POP per l'oggetto selezionato in precedenza Attività. 

Per abilitare il **backup** pulsante in una seconda attività che utilizza un `Toolbar` come la barra di azione, chiamare il `SetDisplayHomeAsUpEnabled` e `SetHomeButtonEnabled` metodi la seconda attività `OnCreate` metodo:

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

Il [supporta la barra degli strumenti v7](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/) nell'esempio di codice viene illustrato il **backup** pulsante di azione. Questo esempio, che utilizza la libreria delle applicazioni descritta di seguito, viene implementata una seconda attività che utilizza la barra degli strumenti **backup** pulsante per la navigazione gerarchica nuovamente per l'attività precedente. In questo esempio, il `DetailActivity` pulsante home consente il **backup** pulsante effettuando le operazioni seguenti `SupportActionBar` chiamate al metodo: 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

Quando l'utente passa da `MainActivity` a `DetailActivity`, `DetailActivity` consente di visualizzare un **backup** pulsante (freccia rivolta verso sinistra), come illustrato nella schermata:

[![Schermata di esempio di una pulsante a sinistra freccia nella barra degli strumenti](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

Se si tocca questo **backup** pulsante causa l'applicazione tornare alla `MainActivity`. In un'applicazione più complessa con più livelli della gerarchia, toccare il pulsante restituirà l'utente al successivo livello più elevato nell'app anziché alla schermata precedente. 



## <a name="related-links"></a>Collegamenti correlati

- [Il simbolo della barra degli strumenti (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra degli strumenti delle applicazioni (esempio)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
