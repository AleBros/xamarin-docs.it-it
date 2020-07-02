---
title: Associa le librerie Swift iOS
description: Questo documento descrive come creare binding C# per il codice Swift, rendendo possibile l'utilizzo di librerie native e CocoaPods in un'applicazione Novell. iOS.
ms.prod: xamarin
ms.assetid: 890EFCCA-A2A2-4561-88EA-30DE3041F61D
ms.technology: xamarin-ios
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: 72ab1d9f10ee308313569528d152d5930a258207
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85852966"
---
# <a name="bind-ios-swift-libraries"></a>Associa le librerie Swift iOS

> [!IMPORTANT]
> Stiamo attualmente analizzando l'utilizzo dell'associazione personalizzata nella piattaforma Novell. Segui [**questo sondaggio**](https://www.surveymonkey.com/r/KKBHNLT) per informare le attività di sviluppo future.

La piattaforma iOS, oltre ai linguaggi e agli strumenti nativi, è in continua evoluzione e sono disponibili numerose librerie di terze parti sviluppate con le offerte più recenti. L'ottimizzazione del riutilizzo del codice e dei componenti è uno degli obiettivi chiave dello sviluppo multipiattaforma. La possibilità di riutilizzare i componenti compilati con Swift è diventata sempre più importante per Novell gli sviluppatori, in quanto la loro popolarità tra gli sviluppatori continua ad aumentare. È possibile che si abbia già familiarità con il processo di associazione di normali librerie [Objective-C](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough) . È ora disponibile documentazione aggiuntiva che descrive il processo di [associazione di un Framework Swift](walkthrough.md), in modo che possano essere utilizzate da un'applicazione Novell nello stesso modo. Lo scopo di questo documento è descrivere un approccio generale per creare un'associazione Swift per Novell.

## <a name="high-level-approach"></a>Approccio di alto livello

Con Novell è possibile associare qualsiasi libreria nativa di terze parti per poter essere utilizzabile da un'applicazione Novell. Swift è il nuovo linguaggio e la creazione dell'associazione per le librerie compilate con questo linguaggio richiede alcuni passaggi aggiuntivi e strumenti. Questo approccio prevede i quattro passaggi seguenti:

1. Compilazione della libreria nativa
1. Preparazione dei metadati di Novell, che consente agli strumenti Novell di generare classi C#
1. Compilazione di una libreria di associazione Novell tramite la libreria nativa e i metadati
1. Utilizzo della libreria di associazione Novell in un'applicazione Novell

Le sezioni seguenti illustrano questi passaggi con ulteriori dettagli.

### <a name="build-the-native-library"></a>Compilare la libreria nativa

Il primo passaggio consiste nel disporre di un Framework Swift nativo pronto con l'intestazione Objective-C creata. Si tratta di un'intestazione generata automaticamente che espone i campi, i metodi e le classi Swift desiderate rendendoli accessibili a Objective-C e infine a C# tramite una libreria di associazione Novell. Questo file si trova all'interno del Framework nel seguente percorso: ** \<FrameworkName> . Framework/Headers/ \<FrameworkName> -Swift. h**. Se l'interfaccia esposta dispone di tutti i membri necessari, è possibile passare al passaggio successivo. In caso contrario, è necessario eseguire ulteriori passaggi per esporre tali membri. L'approccio dipenderà dal fatto che si disponga dell'accesso al codice sorgente Swift Framework:

- Se si ha accesso al codice, è possibile decorare i membri Swift necessari con l' `@objc` attributo e applicare alcune regole aggiuntive per consentire agli strumenti di compilazione di Xcode di tenere presente che questi membri devono essere esposti al mondo Objective-C e all'intestazione.
- Se non si dispone dell'accesso al codice sorgente, è necessario creare un Framework proxy Swift, che esegue il wrapping del Framework Swift originale e definisce l'interfaccia pubblica richiesta dall'applicazione usando l' `@objc` attributo.

### <a name="prepare-the-xamarin-metadata"></a>Preparare i metadati Novell

Il secondo passaggio consiste nel preparare le interfacce di definizione API, che vengono usate da un progetto di associazione per generare classi C#. È possibile creare queste definizioni manualmente o automaticamente mediante lo strumento [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/) e il file di intestazione generato automaticamente ** \<FrameworkName> -Swift. h** sopra menzionato. Una volta generati, i metadati devono essere verificati e convalidati manualmente.

### <a name="build-the-xamarinios-binding-library"></a>Compilare la libreria di binding Novell. iOS

Il terzo passaggio consiste nel creare una libreria di binding Project-Novell. iOS speciale. Fa riferimento ai Framework e ai metadati preparati nel passaggio precedente insieme a eventuali dipendenze aggiuntive su cui è affidato il rispettivo Framework. Gestisce anche il collegamento dei framework nativi a cui si fa riferimento con l'applicazione Novell. iOS che utilizza.

### <a name="consume-the-xamarin-binding-library"></a>Utilizzare la libreria di associazione Novell

Il quarto e ultimo passaggio consiste nel fare riferimento alla libreria di associazione in un'applicazione Novell. iOS. È sufficiente abilitare l'uso della libreria nativa nelle applicazioni Novell. iOS destinate a iOS 12,2 e versioni successive. Per le applicazioni destinate a una versione precedente, sono necessari alcuni passaggi aggiuntivi:

- Aggiungere le dipendenze dylib Swift per il supporto in fase di esecuzione. A partire da iOS 12,2 e Swift 5,1, il linguaggio si è reso stabile e compatibile con l'ABI (Application Binary Interface). Questo è il motivo per cui tutte le applicazioni destinate a una versione precedente di iOS devono includere le dipendenze di Swift dylib usate dal Framework. Usare il [pacchetto NuGet SwiftRuntimeSupport](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/) per includere automaticamente le dipendenze dylib obbligatorie nel pacchetto dell'applicazione risultante.
- Aggiungere la cartella **SwiftSupport** con dylib firmato, che viene convalidato da AppStore durante il processo di caricamento. Il pacchetto deve essere firmato e distribuito ad AppStore Connect usando gli strumenti di Xcode. in caso contrario, verrà rifiutato automaticamente.

## <a name="walkthrough"></a>Procedura dettagliata

L'approccio precedente descrive i passaggi di alto livello necessari per creare un'associazione Swift per Novell. Sono disponibili molti passaggi di basso livello e altri dettagli da tenere in considerazione durante la preparazione di questi binding in pratica, incluso l'adattamento alle modifiche apportate agli strumenti e ai linguaggi nativi. Lo scopo è quello di aiutare a ottenere una conoscenza più approfondita di questo concetto e i passaggi di alto livello necessari per questo processo. Per una guida dettagliata, vedere la documentazione relativa alla [procedura dettagliata per l'associazione di Novell Swift](walkthrough.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Xcode](https://apps.apple.com/us/app/xcode/id497799835)
- [Visual Studio per Mac](https://visualstudio.microsoft.com/downloads)
- [Objective Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/)
- [Verifica dei metadati di Sharpie](https://docs.microsoft.com/xamarin/cross-platform/macios/binding/objective-sharpie/platform/verify)
- [Binding di Objective-C Framework](https://docs.microsoft.com/xamarin/ios/platform/binding-objective-c/walkthrough)
- [Gigya iOS SDK (Swift Framework)](https://developers.gigya.com/display/GD/Swift+SDK)
- [Stabilità 5,1 ABI Swift](https://swift.org/blog/swift-5-1-released/)
- [SwiftRuntimeSupport NuGet](https://www.nuget.org/packages/Xamarin.iOS.SwiftRuntimeSupport/)
- [Automazione di Novell UITest](https://docs.microsoft.com/appcenter/test-cloud/uitest/)
- [Configurazione di Novell. iOS UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [Test Cloud AppCenter](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [Repository del progetto di esempio](https://github.com/xamcat/xamarin-binding-swift-framework)
