---
title: Xamarin. Mac in anticipo rispetto alla fase di compilazione
description: Questo documento descrive in anticipo rispetto alla compilazione in xamarin. Mac. Confronta la compilazione AOT alla compilazione JIT, spiega come abilitare AOT e si esaminano le AOT ibrido.
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: e155a394afd68d9970ee32785f6d0aeda6e2d129
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117259"
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>Xamarin. Mac in anticipo rispetto alla fase di compilazione

## <a name="overview"></a>Panoramica

Anticipo del tempo (AOT) della compilazione è una tecnica di ottimizzazione avanzate per migliorare le prestazioni di avvio. Tuttavia, influisce anche il tempo di compilazione, le dimensioni dell'applicazione e l'esecuzione del programma in modi profondi. Per comprendere i compromessi che vengono imposti, dobbiamo approfondire leggermente la compilazione e l'esecuzione di un'applicazione.

Il codice scritto in linguaggi, gestiti come C# e F#, viene compilato in una rappresentazione intermedia chiamata linguaggio intermedio. Questo linguaggio intermedio, archiviato nella libreria e il programma assembly, è relativamente compatta e portabile tra le architetture di processori. Livello di integrità, tuttavia, è solo intermedio set di istruzioni e a un certo punto che IL dovrà essere convertito in codice macchina specifico del processore.

Esistono due punti in cui questa elaborazione può essere eseguita:

- **Just-in-time (JIT)** : durante l'avvio e l'esecuzione dell'applicazione il linguaggio intermedio viene compilato in memoria in codice macchina.
- **Ahead di tempo (AOT)** : durante la compilazione, il linguaggio intermedio viene compilato e scritto in librerie native e archiviato all'interno del bundle dell'applicazione.

Ogni opzione offre numerosi vantaggi e compromessi:

- **JIT**
  - **Tempo di avvio** : la compilazione JIT deve essere eseguita all'avvio. Per la maggior parte delle applicazioni si è nell'ordine di 100 ms, ma per applicazioni di grandi dimensioni, questo tempo può essere significativamente più.
  - **Esecuzione** – codice come il JIT può essere ottimizzato per il processore specifico in uso, è possibile generare codice leggermente più efficiente. Nella maggior parte delle applicazioni si è al massimo pochi punti percentuale più velocemente.
- **AOT**
  - **Tempo di avvio** : il caricamento di pre-compilate dylibs è notevolmente più veloce rispetto agli assembly JIT.
  - **Spazio su disco** – tali dylibs può tuttavia richiedere una notevole quantità di spazio su disco. A seconda di quali assembly vengono AOTed, è possibile fare doppio o più le dimensioni della parte di codice dell'applicazione.
  - **Fase di compilazione** : la compilazione AOT è molto più lenta tale JIT e rallenterà le compilazioni utilizzarlo. Questo rallentamento può variare da secondi fino a un minuto o più, a seconda delle dimensioni e dal numero di assembly compilato.
  - **Offuscamento** , come il linguaggio intermedio, che è molto più semplice decompilare rispetto al codice macchina, non è necessariamente richiesta possono essere rimossi per nascondere il codice sensibili. Questa operazione richiede l'opzione descritto di seguito "ibrido".

## <a name="enabling-aot"></a>Abilita AOT

Opzioni di AOT verranno aggiunto al riquadro di compilazione Mac in un aggiornamento futuro. Fino ad allora, Abilita AOT richiede il passaggio di un argomento della riga di comando tramite il campo "argomenti aggiuntivi di mmp" nella compilazione Mac. Le opzioni sono le seguenti:


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



## <a name="hybrid-aot"></a>AOT ibrido

Durante l'esecuzione di un'applicazione macOS il runtime Usa codice macchina caricati dalle librerie native prodotte dalla compilazione AOT. Esistono, tuttavia, alcune aree del codice, ad esempio trampolines, in cui la compilazione JIT può produrre risultati molto più ottimizzati. Questa operazione richiede il livello di assembly gestiti integrità sia disponibile. In iOS, le applicazioni sono limitate da eventuali usi della compilazione JIT; le sezione di codice sono AOT compilati x.

Opzione ibrida indica al compilatore di entrambi compilazione questi sezione (ad esempio, iOS) ma anche a presupporre che il linguaggio intermedio non sarà disponibile in fase di esecuzione. Questo livello di integrità può quindi essere rimosso dopo la compilazione. Come indicato in precedenza, il runtime sarà costretti a usare meno con ottimizzazione per la routine in alcune posizioni.

## <a name="further-considerations"></a>Altre considerazioni

Le conseguenze negative della scala AOT con le dimensioni e numero di assembly che elaborate. La versione completa [framework di destinazione](~/mac/platform/target-framework.md) per esempio contiene un significativamente più grande Base della libreria di classi più moderno, e pertanto AOT accetta significativamente più lunghi e genera il bundle di dimensioni maggiori. Ciò viene composto dall'incompatibilità del framework di destinazione completo con il collegamento, che consente di eliminare i codice inutilizzato. Provare a spostare l'applicazione moderno e abilitare il collegamento per ottenere risultati ottimali.

Un ulteriore vantaggio di AOT include interazioni migliorate con native di debug e profilatura toolchain. Poiché la maggior parte della codebase verrà compilata anticipatamente, avrà nomi di funzione e simboli che sono più facili da leggere all'interno di report di arresto anomalo nativo, profilatura e debug. Funzioni JIT generato non dispone di questi nomi e spesso visualizzato come offset esadecimale senza nome sono molto difficili da risolvere.
