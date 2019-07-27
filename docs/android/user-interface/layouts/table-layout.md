---
title: Novell. Android TableLayout
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 9a5186fe49edc81f5d6ec8ca297146a27a0274fa
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509678"
---
# <a name="xamarinandroid-tablelayout"></a>Novell. Android TableLayout

[`TableLayout`](xref:Android.Widget.TableLayout)è un[`ViewGroup`](xref:Android.Views.ViewGroup)
che Visualizza l'elemento figlio[`View`](xref:Android.Views.View)
elementi in righe e colonne.

Avviare un nuovo progetto denominato **HelloTableLayout**.

Aprire il file resources **/layout/Main. aXML** e inserire quanto segue:

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

Si noti che questo aspetto è simile alla struttura di una tabella HTML. Il[`TableLayout`](xref:Android.Widget.TableLayout)
l'elemento è simile all' `<table>` elemento HTML.[`TableRow`](xref:Android.Widget.TableRow)
è simile a `<tr>` un elemento, ma per le celle è possibile utilizzare qualsiasi tipo di [`View`](xref:Android.Views.View) elemento. In questo esempio,[`TextView`](xref:Android.Widget.TextView)
viene utilizzato per ogni cella. In una parte delle righe è presente anche un oggetto di base [`View`](xref:Android.Views.View), che viene usato per creare una linea orizzontale.

Assicurarsi che l'attività **HelloTableLayout** carichi questo layout nel[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
Metodo

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

Il [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)metodo) carica il file di layout per [`Activity`](xref:Android.App.Activity)l'oggetto, specificato dall'ID &mdash; `Resource.Layout.Main` risorsa, che fa riferimento al file di layout Resources **/layout/Main. aXML** .

Eseguire l'applicazione. Verrà visualizzato quanto segue:

[![Schermata di esempio dell'app TableLayout che Visualizza più righe di tabella](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)



## <a name="references"></a>Riferimenti

-   [`TableLayout`](xref:Android.Widget.TableLayout) 

-   [`TableRow`](xref:Android.Widget.TableRow) 

-   [`TextView`](xref:Android.Widget.TextView) 

*Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base alle condizioni descritte nella*
[*licenza Creative Commons 2,5 Attribution*](http://creativecommons.org/licenses/by/2.5/).
