---
title: Eseguire il rendering di controlli personalizzati nel Visualizzatore anteprima XAML
description: Questo articolo descrive come visualizzare i controlli personalizzati nel Visualizzatore anteprima XAML.
ms.prod: xamarin
ms.assetid: 4D795372-CB8F-48F4-B63D-845E44B261F7
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: 977c29312e0be8b92f216c224414f9bd03f8562d
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58858971"
---
# <a name="render-custom-controls-in-the-xaml-previewer"></a>Eseguire il rendering di controlli personalizzati nel Visualizzatore anteprima XAML

_In alcuni casi, i controlli personalizzati non funzionano come previsto nel Visualizzatore anteprima XAML. Usare le indicazioni fornite in questo articolo per capire le limitazioni di visualizzazione in anteprima i controlli personalizzati._

## <a name="basic-preview-mode"></a>Modalità di anteprima di base

Anche se ancora stato compilato il progetto, il Visualizzatore anteprima XAML verrà eseguito il rendering delle pagine. Fino a quando non si compila, qualsiasi controllo che si basa su code-behind mostrerà il relativo tipo base di xamarin. Forms. Quando viene compilato il progetto, il Visualizzatore anteprima XAML proverà a mostrare i controlli personalizzati con il rendering in Progettazione abilitato. Se il rendering non riesce, verrà indicato il tipo base di xamarin. Forms.

## <a name="enable-design-time-rendering-for-custom-controls"></a>Abilitare il rendering di fase di progettazione per i controlli personalizzati

Se si creare controlli personalizzati o usare i controlli da una libreria di terze parti, il Visualizzatore anteprima può visualizzarli in modo non corretto. Controlli personalizzati devono acconsentire esplicitamente a rendering in tempo da visualizzare nel Visualizzatore anteprima, se è stato scritto il controllo o averlo importato da una libreria di progettazione. Con i controlli creati, aggiungere il [ `[DesignTimeVisible(true)]` ](xref:System.ComponentModel.DesignTimeVisibleAttribute) alla classe del controllo per visualizzarlo nel Visualizzatore anteprima:

```csharp
namespace MyProject
{
  [DesignTimeVisible(true)]
  public class MyControl : BaseControl
  {
    // Your control's code here
  }

}
```

Uso [classe di base di ImageCirclePlugin di James Montemagno](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs) come esempio.


## <a name="skiasharp-controls"></a>Controlli di SkiaSharp

Attualmente, i controlli di SkiaSharp sono supportati solo quando sta visualizzando l'anteprima in iOS. Essi non eseguono il rendering sull'anteprima in Android.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="check-your-xamarinforms-version"></a>Controllare la versione di xamarin. Forms
Assicurarsi di avere almeno xamarin. Forms 3.6 installato. È possibile aggiornare la versione di xamarin. Forms in NuGet.

### <a name="even-with-designtimevisibletrue-my-custom-control-isnt-rendering-properly"></a>Anche con `[DesignTimeVisible(true)]`, il controllo personalizzato non è il rendering correttamente.
Controlli personalizzati che si basano sempre su dati di code-behind o back-end non funzionano sempre nel Visualizzatore anteprima XAML. È possibile provare:
* Spostamento del controllo in modo che non inizializza se [è abilitata la modalità di progettazione](index.md#detect-design-mode)
* Configurazione di [dati della fase di progettazione](design-time-data.md) per mostrare dati falsi dal back-end

### <a name="the-xaml-previewer-shows-the-error-custom-controls-arent-rendering-properly"></a>Il Visualizzatore anteprima XAML viene illustrato l'errore "Non sono il rendering di controlli personalizzati correttamente"
Riprovare la pulizia e ricompilazione del progetto, o chiudere e riaprire il file XAML.
