---
title: CheckBox
ms.topic: article
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 78b32a90661c4fde279314aed5c62854b497980a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="checkbox"></a>CheckBox

In questa sezione verrà creata una casella di controllo per la selezione di elementi, con la [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox) widget. Quando si preme la casella di controllo, un messaggio di tipo avviso popup indicherà lo stato corrente della casella di controllo.

Aprire il **Resources/layout/Main.axml** file e aggiungere il [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) elemento (all'interno di [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Per eseguire un'operazione quando lo stato viene modificato, aggiungere il codice seguente alla fine del [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle) metodo:

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Acquisisce il [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) elemento dal layout, quindi gestisce l'evento Click, che definisce l'azione da eseguire quando viene selezionata la casella di controllo. Quando si fa clic, il [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/) proprietà viene chiamata per verificare il nuovo stato della casella di controllo. Se sono stati verificato, quindi un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) viene visualizzato il messaggio "Selezionati", in caso contrario, viene visualizzato "Non è selezionata". Il [ `CheckBox` ](https://developer.xamarin.com/api/type/Android.Widget.CheckBox/) gestisce il proprio cambia lo stato, pertanto è necessario solo eseguire query sullo stato corrente.

Eseguire il codice.

**Suggerimento:** se è necessario modificare manualmente lo stato (ad esempio quando il caricamento di un salvato [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference), utilizzare il [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked) setter di proprietà o [ `Toggle()` ](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle) metodo.

*Parti di questa pagina sono modifiche basate sul lavoro creati e condivisi per il progetto Android di origine aprire e usare in base alle condizioni descritte nel*
[*Creative Commons 2.5 attribuzione licenza* ](http://creativecommons.org/licenses/by/2.5/).
