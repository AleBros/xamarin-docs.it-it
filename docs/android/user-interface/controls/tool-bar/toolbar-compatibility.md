---
title: "Compatibilità degli strumenti"
ms.topic: article
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: d4d6e93bf3a755d9b48c9e096de87b4c89f2831f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="toolbar-compatibility"></a>Compatibilità degli strumenti

<a name="overview" />

## <a name="overview"></a>Panoramica

In questa sezione viene illustrato come utilizzare `Toolbar` nelle versioni precedenti di Android 5.0 simbolo di Android. Se l'app non supporta le versioni precedenti ad Android 5.0 di Android, è possibile ignorare questa sezione. 

Poiché `Toolbar` fa parte della libreria di supporto Android v7, può essere utilizzato nei dispositivi che eseguono Android 2.1 (API livello 7) e versioni successive. Tuttavia, il [delle applicazioni di libreria di supporto Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet deve essere installato e il codice modificato in modo che utilizzi il `Toolbar` implementazione fornita nella raccolta. In questa sezione viene descritto come installare questo NuGet e modificare il **ToolbarFun** app da [aggiunta di una barra degli strumenti secondo](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md) in modo che venga eseguito nelle versioni di Android precedenti alla versione 5.0 di simbolo.

Per modificare un'app per usare la versione delle applicazioni della barra degli strumenti: 

1.  Impostare le versioni minima e Android di destinazione per l'app.

2.  Installare il pacchetto NuGet delle applicazioni.

3.  Utilizzare un tema delle applicazioni anziché un tema Android incorporato.

4.  Modificare `MainActivity` in modo che si sottoclassi `AppCompatActivity` anziché `Activity`. 

Ognuno di questi passaggi è illustrato in dettaglio nelle sezioni seguenti.


<a name="android_version" />

## <a name="set-the-minimum-and-target-android-version"></a>Impostare i valori minimo e la versione di destinazione Android

Il Framework di destinazione dell'app deve essere impostato a livello API 21 o versione successiva o l'app non verrà distribuita correttamente. Se un errore, ad esempio **alcun identificatore di risorsa non trovata per l'attributo 'tileModeX' nel pacchetto 'android'** viene visualizzato durante la distribuzione dell'app, infatti, il Framework di destinazione non è impostato su **Android 5.0 (API livello 21 - simbolo)**  o versione successiva. 

Impostare il Framework di destinazione di livello a livello API 21 o maggiore e le impostazioni di progetto a livello di API Android per la versione minima di Android che deve supportare l'app. Per ulteriori informazioni sull'impostazione dei livelli di API Android, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md). Nel `ToolbarFun` esempio, la versione di Android minimo è impostata su KitKat (4.4 livello API). 

<a name="install_nuget" />

## <a name="install-the-appcompat-nuget-package"></a>Installare il pacchetto NuGet delle applicazioni

Successivamente, aggiungere il [delle applicazioni di libreria di supporto Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacchetto al progetto. In Visual Studio, fare doppio clic su **riferimenti** e selezionare **Gestisci pacchetti NuGet...** . Fare clic su **Sfoglia** e cercare **delle applicazioni di libreria di supporto Android v7**. Selezionare **Xamarin.Android.Support.v7.AppCompat** e fare clic su **installare**: 

[![Schermata di applicazioni V7 pacchetto selezionato in Gestione pacchetti NuGet](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png)

Quando viene installato questo NuGet, diversi altri pacchetti NuGet vengono installati anche se non già presente (ad esempio **Xamarin.Android.Support.Animated.Vector.Drawable**, **Xamarin.Android.Support.v4**, e **Xamarin.Android.Support.Vector.Drawable**). Per ulteriori informazioni sull'installazione dei pacchetti NuGet, vedere [procedura dettagliata: inclusi un NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 

<a name="appcompat_theme" />

## <a name="use-an-appcompat-theme-and-toolbar"></a>Utilizzare un tema delle applicazioni e sulla barra degli strumenti

La raccolta delle applicazioni viene fornito con diversi `Theme.AppCompat` temi che possono essere usati in qualsiasi versione di Android supportati dalla libreria delle applicazioni. Il `ToolbarFun` tema dell'app di esempio è derivato da `Theme.Material.Light.DarkActionBar`, che non è disponibile nelle versioni Android precedenti rispetto al simbolo. Pertanto, `ToolbarFun` deve essere adattati per l'utilizzo la controparte delle applicazioni per il tema, `Theme.AppCompat.Light.DarkActionBar`. Inoltre, poiché `Toolbar` è non disponibile nelle versioni di Android precedente a simbolo, è necessario utilizzare la versione delle applicazioni di `Toolbar`. Pertanto, è necessario utilizzare layout `android.support.v7.widget.Toolbar` anziché `Toolbar`. 

<a name="update_layouts" />

### <a name="update-layouts"></a>Aggiornamento layout

Modifica **Resources/layout/Main.axml** e sostituire il `Toolbar` elemento con il codice XML seguente: 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Modifica **Resources/layout/toolbar.xml** e sostituirne il contenuto con il codice XML seguente: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"/>
```

Si noti che il `?attr` valori non sono preceduti da `android:` (tenere presente che il `?` notazione fa riferimento a una risorsa del tema corrente). Se `?android:attr` ancora utilizzati Android fa riferimento in questo caso, il valore dell'attributo dalla piattaforma attualmente in esecuzione anziché dalla libreria delle applicazioni. Poiché questo esempio viene utilizzato il `actionBarSize` definito dalla libreria delle applicazioni, il `android:` prefisso viene eliminato. Analogamente, `@android:style` viene modificato in `@style` in modo che il `android:theme` attributo è impostato su un tema nella libreria delle applicazioni &ndash; il `ThemeOverlay.AppCompat.Dark.ActionBar` tema viene utilizzato qui anziché `ThemeOverlay.Material.Dark.ActionBar`. 

<a name="update_style" />

### <a name="update-the-style"></a>Aggiornare lo stile

Modifica **Resources/values/styles.xml** e sostituirne il contenuto con il codice XML seguente: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="MyTheme.Base"> </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.DarkActionBar">
    <item name="windowNoTitle">true</item>
    <item name="windowActionBar">false</item>
    <item name="colorPrimary">#5A8622</item>
    <item name="colorAccent">#A88F2D</item>
  </style>
</resources>
```

I nomi di elemento e il tema padre in questo esempio non sono precedute da `android:` poiché si sta usando la raccolta delle applicazioni. Inoltre, il tema padre viene modificato per la versione delle applicazioni di `Light.DarkActionBar`. 


<a name="update_menus" />

### <a name="update-menus"></a>Aggiornamento menu

Per supportare le versioni precedenti di Android, la raccolta delle applicazioni utilizza gli attributi personalizzati che riflettono gli attributi del `android:` dello spazio dei nomi. Tuttavia, alcuni attributi (ad esempio il `showAsAction` attributo utilizzato nel `<menu>` tag) non sono presenti nel framework Android sui dispositivi meno recenti &ndash; `showAsAction` è stata introdotta in 11 API Android, ma non è disponibile in 7 API Android. Per questo motivo, uno spazio dei nomi personalizzato deve essere utilizzata per tutti gli attributi definiti dalla libreria di supporto del prefisso. Nei file di risorse di menu, uno spazio dei nomi denominato `local` è definito per anteporre un prefisso di `showAsAction` attributo. 

Modifica **Resources/menu/top_menus.xml** e sostituirne il contenuto con il codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       local:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       local:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       local:showAsAction="never"
       android:title="Preferences" />
</menu>
```

Il `local` dello spazio dei nomi viene aggiunto con la riga seguente:

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

Il `showAsAction` attributo è preceduto `local:` dello spazio dei nomi anziché `android:` 

```csharp
local:showAsAction="ifRoom"
```

Modificare in modo analogo, **Resources/menu/edit_menus.xml** e sostituirne il contenuto con il codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:local="http://schemas.android.com/apk/res-auto">
  <item
       android:id="@+id/menu_cut"
       android:icon="@mipmap/ic_menu_cut_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Cut" />
  <item
       android:id="@+id/menu_copy"
       android:icon="@mipmap/ic_menu_copy_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Copy" />
  <item
       android:id="@+id/menu_paste"
       android:icon="@mipmap/ic_menu_paste_holo_dark"
       local:showAsAction="ifRoom"
       android:title="Paste" />
</menu>
```

La modalità fornisce supporto per questo parametro spazio dei nomi di `showAsAction` attributo Android versioni precedenti a 11 livello API? L'attributo personalizzato `showAsAction` e tutti i relativi valori possibili sono incluse nell'app quando AppCompat NuGet è installato. 

<a name="subclass" />

## <a name="subclass-appcompatactivity"></a>Sottoclasse AppCompatActivity

Il passaggio finale nella conversione consiste nel modificare `MainActivity` in modo che sia una sottoclasse di `AppCompactActivity`. Modifica **Mainactivity** e aggiungere le seguenti `using` istruzioni: 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

Si dichiara `Toolbar` sia la versione delle applicazioni di `Toolbar`. Successivamente, modificare la definizione della classe `MainActivity`: 

```csharp
public class MainActivity : AppCompatActivity
```

Per impostare la barra delle azioni per la versione delle applicazioni di `Toolbar`, sostituire la chiamata a `SetActionBar` con `SetSupportActionBar`. In questo esempio viene modificato anche il titolo per indicare che la versione delle applicazioni di `Toolbar` è in uso:

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

Infine, è possibile modificare il livello minimo Android al pre-simbolo di valore che deve essere supportata (ad esempio, l'API 19). 

Compilare l'applicazione ed eseguirlo in un dispositivo di pre- simbolo di o l'emulatore Android. La schermata seguente mostra la versione delle applicazioni di **ToolbarFun** 4 un nodo in esecuzione KitKat (API 19): 

[![Schermata dell'app in esecuzione in un dispositivo KitKat, vengono visualizzate entrambe le barre degli strumenti](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png)

Quando si utilizza la libreria delle applicazioni, i temi non è necessario passare in base alla versione di Android &ndash; la raccolta delle applicazioni consente di fornire un'esperienza utente coerente in tutte le versioni Android supportate. 




## <a name="related-links"></a>Collegamenti correlati

- [Il simbolo della barra degli strumenti (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra degli strumenti delle applicazioni (esempio)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
