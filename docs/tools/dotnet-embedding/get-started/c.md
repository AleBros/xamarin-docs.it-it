---
title: Introduzione a C
description: Questo documento descrive come usare .NET l'incorporamento per incorporare il codice .NET in un'applicazione C. Illustra come usare l'incorporamento di .NET in Visual Studio 2019 e Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: lobrien
ms.author: laobri
ms.date: 04/19/2018
ms.openlocfilehash: 342ba2a6b51483983df7bd04034a4cef62fd57ff
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58854912"
---
# <a name="getting-started-with-c"></a>Introduzione a C

## <a name="requirements"></a>Requisiti

Per usare .NET incorporamento con C, è necessario un computer Mac o Windows che esegue:

### <a name="macos"></a>macOS

* macOS 10.12 (Sierra) o versione successiva
* Xcode 8.3.2 o versione successiva
* [Mono](https://www.mono-project.com/download/)

### <a name="windows"></a>WINDOWS

* Windows 7, 8, 10 o versioni successive
* Visual Studio 2015 o versione successiva

## <a name="installing-net-embedding-from-nuget"></a>Installazione di incorporamento di .NET da NuGet

Seguire queste [istruzioni](~/tools/dotnet-embedding/get-started/install/install.md) per installare e configurare .NET l'incorporamento per il progetto.

È necessario configurare la chiamata del comando sarà simile (possibilmente con diversi numeri di versione e i percorsi):

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

```shell
mono {SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=macos --compile managed.dll
```

### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
$(SolutionDir)\packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe --gen=c --outdir=managed_c --platform=windows --compile managed.dll
```

## <a name="generation"></a>Generazione

### <a name="output-files"></a>File di output

Se tutto va bene, verrà visualizzato l'output seguente:

```shell
Parsing assemblies...
    Parsed 'managed.dll'
Processing assemblies...
Generating binding code...
    Generated: managed.h
    Generated: managed.c
    Generated: mscorlib.h
    Generated: mscorlib.c
    Generated: embeddinator.h
    Generated: glib.c
    Generated: glib.h
    Generated: mono-support.h
    Generated: mono_embeddinator.c
    Generated: mono_embeddinator.h
```

Poiché il `--compile` flag è stato passato allo strumento, incorporamento di .NET devono inoltre stati compilati i file di output in una libreria condivisa, che trova accanto ai file generati, una **libmanaged.dylib** file in macOS e **managed.dll** su Windows.

Per usare la libreria condivisa, è possibile includere il **managed.h** file di intestazione C, che fornisce le dichiarazioni C corrispondente ai rispettivi managed API della libreria e il collegamento con citati in precedenza compilate libreria condivisa.

## <a name="further-reading"></a>Ulteriori informazioni

* [Limitazioni di incorporamento di .NET](~/tools/dotnet-embedding/limitations.md)
* [Aggiunta come contributo al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Le descrizioni e i codici di errore](~/tools/dotnet-embedding/errors.md)
