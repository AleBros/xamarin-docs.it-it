---
title: Introduzione a C
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 04/19/2018
ms.openlocfilehash: 1313d7156a1fd75fd40e2aff65404aef5ab023bb
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="getting-started-with-c"></a>Introduzione a C

## <a name="requirements"></a>Requisiti

Per usare .NET incorporamento con C, è necessario un computer Mac o Windows che esegue:

### <a name="macos"></a>macOS

* macOS 10.12 (Sierra) o versione successiva
* Xcode 8.3.2 o versione successiva
* [Mono](http://www.mono-project.com/download/)

### <a name="windows"></a>WINDOWS

* Windows 7, 8, 10 o versione successiva
* Visual Studio 2015 o versione successiva

## <a name="installing-net-embedding-from-nuget"></a>L'installazione di .NET incorporamento da NuGet

Attenersi alla seguente [istruzioni](~/tools/dotnet-embedding/get-started/install/install.md) per installare e configurare .NET incorporamento per il progetto.

La chiamata a un comando che è necessario configurare sarà simile (possibilmente con numeri di versione diversi e i percorsi):

### <a name="visual-studio-for-mac"></a>Visual Studio per Mac

```shell
mono {SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe --gen=c --output=managed_c --platform=macos --compile managed.dll
```

### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
$(SolutionDir)\packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe --gen=c --output=managed_c --platform=windows --compile managed.dll
```

## <a name="generation"></a>Generazione

### <a name="output-files"></a>File di output

Se tutto va bene, verrà visualizzato il seguente output:

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

Poiché il `--compile` flag è stato passato allo strumento, durante l'incorporamento .NET deve inoltre stati compilati i file di output in una libreria condivisa, è possibile trovare accanto ai file generati, una **libmanaged.dylib** file sulla macOS e **managed.dll** in Windows.

Per utilizzare la libreria condivisa, è possibile includere il **managed.h** file di intestazione C, che rende disponibili le dichiarazioni C corrispondente ai rispettivi managed API della libreria e il collegamento con indicati in precedenza compilato libreria condivisa.

## <a name="further-reading"></a>Ulteriori informazioni

* [Limitazioni di incorporamento .NET](~/tools/dotnet-embedding/limitations.md)
* [Che contribuiscono al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Codici di errore e descrizioni](~/tools/dotnet-embedding/errors.md)
