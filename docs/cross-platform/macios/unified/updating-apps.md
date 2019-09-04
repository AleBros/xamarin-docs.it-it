---
title: Aggiornamento delle app esistenti al API unificata
description: Questo documento contiene collegamenti a diverse guide che descrivono come aggiornare le applicazioni Novell al API unificata. Vengono illustrate le app Novell. iOS, Novell. Mac. App Novell. Forms, tipi nativi nelle app multipiattaforma e progetti di associazione.
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 021139c817d2b0eb430a6c8fe09dbd07b7c17813
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226148"
---
# <a name="updating-existing-apps-to-the-unified-api"></a>Aggiornamento delle app esistenti al API unificata

> [!IMPORTANT]
> Il API classica Novell, che precedeva il API unificata, è stato deprecato.
> - L'ultima versione di Novell. iOS per supportare il API classica (MonoTouch. dll) era Novell. iOS 9,10.
> - Novell. Mac supporta ancora la API classica, ma non viene più aggiornata. Poiché è deprecato, gli sviluppatori devono spostare le applicazioni nel API unificata.

## <a name="how-to-update-your-apps"></a>Come aggiornare le app

Per aggiornare le app, è necessario eseguire tre passaggi:

1. Correggere gli avvisi del compilatore nel codice esistente, in particolare quelli relativi alle API deprecate.

2. Usare lo strumento di migrazione incorporato per Visual Studio per Mac aggiornare i file di progetto e gli spazi dei nomi.

3. Correggere gli errori del compilatore rimanenti relativi ai nuovi [tipi 64](~/cross-platform/macios/nativetypes.md) e ad [altre API](~/cross-platform/macios/unified/overview.md#deprecated-typos) che sono state modificate. Vedere [questi suggerimenti](~/cross-platform/macios/unified/updating-tips.md) per ulteriori informazioni sugli aggiornamenti manuali che potrebbero essere necessari.

Per ogni prodotto sono disponibili guide specifiche che consentono di aggiornare le app al supporto di API unificata e a 64 bit:

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[App Novell. iOS](~/cross-platform/macios/unified/updating-ios-apps.md)

Le app Novell. iOS esistenti possono essere aggiornate alla API unificata usando lo strumento di migrazione automatico integrato in per Visual Studio per Mac. Potrebbero essere necessarie alcune correzioni aggiuntive, come descritto in [queste istruzioni](~/cross-platform/macios/unified/updating-ios-apps.md) e [suggerimenti](~/cross-platform/macios/unified/updating-tips.md).

### <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[App Novell. Mac](~/cross-platform/macios/unified/updating-mac-apps.md)

Le app Novell. Mac esistenti possono essere aggiornate alla API unificata usando lo strumento di migrazione automatico integrato in per Visual Studio per Mac. Potrebbero essere necessarie alcune correzioni aggiuntive, come descritto in [queste istruzioni](~/cross-platform/macios/unified/updating-mac-apps.md) e [suggerimenti](~/cross-platform/macios/unified/updating-tips.md).

### <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[App Xamarin.Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

Seguire queste istruzioni per aggiornare una soluzione Novell. Forms esistente con un progetto iOS per usare la API unificata. API unificata supporto è disponibile solo in Novell. Forms 1,3 e versioni successive, quindi [le istruzioni](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md) illustrano anche come aggiornare l'app Novell. Forms alla versione 1,3. Questi [suggerimenti](~/cross-platform/macios/unified/updating-tips.md) possono aiutare ad aggiornare qualsiasi codice iOS nativo in renderer personalizzati o servizi di dipendenza.

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/nativetypes.md)

Questo articolo illustra l'uso dei nuovi tipi nativi iOS API unificata (nint, nuint, nFloat) in un'applicazione multipiattaforma in cui il codice viene condiviso con dispositivi non iOS, ad esempio Android o Windows Phone sistemi operativi. Fornisce informazioni dettagliate su quando usare i tipi nativi e fornisce diverse soluzioni possibili ai casi in cui il nuovo tipo deve essere usato con il codice multipiattaforma.

## <a name="update-bindings-to-the-unified-api"></a>Aggiornare i binding al API unificata

I clienti che hanno creato binding per le librerie Objective-C dovranno aggiornare il progetto di binding in modo da riflettere le modifiche nell'API sottostante, in cui alcuni tipi saranno ora a 64 bit.
Seguire queste istruzioni per [aggiornare un progetto di binding esistente per supportare il API unificata](~/cross-platform/macios/unified/update-binding.md).

## <a name="related-links"></a>Collegamenti correlati

- [Aggiornamento di app iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Aggiornamento di app Mac](~/cross-platform/macios/unified/updating-mac-apps.md)
- [Aggiornamento delle app Novell. Forms](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [Aggiornamento di binding](~/cross-platform/macios/unified/update-binding.md)
- [Aggiornamento suggerimenti](~/cross-platform/macios/unified/updating-tips.md)
- [Differenze rispetto a API unificata classiche](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
