---
title: Riproduzione di suoni con AVAudioPlayer
description: In questo articolo viene illustrato come utilizzare una classe helper per controllare la riproduzione di audio usando un AVAudioPlayer.
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/19/2016
ms.openlocfilehash: 25e3285a1da1b6a112629001d5412fdd0788c705
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="playing-sound-with-avaudioplayer"></a>Riproduzione di suoni con AVAudioPlayer

_In questo articolo viene illustrato come utilizzare una classe helper per controllare la riproduzione di audio usando un AVAudioPlayer._

## <a name="about-the-avaudioplayer"></a>Sul AVAudioPlayer

La `AVAudioPlayer` classe viene utilizzata per i dati di riproduzione audio da memoria o un file. Apple consiglia di utilizzare questa classe per riprodurre l'audio nell'app, a meno che si sta eseguendo il flusso di rete o richiedono i/o audio di bassa latenza.

È possibile utilizzare la `AVAudioPlayer` classe per eseguire le operazioni seguenti:

- Riprodurre suoni durata alla visualizzazione ciclica facoltativo.
- Riprodurre suoni più allo stesso tempo con la sincronizzazione facoltativo.
- Controllare il posizionamento stereo per ogni la riproduzione di suoni, velocità di riproduzione e volume.
- Supportare funzionalità quali o il riavvolgimento rapido.
- Ottenere la riproduzione livello dati di controllo.

`AVAudioPlayer` supporta l'audio in qualsiasi formato audio fornita da iOS, tvOS e macOS quali. AIF,. wav o MP3.

## <a name="playing-sounds-in-macos"></a>Riproduzione di suoni in macOS

Poiché macOS supporta le stesse classi Audio della casella degli strumenti come iOS, consultare il nostro iOS [riproduzione di audio con AVAudioPlayer](https://developer.xamarin.com/recipes/ios/media/sound/avaudioplayer/) documentazione per i dettagli completi della fase di riproduzione audio in un'app Xamarin.Mac.



## <a name="related-links"></a>Collegamenti correlati

- [Riferimento AVAudioPlayer](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
