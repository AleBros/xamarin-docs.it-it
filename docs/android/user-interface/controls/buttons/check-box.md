---
title: CheckBox
ms.prod: xamarin
ms.assetid: A884AF10-D5EA-72CA-2301-B80CEC7FFBE7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 06908ad8993eb6d47476006b23865fa1c7fe694f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029339"
---
# <a name="checkbox"></a>CheckBox

In questa sezione verrà creata una casella di controllo per la selezione di elementi tramite il widget [`CheckBox`](xref:Android.Widget.CheckBox) . Quando si preme la casella di controllo, un messaggio di avviso popup indicherà lo stato corrente della casella di controllo.

Aprire il file **Resources/layout/Main. aXML** e aggiungere l'elemento [`CheckBox`](xref:Android.Widget.CheckBox) (all'interno della [`LinearLayout`](xref:Android.Widget.LinearLayout)):

```xml
<CheckBox android:id="@+id/checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="check it out" />
```

Per eseguire un'operazione quando lo stato viene modificato, aggiungere il codice seguente alla fine del metodo [`OnCreate()`](xref:Android.App.Activity.OnCreate*) :

```csharp
CheckBox checkbox = FindViewById<CheckBox>(Resource.Id.checkbox);

checkbox.Click += (o, e) => {
    if (checkbox.Checked)
        Toast.MakeText (this, "Selected", ToastLength.Short).Show ();
    else
        Toast.MakeText (this, "Not selected", ToastLength.Short).Show ();
};
```

Acquisisce l'elemento [`CheckBox`](xref:Android.Widget.CheckBox) dal layout, quindi gestisce l'evento click, che definisce l'azione da effettuare quando si fa clic sulla casella di controllo. Quando si fa clic su di essa, viene chiamata la proprietà [`Checked`](xref:Android.Widget.CompoundButton.Checked) per verificare il nuovo stato della casella di controllo. Se è stato selezionato, in un [`Toast`](xref:Android.Widget.Toast) viene visualizzato il messaggio "selezionato"; in caso contrario, viene visualizzato "non selezionato". Il [`CheckBox`](xref:Android.Widget.CheckBox) gestisce le proprie modifiche di stato, quindi è sufficiente eseguire una query sullo stato corrente.

Eseguirlo.

> [!TIP]
> Se è necessario modificare lo stato autonomo, ad esempio durante il caricamento di un [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)salvato, usare il metodo di impostazione della proprietà [`Checked`](xref:Android.Widget.CompoundButton.Checked) o [`Toggle()`](xref:Android.Widget.CompoundButton.Toggle) metodo.

*Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base alle condizioni descritte nella* [*licenza Creative Commons 2,5 Attribution*](https://creativecommons.org/licenses/by/2.5/).
