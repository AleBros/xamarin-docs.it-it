---
title: Dispositivi di scorrimento, commutatori e controlli segmentati in Novell. iOS
description: Questo documento illustra le diapositive, i commutatori e i controlli segmentati in Novell. iOS, che descrivono come utilizzarli sia a livello di codice che in iOS designer.
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: bcb860a88c67b3b2cde7336d53d717d4d9201fd4
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288482"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>Dispositivi di scorrimento, commutatori e controlli segmentati in Novell. iOS

<a name="Sliders" />

## <a name="sliders"></a>Scorrimento

Il dispositivo di scorrimento consente di selezionare in modo semplice un valore numerico all'interno di un intervallo. Per impostazione predefinita, il controllo è un valore compreso tra 0 e 1, ma è possibile personalizzare questi limiti.

 [![](slider-switch-segmented-controls-images/image25a.png "Cursore")](slider-switch-segmented-controls-images/image25a.png#lightbox)

Lo screenshot seguente mostra le proprietà modificabili nella finestra di progettazione:

 [![](slider-switch-segmented-controls-images/image26a.png "Proprietà dispositivo di scorrimento")](slider-switch-segmented-controls-images/image25a.png#lightbox)

È possibile impostare questi valori nel codice come illustrato di seguito, incluso il collegamento di un gestore per visualizzare il valore attualmente selezionato `UILabel` in un controllo:

```csharp
slider1.MinValue = -1;
slider1.MaxValue = 2;
slider1.Value = 0.5f; // the current value
slider1.ValueChanged += (sender,e) => label1.Text = ((UISlider)sender).Value.ToString ();
```

È anche possibile personalizzare l'aspetto visivo del dispositivo di scorrimento impostando

```csharp
slider1.ThumbTintColor = UIColor.Blue;
slider1.MinimumTrackTintColor = UIColor.Gray;
slider1.MaximumTrackTintColor = UIColor.Green;
```

Il dispositivo di scorrimento personalizzato ha un aspetto simile al seguente:

 [![](slider-switch-segmented-controls-images/image27a.png "Dispositivo di scorrimento personalizzato")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> Attualmente si verifica un [bug](https://stackoverflow.com/a/19496179) che impedisce `ThumbTint` il rendering in fase di esecuzione come previsto. È possibile aggiungere la seguente riga di codice **prima** del codice precedente come soluzione alternativa. [[Origine](https://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> È possibile usare qualsiasi immagine, in quanto verrà sottoposta a override, ma assicurarsi che venga inserito _nella_ directory Resources e venga chiamato nel codice.

<a name="Switch" />

## <a name="switch"></a>Opzione

iOS USA `UISwitch` come input booleano che può essere rappresentato da un pulsante di opzione su altre piattaforme. L'utente può modificare il controllo spostando il *cursore* tra le posizioni **on/off** .

 [![](slider-switch-segmented-controls-images/image28a.png "Commutatore")](slider-switch-segmented-controls-images/image28a.png#lightbox)

L'aspetto dell'opzione può essere personalizzato nella **riquadro delle proprietà** della finestra di progettazione, che consente di controllare lo stato predefinito, i colori **di tinta/** disattivazione e un' **immagine di attivazione/disattivazione**. Questa operazione è illustrata nell'immagine seguente:

 [![](slider-switch-segmented-controls-images/image29a.png "Cambia proprietà")](slider-switch-segmented-controls-images/image29a.png#lightbox)

Le proprietà dell'opzione possono essere impostate anche nel codice. ad esempio, il codice seguente visualizzerà un'opzione con il valore `On`predefinito:

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls" />


## <a name="segmented-controls"></a>Controlli segmentati

Un controllo segmentato è un modo organizzato per consentire agli utenti di interagire con un numero ridotto di opzioni. Viene steso orizzontalmente e ogni segmento funziona come un pulsante separato. Quando si usa la finestra di progettazione, il controllo segmentato è disponibile nella **casella degli strumenti > controlli**e dovrebbe avere un aspetto simile all'immagine seguente:

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "Controllo segmentato")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

Una funzionalità univoca della finestra di progettazione consente di selezionare ogni segmento singolarmente nell'area di progettazione, come illustrato di seguito:

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "Controllo segmentato")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

In questo modo è possibile utilizzare il riquadro delle proprietà per controllare con maggiore precisione le proprietà di ogni segmento. È possibile visualizzare le proprietà modificabili nello screenshot seguente:

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "Controllo segmentato")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

Si noti che lo stile del controllo segmentato è stato deprecato in iOS7 e, di conseguenza, le opzioni di modifica in un'applicazione iOS7 non avranno alcun effetto.

## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [Controller di avviso](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
