---
title: Associare le librerie Android Kotlin
description: In questo documento viene descritto come creare associazioni c'è al codice Kotlin, rendendo possibile l'utilizzo di librerie native in un'applicazione Xamarin.Android.This document describes how to create C ' bindings to Kotlin code, making it possible to consume native libraries in a Xamarin.Android application.
ms.prod: xamarin
ms.assetid: AB03A6C4-5A5A-4EAD-AD51-D887B20A3551
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: ec7d154b0d7fcb055bd398089e142fe8b1d9f60e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291851"
---
# <a name="bind-android-kotlin-libraries"></a>Associare le librerie Android Kotlin

La piattaforma Android, insieme alle sue lingue native e agli strumenti, è in continua evoluzione e ci sono un sacco di librerie di terze parti che sono state sviluppate utilizzando le ultime offerte. La massimizzazione del riutilizzo di codice e componenti è uno degli obiettivi principali dello sviluppo multipiattaforma. La capacità di riutilizzare i componenti costruiti con Kotlin è diventata sempre più importante per gli sviluppatori Xamarin, poiché la loro popolarità tra gli sviluppatori continua a crescere. È possibile che si abbia già familiarità con il processo di associazione di librerie [Java](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/) regolari. È ora disponibile ulteriore documentazione che descrive il processo di associazione di [una libreria Kotlin](walkthrough.md), in modo che siano consumabili da un'applicazione Xamarin nello stesso modo. Lo scopo di questo documento è descrivere un approccio di alto livello per creare un'associazione Kotlin per Xamarin.The purpose of this document is to describe a high-level approach to create a Kotlin Binding for Xamarin.

## <a name="high-level-approach"></a>Approccio di alto livello

Con Xamarin, è possibile associare qualsiasi libreria nativa di terze parti per essere consumabile da un'applicazione Xamarin. Kotlin è il nuovo linguaggio e la creazione di associazioni per le librerie compilate con questo linguaggio richiede alcuni passaggi aggiuntivi e strumenti. Questo approccio prevede i quattro passaggi seguenti:This approach involves the following four steps:

1. Compilare la libreria nativaBuild the native library
1. Preparare i metadati di Xamarin, che consente agli strumenti Xamarin di generare classi C
1. Creare una libreria di associazione Xamarin usando la libreria nativa e i metadatiBuild a Xamarin Binding Library using the native library and the metadata
1. Usare la libreria di rilegatura Xamarin in un'applicazione XamarinConsume the Xamarin Binding Library in a Xamarin application

Nelle sezioni seguenti vengono descritti questi passaggi con ulteriori dettagli.

### <a name="build-the-native-library"></a>Compilare la libreria nativaBuild the native library

Il primo passo è quello di ottenere una libreria Kotlin nativa (pacchetto AAR, che è un archivio Android). Puoi richiederlo direttamente a un fornitore o crearlo da solo.

### <a name="prepare-the-xamarin-metadata"></a>Preparare i metadati Xamarin

Il secondo passaggio consiste nel preparare il file di trasformazione dei metadati, che verrà utilizzato dagli strumenti Xamarin per generare le rispettive classi C. Nel migliore dei casi, questo file potrebbe essere vuoto dove tutte le classi vengono individuate e generate dagli strumenti Xamarin. In alcuni casi, la trasformazione dei metadati deve essere applicata per generare il codice C' corretto e/o desiderato. In molti casi, un disassembler AAR, ad esempio [Java Decompiler (JD)](http://java-decompiler.github.io/), deve essere utilizzato per identificare le dipendenze nascoste e le classi indesiderate che si desidera escludere dall'elenco finale di classi C . I metadati finali devono rappresentare l'interfaccia pubblica con cui l'applicazione Xamarin.Android di riferimento interagirà.

### <a name="build-a-xamarinandroid-binding-library"></a>Creare una libreria di binding Xamarin.AndroidBuild a Xamarin.Android binding library

Il terzo passaggio consiste nel creare un progetto speciale, una libreria di binding Xamarin.Android.The third step is to create a special project - a Xamarin.Android Binding Library. Include le librerie Kotlin come riferimenti nativi e la trasformazione dei metadati definita nel passaggio precedente. Al momento della scrittura, è necessario un progetto di libreria di binding Android separato per ogni pacchetto AAR a cui si fa riferimento. La libreria di associazione deve aggiungere il pacchetto [Xamarin.Kotlin.StdLib](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) per supportare la libreria standard Kotlin.

### <a name="consume-the-xamarin-binding-library"></a>Usare la libreria di rilegatura Xamarin

Il quarto e ultimo passaggio consiste nel fare riferimento alla libreria di associazione in un'applicazione Xamarin.Android.The fourth and the final step is to reference the binding library in a Xamarin.Android application. L'aggiunta di un riferimento alla libreria di binding Xamarin.Android consente all'applicazione Xamarin di usare le classi Kotlin esposte dall'interno del pacchetto.

## <a name="walkthrough"></a>Procedura dettagliata

L'approccio precedente descrive i passaggi di alto livello necessari per creare un'associazione Kotlin per Xamarin. Ci sono molti passaggi di livello inferiore coinvolti e ulteriori dettagli da considerare quando si preparano questi binding in pratica, tra cui l'adattamento alle modifiche negli strumenti nativi e linguaggi. L'intento è quello di aiutare a ottenere una comprensione più profonda di questo concetto e dei passaggi di alto livello coinvolti in questo processo. Per una guida dettagliata, fare riferimento alla documentazione di [Xamarin Kotlin Binding Walkthrough.](walkthrough.md)

## <a name="related-links"></a>Collegamenti correlati

- [Android Studio](https://developer.android.com/studio)
- [Installazione Gradle](https://gradle.org/install/)
- [Visual Studio per Mac](https://visualstudio.microsoft.com/downloads)
- [Java Decompiler](http://java-decompiler.github.io/)
- [Libreria BubblePicker Kotlin](https://github.com/igalata/Bubble-Picker)
- [Libreria Java di associazioneBinding Java Library](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [Metadati di associazione JavaJava Binding Metadata](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Xamarin.Kotlin.StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [Repository di progetto di esempio](https://github.com/xamcat/xamarin-binding-kotlin-framework)
