---
title: Implementazione di frammenti - procedura dettagliata
description: In questo articolo illustra in dettaglio come usare frammenti per sviluppare applicazioni di xamarin. Android.
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: 92c68298d7abd2570efd89e12d7cfb6364e90972
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="implementing-fragments---walkthrough"></a>Implementazione di frammenti - procedura dettagliata

_I frammenti sono componenti autonomi, modulari che possono contribuire a gestire la complessità delle App Android destinate a dispositivi con un'ampia gamma di dimensioni dello schermo. In questo articolo illustra in dettaglio come creare e usare frammenti durante lo sviluppo di applicazioni di xamarin. Android._

## <a name="overview"></a>Panoramica

In questa sezione verranno esaminati come creare e utilizzare i frammenti in un'applicazione di xamarin. Android. L'applicazione visualizzerà i titoli di riproduzione diversi da William Shakespeare in un elenco. Quando l'utente tocca sul titolo di un gioco, l'applicazione visualizzerà una virgoletta da tale play in un'attività separata:

[![App in esecuzione in un telefono Android in modalità verticale](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Quando il telefono viene ruotato in orizzontale, viene modificato l'aspetto dell'app: sia l'elenco di riproduzione e le virgolette verrà visualizzati nella stessa attività. Quando è selezionata una riproduzione, l'offerta sarà visualizzato nella stessa attività:

[![App in esecuzione in un telefono Android in modalità orizzontale](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

Infine, se l'app è in esecuzione su un tablet:

[![App in esecuzione su un tablet Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

Questa applicazione di esempio può adattare facilmente i diversi fattori di forma e gli orientamenti con modifiche minime al codice usando frammenti e [layout alternativi](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources).

I dati per l'applicazione saranno disponibile in due matrici di stringhe che sono hardcoded nell'app come matrici di stringhe in c#. Ognuna delle matrici fungerà da origine dati per un frammento.  Una matrice conterrà il nome di alcuni svolto da considerato e l'altra matrice conterrà una virgoletta da tale play. Se l'app viene avviata, visualizzerà i nomi di riproduzione in un `ListFragment`. Quando l'utente fa clic sul gioco nel `ListFragment`, l'app verrà avviata un'altra attività che verrà visualizzato l'offerta.

L'interfaccia utente per l'app sarà costituito da due layout, uno per verticale e uno per la modalità orizzontale. In fase di esecuzione Android determinerà il layout per caricare in base all'orientamento del dispositivo e fornirà tale layout per l'attività per eseguire il rendering. Tutta la logica per risponde ai clic dell'utente e la visualizzazione dei dati saranno contenuti in frammenti. Le attività nell'app esistono solo come contenitori che ospiteranno i frammenti.

Questa procedura dettagliata verrà suddivisi in due guide. Il [prima parte](./walkthrough.md) si basano sulle parti principali dell'applicazione. Verrà creato un singolo set di layout (ottimizzato per la modalità verticale), insieme ai due frammenti e due attività:

1. `MainActivity` &nbsp; Questo è l'attività di avvio per l'app.
1. `TitlesFragment` &nbsp; Questo frammento verrà visualizzato un elenco dei titoli di riproduzione che sono state progettate William Shakespeare. Questa verrà ospitata da `MainActivity`.
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` verrà avviata il `PlayQuoteActivity` in risposta all'utente la selezione di riproduzione in `TitlesFragment`.
1. `PlayQuoteFragment` &nbsp; Questo frammento visualizzerà una virgoletta da riprodurre dal William Shakespeare. Questa verrà ospitata da `PlayQuoteActivity`.

Il [seconda parte di questa procedura dettagliata](./walkthrough-landscape.md) parlerà aggiunta di un layout diverso (ottimizzato per la modalità orizzontale) che verranno visualizzati entrambi i frammenti sullo schermo. Inoltre, verranno apportate alcune modifiche di codice secondario per il codice in modo che l'app verrà adattare il comportamento corrispondente al numero di frammenti che vengono visualizzati contemporaneamente sullo schermo.

## <a name="related-links"></a>Collegamenti correlati

- [FragmentsWalkthrough (esempio)](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [Panoramica della finestra di progettazione](~/android/user-interface/android-designer/index.md)
- [Implementazione di frammenti](http://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacchetto di supporto](http://developer.android.com/sdk/compatibility-library.html)
