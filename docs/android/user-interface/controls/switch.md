---
title: Opzione
description: Come usare il widget commutatore in un'applicazione xamarin. Android
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/29/2018
ms.openlocfilehash: e3bcce48a675a9ba3d1d41f93babc7fcb26448c8
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403276"
---
# <a name="switch"></a>Opzione

Il `Switch` widget (mostrato sotto) consente agli utenti di passare tra due stati, ad esempio in data o su OFF. Il `Switch` valore predefinito è OFF. In entrambi gli stati ON e OFF, il widget è la seguente:

[![Screenshot di un widget di Switch e disattivare stati](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)


## <a name="creating-a-switch"></a>Creazione di un commutatore

Per creare un commutatore, è sufficiente dichiarare un `Switch` elemento nello schema XML come indicato di seguito:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Ciò consente di creare un commutatore di base come illustrato di seguito:

[![Screenshot dell'app demo per la visualizzazione di un commutatore in stato non attivo](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)


## <a name="changing-default-values"></a>Modificare i valori predefiniti

Sia il testo che consente di visualizzare il controllo per il ON e OFF indica che il valore predefinito sono configurabili. Ad esempio, per rendere il commutatore predefinito su ON e lettura Sì/NO anziché ON/OFF, è possibile impostare il `checked`, `textOn`, e `textOff` attributi nel codice XML seguente.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>Che fornisce un titolo

Il `Switch` widget supporta anche incluso un'etichetta di testo impostando la `text` attributo come indicato di seguito:

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

Questo codice produce nella schermata seguente in fase di esecuzione:

[![Screenshot dell'app demo con testo in senso orizzontale che precede il widget commutatore](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Quando un `Switch`del valore cambia, verrà generato un `CheckedChange` evento.
Ad esempio, nel codice seguente è acquisire questo evento e presentare una `Toast` widget con un messaggio in base al `isChecked` pari a `Switch`, che viene passato al gestore eventi come parte del `CompoundButton.CheckedChangeEventArg` argomento.

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```


## <a name="related-links"></a>Collegamenti correlati

- [SwitchDemo (esempio)](https://developer.xamarin.com/samples/monodroid/SwitchDemo/)
- [Scheda Layout esercitazione](~/android/user-interface/layouts/tab-layout/index.md)
