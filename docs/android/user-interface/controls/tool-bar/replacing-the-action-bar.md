---
title: Sostituire la barra delle azioni
ms.topic: article
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: e71c6ea816b8b732d21148db32fd9395732dd4c0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="replacing-the-action-bar"></a>Sostituire la barra delle azioni


## <a name="overview"></a>Panoramica

Una delle più comuni viene utilizzato per il `Toolbar` consiste nel sostituire la barra delle azioni predefinito con un oggetto personalizzato `Toolbar` (quando viene creato un nuovo progetto Android, utilizza la barra delle azioni). Poiché il `Toolbar` offre la possibilità di aggiungere il logo personalizzato, titoli, voci di menu, pulsanti di navigazione e visualizzazioni personalizzate anche alla sezione barra app di un'attività dell'interfaccia utente, offre un significativo aggiornamento posizionato sopra la barra di azione predefinito.

Per sostituire una barra di azione predefinito di un'app con un `Toolbar`: 

1.  Creare un nuovo tema personalizzato e modificare le proprietà dell'app in modo che utilizzi il nuovo tema. 

2.  Disabilitare il `windowActionBar` attributo nel tema personalizzato e abilitare il `windowNoTitle` attributo.

3.  Definire un layout per il `Toolbar`.

4.  Includere il `Toolbar` layout dell'attività **axml** file di layout. 

5.  Aggiungere il codice dell'attività `OnCreate` metodo per individuare il `Toolbar` e chiamare `SetActionBar` per installare il `ToolBar` come la barra delle azioni.

Le sezioni seguenti illustrano in dettaglio il processo. Viene creata una semplice app e la barra delle azioni viene sostituito con un oggetto personalizzato `Toolbar`. 



## <a name="start-an-app-project"></a>Avviare un progetto di App

Creare un nuovo progetto Android denominato **ToolbarFun** (vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) per ulteriori informazioni sulla creazione di un nuovo progetto Android). Dopo aver creato il progetto, impostare i livelli di API Android minimo e di destinazione **Android 5.0 (API livello 21 - simbolo)**. Per ulteriori informazioni sull'impostazione dei livelli di versione di Android, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md). Quando l'app viene compilato ed eseguito, come illustrato in questa schermata viene visualizzato sulla barra delle azioni predefinite: 

[![Schermata della barra delle azioni predefinite](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)



## <a name="create-a-custom-theme"></a>Creare un tema personalizzato

Aprire il **risorse/valori** directory e creare un nuovo file denominato **Styles**. Sostituire il contenuto con il codice XML seguente: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="@android:style/Theme.Material.Light.DarkActionBar">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowActionBar">false</item>
    <item name="android:colorPrimary">#5A8622</item>
  </style>
</resources>
```

Questo codice XML definisce un nuovo tema personalizzato denominato **MyTheme** basato sul **Theme.Material.Light.DarkActionBar** tema nel simbolo. Il `windowNoTitle` attributo è impostato su `true` per nascondere la barra del titolo: 

```xml
<item name="android:windowNoTitle">true</item>
```

Per visualizzare la barra degli strumenti personalizzata, il valore predefinito `ActionBar` deve essere disabilitato: 

```xml
<item name="android:windowActionBar">false</item>
```

Un olive-green `colorPrimary` impostazione viene utilizzata per il colore di sfondo della barra degli strumenti: 
 
```xml
<item name="android:colorPrimary">#5A8622</item>
```

Modifica **Properties/AndroidManifest.xml** e aggiungere le seguenti `android:theme` attributo la `<application>` elemento in modo che l'app Usa la `MyTheme` tema personalizzato: 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

Per ulteriori informazioni sull'applicazione di un tema personalizzato a un'app, vedere [temi personalizzati utilizzando](~/android/user-interface/material-theme.md#customtheme). 



## <a name="define-a-toolbar-layout"></a>Definire un Layout della barra degli strumenti

Nel **risorse/layout** directory, creare un nuovo file denominato **toolbar.xml**. Sostituire il contenuto con il codice XML seguente: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?android:attr/actionBarSize"
    android:background="?android:attr/colorPrimary"
    android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"/>
```

Questo codice XML definisce personalizzata `Toolbar` che sostituisce la barra delle azioni predefinite. L'altezza minima del `Toolbar` è impostata sulle dimensioni della barra delle azioni che sostituisce: 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

Il colore di sfondo di `Toolbar` è impostato sul colore olive-green definito in precedenza nel **Styles**:

```csharp
android:background="?android:attr/colorPrimary"
```

A partire da, il simbolo di `android:theme` attributo può essere utilizzato per definire lo stile di una singola visualizzazione. Il `ThemeOverlay.Material` temi introdotti in simbolo consentono di sovrapposizione predefinito `Theme.Material` temi, sovrascrivendo gli attributi rilevanti per rendere chiaro o scuro. In questo esempio, il `Toolbar` Usa un tema scuro in modo che il relativo contenuto è un colore chiaro: 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

Questa impostazione viene utilizzata in modo che le voci di menu si differenzia il colore di sfondo scuro.



## <a name="include-the-toolbar-layout"></a>Includere il Layout della barra degli strumenti

Modificare il file di layout **Resources/layout/Main.axml** e sostituirne il contenuto con il codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
    <Button
        android:id="@+id/MyButton"
        android:layout_below="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Hello World, Click Me!" />
</RelativeLayout>
```

Questo layout è inclusa la `Toolbar` definito in **toolbar.xml** e utilizza un `RelativeLayout` per specificare che il `Toolbar` deve essere inserito nella parte superiore dell'interfaccia utente (sopra il pulsante). 



## <a name="find-and-activate-the-toolbar"></a>Trovare e attivare la barra degli strumenti

Modifica **Mainactivity** e aggiungere la seguente istruzione using:

```csharp
using Android.Views;
```

Inoltre, aggiungere le seguenti righe di codice alla fine del `OnCreate` metodo:

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

Questo codice viene cercato il `Toolbar` e chiama `SetActionBar` in modo che il `Toolbar` accetterà le caratteristiche della barra di azione predefinito. Il titolo della barra degli strumenti viene modificato in **personale sulla barra degli strumenti**. Come illustrato nell'esempio di codice, il `ToolBar` può fare riferimento direttamente come una barra di azione. Compilare ed eseguire questa app &ndash; personalizzata `Toolbar` viene visualizzato al posto di barra delle azioni predefinite: 

[![Schermata della barra degli strumenti personalizzata con lo schema di colore verde](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

Si noti che il `Toolbar` stile in modo indipendente dal `Theme.Material.Light.DarkActionBar` applicato al resto dell'app. 


 
## <a name="add-menu-items"></a>Aggiungere voci di Menu 

In questa sezione vengono aggiunti i menu di `Toolbar`. L'area superiore destra del `ToolBar` è riservato per le voci di menu &ndash; ogni voce di menu (detto anche un *azione*) consente di eseguire un'azione all'interno dell'attività corrente o è possibile eseguire un'azione per conto dell'intera applicazione. 

Per aggiungere i menu per il `Toolbar`: 

1.  Aggiungere le icone di menu (se richiesto) per il `mipmap-` cartelle di progetto di applicazione. Google fornisce un set di icone del menu disponibile nella [icone materiale](https://design.google.com/icons/) pagina. 

2.  Definire il contenuto delle voci di menu aggiungendo un nuovo file di risorse di menu in **risorse/menu**. 

3.  Implementare il `OnCreateOptionsMenu` metodo dell'attività &ndash; questo metodo ingrandisce le voci di menu. 

4.  Implementare il `OnOptionsItemSelected` metodo dell'attività &ndash; questo metodo esegue un'azione quando una voce di menu verrà scelti. 

Nelle sezioni seguenti illustrano in dettaglio il processo aggiungendo **modifica** e **salvare** voci di menu personalizzata `Toolbar`. 



### <a name="install-menu-icons"></a>Installare le icone di Menu

Continuare con la `ToolbarFun` applicazione di esempio, aggiungere le icone di menu al progetto di app. Scaricare [barra degli strumenti icons.zip](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons.zip?raw=true) e decomprimerlo. Copiare il contenuto dell'estratto *mipmap -* cartelle al progetto *mipmap -* cartelle **ToolbarFun/risorse** e includere ogni file di icona aggiunto nel progetto.


### <a name="define-a-menu-resource"></a>Definire una risorsa di Menu

Creare un nuovo **menu** sottodirectory **risorse**. Nel **menu** sottodirectory, creare un nuovo file di risorse di menu denominato **top_menus.xml** e sostituirne il contenuto con il codice XML seguente: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       android:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       android:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       android:showAsAction="never"
       android:title="Preferences" />
</menu>
```

Questo codice XML crea tre voci di menu:

-   Un **modifica** voce di menu che utilizza il `ic_action_content_create.png` icona (una matita). 

-   Oggetto **salvare** voce di menu che utilizza il `ic_action_content_save.png` icona (un disco floppy). 

-   Oggetto **preferenze** voce di menu che non dispone di un'icona.

Il `showAsAction` gli attributi del **modifica** e **salvare** voci di menu sono impostate su `ifRoom` &ndash; questa impostazione, queste voci di menu da visualizzare nel `Toolbar` se è presente spazio sufficiente per poter essere visualizzato. Il **preferenze** voce di menu imposta `showAsAction` per `never` &ndash; in questo modo il **preferenze** menu viene visualizzato nel *overflow* (tre menu punti verticali). 


### <a name="implement-oncreateoptionsmenu"></a>Implementare OnCreateOptionsMenu

Aggiungere il seguente metodo alla **Mainactivity**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Chiamate Android di `OnCreateOptionsMenu` metodo in modo che l'app può specificare la risorsa di menu per un'attività. In questo metodo, il **top_menus.xml** risorse viene ingrandita in passato `menu`. Questo codice genera il nuovo **modifica**, **salvare**, e **preferenze** voci di menu da visualizzare nel `Toolbar`. 



### <a name="implement-onoptionsitemselected"></a>Implementare OnOptionsItemSelected

Aggiungere il seguente metodo alla **Mainactivity**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

Quando un utente digita una voce di menu, Android chiama il `OnOptionsItemSelected` metodo e passa la voce di menu selezionata. In questo esempio, l'implementazione consente semplicemente di visualizzare un avviso popup per indicare la voce di menu viene toccata. 

Compilare ed eseguire `ToolbarFun` per visualizzare le nuove voci di menu nella barra degli strumenti. Il `Toolbar` ora visualizza tre icone del menu come illustrato in questo screenshot: 

[![Diagramma che illustra i percorsi di modifica, Salva e voci di menu di Overflow](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

Quando un'utente le scelte di **modifica** voce di menu, un avviso popup visualizzata per indicare che il `OnOptionsItemSelected` chiamata al metodo: 

[![Schermata di tipo avviso popup visualizzata quando verrà scelti modificare un elemento](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

Quando un utente tocca il menu di overflow, il **preferenze** voce di menu viene visualizzata. In genere, meno comune azioni devono risiedere nel menu di overflow &ndash; questo esempio viene utilizzato il menu di overflow per **preferenze** perché non è più spesso utilizzato come **modifica** e  **Salvare**: 

[![Schermata di preferenze menu che viene visualizzato nel menu di Overflow](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

Per ulteriori informazioni sui menu Android, vedere Android Developer [menu](https://developer.android.com/guide/topics/ui/menus.html) argomento. 
 



## <a name="related-links"></a>Collegamenti correlati

- [Il simbolo della barra degli strumenti (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra degli strumenti delle applicazioni (esempio)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
