---
title: Pulsante personalizzato
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: d85c67cf18c61af04cf12bfab58a5b516d380f62
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029359"
---
# <a name="custom-button"></a>Pulsante personalizzato

In questa sezione si creerà un pulsante con un'immagine personalizzata anziché un testo, usando il widget [`Button`](xref:Android.Widget.Button) e un file XML che definisce tre diverse immagini da usare per i diversi stati dei pulsanti. Quando si preme il pulsante, viene visualizzato un breve messaggio.

Fare clic con il pulsante destro del mouse e scaricare le tre immagini seguenti, quindi copiarle nella directory **Resources/** di cui è possibile eseguire il progetto. Questi verranno usati per i diversi stati dei pulsanti.

 [![icona Android verde per lo stato normale](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [![icona Android arancione per lo stato attivo](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) [![icona Android gialla per](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox) lo stato premuto

Creare un nuovo file nella directory **Resources/** di cui è stato creato il nome **android_button. XML**. Inserire il codice XML seguente:

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

Definisce una singola risorsa che può essere disegnato, che modifica l'immagine in base allo stato corrente del pulsante. Il primo `<item>` definisce **android_pressed. png** come immagine quando si preme il pulsante (è stato attivato); il secondo `<item>` definisce **android_focused. png** come immagine quando il pulsante è attivo (quando il pulsante è evidenziato usando la trackball o il riquadro direzionale); e il terzo `<item>` definisce **android_normal. png** come immagine per lo stato normale (se non è stato premuto né lo stato attivo). Questo file XML rappresenta ora una singola risorsa che può essere disegnato e, quando viene fatto riferimento da un [`Button`](xref:Android.Widget.Button) per lo sfondo, l'immagine visualizzata cambierà in base a questi tre stati.

> [!NOTE]
> L'ordine degli elementi `<item>` è importante. Quando si fa riferimento a questo oggetto, il `<item>`s viene attraversato in ordine per determinare quale è appropriato per lo stato del pulsante corrente.
> Poiché l'immagine "normale" è l'ultima, viene applicata solo quando le condizioni `android:state_pressed` e `android:state_focused` hanno entrambe valutato false.

Aprire il file **Resources/layout/Main. aXML** e aggiungere l'elemento [`Button`](xref:Android.Widget.Button) :

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

L'attributo `android:background` specifica la risorsa che è possibile utilizzare per lo sfondo del pulsante (che, quando viene salvata in **Resources/disegnator/Android. XML**, viene fatto riferimento come `@drawable/android`). Viene sostituita l'immagine di sfondo normale utilizzata per i pulsanti nel sistema. Affinché il disegnatore modifichi la propria immagine in base allo stato del pulsante, l'immagine deve essere applicata allo sfondo.

Per fare in modo che il pulsante esegua una pressione, aggiungere il codice seguente alla fine del [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
Metodo

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

Questa operazione acquisisce il [`Button`](xref:Android.Widget.Button) dal layout e quindi aggiunge un messaggio [`Toast`](xref:Android.Widget.Toast) da visualizzare quando si fa clic sul [`Button`](xref:Android.Widget.Button) .

A questo punto, eseguire l'applicazione.

*Parti di questa pagina sono modifiche basate sul lavoro creato e condivise dal progetto open source Android e usate in base ai termini descritti nella* licenza
[*Creative Commons 2,5 Attribution*](https://creativecommons.org/licenses/by/2.5/).
