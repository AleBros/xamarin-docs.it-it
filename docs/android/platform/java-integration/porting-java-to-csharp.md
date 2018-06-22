---
title: Portabilità di linguaggio c#
description: Una terza opzione per l'utilizzo di Java in un'applicazione di xamarin è per trasferire il codice di origine Java in c#.
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/05/2016
ms.openlocfilehash: c2d05b101c627dab42dc1343eab2a408d1bd010f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762597"
---
# <a name="porting-java-to-c"></a>Portabilità di linguaggio c#

_Una terza opzione per l'utilizzo di Java in un'applicazione di xamarin è per trasferire il codice di origine Java in c#._

## <a name="overview"></a>Panoramica

Questo approccio può essere di interesse per le organizzazioni che:

-  **Passa gli stack di tecnologia da Java a c#.**
-  **È necessario mantenere in c# e una versione di Java dello stesso prodotto.**
-  **Se si desidera dispone di una versione di .NET di una libreria di linguaggio comune.**


Esistono due modi per trasferire il codice Java in c#. La prima consiste nel trasferire il codice manualmente. Questo implica l'esperto agli sviluppatori che comprendere sia .NET e Java e si ha familiarità con l'idiomi appropriati per ogni lingua. Questo approccio più appropriato per piccole quantità di codice o per le organizzazioni che desiderano completamente abbandonare Java in c#.

La seconda metodologia di porting consiste nel provare e automatizzare il processo utilizzando un convertitore di tipi di codice, ad esempio [l'aumento della nitidezza](https://github.com/mono/sharpen). [Nitidezza](https://github.com/mono/sharpen) è un convertitore di tipi di origine aperti da Versant utilizzato originariamente per trasferire il codice per *db4o* da Java a c#. db4o è un database orientata agli oggetti Versant sviluppate in Java, e quindi trasferita a .NET. Utilizzo di un convertitore di codice può risultare utile per i progetti che deve essere presente in entrambi i linguaggi e che richiedono alcuni parità tra i due.

Un esempio di quando uno strumento di conversione di codice automatica senso può essere visti nel [ngit](https://github.com/mono/ngit) progetto.
Ngit è una porta del progetto Java [jgit](http://eclipse.org/).
Jgit stesso è un'implementazione Java del [Git](http://git-scm.com/) sistema di gestione del codice di origine. Per generare codice c# da Java, i programmatori ngit utilizzano un sistema automatico personalizzato per estrarre il codice Java da jgit, applicare patch per supportare il processo di conversione e quindi eseguire l'aumento della nitidezza, che genera il codice c#. In questo modo il progetto ngit per trarre vantaggio dal lavoro che viene eseguito su jgit continuo, in corso.

Vi è spesso una considerevole quantità di lavoro necessarie per l'avvio di uno strumento di conversione di codice automatica e questo potrebbe non risultare una barriera da utilizzare. In molti casi, può risultare più semplice e più facile porta Java e c# manualmente.



## <a name="related-links"></a>Collegamenti correlati

- [Strumento di conversione precisione](https://github.com/mono/sharpen)
