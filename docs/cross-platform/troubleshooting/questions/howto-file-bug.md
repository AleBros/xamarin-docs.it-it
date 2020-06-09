---
title: Quando e come è opportuno registrare un report sui bug?
description: Questo documento descrive quando, dove e come archiviare un report sui bug. Sono inoltre disponibili procedure consigliate per le segnalazioni dei bug che consentono ai tecnici di diagnosticare al meglio il problema.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: davidortinau
ms.author: daortin
ms.date: 08/01/2018
ms.openlocfilehash: 0dfffe2b3363a2d5498c0b8628b382a4331b9269
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571454"
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>Quando e come è opportuno registrare un report sui bug?

> [!TIP]
> Usare la voce di menu **segnala un problema** in Visual Studio. in &ndash; questo modo, le informazioni di diagnostica vengono inviate insieme al report dei bug per facilitare la risoluzione del problema.
>
> Sono disponibili istruzioni dettagliate per [Visual studio 2019 o Visual studio 2017](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio) e [Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/report-a-problem).
>
> È possibile cercare report esistenti nel sito Web della [community degli sviluppatori di Visual Studio](https://developercommunity.visualstudio.com/) .

## <a name="file-a-bug-if"></a>Segnala un bug se...

È necessario disporre di una serie di passaggi che i tecnici potranno utilizzare per riprodurre un problema.

OPPURE

È possibile descrivere con attenzione i sintomi del problema, soprattutto se è possibile descrivere anche alcune circostanze precise correlate al problema. <sup> [[1]](#note-1)</sup>

## <a name="best-practices-to-help-address-bugs-quickly-and-efficiently"></a>Procedure consigliate per risolvere i bug in modo rapido ed efficiente

1. <a name="ref-1"></a>Cerca nella [community degli sviluppatori di Visual Studio](https://developercommunity.visualstudio.com/) e nel Web le segnalazioni di bug esistenti o i suggerimenti di utilizzo che potrebbero risolvere direttamente il problema. <sup>[[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2"></a>Descrivere il problema nel modo più chiaro e conciso possibile, inclusa una descrizione degli eventi che si sono verificati e che era previsto.

1. <a name="ref-3"></a>Includere eventuali analisi dello stack, testo del messaggio di errore o log di arresto anomalo (se si utilizza la funzionalità **segnala un problema** , questi possono essere inclusi automaticamente). <sup>[4](#note-4)</sup>

1. <a name="ref-4"></a>Annotare tutti i messaggi di errore importanti visualizzati negli allegati screenshot come testo normale.

1. <a name="ref-5"></a>Includere un piccolo test case autonomo che riproduce il bug con il minor quantità possibile di codice.  Se non è possibile riprodurre il problema con un nuovo progetto, creato usando uno dei modelli predefiniti, è necessario comprimere un progetto che illustra il problema e associarlo al report dei bug.  Rendere il progetto di esempio il più semplice possibile prima di collegarlo. <sup>[[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6"></a>Descrivere l'ambiente in cui è stato rilevato il bug, inclusi il sistema operativo e le [versioni di Novell](~/cross-platform/troubleshooting/questions/version-logs.md) e le eventuali dipendenze.

## <a name="additional-details"></a>Dettagli aggiuntivi

1. <a name="note-1"></a>[*^*](#ref-1)Idealmente, la descrizione dei "sintomi visibili" deve includere informazioni sufficienti in modo che gli altri clienti possano verificare se visualizzano lo stesso problema (gli stessi messaggi di errore, lo stesso peggioramento delle prestazioni, lo stesso traccia dello stack da un arresto anomalo e così _via_). Per "circostanze precise", un esempio valido è se è possibile pronunciare un errore simile al seguente: "normalmente si è verificato il problema 75% del tempo, ma se si modifica questa operazione, è possibile evitare il problema completamente". Un altro esempio simile di "circostanza precisa" è se il downgrade a una versione precedente di Novell interrompe il problema.

1. <a name="note-2"></a>[*^*](#ref-2)Come ci si aspetterebbe, i frammenti di testo di errore (o qualsiasi altro testo descrittivo in modo univoco) sono in genere i termini di ricerca migliori. Se il report dei bug esistente è incompleto, è possibile aggiungere dettagli o archiviare un nuovo report sui bug migliore.

1. <a name="note-3"></a>[*^*](#ref-3)Un'altra domanda interessante è se lo stesso problema è stato segnalato per qualsiasi app Java, Objective-C o SWIFT. In tal caso, è molto probabile che il problema sia parte di Android o iOS, anziché parte di Novell.

1. <a name="note-4"></a>[*^*](#ref-4)Di seguito sono riportati alcuni esempi di informazioni da includere:

    1. Per gli errori che si verificano durante la compilazione di un progetto, includere l' [output di compilazione diagnostica](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output) completo nel report dei bug.

    1. Per gli errori che si verificano durante la compilazione o il debug di un progetto iOS da Visual Studio, eseguire la **guida > novell > log zip** dopo aver raggiunto l'errore e includere il file con estensione zip risultante nel report dei bug.

    1. Per le eccezioni o gli arresti anomali nelle app Android o iOS, includere i [log di debug pertinenti per le app Novell. Android e Novell. iOS](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps).

1. <a name="note-5"></a>[*^*](#ref-5)Se possibile per un problema specifico, un'opzione consiste nel ricreare il problema aggiungendo un numero ridotto di file dalla soluzione originale in una soluzione completamente nuova. Il team di Novell sarà spesso in grado di analizzare i problemi anche nei test case più grandi (presupponendo che i passaggi da riprodurre siano illustrati in modo chiaro), ma i test case più semplici offrono la migliore probabilità che il bug venga risolto rapidamente.

1. <a name="note-6"></a>[*^*](#ref-6)Se _non_ è possibile riprodurre il problema aggiungendo un numero ridotto di file a una nuova soluzione, è possibile eseguire il comprimere e allineare l'intera cartella della soluzione per l'app completa. Eliminare le `bin` cartelle, `obj` , `Components` e `packages` per ridurre il file zip. L'IDE e il processo di compilazione in genere ripristinano o ricreano il contenuto di queste cartelle in base alle esigenze. È anche possibile eliminare tutti i file di codice e di risorse dal progetto come si desidera, purché la soluzione risultante mostri ancora il problema originale.
