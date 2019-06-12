---
title: I dispositivi di scorrimento, commutatori e controlli segmentati in xamarin. IOS
description: Questo documento vengono illustrate le diapositive, commutatori e controlli segmentati in xamarin. IOS, che descrive come lavorare con loro a livello di programmazione sia in iOS Designer.
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 7b66005ff64763d68dc6101985e514fa56cf896d
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827367"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>I dispositivi di scorrimento, commutatori e controlli segmentati in xamarin. IOS

<a name="Sliders" />

## <a name="sliders"></a>Dispositivi di scorrimento

Controllo dispositivo di scorrimento consente la semplice selezione di un valore numerico in un intervallo. Valore predefinito è il controllo su un valore compreso tra 0 e 1, ma questi limiti possono essere personalizzati.

 [![](slider-switch-segmented-controls-images/image25a.png "Slider")](slider-switch-segmented-controls-images/image25a.png#lightbox)

Lo screenshot seguente mostra le proprietà che possono essere modificate nella finestra di progettazione:

 [![](slider-switch-segmented-controls-images/image26a.png "Proprietà dispositivo di scorrimento")](slider-switch-segmented-controls-images/image25a.png#lightbox)

È possibile impostare questi valori nel codice, come illustrato di seguito, tra cui collegare un gestore per visualizzare il valore attualmente selezionato in un `UILabel` controllo:

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

È anche possibile personalizzare l'aspetto visivo del dispositivo di scorrimento dall'impostazione

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

Il dispositivo di scorrimento personalizzato è simile alla seguente:

 [![](slider-switch-segmented-controls-images/image27a.png "Dispositivo di scorrimento personalizzato")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> Attualmente non è disponibile un' [bug](https://stackoverflow.com/a/19496179) causando il `ThumbTint` per non eseguire il rendering in fase di esecuzione come previsto. È possibile aggiungere la seguente riga di codice **prima di** il codice precedente come soluzione alternativa. [[Origine](https://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> È possibile usare qualsiasi immagine, come verrà sottoposto a override, ma assicurarsi che questo viene posizionato _in_ directory delle risorse e viene chiamato nel codice.

<a name="Switch" />

## <a name="switch"></a>Opzione

iOS Usa il `UISwitch` come input un valore booleano che può essere rappresentato da un pulsante di opzione in altre piattaforme. L'utente può modificare il controllo spostando le *thumb* tra il **On/Off** posizioni.

 [![](slider-switch-segmented-controls-images/image28a.png "Switch")](slider-switch-segmented-controls-images/image28a.png#lightbox)

L'aspetto del commutatore può essere personalizzato nel **riquadro delle proprietà** della finestra di progettazione, quale sarà consentono di controllare lo stato predefinito, **On/Off tint** dei colori e un **on/off immagine**. Come illustrato nell'immagine seguente:

 [![](slider-switch-segmented-controls-images/image29a.png "Proprietà del commutatore")](slider-switch-segmented-controls-images/image29a.png#lightbox)

Le proprietà del commutatore possono essere impostate anche nel codice, ad esempio il codice seguente visualizzerà un commutatore con il valore predefinito di `On`:

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls" />


## <a name="segmented-controls"></a>Controlli segmentati

Un controllo Segmented è un modo organizzato per consentire agli utenti di interagire con un numero ridotto di opzioni. È disposto in senso orizzontale e ogni segmento funziona come un pulsante separato. Quando si usa la finestra di progettazione, sono disponibili sotto controllo segmentato **casella degli strumenti > controlli**e dovrebbe essere simile all'immagine seguente:

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "Controllo Segmented")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

Consente a una funzionalità univoca di progettazione per ogni segmento di essere selezionate singolarmente nella finestra di progettazione, come illustrato di seguito:

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "Controllo Segmented")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

In questo modo il riquadro delle proprietà da utilizzare per controllare in modo più preciso le proprietà di ogni segmento. È possibile visualizzare le proprietà modificabili nella schermata seguente:

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "Controllo Segmented")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

Si noti che lo stile del controllo segmentato è stato deprecato in IOS 7 e di conseguenza, modificando le opzioni per l'oggetto in un'applicazione IOS 7 non avrà effetto.

## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://developer.xamarin.com/samples/monotouch/Controls/)
- [Controller di avvisi](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
