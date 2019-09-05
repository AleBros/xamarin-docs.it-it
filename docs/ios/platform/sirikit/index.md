---
title: SiriKit in Novell. iOS
description: Questo articolo illustra come usare SiriKit in un'app Novell. iOS per fornire servizi accessibili all'utente usando Siri in un dispositivo iOS.
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: e3ef6dd857760d722fe84f98250b8db9e398ea2e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287177"
---
# <a name="sirikit-in-xamarinios"></a>SiriKit in Novell. iOS

_Questo articolo illustra come usare SiriKit in un'app Novell. iOS per fornire servizi accessibili all'utente usando Siri in un dispositivo iOS._

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

Questo articolo illustra i concetti chiave che saranno necessari per l'uso di SiriKit in un'app Novell. iOS. Vengono illustrati i nuovi punti di estensione dell'interfaccia utente e il modo in cui funzionano con l'app e il vocabolario utente per aprire un'app in Siri.

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[Implementazione di SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

Questo articolo illustra i passaggi necessari per implementare il supporto SiriKit in un'app Novell. iOS. Lo sviluppatore dovrebbe leggere la guida ai concetti di SiriKit precedente prima di provare ad aggiungere il supporto SiriKit a un'app, in quanto vengono trattati i concetti chiave che saranno necessari per un'implementazione corretta.





## <a name="related-links"></a>Collegamenti correlati

- [Esempio ElizaChat](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-elizachat)
- [Guida alla programmazione di SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Informazioni di riferimento sul Framework Intent](https://developer.apple.com/reference/intents)
- [Informazioni di riferimento sul Framework dell'interfaccia utente Intent](https://developer.apple.com/reference/intentsui)
