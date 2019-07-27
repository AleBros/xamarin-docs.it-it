---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 91003f9a23c667b38028a9852b28dba656ba13db
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510328"
---
# <a name="togglebutton"></a>ToggleButton

In questa sezione verrà creato un pulsante usato in modo specifico per l'alternanza tra due Stati, usando [`ToggleButton`](xref:Android.Widget.ToggleButton) il widget. Questo widget rappresenta un'ottima alternativa ai pulsanti di opzione se si dispone di due semplici Stati che si escludono a vicenda (ad esempio "on" e "off"). Android 4,0 (API level 14) ha introdotto un'alternativa all'interruttore noto come [`Switch`](xref:Android.Widget.Switch).

Un esempio di **ToggleButton** può essere visualizzato nella coppia di immagini a sinistra, mentre la coppia di immagini a destra presenta un esempio di un compasso **:**

![Esempi di opzioni e ToggleButtons negli Stati on e off](toggle-button-images/togglebutton-switch.png)  

Il controllo usato da un'applicazione è una cosa di stile. Entrambi i widget sono funzionalmente equivalenti.

Aprire il file resources **/layout/Main. aXML** e [`ToggleButton`](xref:Android.Widget.ToggleButton) aggiungere l'elemento ( [`LinearLayout`](xref:Android.Widget.LinearLayout)all'interno di):

Per eseguire un'operazione quando lo stato viene modificato, aggiungere il codice seguente alla fine del[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
Metodo

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

Acquisisce l' [`ToggleButton`](xref:Android.Widget.ToggleButton) elemento dal layout e gestisce l'evento click, che definisce l'azione da eseguire quando si fa clic sul pulsante. In questo esempio, il metodo controlla il nuovo stato del pulsante, quindi Visualizza un [`Toast`](xref:Android.Widget.Toast) messaggio che indica lo stato corrente.

Si noti che [`ToggleButton`](xref:Android.Widget.ToggleButton) il gestisce il proprio cambiamento di stato tra checked e unchecked, quindi è sufficiente chiedersi quale sia.

Eseguire l'applicazione.


> [!TIP]
> Se è necessario modificare autonomamente lo stato, ad esempio quando si carica un [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)oggetto salvato, usare il[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> Setter proprietà o[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> ProcessOnStatus.


## <a name="related-links"></a>Collegamenti correlati

- [ToggleButton](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [Switch](https://developer.android.com/reference/android/widget/Switch.html)
