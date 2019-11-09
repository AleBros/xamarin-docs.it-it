---
title: Che cos'è Xamarin?
description: Questo articolo presenta Novell e le librerie correlate.
ms.prod: xamarin
ms.assetid: 33C83E13-F3E5-17B4-6512-207F3D3C5AB6
ms.custom: video
author: profexorgeek
ms.author: jusjohns
ms.date: 09/16/2019
ms.openlocfilehash: 34763804e9833224721ea32f9c7e6200dd5faba7
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842936"
---
# <a name="what-is-xamarin"></a>Che cos'è Xamarin?

[![screenshot dell'applicazione Novell di esempio in iOS e Android](what-is-xamarin-images/xamarin-app-cropped.png)](what-is-xamarin-images/xamarin-app.png#lightbox)

Novell è una piattaforma open source per la creazione di applicazioni moderne e performanti per iOS, Android e Windows con .NET. Novell è un livello di astrazione che gestisce la comunicazione del codice condiviso con il codice di piattaforma sottostante. Novell viene eseguito in un ambiente gestito che fornisce praticità, ad esempio l'allocazione di memoria e la Garbage Collection.

Novell consente agli sviluppatori di condividere una media del 90% delle applicazioni su più piattaforme. Questo modello consente agli sviluppatori di scrivere tutta la logica di business in un unico linguaggio (o riutilizzare il codice dell'applicazione esistente), ma di ottenere prestazioni native, l'aspetto e la sensazione di ogni piattaforma.

Le applicazioni Novell possono essere scritte su PC o Mac e compilate in pacchetti di applicazioni native, ad esempio un file con **estensione APK** in Android o un file con estensione **IPA** in iOS.

> [!NOTE]
> Per la compilazione e la distribuzione di applicazioni per iOS è attualmente necessario un computer MacOS. Per ulteriori informazioni sui requisiti di sviluppo, vedere [requisiti di sistema](~/cross-platform/get-started/requirements.md#macos-requirements).

## <a name="who-xamarin-is-for"></a>Chi Novell è per

Novell è destinata agli sviluppatori con gli obiettivi seguenti:

- Condividi codice, test e logica di business tra le diverse piattaforme.
- Scrivere applicazioni multipiattaforma in C# con Visual Studio.

## <a name="how-xamarin-works"></a>Funzionamento di Novell

![Diagramma dell'architettura Novell](what-is-xamarin-images/xamarin-architecture.png)

Il diagramma mostra l'architettura complessiva di un'applicazione Novell multipiattaforma. Novell consente di creare un'interfaccia utente nativa in ogni piattaforma e scrivere la logica C# di business in condivisa tra le diverse piattaforme. Nella maggior parte dei casi, il 80% del codice dell'applicazione è condivisibile con Novell.

Novell si basa su **mono**, una versione open source del .NET Framework in base agli standard ECMA di .NET. Mono è esistito per quasi tutto il tempo che il .NET Framework e viene eseguito sulla maggior parte delle piattaforme, tra cui Linux, UNIX, FreeBSD e macOS. L'ambiente di esecuzione mono gestisce automaticamente le attività quali l'allocazione della memoria, la Garbage Collection e l'interoperabilità con le piattaforme sottostanti.

Per altre informazioni sull'architettura specifica della piattaforma, vedere [Novell. Android](#xamarinandroid) e [Novell. iOS](#xamarinios).

### <a name="added-features"></a>Funzionalità aggiunte

Novell combina le capacità delle piattaforme native e aggiunge numerose funzionalità, tra cui:

1. **Associazione completa per gli SDK sottostanti** : Novell contiene le associazioni per quasi tutti gli SDK della piattaforma sottostanti in iOS e Android. Inoltre, questi binding sono fortemente tipizzati, il che significa che sono semplici da esplorare e usare e forniscono un solido controllo del tipo in fase di compilazione e durante lo sviluppo. Le associazioni fortemente tipizzate portano a meno errori di runtime e applicazioni di qualità superiore.
1. **Objective-C, Java, C e C++ interoperabilità** : Novell fornisce le funzionalità per richiamare direttamente le librerie Objective-c, Java, c e C++ , offrendo la possibilità di usare un'ampia gamma di codice di terze parti. Questa funzionalità consente di usare le librerie iOS e Android esistenti scritte in Objective-C, Java o C/C++. Inoltre, Novell offre progetti di associazione che consentono di associare le librerie native Objective-C e Java usando una sintassi dichiarativa.
1. **Costrutti di linguaggio moderni** : le applicazioni Novell sono C#scritte in, un linguaggio moderno che include miglioramenti significativi rispetto a Objective-C e Java come le funzionalità del linguaggio dinamico, i costrutti funzionali come le espressioni lambda, LINQ, la programmazione parallela, i generics e altro ancora.
1. **Libreria di classi base robusta** : le applicazioni Novell utilizzano la libreria di classi base .NET, una vasta raccolta di classi con funzionalità complete e semplificate, ad esempio potenti funzionalità di XML, database, serializzazione, i/o, stringhe e rete e altro ancora. Il C# codice esistente può essere compilato per l'uso in un'app, che fornisce l'accesso a migliaia di librerie che aggiungono funzionalità oltre a BCL.
1. **Ambiente di sviluppo integrato (IDE) moderno** : Novell usa Visual Studio, un IDE moderno che include funzionalità quali il completamento automatico del codice, un sofisticato sistema di gestione di progetti e soluzioni, una libreria di modelli di progetto completa, il controllo del codice sorgente integrato e altro ancora.
1. **Supporto multipiattaforma per dispositivi mobili** : Novell offre un supporto multipiattaforma sofisticato per le tre principali piattaforme di iOS, Android e Windows. Le applicazioni possono essere scritte in modo da condividere fino al 90% del codice e Novell. Essentials offre un'API unificata per accedere alle risorse comuni su tutte e tre le piattaforme. Il codice condiviso può ridurre significativamente i costi di sviluppo e i tempi di immissione sul mercato per gli sviluppatori di dispositivi mobili.

### <a name="xamarinandroid"></a>Xamarin.Android

[diagramma dell'architettura ![Novell. Android](what-is-xamarin-images/android-architecture-cropped.png)](what-is-xamarin-images/android-architecture.png#lightbox)

Le applicazioni Novell. Android vengono C# compilate da in **linguaggio intermedio (il)** , che viene quindi compilato in modalità **JIT (just-in-Time)** in un assembly nativo all'avvio dell'applicazione. Le applicazioni Novell. Android vengono eseguite all'interno dell'ambiente di esecuzione mono, affiancate alla macchina virtuale di Android Runtime (ART). Novell fornisce associazioni .NET agli spazi dei nomi Android. * e Java. *. L'ambiente di esecuzione mono chiama questi spazi dei nomi tramite **Managed Wrappers (MCW)** e fornisce **Android Callable Wrapper (ACW)** all'arte, consentendo a entrambi gli ambienti di richiamare codice tra loro.

Per altre informazioni, vedere [architettura di Novell. Android](~/android/internals/architecture.md).

### <a name="xamarinios"></a>Xamarin.iOS

[diagramma dell'architettura ![Novell. iOS](what-is-xamarin-images/ios-architecture-cropped.png)](what-is-xamarin-images/ios-architecture.png#lightbox)

Le applicazioni Novell. iOS sono state compilate in modo completo **(AOT)** dal C# codice assembly ARM nativo. Novell usa i **selettori** per esporre Objective-c C# ai **registrar** e gestiti per C# esporre il codice gestito a Objective-c. I selettori e i registrar sono collettivamente denominati "binding" e consentono C# la comunicazione tra Objective-C e.

Per altre informazioni, vedere [architettura di Novell. iOS](~/ios/internals/architecture.md).

### <a name="xamarinessentials"></a>Xamarin.Essentials

Novell. Essentials è una libreria che fornisce API multipiattaforma per le funzionalità native dei dispositivi. Analogamente a Novell, Novell. Essentials è un'astrazione che semplifica il processo di accesso alle funzionalità native. Di seguito sono riportati alcuni esempi di funzionalità disponibili in Novell. Essentials:

- Informazioni sul dispositivo
- File system
- Accelerometro
- Dialer telefonico
- Sintesi vocale
- Blocco schermo

Per altre informazioni, vedere [Novell. Essentials](~/essentials/index.md).

### <a name="xamarinforms"></a>Xamarin.Forms

Novell. Forms è un Framework dell'interfaccia utente open source. Novell. Forms consente agli sviluppatori di compilare applicazioni iOS, Android e Windows da una singola codebase condivisa. Novell. Forms consente agli sviluppatori di creare interfacce utente in XAML con code- C#behind in. Queste interfacce utente vengono visualizzate come controlli nativi performanti in ogni piattaforma. Di seguito sono riportati alcuni esempi di funzionalità fornite da Novell. Forms:

- Linguaggio dell'interfaccia utente XAML
- Data binding
- Movimenti
- Effetti
- Stile

Per ulteriori informazioni, vedere [Novell. Forms](~/xamarin-forms/index.yml).

## <a name="get-started"></a>Introduzione

Le guide seguenti consentono di compilare la prima app usando Novell:

- [Introduzione a Novell. Forms](~/xamarin-forms/index.yml)
- [Introduzione a Novell. Android](~/android/index.yml)
- [Introduzione a Novell. iOS](~/ios/index.yml)
- [Introduzione a Novell. Mac](~/mac/index.yml)

## <a name="related-video"></a>Video correlati

> [!Video https://channel9.msdn.com/Series/Xamarin-101/What-is-Xamarin-1-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
