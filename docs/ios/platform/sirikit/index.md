---
title: SiriKit
description: In questo articolo viene illustrato come utilizzare SiriKit in un'app xamarin di fornire servizi sono accessibili all'utente l'utilizzo di Siri nel dispositivo iOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 9d7773be1244b0ba4e1a57c8a1efbddf02396138
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="sirikit"></a>SiriKit

_In questo articolo viene illustrato come utilizzare SiriKit in un'app xamarin di fornire servizi sono accessibili all'utente l'utilizzo di Siri nel dispositivo iOS._

Nuovo in iOS 10, SiriKit consente a un'app iOS fornire servizi che sono accessibili all'utente mediante Siri e l'app esegue il mapping in un dispositivo iOS mediante le estensioni App e il nuovo **intenti** e **dell'interfaccia utente intenti** Framework.

Siri funziona con il concetto di **domini**, sapere di gruppi di azioni per attività correlate. Ogni interazione di un'app con Siri deve assumere uno dei domini servizio noto come indicato di seguito:

- Audio o video chiamata.
- Prenotazione di un interscambio.
- Gestione allenamenti.
- Messaggistica.
- Ricerca foto.
- Inviare o ricevere i pagamenti.

Quando l'utente effettua una richiesta di Siri che include uno dei servizi di un'estensione App, SiriKit invia l'estensione un **finalità** oggetto che descrive la richiesta dell'utente insieme a eventuali dati di supporto. L'estensione dell'App, quindi genera appropriata **risposta** dell'oggetto per il determinato **finalità**, che riporta in dettaglio come l'estensione può gestire la richiesta.

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[Informazioni sui concetti di SiriKit](~/ios/platform/sirikit/understanding-sirikit.md)

In questo articolo descrive i concetti chiavi che verrà richiesto per l'utilizzo di SiriKit in un'app xamarin. IOS. Vengono illustrate la nuova tipi e tipi di punti di estensione dell'interfaccia utente e sul loro utilizzo con App e il vocabolario di utente per aprire un'app a Siri.

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[Implementazione di SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

In questo articolo vengono illustrati i passaggi necessari per implementare il supporto di SiriKit in un'App xamarin. IOS. Lo sviluppatore deve leggere la Guida di informazioni sui concetti SiriKit sopra prima di tentare di aggiungere il supporto di SiriKit a un'app, come chiave vengono trattati i concetti che sarà necessarie per la corretta implementazione.





## <a name="related-links"></a>Collegamenti correlati

- [Esempio ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guida per programmatori SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Tipi di riferimento di Framework](https://developer.apple.com/reference/intents)
- [Tipi di riferimento di Framework dell'interfaccia utente](https://developer.apple.com/reference/intentsui)
