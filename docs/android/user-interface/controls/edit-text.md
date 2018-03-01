---
title: Modifica testo
ms.topic: article
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 280bb1ad8f717476ec425462a43a32c1f3eac381
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="edit-text"></a>Modifica testo

In questa sezione si creerà un campo di testo per l'input dell'utente, utilizzando il [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) widget. Una volta immesso nel campo di testo, "invio" visualizzerà il testo in un messaggio di tipo avviso popup.

Aprire il <code>Resources\layout\main.xml</code> file e aggiungere il [EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) elemento (all'interno di [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

```xml
<pre><code class=" syntax brush-C#">&lt;EditText
    android:id="@+id/edittext"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"/&gt;</code></pre>
```

Per eseguire un'operazione con il testo che i tipi di utente, aggiungere il codice seguente alla fine del [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/) metodo:

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
 e.Handled = false;
 if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter) {
  Toast.MakeText (this, edittext.Text, ToastLength.Short).Show ();
  e.Handled = true;
         }
};
```

Acquisisce il [ `EditText` ](https://developer.xamarin.com/api/type/Android.Widget.EditText/) elemento dal layout e imposta un [ `KeyPress` ](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/) gestore, che definisce l'azione da eseguire quando viene premuto un tasto mentre il widget ha lo stato attivo. In questo caso, è definito il metodo per attendere il tasto INVIO (quando viene premuto), quindi vengono visualizzati un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) messaggio con il testo che è stata immessa. Il [ `Handled` ](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/) proprietà deve essere sempre `true` se l'evento è stata gestita in modo che l'evento non bolle backup (con la conseguenza di un ritorno a capo nel campo di testo).

Eseguire l'applicazione.

*Parti di questa pagina sono modifiche basate sul lavoro creata e* [ *condiviso dal progetto di origine aprire Android* ](http://code.google.com/policies.html) *e usare in base alle condizioni descritte nel* [ *2.5 di creative Commons Attribution licenza* ](http://creativecommons.org/licenses/by/2.5/) *. In questa esercitazione si basa sul* [ *esercitazione Android modulo Stuff* ](http://developer.android.com/resources/tutorials/views/hello-formstuff.html) *.*



## <a name="related-links"></a>Collegamenti correlati

- [EditTextSample](https://developer.xamarin.com/samples/monodroid/UserInterface/EditTextSample/)
