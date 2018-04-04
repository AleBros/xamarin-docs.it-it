---
title: Opzione
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 0f4bfc3646f1ccd956ee8151468b3de20f6e1e2b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="switch"></a>Opzione

Il `Switch` widget (mostrato sotto) consente agli utenti di passare tra due stati, ad esempio come in o OFF. Il `Switch` valore predefinito è OFF. Il widget è illustrato di seguito in entrambi gli stati ON e OFF:

[![Schermate di un widget commutatore e stati](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)


## <a name="creating-a-switch"></a>Creazione di un commutatore

Per creare un commutatore, è sufficiente dichiarare un `Switch` elemento nello schema XML come indicato di seguito:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

Verrà creato un commutatore di base, come illustrato di seguito:

[![Schermata dell'app demo la visualizzazione di un commutatore in questo stato](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)


## <a name="changing-default-values"></a>Modificare i valori predefiniti

Sia il testo visualizzato dal controllo per il ON e OFF gli Stati e il valore predefinito sono configurabili. Per rendere il commutatore predefinito su ON e lettura/Sì NO anziché accensione e spegnimento, ad esempio, è possibile impostare il `checked`, `textOn`, e `textOff` attributi nel codice XML seguente.

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>Indicazione di un titolo

Il `Switch` widget supporta anche l'inserimento di un'etichetta di testo impostando il `text` attributo come indicato di seguito:

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

In questo markup produce la schermata riportata di seguito in fase di esecuzione:

[![Schermata dell'app demo con testo in senso orizzontale che precede il widget commutatore](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

Quando un `Switch`del valore viene modificato, viene generato un `CheckedChange` evento.
Ad esempio, nel codice seguente è acquisire questo evento e presentare un `Toast` widget con un messaggio in base al `isChecked` valore `Switch`, che viene passato al gestore dell'evento come parte del `CompoundButton.CheckedChangeEventArg` argomento.

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```


## <a name="related-links"></a>Collegamenti correlati

- [SwitchDemo (esempio)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/SwitchDemo/)
- [Scheda Layout esercitazione](~/android/user-interface/layouts/tab-layout/index.md)
- [Introduzione a Sandwich gelato](http://www.android.com/about/ice-cream-sandwich/)
- [Piattaforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
