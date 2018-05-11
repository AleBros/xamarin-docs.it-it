---
title: Compilazione incrociata piattaforma applicazioni
description: 'In questa sezione illustra, in sei parti, oltre a un riepilogo delle applicazioni utilizzando la piattaforma di sviluppo di Xamarin: da comprendere il funzionamento di Xamarin per la progettazione di App per dispositivi mobili, test e la distribuzione nei vari archivi di app.'
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
author: asb3993
ms.author: amburns
ms.date: 01/28/2016
ms.openlocfilehash: fba13ab921949cd2361e78535d5ffc96952a1336
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="sharing-code-options"></a>Sharing Code Options

Sono disponibili due opzioni per la condivisione del codice tra applicazioni per dispositivi mobili multipiattaforma: progetti di Asset condivisi e librerie di classi portabili. Queste opzioni sono [descritte di seguito](~/cross-platform/app-fundamentals/code-sharing.md); ulteriori informazioni su [librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md) e [progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md) è disponibile.

<a name="Sections" />

## <a name="building-cross-platform-mobile-apps"></a>Compilazione incrociata App della piattaforma per dispositivi mobili

 [Panoramica](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [Parte 1: informazioni sulla piattaforma Xamarin Mobile](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [Parte 2-architettura](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [Parte 3 – configurazione di una soluzione di piattaforma incrociata di Xamarin](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [Parte 4 – occupa più piattaforme](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [Parte 5 – pratico strategie di condivisione del codice](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [Parte 6 - Test e approvazioni per App Store](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies" />


## <a name="case-studies"></a>Case study

I principi indicati in questo documento vengono inseriti nella pratica nell'applicazione di esempio *Tasky*, così come [pre-compilate applicazioni](https://xamarin.com/prebuilt) come [Xamarin CRM](https://xamarin.com/prebuilt/#xamarincrm).

 <a name="Tasky" />


### <a name="tasky"></a>Tasky

Tasky è un'applicazione di elenco semplice di attività da eseguire per iOS, Android e Windows Phone.
Illustra le nozioni di base di creazione di un'applicazione multipiattaforma con Xamarin e utilizza un database locale di SQLite.

 [![elenco tasky](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) [ ![tasky elenco](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

Lettura di [Tasky Case Study](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md).


## <a name="summary"></a>Riepilogo

In questa sezione vengono introdotti gli strumenti di sviluppo di applicazioni di Xamarin e viene descritto come compilare applicazioni destinate a più piattaforme per dispositivi mobili.

Descrive un'architettura a più livelli che il codice per il riutilizzo strutture in più piattaforme e vengono descritti i modelli diversi di software che possono essere utilizzati all'interno di tale architettura.

Vengono forniti esempi di funzioni comuni di applicazione (ad esempio, operazioni su file e rete) e come può essere compilati in modo multipiattaforma.

Infine, viene brevemente viene illustrato il test che fornisce riferimenti a un case study che inserisce questi principi in azione.



## <a name="related-links"></a>Collegamenti correlati

- [Sharing Code Options](~/cross-platform/app-fundamentals/code-sharing.md)
- [Case Study: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [App di esempio tasky (github)](https://developer.xamarin.com/samples/mobile/TaskyPortable/)
- [Sviluppo di applicazioni mobili di Xamarin: Multipiattaforma in c# e nozioni fondamentali su xamarin. Forms](http://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/))
- [Sviluppo per dispositivi mobile con c# da Greg maniglie (o ' Reilly)](http://shop.oreilly.com/product/0636920024002.do)
- [Professional sviluppo per dispositivi Mobile multipiattaforma in c# da Scott Olson, John Hunter, Ben Horgen, Kenny concesso (Wrox)](http://www.wiley.com/WileyCDA/WileyTitle/productCd-1118157702.html)
