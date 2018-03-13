---
title: Sharing Code Options
description: 'Questo documento consente di confrontare i diversi metodi di condivisione del codice tra progetti multipiattaforma: progetti condivisi, librerie di classi portabili e .NET Standard, inclusi i vantaggi e gli svantaggi di ognuno.'
ms.topic: article
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: e7289d92043bdbe9e4ec55776835530f8ccec526
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="sharing-code-options"></a>Sharing Code Options

_Questo documento consente di confrontare i diversi metodi di condivisione del codice tra progetti multipiattaforma: progetti condivisi, librerie di classi portabili e .NET Standard, inclusi i vantaggi e gli svantaggi di ognuno._

Esistono tre metodi alternativi per la condivisione del codice tra le applicazioni multipiattaforma:

-   [**Progetti condivisi** ](#Shared_Projects) : utilizzare il tipo di progetto Asset condiviso per organizzare codice sorgente e usare le direttive del compilatore #if come richiesto per gestire i requisiti specifici della piattaforma.
-   [**Librerie di classi portabili** ](#Portable_Class_Libraries) : creare con una libreria di classe portabile (PCL) le piattaforme di cui si desidera supportare e utilizzare le interfacce per fornire funzionalità specifiche della piattaforma.
-   [**Librerie standard .NET** ](#Net_Standard) – progetti .NET Standard funzionano in modo analogo a PCLs, richiedere l'utilizzo di interfacce per inserire le funzionalità specifiche della piattaforma.

L'obiettivo di una strategia di condivisione del codice è per supportare l'architettura illustrata in questo diagramma, in cui una singola codebase può essere utilizzata per più piattaforme.

 ![](code-sharing-images/conceptualarchitecture.png "Architettura dell'applicazione il codice condiviso")

In questo articolo vengono confrontati i tre metodi che consentono di scegliere il tipo di progetto appropriato per le applicazioni.

<a name="Shared_Projects" />

## <a name="shared-projects"></a>Progetti condivisi

L'approccio più semplice per la condivisione di file di codice consiste nell'utilizzare un [progetto condiviso](~/cross-platform/app-fundamentals/shared-projects.md).

Questa schermata è riportato un file di soluzione che contiene tre progetti di applicazione (per Android, iOS e Windows Phone), con un **Shared** progetto che contiene i file di codice comuni c# origine:

 ![](code-sharing-images/sharedsolution.png "Soluzione di progetto condiviso")

L'architettura concettuale è illustrata nel diagramma seguente, dove ogni progetto include tutti i file di origine condivisa:

 ![](code-sharing-images/sharedassetproject.png "Diagramma di progetto condiviso")


### <a name="example"></a>Esempio

Un'applicazione multipiattaforma che supporta iOS, Android e Windows Phone richiederebbe un progetto di applicazione per ogni piattaforma. Il codice comune si trova nel progetto condiviso.

Una soluzione di esempio dovrebbe contenere i seguenti progetti (nomi di progetto è stato scelto per espressività, i progetti non è necessario seguire queste linee guida di denominazione) e cartelle:

-   **Shared** : progetto condiviso che contiene il codice comune a tutti i progetti.
-   **AppAndroid** : il progetto di applicazione di xamarin.
-   **AppiOS** : il progetto di applicazione di xamarin. IOS.
-   **AppWinPhone** : il progetto di applicazione Windows Phone.


In questo modo i progetti di tre applicazione condividono lo stesso codice di origine (i file c# condiviso). Tutte le modifiche al codice condiviso verranno condivisi da tutti i tre progetti.


### <a name="benefits"></a>Vantaggi

-  Consente di condividere il codice tra più progetti.
-  Codice condiviso può essere diramato in base alla piattaforma mediante le direttive del compilatore (ad es. utilizzando `#if __ANDROID__` , come illustrato nel [compilazione di applicazioni della piattaforma incrociata](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento).
-  Progetti di applicazione possono includere riferimenti specifici della piattaforma che può utilizzare il codice condiviso (ad esempio usando `Community.CsharpSqlite.WP7` nell'esempio Tasky per Windows Phone).



### <a name="disadvantages"></a>Svantaggi

-  A differenza di molti altri tipi di progetto, un progetto condiviso non dispone di alcun assembly 'output'. Durante la compilazione, i file sono considerati parte del progetto di riferimento e compilati in tale assembly. Se si desidera condividere il codice come assembly quindi le librerie di classi portabile o .NET Standard sono una soluzione migliore.
-  Refactoring che influiscono sul codice all'interno del compilatore 'inactive' direttive non aggiornerà il codice.


 <a name="Shared_Remarks" />

### <a name="remarks"></a>Note

Una buona soluzione per la scrittura di codice che gli sviluppatori di applicazioni è destinata solo nell'applicazione di condivisione (e non la distribuzione agli altri sviluppatori).

 <a name="Portable_Class_Libraries" />


## <a name="portable-class-libraries"></a>Librerie di classi portabili


Librerie di classi portabili sono [descritti dettagliatamente qui](~/cross-platform/app-fundamentals/pcl.md).

 ![](code-sharing-images/portableclasslibrary.png "Diagramma di libreria di classi portabile")


### <a name="benefits"></a>Vantaggi

-  Consente di condividere il codice tra più progetti.
-  Le operazioni di refactoring aggiornano sempre tutti i riferimenti.


### <a name="disadvantages"></a>Svantaggi

-  Non è possibile utilizzare le direttive del compilatore.
-  Solo un subset di .NET framework è disponibile per l'utilizzo, determinato dal profilo selezionato (vedere il [Introduzione alla libreria di classi Portabile](~/cross-platform/app-fundamentals/pcl.md) per altre informazioni).


### <a name="remarks"></a>Note

Una buona soluzione se si prevede di condividere l'assembly risultante con altri sviluppatori.



<a name="Net_Standard" />

## <a name="net-standard-libraries"></a>Librerie standard di .NET

.NET standard è [descritti dettagliatamente qui](~/cross-platform/app-fundamentals/net-standard.md).

![](code-sharing-images/netstandard.png "Diagramma .NET standard")

### <a name="benefits"></a>Vantaggi

-  Consente di condividere il codice tra più progetti.
-  Le operazioni di refactoring aggiornano sempre tutti i riferimenti.
-  Una superficie maggiore della libreria di classi .NET Base (BCL) è disponibile più profili di libreria di classi Portabile.

### <a name="disadvantages"></a>Svantaggi

 -  Non è possibile utilizzare le direttive del compilatore.

### <a name="remarks"></a>Note

.NET standard è simile alla libreria di classi Portabile, ma con un modello più semplice per il supporto di piattaforma e un numero maggiore di classi dalla libreria di classi di base.



## <a name="summary"></a>Riepilogo

Il codice condivisione strategia che scelta dipende dalle piattaforme di destinazione. Scegliere un metodo ottimale per il progetto.

Libreria di classi Portabile o .NET Standard costituiscono un'ottima scelta per la compilazione di librerie di codice condivisibile (in particolare la pubblicazione in NuGet). Progetti condivisi funzionano anche per gli sviluppatori di applicazioni prevede di usare un numero elevato di funzionalità specifiche di plaform nelle proprie App tra platforma.


## <a name="related-links"></a>Collegamenti correlati

- [Compilazione incrociata di applicazioni della piattaforma (documento principale)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md)
- [Progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Case Study: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Esempio tasky (github)](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Tasky campione utilizzando la libreria di classi Portabile (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
