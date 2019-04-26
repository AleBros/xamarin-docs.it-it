---
title: La riproduzione di suoni con AVAudioPlayer in xamarin. Mac
description: Questo documento descrive come riprodurre suoni con AVAudioPlayer in un'app xamarin. Mac. Vengono illustrati AVAudioPlayer a un livello elevato e collegamenti ad altra documentazione che esamina il più completo.
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 10/19/2016
ms.openlocfilehash: 9aeb7bbfc2fddef1f690b5299ec060c475ea1ce7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61234852"
---
# <a name="playing-sound-with-avaudioplayer-in-xamarinmac"></a>La riproduzione di suoni con AVAudioPlayer in xamarin. Mac

## <a name="about-the-avaudioplayer"></a>Sul AVAudioPlayer

Il `AVAudioPlayer` classe viene utilizzato per la riproduzione audio dati dalla memoria o un file. Apple consiglia l'uso di questa classe per riprodurre l'audio nell'app a meno che non si esegue il flusso di rete o richiedono i/o audio a bassa latenza.

È possibile usare il `AVAudioPlayer` classe per eseguire le operazioni seguenti:

- Riprodurre suoni di qualsiasi durata con ciclo facoltativo.
- Riprodurre suoni più allo stesso tempo con la sincronizzazione facoltativo.
- Controllo volume, velocità di riproduzione e il posizionamento stereo per ogni riproduzione di suoni.
- Supportare funzionalità quali o il riavvolgimento rapido.
- Ottenere i dati di controllo a livello di riproduzione.

`AVAudioPlayer` supporta l'audio in qualsiasi formato audio fornito da iOS, tvOS e macOS, ad esempio. AIF, wav o MP3.

## <a name="playing-sounds-in-macos"></a>Riproduzione di suoni in macOS

Poiché macOS supporta le stesse classi Audio della casella degli strumenti come iOS, vedere di iOS [riproduzione di suoni con AVAudioPlayer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) documentazione per i dettagli completi della fase di riproduzione audio in un'app xamarin. Mac.

## <a name="related-links"></a>Collegamenti correlati

- [Riferimento AVAudioPlayer](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
