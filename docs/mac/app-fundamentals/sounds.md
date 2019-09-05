---
title: Riproduzione di suoni con AVAudioPlayer in Novell. Mac
description: Questo documento descrive come riprodurre un suono con AVAudioPlayer in un'app Novell. Mac. Viene illustrato AVAudioPlayer a un livello elevato e sono disponibili collegamenti ad altre documentazioni che lo esplorano più completamente.
ms.prod: xamarin
ms.assetid: 4A683A94-F75D-4EAF-8497-E9443653250B
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 10/19/2016
ms.openlocfilehash: b4a5ead3e3c02fbdd2ae5486a6ac637defeb5abd
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283298"
---
# <a name="playing-sound-with-avaudioplayer-in-xamarinmac"></a>Riproduzione di suoni con AVAudioPlayer in Novell. Mac

## <a name="about-the-avaudioplayer"></a>Informazioni su AVAudioPlayer

La `AVAudioPlayer` classe viene utilizzata per riprodurre dati audio da una o più memoria o da un file. Apple consiglia di usare questa classe per riprodurre l'audio nell'app, a meno che non si esegua lo streaming di rete o non si richieda l'I/O audio a bassa latenza.

È possibile utilizzare la `AVAudioPlayer` classe per eseguire le operazioni seguenti:

- Riprodurre suoni di qualsiasi durata con ciclo facoltativo.
- Riprodurre più suoni contemporaneamente con la sincronizzazione facoltativa.
- Controllo del volume, della velocità di riproduzione e del posizionamento stereo per ogni riproduzione di suoni.
- Funzionalità di supporto quali l'avanzamento rapido o il riavvolgimento.
- Ottenere i dati di misurazione del livello di riproduzione.

`AVAudioPlayer`supporta i suoni in qualsiasi formato audio fornito da iOS, tvOS e macOS, ad esempio. AIF,. wav o. mp3.

## <a name="playing-sounds-in-macos"></a>Riproduzione di suoni in macOS

Dato che macOS supporta le stesse classi della casella degli strumenti audio di iOS, vedere la documentazione di iOS per la [riproduzione di suoni con AVAudioPlayer](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/sound/avaudioplayer) per informazioni dettagliate sulla riproduzione di audio in un'app Novell. Mac.

## <a name="related-links"></a>Collegamenti correlati

- [Riferimento a AVAudioPlayer](https://developer.apple.com/documentation/avfoundation/avaudioplayer)
