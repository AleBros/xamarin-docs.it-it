---
title: .NET incorporamento in Android
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: lobrien
ms.author: laobri
ms.date: 06/15/2018
---

# <a name="net-embedding-on-android"></a>.NET incorporamento in Android

In alcuni casi, è possibile aggiungere una libreria .NET di Xamarin a un progetto Android nativo esistente. A tale scopo, è possibile usare la [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/) dello strumento per trasformare la libreria .NET in una libreria nativa che può essere incorporata in un'app per Android native basate su Java.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="xamarinandroid-requirements"></a>Requisiti di xamarin. Android

Per xamarin. Android lavorare con l'incorporamento di .NET, è necessario quanto segue:

-   **Xamarin. Android** &ndash; [7.5 di xamarin. Android](https://visualstudio.microsoft.com/xamarin/) o versioni successive deve essere installato.

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/) o versioni successive deve essere installato.

-   **Java Developer Kit** &ndash; [Java 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versioni successive deve essere installato.


## <a name="using-embeddinator-4000"></a>Usando Embeddinator 4000

Per usare una libreria .NET in un progetto Android nativo, usare la procedura seguente:

1.  Creare un C# progetto di libreria Android.

2.  Installare [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/).

3.  Individuare **Embeddinator 4000.exe** e aggiungerlo alle **percorso**. Ad esempio:

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4.  Eseguire Embeddinator 4000 in assembly della libreria. Ad esempio:

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  Usare il file AAR generato in un progetto Java in Android Studio.


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

## <a name="xamarinandroid-requirements"></a>Requisiti di xamarin. Android

Per xamarin. Android lavorare con l'incorporamento di .NET, è necessario quanto segue:

-   **Xamarin. Android** &ndash; [7.5 di xamarin. Android](https://visualstudio.microsoft.com/xamarin/) o versioni successive deve essere installato.

-   **Android Studio** &ndash; [Android Studio 3.x](https://developer.android.com/studio/) o versioni successive deve essere installato.

-   **Java Developer Kit** &ndash; [Java 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versioni successive deve essere installato.

-   **Mono** &ndash; [5.0 Mono](https://www.mono-project.com/download/) o versioni successive deve essere installato (mono viene installato con Visual Studio per Mac).


## <a name="using-embeddinator-4000"></a>Usando Embeddinator 4000

Per usare una libreria .NET in un progetto Android nativo, usare la procedura seguente:

1.  Creare un C# progetto di libreria Android.

2.  Installare [Embeddinator 4000](https://www.nuget.org/packages/Embeddinator-4000/).

3.  Individuare **Embeddinator 4000.exe** e aggiungere **mono** al percorso. Ad esempio:

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4.  Eseguire Embeddinator 4000 in assembly della libreria. Ad esempio:

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5.  Usare il file AAR generato in un progetto Java in Android Studio.

-----

Le opzioni di utilizzo e della riga di comando sono descritte nel [Embeddinator 4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c) documentazione.


## <a name="callbacks"></a>Callback

Scopri [effettua le chiamate tra C# e Java](callbacks.md).

## <a name="samples"></a>Esempi

* [App di esempio meteo](https://github.com/jamesmontemagno/embeddinator-weather)
