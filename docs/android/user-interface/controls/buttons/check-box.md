---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: b947217706fc8ef7ce7945bf4c88349f4367ffcd
ms.sourcegitcommit: cf56d2bae34dc0f8e94c2d3d28d5f460d59807bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985666"
---
# <a name="checkbox"></a>CheckBox

In questa sezione verrà creata una casella di controllo per la selezione di elementi tramite [`CheckBox`](xref:Android.Widget.CheckBox) il widget. Quando si preme la casella di controllo, un messaggio di avviso popup indicherà lo stato corrente della casella di controllo.

Aprire il file **Resources/layout/Main. aXML** e [`CheckBox`](xref:Android.Widget.CheckBox) aggiungere l'elemento ( [`LinearLayout`](xref:Android.Widget.LinearLayout)all'interno di):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Per eseguire un'operazione quando lo stato viene modificato, aggiungere il codice seguente alla fine del [`OnCreate()`](xref:Android.App.Activity.OnCreate*) metodo:

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Acquisisce l' [`CheckBox`](xref:Android.Widget.CheckBox) elemento dal layout, quindi gestisce l'evento click, che definisce l'azione da effettuare quando si fa clic sulla casella di controllo. Quando si fa clic su [`Checked`](xref:Android.Widget.CompoundButton.Checked) di essa, viene chiamata la proprietà per verificare il nuovo stato della casella di controllo. Se è stata selezionata, [`Toast`](xref:Android.Widget.Toast) viene visualizzato il messaggio "selezionato"; in caso contrario, viene visualizzato "non selezionato". [`CheckBox`](xref:Android.Widget.CheckBox) Gestisce le proprie modifiche di stato, quindi è sufficiente eseguire una query sullo stato corrente.

Eseguirlo.

> [!TIP]
> Se è necessario modificare autonomamente lo stato, ad esempio quando si carica un [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)oggetto salvato, [`Checked`](xref:Android.Widget.CompoundButton.Checked) usare il metodo [`Toggle()`](xref:Android.Widget.CompoundButton.Toggle) o il metodo di impostazione della proprietà.

*Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base alle condizioni descritte nel* [*Licenza Creative Commons 2,5 Attribution*](http://creativecommons.org/licenses/by/2.5/).
