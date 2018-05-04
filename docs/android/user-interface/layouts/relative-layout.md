---
title: Utilizzo di RelativeLayout in xamarin. Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: cd2d7537036978e30c97b5776155e429178b6dac
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) è un [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) che visualizza figlio [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) elementi nelle posizioni relative. La posizione di un [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) può essere specificata rispetto alla elementi di pari livello, ad esempio sulla sinistra di o di sotto di un determinato elemento, o in posizioni relativo per il [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) area (ad esempio allineato verso il basso, a sinistra dell'area).

Oggetto [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) è un'utilità molto potente per la progettazione di un'interfaccia utente perché è possibile eliminare la nidificazione [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)s. Se si utilizzando più annidato [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) gruppi, è possibile sostituirli con una singola [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/).

Avviare un nuovo progetto denominato **HelloRelativeLayout**.

Aprire il **Resources/Layout/Main.axml** file e inserire il seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <TextView
        android:id="@+id/label"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Type here:"/>
    <EditText
        android:id="@+id/entry"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:background="@android:drawable/editbox_background"
        android:layout_below="@id/label"/>
    <Button
        android:id="@+id/ok"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/entry"
        android:layout_alignParentRight="true"
        android:layout_marginLeft="10dip"
        android:text="OK" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toLeftOf="@id/ok"
        android:layout_alignTop="@id/ok"
        android:text="Cancel" />
</RelativeLayout>
```

Ogni nota di `android:layout_*` attributi, ad esempio `layout_below`, `layout_alignParentRight`, e `layout_toLeftOf`.
Quando si utilizza un [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/), è possibile utilizzare questi attributi per descrivere la modalità con cui si desidera posizionare ogni [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/). Ognuno di questi attributi definiscono un tipo diverso di posizione relativa. Alcuni attributi che utilizzano l'ID di risorsa di un elemento di pari livello [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) per definire una posizione relativa. Ad esempio, l'ultima [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) è definito per cui si trovano a sinistra di e allineato-con-di-top-of il [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) identificato dall'ID `ok` (ovvero il precedente [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)).

Tutti gli attributi di layout disponibili sono definiti [ `RelativeLayout.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/).

È necessario caricare questo layout il [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) metodo:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

Il [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/p/System.Int32/) metodo carica il file di layout per il [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), specificato dall'ID di risorsa &mdash; `Resource.Layout.Main` si intende il **risorse/Layout / Main. axml** file di layout.

Eseguire l'applicazione. È necessario visualizzare lo schema seguente:

[![Schermata di un layout relativo con un oggetto TextView EditText e due pulsanti](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)


## <a name="resources"></a>Risorse

-   [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
-   [`RelativeLayout.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
-   [`EditText`](https://developer.xamarin.com/api/type/Android.Widget.EditText/)
-   [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)


*Parti di questa pagina sono modifiche basate sul lavoro creati e condivisi per il progetto Android di origine aprire e usare in base alle condizioni descritte nel*
[*Creative Commons 2.5 attribuzione licenza* ](http://creativecommons.org/licenses/by/2.5/).
