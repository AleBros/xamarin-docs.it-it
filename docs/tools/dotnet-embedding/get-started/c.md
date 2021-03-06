---
title: Introduzione a C
description: Questo documento descrive come usare l'incorporamento .NET per incorporare il codice .NET in un'applicazione C. Viene illustrato come usare l'incorporamento .NET in Visual Studio 2019 e Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
author: davidortinau
ms.author: daortin
ms.date: 04/19/2018
ms.openlocfilehash: 9660eccd3e14a6c4ecad901064650fe1e25458f8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029790"
---
# <a name="getting-started-with-c"></a>Introduzione a C

## <a name="requirements"></a>Requisiti

Per usare l'incorporamento .NET con C, è necessario un computer Mac o Windows che esegue:

### <a name="macos"></a>macOS

* macOS 10,12 (Sierra) o versione successiva
* Xcode 8.3.2 o versione successiva
* [Mono](https://www.mono-project.com/download/)

### <a name="windows"></a>WINDOWS

* Windows 7, 8, 10 o versione successiva
* Visual Studio 2015 o versione successiva

## <a name="installing-net-embedding-from-nuget"></a>Installazione dell'incorporamento di .NET da NuGet

Seguire queste [istruzioni](~/tools/dotnet-embedding/get-started/install/install.md) per installare e configurare l'incorporamento .NET per il progetto.

La chiamata del comando da configurare avrà un aspetto simile a (possibilmente con numeri di versione e percorsi diversi):

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

Poiché il flag di `--compile` è stato passato allo strumento, l'incorporamento di .NET deve avere compilato anche i file di output in una libreria condivisa, che è possibile trovare accanto ai file generati, un file **libmanaged. dylib** in MacOS e una **DLL gestita** in Windows.

Per utilizzare la libreria condivisa, è possibile includere il file di intestazione c **gestito** , che fornisce le dichiarazioni c corrispondenti alle rispettive API della libreria gestita e il collegamento con la libreria condivisa compilata precedentemente indicata.

## <a name="further-reading"></a>Ulteriori informazioni

* [Limitazioni dell'incorporamento di .NET](~/tools/dotnet-embedding/limitations.md)
* [Aggiunta come contributo al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Codici di errore e descrizioni](~/tools/dotnet-embedding/errors.md)
