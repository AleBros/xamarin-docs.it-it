---
title: Implementazione di frammenti - procedura dettagliataImplementing Fragments - Walkthrough
description: Questo articolo illustra come usare i frammenti per sviluppare applicazioni Xamarin.Android.This article walks through how to use fragments to develop Xamarin.Android applications.
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: b601fc37cc75dcd43c3688de8d302f0a47a06b35
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027411"
---
# <a name="implementing-fragments---walkthrough"></a>Implementazione di frammenti - procedura dettagliataImplementing fragments - walkthrough

_I frammenti sono componenti modulari autonomi che possono aiutare a risolvere la complessità delle app Android destinate a dispositivi con una varietà di dimensioni dello schermo. Questo articolo illustra come creare e usare frammenti durante lo sviluppo di applicazioni Xamarin.Android.This article walks through how to create and use fragments when developing Xamarin.Android applications._

## <a name="overview"></a>Panoramica

In questa sezione verrà illustrato come creare e usare frammenti in un'applicazione Xamarin.Android.In this section, you'll walk through how to create and use fragments in a Xamarin.Android application. Questa applicazione mostrerà i titoli di diverse opere di William Shakespeare in una lista. Quando l'utente tocca il titolo di una riproduzione, l'app visualizzerà un preventivo da tale riproduzione in un'attività separata:

[![App in esecuzione su un telefono Android in modalità verticale](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Quando il telefono viene ruotato in modalità orizzontale, l'aspetto dell'app cambierà: sia l'elenco delle riproduzioni che le citazioni appariranno nella stessa attività. Quando si seleziona una riproduzione, il preventivo verrà visualizzato nella stessa attività:

[![App in esecuzione su un telefono Android in modalità orizzontale](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

Infine, se l'app è in esecuzione su un tablet:

[![App in esecuzione su un tablet Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

Questa applicazione di esempio può facilmente adattarsi ai diversi fattori di forma e orientamenti con modifiche minime al codice utilizzando frammenti e [layout alternativi](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources).

I dati per l'applicazione saranno presenti in due matrici di stringhe che sono hardcoded nell'app come matrici di stringhe c'è. Ognuna delle matrici fungerà da origine dati per un frammento.  Un array conterrà il nome di alcune opere di Shakespeare, e l'altro array conterrà una citazione da quella commedia. All'avvio dell'app, i nomi `ListFragment`di riproduzione vengono visualizzati in un file . Quando l'utente fa clic `ListFragment`su una riproduzione in , l'applicazione avvierà un'altra attività che visualizzerà il preventivo.

L'interfaccia utente per l'app sarà costituita da due layout, uno per verticale e uno per la modalità orizzontale. In fase di esecuzione, Android determinerà il layout da caricare in base all'orientamento del dispositivo e fornirà tale layout all'attività di cui eseguire il rendering. Tutta la logica per rispondere ai clic dell'utente e visualizzare i dati sarà contenuta in frammenti. Le attività nell'app esistono solo come contenitori che ospiteranno i frammenti.

Questa procedura dettagliata verrà suddivisa in due guide. La [prima parte](./walkthrough.md) si concentrerà sulle parti principali dell'applicazione. Verrà creato un unico set di layout (ottimizzato per la modalità verticale), insieme a due frammenti e due attività:

1. `MainActivity`&nbsp; Questa è l'attività di avvio per l'app.
1. `TitlesFragment`&nbsp; Questo frammento mostrerà un elenco di titoli di opere che sono stati scritti da William Shakespeare. Sarà ospitato da `MainActivity`.
1. `PlayQuoteActivity`si avvierà `PlayQuoteActivity` il in risposta all'utente selezionando una riproduzione in `TitlesFragment`. &nbsp; `TitlesFragment`
1. `PlayQuoteFragment`&nbsp; Questo frammento mostrerà una citazione da un'opera di William Shakespeare. Sarà ospitato da `PlayQuoteActivity`.

Nella [seconda parte di questa procedura dettagliata](./walkthrough-landscape.md) verrà illustrata l'aggiunta di un layout alternativo (ottimizzato per la modalità orizzontale) che visualizzerà entrambi i frammenti sullo schermo. Inoltre, alcune modifiche di codice minori verranno apportate al codice in modo che l'app adatterà il comportamento al numero di frammenti che vengono visualizzati contemporaneamente sullo schermo.

## <a name="related-links"></a>Collegamenti correlati

- [FragmentsWalkthrough (esempio)FragmentsWalkthrough (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fragmentswalkthrough)
- [Panoramica della finestra di progettazione](~/android/user-interface/android-designer/index.md)
- [Implementazione di frammentiImplementing Fragments](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacchetto di supporto](https://developer.android.com/sdk/compatibility-library.html)
