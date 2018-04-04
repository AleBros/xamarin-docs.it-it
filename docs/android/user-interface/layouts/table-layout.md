---
title: TableLayout
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: f9a77655091e4b552bd4a9d440f50b6a3cbeabcc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="tablelayout"></a>TableLayout

[`TableLayout`](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) è un [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) che visualizza figlio [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) elementi in righe e colonne.

Avviare un nuovo progetto denominato **HelloTableLayout**.

Aprire il **Resources/Layout/Main.axml** file e inserire il seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:stretchColumns="1">

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Open..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-O"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save As..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-Shift-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Import..."
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Export..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-E"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Quit"
            android:padding="3dip"/>
    </TableRow>
</TableLayout>
```

Si noti come ciò è simile alla struttura di una tabella HTML. Il [ `TableLayout` ](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) elemento è ad esempio HTML `<table>` elemento. [ `TableRow` ](https://developer.xamarin.com/api/type/Android.Widget.TableRow/) è simile a un `<tr>` elemento; ma per le celle, è possibile utilizzare qualsiasi tipo di [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) elemento. In questo esempio, un [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) viene utilizzato per ogni cella. Tra alcune righe, è inoltre disponibile un basic [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/), che consente di disegnare una linea orizzontale.

Verificare che il **HelloTableLayout** attività carica il layout nel [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) metodo:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

Il [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)) metodo carica il file di layout per il [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), specificato dall'ID di risorsa &mdash; `Resource.Layout.Main` si intende il **risorse/Layout / Main. axml** file di layout.

Eseguire l'applicazione. Verrà visualizzato quanto segue:

[![Schermata di esempio di app TableLayout visualizzazione di più righe di tabella](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)



## <a name="references"></a>Riferimenti

-   [`TableLayout`](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) 

-   [`TableRow`](https://developer.xamarin.com/api/type/Android.Widget.TableRow/) 

-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*Parti di questa pagina sono modifiche basate sul lavoro creati e condivisi per il progetto Android di origine aprire e usare in base alle condizioni descritte nel*
[*Creative Commons 2.5 attribuzione licenza* ](http://creativecommons.org/licenses/by/2.5/).
