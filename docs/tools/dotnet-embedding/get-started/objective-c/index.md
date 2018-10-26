---
title: Introduzione a Objective-C
description: Questo documento descrive come iniziare a usare .NET incorporamento con Objective-C. Vengono illustrati i requisiti, l'installazione di incorporamento di .NET da NuGet e piattaforme supportate.
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: c7bac0612679131383d3b89f24904c8083fa925b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103101"
---
# <a name="getting-started-with-objective-c"></a>Introduzione a Objective-C

Questa è la pagina introduttiva per Objective-C, che illustra le nozioni di base per tutte le piattaforme supportate.

## <a name="requirements"></a>Requisiti

Per usare .NET incorporamento con Objective-C, è necessario un Mac che esegue:

* macOS 10.12 (Sierra) o versione successiva
* Xcode 8.3.2 o versione successiva
* [Mono 5.0](http://www.mono-project.com/download/)

È possibile installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) per modificare e compilare il C# codice.

> [!NOTE]
> * Le versioni precedenti di macOS, Xcode e Mono _potrebbe_ funziona, ma sono non testato e non è supportato
> * Generazione del codice può essere eseguita in Windows, ma è solo possibile compilarlo in un computer Mac in cui è installato Xcode

## <a name="installing-net-embedding-from-nuget"></a>Installazione di incorporamento di .NET da NuGet

Seguire queste [istruzioni](~/tools/dotnet-embedding/get-started/install/install.md) per installare e configurare .NET l'incorporamento per il progetto.

Una chiamata di comandi di esempio è elencata nella [macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) e [iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md) guide introduttive.

## <a name="platforms"></a>Piattaforme

Objective-C è un linguaggio più comunemente utilizzato per scrivere applicazioni per macOS, iOS, tvOS e watchOS; incorporamento di .NET supporta tutte le piattaforme. Utilizzo di ogni piattaforma implica alcuni [principali differenze e queste sono illustrate in questo argomento](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[Creazione di un'applicazione macOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) è più semplice poiché non comporta tanti altri passaggi, ad esempio impostazione di identità, i profili di provisioning, simulatori e dispositivi. Sono invitati a iniziare con il documento di macOS che precede l'elemento per iOS.

### <a name="ios--tvos"></a>iOS o tvOS

Assicurarsi che sono già impostati per sviluppare applicazioni iOS prima di tentare di crearne uno tramite l'incorporamento di .NET. Il [attenendosi alle istruzioni](~/tools/dotnet-embedding/get-started/objective-c/ios.md) presuppongono che si dispone già compilato e distribuito un'applicazione iOS dal computer.

Supporto per tvOS è analogo al funzionamento di iOS, utilizzando solo i progetti tvOS in IDE (Visual Studio e Xcode) anziché i progetti iOS.

> [!NOTE]
> Il supporto per watchOS saranno disponibili nelle versioni future e sarà molto simile a iOS o tvOS.

## <a name="further-reading"></a>Ulteriori informazioni

* [Incorporamento di .NET funzionalità specifiche di Objective-C](~/tools/dotnet-embedding/objective-c/index.md)
* [Le procedure consigliate per Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
* [Limitazioni di incorporamento di .NET](~/tools/dotnet-embedding/limitations.md)
* [Aggiunta come contributo al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Le descrizioni e i codici di errore](~/tools/dotnet-embedding/errors.md)
* [Piattaforme di destinazione](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>Collegamenti correlati

- [Esempio meteo (iOS e macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
