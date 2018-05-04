---
title: Introduzione a Objective-C
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 1769c2cd5e9f68e80f7f4c0ef5e2393971b659f9
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="getting-started-with-objective-c"></a>Introduzione a Objective-C

Questa è la pagina introduttiva per Objective-C, che illustra le nozioni di base per tutte le piattaforme supportate.

## <a name="requirements"></a>Requisiti

Per usare .NET incorporamento con Objective-C, è necessario utilizzare un Mac che eseguono:

* macOS 10.12 (Sierra) o versione successiva
* Xcode 8.3.2 o versione successiva
* [Mono 5.0](http://www.mono-project.com/download/)

È possibile installare [Visual Studio per Mac](https://www.visualstudio.com/vs/visual-studio-mac/) per modificare e compilare il codice c#.

> [!NOTE]
> * Le versioni precedenti di macOS, Xcode e Mono _potrebbe_ funziona, ma sono non testato e non supportati
> * Generazione di codice può essere eseguita in Windows, ma è possibile eseguire la compilazione in un computer Mac in cui è installato Xcode solo

## <a name="installing-net-embedding-from-nuget"></a>L'installazione di .NET incorporamento da NuGet

Attenersi alla seguente [istruzioni](~/tools/dotnet-embedding/get-started/install/install.md) per installare e configurare .NET incorporamento per il progetto.

Una chiamata di comandi di esempio è elencata nella [macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) e [iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md) guide introduttive.

## <a name="platforms"></a>Piattaforme

Objective-C è un linguaggio che viene comunemente utilizzato per scrivere applicazioni per Mac OS, iOS, tvOS e watchOS; durante l'incorporamento .NET supporta tutte le piattaforme. Utilizzo di ogni piattaforma implica alcune [principali differenze e queste sono illustrate in questo argomento](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[Creazione di un'applicazione macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) è più semplice perché non comporta la tanti passaggi aggiuntivi, ad esempio l'impostazione di identità, i profili provisining, simulatori e dispositivi. Vengono fornite informazioni utili per iniziare con il documento macOS prima di quello per iOS.

### <a name="ios--tvos"></a>iOS / tvOS

Verificare che sono già impostati per sviluppare le applicazioni iOS prima di tentare di crearne uno utilizzando l'incorporamento .NET. Il [attenendosi alle istruzioni](~/tools/dotnet-embedding/get-started/objective-c/ios.md) si supponga di aver già completato compilata e distribuita un'applicazione iOS dal computer.

Supporto per tvOS è analogo al funzionamento di iOS, utilizzando solo progetti tvOS in IDE (Visual Studio e Xcode) anziché progetti iOS.

> [!NOTE]
> Il supporto per watchOS sarà disponibile nelle versioni future e sarà molto simile a iOS/tvOS.

## <a name="further-reading"></a>Ulteriori informazioni

* [Funzionalità .NET incorporamento specifiche per Objective-C](~/tools/dotnet-embedding/objective-c/index.md)
* [Procedure consigliate per Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [Limitazioni di incorporamento .NET](~/tools/dotnet-embedding/limitations.md)
* [Che contribuiscono al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Codici di errore e descrizioni](~/tools/dotnet-embedding/errors.md)
* [Piattaforme di destinazione](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di meteo (iOS e Mac OS)](https://github.com/jamesmontemagno/embeddinator-weather)
