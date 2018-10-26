---
title: Uso di RelativeLayout in xamarin. Android
description: Come usare RelativeLayout in un'applicazione xamarin. Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: af2972ecc92435836a75013e6203ba47c2c04627
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113147"
---
# <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) è un [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) che visualizza figlio [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
elementi nelle posizioni relative. La posizione di un [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) possono essere specificati come rispetto agli elementi di pari livello (ad esempio per quanto riguarda la sinistra-of o sotto di un elemento specifico) o in posizioni rispetto al [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
area (ad esempio allineato alla parte inferiore, sinistro del centro).

Oggetto [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) è un'utilità molto efficace per la progettazione di un'interfaccia utente perché in grado di eliminare nidificazione [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)s. Se ci si ritrova utilizzando molti annidati [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
i gruppi, è possibile sostituirli con un unico [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/).

Avviare un nuovo progetto denominato **HelloRelativeLayout**.

Aprire il **Resources/Layout/Main.axml** file e inserire il codice seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/label"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Type here:"/>
    <EditText
        android:id="@+id/entry"
        android:layout_width="match_parent"
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

Si noti che ogni il `android:layout_*` attributi, ad esempio `layout_below`, `layout_alignParentRight`, e `layout_toLeftOf`.
Quando si usa un' [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/), è possibile usare questi attributi per descrivere come si desidera posizionare ognuna [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/). Ognuno di questi attributi definiscono un tipo diverso di posizione relativa. Alcuni attributi di usano l'ID risorsa dell'elemento di pari livello [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) per definire la propria posizione relativa. Ad esempio, l'ultima [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) viene definito per si trovano a sinistra-of e allineati-con-the-top-of il [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) identificato dall'ID `ok` (che è il precedente [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)).

Tutti gli attributi di layout disponibili sono definiti nella [ `RelativeLayout.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/).

È necessario caricare questo layout il nome di [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
metodo:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

Il [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/p/System.Int32/) metodo carica il file di layout per il [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), specificato dall'ID di risorsa &mdash; `Resource.Layout.Main` si intende il **risorse/Layout / Main. axml** file di layout.

Eseguire l'applicazione. Verrà visualizzato il layout seguente:

[![Screenshot di un layout relativo con un controllo TextView EditText e due pulsanti](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)


## <a name="resources"></a>Risorse

-   [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
-   [`RelativeLayout.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
-   [`EditText`](https://developer.xamarin.com/api/type/Android.Widget.EditText/)
-   [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)


*Parti di questa pagina sono modifiche basate sul lavoro creato e condiviso da Android Open Source Project e usate in base a condizioni descritte nel*
[*licenza Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).
