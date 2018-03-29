---
title: Introduzione al linguaggio
ms.topic: article
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/28/2018
ms.openlocfilehash: 06df925525c7bc01963274e9e0d5edfe7bb70613
ms.sourcegitcommit: 17a9cf246a4d33cfa232016992b308df540c8e4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2018
---
# <a name="getting-started-with-java"></a>Introduzione al linguaggio


Questa è la pagina introduttiva per Java, in cui vengono illustrate le nozioni di base per tutte le piattaforme supportate.

## <a name="requirements"></a>Requisiti

Per usare .NET incorporamento con Java, è necessario:

* Java 1.8 o versioni successiva
* [Mono 5.0](http://www.mono-project.com/download/)

Per Mac:
* Xcode 8.3.2 o versione successiva

Per Windows:
* Visual Studio 2017, con supporto per C++
* Windows 10 SDK

Per Android:
* [Xamarin. Android 7.5](https://www.visualstudio.com/xamarin/) o versione successiva
* [Android Studio 3. x](https://developer.android.com/studio/index.html) con Java 1.8

È possibile utilizzare [Visual Studio per Mac](https://www.visualstudio.com/vs/visual-studio-mac/) per modificare e compilare il codice c#.

> [!NOTE]
> Le versioni precedenti di Xcode, Visual Studio, xamarin, Studio Android e Mono _potrebbe_ di lavoro, ma sono non testato e supportato.

## <a name="installation"></a>Installazione

Incorporamento di .NET è attualmente disponibile nel [NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```csharp
nuget install Embeddinator-4000
```
Verranno `Embeddinator-4000.exe` nel `packages/Embeddinator-4000/tools` directory.

Inoltre, è possibile compilare Embeddinator dall'origine, vedere il nostro [repository git](https://github.com/mono/Embeddinator-4000/) e [che hanno contribuito](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md) documento per le istruzioni.

## <a name="platforms"></a>Piattaforme

Java è attualmente in uno stato di anteprima per macOS, Windows e Android.

La piattaforma è selezionata, passando il `--platform=<platform>` argomento della riga di comando per il embeddinator. Attualmente `macOS`, `Windows`, e `Android` sono supportati.

### <a name="macos-and-windows"></a>macOS e Windows

Per lo sviluppo, deve essere in grado di utilizzare qualsiasi IDE linguaggio che supporta Java 1.8. È anche possibile utilizzare Android Studio per questo eventualmente [vedere qui](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects). È possibile utilizzare l'output del file JAR come a qualsiasi altro file jar di Java standard.

### <a name="android"></a>Android

Verificare che sono già impostati per lo sviluppo di applicazioni Android prima di tentare di crearlo utilizzando Embeddinator. Il [attenendosi alle istruzioni](~/tools/dotnet-embedding/get-started/java/android.md) si supponga di aver già completato compilata e distribuita un'applicazione Android dal computer.

Android Studio è consigliata per lo sviluppo, ma altri IDE dovrebbero funzionare, purché sia presente il supporto per il [il formato di file AAR](https://developer.android.com/studio/projects/android-library.html).

## <a name="further-reading"></a>Ulteriori informazioni

* [Guida introduttiva in Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Callback in Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Ricerca di Android preliminare](~/tools/dotnet-embedding/android/index.md)
* [Limitazioni di Embeddinator](~/tools/dotnet-embedding/limitations.md)
* [Che contribuiscono al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [Codici di errore e descrizioni](~/tools/dotnet-embedding/errors.md)
