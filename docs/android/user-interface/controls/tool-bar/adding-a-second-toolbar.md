---
title: Aggiunta di una seconda barra degli strumenti
ms.prod: xamarin
ms.assetid: FCE0AD27-8B6B-47C6-AD19-2B1C12E1BBBF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 43030d4a221ceadf68c30df2e37449e7f996fa6d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029110"
---
# <a name="adding-a-second-toolbar"></a>Aggiunta di una seconda barra degli strumenti

## <a name="overview"></a>Panoramica 

Il `Toolbar` può fare più di sostituire la barra delle azioni &ndash; può essere usata più volte all'interno di un'attività, può essere personalizzata per la selezione host in qualsiasi punto dello schermo e può essere configurata in modo da estendersi solo a una larghezza parziale dello schermo. Gli esempi seguenti illustrano come creare una seconda `Toolbar` e posizionarla nella parte inferiore della schermata. Questo `Toolbar` implementa le voci di menu **copia**, **taglia**e **Incolla** . 

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

Il codice XML aggiunge una seconda `Toolbar` alla parte inferiore dello schermo con una `ImageView` vuota che riempie la parte centrale dello schermo. L'altezza di questo `Toolbar` è impostata sull'altezza di una barra delle azioni: 

```xml
android:minHeight="?android:attr/actionBarSize"
```

Il colore di sfondo di questo `Toolbar` è impostato su un colore dell'accento che verrà definito successivamente:

```xml
android:background="?android:attr/colorAccent
```

Si noti che questo `Toolbar` si basa su un tema diverso (**ThemeOverlay. Material. Dark. ActionBar**) rispetto a quello usato dall'`Toolbar` creato nella [sostituzione del barra delle azioni](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md) &ndash; non è associato alla decorazione della finestra dell'attività o al tema usato in primo `Toolbar`.

Modificare **Resources/values. XML** e aggiungere il colore dell'accento seguente alla definizione dello stile: 

```xml
<item name="android:colorAccent">#C7A935</item>
```

In questo modo la barra degli strumenti inferiore restituisce un colore ambra scuro. La compilazione e l'esecuzione dell'app visualizza una seconda barra degli strumenti vuota nella parte inferiore della schermata: 

[![screenshot dell'app con la seconda barra degli strumenti gialla nella parte inferiore della schermata](adding-a-second-toolbar-images/01-second-toolbar-sml.png)](adding-a-second-toolbar-images/01-second-toolbar.png#lightbox)

## <a name="add-edit-menu-items"></a>Aggiungi voci di menu modifica 

In questa sezione viene illustrato come aggiungere voci di menu modifica alla `Toolbar`inferiore. 

Per aggiungere voci di menu a un `Toolbar`secondario: 

1. Aggiungere le icone di menu alle cartelle `mipmap-` del progetto dell'app, se necessario.

2. Definire il contenuto delle voci di menu aggiungendo un file di risorse di menu aggiuntivo a **risorse/menu**. 

3. Nel metodo di `OnCreate` dell'attività trovare il `Toolbar` (chiamando `FindViewById`) e ingrandire i menu `Toolbar`.

4. Implementare un gestore di clic in `OnCreate` per le nuove voci di menu. 

Le sezioni seguenti illustrano questo processo in modo dettagliato: le voci di menu **taglia**, **copia**e **Incolla** vengono aggiunte alla `Toolbar`inferiore. 

### <a name="define-the-edit-menu-resource"></a>Definire la risorsa menu modifica

Nella sottodirectory **Resources/menu** creare un nuovo file XML denominato **edit_menus. XML** e sostituire il contenuto con il codice XML seguente:

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

Questo codice XML crea le voci di menu **taglia**, **copia**e **Incolla** (usando le icone aggiunte alle cartelle `mipmap-` in [sostituzione del barra delle azioni](~/android/user-interface/controls/tool-bar/replacing-the-action-bar.md)).

### <a name="inflate-the-menus"></a>Ingrandire i menu

Alla fine del metodo `OnCreate` in **MainActivity.cs**aggiungere le righe di codice seguenti: 

```csharp
var editToolbar = FindViewById<Toolbar>(Resource.Id.edit_toolbar);
editToolbar.Title = "Editing";
editToolbar.InflateMenu (Resource.Menu.edit_menus);
editToolbar.MenuItemClick += (sender, e) => {
    Toast.MakeText(this, "Bottom toolbar tapped: " + e.Item.TitleFormatted, ToastLength.Short).Show();
};
```

Questo codice individua la visualizzazione `edit_toolbar` definita in **Main. aXML**, ne imposta il titolo sulla **modifica**e le voci di menu (definite in **edit_menus. XML**). Definisce un gestore di clic di menu che visualizza un avviso popup per indicare quale icona di modifica è stata toccata. 

Compilare ed eseguire l'app. Quando viene eseguita l'app, il testo e le icone aggiunti sopra verranno visualizzati come illustrato di seguito: 

[Diagramma![della barra degli strumenti inferiore con icone taglia, copia e incolla](adding-a-second-toolbar-images/02-bottom-toolbar-sml.png)](adding-a-second-toolbar-images/02-bottom-toolbar.png#lightbox)

Se si tocca l'icona del menu **taglia** , verrà visualizzato il seguente avviso popup: 

[![screenshot del popup che indica che l'icona del menu taglia è stata toccata](adding-a-second-toolbar-images/03-bottom-tapped-sml.png)](adding-a-second-toolbar-images/03-bottom-tapped.png#lightbox)

Toccando le voci di menu su una delle barre degli strumenti vengono visualizzati i toast risultanti: 

[![screenshot dei popup per le voci di menu Salva, copia e incolla da toccare](adding-a-second-toolbar-images/04-menu-action-sml.png)](adding-a-second-toolbar-images/04-menu-action.png#lightbox)

## <a name="the-up-button"></a>Pulsante su 

La maggior parte delle app Android si basa sul pulsante **indietro** per la navigazione delle app; Quando si preme il pulsante **indietro** , l'utente viene visualizzato nella schermata precedente.
Tuttavia, è anche possibile fornire un pulsante **up** che consente agli utenti di spostarsi più facilmente nella schermata principale dell'app. Quando l'utente seleziona il pulsante **verso l'alto** , l'utente passa a un livello superiore nella gerarchia dell'app &ndash; ovvero, l'app estrae più attività nello stack indietro anziché riportarle nell'attività precedentemente visitata. 

Per abilitare il **pulsante di** scorrimento in una seconda attività che utilizza un `Toolbar` come barra delle azioni, chiamare i metodi `SetDisplayHomeAsUpEnabled` e `SetHomeButtonEnabled` nel metodo `OnCreate` della seconda attività:

```csharp
SetActionBar (toolbar);
...
ActionBar.SetDisplayHomeAsUpEnabled (true);
ActionBar.SetHomeButtonEnabled (true);
```

Nell'esempio di codice della [barra degli strumenti del supporto V7](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar) viene illustrato il pulsante **su** in azione. Questo esempio (che usa la libreria AppCompat descritta di seguito) implementa una seconda attività che usa il pulsante di spostamento verso l' **alto** per la navigazione gerarchica all'attività precedente. In questo esempio, il pulsante `DetailActivity` Home consente di abilitare il pulsante **verso l'alto** effettuando le seguenti chiamate al metodo `SupportActionBar`: 

```csharp
SetSupportActionBar (toolbar);
...
SupportActionBar.SetDisplayHomeAsUpEnabled (true);
SupportActionBar.SetHomeButtonEnabled (true);
```

Quando l'utente passa da `MainActivity` a `DetailActivity`, il `DetailActivity` Visualizza un pulsante **su** (freccia rivolta verso sinistra), come illustrato nello screenshot:

[![screenshot esempio di una freccia su a sinistra della barra degli strumenti](adding-a-second-toolbar-images/05-up-button-sml.png)](adding-a-second-toolbar-images/05-up-button.png#lightbox)

Toccando questo pulsante **in alto** , l'app tornerà a `MainActivity`. In un'app più complessa con più livelli di gerarchia, toccando questo pulsante, l'utente verrà restituito al livello più alto successivo nell'app anziché alla schermata precedente. 

## <a name="related-links"></a>Collegamenti correlati

- [Barra degli strumenti Lollipop (Sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra degli strumenti AppCompat (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
