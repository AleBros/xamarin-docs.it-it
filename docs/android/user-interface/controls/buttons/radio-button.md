---
title: RadioButton
ms.prod: xamarin
ms.assetid: 3C32EA3F-D917-C988-72C5-A17354DA791E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 2279282b08c9d97b239de424cf38aa6f1463dc4d
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510360"
---
# <a name="radiobutton"></a>RadioButton

In questa sezione verranno creati due pulsanti di opzione a esclusione reciproca (abilitazione di uno Disabilita l'altro), usando il comando[`RadioGroup`](xref:Android.Widget.RadioGroup)
e[`RadioButton`](xref:Android.Widget.RadioButton)
widget. Quando viene premuto uno di questi pulsanti, viene visualizzato un messaggio di avviso popup.


Aprire il file resources **/layout/Main. aXML** e [`RadioButton`](xref:Android.Widget.RadioButton)aggiungere due oggetti, nidificati in [`LinearLayout`](xref:Android.Widget.LinearLayout)un [`RadioGroup`](xref:Android.Widget.RadioGroup) (all'interno di):

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

È importante che le [`RadioButton`](xref:Android.Widget.RadioButton)istanze [`RadioGroup`](xref:Android.Widget.RadioGroup) di siano raggruppate dall'elemento in modo che non sia possibile selezionare più di una alla volta. Questa logica viene gestita automaticamente dal sistema Android. Quando uno[`RadioButton`](xref:Android.Widget.RadioButton)
all'interno di un gruppo è selezionato, tutti gli altri vengono automaticamente deselezionati.

Per eseguire un'operazione quando [`RadioButton`](xref:Android.Widget.RadioButton) si seleziona ogni, è necessario scrivere un gestore eventi:

```csharp
private void RadioButtonClick (object sender, EventArgs e)
{
    RadioButton rb = (RadioButton)sender;
    Toast.MakeText (this, rb.Text, ToastLength.Short).Show ();
}
```

In primo luogo, il mittente passato viene sottomesso a cast in un RadioButton.
Quindi un[`Toast`](xref:Android.Widget.Toast)
Message Visualizza il testo del pulsante di opzione selezionato.

A questo punto, nella parte inferiore del[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
aggiungere quanto segue:

```csharp
RadioButton radio_red = FindViewById<RadioButton>(Resource.Id.radio_red);
RadioButton radio_blue = FindViewById<RadioButton>(Resource.Id.radio_blue);

radio_red.Click += RadioButtonClick;
radio_blue.Click += RadioButtonClick;
```

Questa operazione acquisisce ogni oggetto [`RadioButton`](xref:Android.Widget.RadioButton)dal layout e aggiunge l'evento handlerto appena creato.

Eseguire l'applicazione.

> [!TIP]
> Se è necessario modificare autonomamente lo stato, ad esempio quando si carica un [`CheckBoxPreference`](xref:Android.Preferences.CheckBoxPreference)oggetto salvato, usare il[`Checked`](xref:Android.Widget.CompoundButton.Checked)
> Setter proprietà o[`Toggle()`](xref:Android.Widget.CompoundButton.Toggle)
> ProcessOnStatus.

*Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base alle condizioni descritte nella*
[*licenza Creative Commons 2,5 Attribution*](http://creativecommons.org/licenses/by/2.5/). 
