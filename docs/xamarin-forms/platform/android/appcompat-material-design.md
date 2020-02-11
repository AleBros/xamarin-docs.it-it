---
title: Aggiunta di AppCompat e della progettazione del materiale
description: Questo articolo illustra come convertire le app Xamarin.Forms Android esistenti per usare AppCompat e la progettazione del materiale.
ms.prod: xamarin
ms.assetid: 045FBCDF-4D45-48BB-9911-BD3938C87D58
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2017
ms.openlocfilehash: 36c5733c347e3493b5ed423c52766c7e33fbdb3d
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728330"
---
# <a name="adding-appcompat-and-material-design"></a>Aggiunta di AppCompat e della progettazione del materiale

_Seguire questa procedura per convertire le app Xamarin.Forms Android esistenti in modo da usare AppCompat e progettazione materiale_

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>Panoramica di

Queste istruzioni spiegano come aggiornare le applicazioni Xamarin.Forms Android esistenti per usare la libreria AppCompat e abilitare la progettazione del materiale nella versione Android delle app Xamarin.Forms.

### <a name="1-update-xamarinforms"></a>1. aggiornare Xamarin.Forms

Assicurarsi che la soluzione usi Xamarin.Forms 2,0 o versione successiva. Se necessario, aggiornare il pacchetto NuGet Xamarin.Forms a 2,0.

### <a name="2-check-android-version"></a>2. controllare la versione di Android

Verificare che il Framework di destinazione del progetto Android sia Android 6,0 (marshmallow). Selezionare le **opzioni > progetto Android > compila > Impostazioni generali** per assicurarsi che sia selezionato corrent Framework:

 ![](appcompat-images/target-android-6-sml.png "Android General Build Configuration")

### <a name="3-add-new-themes-to-support-material-design"></a>3. aggiungere nuovi temi per supportare la progettazione del materiale

Creare i tre file seguenti nel progetto Android e incollare il contenuto seguente. Google fornisce una [Guida di stile](https://www.google.com/design/spec/style/color.html#color-color-palette) e un [Generatore di tavolozze dei colori](https://www.materialpalette.com/) che consentono di scegliere una combinazione di colori alternativa a quella specificata.

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

Per applicare proprietà specifiche durante l'esecuzione su un Lollipop Android e una versione più recente, è necessario includere nella cartella **values-V21** uno stile aggiuntivo.

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

### <a name="4-update-androidmanifestxml"></a>4. aggiornare file AndroidManifest. XML

Per assicurarsi che vengano utilizzate le nuove informazioni sul tema, impostare il tema nel file **file AndroidManifest** aggiungendo `android:theme="@style/MyTheme"` (lasciare invariato il resto del codice XML).

**Properties/AndroidManifest.xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5. specificare i layout della barra degli strumenti e delle schede

Creare i file **tabby. aXML** e **Toolbar. aXML** nella directory **Resources/layout** e incollare il contenuto seguente:

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

Sono state impostate alcune proprietà per le schede, inclusa la gravità della scheda `fill` e la modalità di `fixed`.
Se si dispone di numerose schede, è possibile passare a questa opzione per eseguire lo scorrimento. per altre informazioni, vedere la documentazione di Android [TabLayout](https://developer.android.com/reference/android/support/design/widget/TabLayout.html) .

**Risorse/layout/barra degli strumenti. aXML**

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

In questi file viene creato un tema specifico per la barra degli strumenti che può variare in base all'applicazione.
Per altre informazioni, vedere il post di Blog della [barra degli strumenti Hello](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/) .

### <a name="6-update-the-mainactivity"></a>6. aggiornare il `MainActivity`

Nelle app Xamarin.Forms esistenti la classe **MainActivity.cs** erediterà da `FormsApplicationActivity`. Questa operazione deve essere sostituita con `FormsAppCompatActivity` per abilitare la nuova funzionalità.

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

Infine, "collegare" i nuovi layout dal passaggio 5 nel metodo di `OnCreate`, come illustrato di seguito:

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
