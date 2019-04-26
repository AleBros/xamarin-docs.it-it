---
title: Pulsante personalizzato
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: b5ccefa1eb7e659584c1c82481bbd4473a3a8abc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076247"
---
# <a name="custom-button"></a>Pulsante personalizzato

In questa sezione si creerà un pulsante con un'immagine personalizzata anziché di testo, usando il [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) widget e un file XML che definisce tre diverse immagini da utilizzare per i diversi stati. Quando viene premuto il pulsante, verrà visualizzato un messaggio breve.

Fare doppio clic e scaricare le tre immagini seguenti, quindi copiarli nel **risorse/drawable** del progetto. Questi verrà utilizzati per i diversi stati.

 [![Icona verde Android per lo stato normale](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [ ![icona arancione Android per lo stato attivo](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) [ ![icona gialla Android per lo stato di premuto](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

Creare un nuovo file nei **risorse/drawable** directory denominata **android_button.xml**. Inserire il codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/android_pressed"
          android:state_pressed="true" />
    <item android:drawable="@drawable/android_focused"
          android:state_focused="true" />
    <item android:drawable="@drawable/android_normal" />
</selector>
```

Definisce una singola risorsa drawable, che modificherà l'immagine basata allo stato corrente del pulsante. Il primo `<item>` definisce **android_pressed.png** come l'immagine quando viene premuto il pulsante (è stato attivato); il secondo `<item>` definisce **android_focused.png** come immagine di quando il pulsante è con stato attivo (quando il pulsante è evidenziato usando il trackball o tasto direzionale); e il terzo `<item>` definisce **android_normal.png** come immagine per lo stato normale (quando non premuto né con stato attivo). Questo file XML ora rappresenta una singola risorsa drawable e quando si fa riferimento una [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) per lo sfondo, l'immagine visualizzata cambieranno in base questi tre stati.


> [!NOTE]
> L'ordine dei `<item>` elementi è importante. Quando questo drawable viene fatto riferimento, il `<item>`s vengono attraversati in ordine per determinare quale sia appropriato per lo stato del pulsante corrente.
> Poiché sia l'ultimo elemento dell'immagine "normale", viene applicata solo quando le condizioni `android:state_pressed` e `android:state_focused` hanno valutato false.

Aprire il **Resources/layout/Main.axml** file e aggiungere il [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) elemento:

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

Il `android:background` attributo specifica la risorsa drawable da usare per lo sfondo del pulsante (che, quando salvato in **Resources/drawable/android.xml**, viene fatto riferimento come `@drawable/android`). Questa impostazione sostituisce l'immagine di sfondo normale usato per i pulsanti nell'intero sistema. Affinché il drawable modificare l'immagine basata allo stato del pulsante, è necessario applicare l'immagine di sfondo.

Per rendere il pulsante di eseguire un'operazione quando premuto, aggiungere il codice seguente alla fine del [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle/)
metodo:

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

Acquisisce il [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) del layout, quindi aggiunge un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) messaggio da visualizzare quando il [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) si fa clic.

Ora eseguire l'applicazione.


*Parti di questa pagina sono modifiche basate sul lavoro creato e condiviso da Android Open Source Project e usate in base a condizioni descritte nel*
[*licenza Creative Commons 2.5 Attribution* ](http://creativecommons.org/licenses/by/2.5/).
