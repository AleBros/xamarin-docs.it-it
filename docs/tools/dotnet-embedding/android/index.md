---
title: Incorporamento .NET in Android
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: davidortinau
ms.author: daortin
ms.date: 06/15/2018
ms.openlocfilehash: fef422b799ab5280aef205f4d5e55fd91050da39
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007330"
---
# <a name="net-embedding-on-android"></a>Incorporamento .NET in Android

In alcuni casi, potrebbe essere necessario aggiungere una libreria Xamarin .NET a un progetto Android nativo esistente. A tale scopo, è possibile usare lo strumento [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/) per trasformare la libreria .NET in una libreria nativa che può essere incorporata in un'app Android nativa basata su Java.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="xamarinandroid-requirements"></a>Requisiti di Xamarin.Android

Per usare Xamarin.Android con l'incorporamento di .NET, è necessario quanto segue:

- È necessario installare **Xamarin.android** &ndash;[Xamarin.Android 7,5](https://visualstudio.microsoft.com/xamarin/) o versione successiva.   

- È necessario installare **Android Studio** &ndash;[Android Studio 3. x](https://developer.android.com/studio/) o versione successiva.   

- **Java Developer Kit** &ndash;   [Java 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva deve essere installato.

## <a name="using-embeddinator-4000"></a>Uso di Embeddinator-4000

Per utilizzare una libreria .NET in un progetto Android nativo, attenersi alla procedura seguente:

1. Creare un C# progetto di libreria Android.

2. Installare [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/).

3. Individuare **Embeddinator-4000. exe** e aggiungerlo al **percorso**. Esempio:

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4. Eseguire Embeddinator-4000 nell'assembly della libreria. Esempio:

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. Usare il file AAR generato in un progetto Java in Android Studio.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="xamarinandroid-requirements"></a>Requisiti di Xamarin.Android

Per usare Xamarin.Android con l'incorporamento di .NET, è necessario quanto segue:

- È necessario installare **Xamarin.android** &ndash;[Xamarin.Android 7,5](https://visualstudio.microsoft.com/xamarin/) o versione successiva.   

- È necessario installare **Android Studio** &ndash;[Android Studio 3. x](https://developer.android.com/studio/) o versione successiva.   

- **Java Developer Kit** &ndash;   [Java 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva deve essere installato.

- È necessario installare **mono** &ndash;[Mono 5,0](https://www.mono-project.com/download/) o versione successiva (mono viene installato con Visual Studio per Mac).   

## <a name="using-embeddinator-4000"></a>Uso di Embeddinator-4000

Per utilizzare una libreria .NET in un progetto Android nativo, attenersi alla procedura seguente:

1. Creare un C# progetto di libreria Android.

2. Installare [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/).

3. Individuare **Embeddinator-4000. exe** e aggiungere **mono** al percorso. Esempio:

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4. Eseguire Embeddinator-4000 nell'assembly della libreria. Esempio:

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. Usare il file AAR generato in un progetto Java in Android Studio.

-----

Le opzioni di utilizzo e della riga di comando sono descritte nella documentazione di [Embeddinator-4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c) .

## <a name="callbacks"></a>Callback

Informazioni su come [effettuare chiamate C# tra e Java](callbacks.md).

## <a name="samples"></a>Esempi

- [App di esempio Weather](https://github.com/jamesmontemagno/embeddinator-weather)
