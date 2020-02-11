---
title: ToggleButton
ms.prod: xamarin
ms.assetid: 9ADA8FCF-63ED-897A-DD56-D7D86535A92C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: fe444d255beb9c08b4b5bcf5de36a8740e503b55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029287"
---
# <a name="togglebutton"></a>ToggleButton

In questa sezione verrà creato un pulsante usato in modo specifico per l'alternanza tra due Stati, usando il widget [`ToggleButton`](xref:Android.Widget.ToggleButton) . Questo widget rappresenta un'ottima alternativa ai pulsanti di opzione se si dispone di due semplici Stati che si escludono a vicenda (ad esempio "on" e "off"). Android 4,0 (API level 14) ha introdotto un'alternativa all'interruttore noto come [`Switch`](xref:Android.Widget.Switch).

Un esempio di **ToggleButton** può essere visualizzato nella coppia di immagini a sinistra, mentre la coppia di immagini a destra **presenta un esempio**di un compasso:

![Esempi di opzioni e ToggleButtons negli Stati on e off](toggle-button-images/togglebutton-switch.png)  

Il controllo usato da un'applicazione è una cosa di stile. Entrambi i widget sono funzionalmente equivalenti.

Aprire il file **Resources/layout/Main. aXML** e aggiungere l'elemento [`ToggleButton`](xref:Android.Widget.ToggleButton) (all'interno della [`LinearLayout`](xref:Android.Widget.LinearLayout)):

Per eseguire un'operazione quando lo stato viene modificato, aggiungere il codice seguente alla fine del [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
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

Acquisisce l'elemento [`ToggleButton`](xref:Android.Widget.ToggleButton) dal layout e gestisce l'evento click, che definisce l'azione da eseguire quando si fa clic sul pulsante. In questo esempio, il metodo controlla il nuovo stato del pulsante, quindi Mostra un messaggio di [`Toast`](xref:Android.Widget.Toast) che indica lo stato corrente.

Si noti che il [`ToggleButton`](xref:Android.Widget.ToggleButton) gestisce la propria modifica dello stato tra checked e unchecked, quindi è sufficiente chiederne l'aspetto.

Eseguire l'applicazione.

> [!TIP]
> Se è necessario modificare autonomamente lo stato, ad esempio durante il caricamento di un [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)salvato, usare il [`Checked`](xref:Android.Widget.CompoundButton.Checked)
> Setter proprietà o [`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> ProcessOnStatus.

## <a name="related-links"></a>Collegamenti correlati

- [ToggleButton](https://developer.android.com/reference/android/widget/ToggleButton.html)
- [Switch](https://developer.android.com/reference/android/widget/Switch.html)
