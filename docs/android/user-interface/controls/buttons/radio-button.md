---
title: RadioButton
ms.topic: article
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: c4f4909813f5c82a49ec51278b3b50cc36a8e17b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="radiobutton"></a>RadioButton

In questa sezione si creerà due pulsanti di opzione di incompatibili (abilitazione uno disabilita l'altro), utilizzando il [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) e [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) widget. Quando viene premuto un pulsante di opzione, verrà visualizzato un messaggio di tipo avviso popup.


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

È importante che il [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s sono raggruppate per il [ `RadioGroup` ](https://developer.xamarin.com/api/type/Android.Widget.RadioGroup/) elemento in modo che sia possibile selezionare non più di uno alla volta. Questa logica viene gestita automaticamente dal sistema Android. Quando uno [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) all'interno di un gruppo è selezionato, tutti gli altri vengono automaticamente deselezionati.

Per eseguire un'operazione quando ogni [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/) è selezionata, è necessario scrivere un gestore eventi:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

Innanzitutto, viene eseguito il cast del mittente che viene passato in RadioButton.
Un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) messaggio viene visualizzato il testo del pulsante di opzione selezionato.

A questo punto, nella parte inferiore del [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle) metodo, aggiungere quanto segue:

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Ogni acquisisce il [ `RadioButton` ](https://developer.xamarin.com/api/type/Android.Widget.RadioButton/)s dal layout e lo aggiunge handlerto evento appena creato.

Eseguire l'applicazione.

**Suggerimento:** se è necessario modificare manualmente lo stato (ad esempio quando il caricamento di un salvato [ `CheckBoxPreference` ](https://developer.xamarin.com/api/type/Android.Preferences.CheckBoxPreference/)), utilizzare il [ `Checked` ](https://developer.xamarin.com/api/property/Android.Widget.CompoundButton.Checked/) setter di proprietà o [ `Toggle()` ](https://developer.xamarin.com/api/member/Android.Widget.CompoundButton.Toggle/) metodo.

*Parti di questa pagina sono modifiche basate sul lavoro creati e condivisi per il progetto Android di origine aprire e usare in base alle condizioni descritte nel*
[*Creative Commons 2.5 attribuzione licenza* ](http://creativecommons.org/licenses/by/2.5/). 
