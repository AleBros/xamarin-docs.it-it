---
title: Quando e come è opportuno registrare un report sui bug?
description: Questo documento viene descritto quando, dove e come a un report sui bug. Fornisce inoltre report sui bug procedure consigliate che consentono agli ingegneri di meglio diagnosticare il problema.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: conceptdev
ms.author: crdun
ms.date: 08/01/2018
ms.openlocfilehash: 1224d38a2230fa2f5c7ca08f6e33c5468886c206
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58855211"
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>Quando e come è opportuno registrare un report sui bug?

> [!TIP]
> Usare la **segnalare un problema** voce di menu in Visual Studio &ndash; questa invierà le informazioni di diagnostica insieme ai report bug per consentire di risolvere il problema.
>
> Sono disponibili istruzioni dettagliate sul [2019 di Visual Studio o Visual Studio 2017](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio) e [Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/report-a-problem).
>
> È possibile cercare i report esistenti nel [Visual Studio Developer Community](https://developercommunity.visualstudio.com/) sito Web.

## <a name="file-a-bug-if"></a>Segnalare un bug se...

Si dispone di un set di passaggi si ritiene che i tecnici saranno possibile usare per riprodurre un problema.

OR

È possibile descrivere i sintomi del problema, visibili con cautela soprattutto se è possibile anche descrivere alcune circostanze precisi correlati al problema. <sup> [[1]](#note-1)</sup>

## <a name="best-practices-to-help-address-bugs-quickly-and-efficiently"></a>Le procedure consigliate per correggere i bug rapidamente e in modo efficiente

1. <a name="ref-1" />Ricerca [Visual Studio Developer Community](https://developercommunity.visualstudio.com/) e il web esistente bug report o suggerimenti sull'utilizzo che potrebbero risolvere il problema direttamente.<sup> [[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2" />Descrivere il problema in modo chiaro e conciso possibile, inclusa una descrizione di ciò che si sono verificati ed è stata prevista.

1. <a name="ref-3" />Includere le analisi dello stack pertinenti, testo messaggio di errore o log di arresto anomalo (se si usa la **segnalare un problema** feature, questi possono essere inclusi automaticamente). <sup>[[4]](#note-4)</sup>

1. <a name="ref-4" />Prendere nota di eventuali messaggi di errore importante visualizzate negli screenshot allegati come testo normale troppo.

1. <a name="ref-5" />Includere un piccolo, indipendente test case che riproduca il bug con poco codice possibile.  Se non è possibile riprodurre il problema con un progetto nuovo (creato con uno dei modelli predefiniti), quindi, comprimere un progetto che dimostra il problema e allegarla al report sul bug.  Eseguire il progetto di esempio più semplice possibile prima di allegarlo. <sup> [[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6" />Descrive l'ambiente in cui è stato rilevato il bug, tra cui il sistema operativo e [le versioni di Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md) ed eventuali dipendenze.

## <a name="additional-details"></a>Dettagli aggiuntivi

1. <a name="note-1" />[*^*](#ref-1) In teoria la descrizione dei "Sintomi visibile" deve includere dettagli sufficienti in modo che altri clienti possono verificare se si riscontrano lo stesso problema (stessi messaggi di errore, una riduzione delle prestazioni stesso, stessa analisi dello stack da un arresto anomalo, _e così via._ ). Per "precisi circostanze", un buon esempio sarebbe se è possibile dire qualcosa di simile: "Scelgo normalmente il problema il 75% del tempo, ma se è possibile modificare questa condizione quindi è possibile evitare il problema completamente". Un altro esempio simile di "circostanza preciso" è se il downgrade a una versione precedente di Xamarin si arresta il problema.

1. <a name="note-2" />[*^*](#ref-2) Come si può immaginare, frammenti di testo di errore (o qualsiasi altro testo descrittivo univoco) sono in genere i termini di ricerca migliori. Se il report sui bug esistenti è incompleto, quindi siete invitati a file nuovo o aggiungere i dettagli, meglio report sui bug.

1. <a name="note-3" />[*^*](#ref-3) Un'altra domanda da porsi è se lo stesso problema è stato segnalato per qualsiasi linguaggio, Objective-C, o le app Swift. In questo caso, il problema è molto probabile che parte di Android o iOS stesso anziché come parte di Xamarin.

1. <a name="note-4" />[*^*](#ref-4) Alcuni esempi di informazioni da includere:

    1. Per gli errori che si verificano quando si compila un progetto, includere l'intero [output di compilazione diagnostica](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output) nel report sul bug.

    1. Per gli errori che si verificano quando si compila o il debug di un progetto iOS da Visual Studio, eseguire **aiutare > Xamarin > Comprimi log** dopo aver individuato l'errore e includere il file ZIP risultante in report sul bug.

    1. Per le eccezioni o arresti anomali nelle App Android o iOS, includere il relativo [eseguire il Debug dei log per le app xamarin. Android e xamarin. IOS](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps).

1. <a name="note-5" />[*^*](#ref-5) Se possibile per un problema specifico, una delle opzioni è possibile ricreare il problema aggiungendo un numero ridotto di file dalla soluzione originale in una soluzione nuova di zecca. Il team di Xamarin spesso sarà in grado di analizzare i problemi anche nei casi di test più grandi (presupponendo che sono illustrati chiaramente i passaggi per riprodurre), ma più semplice test case offrono che le migliori possibilità che il bug verrà risolto rapidamente.

1. <a name="note-6" />[*^*](#ref-6) Se si tratta _non_ possibili per riprodurre il problema aggiungendo un numero ridotto di file a una nuova soluzione, quindi è possibile comprimere e collegare la cartella intera soluzione per l'app completa. Eliminare il `bin`, `obj`, `Components`, e `packages` cartelle per rendere il file zip di file più piccoli. (L'IDE e il processo di compilazione verranno in genere ripristinare o ricreare il contenuto di queste cartelle in base alle esigenze). È anche possibile eliminare tante risorse e codice file dal progetto nel modo desiderato, purché la soluzione risultante Mostra comunque il problema originale.
