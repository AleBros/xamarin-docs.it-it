---
title: Sintesi vocale Xamarin.Essentials
description: La classe TextToSpeech consente un'applicazione utilizzare incorporato nei motori di sintesi vocale per pronunciare il testo nascosto dal dispositivo, nonché per lingue disponibili di query in grado di supportare il motore.
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: b2c9ed50c48aee6343a20ddb28c49e1bd05d2153
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-text-to-speech"></a>Sintesi vocale Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **TextToSpeech** classe consente a un'applicazione di utilizzare incorporato nei motori di sintesi vocale per pronunciare il testo nascosto dal dispositivo, nonché per lingue disponibili di query in grado di supportare il motore.

## <a name="using-text-to-speech"></a>Utilizzo della sintesi vocale

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità di sintesi vocale funziona chiamando il `SpeakAsync` metodo con testo e i parametri facoltativi e restituisce dopo l'emissione. 

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

Questo metodo accetta in un oggetto CancellationToken facoltativo per interrompere l'emissione avviato. 
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

Sintesi vocale verrà automaticamente inseriti nella coda le richieste di riconoscimento vocale dallo stesso thread. 

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

Per un maggiore controllo sul modo in cui l'audio viene pronunciata sottoporre `SpeakSettings` che consente di impostare il Volume, x e delle impostazioni locali.

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

Ogni piattaforma offre le impostazioni locali per parlare testo nascosto in più lingue e degli accenti. Ogni piattaforma prevede diversi codici e i modi di specificare, motivo per cui Essentials fornisce una libreria multipiattaforma `Locale` classe e un modo per eseguire le query con `GetLocalesAsync`.

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

- Coda di emissione non è garantita se viene chiamato su più thread.
- Riproduzione audio in background non è ufficialmente supportata.

## <a name="api"></a>API

- [Codice sorgente TextToSpeech](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [Documentazione dell'API TextToSpeech](xref:Xamarin.Essentials.TextToSpeech)
