---
title: SiriKit in Xamarin.iOS
description: Questo articolo illustra come usare SiriKit in un'app Xamarin.iOS per fornire servizi accessibili all'utente usando Siri in un dispositivo iOS.
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 90d3a3b6a1e3a3c20ba8cf2ed1f12f234e3af33b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031478"
---
# <a name="sirikit-in-xamarinios"></a>SiriKit in Xamarin.iOS

_Questo articolo illustra come usare SiriKit in un'app Xamarin.iOS per fornire servizi accessibili all'utente usando Siri in un dispositivo iOS._

Una novità di iOS 10, SiriKit consente a un'app per iOS di fornire servizi accessibili all'utente usando Siri e l'app Maps in un dispositivo iOS usando le estensioni delle app e **i nuovi Framework** **dell'interfaccia utente** Intent e Intent.

Siri funziona con il concetto di **domini**, gruppi di azioni di conoscenza per le attività correlate. Ogni interazione di un'app con Siri deve rientrare in uno dei relativi domini di servizio noti, come indicato di seguito:

- Chiamata audio o video.
- Prenotare una corsa.
- Gestione degli allenamenti.
- Messaggistica.
- Ricerca di foto.
- Invio o ricezione dei pagamenti.

Quando l'utente effettua una richiesta di Siri che interessa uno dei servizi di un'estensione dell'app, SiriKit invia all'estensione un oggetto **preventivo** che descrive la richiesta dell'utente insieme a tutti i dati di supporto. L'estensione dell'app genera quindi l'oggetto **Response** appropriato per la **finalità**specificata, in cui viene illustrato in dettaglio il modo in cui l'estensione può gestire la richiesta.

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[Informazioni sui concetti di SiriKit](~/ios/platform/sirikit/understanding-sirikit.md)

Questo articolo illustra i concetti chiave che saranno necessari per l'uso di SiriKit in un'app Xamarin.iOS. Vengono illustrati i nuovi punti di estensione dell'interfaccia utente e il modo in cui funzionano con l'app e il vocabolario utente per aprire un'app in Siri.

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[Implementazione di SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

Questo articolo illustra i passaggi necessari per implementare il supporto SiriKit in un'app Xamarin.iOS. Lo sviluppatore dovrebbe leggere la guida ai concetti di SiriKit precedente prima di provare ad aggiungere il supporto SiriKit a un'app, in quanto vengono trattati i concetti chiave che saranno necessari per un'implementazione corretta.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio ElizaChat](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-elizachat)
- [Guida alla programmazione di SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Informazioni di riferimento sul Framework Intent](https://developer.apple.com/reference/intents)
- [Informazioni di riferimento sul Framework dell'interfaccia utente Intent](https://developer.apple.com/reference/intentsui)
