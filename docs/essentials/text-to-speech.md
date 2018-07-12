---
title: 'Xamarin.Essentials: sintesi vocale'
description: La classe TextToSpeech in Xamarin.Essentials consente un'applicazione usare incorporato nei motori di sintesi vocale da pronunciare il testo nascosto dal dispositivo, nonché di lingue disponibili di query in grado di supportare il motore.
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 9383411074bc43af1034138aadbb6ac5494c2c01
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815661"
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials: sintesi vocale

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **TextToSpeech** classe consente a un'applicazione usare incorporato nei motori di sintesi vocale da pronunciare il testo nascosto dal dispositivo, nonché di lingue disponibili di query in grado di supportare il motore.

## <a name="using-text-to-speech"></a>Utilizzo della sintesi vocale

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità di sintesi vocale funziona chiamando il `SpeakAsync` metodo con testo e i parametri facoltativi e restituisce il utterance termine. 

```csharp
public async Task SpeakNowDefaultSettings()
{
    await TextToSpeech.SpeakAsync("Hello World");

    // This method will block until utterance finishes.
}

public void SpeakNowDefaultSettings2()
{
    TextToSpeech.SpeakAsync("Hello World").ContinueWith((t) => 
    {
        // Logic that will run after utterance finishes.

    }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

Questo metodo accetta un oggetto CancellationToken facoltativo per arrestare il utterance dopo l'avvio. 
```csharp
CancellationTokenSource cts;
public async Task SpeakNowDefaultSettings()
{
    cts = new CancellationTokenSource();
    await TextToSpeech.SpeakAsync("Hello World", cancelToken: cts.Token);

    // This method will block until utterance finishes.
}

public void CancelSpeech()
{
    if (cts?.IsCancellationRequested ?? false)
        return;

    cts.Cancel();
}
```

Sintesi vocale verrà automaticamente messi in coda le richieste vocali dallo stesso thread. 

```csharp
bool isBusy = false;
public void SpeakMultiple()
{
    isBusy = true;
    Task.Run(async () =>
    {
        await TextToSpeech.SpeakAsync("Hello World 1");
        await TextToSpeech.SpeakAsync("Hello World 2");
        await TextToSpeech.SpeakAsync("Hello World 3");
        isBusy = false;
    });

    // or you can query multiple without a Task:
    Task.WhenAll(
        TextToSpeech.SpeakAsync("Hello World 1"),
        TextToSpeech.SpeakAsync("Hello World 2"),
        TextToSpeech.SpeakAsync("Hello World 3"))
        .ContinueWith((t) => { isBusy = false; }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

### <a name="speech-settings"></a>Impostazioni di riconoscimento vocale

Per maggiore controllo sul modo in cui l'audio viene pronunciata eseguire il backup con `SpeakSettings` che consente di impostare il Volume del passo e delle impostazioni locali.

```csharp
public async Task SpeakNow()
{
    var settings = new SpeakSettings()
        {
            Volume = .75,
            Pitch = 1.0
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

Di seguito sono supportati i valori per questi parametri:

| Parametro | Minimo | Massimo |
| --- | :---: | :---: |
| Passo | 0 | 2.0 |
| Volume | 0 | 1.0 |

### <a name="speech-locales"></a>Impostazioni locali di riconoscimento vocale

Ogni piattaforma sono disponibili le impostazioni locali per pronunciare il testo nascosto in più lingue e le evidenziazioni. Ogni piattaforma ha diversi codici e sui metodi per la definizione di questo, motivo per cui multi-piattaforma di fornisce Essentials `Locale` classe e un modo per eseguire query con `GetLocalesAsync`.

```csharp
public async Task SpeakNow()
{
    var locales = await TextToSpeech.GetLocalesAsync();

    // Grab the first locale
    var locale = locales.FirstOrDefault();

    var settings = new SpeakSettings()
        {
            Volume = .75,
            Pitch = 1.0,
            Locale = locale
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

## <a name="limitations"></a>Limitazioni

- Coda utterance non è garantita se viene chiamato attraverso più thread.
- Riproduzione audio in background non è ufficialmente supportata.

## <a name="api"></a>API

- [Codice sorgente TextToSpeech](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [Documentazione dell'API TextToSpeech](xref:Xamarin.Essentials.TextToSpeech)
