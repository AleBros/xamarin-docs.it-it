---
title: Panoramica del codice di condivisione
description: 'Questo documento vengono confrontati i diversi metodi di condivisione del codice tra progetti multipiattaforma: Condiviso progetti di librerie di classi portabili e .NET Standard, inclusi i vantaggi e gli svantaggi di ognuna.'
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
author: conceptdev
ms.author: crdun
ms.date: 08/06/2018
ms.openlocfilehash: 98b5786ae4f071b4d8e8f854561db97aee037fdc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61228040"
---
# <a name="sharing-code-overview"></a>Panoramica del codice di condivisione

_Questo documento consente di confrontare i diversi metodi di condivisione del codice tra progetti multipiattaforma: .NET Standard, i progetti condivisi e librerie di classi portabili, inclusi i vantaggi e gli svantaggi di ognuna._

Esistono tre metodi per condividere codice tra le applicazioni multi-piattaforma:

- [**Le librerie .NET standard** ](#Net_Standard) : i progetti .NET Standard può implementare il codice per essere condivisi tra più piattaforme e possono accedere a un numero elevato di API .NET (a seconda della versione). .NET standard 1.0-1.6 implementare progressivamente più ampio set di API, mentre .NET Standard 2.0 offre la migliore copertura della libreria di classi base .NET (incluse le API di .NET disponibili nelle App Xamarin).
- [**Progetti condivisi** ](#Shared_Projects) : usare il tipo di progetto di Asset condivisi per organizzare il codice sorgente e usare `#if` direttive del compilatore come richiesto per gestire i requisiti specifici della piattaforma.
- [**Librerie di classi portabili** ](#Portable_Class_Libraries) (deprecata): librerie di classi portabili (librerie di classi portabili) può interagire con più piattaforme con una superficie API comune e utilizzare le interfacce per fornire funzionalità specifiche della piattaforma. Librerie di classi portabili sono deprecati nelle versioni più recenti di Visual Studio &ndash; alternativa, usare .NET Standard.

L'obiettivo di una strategia di condivisione del codice è per supportare l'architettura illustrata in questo diagramma, in cui una singola codebase può essere utilizzata per più piattaforme.

 ![Architettura di codice dell'applicazione condivisa](code-sharing-images/conceptualarchitecture.png "condiviso architettura delle applicazioni di codice")

Questo articolo mette a confronto i metodi disponibili che consentono di scegliere il tipo di progetto appropriato per le applicazioni.

<a name="Net_Standard" />

## <a name="net-standard-libraries"></a>Librerie .NET standard

[.NET standard](~/cross-platform/app-fundamentals/net-standard.md) librerie forniscono un set ben definito di librerie di classi base che può essere fatto riferimento nei diversi tipi di progetto, inclusi i progetti multipiattaforma come xamarin. Android e xamarin. IOS. .NET standard 2.0 è consigliato per garantire la massima compatibilità con il codice di .NET Framework esistente.

![Diagramma di .NET standard](code-sharing-images/netstandard.png "diagramma .NET Standard")

### <a name="benefits"></a>Vantaggi

- Consente di condividere codice tra più progetti.
- Operazioni di refactoring Aggiorna sempre tutti i riferimenti.
- Una superficie maggiore della libreria di classi Base (BCL) .NET, è disponibile più profili di libreria di classi Portabile. In particolare, .NET Standard 2.0 è quasi la stessa superficie dell'API di .NET Framework ed è consigliato per nuove app e librerie di classi portabili esistenti al porting.

### <a name="disadvantages"></a>Svantaggi

- Non è possibile usare le direttive del compilatore, ad esempio `#if __IOS__`.

### <a name="remarks"></a>Note

.NET standard è [simile alla libreria di classi Portabile](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries), ma con un modello più semplice per il supporto di piattaforma e un maggior numero di classi dalla libreria di classi base.

<a name="Shared_Projects" />

## <a name="shared-projects"></a>Progetti condivisi

[Progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md) contengono i file di codice e le risorse incluse in qualsiasi progetto che fa riferimento a essi. I progetti di condivisione non producono output compilato in modo indipendente.

Questo screenshot Mostra un file di soluzione che contiene tre progetti di applicazione (per Android, iOS e Windows), con un **condiviso** progetto che contiene file di codice comuni in c# origine:

![Soluzione di progetto condivisi](code-sharing-images/sharedsolution.png "condiviso soluzione del progetto")

L'architettura concettuale è illustrata nel diagramma seguente, in cui ogni progetto include tutti i file di codice sorgente condiviso:

![Diagramma di progetto Shared](code-sharing-images/sharedassetproject.png "diagramma progetto condiviso")

### <a name="example"></a>Esempio

Un'applicazione multipiattaforma che supporta iOS, Android e Windows richiede un progetto di applicazione per ogni piattaforma. Il codice comune si trova nel progetto condiviso.

Una soluzione di esempio contiene le cartelle e i progetti (i nomi dei progetti sono stati scelti per espressività, i progetti non sono necessario seguire le convenzioni di denominazione) seguenti:

- **Shared** : progetto condiviso che contiene il codice comune a tutti i progetti.
- **AppAndroid** : progetto di applicazione xamarin. Android.
- **AppiOS** : progetto di applicazione xamarin. IOS.
- **AppWindows** : progetto di applicazione Windows.

In questo modo i progetti di tre applicazione condividono lo stesso codice sorgente (i file c# in condiviso). Tutte le modifiche al codice condiviso verranno condivisa tra tutti e tre i progetti.

### <a name="benefits"></a>Vantaggi

- Consente di condividere codice tra più progetti.
- Codice condiviso può essere sottoposto a branching basato sulla piattaforma usando le direttive del compilatore (ad es. usando `#if __ANDROID__` , come illustrato nel [Building Cross Platform Applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) documento).
- Progetti di applicazione possono essere inclusi riferimenti specifici della piattaforma che può utilizzare il codice condiviso (ad esempio usando `Community.CsharpSqlite.WP7` nell'esempio Tasky per Windows Phone).

### <a name="disadvantages"></a>Svantaggi

- Refactoring che influiscono sul codice all'interno di direttive del compilatore 'inactive' non verrà aggiornato il codice all'interno di tali direttive.
- A differenza di molti altri tipi di progetto, un progetto condiviso non con alcun assembly 'output'. Durante la compilazione, i file vengono considerati come parte del progetto di riferimento e compilati nell'assembly. Se si vuole condividere il codice come assembly quindi .NET Standard o librerie di classi portabili sono una soluzione migliore.

<a name="Shared_Remarks" />

### <a name="remarks"></a>Note

Un'ottima soluzione per gli sviluppatori di applicazioni la scrittura di codice che è destinata solo condivisione nella propria app (e non alla distribuzione ad altri sviluppatori).

<a name="Portable_Class_Libraries" />

## <a name="portable-class-libraries"></a>Librerie di classi portabili

> [!TIP]
> Le librerie .NET standard 2.0 sono consigliate su librerie di classi portabili.

Sono librerie di classi portabili [descritti in dettaglio qui](~/cross-platform/app-fundamentals/pcl.md).

![Diagramma della libreria di classi portabile](code-sharing-images/portableclasslibrary.png "diagramma della libreria di classi portabile")

### <a name="benefits"></a>Vantaggi

- Consente di condividere codice tra più progetti.
- Operazioni di refactoring Aggiorna sempre tutti i riferimenti.

### <a name="disadvantages"></a>Svantaggi

- Librerie .NET Standard deprecati nelle versioni più recenti di Visual Studio, ma sono consigliate. Fare riferimento a questo [descrizione delle differenze](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries) tra PCL e .NET Standard.
- Non è possibile usare le direttive del compilatore.
- Solo un subset di .NET framework è disponibile per l'uso, determinate dal profilo selezionato (vedere la [Introduzione alla libreria di classi Portabile](~/cross-platform/app-fundamentals/pcl.md) per altre informazioni).

### <a name="remarks"></a>Note

Modello di libreria di classi Portabile è considerata deprecata nelle versioni più recenti di Visual Studio.

## <a name="summary"></a>Riepilogo

Lo strategia che scelta di condivisione del codice dipende dalle piattaforme di destinazione. Scegliere un metodo ottimale per il progetto.

.NET standard è la scelta migliore per la compilazione di librerie di codice condivisibile (in particolare la pubblicazione su NuGet). Progetti condivisi funzionano bene per gli sviluppatori di applicazioni prevede di usare un numero elevato di funzionalità specifiche della piattaforma nelle proprie App multipiattaforma.

Mentre i progetti libreria di classi Portabile continuano a essere supportato in Visual Studio, .NET Standard è consigliata per i nuovi progetti.

## <a name="related-links"></a>Collegamenti correlati

- [Building Cross Platform Applications (documento principale)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md)
- [Progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Case Study: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Esempio tasky (github)](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Esempio tasky con libreria di classi Portabile (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
