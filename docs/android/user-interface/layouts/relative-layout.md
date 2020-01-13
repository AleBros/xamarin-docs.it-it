---
title: Uso di sul relativelayout in Xamarin.Android
description: Come usare sul relativelayout in un'applicazione Xamarin.Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/29/2018
ms.openlocfilehash: 6cac771f46242cc0475be0a7ec0d475950f4b4e1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028800"
---
# <a name="xamarinandroid-relativelayout"></a>Xamarin.Android sul relativelayout

[`RelativeLayout`](xref:Android.Widget.RelativeLayout) è un [`ViewGroup`](xref:Android.Views.ViewGroup) che visualizza [`View`](xref:Android.Views.View) figlio
elementi nelle posizioni relative. La posizione di un [`View`](xref:Android.Views.View) può essere specificata in relazione agli elementi di pari livello (ad esempio, all'estrema sinistra o al di sotto di un determinato elemento) o in posizioni relative al [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
area (ad esempio allineata alla parte inferiore, a sinistra del centro).

Una [`RelativeLayout`](xref:Android.Widget.RelativeLayout) è un'utilità molto potente per la progettazione di un'interfaccia utente in quanto può eliminare i [`ViewGroup`](xref:Android.Views.ViewGroup)annidati. Se ci si ritrova a usare diversi [`LinearLayout`](xref:Android.Widget.LinearLayout) annidati
i gruppi possono essere sostituiti con un singolo [`RelativeLayout`](xref:Android.Widget.RelativeLayout).

Avviare un nuovo progetto denominato **HelloRelativeLayout**.

Aprire il file **Resources/layout/Main. aXML** e inserire quanto segue:

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

Si notino tutti gli attributi di `android:layout_*`, ad esempio `layout_below`, `layout_alignParentRight`e `layout_toLeftOf`.
Quando si utilizza una [`RelativeLayout`](xref:Android.Widget.RelativeLayout), è possibile utilizzare questi attributi per descrivere il modo in cui si desidera posizionare ogni [`View`](xref:Android.Views.View). Ognuno di questi attributi definisce un tipo diverso di posizione relativa. Alcuni attributi usano l'ID risorsa di un [`View`](xref:Android.Views.View) di pari livello per definire la propria posizione relativa. Ad esempio, l'ultimo [`Button`](xref:Android.Widget.Button) viene definito in modo da trovarsi a sinistra di e allineato con il livello superiore del [`View`](xref:Android.Views.View) identificato dall'ID `ok` (ovvero il [`Button`](xref:Android.Widget.Button)precedente).

Tutti gli attributi di layout disponibili sono definiti in [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams).

Assicurarsi di caricare il layout nel [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
Metodo

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

Il metodo [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*) carica il file di layout per il [`Activity`](xref:Android.App.Activity), specificato dall'ID risorsa &mdash; `Resource.Layout.Main` fa riferimento al file di layout **Resources/layout/Main. aXML** .

Eseguire l'applicazione. Dovrebbe essere visualizzato il layout seguente:

[![screenshot di un layout relativo con TextView, EditText e due pulsanti](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)

## <a name="resources"></a>Risorse

- [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
- [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams)
- [`TextView`](xref:Android.Widget.TextView)
- [`EditText`](xref:Android.Widget.EditText)
- [`Button`](xref:Android.Widget.Button)

_Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base alle condizioni descritte nella [licenza Creative Commons 2,5 Attribution](https://creativecommons.org/licenses/by/2.5/)._
