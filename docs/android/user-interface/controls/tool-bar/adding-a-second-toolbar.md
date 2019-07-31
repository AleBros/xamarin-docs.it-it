---
title: Aggiunta di una seconda barra degli strumenti
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 5d2fec537f10ad3ef5300275c9851d4f57bc961d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645164"
---
# <a name="adding-a-second-toolbar"></a>Aggiunta di una seconda barra degli strumenti


## <a name="overview"></a>Panoramica 

Il `Toolbar` può eseguire più di sostituire la barra &ndash; delle azioni che può essere usata più volte all'interno di un'attività, può essere personalizzata per la selezione host in qualsiasi posizione sullo schermo e può essere configurata in modo da estendersi solo a una larghezza parziale dello schermo. Gli esempi seguenti illustrano come creare un secondo `Toolbar` e posizionarlo nella parte inferiore della schermata. Implementa le voci di menu **copia**, **taglia**e **Incolla.** `Toolbar` 


## <a name="define-the-second-toolbar"></a>Definire la seconda barra degli strumenti 

Modificare il file di layout **Main. aXML** e sostituirne il contenuto con con il codice XML seguente:

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

Questo XML aggiunge un secondo `Toolbar` alla parte inferiore dello schermo con un riempimento vuoto `ImageView` al centro dello schermo. L'altezza di questo `Toolbar` oggetto è impostata sull'altezza di una barra delle azioni: 

```xml
android:minHeight="?android:attr/actionBarSize"
```

Il colore di sfondo di `Toolbar` questo oggetto è impostato su un colore d'accento che verrà definito successivamente:

```xml
android:background="?android:attr/colorAccent
```

Si noti che `Toolbar` questo si basa su un tema diverso (**ThemeOverlay. Material. Dark. ActionBar**) rispetto a `Toolbar` quello usato dal creato in sostituendo [il barra delle azioni](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash; non è associato alla decorazione della finestra dell'attività o a tema utilizzato nella prima `Toolbar`.

Modificare **Resources/values. XML** e aggiungere il colore dell'accento seguente alla definizione dello stile: 

```xml
<item name="android:colorAccent">#C7A935</item>
```

In questo modo la barra degli strumenti inferiore restituisce un colore ambra scuro. La compilazione e l'esecuzione dell'app visualizza una seconda barra degli strumenti vuota nella parte inferiore della schermata: 

[![Screenshot dell'app con la seconda barra degli strumenti gialla nella parte inferiore della schermata](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)


 
## <a name="add-edit-menu-items"></a>Aggiungi voci di menu modifica 

In questa sezione viene illustrato come aggiungere voci di menu modifica in `Toolbar`basso. 

Per aggiungere voci di menu a un `Toolbar`database secondario: 

1.  Aggiungere le `mipmap-` icone del menu alle cartelle del progetto dell'app, se necessario.

2.  Definire il contenuto delle voci di menu aggiungendo un file di risorse di menu aggiuntivo a **risorse/menu**. 

3.  Nel `OnCreate` metodo dell'attività, `Toolbar` trovare (chiamando `FindViewById`) e inflatare i `Toolbar`menu di.

4.  Implementare un gestore di clic `OnCreate` in per le nuove voci di menu. 

Le sezioni seguenti illustrano questo processo in modo dettagliato: Le voci di menu **taglia**, **copia**e **Incolla** vengono aggiunte alla fine `Toolbar`. 



### <a name="define-the-edit-menu-resource"></a>Definire la risorsa menu modifica

Nella sottodirectory Resources **/menu** creare un nuovo file XML denominato **edit_menus. XML** e sostituire il contenuto con il codice XML seguente:

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

Questo codice XML crea le voci di menu **taglia**, **copia**e **Incolla** (usando le icone aggiunte alle `mipmap-` cartelle in [sostituzione del barra delle azioni](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).



### <a name="inflate-the-menus"></a>Ingrandire i menu

Alla fine del `OnCreate` metodo in **MainActivity.cs**aggiungere le righe di codice seguenti: 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

Questo `edit_toolbar` codice individua la vista definita in **Main. aXML**, ne imposta il titolo per la **modifica**e le voci di menu (definite in **edit_menus. XML**). Definisce un gestore di clic di menu che visualizza un avviso popup per indicare quale icona di modifica è stata toccata. 

Compilare ed eseguire l'app. Quando viene eseguita l'app, il testo e le icone aggiunti sopra verranno visualizzati come illustrato di seguito: 

[![Diagramma della barra degli strumenti inferiore con icone taglia, copia e incolla](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

Se si tocca l'icona del menu **taglia** , verrà visualizzato il seguente avviso popup: 

[![Screenshot del popup che indica che l'icona del menu taglia è stata toccata](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

Toccando le voci di menu su una delle barre degli strumenti vengono visualizzati i toast risultanti: 

[![Screenshot dei popup per le voci di menu Salva, copia e incolla da toccare](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)



## <a name="the-up-button"></a>Pulsante su 

La maggior parte delle app Android si basa sul pulsante **indietro** per la navigazione delle app; Quando si preme il pulsante **indietro** , l'utente viene visualizzato nella schermata precedente.
Tuttavia, è anche possibile fornire un pulsante **up** che consente agli utenti di spostarsi più facilmente nella schermata principale dell'app. Quando l'utente seleziona il pulsante **verso l'alto** , l'utente passa a un livello superiore nella gerarchia &ndash; delle app, ovvero l'app estrae più attività nello stack indietro anziché riportarle nell'attività precedentemente visitata. 

Per abilitare il pulsante **freccia su** in una seconda attività che usa `Toolbar` un come barra delle azioni, chiamare `SetDisplayHomeAsUpEnabled` i `SetHomeButtonEnabled` metodi `OnCreate` e nel metodo della seconda attività:

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

Nell'esempio di codice della [barra degli strumenti del supporto V7](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar) viene illustrato il pulsante **su** in azione. Questo esempio (che usa la libreria AppCompat descritta di seguito) implementa una seconda attività che usa il pulsante di spostamento verso l' **alto** per la navigazione gerarchica all'attività precedente. In questo esempio, il `DetailActivity` pulsante Home Abilita il pulsante **verso l'alto** effettuando `SupportActionBar` le chiamate al metodo seguenti: 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

Quando l'utente passa `MainActivity` da a `DetailActivity`, `DetailActivity` Visualizza un pulsante **su** (freccia rivolta verso sinistra), come illustrato nello screenshot:

[![Schermata di esempio di una freccia su a sinistra della barra degli strumenti](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

Toccando questo pulsante **in alto** , l'app tornerà a `MainActivity`. In un'app più complessa con più livelli di gerarchia, toccando questo pulsante, l'utente verrà restituito al livello più alto successivo nell'app anziché alla schermata precedente. 



## <a name="related-links"></a>Collegamenti correlati

- [Barra degli strumenti Lollipop (Sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra degli strumenti AppCompat (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
