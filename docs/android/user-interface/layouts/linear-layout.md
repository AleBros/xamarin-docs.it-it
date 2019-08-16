---
title: Novell. Android LinearLayout
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/07/2018
ms.openlocfilehash: 3171a89678e88a924198c3921d197c0f0378d29b
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522621"
---
# <a name="xamarinandroid-linearlayout"></a>Novell. Android LinearLayout

[`LinearLayout`](xref:Android.Widget.LinearLayout)è un[`ViewGroup`](xref:Android.Views.ViewGroup)
che Visualizza l'elemento figlio[`View`](xref:Android.Views.View)
elementi in una direzione lineare, verticalmente o orizzontalmente.

È necessario prestare attenzione all' [`LinearLayout`](xref:Android.Widget.LinearLayout)uso di.
Se si inizia a nidificare [`LinearLayout`](xref:Android.Widget.LinearLayout)più istanze di, è consigliabile usare un[`RelativeLayout`](xref:Android.Widget.RelativeLayout)
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

Esaminare attentamente questo codice XML. Esiste una radice[`LinearLayout`](xref:Android.Widget.LinearLayout)
che definisce l'orientamento in modo che &ndash; sia verticale [`View`](xref:Android.Views.View)tutti gli elementi figlio (di cui ha due) verranno impilati verticalmente. Il primo elemento figlio è un altro[`LinearLayout`](xref:Android.Widget.LinearLayout)
che usa un orientamento orizzontale e il secondo figlio è un[`LinearLayout`](xref:Android.Widget.LinearLayout)
che usa un orientamento verticale. Ognuno di questi oggetti [`LinearLayout`](xref:Android.Widget.LinearLayout)annidati contiene diversi[`TextView`](xref:Android.Widget.TextView)
elementi, orientati l'uno con l'altro nel modo definito dal rispettivo [`LinearLayout`](xref:Android.Widget.LinearLayout)elemento padre.

A questo punto, aprire **HelloLinearLayout.cs** e assicurarsi che venga caricato il layout **delle risorse/layout/Main. aXML** nel[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
Metodo

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

Il [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)metodo) carica il file di layout per [`Activity`](xref:Android.App.Activity)l'oggetto, specificato dall'ID &ndash; `Resources.Layout.Main` risorsa, che fa riferimento al file di layout Resources **/layout/Main. aXML** .

Eseguire l'applicazione. Verrà visualizzato quanto segue:

[![Screenshot dell'app prima LinearLayout disposti orizzontalmente, secondo verticalmente](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

Si noti come gli attributi XML definiscono il comportamento di ogni visualizzazione. Provare a sperimentare con valori diversi per `android:layout_weight` vedere come viene distribuita la proprietà dello schermo in base al peso di ogni elemento. Per ulteriori informazioni, vedere il documento [oggetti di layout comuni](https://developer.android.com/guide/topics/ui/declaring-layout.html)[`LinearLayout`](xref:Android.Widget.LinearLayout)
gestisce l' `android:layout_weight` attributo.


## <a name="references"></a>Riferimenti

- [`LinearLayout`](xref:Android.Widget.LinearLayout)
- [`TextView`](xref:Android.Widget.TextView)

_Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base alle condizioni descritte nella [licenza Creative Commons 2,5 Attribution](http://creativecommons.org/licenses/by/2.5/)._
