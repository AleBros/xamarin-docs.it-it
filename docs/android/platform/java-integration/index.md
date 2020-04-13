---
title: Integrazione Java con Xamarin.Android
description: L'ecosistema Java include una vasta e diversificata collezione di componenti. Molti di questi componenti possono essere utilizzati per ridurre il tempo necessario per sviluppare un'applicazione Android. Questo documento introdurrà e fornirà una panoramica generale di alcuni dei modi in cui gli sviluppatori possono utilizzare questi componenti Java esistenti per migliorare la loro esperienza di sviluppo di applicazioni Xamarin.Android.
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/18/2017
ms.openlocfilehash: ecaa02e036c74074b4fa922ea079355b72ff02e2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020091"
---
# <a name="java-integration-with-xamarinandroid"></a>Integrazione Java con Xamarin.Android

_L'ecosistema Java include una vasta e diversificata collezione di componenti. Molti di questi componenti possono essere utilizzati per ridurre il tempo necessario per sviluppare un'applicazione Android. Questo documento introdurrà e fornirà una panoramica generale di alcuni dei modi in cui gli sviluppatori possono utilizzare questi componenti Java esistenti per migliorare la loro esperienza di sviluppo di applicazioni Xamarin.Android._

## <a name="overview"></a>Panoramica

Data l'estensione dell'ecosistema Java, è molto probabile che qualsiasi funzionalità necessaria per un'applicazione Xamarin.Android sia già stata codificata in Java. Per questo motivo, è interessante provare e riutilizzare queste librerie esistenti durante la creazione di un'applicazione Xamarin.Android.

Esistono tre modi per riutilizzare le librerie Java in un'applicazione Xamarin.Android:There are three possible ways to reuse Java libraries in a Xamarin.Android application: 

- **Creare una libreria** &ndash; di associazioni Java Con questa tecnica, un progetto Xamarin.Android viene utilizzato per creare wrapper di C , intorno ai tipi Java. Un'applicazione Xamarin.Android può quindi fare riferimento ai wrapper di `.jar` C , creati da questo progetto e quindi utilizzare il file. 

- **Java Native Interface** &ndash; (Java *Native* *Interface)* è un framework che consente a codice non Java (ad esempio, C , C , o C ) di chiamare o essere chiamato dal codice Java in esecuzione all'interno di una JVM. 

- **Convertire il codice** &ndash; Questo metodo comporta l'assunzione del codice sorgente Java e quindi la conversione in C . Questa operazione può essere eseguita manualmente o utilizzando uno strumento automatico come Nitidezza. 

Al centro delle prime due tecniche è la *Java Native Interface* (JNI). JNI è un framework che consente alle applicazioni non scritte in Java di interagire con il codice Java in esecuzione in una Java Virtual Machine. Xamarin.Android usa JNI per creare associazioni per *il* codice C. 

La prima tecnica è un approccio più automatizzato e dichiarativo per l'associazione di librerie Java.The first technique is a more automated, declarative approach to binding Java libraries. Implica l'utilizzo di Visual Studio per Mac o di un tipo &ndash; di progetto di Visual Studio fornito da Xamarin.Android la libreria di associazioni Java.It involves using either Visual Studio for Mac or a Visual Studio project type that is provided by Xamarin.Android the Java Bindings Library. Per creare correttamente queste associazioni, una libreria di associazioni Java può ancora richiedere alcune modifiche manuali, ma non come molti come sarebbe un approccio JNI puro. Per ulteriori informazioni sulle librerie di associazione Java, vedere [Associazione](~/android/platform/binding-java-library/index.md) di una libreria Java.See Binding a Java Library for more information about Java Binding libraries. 

La seconda tecnica, utilizzando JNI, funziona a un livello molto inferiore, ma può fornire un controllo più preciso e l'accesso ai metodi Java che normalmente non sarebbero accessibili tramite una libreria di associazione Java. 

La terza tecnica è radicalmente diversa dalle due precedenti: il porting del codice da Java a C . Portare il codice da una lingua all'altra può essere un processo molto laborioso, ma è possibile ridurre tale sforzo con l'aiuto di uno strumento chiamato *Sharpen*. Sharpen è uno strumento open source che è un convertitore da Java a C. 

## <a name="summary"></a>Riepilogo

Questo documento ha fornito una panoramica generale di alcuni dei diversi modi in cui le librerie da Java possono essere riutilizzate in un'applicazione Xamarin.Android.This document provided a high-level overview of some of the different ways that libraries from Java can be reused in a Xamarin.Android application. Sono stati introdotti i concetti di associazioni e wrapper richiamabili gestiti e sono state illustrate le opzioni per il porting del codice Java in C. 

## <a name="related-links"></a>Collegamenti correlati

- [Architecture](~/android/internals/architecture.md)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
- [Lavorare con JNI](~/android/platform/java-integration/working-with-jni.md)
- [Nitidezza](https://github.com/slluis/sharpen)
- [Interfaccia nativa Java](https://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
