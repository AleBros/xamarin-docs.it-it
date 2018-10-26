---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: e03595e8d88a2f12341b9e339d0581c631224848
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120801"
---
# <a name="checkbox"></a>CheckBox

In questa sezione si creerà una casella di controllo per la selezione di elementi, tramite il [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox)
widget. Quando si preme la casella di controllo, un messaggio popup indicherà lo stato corrente della casella di controllo.

Aprire il **Resources/layout/Main.axml** file e aggiungere il [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) elemento (all'interno di [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Per eseguire un'operazione quando viene modificato lo stato, aggiungere il codice seguente alla fine del [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
metodo:

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Ciò consente di acquisire il [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)
elemento del layout, quindi gestisce l'evento Click, che definisce l'azione che deve essere effettuata quando si fa clic sulla casella di controllo. Quando si fa clic, il [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
proprietà viene chiamata per verificare il nuovo stato della casella di controllo. Se sono stati verificato, quindi un [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
Visualizza il messaggio "Selezionati", in caso contrario, che viene visualizzato "Non è selezionata". Il [`CheckBox`](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/)
gestisce il proprio le modifiche dello stato, quindi è sufficiente eseguire una query lo stato corrente.

Eseguire il codice.

**Suggerimento:** se è necessario modificare manualmente lo stato (ad esempio, quando il caricamento di un salvato [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference), usare il [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked)
setter di proprietà o [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle)
ProcessOnStatus.

*Parti di questa pagina sono modifiche basate sul lavoro creato e condiviso da Android Open Source Project e usate in base a condizioni descritte nel*
[*licenza Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).
