---
title: Eseguire il rendering di controlli personalizzati nel Visualizzatore anteprima XAML
description: Questo articolo descrive come visualizzare i controlli personalizzati nel Visualizzatore anteprime XAML.
ms.prod: xamarin
ms.assetid: 4D795372-CB8F-48F4-B63D-845E44B261F7
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 03/27/2019
ms.openlocfilehash: 57c0fd540ef42c18462b4f989b21bac5ed05dc04
ms.sourcegitcommit: 6b833f44d5fd8dc7ab7f8546e8b7d383e5a989db
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105989"
---
# <a name="render-custom-controls-in-the-xaml-previewer"></a>Eseguire il rendering di controlli personalizzati nel Visualizzatore anteprima XAML

_I controlli personalizzati a volte non funzionano come previsto nel Visualizzatore anteprima XAML. Usare le indicazioni fornite in questo articolo per comprendere le limitazioni dell'anteprima dei controlli personalizzati._

## <a name="basic-preview-mode"></a>Modalità di anteprima di base

Anche se il progetto non è stato compilato, il Visualizzatore anteprima XAML eseguirà il rendering delle pagine. Fino a quando non si compila, qualsiasi controllo che si basa sul code-behind visualizzerà il tipo Novell. Forms di base. Quando il progetto viene compilato, il Visualizzatore anteprima XAML tenterà di visualizzare i controlli personalizzati con il rendering della fase di progettazione abilitato. Se il rendering ha esito negativo, verrà visualizzato il tipo Novell. Forms di base.

## <a name="enable-design-time-rendering-for-custom-controls"></a>Abilitare il rendering della fase di progettazione per i controlli personalizzati

Se si crea un controllo personalizzato o si usano i controlli di una libreria di terze parti, il Visualizzatore anteprima potrebbe visualizzarli in modo non corretto. I controlli personalizzati devono acconsentire esplicitamente al rendering della fase di progettazione per essere visualizzati nel Visualizzatore anteprima, indipendentemente dal fatto che il controllo sia stato scritto o importato da una libreria. Con i controlli creati, aggiungere [`[DesignTimeVisible(true)]`](xref:System.ComponentModel.DesignTimeVisibleAttribute) alla classe del controllo per visualizzarlo nel Visualizzatore anteprime:

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

### <a name="check-your-xamarinforms-version"></a>Verificare la versione di Novell. Forms
Verificare che sia installato almeno Novell. Forms 3,6. È possibile aggiornare la versione di Novell. Forms in NuGet.

### <a name="even-with-designtimevisibletrue-my-custom-control-isnt-rendering-properly"></a>Anche con `[DesignTimeVisible(true)]`, il controllo personalizzato non esegue correttamente il rendering.
I controlli personalizzati che si basano molto sui dati di code-behind o back-end non funzionano sempre nel Visualizzatore anteprime XAML. È possibile provare:

* Il controllo viene spostato in modo che non venga inizializzato se [è abilitata la modalità progettazione](index.md#detect-design-mode)
* Impostazione [dei dati della fase di progettazione](design-time-data.md) per visualizzare i dati falsi dal back-end

### <a name="the-xaml-previewer-shows-the-error-custom-controls-arent-rendering-properly"></a>Il Visualizzatore anteprima XAML mostra l'errore "il rendering dei controlli personalizzati non è corretto"
Provare a pulire e ricompilare il progetto oppure chiudere e riaprire il file XAML.
