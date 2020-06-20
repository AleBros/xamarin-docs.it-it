---
title: Eseguire il rendering di controlli personalizzati nel Visualizzatore anteprima XAML
description: Questo articolo descrive come visualizzare i controlli personalizzati nel Visualizzatore anteprime XAML.
ms.prod: xamarin
ms.assetid: 4D795372-CB8F-48F4-B63D-845E44B261F7
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4f20a0586aee998c10372c60c96577321e697aad
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137267"
---
# <a name="render-custom-controls-in-the-xaml-previewer"></a>Eseguire il rendering di controlli personalizzati nel Visualizzatore anteprima XAML

_I controlli personalizzati a volte non funzionano come previsto nel Visualizzatore anteprima XAML. Usare le indicazioni fornite in questo articolo per comprendere le limitazioni dell'anteprima dei controlli personalizzati._

## <a name="basic-preview-mode"></a>Modalità di anteprima di base

Anche se il progetto non è stato compilato, il Visualizzatore anteprima XAML eseguirà il rendering delle pagine. Fino a quando non si compila, qualsiasi controllo che si basa sul code-behind visualizzerà il tipo di base Xamarin.Forms . Quando il progetto viene compilato, il Visualizzatore anteprima XAML tenterà di visualizzare i controlli personalizzati con il rendering della fase di progettazione abilitato. Se il rendering ha esito negativo, verrà visualizzato il tipo di base Xamarin.Forms .

## <a name="enable-design-time-rendering-for-custom-controls"></a>Abilitare il rendering della fase di progettazione per i controlli personalizzati

Se si crea un controllo personalizzato o si usano i controlli di una libreria di terze parti, il Visualizzatore anteprima potrebbe visualizzarli in modo non corretto. I controlli personalizzati devono acconsentire esplicitamente al rendering della fase di progettazione per essere visualizzati nel Visualizzatore anteprima, indipendentemente dal fatto che il controllo sia stato scritto o importato da una libreria. Con i controlli creati, aggiungere alla [`[DesignTimeVisible(true)]`](xref:System.ComponentModel.DesignTimeVisibleAttribute) classe del controllo per visualizzarlo nel Visualizzatore anteprime:

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

Usare come esempio [la classe di base ImageCirclePlugin's di James Montemagno su](https://github.com/jamesmontemagno/ImageCirclePlugin/blob/master/src/ImageCircle/CircleImage.shared.cs) .

## <a name="skiasharp-controls"></a>Controlli SkiaSharp

Attualmente, i controlli SkiaSharp sono supportati solo quando si esegue l'anteprima in iOS. Non verrà eseguito il rendering nell'anteprima di Android.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="check-your-xamarinforms-version"></a>Controllare la Xamarin.Forms versione
Verificare che sia Xamarin.Forms installato almeno 3,6. È possibile aggiornare la Xamarin.Forms versione in NuGet.

### <a name="even-with-designtimevisibletrue-my-custom-control-isnt-rendering-properly"></a>Anche con `[DesignTimeVisible(true)]` , il controllo personalizzato non esegue correttamente il rendering.
I controlli personalizzati che si basano molto sui dati di code-behind o back-end non funzionano sempre nel Visualizzatore anteprime XAML. Provare questa procedura:

* Il controllo viene spostato in modo che non venga inizializzato se [è abilitata la modalità progettazione](index.md#detect-design-mode)
* Impostazione [dei dati della fase di progettazione](design-time-data.md) per visualizzare i dati falsi dal back-end

### <a name="the-xaml-previewer-shows-the-error-custom-controls-arent-rendering-properly"></a>Il Visualizzatore anteprima XAML mostra l'errore "il rendering dei controlli personalizzati non è corretto"
Provare a pulire e ricompilare il progetto oppure chiudere e riaprire il file XAML.
