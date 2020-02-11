---
title: Xamarin.Android LinearLayout
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/07/2018
ms.openlocfilehash: b1cff01c66ae2581a68286e62bd8c8c5fb7f9d72
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028949"
---
# <a name="xamarinandroid-linearlayout"></a>Xamarin.Android LinearLayout

[`LinearLayout`](xref:Android.Widget.LinearLayout) è un [`ViewGroup`](xref:Android.Views.ViewGroup)
che visualizza [`View`](xref:Android.Views.View) figlio
elementi in una direzione lineare, verticalmente o orizzontalmente.

È necessario prestare attenzione all'utilizzo del [`LinearLayout`](xref:Android.Widget.LinearLayout).
Se si inizia a nidificare più [`LinearLayout`](xref:Android.Widget.LinearLayout)s, è consigliabile usare una [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
invece.

Avviare un nuovo progetto denominato **HelloLinearLayout**.

Aprire **risorse/layout/Main. aXML** e inserire quanto segue:

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

Esaminare attentamente questo codice XML. [`LinearLayout`](xref:Android.Widget.LinearLayout) radice
che definisce l'orientamento in verticale &ndash; tutti i [`View`](xref:Android.Views.View)figlio (di cui ha due) verranno impilati verticalmente. Il primo elemento figlio è un altro [`LinearLayout`](xref:Android.Widget.LinearLayout)
che usa un orientamento orizzontale e il secondo elemento figlio è un [`LinearLayout`](xref:Android.Widget.LinearLayout)
che usa un orientamento verticale. Ognuno di questi [`LinearLayout`](xref:Android.Widget.LinearLayout)annidati contiene diversi [`TextView`](xref:Android.Widget.TextView)
gli elementi, orientati l'uno con l'altro nel modo definito dal [`LinearLayout`](xref:Android.Widget.LinearLayout)padre.

A questo punto, aprire **HelloLinearLayout.cs** e assicurarsi che venga caricato il layout **delle risorse/layout/Main. aXML** nella [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
Metodo

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

Il metodo [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)) carica il file di layout per il [`Activity`](xref:Android.App.Activity), specificato dall'ID risorsa &ndash; `Resources.Layout.Main` fa riferimento al file di layout **Resources/layout/Main. aXML** .

Eseguire l'applicazione. Verrà visualizzato quanto segue:

[![screenshot dell'app prima LinearLayout disposti orizzontalmente, secondo verticalmente](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

Si noti come gli attributi XML definiscono il comportamento di ogni visualizzazione. Provare a sperimentare valori diversi per `android:layout_weight` per vedere come viene distribuita la proprietà dello schermo in base al peso di ogni elemento. Vedere il documento [Common Layout Objects](https://developer.android.com/guide/topics/ui/declaring-layout.html) per ulteriori informazioni su come [`LinearLayout`](xref:Android.Widget.LinearLayout)
gestisce l'attributo `android:layout_weight`.

## <a name="references"></a>Riferimenti

- [`LinearLayout`](xref:Android.Widget.LinearLayout)
- [`TextView`](xref:Android.Widget.TextView)

_Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base alle condizioni descritte nella [licenza Creative Commons 2,5 Attribution](https://creativecommons.org/licenses/by/2.5/)._
