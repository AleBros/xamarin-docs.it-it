---
title: Compatibilità di Toolbar
ms.prod: xamarin
ms.assetid: A0798CA1-2C7D-43B6-9E91-4435CC7B6683
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: 809dc8ec8fd1106b8ad8631c0c506067abdf0d97
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029076"
---
# <a name="toolbar-compatibility"></a>Compatibilità di Toolbar

## <a name="overview"></a>Panoramica

Questa sezione illustra come usare `Toolbar` nelle versioni di Android precedenti a Android 5,0 Lollipop. Se l'app non supporta le versioni di Android precedenti a Android 5,0, è possibile ignorare questa sezione. 

Poiché `Toolbar` fa parte della libreria di supporto di Android V7, può essere usato nei dispositivi che eseguono Android 2,1 (API Level 7) e versioni successive. Tuttavia, la [libreria di supporto per Android V7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) NuGet deve essere installata e il codice viene modificato in modo da usare l'implementazione `Toolbar` fornita in questa libreria. Questa sezione illustra come installare questo NuGet e modificare l'app **ToolbarFun** dall' [aggiunta di una seconda barra degli strumenti](~/android/user-interface/controls/tool-bar/adding-a-second-toolbar.md) in modo che venga eseguita nelle versioni di Android precedenti a lollipop 5,0.

Per modificare un'app per l'uso della versione AppCompat della barra degli strumenti: 

1. Impostare le versioni di Android minime e di destinazione per l'app.

2. Installare il pacchetto NuGet AppCompat.

3. Usare un tema AppCompat invece di un tema Android incorporato.

4. Modificare `MainActivity` in modo che le sottoclassi `AppCompatActivity` invece che `Activity`. 

Ognuno di questi passaggi è illustrato in dettaglio nelle sezioni seguenti.

## <a name="set-the-minimum-and-target-android-version"></a>Impostare la versione minima e di destinazione di Android

Il Framework di destinazione dell'app deve essere impostato sul livello API 21 o superiore oppure l'app non verrà distribuita correttamente. Se si verifica un errore, ad esempio **Nessun identificatore di risorsa trovato per l'attributo ' tileModeX ' nel pacchetto ' Android '** durante la distribuzione dell'app, il Framework di destinazione non è impostato su **Android 5,0 (API level 21-Lollipop)** o versione successiva. 

Impostare il livello di Framework di destinazione su API livello 21 o superiore e impostare le impostazioni del progetto livello API Android sulla versione minima di Android che l'app deve supportare. Per altre informazioni sull'impostazione dei livelli di API Android, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md). Nell'esempio `ToolbarFun` la versione minima di Android è impostata su KitKat (livello API 4,4). 

## <a name="install-the-appcompat-nuget-package"></a>Installare il pacchetto NuGet AppCompat

Successivamente, aggiungere il pacchetto AppCompat per la [libreria di supporto Android V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) al progetto. In Visual Studio fare clic con il pulsante destro del mouse su **riferimenti** e scegliere **Gestisci pacchetti NuGet...** . Fare clic su **Sfoglia** e cercare la **libreria di supporto Android V7 AppCompat**. Selezionare **Xamarin.Android. support. V7. AppCompat** e fare clic su **Installa**: 

[![screenshot del pacchetto V7 AppCompat selezionato in Gestisci pacchetti NuGet](toolbar-compatibility-images/01-appcompat-nuget-sml.png)](toolbar-compatibility-images/01-appcompat-nuget.png#lightbox)

Quando questo NuGet è installato, vengono installati anche diversi altri pacchetti NuGet, se non sono già presenti, ad esempio **Xamarin.Android. support. animata. Vector. disegnatore**, **Xamarin.Android. support. v4**e  **Xamarin.Android. support. Vector. disegnatore**). Per altre informazioni sull'installazione di pacchetti NuGet, vedere [procedura dettagliata: inclusione di NuGet nel progetto](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough). 

## <a name="use-an-appcompat-theme-and-toolbar"></a>Usare un tema e una barra degli strumenti di AppCompat

La libreria AppCompat include diversi temi `Theme.AppCompat` che possono essere usati in qualsiasi versione di Android supportata dalla libreria AppCompat. Il tema dell'app `ToolbarFun` esempio deriva da `Theme.Material.Light.DarkActionBar`, che non è disponibile nelle versioni di Android precedenti a lollipop. Pertanto, `ToolbarFun` necessario adattarsi all'utilizzo della controparte AppCompat per questo tema, `Theme.AppCompat.Light.DarkActionBar`. Inoltre, poiché `Toolbar` non è disponibile nelle versioni di Android precedenti a lollipop, è necessario usare la versione AppCompat di `Toolbar`. Pertanto, i layout devono utilizzare `android.support.v7.widget.Toolbar` anziché `Toolbar`. 

### <a name="update-layouts"></a>Aggiornare i layout

Modificare **Resources/layout/Main. aXML** e sostituire l'elemento `Toolbar` con il codice XML seguente: 

```xml
<android.support.v7.widget.Toolbar
    android:id="@+id/edit_toolbar"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorAccent"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Modificare **Resources/layout/Toolbar. XML** e sostituirne il contenuto con il codice XML seguente: 

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

Si noti che i valori `?attr` non sono più preceduti da `android:` (ricordare che la notazione `?` fa riferimento a una risorsa nel tema corrente). Se `?android:attr` stati ancora usati qui, Android fa riferimento al valore dell'attributo dalla piattaforma attualmente in esecuzione anziché dalla libreria AppCompat. Poiché in questo esempio viene utilizzata la `actionBarSize` definita dalla libreria AppCompat, il prefisso `android:` viene eliminato. Analogamente, `@android:style` viene modificato in `@style` in modo che l'attributo `android:theme` sia impostato su un tema nella libreria AppCompat &ndash; viene usato il tema `ThemeOverlay.AppCompat.Dark.ActionBar` anziché `ThemeOverlay.Material.Dark.ActionBar`. 

### <a name="update-the-style"></a>Aggiornare lo stile

Modificare **Resources/values. XML** e sostituirne il contenuto con il codice XML seguente: 

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

I nomi degli elementi e il tema padre in questo esempio non sono più preceduti dal prefisso `android:` perché si sta usando la libreria AppCompat. Inoltre, il tema padre viene modificato nella versione AppCompat di `Light.DarkActionBar`. 

### <a name="update-menus"></a>Menu Aggiorna

Per supportare le versioni precedenti di Android, la libreria AppCompat USA attributi personalizzati che rispecchiano gli attributi dello spazio dei nomi `android:`. Tuttavia, alcuni attributi, ad esempio l'attributo `showAsAction` usato nel tag `<menu>`, non esistono nel Framework Android nei dispositivi meno recenti &ndash; `showAsAction` è stato introdotto nell'API Android 11, ma non è disponibile nell'API Android 7. Per questo motivo, è necessario utilizzare uno spazio dei nomi personalizzato per anteporre tutti gli attributi definiti dalla libreria di supporto. Nei file di risorse di menu viene definito uno spazio dei nomi denominato `local` per il prefisso dell'attributo `showAsAction`. 

Modificare **Resources/menu/top_menus. XML** e sostituirne il contenuto con il codice XML seguente:

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

Lo spazio dei nomi `local` viene aggiunto con questa riga:

```xml
xmlns:local="http://schemas.android.com/apk/res-auto">
```

L'attributo `showAsAction` è preceduto da questo `local:` spazio dei nomi anziché `android:` 

```csharp
local:showAsAction="ifRoom"
```

Analogamente, modificare **Resources/menu/edit_menus. XML** e sostituirne il contenuto con il codice XML seguente:

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

In che modo questo switch dello spazio dei nomi fornisce il supporto per l'attributo `showAsAction` nelle versioni di Android precedenti al livello API 11? L'attributo personalizzato `showAsAction` e tutti i relativi valori possibili sono inclusi nell'app durante l'installazione di AppCompat NuGet. 

## <a name="subclass-appcompatactivity"></a>Sottoclasse AppCompatActivity

Il passaggio finale della conversione consiste nel modificare `MainActivity` in modo che sia una sottoclasse di `AppCompactActivity`. Modificare **MainActivity.cs** e aggiungere le seguenti istruzioni `using`: 

```csharp
using Android.Support.V7.App;
using Toolbar = Android.Support.V7.Widget.Toolbar;
```

In questo modo si dichiara `Toolbar` come versione AppCompat di `Toolbar`. Modificare quindi la definizione di classe di `MainActivity`: 

```csharp
public class MainActivity : AppCompatActivity
```

Per impostare la barra delle azioni sulla versione AppCompat di `Toolbar`, sostituire la chiamata `SetActionBar` con `SetSupportActionBar`. In questo esempio viene modificato anche il titolo per indicare che è in uso la versione AppCompat di `Toolbar`:

```csharp
SetSupportActionBar (toolbar);
SupportActionBar.Title = "My AppCompat Toolbar";
```

Infine, impostare il livello minimo di Android sul valore di pre-Lollipop che deve essere supportato (ad esempio, l'API 19). 

Compilare l'app ed eseguirla in un dispositivo pre-Lollipop o in un emulatore Android. Lo screenshot seguente illustra la versione AppCompat di **ToolbarFun** in un Nexus 4 che esegue KITKAT (API 19): 

[![screenshot completo dell'app in esecuzione su un dispositivo KitKat, vengono visualizzate entrambe le barre degli strumenti](toolbar-compatibility-images/02-running-on-kitkat-sml.png)](toolbar-compatibility-images/02-running-on-kitkat.png#lightbox)

Quando si usa la libreria AppCompat, i temi non devono essere scambiati in base alla versione di Android &ndash; la libreria AppCompat rende possibile garantire un'esperienza utente coerente in tutte le versioni di Android supportate. 

## <a name="related-links"></a>Collegamenti correlati

- [Barra degli strumenti Lollipop (Sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barra degli strumenti AppCompat (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
