---
title: Compatibilità di Toolbar
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 12c19cf1024b78e8be30b7c9f2652019e9854375
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61300345"
---
# <a name="toolbar-compatibility"></a>Compatibilità di Toolbar


## <a name="overview"></a>Panoramica

In questa sezione illustra come usare `Toolbar` in versioni di Android precedenti alla Android 5.0 Lollipop. Se l'app non supporta le versioni di Android precedente alla versione 5.0 di Android, è possibile ignorare questa sezione. 

Poiché `Toolbar` fa parte della libreria di supporto Android v7, può essere utilizzato nei dispositivi che eseguono Android 2.1 (API livello 7) e versioni successive. Tuttavia, il [libreria di supporto Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet deve essere installato e il codice modificato in modo che utilizzi il `Toolbar` implementazione fornita in questa libreria. Questa sezione viene illustrato come installare il NuGet e modificare il **ToolbarFun** app da [aggiunta di una seconda barra](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md) in modo che venga eseguito in versioni di Android precedente alla versione 5.0 Lollipop.

Per modificare un'app per usare la versione AppCompat della barra degli strumenti: 

1.  Impostare le versioni minima e di destinazione di Android per l'app.

2.  Installare il pacchetto AppCompat NuGet.

3.  Utilizzare un tema AppCompat anziché un tema predefinito Android.

4.  Modificare `MainActivity` in modo che lo sottoclassi `AppCompatActivity` anziché `Activity`. 

Ognuno di questi passaggi è illustrato in dettaglio nelle sezioni seguenti.



## <a name="set-the-minimum-and-target-android-version"></a>Impostare i valori minimo e la versione Android di destinazione

Framework di destinazione dell'app deve essere impostata a livello API 21 o versione successiva o l'app non verrà distribuita correttamente. Se un errore, ad esempio **alcun identificatore di risorsa non trovata per l'attributo 'tileModeX' nel pacchetto 'android'** viene visualizzato durante la distribuzione dell'app, infatti, il Framework di destinazione non è impostato su **Android 5.0 (API livello 21 - Lollipop)**  o versione successiva. 

Impostare il Framework di destinazione a livello di API livello 21 o versione successiva e le impostazioni del progetto a livello API Android per la versione minima di Android che l'app deve supportare. Per altre informazioni sull'impostazione dei livelli API Android, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md). Nel `ToolbarFun` esempio, la versione minima di Android è impostata su KitKat (4.4 livello API). 


## <a name="install-the-appcompat-nuget-package"></a>Installare il pacchetto AppCompat NuGet

Successivamente, aggiungere il [libreria di supporto Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacchetto al progetto. In Visual Studio, fare doppio clic **riferimenti** e selezionare **Gestisci pacchetti NuGet...** . Fare clic su **esplorare** e cercare **libreria di supporto Android v7 AppCompat**. Selezionare **Xamarin.Android.Support.v7.AppCompat** e fare clic su **installare**: 

[![Schermata di V7 Appcompat pacchetto selezionato in Gestisci pacchetti NuGet](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

Quando si installa questo NuGet, diversi altri pacchetti NuGet vengono installati anche se non è già presente (ad esempio **Xamarin.Android.Support.Animated.Vector.Drawable**, **Xamarin.Android.Support.v4**, e **Xamarin.Android.Support.Vector.Drawable**). Per altre informazioni sull'installazione di pacchetti NuGet, vedere [procedura dettagliata: Inserimento di pacchetto NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 


## <a name="use-an-appcompat-theme-and-toolbar"></a>Utilizzare un tema AppCompat e barra degli strumenti

La libreria AppCompat viene fornito con molti `Theme.AppCompat` temi che possono essere utilizzati in qualsiasi versione di Android supportati dalla libreria AppCompat. Il `ToolbarFun` tema di app di esempio è derivato da `Theme.Material.Light.DarkActionBar`, che non è disponibile nelle versioni precedenti di Lollipop. Pertanto `ToolbarFun` devono essere adattati per l'uso della controparte AppCompat per questo tema, `Theme.AppCompat.Light.DarkActionBar`. Inoltre, poiché `Toolbar` è non disponibile nelle versioni di Android precedenti alla Lollipop, è necessario usare la versione AppCompat di `Toolbar`. Pertanto, è necessario usare i layout `android.support.v7.widget.Toolbar` invece di `Toolbar`. 


### <a name="update-layouts"></a>Aggiornare i layout

Modificare **Resources/layout/Main.axml** e sostituire il `Toolbar` elemento con il codice XML seguente: 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Modificare **Resources/layout/toolbar.xml** e sostituirne il contenuto con il codice XML seguente: 

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

Si noti che il `?attr` valori non sono più sono preceduti `android:` (tenere presente che il `?` notazione fa riferimento a una risorsa nel tema corrente). Se `?android:attr` ancora sono stati usati in questo caso, fa riferimento il valore dell'attributo a Android dalla piattaforma attualmente in esecuzione invece che dalla libreria AppCompat. Poiché questo esempio Usa la `actionBarSize` definite dalla libreria AppCompat, il `android:` prefisso viene eliminato. Analogamente, `@android:style` viene modificato in `@style` in modo che il `android:theme` attributo è impostato su un tema nella libreria AppCompat &ndash; il `ThemeOverlay.AppCompat.Dark.ActionBar` tema viene utilizzato in questo caso anziché `ThemeOverlay.Material.Dark.ActionBar`. 


### <a name="update-the-style"></a>Aggiornare lo stile

Modificare **Resources/values/styles.xml** e sostituirne il contenuto con il codice XML seguente: 

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

I nomi di elemento e il tema principale in questo esempio non è più sono preceduti da `android:` perché si sta usando la libreria AppCompat. Inoltre, il tema padre viene modificato nella versione AppCompat di `Light.DarkActionBar`. 



### <a name="update-menus"></a>Aggiornamento menu

Per supportare le versioni precedenti di Android, la libreria AppCompat Usa gli attributi personalizzati che riflettono gli attributi del `android:` dello spazio dei nomi. Tuttavia, alcuni attributi (ad esempio il `showAsAction` attributo utilizzato nel `<menu>` tag) non esistono in framework Android in dispositivi meno recenti &ndash; `showAsAction` è stata introdotta in 11 API Android, ma non è disponibile in Android 7 API. Per questo motivo, uno spazio dei nomi personalizzato da utilizzare come prefisso di tutti gli attributi definiti dalla libreria di supporto. Nei file di risorse di menu, uno spazio dei nomi chiamato `local` è definito per anteporre un prefisso di `showAsAction` attributo. 

Modificare **Resources/menu/top_menus.xml** e sostituirne il contenuto con il codice XML seguente:

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

Analogamente, modificare **Resources/menu/edit_menus.xml** e sostituirne il contenuto con il codice XML seguente:

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

In che modo questo commutatore dello spazio dei nomi offre supporto per il `showAsAction` attributo nelle versioni di Android precedenti alla API livello 11? L'attributo personalizzato `showAsAction` e tutti i relativi valori possibili sono incluse nell'app durante l'installazione di AppCompat NuGet. 


## <a name="subclass-appcompatactivity"></a>Sottoclasse AppCompatActivity

Il passaggio finale nella conversione consiste nel modificare `MainActivity` in modo che sia una sottoclasse di `AppCompactActivity`. Modificare **MainActivity.cs** e aggiungere quanto segue `using` istruzioni: 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

Questo codice dichiara `Toolbar` a corrispondere alla versione AppCompat `Toolbar`. Successivamente, modificare la definizione della classe `MainActivity`: 

```csharp
public class MainActivity : AppCompatActivity
```

Per impostare la barra delle azioni alla versione di AppCompat `Toolbar`, sostituire la chiamata a `SetActionBar` con `SetSupportActionBar`. In questo esempio, il titolo viene modificato anche per indicare che la versione AppCompat di `Toolbar` è in uso:

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

Infine, modificare il livello minima di Android per il valore di simbolo non definitiva che deve essere supportata (ad esempio, API 19). 

Compilare l'app ed eseguirla in un dispositivo di pre-Lollipop o un emulatore Android. Lo screenshot seguente mostra la versione AppCompat **ToolbarFun** su 4 Nexus esecuzione KitKat (API 19): 

[![Schermata dell'app in esecuzione in un dispositivo KitKat, vengono visualizzate entrambe le barre degli strumenti](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

Quando viene usata la libreria AppCompat, temi non sono necessario essere spostati in base alla versione di Android &ndash; libreria AppCompat rende possibile fornire un'esperienza utente coerente in tutte le versioni di Android supportate. 




## <a name="related-links"></a>Collegamenti correlati

- [Barra degli strumenti lollipop (esempio)](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [Barra degli strumenti AppCompat (esempio)](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
