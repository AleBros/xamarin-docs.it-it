---
title: Incorporamento .NET in Android
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: lobrien
ms.author: laobri
ms.date: 06/15/2018
ms.openlocfilehash: 4612f5d04da4ba82127d37abfa41dd1dff2985bc
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120014"
---
# <a name="net-embedding-on-android"></a>Incorporamento .NET in Android

In alcuni casi, potrebbe essere necessario aggiungere una libreria Novell .NET a un progetto Android nativo esistente. A tale scopo, è possibile usare lo strumento [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/) per trasformare la libreria .NET in una libreria nativa che può essere incorporata in un'app Android nativa basata su Java.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="xamarinandroid-requirements"></a>Requisiti di Novell. Android

Per usare Novell. Android con l'incorporamento di .NET, è necessario quanto segue:

- È necessario installare **Novell. Android** &ndash; [Novell. Android 7,5](https://visualstudio.microsoft.com/xamarin/) o versione successiva.

- **Android Studio** È necessario installare [Android Studio 3. x](https://developer.android.com/studio/) o versione successiva. &ndash;

- **Java Developer Kit** È necessario che sia installato [Java 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva. &ndash;


## <a name="using-embeddinator-4000"></a>Uso di Embeddinator-4000

Per utilizzare una libreria .NET in un progetto Android nativo, attenersi alla procedura seguente:

1. Creare un C# progetto di libreria Android.

2. Installare [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/).

3. Individuare **Embeddinator-4000. exe** e aggiungerlo al **percorso**. Ad esempio:

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4. Eseguire Embeddinator-4000 nell'assembly della libreria. Ad esempio:

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. Usare il file AAR generato in un progetto Java in Android Studio.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="xamarinandroid-requirements"></a>Requisiti di Novell. Android

Per usare Novell. Android con l'incorporamento di .NET, è necessario quanto segue:

- È necessario installare **Novell. Android** &ndash; [Novell. Android 7,5](https://visualstudio.microsoft.com/xamarin/) o versione successiva.

- **Android Studio** È necessario installare [Android Studio 3. x](https://developer.android.com/studio/) o versione successiva. &ndash;

- **Java Developer Kit** È necessario che sia installato [Java 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva. &ndash;

- **Mono** È necessario installare [Mono 5,0](https://www.mono-project.com/download/) o versione successiva (mono viene installato con Visual Studio per Mac). &ndash;


## <a name="using-embeddinator-4000"></a>Uso di Embeddinator-4000

Per utilizzare una libreria .NET in un progetto Android nativo, attenersi alla procedura seguente:

1. Creare un C# progetto di libreria Android.

2. Installare [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/).

3. Individuare **Embeddinator-4000. exe** e aggiungere **mono** al percorso. Ad esempio:

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4. Eseguire Embeddinator-4000 nell'assembly della libreria. Ad esempio:

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
