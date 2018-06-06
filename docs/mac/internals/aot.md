---
title: Xamarin.Mac prima compilazione
description: Questo documento descrive in anticipo rispetto alla compilazione in Xamarin.Mac. Confronta la compilazione AOT alla compilazione JIT, viene spiegato come abilitare AOT e prende in considerazione ibrida AOT.
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: ec8474293fbb7372529e0f850e2d16db7ebf17be
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792239"
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>Xamarin.Mac prima compilazione

## <a name="overview"></a>Panoramica

Avanti di tempo (AOT) compilazione è una tecnica di ottimizzazione avanzate per migliorare le prestazioni di avvio. Tuttavia, influisce anche il tempo di compilazione, dimensioni dell'applicazione e l'esecuzione del programma in modi evidenti. Per comprendere i compromessi che impone, si intende un bit approfondire la compilazione e l'esecuzione di un'applicazione.

Codice scritto in linguaggi gestiti, ad esempio in c# e F #, viene compilato in una rappresentazione intermedia chiamata linguaggio intermedio. Questo livello di integrità, archiviato negli assembly di libreria e un programma, è relativamente compatto e portabile tra le architetture di processore. IL, tuttavia, è solo intermedio set di istruzioni e a un certo punto che dovrà essere convertito in codice macchina specifico per il processore di linguaggio intermedio.

Esistono due punti in cui è possibile eseguire questo tipo di elaborazione:

- **In-time (JIT)** : durante l'avvio e l'esecuzione dell'applicazione viene compilato il linguaggio intermedio in memoria nel codice macchina.
- **Ahead di tempo (AOT)** : durante la compilazione, il linguaggio intermedio viene compilato e scritto librerie native e archiviato all'interno del bundle dell'applicazione.

Ogni opzione presenta numerosi vantaggi e gli svantaggi:

- **JIT**
  - **Tempo di avvio** : compilazione JIT deve essere eseguita all'avvio. Per la maggior parte delle applicazioni si tratta di ordine 100 ms, ma per applicazioni di grandi dimensioni, questa volta può essere significativamente più.
  - **Esecuzione** : codice come il JIT può essere ottimizzato per il processore specifico in uso, può essere generato un codice leggermente migliore. Nella maggior parte delle applicazioni equivale al massimo pochi punti percentuale più velocemente.
- **AOT**
  - **Tempo di avvio** : il caricamento di pre-compilate dylib è significativamente più veloce rispetto agli assembly JIT.
  - **Spazio su disco** – tali dylib può richiedere una quantità significativa di spazio su disco, tuttavia. A seconda di quali sono gli assembly AOTed, può raddoppiare o più le dimensioni della parte di codice dell'applicazione.
  - **Fase di compilazione** : compilazione AOT è significativamente più lenta che JIT e rallenterà le compilazioni di utilizzarlo. Questo rallentamento può variare da secondi fino a un minuto o più, a seconda delle dimensioni e il numero di assembly compilato.
  - **Offuscamento** , come il linguaggio intermedio, che è molto più semplice decompilare rispetto al codice macchina, non è necessariamente richiesta possono essere rimossi per offuscare codice sensibili. Questa operazione richiede l'opzione descritto di seguito "ibrido".

## <a name="enabling-aot"></a>Abilitazione AOT

Opzioni AOT verranno aggiunto al riquadro Mac compila in un aggiornamento futuro. Fino ad allora, l'abilitazione di AOT richiede passando un argomento della riga di comando mediante il campo "mmp ulteriori argomenti" di compilazione Mac. Le opzioni sono le seguenti:


      --aot[=VALUE]          Specify assemblies that should be AOT compiled
                               - none - No AOT (default)
                               - all - Every assembly in MonoBundle
                               - core - Xamarin.Mac, System, mscorlib
                               - sdk - Xamarin.Mac.dll and BCL assemblies
                               - |hybrid after option enables hybrid AOT which
                               allows IL stripping but is slower (only valid
                               for 'all')
                                - Individual files can be included for AOT via +
                               FileName.dll and excluded via -FileName.dll

                               Examples:
                                 --aot:all,-MyAssembly.dll
                                 --aot:core,+MyOtherAssembly.dll,-mscorlib.dll



## <a name="hybrid-aot"></a>Ibrida AOT

Durante l'esecuzione di un'applicazione macOS il runtime vengono automaticamente utilizzate codice macchina caricare le librerie native prodotti dalla compilazione AOT. Vi sono, tuttavia, alcune aree del codice, ad esempio trampolines, in cui la compilazione JIT può produrre risultati significativamente più ottimizzati. Questa operazione richiede il livello di assembly gestiti integrità sia disponibile. In iOS, le applicazioni si impedisce l'utilizzo della compilazione JIT. tali sezione di codice sono compilati anche AOT.

L'opzione ibrida indica al compilatore di entrambi compilazione questi sezione (ad esempio, iOS) ma anche per presuppone che il livello di integrità non sarà disponibile in fase di esecuzione. Questo IL può quindi essere rimossi post-compilazione. Come indicato in precedenza, il runtime sarà costretti a utilizzare le routine di meno ottimizzate in alcune occasioni.

## <a name="further-considerations"></a>Altre considerazioni

Le conseguenze negative della scala AOT con le dimensioni e il numero di assembly elaborati. La versione completa [framework di destinazione](~/mac/platform/target-framework.md) per l'esempio contiene un significativamente più grande classe libreria Base (BCL) più moderno, e pertanto AOT accetta notevolmente più lunghi e genera il bundle di dimensioni maggiore. Questo è composto da incompatibilità del framework di destinazione completo con il collegamento, che consente di rimuovere codice non usato. Provare a spostare l'applicazione moderno e abilitazione di collegamento per ottenere risultati ottimali.

Un ulteriore vantaggio di AOT dotato di interazioni migliorate con nativo, debug e profilatura toolchains. Poiché la maggior parte della base di codice verrà compilata anticipatamente, avranno nomi di funzione e simboli che sono più facili da leggere all'interno di segnalazioni di arresti anomali native, profilatura e debug. Funzioni generate JIT non questi nomi e spesso visualizzato come offset esadecimale senza nome molto difficili da risolvere.
