---
title: Che cos'è Xamarin?
description: Questo articolo presenta la piattaforma Novell e le librerie correlate.
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
ms.custom: video
author: profexorgeek
ms.author: jusjohns
ms.date: 05/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 708a310ea015f9e678d534898fde18abc3848120
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84198324"
---
# <a name="what-is-xamarin"></a>Che cos'è Xamarin?

[![Screenshot dell'applicazione Novell di esempio in iOS e Android](what-is-xamarin-images/xamarin-app-cropped.png)](what-is-xamarin-images/xamarin-app.png#lightbox)

Xamarin è una piattaforma open source per la compilazione di applicazioni moderne e a prestazioni elevate per iOS, Android e Windows con .NET. Xamarin è un livello di astrazione che gestisce la comunicazione del codice condiviso con il codice della piattaforma sottostante. Xamarin viene eseguito in un ambiente gestito che offre diversi vantaggi, ad esempio l'allocazione della memoria e la Garbage Collection.

Xamarin consente agli sviluppatori di condividere una media del 90% delle applicazioni su più piattaforme. Questo modello consente agli sviluppatori di scrivere tutta la logica di business in un unico linguaggio (o di riutilizzare il codice dell'applicazione esistente), replicando in ogni piattaforma le prestazioni e l'aspetto originari.

Le applicazioni Xamarin possono essere scritte su PC o Mac e vengono compilate in pacchetti dell'applicazione nativa, ad esempio un file **APK** in Android o un file **IPA** in iOS.

> [!NOTE]
> Per la compilazione e la distribuzione di applicazioni per iOS è attualmente necessario un computer MacOS. Per altre informazioni sui requisiti di sviluppo, vedere [Requisiti di sistema](~/cross-platform/get-started/requirements.md#macos-requirements).

## <a name="who-xamarin-is-for"></a>Destinatari di Xamarin

Xamarin è destinato agli sviluppatori con gli obiettivi seguenti:

- Condividere il codice, i test e logica di business tra le piattaforme.
- Scrivere applicazioni multipiattaforma in C# con Visual Studio.

## <a name="how-xamarin-works"></a>Funzionamento di Xamarin

![Diagramma dell'architettura di Xamarin](what-is-xamarin-images/xamarin-architecture.png)

Il diagramma illustra l'architettura generale di un'applicazione Xamarin multipiattaforma. Xamarin consente di creare un'interfaccia utente nativa in ogni piattaforma e di scrivere in C# la logica di business da condividere tra le piattaforme. Nella maggior parte dei casi, l'80% del codice dell'applicazione è condivisibile tramite Xamarin.

Novell è basato su .NET, che gestisce automaticamente le attività, ad esempio l'allocazione della memoria, la Garbage Collection e l'interoperabilità con le piattaforme sottostanti.

Per altre informazioni sull'architettura specifica di una piattaforma, vedere [Xamarin.Android](#xamarinandroid) e [Xamarin.iOS](#xamarinios).

### <a name="added-features"></a>Funzionalità aggiunte

Xamarin offre le capacità delle piattaforme native a cui vanno ad aggiungersi svariate funzionalità, tra cui:

1. **Binding completo per l'SDK sottostante**: Xamarin include binding per quasi tutti gli SDK della piattaforma sottostante in iOS e in Android. Inoltre, questi binding sono fortemente tipizzati, il che significa che sono semplici da esplorare e usare e forniscono un solido controllo del tipo in fase di compilazione e durante lo sviluppo. I binding fortemente tipizzati comportano un minor numero di errori di runtime e applicazioni di qualità superiore.
1. **Interoperabilità con Objective-C, Java, C e C++**: Xamarin offre funzionalità per richiamare direttamente librerie Objective-C, Java, C e C++, assicurando la possibilità di usare un'ampia gamma di codice di terze parti. Queste funzionalità consentono di usare le librerie iOS e Android esistenti scritte in Objective-C, Java o C/C++. Xamarin offre inoltre progetti di binding che consentono di associare librerie native di Objective-C e Java usando la sintassi dichiarativa.
1. **Costrutti di linguaggio moderni**: le applicazioni Xamarin sono scritte in C#, un linguaggio moderno che include miglioramenti significativi rispetto a Objective-C e Java, ad esempio funzionalità del linguaggio dinamiche, costrutti funzionali come espressioni lambda, LINQ, programmazione parallela e altro ancora.
1. **Libreria di classi base solida**: le applicazioni Xamarin usano la libreria di classi base .NET, una vasta raccolta di classi con funzionalità complete e ottimizzate quali il supporto avanzato per XML, database, serializzazione, I/O, stringhe, reti e altro ancora. Il codice C# esistente può essere compilato per l'uso nelle app, garantendo l'accesso a migliaia di librerie che offrono altre funzionalità oltre a quelle della libreria di classi base.
1. **IDE (Integrated Development Environment) moderno**: Xamarin usa Visual Studio, un IDE moderno che include funzionalità come completamento automatico del codice, sistema avanzato di gestione di progetti e soluzioni, libreria di modelli di progetto completa, controllo del codice sorgente integrato e altro ancora.
1. **Supporto multipiattaforma per dispositivi mobili**: Xamarin offre un supporto multipiattaforma sofisticato per le tre principali piattaforme iOS, Android e Windows. È possibile scrivere applicazioni in modo da condividere fino al 90% del codice e Xamarin.Essentials offrire un'API unificata per accedere alle risorse comuni su tutte e tre le piattaforme. Il codice condiviso può ridurre notevolmente i costi di sviluppo e i tempi di immissione sul mercato per gli sviluppatori per dispositivi mobili.

### <a name="xamarinandroid"></a>Xamarin.Android

[![Diagramma dell'architettura di Novell. Android](what-is-xamarin-images/android-architecture-cropped.png)](what-is-xamarin-images/android-architecture.png#lightbox)

Le applicazioni Xamarin.Android vengono compilate da C# nel **linguaggio intermedio (IL)**, che viene quindi compilato in modalità **JIT (Just-In-Time)** in un assembly nativo all'avvio dell'applicazione. Le applicazioni Xamarin.Android vengono eseguite all'interno dell'ambiente di esecuzione di Mono, side-by-side con la macchina virtuale Android Runtime (ART). Xamarin fornisce i binding .NET agli spazi dei nomi Android.* e Java.*. L'ambiente di esecuzione di Mono chiama questi spazi dei nomi tramite **MCW (Managed Callable Wrapper)** e fornisce **ACW (Android Callable Wrapper)** ad ART, consentendo a entrambi gli ambienti di richiamare il codice reciprocamente.

Per altre informazioni, vedere [Architettura di Xamarin.Android](~/android/internals/architecture.md).

### <a name="xamarinios"></a>Xamarin.iOS

[![Diagramma dell'architettura di Novell. iOS](what-is-xamarin-images/ios-architecture-cropped.png)](what-is-xamarin-images/ios-architecture.png#lightbox)

Le applicazioni Xamarin.iOS sono compilate completamente in modalità **AOT (Ahead Of Time)** da C# nel codice dell'assembly ARM nativo. Xamarin usa i **selettori** per esporre Objective-C ai **registrar** C# gestiti e poter esporre il codice C# gestito a Objective-C. I selettori e i registrar sono denominati collettivamente "binding" e consentono la comunicazione tra Objective-C e C#.

Per altre informazioni, vedere [Architettura di Xamarin.iOS](~/ios/internals/architecture.md).

### Xamarin.Essentials

Xamarin.Essentialsè una libreria che fornisce API multipiattaforma per le funzionalità native dei dispositivi. Analogamente a Novell, Xamarin.Essentials è un'astrazione che semplifica il processo di accesso alle funzionalità native. Di seguito sono riportati alcuni esempi di funzionalità disponibili in Xamarin.Essentials :

- Informazioni dispositivo
- File system
- Accelerometro
- Dialer telefono
- Sintesi vocale
- Blocco dello schermo

Per altre informazioni, vedere [Xamarin.Essentials](~/essentials/index.md).

### Xamarin.Forms

Xamarin.Formsè un Framework dell'interfaccia utente open source. Xamarin.Formsconsente agli sviluppatori di compilare applicazioni Novell. iOS, Novell. Android e Windows da una singola codebase condivisa. Xamarin.Formsconsente agli sviluppatori di creare interfacce utente in XAML con code-behind in C#. Queste interfacce utente vengono visualizzate come controlli nativi a prestazioni elevate in ogni piattaforma. Di seguito sono riportati alcuni esempi di funzionalità fornite da Xamarin.Forms :

- Lingua dell'interfaccia utente XAML
- Data binding
- Movimenti
- Effetti
- Stile

Per altre informazioni, vedere [Xamarin.Forms](~/xamarin-forms/index.yml).

## <a name="get-started"></a>Introduzione

Le guide seguenti consentono di compilare la prima app usando Xamarin:

- [Introduzione aXamarin.Forms](~/xamarin-forms/index.yml)
- [Introduzione a Xamarin.Android](~/android/index.yml)
- [Introduzione a Xamarin.iOS](~/ios/index.yml)
- [Introduzione a Xamarin.Mac](~/mac/index.yml)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Series/Xamarin-101/What-is-Xamarin-1-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
