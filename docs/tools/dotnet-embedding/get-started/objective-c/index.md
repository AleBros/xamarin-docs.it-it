---
title: Introduzione a Objective-C
description: Questo documento descrive come iniziare a usare l'incorporamento di .NET con Objective-C. Vengono illustrati i requisiti, l'installazione dell'incorporamento .NET da NuGet e le piattaforme supportate.
ms.prod: xamarin
ms.assetid: 4ABC0247-B608-42D4-89CB-D2E598097142
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: 66b99e0da574c50df32afedb1dd6dc9de315b347
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278460"
---
# <a name="getting-started-with-objective-c"></a>Introduzione a Objective-C

Si tratta della pagina introduttiva per Objective-C, che illustra le nozioni di base per tutte le piattaforme supportate.

## <a name="requirements"></a>Requisiti

Per usare l'incorporamento .NET con Objective-C, è necessario un Mac che esegue:

- macOS 10,12 (Sierra) o versione successiva
- Xcode 8.3.2 o versione successiva
- [Mono 5.0](https://www.mono-project.com/download/)

È possibile installare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) per modificare e compilare il C# codice.

> [!NOTE]
> - Le versioni precedenti di macOS, Xcode e mono _potrebbero_ funzionare, ma non sono testate e non sono supportate
> - La generazione del codice può essere eseguita in Windows, ma è possibile solo compilarla in un computer Mac in cui è installato Xcode

## <a name="installing-net-embedding-from-nuget"></a>Installazione dell'incorporamento di .NET da NuGet

Seguire queste [istruzioni](~/tools/dotnet-embedding/get-started/install/install.md) per installare e configurare l'incorporamento .NET per il progetto.

Una chiamata di comando di esempio è elencata nelle guide introduttive di [MacOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) e [iOS](~/tools/dotnet-embedding/get-started/objective-c/ios.md) .

## <a name="platforms"></a>Piattaforme

Objective-C è un linguaggio usato più di frequente per scrivere applicazioni per macOS, iOS, tvOS e watchos; l'incorporamento di .NET supporta tutte queste piattaforme. L'uso di ogni piattaforma implica alcune [differenze principali e queste sono descritte qui](~/tools/dotnet-embedding/objective-c/platforms.md).

### <a name="macos"></a>macOS

[La creazione di un'applicazione MacOS](~/tools/dotnet-embedding/get-started/objective-c/macos.md) è più semplice, poiché non prevede il numero di passaggi aggiuntivi, ad esempio la configurazione di identità, profili provisioning, simulatori e dispositivi. È consigliabile iniziare con il documento macOS prima di quello per iOS.

### <a name="ios--tvos"></a>iOS/tvOS

Assicurarsi di essere già stati configurati per sviluppare applicazioni iOS prima di provare a crearne uno usando l'incorporamento .NET. Le [istruzioni seguenti](~/tools/dotnet-embedding/get-started/objective-c/ios.md) presuppongono che sia già stata compilata e distribuita correttamente un'applicazione iOS dal computer.

Il supporto per tvOS è analogo al funzionamento di iOS, usando solo progetti tvOS negli IDE (sia Visual Studio che Xcode) invece dei progetti iOS.

> [!NOTE]
> Il supporto per watchos sarà disponibile in una versione futura e sarà molto simile a iOS/tvOS.

## <a name="further-reading"></a>Ulteriori informazioni

- [Funzionalità di incorporamento di .NET specifiche per Objective-C](~/tools/dotnet-embedding/objective-c/index.md)
- [Procedure consigliate per Objective-C](~/tools/dotnet-embedding/objective-c/best-practices.md)
- [Limitazioni dell'incorporamento di .NET](~/tools/dotnet-embedding/limitations.md)
- [Aggiunta come contributo al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
- [Codici di errore e descrizioni](~/tools/dotnet-embedding/errors.md)
- [Piattaforme di destinazione](~/tools/dotnet-embedding/objective-c/platforms.md)

## <a name="related-links"></a>Collegamenti correlati

- [Esempio meteorologico (iOS & macOS)](https://github.com/jamesmontemagno/embeddinator-weather)
