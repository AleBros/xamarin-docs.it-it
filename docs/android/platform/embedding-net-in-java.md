---
title: Incorporamento di .NET in Java
description: Come utilizzare una libreria .NET di Xamarin in un progetto di Android Native basati su Java
ms.topic: article
ms.prod: xamarin
ms.assetid: A489EEF3-1008-4257-BF63-FE21D8C23821
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 1a25f4bc39e39ce58a07ed399082bf13284c16e9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="embedding-net-in-java"></a>Incorporamento di .NET in Java

In alcuni casi, si desidera aggiungere una libreria .NET di Xamarin a un progetto Android nativo esistente. A tale scopo, è possibile utilizzare il [Embeddinator 4000](https://mono.github.io/Embeddinator-4000/) strumento per convertire la libreria .NET in una libreria nativa che può essere incorporata in un'app di Android native basati su Java.

 
## <a name="requirements"></a>Requisiti

Per utilizzare il Embeddinator-4000 con Java in Android, è necessario quanto segue:

-   **Android Studio** &ndash; [Android Studio 3. x](https://developer.android.com/studio/preview/index.html) o versioni successive deve essere installato.

-   **Xamarin** &ndash; [xamarin 7.4.99](https://jenkins.mono-project.com/view/Xamarin.Android/job/xamarin-android/lastSuccessfulBuild/Azure/) o versioni successive deve essere installato.

-   **Java Developer Kit** &ndash; [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versioni successive deve essere installato.

-   **Mono** &ndash; [5.0 Mono](http://www.mono-project.com/download/) o versioni successive deve essere installato.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

È possibile utilizzare Visual Studio per modificare e compilare il codice c#.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per modificare e compilare il codice c#, è possibile utilizzare Visual Studio per Mac.

-----

 
## <a name="using-the-embeddinator-4000"></a>Utilizzo di Embeddinator-4000.

Per utilizzare una libreria .NET in un progetto Android nativo, utilizzare la procedura seguente:

1.  Creare un progetto di libreria Android in c#.

2.  Installare Embeddinator-4000 tramite NuGet.

3.  Eseguire Embeddinator dall'assembly di libreria Android.

4.  Utilizzare il file AAR generato in un progetto Java in Android Studio.

Questi passaggi vengono descritti in dettaglio nel [Embeddinator 4000](https://mono.github.io/Embeddinator-4000/getting-started-java-android.html) documentazione.
