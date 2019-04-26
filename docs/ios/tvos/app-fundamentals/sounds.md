---
title: La riproduzione di suoni in tvOS con AVAudioPlayer in Xamarin
description: Questo articolo illustra come usare una classe helper per controllare la riproduzione di suoni con AVAudioPlayer un in un'applicazione xamarin. IOS.
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: bc1e9febea9529c3fadbacd689404562952276fe
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61414100"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>La riproduzione di suoni in tvOS con AVAudioPlayer in Xamarin

## <a name="about-the-avaudioplayer"></a>Sul AVAudioPlayer

Il `AVAudioPlayer` viene utilizzato per la riproduzione audio dati dalla memoria o un file. Apple consiglia l'uso di questa classe per riprodurre l'audio nell'app a meno che non si esegue il flusso di rete o richiedono i/o audio a bassa latenza.

È possibile usare il `AVAudioPlayer` per eseguire le operazioni seguenti:

- Riprodurre suoni di qualsiasi durata con ciclo facoltativo.
- Riprodurre suoni più allo stesso tempo con la sincronizzazione facoltativo.
- Controllo volume, velocità di riproduzione e il posizionamento stereo per ogni riproduzione di suoni.
- Supportare funzionalità quali o il riavvolgimento rapido.
- Ottenere i dati di controllo a livello di riproduzione.

`AVAudioPlayer` supporta l'audio in qualsiasi formato audio fornito da iOS, tvOS e OS X, ad esempio `.aif`, `.wav` o `.mp3`.

## <a name="playing-sounds-in-tvos"></a>Riproduzione di suoni in tvOS

Poiché tvOS supporta le stesse classi Audio della casella degli strumenti come iOS, vedere di iOS [riproduzione di suoni con AVAudioPlayer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) documentazione per i dettagli completi della fase di riproduzione audio in un'app xamarin. tvos.



## <a name="related-links"></a>Collegamenti correlati

- [Riferimento AVAudioPlayer](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
