---
title: Xamarin.Android TableLayout
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 4175b1fa62b2bc0e4209d13934c2bdbdd1e2a085
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028740"
---
# <a name="xamarinandroid-tablelayout"></a>Xamarin.Android TableLayout

[`TableLayout`](xref:Android.Widget.TableLayout) è un [`ViewGroup`](xref:Android.Views.ViewGroup)
che visualizza [`View`](xref:Android.Views.View) figlio
elementi in righe e colonne.

Avviare un nuovo progetto denominato **HelloTableLayout**.

Aprire il file **Resources/layout/Main. aXML** e inserire quanto segue:

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

Si noti che questo aspetto è simile alla struttura di una tabella HTML. [`TableLayout`](xref:Android.Widget.TableLayout)
l'elemento è simile all'elemento HTML `<table>`; [`TableRow`](xref:Android.Widget.TableRow)
è simile a un elemento `<tr>`; Tuttavia, per le celle, è possibile usare qualsiasi tipo di elemento [`View`](xref:Android.Views.View) . In questo esempio, un [`TextView`](xref:Android.Widget.TextView)
viene utilizzato per ogni cella. In una parte delle righe è presente anche un [`View`](xref:Android.Views.View)di base, che viene usato per creare una linea orizzontale.

Assicurarsi che l'attività **HelloTableLayout** carichi questo layout nel [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
Metodo

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

Il metodo [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)) carica il file di layout per il [`Activity`](xref:Android.App.Activity), specificato dall'ID risorsa &mdash; `Resource.Layout.Main` fa riferimento al file di layout **Resources/layout/Main. aXML** .

Eseguire l'applicazione. Verrà visualizzato quanto segue:

[schermata di esempio![dell'app TableLayout che Visualizza più righe della tabella](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)

## <a name="references"></a>Riferimenti

- [`TableLayout`](xref:Android.Widget.TableLayout)
- [`TableRow`](xref:Android.Widget.TableRow)
- [`TextView`](xref:Android.Widget.TextView)

_Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base alle condizioni descritte nella [licenza Creative Commons 2,5 Attribution](https://creativecommons.org/licenses/by/2.5/)._
