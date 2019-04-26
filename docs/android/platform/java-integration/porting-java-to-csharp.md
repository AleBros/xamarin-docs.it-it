---
title: Convertire Java in C#
description: Una terza opzione per usare Java in un'applicazione xamarin. Android è possibile trasferire il codice sorgente Java in C#.
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/05/2016
ms.openlocfilehash: 9beb6d59c9376a404c06af7f0cd1efd985929843
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075144"
---
# <a name="porting-java-to-c"></a>Convertire Java in C#

_Una terza opzione per usare Java in un'applicazione xamarin. Android è possibile trasferire il codice sorgente Java in C#._

## <a name="overview"></a>Panoramica

Questo approccio può rivelarsi utile alle organizzazioni che:

-  **Passa gli stack di tecnologie da Java a C#.**
-  **È necessario mantenere un C# e una versione di Java dello stesso prodotto.**
-  **Utile avere una versione di .NET di una libreria Java più diffusi.**


Esistono due modi per convertire il codice Java per C#. Il primo consiste nel trasferire manualmente il codice. Ciò comporta agli sviluppatori esperti che comprendere .NET e Java e si ha familiarità con i linguaggi appropriati per ogni lingua. Questo approccio più appropriato per piccole quantità di codice o per le organizzazioni che desiderano spostare completamente di fuori di Java per C#.

La seconda metodologia porting consiste nel provare e automatizzare il processo utilizzando un convertitore di tipi di codice, ad esempio [contrasta](https://github.com/mono/sharpen). [Affinare](https://github.com/mono/sharpen) è un convertitore di tipi open source da Versant utilizzata originariamente per trasferire il codice per *db4o* da Java a C#. db4o è un database orientato Versant sviluppate in Java, e quindi trasferito in .NET. Utilizzo di un convertitore di codice può risultare utile per i progetti che deve essere presente in entrambi i linguaggi e che richiedono alcune parità tra i due.

Un esempio di quando uno strumento di conversione del codice automatiche senso visualizzabili nel [ngit](https://github.com/mono/ngit) progetto.
Ngit è una porta del progetto Java [jgit](http://eclipse.org/).
Jgit stesso è un'implementazione Java del [Git](http://git-scm.com/) sistema di gestione del codice di origine. Per generare C# codice da Java, l'uso di programmatori ngit personalizzato automatico del sistema per estrarre il codice Java da jgit applicare alcune patch per supportare il processo di conversione e quindi eseguire contrasta, che genera il C# codice. In questo modo il progetto ngit per trarre vantaggio dal lavoro svolto su jgit continuo, in corso.

È spesso una considerevole quantità di lavoro coinvolti con uno strumento di conversione del codice automatiche di avvio automatico e questo potrebbe rivelarsi una barriera da utilizzare. In molti casi, potrebbe essere più semplice e facile da porta Java per C# manualmente.



## <a name="related-links"></a>Collegamenti correlati

- [Strumento di conversione precisione](https://github.com/mono/sharpen)
