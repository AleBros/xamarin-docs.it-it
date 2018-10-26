---
title: Modifica testo
description: Come usare il widget EditText per accettare l'input dell'utente.
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/09/2018
ms.openlocfilehash: e708ceb4b0487cb07ae5a55fae5e96f0e41f17f6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119501"
---
# <a name="edit-text"></a>Modifica testo

In questa sezione si userà il [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) widget per creare un campo di testo per l'input utente. Dopo che è stato immesso nel campo, il **invio** chiave verrà visualizzato il testo in un messaggio di tipo avviso popup.

Aprire **Resources/layout/activity_main.axml** e aggiungere il [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) elemento a un layout che lo contiene. Nell'esempio seguente **activity_main.axml** dispone di un `EditText` che è stato aggiunto a un `LinearLayout`:

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

In questo esempio di codice, il `EditText` attributo `android:imeOptions` è impostata su `actionGo`. Questa impostazione viene modificata l'impostazione predefinita [eseguita](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE) azione per il [passare](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO) azione in modo che se si tocca i **invio** trigger della chiave il `KeyPress` gestore di input.
(In genere `actionGo` viene usato in modo che il **invio** chiave richiede all'utente di destinazione di un URL immesso nel.)

Per gestire l'input di testo dell'utente, aggiungere il codice seguente alla fine della [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/) metodo nella **MainActivity.cs**:

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

Inoltre, aggiungere quanto segue `using` all'inizio dell'istruzione **MainActivity.cs** se non è già presente:

```csharp
using Android.Views;
```

Questo esempio di codice la ingrandisce il [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) elemento dal layout e aggiunge un [KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) gestore che definisce l'azione che deve essere effettuata quando viene premuto un tasto mentre il widget ha lo stato attivo. In questo caso, il metodo è definito per l'ascolto per il **invio** chiave (quando si tocca) e quindi visualizzata la un [tipo avviso popup](https://developer.xamarin.com/api/type/Android.Widget.Toast/) messaggio con il testo che è stata immessa. Si noti che il [Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) proprietà dovrebbe sempre essere `true` se l'evento è stato gestito. Questa operazione è necessaria per impedire l'evento di bubbling remote (che comporterebbero un ritorno a capo nel campo di testo).

Eseguire l'applicazione e immettere il testo nel campo di testo. Quando si preme il **invio** chiave, di tipo avviso popup verrà visualizzato come illustrato a destra:

[![Esempi di immissione di testo in EditText](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*Parti di questa pagina sono modifiche basate sul lavoro creato e* [ *condiviso da Android Open Source Project* ](http://code.google.com/policies.html) *e usate in base a condizioni descritte nel* [ *Licenza di attribuzione creative Commons 2.5* ](http://creativecommons.org/licenses/by/2.5/) *. Questa esercitazione si basa sul* [ *esercitazione Android cose modulo* ](http://developer.android.com/resources/tutorials/views/hello-formstuff.html) *.*


## <a name="related-links"></a>Collegamenti correlati

- [EditTextSample](https://developer.xamarin.com/samples/monodroid/UserInterface/EditTextSample/)
