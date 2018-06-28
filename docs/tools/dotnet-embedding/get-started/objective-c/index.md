---
title: Guida introduttiva a Objective-C
description: Questo documento descrive come iniziare a utilizzare .NET incorporamento con Objective-C. Vengono illustrati i requisiti, l'installazione di .NET incorporamento da NuGet e piattaforme supportate.
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 02d79103825d150b6e6f5bec7ed3ee1788078312
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066627"
---
# <a name="getting-started-with-objective-c"></a>Guida introduttiva a Objective-C

Questa è la pagina introduttiva per Objective-C, che vengono illustrate le nozioni di base per tutte le piattaforme supportate.

## <a name="requirements"></a>Requisiti

Per usare .NET incorporamento con Objective-C, è necessario utilizzare un Mac che eseguono:

* macOS 10.12 (Sierra) o versione successiva
* Xcode 8.3.2 o versione successiva
* [Mono 5.0](http://www.mono-project.com/download/)

È possibile installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) per modificare e compilare il codice c#.

> [!NOTE]
> * Le versioni precedenti di macOS, Xcode e Mono _potrebbe_ funziona, ma sono non testato e non supportati
> * Generazione di codice può essere eseguita in Windows, ma è possibile eseguire la compilazione in un computer Mac in cui è installato Xcode solo

## <a name="installing-net-embedding-from-nuget"></a>Installazione di .NET incorporamento da NuGet

Attenersi alla seguente [istruzioni](~/tools/dotnet-embedding/get-started/install/install.md) per installare e configurare .NET incorporamento per il progetto.

Una chiamata di comandi di esempio è elencata nella [macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) e [iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md) guide introduttive.

## <a name="platforms"></a>Piattaforme

Objective-C è un linguaggio che viene comunemente utilizzato per scrivere applicazioni per Mac OS, iOS, tvOS e watchOS; durante l'incorporamento .NET supporta tutte le piattaforme. Utilizzo di ogni piattaforma implica alcune [principali differenze e queste sono illustrate in questo argomento](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[Creazione di un'applicazione macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) è più semplice poiché non comporta la come numero di passaggi aggiuntivo, come impostazione di identità, i profili provisining, simulatori e dispositivi. Vengono fornite informazioni utili per iniziare con il documento macOS precedenti a quella per iOS.

### <a name="ios--tvos"></a>iOS / tvOS

Verificare che sono già impostati per sviluppare le applicazioni iOS prima di tentare di crearne uno utilizzando l'incorporamento .NET. Il [attenendosi alle istruzioni accessibili](~/tools/dotnet-embedding/get-started/objective-c/ios.md) presupporre che è stato già compilata e distribuita un'applicazione iOS dal computer.

Supporto per tvOS è analogo al funzionamento di iOS, utilizzando solo progetti tvOS in IDE (Visual Studio e in Xcode) anziché i progetti iOS.

> [!NOTE]
> Il supporto per watchOS sarà disponibile nelle versioni future e sarà molto simile a iOS/tvOS.

## <a name="further-reading"></a>Ulteriori informazioni

* [Funzionalità .NET incorporamento specifiche per Objective-C](~/tools/dotnet-embedding/objective-c/index.md)
* [Procedure consigliate per Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [Limitazioni durante l'incorporamento .NET](~/tools/dotnet-embedding/limitations.md)
* [Che hanno contribuito al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Codici di errore e descrizioni](~/tools/dotnet-embedding/errors.md)
* [Piattaforme di destinazione](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di meteo (iOS & macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
