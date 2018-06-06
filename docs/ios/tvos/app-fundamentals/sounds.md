---
title: La riproduzione di suoni in tvOS con AVAudioPlayer in Xamarin
description: In questo articolo viene illustrato come utilizzare una classe helper per controllare la riproduzione di audio usando un AVAudioPlayer in un'applicazione di xamarin. IOS.
ms.prod: xamarin
ms.assetid: E0305572-DC64-48BB-BD97-0A5096E6CA04
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 7d95a8ea6c22c0d897d8ccfe0c2ca401f6523783
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788633"
---
# <a name="playing-sound-in-tvos-with-avaudioplayer-in-xamarin"></a>La riproduzione di suoni in tvOS con AVAudioPlayer in Xamarin

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
