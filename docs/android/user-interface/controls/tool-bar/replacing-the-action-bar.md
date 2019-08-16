---
title: Sostituzione della Barra delle azioni
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/27/2018
ms.openlocfilehash: d59acaec36c63c62fe934f145d06bfbb78d11f6c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522865"
---
# <a name="replacing-the-action-bar"></a>Sostituzione della Barra delle azioni

## <a name="overview"></a>Panoramica

Uno degli usi più comuni di `Toolbar` è quello di sostituire la barra delle azioni predefinita con una personalizzata `Toolbar` (quando viene creato un nuovo progetto Android, usa la barra delle azioni predefinita). Dato che `Toolbar` offre la possibilità di aggiungere logo, titoli, voci di menu, pulsanti di navigazione e persino visualizzazioni personalizzate alla sezione della barra delle applicazioni dell'interfaccia utente di un'attività, offre un aggiornamento significativo sulla barra delle azioni predefinita.

Per sostituire la barra delle azioni predefinita di un'app `Toolbar`con: 

1. Creare un nuovo tema personalizzato e modificare le proprietà dell'app in modo che usi questo nuovo tema. 

2. Disabilitare l' `windowActionBar` attributo nel tema personalizzato e abilitare l' `windowNoTitle` attributo.

3. Definire un layout per `Toolbar`.

4. Includere il `Toolbar` layout nel file di layout **Main. aXML** dell'attività. 

5. `OnCreate` Aggiungere il codice al metodo dell'attività per individuare la `Toolbar` chiamata `SetActionBar` a `ToolBar` e per installare come barra delle azioni.

Le sezioni seguenti illustrano in dettaglio questo processo. Viene creata un'app semplice e la relativa barra delle azioni viene sostituita `Toolbar`con una personalizzata. 



## <a name="start-an-app-project"></a>Avviare un progetto di app

Creare un nuovo progetto Android denominato **ToolbarFun** (per altre informazioni sulla creazione di un nuovo progetto Android [, vedere Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) ). Dopo aver creato questo progetto, impostare i livelli di API di destinazione e minimo dell'API Android su **android 5,0 (API level 21-Lollipop)** o versione successiva. Per altre informazioni sull'impostazione dei livelli di versione di Android, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md). Quando l'app viene compilata ed eseguita, Visualizza la barra delle azioni predefinita, come illustrato in questo screenshot:

[![Screenshot della barra delle azioni predefinita](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)



## <a name="create-a-custom-theme"></a>Creare un tema personalizzato

Aprire la directory Resources **/values** e creare un nuovo file denominato **Styles. XML**. Sostituire il contenuto con il codice XML seguente: 

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

Questo codice XML definisce un nuovo tema personalizzato , denominato in base al tema **. Material. Light. DarkActionBar** , in Lollipop. L' `windowNoTitle` attributo è impostato su `true` per nascondere la barra del titolo: 

```xml
<item name="android:windowNoTitle">true</item>
```

Per visualizzare la barra degli strumenti personalizzata, `ActionBar` è necessario disabilitare l'impostazione predefinita: 

```xml
<item name="android:windowActionBar">false</item>
```

Viene utilizzata un'impostazione `colorPrimary` verde oliva per il colore di sfondo della barra degli strumenti: 
 
```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>Applicare il tema personalizzato

Modificare **Properties/file AndroidManifest. XML** e aggiungere l'attributo `android:theme` seguente all' `<application>` elemento in modo che l'app usi il `MyTheme` tema personalizzato: 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

Per altre informazioni sull'applicazione di un tema personalizzato a un'app, vedere [uso dei temi personalizzati](~/android/user-interface/material-theme.md#customtheme). 



## <a name="define-a-toolbar-layout"></a>Definire un layout della barra degli strumenti

Nella directory **Resources/layout** creare un nuovo file denominato **Toolbar. XML**. Sostituire il contenuto con il codice XML seguente: 

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

Questo XML definisce l'oggetto `Toolbar` personalizzato che sostituisce la barra delle azioni predefinita. L'altezza minima di `Toolbar` è impostata sulla dimensione della barra delle azioni che sostituisce: 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

Il colore di sfondo di `Toolbar` è impostato sul colore verde oliva definito in precedenza in **Styles. XML**:

```csharp
android:background="?android:attr/colorPrimary"
```

A partire da Lollipop, `android:theme` l'attributo può essere usato per applicare uno stile a una singola visualizzazione. I `ThemeOverlay.Material` temi introdotti in Lollipop consentono di sovrapporre i temi `Theme.Material` predefiniti, sovrascrivendo gli attributi rilevanti per renderli chiaro o scuro. In questo esempio, `Toolbar` usa un tema scuro, in modo che il relativo contenuto sia chiaro a colori: 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

Questa impostazione viene utilizzata in modo che le voci di menu contrastino con il colore di sfondo più scuro.



## <a name="include-the-toolbar-layout"></a>Includi il layout della barra degli strumenti

Modificare il file di layout Resources **/layout/Main. aXML** e sostituirne il contenuto con il codice XML seguente:

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

Questo layout include l' `Toolbar` oggetto definito in **Toolbar. XML** e USA `RelativeLayout` un oggetto per specificare `Toolbar` che deve essere inserito nella parte superiore dell'interfaccia utente (sopra il pulsante). 



## <a name="find-and-activate-the-toolbar"></a>Trovare e attivare la barra degli strumenti

Modificare **MainActivity.cs** e aggiungere l'istruzione using seguente:

```csharp
using Android.Views;
```

Aggiungere inoltre le seguenti righe di codice alla fine del `OnCreate` metodo:

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

Questo codice trova le `Toolbar` chiamate `SetActionBar` e in modo che `Toolbar` l'oggetto prenda le caratteristiche predefinite della barra delle azioni. Il titolo della barra degli strumenti viene modificato in **barra degli strumenti**. Come illustrato in questo esempio di codice, `ToolBar` è possibile fare riferimento direttamente a come una barra delle azioni. Compila ed Esegui questa app &ndash; l'oggetto `Toolbar` personalizzato viene visualizzato al posto della barra delle azioni predefinita: 

[![Screenshot della barra degli strumenti personalizzata con la combinazione di colori verde](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

Si noti che `Toolbar` l'oggetto è con stile indipendentemente `Theme.Material.Light.DarkActionBar` dal tema applicato al resto dell'applicazione. 

Se si verifica un'eccezione durante l'esecuzione dell'app, vedere la sezione relativa alla [risoluzione dei problemi](#troubleshooting) di seguito.

 
## <a name="add-menu-items"></a>Aggiungi voci di menu 

In questa sezione vengono aggiunti i `Toolbar`menu a. L'area superiore destra del `ToolBar` è riservata per le voci &ndash; di menu ogni voce di menu (detta anche *elemento azione*) può eseguire un'azione all'interno dell'attività corrente o eseguire un'azione per conto dell'intera app. 

Per aggiungere menu a `Toolbar`: 

1. Aggiungere le `mipmap-` icone del menu (se necessario) alle cartelle del progetto dell'app. Google fornisce un set di icone di menu gratuite nella pagina [Icone materiali](https://design.google.com/icons/) . 

2. Definire il contenuto delle voci di menu aggiungendo un nuovo file di risorse di menu in **risorse/menu**. 

3. Implementare il `OnCreateOptionsMenu` metodo dell'attività &ndash; . questo metodo consente di ingrandire le voci di menu. 

4. Implementare il `OnOptionsItemSelected` metodo dell'attività &ndash; questo metodo esegue un'azione quando viene toccata una voce di menu. 

Le sezioni seguenti illustrano questo processo in modo dettagliato aggiungendo le voci di menu **modifica** e **Salva** al personalizzato `Toolbar`. 



### <a name="install-menu-icons"></a>Icone del menu Installa

Continuando con `ToolbarFun` l'app di esempio, Aggiungi icone di menu al progetto dell'app. Scaricare le [Icone della barra degli strumenti](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true), decomprimere e copiare il contenuto delle cartelle *mipmap* estratte nel progetto *mipmap-* Folders in **ToolbarFun/** Resources e includere ogni file di icona aggiunto nel progetto.


### <a name="define-a-menu-resource"></a>Definire una risorsa di menu

Creare una nuova sottodirectory di **menu** in **risorse**. Nella sottodirectory del **menu** creare un nuovo file di risorse di menu denominato **top_menus. XML** e sostituirne il contenuto con il codice XML seguente: 

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

- Una voce di menu **modifica** che usa `ic_action_content_create.png` l'icona (matita). 

- Una voce di menu **Salva** che usa `ic_action_content_save.png` l'icona (un dischetto). 

- Una voce di menu **Preferenze** che non dispone di un'icona.

Gli `showAsAction` attributi delle voci di menu **modifica** e **Salva** sono impostati su `ifRoom` &ndash; questa impostazione causa la visualizzazione di queste voci di menu `Toolbar` nel se è disponibile spazio sufficiente per la visualizzazione. La voce di menu Preferenze `showAsAction` imposta `never` su &ndash; questo determina la visualizzazione del menu **Preferenze** nel menu di *overflow* (tre punti verticali). 


### <a name="implement-oncreateoptionsmenu"></a>Implementare OnCreateOptionsMenu

Aggiungere il metodo seguente a **MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android chiama il `OnCreateOptionsMenu` metodo in modo che l'app possa specificare la risorsa di menu per un'attività. In questo metodo, la risorsa **top_menus. XML** è inflat nell'oggetto passato `menu`. Questo codice fa in modo che `Toolbar`le nuove voci di menu **modifica**, **Salva**e **Preferenze** siano visualizzate in. 



### <a name="implement-onoptionsitemselected"></a>Implementare OnOptionsItemSelected

Aggiungere il metodo seguente a **MainActivity.cs**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

Quando un utente tocca una voce di menu, Android chiama `OnOptionsItemSelected` il metodo e passa la voce di menu selezionata. In questo esempio, l'implementazione Visualizza solo un avviso popup per indicare quale voce di menu è stata toccata. 

Compilare ed eseguire `ToolbarFun` per visualizzare le nuove voci di menu sulla barra degli strumenti. `Toolbar` Ora visualizza tre icone di menu, come illustrato in questo screenshot: 

[![Diagramma che illustra le posizioni delle voci di menu modifica, Salva e overflow](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

Quando un utente tocca la voce di menu **modifica** , viene visualizzato un avviso popup per indicare `OnOptionsItemSelected` che è stato chiamato il metodo: 

[![Screenshot del popup visualizzato quando si tocca Modifica elemento](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

Quando un utente tocca il menu di overflow, viene visualizzata la voce di menu **Preferenze** . In genere, le azioni meno comuni devono essere &ndash; inserite nel menu di overflow. in questo esempio viene utilizzato il menu di overflow per le **Preferenze** , perché non viene utilizzato con la stessa frequenza di **modifica** e **salvataggio**: 

[![Screenshot della voce di menu Preferenze visualizzata nel menu di overflow](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

Per altre informazioni sui menu Android, vedere l'argomento [menu](https://developer.android.com/guide/topics/ui/menus.html) per sviluppatori Android. 
 

## <a name="troubleshooting"></a>Risoluzione dei problemi

I suggerimenti seguenti consentono di eseguire il debug dei problemi che possono verificarsi durante la sostituzione della barra delle azioni con una barra degli strumenti.

### <a name="activity-already-has-an-action-bar"></a>L'attività dispone già di un Barra delle azioni

Se l'app non è configurata correttamente per l'uso di un tema personalizzato come illustrato in [applicare il tema personalizzato](#apply-the-custom-theme), durante l'esecuzione dell'app potrebbe verificarsi l'eccezione seguente:

![Errore che può verificarsi quando non viene usato il tema personalizzato](replacing-the-action-bar-images/03-theme-not-defined.png)

Inoltre, è possibile che venga generato un messaggio di errore simile al seguente: _Java.Lang.IllegalStateException: Questa attività dispone già di una barra delle azioni fornita dalla decorazione della finestra._ 

Per correggere l'errore, verificare che l' `android:theme` attributo del tema personalizzato venga aggiunto a `<application>` (in **Properties/file AndroidManifest. XML**), come descritto in precedenza in [applicare il tema personalizzato](#apply-the-custom-theme). Questo errore può inoltre verificarsi se il layout o il `Toolbar` tema personalizzato non è configurato correttamente.


## <a name="related-links"></a>Collegamenti correlati

- [Barra degli strumenti Lollipop (Sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra degli strumenti AppCompat (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
