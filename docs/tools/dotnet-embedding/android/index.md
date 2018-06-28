---
title: .NET incorporamento in Android
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: topgenorth
ms.author: toopge
ms.date: 06/15/2018
ms.openlocfilehash: e90d1e6258d4cfd9c918c566c9e18c358ee7668a
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37067354"
---
# <a name="net-embedding-on-android"></a>.NET incorporamento in Android

In alcuni casi, si desidera aggiungere una libreria .NET di Xamarin a un progetto Android nativo esistente. A tale scopo, è possibile usare il [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/) strumento per trasformare la libreria .NET in una libreria nativa che può essere incorporata in un'app di Android native basata su Java.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="xamarinandroid-requirements"></a>Requisiti di xamarin

Per xamarin. Android da utilizzare durante l'incorporamento .NET, è necessario quanto segue:

-   **Xamarin. Android** &ndash; [xamarin 7.5](https://visualstudio.microsoft.com/xamarin/) o versioni successive deve essere installato.

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/) o versioni successive deve essere installato.

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versioni successive deve essere installato.


## <a name="using-embeddinator-4000"></a>Con 4000 Embeddinator

Per utilizzare una libreria .NET in un progetto Android nativo, utilizzare la procedura seguente:

1.  Creare un progetto di libreria Android in c#.

2.  Installare [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/).

3.  Individuare **Embeddinator 4000.exe** e aggiungerlo al **percorso**. Ad esempio:

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4.  Eseguire Embeddinator 4000 dall'assembly di libreria. Ad esempio:

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  Utilizzare il file AAR generato in un progetto Java in Android Studio.


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

## <a name="xamarinandroid-requirements"></a>Requisiti di xamarin

Per xamarin. Android da utilizzare durante l'incorporamento .NET, è necessario quanto segue:

-   **Xamarin. Android** &ndash; [xamarin 7.5](https://visualstudio.microsoft.com/xamarin/) o versioni successive deve essere installato.

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/) o versioni successive deve essere installato.

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versioni successive deve essere installato.

-   **Mono** &ndash; [5.0 Mono](http://www.mono-project.com/download/) o versioni successive deve essere installato (mono viene installata con Visual Studio per Mac).


## <a name="using-embeddinator-4000"></a>Con 4000 Embeddinator

Per utilizzare una libreria .NET in un progetto Android nativo, utilizzare la procedura seguente:

1.  Creare un progetto di libreria Android in c#.

2.  Installare [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/).

3.  Individuare **Embeddinator 4000.exe** e aggiungere **mono** al percorso. Ad esempio:

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4.  Eseguire Embeddinator 4000 dall'assembly di libreria. Ad esempio:

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  Utilizzare il file AAR generato in un progetto Java in Android Studio.

-----

Vengono descritte le opzioni di utilizzo e riga di comando nel [Embeddinator 4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c) documentazione.


## <a name="callbacks"></a>Callback

Apprendere [effettuano chiamate tra linguaggi c# e Java](callbacks.md).

## <a name="samples"></a>Esempi

* [Weather app di esempio](https://github.com/jamesmontemagno/embeddinator-weather)
