---
title: La riproduzione di audio con AVAudioPlayer in Xamarin.Mac
description: Questo documento viene descritto come riprodurre un suono con AVAudioPlayer in un'app Xamarin.Mac. Viene descritto AVAudioPlayer a un livello elevato e collegamenti ad altra documentazione che Esplora il modo più completo.
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/19/2016
ms.openlocfilehash: 9e5b9ec43189999f8a0aee29eb50221b494e2133
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791855"
---
# <a name="playing-sound-with-avaudioplayer-in-xamarinmac"></a>La riproduzione di audio con AVAudioPlayer in Xamarin.Mac

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
