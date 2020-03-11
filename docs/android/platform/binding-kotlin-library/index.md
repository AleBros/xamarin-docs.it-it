---
title: Associare le librerie Android Kotlin
description: Questo documento descrive come creare C# binding per Kotlin codice, rendendo possibile l'utilizzo di librerie native in un'applicazione Novell. Android.
ms.prod: xamarin
ms.assetid: AB03A6C4-5A5A-4EAD-AD51-D887B20A3551
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: ec7d154b0d7fcb055bd398089e142fe8b1d9f60e
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78291851"
---
# <a name="bind-android-kotlin-libraries"></a>Associare le librerie Android Kotlin

La piattaforma Android, oltre ai linguaggi e agli strumenti nativi, è in continua evoluzione e sono disponibili numerose librerie di terze parti sviluppate con le offerte più recenti. L'ottimizzazione del riutilizzo del codice e dei componenti è uno degli obiettivi chiave dello sviluppo multipiattaforma. La possibilità di riutilizzare i componenti compilati con Kotlin è diventata sempre più importante per Novell gli sviluppatori, in quanto la loro popolarità tra gli sviluppatori continua ad aumentare. È possibile che si abbia già familiarità con il processo di associazione di librerie [Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) normali. È ora disponibile documentazione aggiuntiva che descrive il processo di [associazione di una libreria Kotlin](walkthrough.md), in modo che possano essere utilizzate da un'applicazione Novell nello stesso modo. Lo scopo di questo documento è descrivere un approccio di alto livello per creare un'associazione Kotlin per Novell.

## <a name="high-level-approach"></a>Approccio di alto livello

Con Novell è possibile associare qualsiasi libreria nativa di terze parti per poter essere utilizzabile da un'applicazione Novell. Kotlin è il nuovo linguaggio e la creazione dell'associazione per le librerie compilate con questo linguaggio richiede alcuni passaggi aggiuntivi e gli strumenti. Questo approccio prevede i quattro passaggi seguenti:

1. Compilare la libreria nativa
1. Preparare i metadati Novell, che consente agli strumenti Novell di generare C# le classi
1. Compilare una libreria di associazione Novell usando la libreria nativa e i metadati
1. Utilizzare la libreria di associazione Novell in un'applicazione Novell

Le sezioni seguenti illustrano questi passaggi con ulteriori dettagli.

### <a name="build-the-native-library"></a>Compilare la libreria nativa

Il primo passaggio consiste nell'ottenere una libreria Kotlin nativa, ovvero un pacchetto AAR, che è un archivio Android. È possibile richiederlo direttamente da un fornitore o compilarlo manualmente.

### <a name="prepare-the-xamarin-metadata"></a>Preparare i metadati Novell

Il secondo passaggio consiste nel preparare il file di trasformazione dei metadati, che verrà usato dagli strumenti Novell per generare le rispettive C# classi. Nello scenario migliore, questo file può essere vuoto in cui tutte le classi vengono individuate e generate dagli strumenti Novell. In alcuni casi, è necessario applicare la trasformazione dei metadati per generare il codice corretto C# e/o desiderato. In molti casi, è necessario usare un disassembler AAR, ad esempio [Java Decompiler (JD)](http://java-decompiler.github.io/), per identificare le dipendenze nascoste e le classi indesiderate che si vuole escludere C# dall'elenco finale delle classi da generare. I metadati finali devono rappresentare l'interfaccia pubblica in cui interagirà l'applicazione di riferimento Novell. Android.

### <a name="build-a-xamarinandroid-binding-library"></a>Compilare una libreria di binding Novell. Android

Il terzo passaggio consiste nel creare un progetto speciale, ovvero una libreria di binding Novell. Android. Include le librerie Kotlin come riferimenti nativi e la trasformazione dei metadati definiti nel passaggio precedente. Al momento della stesura di questo documento, è necessario un progetto di libreria di binding Android separato per ogni pacchetto AAR a cui si fa riferimento. La libreria di associazione deve aggiungere il pacchetto [Novell. Kotlin. StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) per supportare la libreria standard Kotlin.

### <a name="consume-the-xamarin-binding-library"></a>Utilizzare la libreria di associazione Novell

Il quarto e il passaggio finale consiste nel fare riferimento alla libreria di associazione in un'applicazione Novell. Android. L'aggiunta di un riferimento alla libreria di binding Novell. Android consente all'applicazione Novell di usare le classi Kotlin esposte dall'interno di tale pacchetto.

## <a name="walkthrough"></a>Procedura dettagliata

L'approccio precedente descrive i passaggi di alto livello necessari per creare un'associazione Kotlin per Novell. Sono disponibili molti passaggi di basso livello e altri dettagli da tenere in considerazione durante la preparazione di questi binding in pratica, incluso l'adattamento alle modifiche apportate agli strumenti e ai linguaggi nativi. Lo scopo è quello di aiutare a ottenere una conoscenza più approfondita di questo concetto e i passaggi di alto livello necessari per questo processo. Per una guida dettagliata, vedere la documentazione relativa alla [procedura dettagliata per l'associazione di Novell Kotlin](walkthrough.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Android Studio](https://developer.android.com/studio)
- [Installazione di Gradle](https://gradle.org/install/)
- [Visual Studio per Mac](https://visualstudio.microsoft.com/downloads)
- [Decompilatore Java](http://java-decompiler.github.io/)
- [Libreria Kotlin BubblePicker](https://github.com/igalata/Bubble-Picker)
- [Associazione libreria Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [Metadati di binding Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Novell. Kotlin. StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Repository del progetto di esempio](https://github.com/xamcat/xamarin-binding-kotlin-framework)
