---
title: Modifica testo
description: Come usare il widget EditText per accettare l'input dell'utente.
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/09/2018
ms.openlocfilehash: 62859e19c533d7b200833996b44a65cb275805c6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029258"
---
# <a name="xamarinandroid-edit-text"></a>Testo di modifica di Xamarin.Android

In questa sezione si userà il widget [EDITTEXT](xref:Android.Widget.EditText) per creare un campo di testo per l'input dell'utente. Una volta immesso il testo nel campo, il tasto **invio** visualizzerà il testo in un messaggio di avviso popup.

Aprire **Resources/layout/activity_main. aXML** e aggiungere l'elemento [EDITTEXT](xref:Android.Widget.EditText) a un layout contenitore. Nell'esempio seguente **activity_main. aXML** include un `EditText` che è stato aggiunto a una `LinearLayout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <EditText
        android:id="@+id/edittext"
        android:layout_width="match_parent"
        android:imeOptions="actionGo"
        android:inputType="text"
        android:layout_height="wrap_content" />
</LinearLayout>
```

In questo esempio di codice, l'attributo `EditText` `android:imeOptions` è impostato su `actionGo`. Questa impostazione consente di modificare l'azione predefinita [eseguita](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE) nell'azione [go](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO) , in modo che il tasto **invio** attiva il gestore di input `KeyPress`.
In genere, `actionGo` viene utilizzato in modo che il tasto **invio immetta** all'utente la destinazione di un URL digitato in.

Per gestire l'input di testo dell'utente, aggiungere il codice seguente alla fine del metodo [OnCreate](xref:Android.App.Activity.OnCreate*) in **MainActivity.cs**:

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
    e.Handled = false;
    if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter) 
    {
        Toast.MakeText(this, edittext.Text, ToastLength.Short).Show();
        e.Handled = true;
    }
};
```

Aggiungere inoltre l'istruzione `using` seguente all'inizio di **MainActivity.cs** , se non è già presente:

```csharp
using Android.Views;
```

Questo esempio di codice consente di ingrandire l'elemento [EDITTEXT](xref:Android.Widget.EditText) dal layout e di aggiungere un gestore [KeyPress](xref:Android.Views.View.KeyPress) che definisce l'azione da effettuare quando si preme un tasto mentre il widget ha lo stato attivo. In questo caso, il metodo viene definito per restare in attesa del tasto **invio** (quando viene toccato) e quindi [visualizzare un messaggio popup con](xref:Android.Widget.Toast) il testo immesso. Si noti che la proprietà [Handled](xref:Android.Views.View.KeyEventArgs.Handled) deve essere sempre `true` se l'evento è stato gestito. Questa operazione è necessaria per impedire che l'evento venga bubbling (che comporterebbe un ritorno a capo nel campo di testo).

Eseguire l'applicazione e immettere testo nel campo di testo. Quando si preme il tasto **invio** , il popup verrà visualizzato come mostrato a destra:

[![esempi di immissione di testo in EditText](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*Parti di questa pagina sono modifiche basate sul lavoro creato e* [*condivise dal progetto open source Android*](https://code.google.com/policies.html) *e usate in base alle condizioni descritte nella* [*licenza Creative Commons 2,5 Attribution*](https://creativecommons.org/licenses/by/2.5/) *. Questa esercitazione è basata sull'esercitazione relativa ai* [*moduli Android*](https://developer.android.com/resources/tutorials/views/hello-formstuff.html) *.*

## <a name="related-links"></a>Collegamenti correlati

- [EditTextSample](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-edittextsample)
