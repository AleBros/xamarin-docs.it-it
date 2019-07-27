---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: f6f594d86cab8b1173ee9f67402862e1ec2890b2
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510365"
---
# <a name="checkbox"></a>CheckBox

In questa sezione verrà creata una casella di controllo per la selezione di elementi tramite il[`CheckBox`](xref:Android.Widget.CheckBox)
widget. Quando si preme la casella di controllo, un messaggio di avviso popup indicherà lo stato corrente della casella di controllo.

Aprire il file resources **/layout/Main. aXML** e [`CheckBox`](xref:Android.Widget.CheckBox) aggiungere l'elemento ( [`LinearLayout`](xref:Android.Widget.LinearLayout)all'interno di):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Per eseguire un'operazione quando lo stato viene modificato, aggiungere il codice seguente alla fine del[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
Metodo

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Acquisisce il[`CheckBox`](xref:Android.Widget.CheckBox)
elemento del layout, quindi gestisce l'evento click, che definisce l'azione da eseguire quando si fa clic sulla casella di controllo. Quando si fa clic sul pulsante[`Checked`](xref:Android.Widget.CompoundButton.Checked)
viene chiamata la proprietà per verificare il nuovo stato della casella di controllo. Se è stato selezionato,[`Toast`](xref:Android.Widget.Toast)
Visualizza il messaggio "selezionato"; in caso contrario, Visualizza "non selezionato". Il[`CheckBox`](xref:Android.Widget.CheckBox)
gestisce le proprie modifiche di stato, quindi è sufficiente eseguire una query sullo stato corrente.

Eseguirlo.

> [!TIP]
> Se è necessario modificare autonomamente lo stato, ad esempio quando si carica un [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)oggetto salvato, usare il[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> Setter proprietà o[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> ProcessOnStatus.

*Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base alle condizioni descritte nella*
[*licenza Creative Commons 2,5 Attribution*](http://creativecommons.org/licenses/by/2.5/).
