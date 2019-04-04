---
title: Implementazione di frammenti - procedura dettagliata
description: Questo articolo illustra come usare frammenti per sviluppare applicazioni xamarin. Android.
ms.topic: tutorial
ms.prod: xamarin
ms.assetid: A71E9D87-CB69-10AB-CE51-357A05C76BCD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: 2ff4729e68497391d41521da26917571c146b541
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667265"
---
# <a name="implementing-fragments---walkthrough"></a>Implementazione di frammenti - procedura dettagliata

_I frammenti sono componenti indipendenti e modulari che possono aiutare a risolvere la complessità delle App Android destinate a dispositivi con un'ampia gamma di dimensioni dello schermo. Questo articolo illustra come creare e usare i frammenti quando si sviluppano applicazioni xamarin. Android._

## <a name="overview"></a>Panoramica

In questa sezione illustra come creare e usare i frammenti in un'applicazione xamarin. Android. Questa applicazione visualizzerà i titoli dei diversi svolto da William Shakespeare in un elenco. Quando l'utente tocca il titolo di un gioco, l'app visualizzerà una virgoletta da tale play in un'attività separata:

[![App in esecuzione in un telefono Android in modalità verticale](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Quando il telefono viene ruotato in modalità orizzontale, viene modificato l'aspetto dell'app: verrà visualizzato sia l'elenco di riproduzione e le virgolette nella stessa attività. Quando viene selezionato un gioco, l'offerta sarà visualizzato nella stessa attività:

[![App in esecuzione in un telefono Android in modalità orizzontale](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

Infine, se l'app è in esecuzione su un tablet:

[![App in esecuzione in un tablet Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)

Questa applicazione di esempio può adattare facilmente i diversi fattori di forma e gli orientamenti con modifiche minime al codice usando frammenti e [layout alternativi](/xamarin/android/app-fundamentals/resources-in-android/alternate-resources).

I dati per l'applicazione saranno disponibile in due matrici di stringhe che sono hardcoded nell'app come C# le matrici di stringhe. Ognuna delle matrici fungerà da origine dati per un frammento.  Una matrice conterrà il nome di alcune svolto da Shakespeare, e l'altra matrice conterrà un'offerta da tale play. Durante l'avvio dell'app, verranno visualizzate i nomi di riproduzione in un `ListFragment`. Quando l'utente fa clic su un gioco nel `ListFragment`, l'app verrà avviata un'altra attività che consentirà di visualizzare l'offerta.

L'interfaccia utente per l'app sarà costituito da due layout, uno per verticale e uno per la modalità orizzontale. In fase di esecuzione Android determinerà il layout per il caricamento in base all'orientamento del dispositivo e fornirà tale layout per l'attività per eseguire il rendering. Tutta la logica per rispondere all'interazione dell'utente e la visualizzazione dei dati sarà contenuto nei frammenti. Le attività nell'app esistono solo come contenitori che ospiteranno i frammenti.

Questa procedura dettagliata verrà suddivisi in due guide. Il [prima di tutto parte](./walkthrough.md) sarà incentrata sui componenti principali dell'applicazione. Verrà creato un singolo set di layout (ottimizzato per la modalità verticale), insieme ai due frammenti e due attività:

1. `MainActivity` &nbsp; Si tratta di attività di avvio per l'app.
1. `TitlesFragment` &nbsp; Questo frammento verrà visualizzato un elenco dei titoli di riproduzione che sono stati scritti dal William Shakespeare. Questa verrà ospitata da `MainActivity`.
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` verrà avviata il `PlayQuoteActivity` in risposta all'utente la selezione di un gioco in `TitlesFragment`.
1. `PlayQuoteFragment` &nbsp; Questo frammento verrà visualizzato un'offerta da un gioco da William Shakespeare. Questa verrà ospitata da `PlayQuoteActivity`.

Il [seconda parte di questa procedura dettagliata](./walkthrough-landscape.md) verranno illustrate l'aggiunta di un layout alternativo (ottimizzato per la modalità orizzontale) che consentirà di visualizzare entrambi i frammenti sullo schermo. Inoltre, alcune modifiche al codice secondario sarà al codice in modo che l'app si adatterà il comportamento per il numero di frammenti che vengono visualizzati contemporaneamente sullo schermo.

## <a name="related-links"></a>Collegamenti correlati

- [FragmentsWalkthrough (esempio)](https://developer.xamarin.com/samples/monodroid/FragmentsWalkthrough/)
- [Panoramica della finestra di progettazione](~/android/user-interface/android-designer/index.md)
- [Implementazione di frammenti](https://developer.android.com/guide/topics/fundamentals/fragments.html)
- [Pacchetto di supporto](https://developer.android.com/sdk/compatibility-library.html)
