---
title: Compilazione ahead of Time di Novell. Mac
description: In questo documento viene descritta la compilazione anticipata in Novell. Mac. Viene confrontata la compilazione AOT con la compilazione JIT, viene illustrato come abilitare AOT e viene esaminata l'AOT ibrida.
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: e1f1e2e1e5dbec7dc8f2310b3f9565d0bc209c00
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283685"
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>Compilazione ahead of Time di Novell. Mac

## <a name="overview"></a>Panoramica

La compilazione ahead of Time (AOT) è una tecnica di ottimizzazione efficace per migliorare le prestazioni di avvio. Tuttavia, influiscono anche sul tempo di compilazione, sulle dimensioni dell'applicazione e sull'esecuzione del programma in modi profondi. Per comprendere i compromessi che impone, è possibile approfondire la compilazione e l'esecuzione di un'applicazione.

IL codice scritto nei linguaggi gestiti, ad C# esempio F#e, viene compilato in una rappresentazione intermedia denominata il. Questo IL, archiviato nella libreria e negli assembly del programma, è relativamente compatto e portatile tra le architetture del processore. IL, tuttavia, è solo un set di istruzioni intermedio e, a un certo punto, IL linguaggio IL dovrà essere convertito in codice macchina specifico del processore.

Questa elaborazione può essere eseguita in due punti:

- **JIT (just-in-Time)** : durante l'avvio e l'esecuzione dell'applicazione, IL linguaggio il viene compilato in memoria per il codice macchina.
- **Ahead of Time (AOT)** : durante la compilazione il linguaggio intermedio viene compilato e scritto nelle librerie native e archiviato nel bundle dell'applicazione.

Ogni opzione presenta diversi vantaggi e compromessi:

- **JIT**
  - **Tempo di avvio** : la compilazione JIT deve essere eseguita all'avvio. Per la maggior parte delle applicazioni si tratta dell'ordine di 100 ms, ma per le applicazioni di grandi dimensioni questo tempo può essere notevolmente maggiore.
  - **Esecuzione** : poiché il codice JIT può essere ottimizzato per l'utilizzo di un processore specifico, è possibile generare codice leggermente migliore. Nella maggior parte delle applicazioni si tratta di una percentuale più veloce di punti percentuali.
- **AOT**
  - **Tempo di avvio** : il caricamento di dylib precompilati è significativamente più veloce degli assembly JIT.
  - **Spazio su disco** : i dylib possono richiedere una quantità significativa di spazio su disco. A seconda degli assembly AOTed, può raddoppiare o più le dimensioni della parte del codice dell'applicazione.
  - **Tempo di compilazione** : la compilazione AOT è significativamente più lenta che JIT e rallenterà le compilazioni che lo usano. Questo rallentamento può variare da secondi fino a un minuto o più, a seconda delle dimensioni e del numero di assembly compilati.
  - **Offuscamento** : come il, che è significativamente più facile decompilare il codice del computer, non è necessariamente necessario rimuoverlo per consentire l'offuscamento del codice sensibile. Questa operazione richiede l'opzione "ibrido" descritta di seguito.

## <a name="enabling-aot"></a>Abilitazione AOT

Le opzioni AOT verranno aggiunte al riquadro compilazione Mac in un aggiornamento futuro. Fino a quel momento, abilitando AOT è necessario passare un argomento della riga di comando tramite il campo "argomenti aggiuntivi MMP" nella compilazione Mac. Le opzioni sono le seguenti:

```
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
```


## <a name="hybrid-aot"></a>AOT ibrida

Durante l'esecuzione di un'applicazione macOS, il runtime usa il codice macchina caricato dalle librerie native generate dalla compilazione AOT. Esistono, tuttavia, alcune aree di codice, ad esempio i trampolini, in cui la compilazione JIT può produrre risultati significativamente più ottimizzati. A questo scopo è necessario che gli assembly gestiti siano disponibili. In iOS le applicazioni sono limitate da qualsiasi uso della compilazione JIT; anche queste sezioni di codice sono compilate in AOT.

L'opzione ibrida indica al compilatore di compilare questa sezione (ad esempio iOS), ma anche di presupporre che il linguaggio il non sarà disponibile in fase di esecuzione. Questo IL può quindi essere rimosso dopo la compilazione. Come indicato in precedenza, il runtime verrà forzato a usare le routine meno ottimizzate in alcune posizioni.

## <a name="further-considerations"></a>Altre considerazioni

Conseguenze negative della scala AOT con le dimensioni e il numero di assembly elaborati. Il [Framework di destinazione](~/mac/platform/target-framework.md) completo, ad esempio, contiene una libreria di classi di base (BCL) significativamente più grande rispetto alla moderna, quindi l'AOT sarà molto più lunga e produrrà bundle più grandi. Questa operazione è composta dall'incompatibilità del Framework di destinazione completo con il collegamento, che rimuove il codice inutilizzato. Provare a trasferire l'applicazione alla moderna e ad abilitare il collegamento per ottenere risultati ottimali.

Un ulteriore vantaggio di AOT è costituito da interazioni migliorate con il debug e il profiling nativi toolchain. Poiché la maggior parte della codebase verrà compilata in anticipo, i nomi delle funzioni e i simboli saranno più facili da leggere all'interno di report di arresti anomali, profilatura e debug nativi. Le funzioni generate da JIT non hanno questi nomi e spesso vengono visualizzate come offset esadecimali non denominati che sono molto difficili da risolvere.
