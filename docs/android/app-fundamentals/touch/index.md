---
title: Tocco e movimenti in Xamarin.Android
description: Gli schermi touch su molti dei dispositivi attuali consentono agli utenti di interagire in modo rapido ed efficiente con i dispositivi in modo naturale e intuitivo. Questa interazione non è limitata solo al rilevamento semplice del tocco. è anche possibile usare i movimenti. Ad esempio, il gesto di pinch-to-zoom è un esempio molto comune, pizzicando una parte dello schermo con due dita, l'utente può ingrandire o ridurre. Questa guida esamina il tocco e i movimenti in Android.
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 43637d8592631b2732e5922544f52d91947dd3bd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024292"
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>Tocco e movimenti in Xamarin.Android

_Gli schermi touch su molti dei dispositivi attuali consentono agli utenti di interagire in modo rapido ed efficiente con i dispositivi in modo naturale e intuitivo. Questa interazione non è limitata solo al rilevamento semplice del tocco. è anche possibile usare i movimenti. Ad esempio, il gesto di pinch-to-zoom è un esempio molto comune, pizzicando una parte dello schermo con due dita, l'utente può ingrandire o ridurre. Questa guida esamina il tocco e i movimenti in Android._

## <a name="touch-overview"></a>Panoramica sul tocco

iOS e Android sono simili nei modi in cui gestiscono il tocco. Entrambi possono supportare molti punti di contatto a più touch sullo schermo e i movimenti complessi. Questa guida presenta alcune delle analogie nei concetti, nonché le peculiarità dell'implementazione di tocco e movimenti su entrambe le piattaforme.

Android usa un oggetto `MotionEvent` per incapsulare i dati di tocco e i metodi sull'oggetto visualizzazione per restare in ascolto dei tocchi.

Oltre a acquisire i dati di tocco, iOS e Android forniscono i mezzi per interpretare i modelli di tocchi nei movimenti. Questi riconoscitori di movimento possono a sua volta essere usati per interpretare i comandi specifici dell'applicazione, ad esempio una rotazione di un'immagine o una volta una pagina. Android offre alcuni movimenti supportati, oltre alle risorse per semplificare l'aggiunta di movimenti personalizzati complessi.

Indipendentemente dal fatto che si stia lavorando a Android o iOS, la scelta tra i riconoscimenti e i riconoscitori di movimento può essere confusa. Questa guida suggerisce che in generale è necessario assegnare una preferenza ai riconoscitori di movimento. I riconoscitori di movimento vengono implementati come classi discrete, che offrono una maggiore separazione dei problemi e una migliore incapsulamento. In questo modo è più semplice condividere la logica tra visualizzazioni diverse, riducendo al minimo la quantità di codice scritto.

Questa guida segue un formato simile per ogni sistema operativo: prima di tutto, vengono introdotte e spiegate le API Touch della piattaforma, in quanto costituiscono la base in cui vengono compilate le interazioni di tocco. Viene quindi analizzato il mondo dei riconoscitori di movimento, prima esplorando alcuni movimenti comuni e terminando con la creazione di movimenti personalizzati per le applicazioni. Infine, si vedrà come tenere traccia delle singole dita usando il rilevamento del tocco di basso livello per creare un programma di disegno Finger.

## <a name="sections"></a>Sezioni

- [Tocco in Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
- [Procedura dettagliata: uso del tocco in Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
- [Rilevamento multitocco](touch-tracking.md)

## <a name="summary"></a>Riepilogo

In questa guida è stato esaminato il tocco in Android. Per entrambi i sistemi operativi è stato illustrato come abilitare il tocco e come rispondere agli eventi di tocco. A questo punto, sono stati appresi i movimenti e alcuni dei riconoscitori di movimento che sia Android che iOS forniscono per gestire alcuni degli scenari più comuni. È stato esaminato come creare movimenti personalizzati e come implementarli nelle applicazioni. Una procedura dettagliata ha illustrato i concetti e le API per ogni sistema operativo in azione ed è stato anche illustrato come tenere traccia delle singole dita.

## <a name="related-links"></a>Collegamenti correlati

- [Android touch Start (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android touch finale (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
- [FingerPaint (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)
