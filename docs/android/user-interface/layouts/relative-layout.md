---
title: Uso di sul relativelayout in Novell. Android
description: Come usare sul relativelayout in un'applicazione Novell. Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: af74ae3c7c87f501bff519bcfa361264205ca3f1
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522383"
---
# <a name="xamarinandroid-relativelayout"></a>Novell. Android sul relativelayout

[`RelativeLayout`](xref:Android.Widget.RelativeLayout)è un [`ViewGroup`](xref:Android.Views.ViewGroup) oggetto che Visualizza l'elemento figlio[`View`](xref:Android.Views.View)
elementi nelle posizioni relative. La posizione di un [`View`](xref:Android.Views.View) oggetto può essere specificata in relazione a elementi di pari livello (ad esempio, all'estrema sinistra o al di sotto di un determinato elemento) o in posizioni relative al parametro[`RelativeLayout`](xref:Android.Widget.RelativeLayout)
area (ad esempio allineata alla parte inferiore, a sinistra del centro).

Un [`RelativeLayout`](xref:Android.Widget.RelativeLayout) è un'utilità molto potente per la progettazione di un'interfaccia utente, in quanto [`ViewGroup`](xref:Android.Views.ViewGroup)può eliminare i annidati. Se ci si ritrova a usare diversi[`LinearLayout`](xref:Android.Widget.LinearLayout)
i gruppi possono essere sostituiti con un singolo [`RelativeLayout`](xref:Android.Widget.RelativeLayout).

Avviare un nuovo progetto denominato **HelloRelativeLayout**.

Aprire il file resources **/layout/Main. aXML** e inserire quanto segue:

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

Si noti ogni `android:layout_*` attributo, `layout_below`ad esempio, `layout_alignParentRight`e `layout_toLeftOf`.
Quando si utilizza [`RelativeLayout`](xref:Android.Widget.RelativeLayout)un oggetto, è possibile utilizzare questi attributi per descrivere il modo in cui [`View`](xref:Android.Views.View)si desidera posizionare ognuno di essi. Ognuno di questi attributi definisce un tipo diverso di posizione relativa. Alcuni attributi usano l'ID risorsa di un elemento [`View`](xref:Android.Views.View) di pari livello per definire la propria posizione relativa. Ad esempio, l'ultimo [`Button`](xref:Android.Widget.Button) viene definito in modo da trovarsi a sinistra di e allineato con-il-top- [`View`](xref:Android.Views.View) di identificato dall'ID `ok` (che è il precedente [`Button`](xref:Android.Widget.Button)).

Tutti gli attributi di layout disponibili sono definiti in [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams).

Assicurarsi di caricare il layout nel[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
Metodo

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

Il [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*) metodo carica il file di layout per [`Activity`](xref:Android.App.Activity)l'oggetto, specificato dall'ID &mdash; `Resource.Layout.Main` risorsa, che fa riferimento al file di layout Resources **/layout/Main. aXML** .

Eseguire l'applicazione. Dovrebbe essere visualizzato il layout seguente:

[![Screenshot di un layout relativo con TextView, EditText e due pulsanti](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)

## <a name="resources"></a>Risorse

- [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
- [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams)
- [`TextView`](xref:Android.Widget.TextView)
- [`EditText`](xref:Android.Widget.EditText)
- [`Button`](xref:Android.Widget.Button)

_Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base alle condizioni descritte nella [licenza Creative Commons 2,5 Attribution](http://creativecommons.org/licenses/by/2.5/)._
