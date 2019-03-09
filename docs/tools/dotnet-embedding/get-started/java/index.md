---
title: Introduzione a Java
description: 'Questo documento descrive come iniziare a usare l''incorporamento di .NET con Java. Illustra i requisiti di sistema, installazione e le piattaforme supportate.'
ms.prod: xamarin
ms.assetid: B9A25E9B-3EC2-489A-8AD3-F78287609747
author: lobrien
ms.author: laobri
ms.date: 03/28/2018
---

# <a name="getting-started-with-java"></a>Introduzione a Java

Questa è la pagina introduttiva per Java, che illustra le nozioni di base per tutte le piattaforme supportate.

## <a name="requirements"></a>Requisiti

Per usare l'incorporamento di .NET con Java è necessario:

* Java versione 1.8 o versione successiva
* [Mono 5.0](https://www.mono-project.com/download/)

Per Mac:

* Xcode 8.3.2 o versione successiva

Per Windows:

* Visual Studio 2017 con il supporto per C++
* Windows 10 SDK

Per Android:

* [Xamarin. Android 7.5](https://visualstudio.microsoft.com/xamarin/) o versione successiva
* [Android Studio 3.x](https://developer.android.com/studio/index.html) with Java 1.8

È possibile usare [Visual Studio per Mac](https://visualstudio.microsoft.com/vs/mac/) per modificare e compilare il C# codice.

> [!NOTE]
> Le versioni precedenti di Xcode, Visual Studio, xamarin. Android, Android Studio e Mono _potrebbe_ funziona, ma sono non testato e non è supportato.

## <a name="installation"></a>Installazione

Incorporamento di .NET è attualmente disponibile nel [NuGet](https://www.nuget.org/packages/Embeddinator-4000/):

```shell
nuget install Embeddinator-4000
```

Si inseriranno **Embeddinator 4000.exe** nel **pacchetti/Embeddinator-4000/tools** directory.

Inoltre, è possibile compilare .NET incorporamento dall'origine, vedere la [repository git](https://github.com/mono/Embeddinator-4000/) e il [aggiunta come contributo](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md) documento per le istruzioni.

## <a name="platforms"></a>Piattaforme

Java è attualmente in uno stato di anteprima per Windows, macOS e Android.

La piattaforma è selezionata, passando il `--platform=<platform>` argomento della riga di comando per lo strumento di incorporamento di .NET. Attualmente `macOS`, `Windows`, e `Android` sono supportati.

### <a name="macos-and-windows"></a>macOS e Windows

Per lo sviluppo, dovrebbe essere possibile usare qualsiasi ambiente IDE Java che supporta Java 1.8. È anche possibile usare Android Studio per questo eventualmente [riportato di seguito](https://stackoverflow.com/questions/16626810/can-android-studio-be-used-to-run-standard-java-projects). Come si farebbe con qualsiasi file con estensione jar Java standard, è possibile usare l'output del file con estensione JAR.

### <a name="android"></a>Android

Assicurarsi che sono già impostati per sviluppare applicazioni Android prima di tentare di crearne uno tramite l'incorporamento di .NET. Il [attenendosi alle istruzioni](~/tools/dotnet-embedding/get-started/java/android.md) presuppongono che si dispone già compilato e distribuito un'applicazione Android dal computer.

Android Studio è consigliato per lo sviluppo, ma dovrebbero funzionare altri IDE, purché sia presente il supporto per la [formato di file AAR](https://developer.android.com/studio/projects/android-library.html).

## <a name="further-reading"></a>Ulteriori informazioni

* [Introduzione all'uso su Android](~/tools/dotnet-embedding/get-started/java/android.md)
* [Callback in Android](~/tools/dotnet-embedding/android/callbacks.md)
* [Ricerca preliminare Android](~/tools/dotnet-embedding/android/index.md)
* [Limitazioni di incorporamento di .NET](~/tools/dotnet-embedding/limitations.md)
* [Aggiunta come contributo al progetto open source](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [Le descrizioni e i codici di errore](~/tools/dotnet-embedding/errors.md)
