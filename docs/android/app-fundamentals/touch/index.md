---
title: Tocco e movimenti in xamarin. Android
description: "Touchscreen su molti dei dispositivi moderni consentono agli utenti di interagire in modo rapido ed efficiente con i dispositivi in modo naturale e intuitivo. Questa interazione non è limitata solo al rilevamento di tocco semplice: è possibile usare anche i movimenti. Ad esempio, il movimento di zoom con avvicinamento delle dita è un esempio molto comune di questo oggetto dall'avvicinamento una parte della schermata con due dita, che l'utente può eseguire lo zoom avanti o indietro. Questa guida esamina il tocco e movimenti in Android."
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7f957c9ff5a0e7c3a0821978703860ed2f723a92
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123322"
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>Tocco e movimenti in xamarin. Android

_Touchscreen su molti dei dispositivi moderni consentono agli utenti di interagire in modo rapido ed efficiente con i dispositivi in modo naturale e intuitivo. Questa interazione non è limitata solo al rilevamento di tocco semplice: è possibile usare anche i movimenti. Ad esempio, il movimento di zoom con avvicinamento delle dita è un esempio molto comune di questo oggetto dall'avvicinamento una parte della schermata con due dita, che l'utente può eseguire lo zoom avanti o indietro. Questa guida esamina il tocco e movimenti in Android._

## <a name="touch-overview"></a>Panoramica di tocco

iOS e Android sono simili nei modi in cui gestiscono il tocco. Entrambi possono supportare Multi-touch - numero di punti di contatto nella schermata di - e i movimenti complessi. Questa guida presenta alcune analogie fra i concetti, nonché particolarità di implementazione di tocco e movimenti in entrambe le piattaforme.

Android Usa un `MotionEvent` oggetto per incapsulare i dati di tocco e i metodi sull'oggetto visualizzazione per l'ascolto delle tocchi.

Oltre ad acquisire i dati di tocco, iOS e Android fornisce mezzi per l'interpretazione degli schemi di tocchi in movimenti. I riconoscitori di movimento a sua volta possono essere utilizzati per interpretare comandi specifici dell'applicazione, ad esempio una rotazione di un'immagine o un turno di una pagina. Android fornisce un numero limitato di movimenti supportati, nonché delle risorse per garantire l'aggiunta di movimenti personalizzati complessi semplice.

Se si lavora su Android o iOS, la scelta tra i tocchi e i riconoscitori di movimento può essere uno confuso. Questa Guida suggerisce che in genere, dovrebbe dare la preferenza per i riconoscitori di movimento. I riconoscitori di movimento sono implementati come classi discrete, che forniscono una maggiore separazione delle competenze e un miglior incapsulamento. Questo rende facile condividere la logica tra diverse visualizzazioni, riducendo al minimo la quantità di codice scritto.

Questa guida segue un formato simile per ogni sistema operativo: per prima cosa, tocco della piattaforma API vengono introdotti e spiegate, così come sono la base su cui tocco vengono compilate le interazioni. Quindi, possiamo addentrarci nel mondo dei riconoscitori di movimento – prima di tutto esplorando alcuni movimenti comune, e completamento dell'operazione con la creazione di movimenti personalizzati per le applicazioni. Infine, si noterà come tenere traccia delle singole dita uso del rilevamento di tocco di basso livello per creare un programma finger-paint.

## <a name="sections"></a>Sezioni

-  [Tocco in Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [Procedura dettagliata: Uso del tocco in Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [Rilevamento del multitouch](touch-tracking.md)

## <a name="summary"></a>Riepilogo

In questa guida viene esaminato il tocco in Android. Per entrambi i sistemi operativi, è stato descritto come abilitare il tocco e come rispondere agli eventi di tocco. Successivamente, abbiamo imparato a movimenti e alcuni dei riconoscitori di movimento che forniscono iOS e Android per gestire alcuni degli scenari più comuni. Abbiamo esaminato come creare i movimenti personalizzati e implementarli nelle applicazioni. Una procedura dettagliata illustrati i concetti e le API per ogni sistema operativo in azione ed è stato anche illustrato come tenere traccia delle singole dita.



## <a name="related-links"></a>Collegamenti correlati

- [Android toccare Start (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android Touch finale (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
- [Pittura con le dita (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
