---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 22eb8f999450ed8fb46b1f7809c92540be13aa65
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105922"
---
# <a name="togglebutton"></a>ToggleButton

In questa sezione si creerà un pulsante utilizzato in particolare per la disattivazione/attivazione tra due stati, usando il [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) widget. Questo widget è un'alternativa eccellente ai pulsanti di opzione se si dispongono di due stati semplici che si escludono a vicenda ("on" e "off", ad esempio). Android 4.0 (livello API 14) è stata introdotta un'alternativa all'interruttore noto come un [ `Switch` ](https://developer.xamarin.com/api/type/Android.Widget.Switch/).

Un esempio di un **ToggleButton** può essere visualizzato nella coppia a sinistra delle immagini, anche se la coppia a destra delle immagini viene presentato un esempio di un **commutatore**:

![Esempi di commutatori e ToggleButton in entrambe di attivare e disattivare gli Stati](toggle-button-images/togglebutton-switch.png)  

Controllo che usa un'applicazione è una questione di stile. Entrambi i widget sono funzionalmente equivalenti.

Aprire il **Resources/layout/Main.axml** file e aggiungere il [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) elemento (all'interno di [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

Per eseguire un'operazione quando viene modificato lo stato, aggiungere il codice seguente alla fine del [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
metodo:

```csharp
ToggleButton togglebutton = FindViewById<ToggleButton>(Resource.Id.togglebutton);

togglebutton.Click += (o, e) => {
    // Perform action on clicks
    if (togglebutton.Checked)
        Toast.MakeText(this, "Checked", ToastLength.Short).Show ();
    else
        Toast.MakeText(this, "Not checked", ToastLength.Short).Show ();
};
```

Ciò consente di acquisire il [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) elemento dal layout e gestisce l'evento Click, che definisce l'azione da eseguire quando si fa clic sul pulsante. In questo esempio, il metodo controlla il nuovo stato del pulsante, quindi viene illustrata una [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) messaggio che indica lo stato corrente.

Si noti che il [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) gestisce il proprio stato cambiare tra checked e unchecked, in modo da cui è basta chiedere.

Eseguire l'applicazione.


**Suggerimento:** se è necessario modificare manualmente lo stato (ad esempio, quando il caricamento di un salvato [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), usare il [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
setter di proprietà o [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)
ProcessOnStatus.


## <a name="related-links"></a>Collegamenti correlati

- [ToggleButton](http://developer.android.com/reference/android/widget/ToggleButton.html)
- [Switch](http://developer.android.com/reference/android/widget/Switch.html)
