---
title: LinearLayout
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/07/2018
ms.openlocfilehash: d8ee5f867c9a4d724c54c14b5afdd005f1b805d3
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115770"
---
# <a name="linearlayout"></a>LinearLayout

[`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) è un [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
che consente di visualizzare figlio [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
gli elementi in una direzione lineare, verticalmente o orizzontalmente.

È necessario prestare attenzione sull'utilizzo di risorse in eccesso il [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).
Se si inizia l'annidamento multiplo [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s, è possibile provare a usare un [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
In alternativa.

Avviare un nuovo progetto denominato **HelloLinearLayout**.

Aprire **Resources/Layout/Main.axml** e inserire il codice seguente:

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

Esaminare attentamente il codice XML. È una radice [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
che definisce l'orientamento su verticale &ndash; figlio tutte [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)s (di cui si dispone di due) sarà impilato verticalmente. Il primo elemento figlio è un altro [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
che usa l'orientamento orizzontale e il secondo elemento figlio è un [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
che utilizza un orientamento verticale. Ognuno di questi elementi nidificati [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s contiene diversi [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
gli elementi, che sono orientati tra loro nel modo definito dal relativo elemento padre [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).

A questo punto aprire **HelloLinearLayout.cs** e assicurarsi che carica il **Resources/Layout/Main.axml** layout nel [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
metodo:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

Il [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)) metodo carica il file di layout per il [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), specificato dall'ID di risorsa &ndash; `Resources.Layout.Main` si intende il **risorse/Layout / Main. axml** file di layout.

Eseguire l'applicazione. È consigliabile vedere gli argomenti seguenti:

[![Screenshot dell'app prima LinearLayout disposto in senso orizzontale, secondo verticalmente](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

Si noti come gli attributi XML per definiscono il comportamento di ogni visualizzazione. Provare a sperimentare con valori diversi per `android:layout_weight` per vedere come verrà distribuito l'area dello schermo in base al peso di ogni elemento. Vedere le [oggetti Layout comuni](http://developer.android.com/guide/topics/ui/declaring-layout.html) documento per altre informazioni su come [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
gli handle di `android:layout_weight` attributo.


## <a name="references"></a>Riferimenti

-   [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*Parti di questa pagina sono modifiche basate sul lavoro creato e condiviso da Android Open Source Project e usate in base a condizioni descritte nel*
[*licenza Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).

