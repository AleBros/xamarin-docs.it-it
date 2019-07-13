---
title: L'aggiornamento delle App esistenti per l'API unificata
description: Questo documento include collegamenti alle varie guide che descrivono come aggiornare le applicazioni Xamarin all'API unificata. Vengono illustrati le app xamarin. IOS, le app xamarin. Mac. App xamarin. Forms, i tipi nativi nelle App multipiattaforma e progetti di binding.
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 7730d19e4f261a0e414e4946fd0add5312f5d030
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864365"
---
# <a name="updating-existing-apps-to-the-unified-api"></a>L'aggiornamento delle App esistenti per l'API unificata

> [!IMPORTANT]
> L'API classica Xamarin, che è preceduto l'API unificata, è stato deprecato.
> - L'ultima versione di xamarin. IOS per supportare l'API classica (monotouch) era 9.10 di xamarin. IOS.
> - Xamarin. Mac supporta ancora l'API classica, ma non verrà più aggiornata. Poiché è deprecato, gli sviluppatori devono spostare le proprie applicazioni per l'API unificata.

## <a name="how-to-update-your-apps"></a>Come aggiornare le tue App

Esistono tre passaggi per aggiornare le tue App:

1. Risolvere eventuali avvisi del compilatore nel codice esistente, in particolare quelle relative alle API deprecate.

2. Usare lo strumento di migrazione incorporati Visual Studio per Mac per aggiornare i file di progetto e gli spazi dei nomi.

3. Correggere gli errori del compilatore relative alla nuova rimanenti [64-types](~/cross-platform/macios/nativetypes.md) e [altre API](~/cross-platform/macios/unified/overview.md#deprecated-typos) che sono stati modificati. Consulta [questi suggerimenti](~/cross-platform/macios/unified/updating-tips.md) per altre informazioni sugli aggiornamenti manuali che potrebbero essere necessarie.

Sono disponibili guide specifiche per ogni prodotto aggiornare le tue App per le API unificata e il supporto a 64 bit:

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[App xamarin. IOS](~/cross-platform/macios/unified/updating-ios-apps.md)

Le app xamarin. IOS esistenti possono essere aggiornate per l'API unificata usando lo strumento di migrazione automatica incorporato Visual Studio per Mac. Alcune correzioni aggiuntive potrebbero quindi essere necessarie, come illustrato in [queste istruzioni](~/cross-platform/macios/unified/updating-ios-apps.md) e [suggerimenti](~/cross-platform/macios/unified/updating-tips.md).

### <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[App xamarin. Mac](~/cross-platform/macios/unified/updating-mac-apps.md)

Le app esistenti di xamarin. Mac possono essere aggiornate per l'API unificata usando lo strumento di migrazione automatica incorporato Visual Studio per Mac. Alcune correzioni aggiuntive potrebbero quindi essere necessarie, come illustrato in [queste istruzioni](~/cross-platform/macios/unified/updating-mac-apps.md) e [suggerimenti](~/cross-platform/macios/unified/updating-tips.md).

### <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[App xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

Seguire queste istruzioni per aggiornare una soluzione xamarin. Forms esistente con un progetto iOS per usare l'API unificata. Supporto API unificata è disponibile solo nella versione 1.3 di xamarin. Forms e versioni successive, pertanto [le istruzioni](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) illustrano anche come aggiornare l'app xamarin. Forms alla versione 1.3. Questi [suggerimenti](~/cross-platform/macios/unified/updating-tips.md) può essere utile aggiornare qualsiasi codice iOS nativo nel renderer personalizzati o servizi di dipendenza.

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/nativetypes.md)

Questo articolo illustra l'uso di iOS nuovi tipi Unified API Native (nint, nuint, nfloat) in un'applicazione multipiattaforma in cui il codice è condiviso con i dispositivi non iOS, ad esempio Android o sistemi operativi Windows Phone. Fornisce informazioni su quando usare i tipi nativi e offre diverse soluzioni possibili per i casi in cui il nuovo tipo deve essere utilizzato con codice multipiattaforma.

## <a name="update-bindings-to-the-unified-api"></a>Aggiornare le associazioni per l'API unificata

I clienti che hanno creato le associazioni alle librerie Objective-C dovranno aggiornare il progetto di associazione per riflettere le modifiche nell'API sottostante (in cui alcuni tipi a questo punto sarà a 64 bit).
Per seguire queste istruzioni [aggiornare un progetto di associazione esistente per supportare l'API unificata](~/cross-platform/macios/unified/update-binding.md).

## <a name="related-links"></a>Collegamenti correlati

- [L'aggiornamento delle App iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [L'aggiornamento di App Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [L'aggiornamento di App xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Aggiornamento delle associazioni](~/cross-platform/macios/unified/update-binding.md)
- [L'aggiornamento di suggerimenti](~/cross-platform/macios/unified/updating-tips.md)
- [Visual Studio classico differenze API unificata](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
