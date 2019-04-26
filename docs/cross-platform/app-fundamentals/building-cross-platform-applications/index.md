---
title: Creazione di applicazioni multipiattaforma
description: Questa sezione illustra un riepilogo più di sei parti, come creare applicazioni con la piattaforma di sviluppo Xamarin – da comprendere il funzionamento di Xamarin per la progettazione di App per dispositivi mobili e quindi il test e la distribuzione in diverse app Store.
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
author: asb3993
ms.author: amburns
ms.date: 01/28/2016
ms.openlocfilehash: 683400e24844308769f0562552641216d45e7d11
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61276369"
---
# <a name="building-cross-platform-applications"></a>Creazione di applicazioni multipiattaforma

Sono disponibili due opzioni per condividere codice tra le applicazioni per dispositivi mobili multipiattaforma con: Condividere i progetti di Asset e librerie di classi portabili. Queste opzioni sono [illustrati di seguito](~/cross-platform/app-fundamentals/code-sharing.md); ulteriori informazioni sulle [librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md) e [progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md) è inoltre disponibile.

<a name="Sections" />

 [Panoramica](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [Parte 1: informazioni sulla piattaforma per dispositivi mobili Xamarin](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [Parte 2-architettura](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [Parte 3 – configurazione di una soluzione multipiattaforma Xamarin](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [Parte 4 – gestione di più piattaforme](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [Parte 5-strategie di condivisione del codice pratico](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [Parte 6 - Test e approvazioni per App Store](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies" />

## <a name="case-studies"></a>Case study

I principi descritti in questo documento vengono inseriti nella pratica nell'applicazione di esempio *Tasky*, nonché [pre-compilate applicazioni](https://xamarin.com/prebuilt) simile [CRM Xamarin](https://xamarin.com/prebuilt/#xamarincrm).

 <a name="Tasky" />

### <a name="tasky"></a>Tasky

Tasky è un'applicazione elenco attività semplice per iOS, Android e Windows Phone.
Illustra le nozioni di base della creazione di un'applicazione multipiattaforma con Xamarin e si usa un database SQLite locale.

 [![elenco tasky](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [ ![tasky elenco](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

Leggere il [Tasky Case Study](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md).

## <a name="summary"></a>Riepilogo

Questa sezione vengono introdotti gli strumenti di sviluppo dell'applicazione di Xamarin e viene descritto come compilare applicazioni destinate a più piattaforme per dispositivi mobili.

Illustra un'architettura a più livelli che il codice strutture per essere riusata su più piattaforme e descrive i modelli di software diverse che possono essere utilizzati all'interno di quell'architettura.

Vengono forniti esempi di funzioni di applicazione comuni (ad esempio operazioni su file e rete) e come può essere compilati in modo lo sviluppo multipiattaforma.

Infine, viene brevemente viene illustrato il test e vengono forniti i riferimenti a un case study che inserisce questi principi in azione.

## <a name="related-links"></a>Collegamenti correlati

- [Opzioni di condivisione codice](~/cross-platform/app-fundamentals/code-sharing.md)
- [Case Study: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [App di esempio tasky (github)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Sviluppo di applicazioni per dispositivi mobili Xamarin: Lo sviluppo multipiattaforma C# e concetti di base di xamarin. Forms (Amazon)](http://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/)
- [Sviluppo per dispositivi mobili con C# da Greg Shackles (o ' Reilly)](http://shop.oreilly.com/product/0636920024002.do)
- [Cross-Platform per dispositivi mobili professionale nel C# dal Scott Olson Hunter John, Ben Horgen, Kenny concesso (Wrox)](http://www.wrox.com/WileyCDA/WroxTitle/Professional-Cross-Platform-Mobile-Development-in-C-.productCd-1118157702.html)
