---
title: Aggiornamento di App esistenti per l'API unificata
ms.topic: article
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: a09ba93fe7c3f5ade6b5cafe44fd7ee2b0c33487
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="updating-existing-apps-to-the-unified-api"></a>Aggiornamento di App esistenti per l'API unificata

> [!IMPORTANT]
> **Deprecazione di profilo classico:** con l'aggiunta di nuove piattaforme in xamarin. IOS, si inizierà gradualmente deprecare funzionalità dal profilo classico (monotouch.dll). Ad esempio, l'opzione non NRC (nuovo-ref-count) è stato rimosso. NRC è sempre stato abilitato per unificata tutte le applicazioni (ad esempio NRC non è stato mai un'opzione) e sono presenti problemi noti. Nelle versioni future rimuoverà la possibilità di usare Boehm come il garbage collector. Questo è anche un'opzione non disponibile alle applicazioni unificate. La rimozione completa del supporto classico è pianificata per il passaggio successivo con la versione di xamarin. IOS 10.0.




## <a name="how-to-update-your-apps"></a>Come aggiornare le app

University Xamarin è un video disponibile gratuitamente **l'aggiornamento a iOS Unified API**. Visitare [Xamarin University fulmine lezioni](http://university.xamarin.com/lightninglectures) da controllare.

[ ![](updating-apps-images/xamu-video-sml.png "University Xamarin")](http://university.xamarin.com/lightninglectures)

Esistono tre passaggi per aggiornare le app:

1. Risolvere eventuali avvisi del compilatore nel codice esistente, in particolare quelle relative alle API deprecate.

2. Per aggiornare i file di progetto e gli spazi dei nomi, utilizzare lo strumento di migrazione integrata in Visual Studio per Mac.

3. Correggere gli errori del compilatore relative al nuovo rimanenti [64 tipi](~/cross-platform/macios/nativetypes.md) e [altre API](~/cross-platform/macios/unified/index.md#deprecated-typos) che sono stati modificati. Estrarre [questi suggerimenti](~/cross-platform/macios/unified/updating-tips.md) per ulteriori informazioni su aggiornamenti manuali che potrebbero essere necessarie.

Sono disponibili guide specifiche per ogni prodotto aggiornare le App per le API unificata e supporto a 64 bit:

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[App xamarin](~/cross-platform/macios/unified/updating-ios-apps.md)

App xamarin esistente può essere aggiornata per l'API unificata utilizzando lo strumento di migrazione automatica integrato in Visual Studio per Mac. Alcune correzioni aggiuntive possono essere necessari, quindi come spiegato in [queste istruzioni](~/cross-platform/macios/unified/updating-ios-apps.md) e [suggerimenti](~/cross-platform/macios/unified/updating-tips.md).

###  <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[App Xamarin.Mac](~/cross-platform/macios/unified/updating-mac-apps.md)

App Xamarin.Mac esistenti possono essere aggiornate all'API unificata utilizzando lo strumento di migrazione automatica integrato in Visual Studio per Mac. Alcune correzioni aggiuntive possono essere necessari, quindi come spiegato in [queste istruzioni](~/cross-platform/macios/unified/updating-mac-apps.md) e [suggerimenti](~/cross-platform/macios/unified/updating-tips.md).

###  <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[App xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

Seguire queste istruzioni per aggiornare una soluzione xamarin. Forms esistente con un progetto iOS usare l'API unificata. Supporto API unificato è solo disponibili in xamarin. Forms 1.3 e versioni successive, in modo [le istruzioni](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) anche descritto come aggiornare l'app xamarin. Forms per la versione 1.3. Questi [suggerimenti](~/cross-platform/macios/unified/updating-tips.md) potrebbe consentire l'aggiornamento di qualsiasi codice iOS nativo in un renderer personalizzato o servizi di dipendenza.

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/nativetypes.md)

In questo articolo viene illustrato l'utilizzo di iOS nuovi tipi Unified API Native (nint, nuint, nfloat) in un'applicazione multipiattaforma in cui il codice è condiviso con i dispositivi non iOS, ad esempio Android o sistemi operativi Windows Phone. Fornisce informazioni su quando usare i tipi nativi e offre diverse soluzioni possibili per i casi in cui il nuovo tipo deve essere utilizzato con codice multipiattaforma.

## <a name="update-bindings-to-the-unified-api"></a>Aggiornare le associazioni all'API unificata

I clienti che hanno creato le associazioni alle librerie Objective-C saranno necessario aggiornare il progetto di associazione per riflettere le modifiche nell'API sottostante (in cui alcuni tipi verranno a 64 bit).
Seguire queste istruzioni per [aggiornare un progetto di associazione esistente per supportare l'API unificata](~/cross-platform/macios/unified/update-binding.md).




## <a name="related-links"></a>Collegamenti correlati

- [Aggiornamento delle App iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [L'aggiornamento di App Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [L'aggiornamento di App xamarin. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Aggiornamento delle associazioni](~/cross-platform/macios/unified/update-binding.md)
- [Aggiornamento dei suggerimenti](~/cross-platform/macios/unified/updating-tips.md)
- [Differenze tra API unificata di vs classico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
