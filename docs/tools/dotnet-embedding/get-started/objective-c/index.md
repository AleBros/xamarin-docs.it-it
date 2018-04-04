---
title: Introduzione a Objective-C
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 96afe6bbfd1d9c6f4fd5ca3b7358ef0b89da30bb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-objective-c"></a>Introduzione a Objective-C

Questa è la pagina introduttiva per Objective-C, che illustra le nozioni di base per tutte le piattaforme supportate.


## <a name="requirements"></a>Requisiti

Per usare .NET incorporamento con Objective-C, è necessario un Mac che esegue:

* macOS 10.12 (Sierra) o versione successiva
* Xcode 8.3.2 o versione successiva
* [Mono 5.0](http://www.mono-project.com/download/)

È possibile installare [Visual Studio per Mac](https://www.visualstudio.com/vs/visual-studio-mac/) per modificare e compilare il codice c#.


Note:

* Le versioni precedenti di macOS, Xcode e Mono _potrebbe_ di lavoro, ma sono non testato e non è supportato;
* Generazione di codice può essere eseguita in Windows, ma è possibile eseguire la compilazione in un computer Mac in cui è installato Xcode; solo


## <a name="installation"></a>Installazione

Il passaggio successivo è necessario scaricare e installare .NET incorporamento sul Mac.

* [Pacchetto](https://dl.xamarin.com/embeddinator/Xamarin.Embeddinator-4000-0.2.0.79.pkg)
* [Note sulla versione](https://github.com/mono/Embeddinator-4000/tree/master/docs/releases)

In alternativa è possibile compilarla dal nostro [repository git](https://github.com/mono/Embeddinator-4000/tree/objc), vedere il [che contribuiscono](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md) documento per le istruzioni.

Il programma di installazione è un programma di installazione standard pkg basato su:

![Programma di installazione Introduzione](images/install1.png)
![programma di installazione installa tipo](images/install2.png)
![Riepilogo installazione](images/install3.png)

Una volta installato tramite il programma di installazione dopo avere avviato una nuova sessione terminal, è possibile utilizzare il `objcgen` comando.
In caso contrario è sempre possibile eseguire lo strumento tramite il relativo percorso assoluto: `/Library/Frameworks/Xamarin.Embeddinator-4000.framework/Commands/objcgen`.

## <a name="platforms"></a>Piattaforme

Objective-C è un linguaggio che viene comunemente utilizzato per scrivere applicazioni per Mac OS, iOS, tvOS e watchOS; e il embeddinator supporta tutte le piattaforme. Utilizzo di ogni piattaforma implica alcune differenze fondamentali e questi vengono spiegati [qui](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[Creazione di un'applicazione macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) è più semplice perché non comporta la tanti passaggi aggiuntivi, ad esempio l'impostazione di identità, i profili provisining, simulatori e dispositivi. Vengono fornite informazioni utili per iniziare con il documento macOS prima di quello per iOS.

### <a name="ios--tvos"></a>iOS / tvOS

Verificare che sono già impostati per lo sviluppo di applicazioni iOS prima di tentare di crearlo utilizzando la embeddinator. Il [attenendosi alle istruzioni](~/tools/dotnet-embedding/get-started/objective-c/ios.md) si supponga di aver già completato compilata e distribuita un'applicazione iOS dal computer.

Supporto per tvOS è analogo al funzionamento di iOS, utilizzando solo progetti tvOS in IDE (Visual Studio e Xcode) anziché progetti iOS.

> [!NOTE]
> Nota: Supporto per watchOS sarà disponibile nelle versioni future e sarà molto simile a iOS/tvOS.


## <a name="further-reading"></a>Ulteriori informazioni

* [Funzionalità .NET incorporamento specifiche per Objective-C](~/tools/dotnet-embedding/objective-c/index.md)
* [Procedure consigliate per Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [Limitazioni di incorporamento .NET](~/tools/dotnet-embedding/limitations.md)
* [Che contribuiscono al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Codici di errore e descrizioni](~/tools/dotnet-embedding/errors.md)
* [Piattaforme di destinazione](~/tools/dotnet-embedding/objective-c/platforms.md)


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di meteo (iOS e Mac OS)](https://github.com/jamesmontemagno/embeddinator-weather)
