---
title: Conversione di Java in C'è per Xamarin.Android
description: Una terza opzione per l'utilizzo di Java in un'applicazione Xamarin.Android consiste nel convertire il codice sorgente Java in C .
ms.prod: xamarin
ms.assetid: 39E528BD-010F-47FC-BE48-8E7848E30454
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/05/2016
ms.openlocfilehash: 8f96fcc4aadcd8f082d55dc568b2517f048edaf2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027198"
---
# <a name="porting-java-to-c-for-xamarinandroid"></a>Conversione di Java in C'è per Xamarin.Android

Questo approccio può essere interessante per le organizzazioni che:

- **Si stanno commutando stack di tecnologia da Java a C .**
- **È necessario mantenere una versione in linguaggio C e una versione Java dello stesso prodotto.**
- **Desideri avere una versione .NET di una libreria Java popolare.**

Esistono due modi per eseguire il porting del codice Java in C. Il primo modo consiste nel trasferire il codice manualmente. Questo coinvolge gli sviluppatori qualificati che comprendono sia .NET e Java e hanno familiarità con gli idiomi appropriati per ogni linguaggio. Questo approccio ha più senso per piccole quantità di codice, o per le organizzazioni che desiderano passare completamente da Java a C .

La seconda metodologia di conversione consiste nel provare ad automatizzare il processo utilizzando un convertitore di codice, ad esempio [Sharpen](https://github.com/mono/sharpen). [Sharpen](https://github.com/mono/sharpen) è un convertitore open source da Versant che è stato originariamente utilizzato per il porting del codice per *db4o* da Java a C. db4o è un database orientato agli oggetti sviluppato da Versant in Java e quindi sottoposto a porting in .NET. L'utilizzo di un convertitore di codice può avere senso per i progetti che devono esistere in entrambe le lingue e che richiedono una certa parità tra i due.

Un esempio di quando uno strumento di conversione automatica del codice ha senso può essere visto nel progetto [ngit.](https://github.com/mono/ngit)
Ngit è una porta del progetto Java [jgit](https://eclipse.org/).
Jgit è un'implementazione Java del sistema di gestione del codice sorgente [Git.](https://git-scm.com/) Per generare il codice C , i programmatori di ngit utilizzano un sistema automatizzato personalizzato per estrarre il codice Java da jgit, applicare alcune patch per supportare il processo di conversione e quindi eseguire Sharpen, che genera il codice C . Questo permette al progetto ngit di beneficiare del lavoro continuo e continuo che viene svolto su jgit.

C'è spesso una quantità non banale di lavoro coinvolto con il bootstrap di uno strumento di conversione automatica del codice, e questo può rivelarsi una barriera da utilizzare. In molti casi, può essere più semplice e più facile convertire Java in C , a mano.

## <a name="related-links"></a>Collegamenti correlati

- [Strumento di conversione nitidezza](https://github.com/mono/sharpen)
