---
title: SiriKit in xamarin. IOS
description: Questo articolo illustra come usare SiriKit in un'app xamarin. IOS per fornire servizi accessibili all'utente l'uso di Siri in un dispositivo iOS.
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 9f7cbb3f7d9e448947ec8163a8660616910e750f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117525"
---
# <a name="sirikit-in-xamarinios"></a>SiriKit in xamarin. IOS

_Questo articolo illustra come usare SiriKit in un'app xamarin. IOS per fornire servizi accessibili all'utente l'uso di Siri in un dispositivo iOS._

Nuovo ai dispositivi iOS 10, SiriKit consente a un'app per iOS fornire servizi accessibili all'utente tramite Siri e all'app mappe su un dispositivo iOS mediante le estensioni App e le nuove **Intent** e **dell'interfaccia utente Intent** Framework.

Siri funziona con il concetto di **domini**, gruppi di conoscono le azioni per le attività correlate. Ogni interazione di un'app con Siri deve rientrare in uno dei domini del servizio noto come indicato di seguito:

- Audio o video chiamata.
- Una questione di prenotazione.
- La gestione di allenamenti.
- Messaggistica.
- Ricerca di foto.
- Inviare o ricevere i pagamenti.

Quando l'utente effettua una richiesta di Siri con uno dei servizi di un'estensione App, SiriKit invia l'estensione di un **finalità** oggetto che descrive la richiesta dell'utente insieme a eventuali dati di supporto. l'estensione dell'App, quindi genera appropriato **risposta** dell'oggetto per il determinato **finalità**, che riporta in dettaglio come l'estensione può gestire la richiesta.

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[Informazioni sui concetti di SiriKit](~/ios/platform/sirikit/understanding-sirikit.md)

Questo articolo illustra i concetti principali che saranno necessari per l'utilizzo di SiriKit in un'app xamarin. IOS. Viene descritto come il nuovo Intent e punti di estensione dell'interfaccia utente Intent e sul relativo funzionamento con App e utente vocabolario per aprire un'app a Siri.

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[Implementazione di SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

Questo articolo illustra i passaggi necessari per implementare il supporto di SiriKit in un'App xamarin. IOS. Lo sviluppatore deve leggere la Guida informazioni sui concetti di SiriKit sopra prima di tentare di aggiungere il supporto di SiriKit a un'app, come chiave vengono trattati i concetti che sarà necessaria per la corretta implementazione.





## <a name="related-links"></a>Collegamenti correlati

- [Esempio ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guida per programmatori di SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Riferimento a Framework Intent](https://developer.apple.com/reference/intents)
- [Riferimento a Framework dell'interfaccia utente Intent](https://developer.apple.com/reference/intentsui)
