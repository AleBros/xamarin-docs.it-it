---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: be473580b24dba6b4f08384771e2097d368f8dc8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123596"
---
# <a name="radiobutton"></a>RadioButton

In questa sezione si creerà due pulsanti di opzione di si escludono a vicenda (abilitazione uno disabilita l'altra), tramite il [`RadioGroup`](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/)
E [`RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)
widget. Quando viene premuto uno dei pulsanti di opzione, verrà visualizzato un messaggio di tipo avviso popup.


Aprire il **Resources/layout/Main.axml** file e aggiungere due [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s, annidata in una [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) (all'interno di [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

```xml
<RadioGroup
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"
  android:orientation="vertical">
  <RadioButton android:id="@+id/radio_red"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Red" />
  <RadioButton android:id="@+id/radio_blue"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:text="Blue" />
</RadioGroup>
```

È importante che il [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s sono raggruppate per il [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) elemento in modo che sia possibile selezionare non più di uno alla volta. Questa logica è gestita automaticamente dal sistema di Android. Quando per uno [`RadioButton`](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)
all'interno di un gruppo è selezionato, tutti gli altri vengono automaticamente deselezionati.

Per eseguire un'operazione quando ciascun [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) è selezionata, è necessario scrivere un gestore eventi:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

In primo luogo, viene eseguito il cast del mittente che viene passato in un pulsante di opzione.
Un [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
messaggio Visualizza il testo del pulsante di opzione selezionato.

A questo punto, nella parte inferiore del [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
metodo, aggiungere quanto segue:

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Ognuna delle acquisisce il [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s dal layout e aggiunge handlerto eventi appena creato.

Eseguire l'applicazione.

**Suggerimento:** se è necessario modificare manualmente lo stato (ad esempio, quando il caricamento di un salvato [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), usare il [`Checked`](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/)
setter di proprietà o [`Toggle()`](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/)
ProcessOnStatus.

*Parti di questa pagina sono modifiche basate sul lavoro creato e condiviso da Android Open Source Project e usate in base a condizioni descritte nel*
[*licenza Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/). 
