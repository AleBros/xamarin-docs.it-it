---
title: Pulsante personalizzato
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: e6fc3fe4c3cb89d74188557615f58cc8e34f5991
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30766569"
---
# <a name="custom-button"></a>Pulsante personalizzato

In questa sezione si creerà un pulsante con un'immagine personalizzata invece di testo, usando il [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) widget e un file XML che definisce tre diverse immagini da utilizzare per i diversi stati. Quando viene premuto il pulsante, verrà visualizzato un messaggio breve.

Fare doppio clic su e scaricare i seguenti tre immagini, quindi copiarli il **risorse/drawable** directory del progetto. Saranno usati per i diversi stati.

 [![Icona verde Android per lo stato normale](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [ ![icona Android arancione per lo stato attivo](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) [ ![Android giallo sull'icona stato pressed](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

Creare un nuovo file di **risorse/drawable** directory denominata **android_button.xml**. Inserire il codice XML seguente:

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

Definisce una singola risorsa drawable, che modificherà l'immagine in base allo stato corrente del pulsante. Il primo `<item>` definisce **android_pressed.png** come immagine quando viene premuto il pulsante (se è stato attivato); il secondo `<item>` definisce **android_focused.png** come l'immagine quando la pulsante è con stato attivo (quando il pulsante è evidenziato utilizzando il trackball o direzionale); il terzo `<item>` definisce **android_normal.png** come immagine per lo stato normale (se premuto né con stato attivo). Questo file XML rappresenta una singola risorsa drawable e quando si fa riferimento un [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) per lo sfondo, l'immagine visualizzata cambieranno in base questi tre stati.


> [!NOTE]
> L'ordine dei `<item>` elementi è importante. Quando si fa riferimento questo drawable, il `<item>`s vengono attraversati per determinare quale sia appropriato per lo stato del pulsante corrente.
> Poiché l'immagine "normale" ultima, viene applicata solo quando le condizioni `android:state_pressed` e `android:state_focused` sono valutate false.

Aprire il **Resources/layout/Main.axml** file e aggiungere il [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) elemento:

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

Il `android:background` attributo specifica la risorsa drawable da utilizzare per lo sfondo del pulsante (che, quando salvato in **Resources/drawable/android.xml**, viene fatto riferimento come `@drawable/android`). Ciò consente di sostituire l'immagine di sfondo normale utilizzata per i pulsanti in tutto il sistema. Affinché il drawable modificare l'immagine in base allo stato del pulsante, è necessario applicare l'immagine di sfondo.

Per rendere il pulsante di premuto, aggiungere il codice seguente alla fine del [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle/) metodo:

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

Acquisisce il [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) dal layout, quindi aggiunge un [ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) messaggio da visualizzare quando il [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) si fa clic.

Eseguire l'applicazione.


*Parti di questa pagina sono modifiche basate sul lavoro creati e condivisi per il progetto Android di origine aprire e usare in base alle condizioni descritte nel*
[*Creative Commons 2.5 attribuzione licenza* ](http://creativecommons.org/licenses/by/2.5/).
