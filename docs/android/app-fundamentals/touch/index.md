---
title: Tocco
description: "Touch screen su molti dei dispositivi moderni consente agli utenti di interagire in modo rapido ed efficiente con i dispositivi in modo naturale e intuitivo. Questa interazione non è limitata alle rilevamento tocco semplice: è possibile utilizzare anche i movimenti. Ad esempio, il movimento zoom con avvicinamento delle dita è un esempio molto comune di questo da parte della schermata con due dita che l'utente è possibile ingrandire o ridurre di compressione. Questa guida esamina tocco e i movimenti in Android."
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: d6c6d7fd02511ede84b7cfa75575d755f11bab99
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="touch"></a>Tocco

_Touch screen su molti dei dispositivi moderni consente agli utenti di interagire in modo rapido ed efficiente con i dispositivi in modo naturale e intuitivo. Questa interazione non è limitata alle rilevamento tocco semplice: è possibile utilizzare anche i movimenti. Ad esempio, il movimento zoom con avvicinamento delle dita è un esempio molto comune di questo da parte della schermata con due dita che l'utente è possibile ingrandire o ridurre di compressione. Questa guida esamina tocco e i movimenti in Android._

## <a name="touch-overview"></a>Panoramica di tocco

iOS e Android sono simili per le modalità di gestione di tocco. Supportano entrambi Multi-touch - numero di punti di contatto nella schermata - e i movimenti complessi. Questa guida presenta alcune analogie dei concetti, nonché la specificità dell'implementazione di tocco e i movimenti in entrambe le piattaforme.

Android Usa un `MotionEvent` oggetto per incapsulare i dati di tocco e metodi per l'oggetto visualizzazione per ascoltare ritocchi.

Oltre all'acquisizione dei dati di tocco, iOS e Android consentono per l'interpretazione degli schemi di ritocchi in movimenti. Questi tipi di riconoscimento di movimento è a sua volta utilizzabile per interpretare i comandi specifici dell'applicazione, ad esempio una rotazione di un'immagine o una volta di una pagina. Android fornisce un numero limitato di movimenti supportati, nonché risorse per rendere l'aggiunta di movimenti di personalizzato complessi semplice.

Se si sta lavorando Android o iOS, la scelta tra elementi e i riconoscitori di movimento può essere uno confusione. È consigliabile che in genere, dovrebbe dare la preferenza per i riconoscitori di movimento. I riconoscitori di movimento sono implementati come classi discrete, che forniscono una maggiore separazione delle problematiche e migliore incapsulamento. Questo rende facile condividere la logica tra diverse visualizzazioni, riducendo al minimo la quantità di codice scritto.

Questa guida segue un formato simile per ogni sistema operativo: innanzitutto, tocco della piattaforma API vengono introdotte e spiegate, così come sono la base sulla quale tocco vengono compilate le interazioni. Quindi, approfondiremo mondo di riconoscitori di movimento: prima di tutto esplorare alcune azioni comuni e completamento della creazione di movimenti personalizzati per le applicazioni. Infine, si noterà come tenere traccia delle dita singole utilizzando il rilevamento di basso livello tocco per creare un programma finger-paint.

## <a name="sections"></a>Sezioni

-  [Tocco in Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [Procedura dettagliata: Utilizzo di tocco in Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [Rilevamento di multi-Touch](touch-tracking.md)

## <a name="summary"></a>Riepilogo

In questa guida viene esaminato tocco in Android. Per entrambi i sistemi operativi, è stato descritto come abilitare touch e come rispondere agli eventi tocco. Successivamente, abbiamo appreso i movimenti e alcuni dei riconoscitori di movimento che forniscono Android e iOS per gestire alcuni degli scenari più comuni. Abbiamo esaminato come creare movimenti personalizzati e implementarli nelle applicazioni. Una procedura dettagliata illustrati i concetti e le API per ogni sistema operativo in azione, quindi è stato anche illustrato come tenere traccia delle dita singoli.



## <a name="related-links"></a>Collegamenti correlati

- [Android toccare avviare (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android tocco finale (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
- [Pittura con le dita (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
