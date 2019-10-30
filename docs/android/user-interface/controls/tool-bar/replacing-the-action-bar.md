---
title: Sostituzione della Barra delle azioni
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: f20568b5d76fcc1788d19497e372bcd0cecc61ff
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029079"
---
# <a name="replacing-the-action-bar"></a>Sostituzione della Barra delle azioni

## <a name="overview"></a>Panoramica

Uno degli usi più comuni per il `Toolbar` consiste nel sostituire la barra delle azioni predefinita con una `Toolbar` personalizzata (quando viene creato un nuovo progetto Android, usa la barra delle azioni predefinita). Poiché il `Toolbar` offre la possibilità di aggiungere logo, titoli, voci di menu, pulsanti di spostamento e persino visualizzazioni personalizzate alla sezione della barra delle applicazioni dell'interfaccia utente di un'attività, offre un aggiornamento significativo sulla barra delle azioni predefinita.

Per sostituire la barra delle azioni predefinita di un'app con un `Toolbar`: 

1. Creare un nuovo tema personalizzato e modificare le proprietà dell'app in modo che usi questo nuovo tema. 

2. Disabilitare l'attributo `windowActionBar` nel tema personalizzato e abilitare l'attributo `windowNoTitle`.

3. Definire un layout per la `Toolbar`.

4. Includere il layout `Toolbar` nel file di layout **Main. aXML** dell'attività. 

5. Aggiungere il codice al metodo `OnCreate` dell'attività per individuare la `Toolbar` e chiamare `SetActionBar` per installare il `ToolBar` come barra delle azioni.

Le sezioni seguenti illustrano in dettaglio questo processo. Viene creata un'app semplice e la relativa barra delle azioni viene sostituita con una `Toolbar`personalizzata. 

## <a name="start-an-app-project"></a>Avviare un progetto di app

Creare un nuovo progetto Android denominato **ToolbarFun** (per altre informazioni sulla creazione di un nuovo progetto Android [, vedere Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md) ). Dopo aver creato questo progetto, impostare i livelli di API di destinazione e minimo dell'API Android su **android 5,0 (API level 21-Lollipop)** o versione successiva. Per altre informazioni sull'impostazione dei livelli di versione di Android, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md). Quando l'app viene compilata ed eseguita, Visualizza la barra delle azioni predefinita, come illustrato in questo screenshot:

[![screenshot della barra delle azioni predefinita](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)

## <a name="create-a-custom-theme"></a>Creare un tema personalizzato

Aprire la directory **Resources/values** e creare un nuovo file denominato **Styles. XML**. Sostituire il contenuto con il codice XML seguente: 

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

Questo codice XML definisce un nuovo tema personalizzato **, denominato in** base al tema **. Material. Light. DarkActionBar** , in Lollipop. L'attributo `windowNoTitle` è impostato su `true` per nascondere la barra del titolo: 

```xml
<item name="android:windowNoTitle">true</item>
```

Per visualizzare la barra degli strumenti personalizzata, è necessario disabilitare il `ActionBar` predefinito: 

```xml
<item name="android:windowActionBar">false</item>
```

Per il colore di sfondo della barra degli strumenti viene utilizzata un'impostazione di `colorPrimary` verde oliva: 

```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>Applicare il tema personalizzato

Modificare **Properties/file AndroidManifest. XML** e aggiungere l'attributo `android:theme` seguente all'elemento `<application>` in modo che l'app usi il tema personalizzato `MyTheme`: 

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

Questo XML definisce la `Toolbar` personalizzata che sostituisce la barra delle azioni predefinita. L'altezza minima del `Toolbar` è impostata sulla dimensione della barra delle azioni che sostituisce: 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

Il colore di sfondo del `Toolbar` è impostato sul colore verde oliva definito in precedenza in **Styles. XML**:

```csharp
android:background="?android:attr/colorPrimary"
```

A partire da Lollipop, è possibile usare l'attributo `android:theme` per applicare uno stile a una singola visualizzazione. I temi `ThemeOverlay.Material` introdotti in Lollipop consentono di sovrapporre i temi predefiniti di `Theme.Material`, sovrascrivendo gli attributi rilevanti per renderli chiaro o scuro. In questo esempio, il `Toolbar` usa un tema scuro in modo che il relativo contenuto sia chiaro a colori: 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

Questa impostazione viene utilizzata in modo che le voci di menu contrastino con il colore di sfondo più scuro.

## <a name="include-the-toolbar-layout"></a>Includi il layout della barra degli strumenti

Modificare il file di layout **Resources/layout/Main. aXML** e sostituirne il contenuto con il codice XML seguente:

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

Questo layout include il `Toolbar` definito in **Toolbar. XML** e utilizza una `RelativeLayout` per specificare che il `Toolbar` deve essere posizionata nella parte superiore dell'interfaccia utente (sopra il pulsante). 

## <a name="find-and-activate-the-toolbar"></a>Trovare e attivare la barra degli strumenti

Modificare **MainActivity.cs** e aggiungere l'istruzione using seguente:

```csharp
using Android.Views;
```

Aggiungere inoltre le righe di codice seguenti alla fine del metodo `OnCreate`:

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

Questo codice trova il `Toolbar` e chiama `SetActionBar` in modo che il `Toolbar` prenda le caratteristiche predefinite della barra delle azioni. Il titolo della barra degli strumenti viene modificato in **barra degli strumenti**. Come illustrato in questo esempio di codice, è possibile fare riferimento ai `ToolBar` direttamente come una barra delle azioni. Compilare ed eseguire questa app &ndash; viene visualizzato il `Toolbar` personalizzato al posto della barra delle azioni predefinita: 

[![screenshot della barra degli strumenti personalizzata con la combinazione di colori verde](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

Si noti che il `Toolbar` ha lo stile indipendentemente dal tema `Theme.Material.Light.DarkActionBar` applicato al resto dell'app. 

Se si verifica un'eccezione durante l'esecuzione dell'app, vedere la sezione relativa alla [risoluzione dei problemi](#troubleshooting) di seguito.

## <a name="add-menu-items"></a>Aggiungi voci di menu 

In questa sezione vengono aggiunti i menu al `Toolbar`. L'area superiore destra del `ToolBar` è riservata per le voci di menu &ndash; ciascuna voce di menu (detta anche *elemento azione*) può eseguire un'azione all'interno dell'attività corrente o eseguire un'azione per conto dell'intera app. 

Per aggiungere menu al `Toolbar`: 

1. Aggiungere le icone del menu (se necessario) alle cartelle `mipmap-` del progetto dell'app. Google fornisce un set di icone di menu gratuite nella pagina [Icone materiali](https://design.google.com/icons/) . 

2. Definire il contenuto delle voci di menu aggiungendo un nuovo file di risorse di menu in **risorse/menu**. 

3. Implementare il metodo `OnCreateOptionsMenu` dell'attività &ndash; questo metodo consente di ingrandire le voci di menu. 

4. Implementare il metodo `OnOptionsItemSelected` dell'attività &ndash; questo metodo esegue un'azione quando viene toccata una voce di menu. 

Le sezioni seguenti illustrano questo processo in modo dettagliato aggiungendo le voci di menu **modifica** e **Salva** al `Toolbar`personalizzato. 

### <a name="install-menu-icons"></a>Icone del menu Installa

Continuando con l'app di esempio `ToolbarFun`, Aggiungi icone di menu al progetto dell'app. Scaricare le [Icone della barra degli strumenti](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true), decomprimere e copiare il contenuto delle cartelle *mipmap* estratte nel progetto *mipmap-* Folders in **ToolbarFun/Resources** e includere ogni file di icona aggiunto nel progetto.

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

- Una voce di menu **modifica** che usa l'icona `ic_action_content_create.png` (matita). 

- Una voce di menu **Salva** che usa l'icona del `ic_action_content_save.png` (un dischetto). 

- Una voce di menu **Preferenze** che non dispone di un'icona.

Gli attributi `showAsAction` delle voci di menu **modifica** e **Salva** sono impostati su `ifRoom` &ndash; questa impostazione determina la visualizzazione di queste voci di menu nel `Toolbar` se è disponibile spazio sufficiente per la visualizzazione. La voce di menu **Preferenze** imposta `showAsAction` su `never` &ndash; in questo modo il menu **Preferenze** viene visualizzato nel menu di *overflow* (tre punti verticali). 

### <a name="implement-oncreateoptionsmenu"></a>Implementare OnCreateOptionsMenu

Aggiungere il metodo seguente a **MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android chiama il metodo `OnCreateOptionsMenu` in modo che l'app possa specificare la risorsa di menu per un'attività. In questo metodo, la risorsa **top_menus. XML** è inflat nell'`menu`passata. Questo codice causa la visualizzazione delle nuove voci di menu **modifica**, **Salva**e **Preferenze** nel `Toolbar`. 

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

Quando un utente tocca una voce di menu, Android chiama il metodo `OnOptionsItemSelected` e passa la voce di menu selezionata. In questo esempio, l'implementazione Visualizza solo un avviso popup per indicare quale voce di menu è stata toccata. 

Compilare ed eseguire `ToolbarFun` per visualizzare le nuove voci di menu sulla barra degli strumenti. Il `Toolbar` ora visualizza tre icone di menu, come illustrato in questo screenshot: 

[Diagramma![che illustra le posizioni delle voci di menu modifica, Salva e overflow](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

Quando un utente tocca la voce di menu **modifica** , viene visualizzato un avviso popup per indicare che è stato chiamato il metodo `OnOptionsItemSelected`: 

[![screenshot del popup visualizzato quando si tocca Modifica elemento](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

Quando un utente tocca il menu di overflow, viene visualizzata la voce di menu **Preferenze** . In genere, le azioni meno comuni devono essere inserite nel menu di overflow &ndash; questo esempio usa il menu di overflow per le **Preferenze** perché non viene usato con la stessa frequenza di **modifica** e **salvataggio**: 

[![screenshot della voce di menu Preferenze visualizzata nel menu di overflow](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

Per altre informazioni sui menu Android, vedere l'argomento [menu](https://developer.android.com/guide/topics/ui/menus.html) per sviluppatori Android. 

## <a name="troubleshooting"></a>Troubleshooting

I suggerimenti seguenti consentono di eseguire il debug dei problemi che possono verificarsi durante la sostituzione della barra delle azioni con una barra degli strumenti.

### <a name="activity-already-has-an-action-bar"></a>L'attività dispone già di un Barra delle azioni

Se l'app non è configurata correttamente per l'uso di un tema personalizzato come illustrato in [applicare il tema personalizzato](#apply-the-custom-theme), durante l'esecuzione dell'app potrebbe verificarsi l'eccezione seguente:

![Errore che può verificarsi quando non viene usato il tema personalizzato](replacing-the-action-bar-images/03-theme-not-defined.png)

Inoltre, è possibile che venga generato un messaggio di errore simile al seguente: _java. lang. IllegalStateException: questa attività dispone già di una barra delle azioni fornita dalla decorazione della finestra._ 

Per correggere l'errore, verificare che l'attributo `android:theme` per il tema personalizzato venga aggiunto a `<application>` (in **Properties/file AndroidManifest. XML**), come descritto in precedenza in [applicare il tema personalizzato](#apply-the-custom-theme). Questo errore può inoltre verificarsi se il layout del `Toolbar` o il tema personalizzato non è configurato correttamente.

## <a name="related-links"></a>Collegamenti correlati

- [Barra degli strumenti Lollipop (Sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra degli strumenti AppCompat (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
