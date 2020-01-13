---
title: Riproduzione di suoni in tvOS con AVAudioPlayer in Novell
description: Questo articolo illustra come usare una classe helper per controllare la riproduzione di suoni usando un AVAudioPlayer in un'applicazione Xamarin.iOS.
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 4dddde8d4408df6a9b9d73c0a3efff62f563591a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030768"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>Riproduzione di suoni in tvOS con AVAudioPlayer in Novell

## <a name="about-the-avaudioplayer"></a>Informazioni su AVAudioPlayer

Il `AVAudioPlayer` viene usato per riprodurre i dati audio dalla memoria o da un file. Apple consiglia di usare questa classe per riprodurre l'audio nell'app, a meno che non si esegua lo streaming di rete o non si richieda l'I/O audio a bassa latenza.

È possibile utilizzare il `AVAudioPlayer` per eseguire le operazioni seguenti:

- Riprodurre suoni di qualsiasi durata con ciclo facoltativo.
- Riprodurre più suoni contemporaneamente con la sincronizzazione facoltativa.
- Controllo del volume, della velocità di riproduzione e del posizionamento stereo per ogni riproduzione di suoni.
- Funzionalità di supporto quali l'avanzamento rapido o il riavvolgimento.
- Ottenere i dati di misurazione del livello di riproduzione.

`AVAudioPlayer` supporta i suoni in qualsiasi formato audio fornito da iOS, tvOS e OS X, ad esempio `.aif`, `.wav` o `.mp3`.

## <a name="playing-sounds-in-tvos"></a>Riproduzione di suoni in tvOS

Poiché tvOS supporta le stesse classi della casella degli strumenti audio di iOS, vedere la documentazione di iOS per la [riproduzione di suoni con AVAudioPlayer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) per informazioni dettagliate sulla riproduzione di audio in un'app Xamarin.tvOS.

## <a name="related-links"></a>Collegamenti correlati

- [Riferimento a AVAudioPlayer](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
