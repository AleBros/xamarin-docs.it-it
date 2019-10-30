---
title: Integrazione Java con Novell. Android
description: L'ecosistema Java include una vasta gamma di componenti. Molti di questi componenti possono essere usati per ridurre il tempo necessario per lo sviluppo di un'applicazione Android. Questo documento introduce e fornisce una panoramica di alto livello di alcuni dei modi in cui gli sviluppatori possono usare questi componenti Java esistenti per migliorare l'esperienza di sviluppo di applicazioni Novell. Android.
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 01/18/2017
ms.openlocfilehash: ecaa02e036c74074b4fa922ea079355b72ff02e2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020091"
---
# <a name="java-integration-with-xamarinandroid"></a>Integrazione Java con Novell. Android

_L'ecosistema Java include una vasta gamma di componenti. Molti di questi componenti possono essere usati per ridurre il tempo necessario per lo sviluppo di un'applicazione Android. Questo documento introduce e fornisce una panoramica di alto livello di alcuni dei modi in cui gli sviluppatori possono usare questi componenti Java esistenti per migliorare l'esperienza di sviluppo di applicazioni Novell. Android._

## <a name="overview"></a>Panoramica

Data la portata dell'ecosistema Java, è molto probabile che tutte le funzionalità necessarie per un'applicazione Novell. Android siano già state codificate in Java. Per questo motivo, è interessante provare a usare queste librerie esistenti durante la creazione di un'applicazione Novell. Android.

Esistono tre modi possibili per riutilizzare le librerie Java in un'applicazione Novell. Android: 

- **Creare una libreria di binding Java** &ndash; con questa tecnica, viene usato un progetto Novell. Android per creare C# wrapper intorno ai tipi Java. Un'applicazione Novell. Android può quindi fare riferimento C# ai wrapper creati da questo progetto e quindi usare il file di`.jar`. 

- **Java Native interface** &ndash; *Java Native* *Interface* (JNI) è un Framework che consente a codice non Java (ad esempio C++ o C#) di chiamare o essere chiamato da codice Java in esecuzione all'interno di un JVM. 

- **Porta il codice** &ndash; questo metodo implica l'esecuzione del codice sorgente Java e la relativa conversione in C#. Questa operazione può essere eseguita manualmente o usando uno strumento automatizzato, ad esempio affila. 

Alla base delle prime due tecniche è JNI ( *Java Native Interface* ). JNI è un Framework che consente alle applicazioni non scritte in Java di interagire con il codice Java in esecuzione in un Java Virtual Machine. Novell. Android USA JNI per creare *Binding* per C# il codice. 

La prima tecnica è un approccio dichiarativo e più automatizzato per l'associazione di librerie Java. Comporta l'uso di Visual Studio per Mac o di un tipo di progetto di Visual Studio fornito da Novell. Android &ndash; libreria dei binding Java. Per creare correttamente queste associazioni, è possibile che una libreria di associazioni Java richieda alcune modifiche manuali, ma non tutti gli approcci JNI. Vedere [binding di una libreria Java](~/android/platform/binding-java-library/index.md) per altre informazioni sulle librerie di binding Java. 

La seconda tecnica, che usa JNI, funziona a un livello molto inferiore, ma può fornire un controllo più preciso e l'accesso a metodi Java che in genere non sarebbero accessibili tramite una libreria di binding Java. 

La terza tecnica è radicalmente diversa dalle due precedenti: porting del codice da Java a C#. Il porting del codice da un linguaggio a un altro può essere un processo molto laborioso, ma è possibile ridurre tale impegno con l'ausilio di uno strumento denominato *affila*. Sharp è uno strumento open source che è un Java aC# Converter. 

## <a name="summary"></a>Riepilogo

Questo documento fornisce una panoramica di alto livello di alcuni dei diversi modi in cui le librerie di Java possono essere riutilizzate in un'applicazione Novell. Android. Sono stati introdotti i concetti relativi alle associazioni e ai wrapper richiamabili gestiti e sono state illustrate le C#opzioni per il porting di codice Java a. 

## <a name="related-links"></a>Collegamenti correlati

- [Architettura](~/android/internals/architecture.md)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
- [Uso di JNI](~/android/platform/java-integration/working-with-jni.md)
- [Nitidezza](https://github.com/slluis/sharpen)
- [Interfaccia java nativa](https://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
