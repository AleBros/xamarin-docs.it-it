---
title: Aggiunta di AppCompat e Material Design
description: Questo articolo illustra come convertire le app Android di xamarin. Forms esistenti per usare AppCompat e Material Design.
ms.prod: xamarin
ms.assetid: 045FBCDF-4D45-48BB-9911-BD3938C87D58
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2017
ms.openlocfilehash: c2eed44a7c684b91ceed4493a83ff3b4e1578b5f
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209316"
---
# <a name="adding-appcompat-and-material-design"></a>Aggiunta di AppCompat e Material Design

_Seguire questi passaggi per convertire le app Android di xamarin. Forms esistenti per usare AppCompat e Material Design_

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>Panoramica

Queste istruzioni spiegano come aggiornare le applicazioni Android di xamarin. Forms esistente per usare la libreria AppCompat e abilitare Material Design in Android versione delle App xamarin. Forms.

### <a name="1-update-xamarinforms"></a>1. Aggiornare xamarin. Forms

Verificare che la soluzione Usa xamarin. Forms 2.0 o versione successiva. Se necessario, aggiornare il pacchetto Nuget di xamarin. Forms 2.0.

### <a name="2-check-android-version"></a>2. Controllare la versione di Android

Verificare che il framework di destinazione del progetto Android è Android 6.0 (Marshmallow). Controllare la **progetto Android > Opzioni > compilazione > Generale** impostazioni per assicurarsi che il framework corrent sia selezionate:

 ![](appcompat-images/target-android-6-sml.png "Configurazione della Build generale di Android")

### <a name="3-add-new-themes-to-support-material-design"></a>3. Aggiungere nuovi temi per supportare Material Design

Creare i tre file seguenti nel progetto Android e incollare il contenuto riportato di seguito. Google fornisce un [Guida di stile](http://www.google.com/design/spec/style/color.html#color-color-palette) e una [generatore tavolozza di colori](http://www.materialpalette.com/) per facilitare la scelta di uno schema di un colore diverso da quello specificato.

**Resources/values/colors.xml**

```xml
<resources>
  <color name="primary">#2196F3</color>
  <color name="primaryDark">#1976D2</color>
  <color name="accent">#FFC107</color>
  <color name="window_background">#F5F5F5</color>
</resources>
```

**Resources/values/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
  </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="colorPrimary">@color/primary</item>
    <item name="colorPrimaryDark">@color/primaryDark</item>
    <item name="colorAccent">@color/accent</item>
    <item name="android:windowBackground">@color/window_background</item>
    <item name="windowActionModeOverlay">true</item>
  </style>
</resources>
```

Uno stile aggiuntivo deve essere incluso nel **valori v21** cartella in cui si applicano le proprietà specifiche durante l'esecuzione in Android Lollipop e versioni successive.

**Resources/values-v21/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
    <!--If you are using MasterDetailPage you will want to set these, else you can leave them out-->
    <!--<item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>-->
  </style>
</resources>
```

### <a name="4-update-androidmanifestxml"></a>4. Aggiornare file androidmanifest. Xml

Per verificare che il nuovo tema informazioni sono usato, Imposta tema nel **AndroidManifest** file aggiungendo `android:theme="@style/MyTheme"` (lasciare il resto del codice XML è stato).

**Properties/AndroidManifest.xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5. Fornire i layout della barra degli strumenti e tabulazione

Creare **Tabbar.axml** e **Toolbar.axml** i file nei **risorse/layout** directory e incollarvi i contenuti dal basso:

**Resources/layout/Tabbar.axml**

```xml
<android.support.design.widget.TabLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/sliding_tabs"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:tabIndicatorColor="@android:color/white"
    app:tabGravity="fill"
    app:tabMode="fixed" />
```

Sono state impostate alcune proprietà per le schede tra cui la gravità della scheda per `fill` e la modalità a `fixed`.
Se si dispone di molte delle schede è possibile passare questo a scorrevole - leggere l'Android [TabLayout documentazione](http://developer.android.com/reference/android/support/design/widget/TabLayout.html) per altre informazioni.

**Resources/layout/Toolbar.axml**

```xml
<android.support.v7.widget.Toolbar
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
    app:layout_scrollFlags="scroll|enterAlways" />
```

In questi file viene creato un tema specifico per la barra degli strumenti che può variare per l'applicazione.
Vedere la [Hello barra](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/) post di blog per saperne di più.


### <a name="6-update-the-mainactivity"></a>6. Aggiornamento di `MainActivity`

Nelle App xamarin. Forms esistenti di **MainActivity.cs** eredita dalla classe `FormsApplicationActivity`. Questo deve essere sostituito con `FormsAppCompatActivity` per abilitare le nuove funzionalità.

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

Infine, "collegare i" nuovi layout ottenuto nel passaggio 5 di `OnCreate` metodo, come illustrato di seguito:

```csharp
protected override void OnCreate(Bundle bundle)
{
  // set the layout resources first
  FormsAppCompatActivity.ToolbarResource = Resource.Layout.Toolbar;
  FormsAppCompatActivity.TabLayoutResource = Resource.Layout.Tabbar;

  // then call base.OnCreate and the Xamarin.Forms methods
  base.OnCreate(bundle);
  Forms.Init(this, bundle);
  LoadApplication(new App());
}
```
