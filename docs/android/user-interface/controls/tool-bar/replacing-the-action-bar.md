---
title: Sostituzione della Barra delle azioni
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/27/2018
ms.openlocfilehash: 9e9fa1e2651661670f89baac7fcd438b3d14bfb3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61200917"
---
# <a name="replacing-the-action-bar"></a>Sostituzione della Barra delle azioni

## <a name="overview"></a>Panoramica

Uno dei più comuni utilizzato per il `Toolbar` consiste nel sostituire la barra delle azioni predefinita con una classe personalizzata `Toolbar` (quando viene creato un nuovo progetto Android, Usa la barra delle azioni predefinita). Poiché il `Toolbar` offre la possibilità di aggiungere il logo personalizzato, titoli, voci di menu, pulsanti di navigazione e visualizzazioni personalizzate anche per la sezione sulla barra dell'app di un'attività dell'interfaccia utente, offre un aggiornamento importante tramite la barra delle azioni predefinite.

Per sostituire barra delle azioni predefinita di un'app con un `Toolbar`: 

1.  Creare un nuovo tema personalizzato e modificare le proprietà dell'app in modo che utilizzi il nuovo tema. 

2.  Disabilitare il `windowActionBar` dell'attributo del tema personalizzato e abilitare il `windowNoTitle` attributo.

3.  Definire un layout per il `Toolbar`.

4.  Includere il `Toolbar` layout dell'attività **Main. axml** file di layout. 

5.  Aggiungere il codice dell'attività `OnCreate` metodo per individuare il `Toolbar` e chiamare `SetActionBar` per installare il `ToolBar` come barra delle azioni.

Le sezioni seguenti illustrano questo processo in dettaglio. Viene creata una semplice app e la barra delle azioni viene sostituita con un oggetto personalizzato `Toolbar`. 



## <a name="start-an-app-project"></a>Avviare un progetto di App

Creare un nuovo progetto Android chiamato **ToolbarFun** (vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) per altre informazioni sulla creazione di un nuovo progetto Android). Dopo aver creato il progetto, impostare i livelli API Android minima e di destinazione **Android 5.0 (API livello 21 - Lollipop)** o versione successiva. Per altre informazioni sull'impostazione dei livelli di versione di Android, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md). Quando l'app viene compilato ed eseguito, viene visualizzato sulla barra delle azioni predefinita come illustrato in questo screenshot:

[![Screenshot della barra delle azioni predefinite](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)



## <a name="create-a-custom-theme"></a>Creare un tema personalizzato

Aprire il **e i valori per le risorse** directory e creare un nuovo file denominato **Styles**. Sostituirne il contenuto con il codice XML seguente: 

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

Questo XML definisce un nuovo tema personalizzato chiamato **MyTheme** basato sulle **Theme.Material.Light.DarkActionBar** tema in Lollipop. Il `windowNoTitle` attributo è impostato su `true` per nascondere la barra del titolo: 

```xml
<item name="android:windowNoTitle">true</item>
```

Per visualizzare la barra degli strumenti personalizzata, il valore predefinito `ActionBar` deve essere disabilitato: 

```xml
<item name="android:windowActionBar">false</item>
```

Un olive-green `colorPrimary` impostazione viene usata per il colore di sfondo della barra degli strumenti: 
 
```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>Applicare il tema personalizzato

Modificare **Properties/AndroidManifest.xml** e aggiungere quanto segue `android:theme` attributo per il `<application>` elemento in modo che l'app Usa il `MyTheme` tema personalizzato: 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

Per altre informazioni sull'applicazione di un tema personalizzato a un'app, vedere [temi personalizzati usando](~/android/user-interface/material-theme.md#customtheme). 



## <a name="define-a-toolbar-layout"></a>Definire un Layout della barra degli strumenti

Nel **risorse/layout** directory, creare un nuovo file denominato **toolbar.xml**. Sostituirne il contenuto con il codice XML seguente: 

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

Questo XML definisce l'oggetto personalizzato `Toolbar` che sostituisce la barra delle azioni predefinite. L'altezza minima del `Toolbar` viene impostata la dimensione della barra delle azioni che sostituisce: 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

Il colore di sfondo di `Toolbar` è impostato sul colore olive-green definito in precedenza nella **Styles. XML**:

```csharp
android:background="?android:attr/colorPrimary"
```

A partire da Lollipop, il `android:theme` attributo può essere utilizzato per applicare uno stile a una singola visualizzazione. Il `ThemeOverlay.Material` introdotti in Lollipop i temi consentono di sovrapporre il valore predefinito `Theme.Material` temi, sovrascrivendo gli attributi rilevanti per fare in modo chiaro o scuro. In questo esempio, il `Toolbar` Usa un tema scuro in modo che il relativo contenuto sia un colore chiaro: 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

Questa impostazione viene usata in modo che le voci di menu si differenzia il colore di sfondo più scuro.



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
</RelativeLayout>
```

Questo layout è inclusa la `Toolbar` definito in **toolbar.xml** e viene utilizzato un `RelativeLayout` per specificare che il `Toolbar` deve essere collocato nella parte superiore dell'interfaccia utente (vedere sopra il pulsante). 



## <a name="find-and-activate-the-toolbar"></a>Trovare e attivare la barra degli strumenti

Modificare **MainActivity.cs** e aggiungere la seguente istruzione using:

```csharp
using Android.Views;
```

Inoltre, aggiungere le righe di codice seguenti alla fine del `OnCreate` metodo:

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

Questo codice vengono trovati i `Toolbar` e chiama `SetActionBar` in modo che il `Toolbar` richiederà sulle caratteristiche di barra di azione predefinito. Il titolo della barra degli strumenti viene modificato in **sulla barra degli strumenti personali**. Come illustrato nell'esempio di codice, il `ToolBar` può fare riferimento direttamente come una barra delle azioni. Compilare ed eseguire l'app &ndash; personalizzata `Toolbar` viene visualizzato al posto di barra delle azioni predefinita: 

[![Screenshot della barra degli strumenti personalizzata con lo schema di colore verde](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

Si noti che il `Toolbar` è ora uno stile in modo indipendente il `Theme.Material.Light.DarkActionBar` tema applicato al resto dell'app. 

Se si verifica un'eccezione durante l'esecuzione dell'app, vedere la [Troubleshooting](#troubleshooting) sezione riportata di seguito.

 
## <a name="add-menu-items"></a>Aggiungere le voci di Menu 

In questa sezione, i menu vengono aggiunti al `Toolbar`. Angolo superiore destro del `ToolBar` è riservato per le voci di menu &ndash; ogni voce di menu (detto anche un' *intervento*) può eseguire un'azione all'interno dell'attività corrente o è possibile eseguire un'azione per conto dell'intera applicazione. 

Per aggiungere menu e le `Toolbar`: 

1.  Aggiungere le icone di menu (se richiesto) per il `mipmap-` cartelle di progetto dell'app. Google fornisce un set di icone del menu disponibile nella [icone Material](https://design.google.com/icons/) pagina. 

2.  Definire il contenuto delle voci di menu aggiungendo un nuovo file di risorse di menu sotto **risorse/menu**. 

3.  Implementare il `OnCreateOptionsMenu` metodo dell'attività &ndash; questo metodo aumenta le voci di menu. 

4.  Implementare il `OnOptionsItemSelected` metodo dell'attività &ndash; questo metodo esegue un'azione quando toccando una voce di menu. 

Le sezioni seguenti descrivono questo processo in dettaglio aggiungendo **Edit** e **salvare** voci di menu per l'oggetto personalizzato `Toolbar`. 



### <a name="install-menu-icons"></a>Installare le icone di Menu

Continuando con il `ToolbarFun` app di esempio, aggiungere le icone di menu per il progetto dell'app. Scaricare [icone sulla barra degli strumenti](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true)decomprimere e copiare il contenuto dell'estratto *mipmap -* cartelle per il progetto *mipmap -* cartelle sotto **ToolbarFun / Risorse** e includere ogni file di icona aggiunta nel progetto.


### <a name="define-a-menu-resource"></a>Definire una risorsa di Menu

Creare una nuova **dal menu** sottodirectory **risorse**. Nel **dal menu** sottodirectory, creare un nuovo file di risorse di menu denominato **top_menus.xml** e sostituirne il contenuto con il codice XML seguente: 

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

-   Un' **Edit** voce di menu che viene utilizzato il `ic_action_content_create.png` icona (una matita). 

-   Oggetto **salvare** voce di menu che viene utilizzato il `ic_action_content_save.png` icona (un disco floppy). 

-   Oggetto **preferenze** voce di menu che non ha un'icona.

Il `showAsAction` attributi del **modificare** e **salvare** voci di menu sono impostate su `ifRoom` &ndash; questa impostazione fa in modo che queste voci di menu da visualizzare nel `Toolbar` se è presente spazio sufficiente per poter essere visualizzato. Il **preferenze** set di elementi di menu `showAsAction` a `never` &ndash; in questo modo, il **preferenze** menu viene visualizzato nei *overflow* menu (tre punti verticali). 


### <a name="implement-oncreateoptionsmenu"></a>Implementare OnCreateOptionsMenu

Aggiungere il metodo seguente alla **MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android chiama il `OnCreateOptionsMenu` metodo in modo che l'app può specificare la risorsa di menu per un'attività. In questo metodo, il **top_menus.xml** risorsa viene sottoposto a inflating nell'oggetto passato `menu`. Questo codice fa in modo che il nuovo **Edit**, **salvare**, e **preferenze** voci di menu da visualizzare nel `Toolbar`. 



### <a name="implement-onoptionsitemselected"></a>Implementare OnOptionsItemSelected

Aggiungere il metodo seguente alla **MainActivity.cs**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

Quando un utente tocca una voce di menu, Android chiama il `OnOptionsItemSelected` metodo e passa la voce di menu selezionata. In questo esempio, l'implementazione consente semplicemente di visualizzare un avviso popup per indicare la voce di menu viene toccata. 

Compilare ed eseguire `ToolbarFun` per visualizzare le nuove voci di menu sulla barra degli strumenti. Il `Toolbar` ora visualizza tre icone del menu, come illustrato in questo screenshot: 

[![Diagramma che illustra i percorsi di modifica, Salva e voci di menu di Overflow](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

Quando un utente tocca il **Edit** verrà visualizzata la voce di menu, un avviso popup per indicare che il `OnOptionsItemSelected` chiamata al metodo: 

[![Schermata di tipo avviso popup visualizzata quando viene toccato Modifica elemento](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

Quando un utente tocca il menu di overflow, il **preferenze** voce di menu viene visualizzata. In genere, meno comuni azioni devono essere inserite nel menu di overflow &ndash; in questo esempio Usa il menu di overflow **preferenze** perché non viene usato più spesso come **modificare** e  **Salvare**: 

[![Voce di menu schermata di preferenze che viene visualizzato nel menu di Overflow](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

Per altre informazioni sui menu Android, vedere l'Android Developer [menu](https://developer.android.com/guide/topics/ui/menus.html) argomento. 
 

## <a name="troubleshooting"></a>Risoluzione dei problemi

I suggerimenti seguenti possono aiutare a eseguire il debug di problemi che possono verificarsi durante la sostituzione sulla barra delle azioni con una barra degli strumenti.

### <a name="activity-already-has-an-action-bar"></a>Attività esiste già una barra delle azioni

Se l'app non è configurata correttamente per l'uso di un tema personalizzato come descritto in [applicare il tema personalizzato](#apply-the-custom-theme), la seguente eccezione può verificarsi durante l'esecuzione dell'app:

![Errore che può verificarsi quando non viene usato un tema personalizzato](replacing-the-action-bar-images/03-theme-not-defined.png)

Inoltre, può essere prodotto un messaggio di errore simile al seguente: _Java.Lang.IllegalStateException: Questa attività dispone già di una barra delle azioni fornita per la finestra di progettazione._ 

Per correggere questo errore, verificare che il `android:theme` dell'attributo per il tema personalizzato viene aggiunto a `<application>` (nella **Properties/AndroidManifest.xml**) come descritto precedentemente in [applicare il tema personalizzato](#apply-the-custom-theme). Inoltre, questo errore potrebbe essere causato se il `Toolbar` layout o tema personalizzato non è configurato correttamente.


## <a name="related-links"></a>Collegamenti correlati

- [Barra degli strumenti lollipop (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra degli strumenti AppCompat (esempio)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
