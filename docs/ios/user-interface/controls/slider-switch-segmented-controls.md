---
title: Dispositivi di scorrimento, commutatori e controlli segmentati in Novell. iOS
description: Questo documento illustra le diapositive, i commutatori e i controlli segmentati in Novell. iOS, che descrivono come utilizzarli sia a livello di codice che in iOS designer.
ms.prod: xamarin
ms.assetid: 85BF0EC8-E581-49CD-B9E7-98BE4C5A0F6B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f7b27c9f1010be313810b4b0f289ef792efd47a3
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84568646"
---
# <a name="sliders-switches-and-segmented-controls-in-xamarinios"></a>Dispositivi di scorrimento, commutatori e controlli segmentati in Novell. iOS

<a name="Sliders"></a>

## <a name="sliders"></a>Dispositivi di scorrimento

Il dispositivo di scorrimento consente di selezionare in modo semplice un valore numerico all'interno di un intervallo. Per impostazione predefinita, il controllo è un valore compreso tra 0 e 1, ma è possibile personalizzare questi limiti.

 [![](slider-switch-segmented-controls-images/image25a.png "Slider")](slider-switch-segmented-controls-images/image25a.png#lightbox)

Lo screenshot seguente mostra le proprietà modificabili nella finestra di progettazione:

 [![](slider-switch-segmented-controls-images/image26a.png "Slider Properties")](slider-switch-segmented-controls-images/image25a.png#lightbox)

È possibile impostare questi valori nel codice come illustrato di seguito, incluso il collegamento di un gestore per visualizzare il valore attualmente selezionato in un `UILabel` controllo:

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

 [![](slider-switch-segmented-controls-images/image27a.png "Custom Slider")](slider-switch-segmented-controls-images/image28a.png#lightbox)

> [!IMPORTANT]
> Attualmente si verifica un [bug](https://stackoverflow.com/a/19496179) che impedisce il `ThumbTint` rendering in fase di esecuzione come previsto. È possibile aggiungere la seguente riga di codice **prima** del codice precedente come soluzione alternativa. [[Origine](https://stackoverflow.com/a/21396794)]:
>
> `slider1.SetThumbImage(UIImage.FromBundle("thumb.png"),UIControlState.Normal);`
> 
> È possibile usare qualsiasi immagine, in quanto verrà sottoposta a override, ma assicurarsi che venga inserito _nella_ directory Resources e venga chiamato nel codice.

<a name="Switch"></a>

## <a name="switch"></a>Opzione

iOS USA `UISwitch` come input booleano che può essere rappresentato da un pulsante di opzione su altre piattaforme. L'utente può modificare il controllo spostando il *cursore* tra le posizioni **on/off** .

 [![](slider-switch-segmented-controls-images/image28a.png "Switch")](slider-switch-segmented-controls-images/image28a.png#lightbox)

L'aspetto dell'opzione può essere personalizzato nella **riquadro delle proprietà** della finestra di progettazione, che consente di controllare lo stato predefinito, i colori **di tinta/** disattivazione e un' **immagine di attivazione/disattivazione**. Questa operazione è illustrata nell'immagine seguente:

 [![](slider-switch-segmented-controls-images/image29a.png "Switch Properties")](slider-switch-segmented-controls-images/image29a.png#lightbox)

Le proprietà dell'opzione possono essere impostate anche nel codice. ad esempio, il codice seguente visualizzerà un'opzione con il valore predefinito `On` :

```csharp
switch1.On = true;
```

 <a name="Segmented_Controls"></a>

## <a name="segmented-controls"></a>Controlli segmentati

Un controllo segmentato è un modo organizzato per consentire agli utenti di interagire con un numero ridotto di opzioni. Viene steso orizzontalmente e ogni segmento funziona come un pulsante separato. Quando si usa la finestra di progettazione, il controllo segmentato è disponibile nella **casella degli strumenti > controlli**e dovrebbe avere un aspetto simile all'immagine seguente:

 [![](slider-switch-segmented-controls-images/segmentedcontrol.png "Segmented Control")](slider-switch-segmented-controls-images/segmentedcontrol.png#lightbox)

Una funzionalità univoca della finestra di progettazione consente di selezionare ogni segmento singolarmente nell'area di progettazione, come illustrato di seguito:

 [![](slider-switch-segmented-controls-images/segmentedcontrolselection.png "Segmented Control")](slider-switch-segmented-controls-images/segmentedcontrolselection.png#lightbox)

In questo modo è possibile utilizzare il riquadro delle proprietà per controllare con maggiore precisione le proprietà di ogni segmento. È possibile visualizzare le proprietà modificabili nello screenshot seguente:

 [![](slider-switch-segmented-controls-images/segmentedcontrolproperties.png "Segmented Control")](slider-switch-segmented-controls-images/segmentedcontrolproperties.png#lightbox)

Si noti che lo stile del controllo segmentato è stato deprecato in iOS7 e, di conseguenza, le opzioni di modifica in un'applicazione iOS7 non avranno alcun effetto.

## <a name="related-links"></a>Collegamenti correlati

- [Controlli (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [Controller di avviso](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
