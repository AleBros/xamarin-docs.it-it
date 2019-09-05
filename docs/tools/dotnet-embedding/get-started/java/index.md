---
title: Introduzione a Java
description: Questo documento descrive come iniziare a usare l'incorporamento .NET con Java. Vengono illustrati i requisiti di sistema, l'installazione e le piattaforme supportate.
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
author: conceptdev
ms.author: crdun
ms.date: 03/28/2018
ms.openlocfilehash: 8d6bc284d07ce1be11ad273f875b75a70ae14a0f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278398"
---
# <a name="getting-started-with-java"></a>Introduzione a Java

Si tratta della pagina introduttiva per Java, che illustra le nozioni di base per tutte le piattaforme supportate.

## <a name="requirements"></a>Requisiti

Per usare l'incorporamento .NET con Java, è necessario:

* Java 1,8 o versione successiva
* [Mono 5.0](https://www.mono-project.com/download/)

Per Mac:

* Xcode 8.3.2 o versione successiva

Per Windows:

* Visual Studio 2017 con C++ supporto
* Windows 10 SDK

Per Android:

* [Novell. Android 7,5](https://visualstudio.microsoft.com/xamarin/) o versione successiva
* [Android Studio 3. x](https://developer.android.com/studio/index.html) con Java 1,8

È possibile usare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) per modificare e compilare il C# codice.

> [!NOTE]
> Le versioni precedenti di Xcode, Visual Studio, Novell. Android, Android Studio e mono _potrebbero_ funzionare, ma non sono testate e non sono supportate.

## <a name="installation"></a>Installazione

L'incorporamento .NET è attualmente disponibile in [NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```shell
nuget install Embeddinator-4000
```

Il file **Embeddinator-4000. exe** verrà inserito nella directory **packages/Embeddinator-4000/Tools** .

Inoltre, è possibile compilare l'incorporamento .NET dall'origine, vedere il [repository git](https://github.com/mono/Embeddinator-4000/) e il documento [contributo](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md) per istruzioni.

## <a name="platforms"></a>Piattaforme

Java è attualmente in uno stato di anteprima per macOS, Windows e Android.

La piattaforma viene selezionata passando l'argomento `--platform=<platform>` della riga di comando allo strumento di incorporamento .NET. Attualmente `macOS`sono `Windows`supportati, `Android` e.

### <a name="macos-and-windows"></a>macOS e Windows

Per lo sviluppo, dovrebbe essere possibile usare qualsiasi IDE Java che supporti Java 1,8. Se necessario, è anche possibile usare Android Studio per questo, [vedere qui](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects). È possibile usare l'output del file JAR come qualsiasi file jar Java standard.

### <a name="android"></a>Android

Assicurarsi di essere già stati configurati per sviluppare applicazioni Android prima di provare a crearne uno usando l'incorporamento .NET. Le [istruzioni seguenti](~/tools/dotnet-embedding/get-started/java/android.md) presuppongono che sia già stata compilata e distribuita correttamente un'applicazione Android dal computer.

Android Studio è consigliato per lo sviluppo, ma altri IDE dovrebbero funzionare purché sia disponibile il supporto per il [formato di file AAR](https://developer.android.com/studio/projects/android-library.html).

## <a name="further-reading"></a>Ulteriori informazioni

* [Introduzione in Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Callback in Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Ricerca preliminare per Android](~/tools/dotnet-embedding/android/index.md)
* [Limitazioni dell'incorporamento di .NET](~/tools/dotnet-embedding/limitations.md)
* [Aggiunta come contributo al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Codici di errore e descrizioni](~/tools/dotnet-embedding/errors.md)
