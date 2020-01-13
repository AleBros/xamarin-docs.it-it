---
title: Porting di Java C# a per Xamarin.Android
description: Una terza opzione per l'uso di Java in un'applicazione Xamarin.Android consiste nel trasferire il codice sorgente C#Java a.
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/05/2016
ms.openlocfilehash: 8f96fcc4aadcd8f082d55dc568b2517f048edaf2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027198"
---
# <a name="porting-java-to-c-for-xamarinandroid"></a>Porting di Java C# a per Xamarin.Android

Questo approccio può essere interessante per le organizzazioni che:

- **Stanno cambiando gli stack di tecnologia da Java C#a.**
- **Deve mantenere un C# e una versione Java dello stesso prodotto.**
- **Si vuole avere una versione .NET di una nota libreria Java.**

Esistono due modi per trasferire il codice Java a C#. Il primo consiste nel trasferire manualmente il codice. Si tratta di sviluppatori esperti che conoscono sia .NET che Java e hanno familiarità con gli idiomi appropriati per ogni linguaggio. Questo approccio è molto utile per piccole quantità di codice o per le organizzazioni che desiderano uscire completamente da Java a C#.

La seconda metodologia di porting consiste nel provare a automatizzare il processo usando un convertitore di codice, ad esempio l' [affilatura](https://github.com/mono/sharpen). [Sharp](https://github.com/mono/sharpen) è un convertitore Open Source da Versant originariamente usato per trasferire il codice per *db4o* da Java a C#. db4o è un database orientato a oggetti che è inversamente sviluppato in Java e quindi trasferito a .NET. L'uso di un convertitore di codice può essere utile per i progetti che devono esistere in entrambi i linguaggi e che richiedono una certa parità tra i due.

Un esempio di quando uno strumento di conversione automatica del codice è sensato può essere visualizzato nel progetto [Ngit](https://github.com/mono/ngit) .
Ngit è una porta del progetto Java [le jgit](https://eclipse.org/).
Le jgit è un'implementazione Java del sistema di gestione del codice sorgente di [git](https://git-scm.com/) . Per generare C# codice da Java, i programmatori Ngit usano un sistema automatico personalizzato per estrarre il codice Java da le jgit, applicare alcune patch per gestire il processo di conversione, quindi eseguire Sharp, che genera il C# codice. In questo modo, il progetto Ngit può trarre vantaggio dal lavoro continuo continuativo eseguito in le jgit.

Spesso è necessario eseguire il bootstrap di uno strumento di conversione automatica del codice e questo potrebbe rivelarsi una barriera da usare. In molti casi, può essere più semplice e facile trasferire i Java in modo C# manuale.

## <a name="related-links"></a>Collegamenti correlati

- [Affila strumento di conversione](https://github.com/mono/sharpen)
