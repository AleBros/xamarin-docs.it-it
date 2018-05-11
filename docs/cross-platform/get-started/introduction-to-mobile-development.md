---
title: Introduzione allo sviluppo per dispositivi mobili
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
author: asb3993
ms.author: amburns
ms.date: 03/28/2017
ms.openlocfilehash: 81b9dc62587bec8c5edc468d6bd94f5868c6858e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="introduction-to-mobile-development"></a>Introduzione allo sviluppo per dispositivi mobili

La compilazione di applicazioni per dispositivi mobili può essere un'operazione semplice e rapida come aprire l'IDE, assemblare una serie di componenti, eseguire un breve test e caricare il tutto in App Store. Oppure può essere un processo estremamente complesso che richiede una progettazione preliminare rigorosa, un test di usabilità, il controllo qualità su migliaia di dispositivi, un ciclo di vita completo della versione beta e quindi la distribuzione in numerosi modi diversi.

Questo documento è un'introduzione alla piattaforma Xamarin. Per altre informazioni sul *processo* di compilazione di applicazioni per dispositivi mobili dalla progettazione al test, vedere il documento [Introduzione al ciclo di vita di sviluppo del software per dispositivi mobili](~/cross-platform/get-started/introduction-to-mobile-sdlc.md).

Fare riferimento ai [requisiti di sistema](~/cross-platform/get-started/requirements.md#mac) per assicurarsi di essere in grado di installare Xamarin.

## <a name="introduction-to-xamarin"></a>Introduzione a Xamarin

Quando si prende in considerazione la compilazione di applicazioni iOS e Android, molti pensano che i linguaggi nativi come Objective-C, Swift e Java siano l'unica scelta a disposizione. Tuttavia, negli ultimi anni è emerso un intero nuovo ecosistema di piattaforme per la compilazione di applicazioni per dispositivi mobili.

Xamarin è unico nel suo genere poiché offre un solo linguaggio (C#), una libreria di classi e un runtime utilizzabili su tutte e tre le piattaforme per dispositivi mobili iOS, Android e Windows Phone (il linguaggio nativo di Windows Phone è già C#), garantendo tuttavia la compilazione di applicazioni native (non interpretate) in modo da assicurare prestazioni sufficientemente elevate anche per giochi complessi.

Ognuna di queste piattaforme dispone di un set di funzionalità differenti e ciascuna offre possibilità diverse per scrivere applicazioni native, ovvero applicazioni compilate fino al codice nativo che assicurano la massima interoperabilità con il sottosistema Java sottostante. Ad esempio, alcune piattaforme consentono la compilazione di app solo in HTML e JavaScript, mentre altre sono molto di basso livello e consentono solo il codice C/C++. Alcune piattaforme non fanno uso neanche del toolkit di controllo nativo.

Xamarin è unico nel suo genere, offrendo tutta la potenza delle piattaforme native a cui vanno ad aggiungersi svariate funzionalità avanzate proprie, tra cui:

1.   **Binding completo per l'SDK sottostante**: Xamarin include binding per quasi tutti gli SDK della piattaforma sottostante in iOS e in Android. Inoltre, questi binding sono fortemente tipizzati, il che significa che sono semplici da esplorare e usare e forniscono un solido controllo del tipo in fase di compilazione e durante lo sviluppo. Ciò comporta un minor numero di errori di runtime e applicazioni di qualità superiore.
1.   **Interoperabilità con Objective-C, Java, C e C++**: Xamarin offre funzionalità per richiamare direttamente librerie Objective-C, Java, C e C++, assicurando la possibilità di usare un'ampia gamma di codice di terze parti che è già stato creato. Ciò consente di sfruttare i vantaggi offerti dalle librerie iOS e Android esistenti scritte in Objective-C, Java o C/C++. Xamarin offre inoltre progetti di binding che consentono di associare facilmente librerie native di Objective-C e Java usando la sintassi dichiarativa.
1.   **Costrutti di linguaggio moderni**: le applicazioni Xamarin sono scritte in C#, un moderno linguaggio che include miglioramenti significativi in Objective-C e Java come *funzionalità del linguaggio dinamiche*, *costrutti funzionali* quali *espressioni lambda*, *LINQ*, funzionalità di *programmazione parallela*, *generics* sofisticati e altro ancora.
1.   **Libreria di classi base (BCL) eccezionale**: le applicazioni Xamarin usano la libreria di classi base .NET, una vasta raccolta di classi con funzionalità complete e ottimizzate quali il supporto avanzato per XML, database, serializzazione, IO, stringa e rete. Inoltre, il codice C# esistente può essere compilato per l'uso nelle applicazioni, garantendo l'accesso a migliaia di librerie che consentono di eseguire operazioni non ancora previste nella libreria di classi di base.
1.   **Ambiente di sviluppo integrato (IDE) moderno**: Xamarin usa Visual Studio per Mac in Mac OS X e Visual Studio in Windows. Sono entrambi ambienti di sviluppo integrati moderni che includono funzionalità come completamento automatico del codice, sistema avanzato di gestione di progetti e soluzioni, libreria di modelli di progetto completa, controllo del codice sorgente integrato e molte altre ancora.
1.   **Supporto multipiattaforma per dispositivi mobili**: Xamarin offre un supporto multipiattaforma sofisticato per le tre principali piattaforme per dispositivi mobili iOS, Android e Windows Phone. È possibile scrivere applicazioni per condividere fino al 90% del codice e la libreria Xamarin.Mobile offre un'API unificata per l'accesso alle risorse comuni per tutte e tre le piattaforme. Ciò può ridurre notevolmente i costi di sviluppo e i tempi di immissione sul mercato per gli sviluppatori che scrivono applicazioni per le tre più diffuse piattaforme per dispositivi mobili.


Grazie a questo set di funzionalità avanzate e complete, Xamarin costituisce la soluzione ottimale per gli sviluppatori di applicazioni che vogliono usare un linguaggio e una piattaforma moderni per lo sviluppo di applicazioni per dispositivi mobili multipiattaforma.


> [!NOTE]
> Questa serie di guide introduttive illustra come iniziare a compilare applicazioni iOS e Android. Microsoft offre informazioni sullo [sviluppo della piattaforma UWP (Universal Windows Platform)](https://docs.microsoft.com/windows/uwp/develop/) per tablet e desktop. Per altre informazioni sullo sviluppo multipiattaforma con Xamarin (incluse app UWP per Windows), vedere la guida [Building Cross-Platform Applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) (Compilazione di applicazioni multipiattaforma).



## <a name="how-does-xamarin-work"></a>Funzionamento di Xamarin

Xamarin offre due prodotti commerciali, Xamarin.iOS e Xamarin.Android, entrambi progettati basandosi su *Mono*, una versione open source di .NET Framework basata sugli standard ECMA .NET pubblicati. Mono è sul mercato quasi da quanto lo è .NET Framework e viene eseguito in qualsiasi piattaforma tra cui Linux, Unix, FreeBSD e Mac OS X.

In iOS il compilatore *Ahead Of Time* (*AOT*) di Xamarin compila le applicazioni Xamarin.iOS direttamente nel codice assembly ARM nativo. In Android il compilatore di Xamarin consente di compilare fino al *linguaggio intermedio* (*IL*), che viene quindi compilato *Just-in-Time* (*JIT*) in assembly nativo all'avvio dell'applicazione.

In entrambi i casi le applicazioni Xamarin impiegano un runtime che gestisce automaticamente aspetti quali allocazione di memoria, Garbage Collection, interoperabilità con la piattaforma sottostante e così via.



### <a name="xamariniosdll-and-monoandroiddll"></a>Xamarin.iOS.dll e Mono.Android.dll

Le applicazioni Xamarin sono compilate in base a un subset della libreria di classi base .NET noto come profilo per dispositivi mobili Xamarin. Questo profilo è stato creato in modo specifico per applicazioni per dispositivi mobili e inserito in un pacchetto in MonoTouch.dll e Mono.Android.dll (rispettivamente per iOS e Android). Questo processo è analogo al modo in cui le applicazioni Silverlight (e Moonlight) vengono compilate in base al profilo .NET Silverlight/Moonlight. In realtà, il profilo per dispositivi mobili Xamarin è equivalente al profilo Silverlight 4.0 con una serie di classi aggiunte dalla libreria di classi base.

Per un elenco completo di assembly e classi disponibili, vedere l'[elenco di assembly Xamarin.iOS](~/cross-platform/internals/available-assemblies.md) e l'[elenco di assembly Xamarin.Android](~/cross-platform/internals/available-assemblies.md).

Oltre alla libreria di classi base, questi file con estensione dll includono wrapper per iOS SDK e Android SDK che consentono di richiamare le API SDK sottostanti direttamente da C#.



### <a name="application-output"></a>Output dell'applicazione

Quando vengono compilate applicazioni Xamarin, il risultato è un pacchetto dell'applicazione, ovvero un file con estensione app in iOS o con estensione apk in Android. Questi file non sono distinguibili dai pacchetti dell'applicazione compilati con gli ambienti di sviluppo integrato predefiniti della piattaforma e sono distribuibili esattamente nello stesso modo.



## <a name="getting-started"></a>Introduzione

Dopo avere appreso come funziona Xamarin, è il momento di mettere in pratica le conoscenze acquisite.

Il passaggio successivo consiste nell'iniziare a compilare l'app usando una di queste guide:

* [**Hello, iOS**](~/ios/get-started/hello-ios/index.md)

![](introduction-to-mobile-development-images/ios.png "Hello, iOS")


* [**Hello, Android**](~/android/get-started/hello-android/index.md)

![](introduction-to-mobile-development-images/android.png "Hello, Android")


* [**Introduction to Xamarin.Forms**](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md) (Introduzione a Xamarin.Forms)





## <a name="summary"></a>Riepilogo

Questo documento costituisce un'introduzione alla piattaforma Xamarin. Il passaggio successivo consiste nell'iniziare a usare la prima app compilata. Fare riferimento alle guide [Hello, iOS](~/ios/get-started/hello-ios/index.md), [Hello, Android](~/android/get-started/hello-android/index.md) e [Introduction to Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md) (Introduzione a Xamarin.Forms) per cominciare.


## <a name="related-links"></a>Collegamenti correlati

- [Hello, iOS](~/ios/get-started/hello-ios/index.md)
- [Hello, Android](~/android/get-started/hello-android/index.md)
