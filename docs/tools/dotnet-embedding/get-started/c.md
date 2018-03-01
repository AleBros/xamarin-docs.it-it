---
title: Introduzione a C
ms.topic: article
ms.prod: xamarin
ms.assetid: 2A27BE0F-95FB-4C3A-8A43-72540179AA85
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: ea8335348416dc00074d83e09b74521da7abcb66
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="getting-started-with-c"></a>Introduzione a C


## <a name="requirements"></a>Requisiti

Per utilizzare l'incorporamento .NET con C è necessario un computer Mac o Windows che esegue:

* macOS 10.12 (Sierra) o versione successiva
* Xcode 8.3.2 o versione successiva

* Windows 7, 8, 10 o versione successiva
* Visual Studio 2013 o versioni successiva

* [Mono](http://www.mono-project.com/download/)


## <a name="installation"></a>Installazione

Il passaggio successivo è per scaricare e installare gli strumenti di incorporamento .NET nel computer.

Compilazioni binarie per i generatori di C e Java sono comunque non disponibile, ma saranno presto disponibili.

In alternativa, è possibile compilare dal nostro repository git, vedere il [che hanno contribuito](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md) documento per le istruzioni.


## <a name="generation"></a>Generazione

Per generare il codice C, richiamare lo strumento .NET incorporamento passando i flag di destra per il linguaggio C di destinazione:

### <a name="windows"></a>Windows:

```csharp
$ build/lib/Debug/Embeddinator-4000.exe --gen=c --output=managed_c --platform=windows --compile managed.dll
```

Assicurarsi di chiamare da una shell dei comandi di Visual Studio specifico per la versione di Visual Studio si è interessati a.

### <a name="macos"></a>macOS

```csharp
$ mono build/lib/Debug/Embeddinator-4000.exe --gen=c --output=managed_c --platform=macos --compile managed.dll
```

### <a name="output-files"></a>File di output

Se tutto va bene, verrà visualizzato il seguente output:

```csharp
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

Poiché il `--compile` flag è stato passato allo strumento, l'incorporamento .NET deve inoltre stati compilati i file di output in una libreria condivisa, è possibile trovare accanto i file generati, un `libmanaged.dylib` macOS, file e `managed.dll` in Windows.

Per utilizzare la libreria condivisa, è possibile includere il `managed.h` file di intestazione C, che fornisce le dichiarazioni C corrispondente ai rispettivi managed API della libreria e il collegamento con indicati in precedenza compilato libreria condivisa.

## <a name="further-reading"></a>Ulteriori informazioni

* [Limitazioni di Embeddinator](~/tools/dotnet-embedding/limitations.md)
* [Che contribuiscono al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Codici di errore e descrizioni](~/tools/dotnet-embedding/errors.md)
