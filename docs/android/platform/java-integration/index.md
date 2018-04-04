---
title: Panoramica dell'integrazione con Java
description: L'ecosistema di Java include una raccolta di diverse e un'enorme dei componenti. Molti di questi componenti può essere utilizzati per ridurre il tempo che necessario per sviluppare un'applicazione Android. Questo documento verrà introdurre e fornire una panoramica di alcuni metodi che gli sviluppatori possono utilizzare questi componenti Java esistenti per migliorare l'esperienza di sviluppo dell'applicazione di xamarin.
ms.prod: xamarin
ms.assetid: 7B5B8695-1C49-19BF-AE99-948CDCBD2A20
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/18/2017
ms.openlocfilehash: dbaf17479ae077fced425df5ac31bdbbc4e06b64
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="java-integration-overview"></a>Panoramica dell'integrazione con Java

_L'ecosistema di Java include una raccolta di diverse e un'enorme dei componenti. Molti di questi componenti può essere utilizzati per ridurre il tempo che necessario per sviluppare un'applicazione Android. Questo documento verrà introdurre e fornire una panoramica di alcuni metodi che gli sviluppatori possono utilizzare questi componenti Java esistenti per migliorare l'esperienza di sviluppo dell'applicazione di xamarin._


## <a name="overview"></a>Panoramica

Dato l'extent dell'ecosistema di Java, è molto probabile che qualsiasi dato funzionalità necessarie per un'applicazione di xamarin è già stato codificato in Java. Per questo motivo, è interessante per tentare di riutilizzare tali librerie esistenti durante la creazione di un'applicazione di xamarin. 

Esistono tre possibili modi di riutilizzare le librerie di Java in un'applicazione di xamarin: 

-   **Creare una raccolta di associazioni di Java** &ndash; con questa tecnica, un progetto xamarin viene utilizzato per creare wrapper c# per i tipi Java. Un'applicazione di xamarin possa fare riferimento ai wrapper c# creati da questo progetto e quindi utilizzare il `.jar` file. 

-   **Java Native Interface** &ndash; il *Java Native* *interfaccia* (JNI) è un framework che consente al codice non Java (ad esempio C++ o Visual c#) per chiamare o essere chiamato da codice Java in esecuzione all'interno di una JVM . 

-   **Trasferire il codice** &ndash; questo metodo prevede che il codice sorgente Java e quindi convertirla in c#. Questa operazione può essere eseguita manualmente o tramite uno strumento automatico, ad esempio l'aumento della nitidezza. 

È alla base delle prime due tecniche di *Java Native Interface* (JNI). JNI è un framework che consente alle applicazioni non è stato scritte in linguaggio interagire con il codice Java in esecuzione in una macchina virtuale Java. Xamarin Usa JNI per creare *associazioni* per il codice c#. 

La prima tecnica è un approccio dichiarativo automatico all'associazione di raccolte di Java. Presuppone l'utilizzo di Visual Studio per Mac o un tipo di progetto di Visual Studio fornito da xamarin &ndash; la raccolta di associazioni di Java. Per creare queste associazioni, una raccolta di associazioni di Java può essere ancora necessario che alcune modifiche manuali, ma non il numero come sarebbe un approccio JNI puro. Vedere [associazione di una libreria di Java](~/android/platform/binding-java-library/index.md) per ulteriori informazioni sulle librerie di Binding di linguaggio. 

La seconda tecnica, utilizzando JNI, funziona con un altro livello inferiore, ma può fornire per un controllo più preciso e accedere ai metodi di Java che normalmente non sarebbe accessibili tramite una libreria di Binding di linguaggio. 

La terza tecnica è completamente diversa da due precedenti: porting di codice da Java a c#. Porting di codice da una lingua a altra può essere un processo molto complesso, tendente al, ma è possibile ridurre che con l'aiuto di uno strumento chiamato *l'aumento della nitidezza*. Nitidezza è uno strumento open source che è un linguaggio-a-C# convertitore. 



## <a name="summary"></a>Riepilogo

In questo documento viene fornita una panoramica generale di alcuni dei diversi modi, che possono essere riutilizzate librerie da Java in un'applicazione di xamarin. Sono stati introdotti i concetti di associazioni e callable wrapper gestiti e descritte le opzioni per il porting di codice Java in c#. 


## <a name="related-links"></a>Collegamenti correlati

- [Architettura](~/android/internals/architecture.md)
- [Associazione di una libreria Java](~/android/platform/binding-java-library/index.md)
- [Utilizzo di JNI](~/android/platform/java-integration/working-with-jni.md)
- [Nitidezza](https://github.com/slluis/sharpen)
- [Java Native Interface](http://docs.oracle.com/javase/7/docs/technotes~/jni/index.html)
