---
title: Aggiunta delle applicazioni e i materiali progettazione
description: In questo articolo viene descritto come convertire le app Android di xamarin. Forms esistenti per l'utilizzo di AppCompat e materiale di progettazione.
ms.prod: xamarin
ms.assetid: 045FBCDF-4D45-48BB-9911-BD3938C87D58
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2017
ms.openlocfilehash: c2eed44a7c684b91ceed4493a83ff3b4e1578b5f
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35242921"
---
# <a name="adding-appcompat-and-material-design"></a>Aggiunta delle applicazioni e i materiali progettazione

_Seguire questi passaggi per convertire le app Android di xamarin. Forms esistenti per l'utilizzo delle applicazioni e materiale di progettazione_

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>Panoramica

Queste istruzioni spiegano come aggiornare le applicazioni Android di xamarin. Forms esistenti per usare la libreria delle applicazioni e abilitare la progettazione di materiale nella versione di Android delle App xamarin. Forms.

### <a name="1-update-xamarinforms"></a>1. Aggiornamento di xamarin. Forms

Verificare che la soluzione utilizza xamarin. Forms 2.0 o versione successiva. Se necessario, aggiornare il pacchetto Nuget di xamarin. Forms 2.0.

### <a name="2-check-android-version"></a>2. Controllare la versione di Android

Verificare che il framework di destinazione del progetto Android Android 6.0 (Marshmallow). Controllare il **progetto Android > Opzioni > compilare > Generale** impostazioni per garantire che il framework corrent è selezionata:

 ![](appcompat-images/target-android-6-sml.png "Configurazione di compilazione Android generale")

### <a name="3-add-new-themes-to-support-material-design"></a>3. Aggiungere i nuovi temi per supportare la progettazione di materiale

Creare tre file seguenti nel progetto Android e incollare il contenuto riportato di seguito. Google fornisce un [Guida di stile](http://www.google.com/design/spec/style/color.html#color-color-palette) e [generatore tavolozza di colori](http://www.materialpalette.com/) per facilitare la scelta di uno schema di un colore diverso da quello specificato.

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

Uno stile aggiuntivo deve essere incluso nel **valori v21** cartella per applicare le proprietà specifiche durante l'esecuzione in un simbolo Android e versioni successive.

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

### <a name="4-update-androidmanifestxml"></a>4. Aggiornamento AndroidManifest.xml

Per garantire il nuovo tema informazioni sono tema utilizzato, imposta il **AndroidManifest** file aggiungendo `android:theme="@style/MyTheme"` (lasciare il resto del codice XML è stato).

**Properties/AndroidManifest.xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5. Fornire una scheda e sulla barra degli strumenti layout

Creare **Tabbar.axml** e **Toolbar.axml** file il **risorse/layout** directory e incollare il contenuto dal basso:

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

Sono state impostate alcune proprietà per le schede inclusi la gravità della scheda per `fill` e la modalità per `fixed`.
Se si dispone di molte schede si desidera passare questo per scorrevole - leggere la Android [TabLayout documentazione](http://developer.android.com/reference/android/support/design/widget/TabLayout.html) per altre informazioni.

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

In questi file è in corso la creazione del tema specifico per la barra degli strumenti che può variare per l'applicazione.
Vedere il [Hello barra](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/) post di blog per altre informazioni.


### <a name="6-update-the-mainactivity"></a>6. Aggiornamento di `MainActivity`

Nelle App esistenti di xamarin. Forms il **Mainactivity** eredita dalla classe `FormsApplicationActivity`. È necessario sostituirlo con `FormsAppCompatActivity` per abilitare le nuove funzionalità.

**Mainactivity**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

Infine, "associare" nuovi layout dal passaggio 5 di `OnCreate` (metodo), come illustrato di seguito:

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
