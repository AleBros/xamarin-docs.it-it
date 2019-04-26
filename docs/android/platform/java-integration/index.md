---
title: Panoramica dell'integrazione di Java
description: Dell'ecosistema Java include una raccolta di componenti diverso ed elevata. Molti di questi componenti può essere utilizzati per ridurre il tempo che necessario per sviluppare un'applicazione Android. Questo documento verrà introducono e forniscono una panoramica generale di alcuni dei metodi che gli sviluppatori possono usare questi componenti Java esistenti per migliorare l'esperienza di sviluppo dell'applicazione xamarin. Android.
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 01/18/2017
ms.openlocfilehash: 3ab31fb7cac97fbae3315f51daf3dd4b1edbcc1d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085292"
---
# <a name="java-integration-overview"></a>Panoramica dell'integrazione di Java

_Dell'ecosistema Java include una raccolta di componenti diverso ed elevata. Molti di questi componenti può essere utilizzati per ridurre il tempo che necessario per sviluppare un'applicazione Android. Questo documento verrà introducono e forniscono una panoramica generale di alcuni dei metodi che gli sviluppatori possono usare questi componenti Java esistenti per migliorare l'esperienza di sviluppo dell'applicazione xamarin. Android._


## <a name="overview"></a>Panoramica

Dato l'extent dell'ecosistema Java, è molto probabile che qualsiasi dato funzionalità necessarie per un'applicazione xamarin. Android è già stata codificata in Java. Per questo motivo, è interessante per tentare di riutilizzare le librerie esistenti durante la creazione di un'applicazione xamarin. Android. 

Esistono tre possibili modi per riutilizzare le librerie Java in un'applicazione xamarin. Android: 

-   **Creare una raccolta di associazioni Java** &ndash; con questa tecnica, è possibile creare un progetto xamarin. Android C# wrapper per i tipi Java. Un'applicazione xamarin. Android è possibile fare riferimento il C# wrapper creato da questo progetto e quindi usare il `.jar` file. 

-   **Java Native Interface** &ndash; le *Java Native* *interfaccia* (JNI) è un framework che consente al codice non Java (ad esempio C++ o C#) per chiamare o essere chiamato dal codice Java in esecuzione all'interno di una JVM. 

-   **Trasferire il codice** &ndash; questo metodo consiste nel prendere il codice sorgente Java e quindi convertirlo in C#. Questa operazione può essere eseguita manualmente o tramite uno strumento automatico, ad esempio l'aumento della nitidezza. 

Alla base delle prime due tecniche è il *Java Native Interface* (JNI). JNI è un framework che consente alle applicazioni non scritte in Java interagire con il codice Java in esecuzione in una macchina virtuale Java. Xamarin. Android Usa JNI per creare *associazioni* per C# codice. 

La prima tecnica è un approccio dichiarativo, automatico per le librerie Java di associazione. Comporta mediante Visual Studio per Mac o un tipo di progetto di Visual Studio che viene fornito da xamarin. Android &ndash; la libreria di binding Java. Per creare queste associazioni, una libreria di binding Java possono comunque richiedere che alcune modifiche manuali, ma non tanti come sarebbe un approccio JNI puro. Visualizzare [associazione di una libreria Java](~/android/platform/binding-java-library/index.md) per altre informazioni sulle librerie Java di associazione. 

La seconda tecnica, uso di JNI, funziona con un altro livello inferiore, ma può fornire per un controllo più preciso e accedere ai metodi di Java che normalmente non sarebbe accessibili tramite una libreria di Binding Java. 

La terza tecnica è radicalmente diversa dai due precedenti: il porting del codice da Java a C#. Porting di codice da una lingua a un'altra può essere un processo molto laboriosa, ma è possibile ridurre che sforzo con l'aiuto di uno strumento chiamato *contrasta*. Ottimizzare il è uno strumento open source che è un linguaggio-a-C# convertitore. 



## <a name="summary"></a>Riepilogo

Questo documento è fornita una panoramica dettagliata di alcune delle diverse modalità che le librerie da Java possono essere riusati in un'applicazione xamarin. Android. Sono stati introdotti i concetti di associazioni e gestiti callable wrapper e illustrate le opzioni per il porting di codice Java per C#. 


## <a name="related-links"></a>Collegamenti correlati

- [Architettura](~/android/internals/architecture.md)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
- [Utilizzo di JNI](~/android/platform/java-integration/working-with-jni.md)
- [Nitidezza](https://github.com/slluis/sharpen)
- [Java Native Interface](http://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
