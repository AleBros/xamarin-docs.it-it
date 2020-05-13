---
title: 'Xamarin.Essentials: Sintesi vocale'
description: La classe TextToSpeech in Xamarin.Essentials consente a un'applicazione di usare i motori di sintesi vocale predefiniti per pronunciare il testo dal dispositivo, nonché per recuperare le lingue disponibili supportate dal motore.
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: abe591d67ea749de4ae9a2f8dadf4df07712691a
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2020
ms.locfileid: "83149718"
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials: Sintesi vocale

La classe **TextToSpeech** consente a un'applicazione di usare i motori di sintesi vocale predefiniti pronunciare il testo dal dispositivo, nonché per recuperare le lingue disponibili supportate dal motore.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-text-to-speech"></a>Uso della sintesi vocale

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La sintesi vocale funziona chiamando il metodo `SpeakAsync` con testo e parametri facoltativi e restituisce il controllo al termine della riproduzione vocale.

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

Questo metodo accetta un `CancellationToken` facoltativo per arrestare la riproduzione vocale dopo l'avvio.

```csharp
CancellationTokenSource cts;
public async Task SpeakNowDefaultSettings()
{
    cts = new CancellationTokenSource();
    await TextToSpeech.SpeakAsync("Hello World", cancelToken: cts.Token);

    // This method will block until utterance finishes.
}

// Cancel speech if a cancellation token exists & hasn't been already requested.
public void CancelSpeech()
{
    if (cts?.IsCancellationRequested ?? true)
        return;

    cts.Cancel();
}
```

La sintesi vocale accoda automaticamente le richieste vocali dallo stesso thread.

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

### <a name="speech-settings"></a>Impostazioni della sintesi vocale

Per un maggiore controllo sul modo in cui l'audio viene pronunciato, usare in combinazione con `SpeechOptions` che consente di impostare il volume, la tonalità e le impostazioni locali.

```csharp
public async Task SpeakNow()
{
    var settings = new SpeechOptions()
        {
            Volume = .75f,
            Pitch = 1.0f
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

Di seguito sono riportati i valori supportati per questi parametri:

| Parametro | Minima | Massimo |
| --- | :---: | :---: |
| Tonalità | 0 | 2.0 |
| Volume | 0 | 1.0 |

### <a name="speech-locales"></a>Impostazioni locali per la voce

Ogni piattaforma supporta impostazioni locali diverse, per pronunciare il testo in lingue e con accenti diversi. Le piattaforme usano codici e modalità diversi per specificare le impostazioni locali e per questo motivo Xamarin.Essentials offre una classe `Locale` multipiattaforma e un modo per recuperarle con `GetLocalesAsync`.

```csharp
public async Task SpeakNow()
{
    var locales = await TextToSpeech.GetLocalesAsync();

    // Grab the first locale
    var locale = locales.FirstOrDefault();

    var settings = new SpeechOptions()
        {
            Volume = .75f,
            Pitch = 1.0f,
            Locale = locale
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

## <a name="limitations"></a>Limitazioni

- L'accodamento delle riproduzioni vocali non è garantito in caso di chiamata su più thread.
- La riproduzione di audio in background non è ufficialmente supportata.

## <a name="api"></a>API

- [Codice sorgente di TextToSpeech](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [Documentazione dell'API TextToSpeech](xref:Xamarin.Essentials.TextToSpeech)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Text-to-Speech-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
