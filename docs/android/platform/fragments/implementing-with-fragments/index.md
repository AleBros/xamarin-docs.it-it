---
title: Implementazione di frammenti-procedura dettagliata
description: Questo articolo illustra come usare i frammenti per sviluppare applicazioni Xamarin.Android.
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/26/2018
ms.openlocfilehash: b601fc37cc75dcd43c3688de8d302f0a47a06b35
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027411"
---
# <a name="implementing-fragments---walkthrough"></a>Implementazione di frammenti-procedura dettagliata

_I frammenti sono componenti indipendenti e modulari che consentono di risolvere la complessità delle app Android destinate ai dispositivi con diverse dimensioni dello schermo. Questo articolo illustra come creare e usare i frammenti durante lo sviluppo di applicazioni Xamarin.Android._

## <a name="overview"></a>Panoramica

Questa sezione illustra come creare e usare i frammenti in un'applicazione Xamarin.Android. Questa applicazione visualizzerà i titoli di diversi giochi di William Shakespeare in un elenco. Quando l'utente tocca il titolo di una riproduzione, l'app visualizzerà una citazione da tale riproduzione in un'attività separata:

[![app in esecuzione in un telefono Android in modalità verticale](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Quando il telefono viene ruotato in modalità orizzontale, l'aspetto dell'app verrà modificato: sia l'elenco di Riproduci che le virgolette verranno visualizzati nella stessa attività. Quando si seleziona una riproduzione, l'offerta verrà visualizzata nella stessa attività:

[![app in esecuzione in un telefono Android in modalità orizzontale](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

Infine, se l'app è in esecuzione in un tablet:

[![app in esecuzione in un Tablet Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

Questa applicazione di esempio può adattarsi facilmente a diversi fattori di forma e orientamenti con modifiche minime al codice usando frammenti e [layout alternativi](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources).

I dati per l'applicazione saranno presenti in due matrici di stringhe hardcoded nell'app come C# matrici di stringhe. Ognuna delle matrici fungerà da origine dati per un frammento.  Una matrice conterrà il nome di alcune opere riprodotte da Shakespeare e l'altra matrice conterrà un preventivo da tale riproduzione. All'avvio dell'app vengono visualizzati i nomi di riproduzione in un `ListFragment`. Quando l'utente fa clic su una riproduzione nell'`ListFragment`, l'app avvierà un'altra attività che visualizzerà l'offerta.

L'interfaccia utente per l'app è costituita da due layout, uno per il ritratto e uno per la modalità orizzontale. In fase di esecuzione, Android determinerà il layout da caricare in base all'orientamento del dispositivo e fornirà tale layout all'attività per il rendering. Tutta la logica per rispondere ai clic dell'utente e visualizzare i dati sarà contenuta nei frammenti. Le attività nell'app esistono solo come contenitori che ospitano i frammenti.

Questa procedura dettagliata verrà suddivisa in due guide. La [prima parte](./walkthrough.md) si concentra sulle parti principali dell'applicazione. Verrà creato un singolo set di layout (ottimizzato per la modalità verticale) insieme a due frammenti e due attività:

1. `MainActivity` &nbsp; questa è l'attività di avvio per l'app.
1. `TitlesFragment` &nbsp; in questo frammento verrà visualizzato un elenco di titoli di riproduzione scritti da William Shakespeare. Verrà ospitato da `MainActivity`.
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` avvierà il `PlayQuoteActivity` in risposta all'utente che seleziona una riproduzione in `TitlesFragment`.
1. `PlayQuoteFragment` &nbsp; questo frammento visualizzerà una citazione di William Shakespeare. Verrà ospitato da `PlayQuoteActivity`.

Nella [seconda parte di questa procedura dettagliata](./walkthrough-landscape.md) verrà illustrato come aggiungere un layout alternativo (ottimizzato per la modalità orizzontale) in cui verranno visualizzati entrambi i frammenti sullo schermo. Verranno inoltre apportate alcune modifiche minime al codice in modo che l'app possa adattare il proprio comportamento al numero di frammenti simultaneamente visualizzati sullo schermo.

## <a name="related-links"></a>Collegamenti correlati

- [FragmentsWalkthrough (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/fragmentswalkthrough)
- [Panoramica della finestra di progettazione](~/android/user-interface/android-designer/index.md)
- [Implementazione di frammenti](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacchetto per il supporto](https://developer.android.com/sdk/compatibility-library.html)
