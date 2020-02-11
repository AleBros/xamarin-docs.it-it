---
title: Creazione di applicazioni multipiattaforma
description: Questa sezione illustra in un riepilogo più sei parti, come creare applicazioni con la piattaforma di sviluppo Xamarin, da comprendere il funzionamento di Xamarin per la progettazione di app per dispositivi mobili e quindi il test e la distribuzione nei diversi App Store.
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
author: davidortinau
ms.author: daortin
ms.date: 01/28/2016
ms.openlocfilehash: b3444b962a032ceaeeba36f63ad975b3d80a9f14
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016933"
---
# <a name="building-cross-platform-applications"></a>Creazione di applicazioni multipiattaforma

Sono disponibili due opzioni per la condivisione di codice tra applicazioni per dispositivi mobili multipiattaforma: progetti di asset condivisi e librerie di classi portabili. Queste opzioni sono [illustrate di seguito](~/cross-platform/app-fundamentals/code-sharing.md). sono inoltre disponibili altre informazioni sulle [librerie di classi](~/cross-platform/app-fundamentals/pcl.md) portabili e sui [progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md) .

<a name="Sections" />

 [Panoramica](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [Parte 1: informazioni sulla piattaforma Xamarin mobile](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [Parte 2: architettura](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [Parte 3-configurazione di una soluzione multipiattaforma Xamarin](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [Parte 4: gestione di più piattaforme](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [Parte 5: strategie pratiche di condivisione del codice](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [Parte 6 - Test e approvazioni per App Store](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies" />

## <a name="case-studies"></a>Case Study

I principi illustrati in questo documento sono pratici per le *attività*dell'applicazione di esempio, nonché per [applicazioni predefinite](https://xamarin.com/prebuilt) come [Xamarin CRM](https://xamarin.com/prebuilt/#xamarincrm).

 <a name="Tasky" />

### <a name="tasky"></a>Tasky

Tasky è una semplice applicazione to-do list per iOS, Android e Windows Phone.
Vengono illustrati i concetti di base della creazione di un'applicazione multipiattaforma con Xamarin e viene utilizzato un database SQLite locale.

 Elenco attività [![](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [![elenco attività](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

Leggere il [Case Study di attività](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md).

## <a name="summary"></a>Riepilogo

In questa sezione vengono introdotti gli strumenti di sviluppo delle applicazioni di Xamarin e viene illustrato come creare applicazioni destinate a più piattaforme per dispositivi mobili.

Viene illustrata un'architettura a più livelli che consente di strutturare il codice per il riutilizzo su più piattaforme e vengono descritti diversi modelli software che possono essere utilizzati all'interno dell'architettura.

Gli esempi sono forniti da funzioni di applicazione comuni (ad esempio, operazioni di file e rete) e come possono essere compilate in modo multipiattaforma.

Infine, viene brevemente illustrato il testing e vengono forniti riferimenti a una case study che mette in atto questi principi.

## <a name="related-links"></a>Collegamenti correlati

- [Opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md)
- [Case Study: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [App di esempio Tasky (GitHub)](https://docs.microsoft.com/samples/xamarin/mobile-samples/taskyportable/)
- [Sviluppo di applicazioni per dispositivi mobili Xamarin: C# nozioni fondamentali su più piattaforme e Xamarin.Forms (Amazon)](https://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/)
- [Sviluppo di applicazioni C# per dispositivi mobili con di Greg manette (Reilly)](https://shop.oreilly.com/product/0636920024002.do)
- [Sviluppo di app per dispositivi mobili multipiattaforma professionali in C# di Scott Olson, John Hunter, ben Horgen, Kenny frequentatori (Wrox)](https://www.wrox.com/WileyCDA/WroxTitle/Professional-Cross-Platform-Mobile-Development-in-C-.productCd-1118157702.html)
