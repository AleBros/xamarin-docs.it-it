---
title: Panoramica della condivisione del codice
description: 'Questo documento mette a confronto i diversi metodi di condivisione del codice tra progetti multipiattaforma: progetti condivisi, librerie di classi portabili e .NET Standard, inclusi i vantaggi e gli svantaggi di ognuno di essi.'
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
author: davidortinau
ms.author: daortin
ms.date: 08/06/2018
ms.openlocfilehash: 78b849434a087cf7951fe36345688251885ea00b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016905"
---
# <a name="sharing-code-overview"></a>Panoramica della condivisione del codice

_Questo documento mette a confronto i diversi metodi di condivisione del codice tra progetti multipiattaforma: .NET Standard, progetti condivisi e librerie di classi portabili, inclusi i vantaggi e gli svantaggi di ognuno di essi._

Sono disponibili tre metodi per la condivisione di codice tra applicazioni multipiattaforma:

- [**Librerie di .NET standard**](#Net_Standard) : i progetti .NET standard possono implementare codice da condividere tra più piattaforme e possono accedere a un numero elevato di API .NET (a seconda della versione). .NET Standard 1,0-1,6 implementano set di API progressivamente più grandi, mentre .NET Standard 2,0 offre la migliore copertura della libreria di classi portabile .NET (incluse le API .NET disponibili nelle app Novell).
- [**Progetti condivisi**](#Shared_Projects) : usare il tipo di progetto di asset condiviso per organizzare il codice sorgente e usare `#if` direttive del compilatore come richiesto per gestire i requisiti specifici della piattaforma.
- [**Librerie di classi**](#Portable_Class_Libraries) portabili (deprecate): le librerie di classi portabili (classi portabili) possono avere come destinazione più piattaforme con una superficie API comune e usare le interfacce per fornire funzionalità specifiche della piattaforma. Classi portabili sono deprecati nelle versioni più recenti di Visual Studio &ndash; usare .NET Standard.

L'obiettivo di una strategia di condivisione del codice è supportare l'architettura illustrata in questo diagramma, in cui una singola codebase può essere utilizzata da più piattaforme.

 ![Architettura dell'applicazione di codice condiviso](code-sharing-images/conceptualarchitecture.png "Architettura dell'applicazione di codice condiviso")

In questo articolo vengono confrontati i metodi disponibili per scegliere il tipo di progetto appropriato per le applicazioni.

<a name="Net_Standard" />

## <a name="net-standard-libraries"></a>Librerie di .NET Standard

[.NET standard](~/cross-platform/app-fundamentals/net-standard.md) librerie forniscono un set ben definito di librerie di classi di base a cui è possibile fare riferimento in diversi tipi di progetto, inclusi progetti multipiattaforma come Xamarin.Android e Xamarin.iOS. È consigliabile .NET Standard 2,0 per la massima compatibilità con il codice .NET Framework esistente.

![Diagramma .NET Standard](code-sharing-images/netstandard.png "Diagramma .NET Standard")

### <a name="benefits"></a>Vantaggi

- Consente di condividere il codice tra più progetti.
- Le operazioni di refactoring aggiornano sempre tutti i riferimenti interessati.
- Una superficie di attacco più ampia della libreria di classi base (BCL) .NET è disponibile rispetto ai profili PCL. In particolare, .NET Standard 2,0 presenta quasi la stessa superficie dell'API del .NET Framework ed è consigliata per le nuove app e per il porting di classi portabili esistenti.

### <a name="disadvantages"></a>Svantaggi

- Non è possibile usare direttive del compilatore come `#if __IOS__`.

### <a name="remarks"></a>Note

.NET Standard è [simile a PCL](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries), ma con un modello più semplice per il supporto della piattaforma e un numero maggiore di classi da BCL.

<a name="Shared_Projects" />

## <a name="shared-projects"></a>Progetti condivisi

I [progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md) contengono file di codice e asset inclusi in tutti i progetti che vi fanno riferimento. I progetti di condivisione non producono un output compilato autonomamente.

Questa schermata mostra un file di soluzione contenente tre progetti di applicazione (per Android, iOS e Windows) con un progetto **condiviso** che contiene file C# di codice sorgente comuni:

![Soluzione progetto condiviso](code-sharing-images/sharedsolution.png "Soluzione progetto condiviso")

L'architettura concettuale è illustrata nel diagramma seguente, in cui ogni progetto include tutti i file di origine condivisi:

![Diagramma progetto condiviso](code-sharing-images/sharedassetproject.png "Diagramma progetto condiviso")

### <a name="example"></a>Esempio

Un'applicazione multipiattaforma che supporta iOS, Android e Windows richiederebbe un progetto di applicazione per ogni piattaforma. Il codice comune risiede nel progetto condiviso.

Una soluzione di esempio contiene le cartelle e i progetti seguenti (i nomi di progetto sono stati scelti per l'espressività, i progetti non devono seguire queste linee guida per la denominazione):

- **Shared** : progetto condiviso contenente il codice comune a tutti i progetti.
- Progetto di applicazione **AppAndroid** – Xamarin.Android.
- **AppiOS** : progetto di applicazione Xamarin.iOS.
- **AppWindows** : progetto di applicazione Windows.

In questo modo i tre progetti di applicazione condividono lo stesso codice sorgente, ovvero C# i file in condivisione. Tutte le modifiche apportate al codice condiviso verranno condivise tra tutti e tre i progetti.

### <a name="benefits"></a>Vantaggi

- Consente di condividere il codice tra più progetti.
- Il codice condiviso può essere sottoposto a branching in base alla piattaforma usando le direttive del compilatore, ad esempio utilizzando `#if __ANDROID__`, come illustrato nel documento [compilazione di applicazioni multipiattaforma](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) ).
- I progetti di applicazioni possono includere riferimenti specifici della piattaforma che possono essere utilizzati dal codice condiviso (ad esempio, utilizzando `Community.CsharpSqlite.WP7` nell'esempio di attività per Windows Phone).

### <a name="disadvantages"></a>Svantaggi

- I refactoring che interessano il codice all'interno delle direttive del compilatore ' inactive ' non aggiorneranno il codice all'interno di tali direttive.
- A differenza della maggior parte degli altri tipi di progetto, un progetto condiviso non ha un assembly ' output '. Durante la compilazione, i file vengono trattati come parte del progetto di riferimento e compilati in tale assembly. Se si vuole condividere il codice come assembly, .NET Standard o le librerie di classi portabili sono una soluzione migliore.

<a name="Shared_Remarks" />

### <a name="remarks"></a>Note

Una soluzione efficace per gli sviluppatori di applicazioni che scrivono codice destinato solo alla condivisione nella propria app (e non alla distribuzione ad altri sviluppatori).

<a name="Portable_Class_Libraries" />

## <a name="portable-class-libraries"></a>Librerie di classi portabili

> [!TIP]
> Le librerie di .NET Standard 2,0 sono consigliate per le librerie di classi portabili.

Le librerie di classi portabili sono [descritte in dettaglio qui](~/cross-platform/app-fundamentals/pcl.md).

![Diagramma libreria di classi portabile](code-sharing-images/portableclasslibrary.png "Diagramma libreria di classi portabile")

### <a name="benefits"></a>Vantaggi

- Consente di condividere il codice tra più progetti.
- Le operazioni di refactoring aggiornano sempre tutti i riferimenti interessati.

### <a name="disadvantages"></a>Svantaggi

- Deprecato nelle versioni più recenti di Visual Studio, sono invece consigliate le librerie .NET Standard. Per informazioni [sulle differenze](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries) tra PCL e .NET standard, vedere questa spiegazione.
- Non è possibile usare le direttive del compilatore.
- È possibile utilizzare solo un subset di .NET Framework, determinato dal profilo selezionato. per altre informazioni, vedere [Introduzione a PCL](~/cross-platform/app-fundamentals/pcl.md) .

### <a name="remarks"></a>Note

Il modello PCL viene considerato deprecato nelle versioni più recenti di Visual Studio.

## <a name="summary"></a>Riepilogo

La strategia di condivisione del codice scelta sarà determinata dalle piattaforme di destinazione. Scegliere un metodo che funzioni meglio per il progetto.

.NET Standard è la scelta migliore per la compilazione di librerie di codice condivisibili (in particolare la pubblicazione in NuGet). I progetti condivisi funzionano bene per gli sviluppatori di applicazioni che pianificano l'uso di numerose funzionalità specifiche della piattaforma nelle app multipiattaforma.

Sebbene i progetti PCL continuino a essere supportati in Visual Studio, è consigliabile .NET Standard per i nuovi progetti.

## <a name="related-links"></a>Collegamenti correlati

- [Compilazione di applicazioni multipiattaforma (documento principale)](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [Librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md)
- [Progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Case Study: Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Esempio di attività (GitHub)](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Esempio di attività con PCL (GitHub)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
