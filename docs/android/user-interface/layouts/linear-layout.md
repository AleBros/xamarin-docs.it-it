---
title: LinearLayout
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/07/2018
ms.openlocfilehash: b7564d95c9a472276846b4773d355d8167f37efe
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846406"
---
# <a name="linearlayout"></a>LinearLayout

[`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) è un [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) che visualizza figlio [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) elementi in una direzione lineare, verticalmente o orizzontalmente.

È necessario prestare attenzione sull'uso eccessivo di [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).
Se si inizia a più nidificazione [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s, si consiglia di utilizzare un [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) invece.

Avviare un nuovo progetto denominato **HelloLinearLayout**.

Aprire **Resources/Layout/Main.axml** e inserire il seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"    >

  <LinearLayout
      android:orientation=    "horizontal"
      android:layout_width=    "match_parent"
      android:layout_height=    "match_parent"
      android:layout_weight=    "1"    >
      <TextView
          android:text=    "red"
          android:gravity=    "center_horizontal"
          android:background=    "#aa0000"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "green"
          android:gravity=    "center_horizontal"
          android:background=    "#00aa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "blue"
          android:gravity=    "center_horizontal"
          android:background=    "#0000aa"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "yellow"
          android:gravity=    "center_horizontal"
          android:background=    "#aaaa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
  </LinearLayout>
        
  <LinearLayout
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"
    android:layout_weight=    "1"    >
    <TextView
        android:text=    "row one"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row two"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row three"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row four"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
       android:layout_weight=    "1"    />
  </LinearLayout>

</LinearLayout>
```

Controllare attentamente il codice XML. È una radice [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) che definisce il relativo orientamento in verticale &ndash; figlio tutti [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)s (di cui si dispone di due) sarà impilato verticalmente. Il primo figlio è un altro [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) che presenta un orientamento orizzontale e il secondo elemento figlio è un [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) che presenta un orientamento verticale. Ognuna di queste nidificata [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s contiene diversi [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) elementi, sono orientati tra loro nel modo definito dal relativo elemento padre [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).

Aprire **HelloLinearLayout.cs** e assicurarsi che il caricamento di **Resources/Layout/Main.axml** layout nel [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) metodo:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

Il [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)) metodo carica il file di layout per il [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), specificato dall'ID di risorsa &ndash; `Resources.Layout.Main` si intende il **risorse/Layout / Main. axml** file di layout.

Eseguire l'applicazione. Verrà visualizzato quanto segue:

[![Schermata dell'app prima LinearLayout disposte orizzontalmente, secondo verticalmente](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

Si noti come gli attributi XML definiscono il comportamento di ogni visualizzazione. Provare a sperimentare con valori diversi per `android:layout_weight` per visualizzare la modalità in cui viene distribuito all'area dello schermo in base al peso di ogni elemento. Vedere il [oggetti Layout comuni](http://developer.android.com/guide/topics/ui/declaring-layout.html) documento per altre informazioni su come [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) handle di `android:layout_weight` attributo.


## <a name="references"></a>Riferimenti

-   [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*Parti di questa pagina sono modifiche basate sul lavoro creati e condivisi per il progetto Android di origine aprire e usare in base alle condizioni descritte nel*
[*Creative Commons 2.5 attribuzione licenza* ](http://creativecommons.org/licenses/by/2.5/).

