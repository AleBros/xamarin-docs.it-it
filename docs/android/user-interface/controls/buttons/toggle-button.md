---
title: ToggleButton
ms.topic: article
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 9e1e9711d218f4f4be825ff223b650ae932ad041
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="togglebutton"></a>ToggleButton

In questa sezione si creerà un pulsante utilizzato in particolare per attivare e disattivare tra due stati, utilizzando il [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) widget. Questo widget è un'alternativa eccellente ai pulsanti di opzione se si dispongono di due stati semplici che si escludono a vengono ("on" e "off", ad esempio). Android 4.0 (livello API 14) è stata introdotta un'alternativa al pulsante Attiva/Disattiva noto come un [ `Switch` ](https://developer.xamarin.com/api/type/Android.Widget.Switch/).

Un esempio di un **ToggleButton** possono essere visualizzati nella coppia di immagini, a sinistra, mentre la coppia a destra di immagini offre un esempio di un **commutatore**:

![Negli esempi di commutatori e ToggleButton e non stati](toggle-button-images/togglebutton-switch.png)  

Il controllo utilizza un'applicazione è una questione di stile. Entrambi widget sono funzionalmente equivalenti.

Aprire il **Resources/layout/Main.axml** file e aggiungere il [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) elemento (all'interno di [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

Per eseguire un'operazione quando lo stato viene modificato, aggiungere il codice seguente alla fine del [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle) metodo:

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

Acquisisce il [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) elemento dal layout e gestisce l'evento Click, che definisce l'azione da eseguire quando si fa clic sul pulsante. In questo esempio, il metodo controlla lo stato del pulsante nuovo e quindi viene illustrata una [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) messaggio che indica lo stato corrente.

Si noti che il [ `ToggleButton` ](https://developer.xamarin.com/api/type/Android.Widget.ToggleButton/) gestisce il proprio stato modificare tra checked e unchecked, in modo da cui è basta chiedere.

Eseguire l'applicazione.


**Suggerimento:** se è necessario modificare manualmente lo stato (ad esempio quando il caricamento di un salvato [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), utilizzare il [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/) setter di proprietà o [ `Toggle()` ](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/) metodo.


## <a name="related-links"></a>Collegamenti correlati

- [ToggleButton](http://developer.android.com/reference/android/widget/ToggleButton.html)
- [commutatore](http://developer.android.com/reference/android/widget/Switch.html)
