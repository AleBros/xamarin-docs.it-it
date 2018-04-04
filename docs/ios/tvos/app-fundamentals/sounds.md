---
title: Riproduzione con AVAudioPlayer
description: In questo articolo viene illustrato come utilizzare una classe helper per controllare la riproduzione di audio usando un AVAudioPlayer.
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: c50aea9c4c35e91c2baa98c94db2fd7c61136d69
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="playing-sound-with-avaudioplayer"></a>Riproduzione con AVAudioPlayer

_In questo articolo viene illustrato come utilizzare una classe helper per controllare la riproduzione di audio usando un AVAudioPlayer._

## <a name="about-the-avaudioplayer"></a>Sul AVAudioPlayer

Il `AVAudioPlayer` utilizzata per la riproduzione audio dati da memoria o un file. Apple consiglia di utilizzare questa classe per riprodurre l'audio nell'app, a meno che si sta eseguendo il flusso di rete o richiedono i/o audio di bassa latenza.

È possibile utilizzare il `AVAudioPlayer` per eseguire le operazioni seguenti:

- Riprodurre suoni durata alla visualizzazione ciclica facoltativo.
- Riprodurre suoni più allo stesso tempo con la sincronizzazione facoltativo.
- Controllare il posizionamento stereo per ogni la riproduzione di suoni, velocità di riproduzione e volume.
- Supportare funzionalità quali o il riavvolgimento rapido.
- Ottenere la riproduzione livello dati di controllo.

`AVAudioPlayer` supporta l'audio in qualsiasi formato audio fornito da iOS, tvOS e OS X come `.aif`, `.wav` o `.mp3`.

## <a name="playing-sounds-in-tvos"></a>Riproduzione di suoni in tvOS

Poiché tvOS supporta le stesse classi Audio della casella degli strumenti come iOS, consultare il nostro iOS [riproduzione di audio con AVAudioPlayer](http://developer.xamarin.com/recipes/ios/media/sound/avaudioplayer/) documentazione per i dettagli completi della fase di riproduzione audio in un'app Xamarin.tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Riferimento AVAudioPlayer](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAudioPlayerClassReference/)
