---
title: Quando e come devo file di un report sui bug?
ms.topic: article
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 3a57c0843a68b454c8cb21c95b280d2731d064cd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>Quando e come devo file di un report sui bug?


Segnalare i bug in seguito individuazione di bug Bugzilla di Xamarin: [https://bugzilla.xamarin.com/enter_bug.cgi?classification=__all](https://bugzilla.xamarin.com/enter_bug.cgi?classification=__all).

## <a name="file-a-bug-if"></a>Segnalare un bug se...


Si dispone di un set di passaggi si ritiene che i tecnici Xamarin saranno possibile utilizzare per riprodurre un problema causato da Xamarin.

OR

Con attenzione, è possibile descrivere i sintomi visibili di questo problema, soprattutto se è inoltre possibile descrivere alcune circostanze precisi correlati al problema. <sup> [[1]](#note-1)</sup>


## <a name="best-practices-to-help-xamarin-address-bugs-quickly-and-efficiently"></a>Procedure consigliate per correggere i bug Xamarin rapido ed efficiente


1. <a name="ref-1" />Ricerca [Bugzilla](https://bugzilla.xamarin.com/query.cgi?format=specific&amp;bug_status=__all__) e web esistenti bug report o suggerimenti di utilizzo che potrebbero risolvere il problema direttamente.<sup> [[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2" />Seguire il [bug scrittura linee guida](https://bugzilla.xamarin.com/page.cgi?id=bug-writing.html) per descrivere il problema in modo chiaro e conciso possibile, inclusa una descrizione di ciò che si è verificata e stato previsto per verificarsi.

1. <a name="ref-3" />Includere eventuali tracce dello stack rilevanti, il testo del messaggio di errore o di arresto anomalo del log. <sup>[[4]](#note-4)</sup>

1. <a name="ref-4" />Prendere nota di eventuali messaggi di errore importanti che vengono visualizzate nella schermata allegati come testo normale troppo.

1. <a name="ref-5" />Includere un piccolo e indipendente, test case che riproduce il bug con codice minimo possibile.  Se è possibile riprodurre il problema relativo a un progetto nuovo (creato con uno dei modelli predefiniti), quindi, comprimere un progetto che dimostra il problema e associarla a report sul bug.  Rendere più semplice possibile il progetto di esempio prima di collegarlo. <sup> [[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6" />Descrive l'ambiente in cui è stato rilevato il bug, inclusi il sistema operativo e [versioni di Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md) ed eventuali dipendenze.

---

## <a name="additional-details"></a>Dettagli aggiuntivi

1. <a name="note-1" />[*^*](#ref-1) In teoria la descrizione di Sintomi"visibili" deve includere dettagli sufficienti in modo che altri clienti è possono verificare se visualizzati lo stesso problema (stessi messaggi di errore, un calo delle prestazioni stesso, stessa traccia dello stack a un arresto anomalo, _e così via._ ). Per "circostanze precisi", un buon esempio sarebbe se è possibile ad esempio simile al seguente: "in genere il problema 75% riscontri del tempo, ma se si modifica questa condizione quindi è possibile evitare il problema completamente." Un altro esempio simile di una circostanza"precisa" è se il downgrade a una versione precedente di Xamarin interrompe il problema.

1. <a name="note-2" />[*^*](#ref-2) Come prevedibile, frammenti di testo di errore (o qualsiasi altro testo descrittivo in modo univoco) sono in genere i termini di ricerca migliori. Se il report di bug esistente è incompleto, quindi aggiungere i dettagli o il file di un nuovo sono, meglio bug report.

1. <a name="note-3" />[*^*](#ref-3) Un'altra domanda buona è se lo stesso problema è stato segnalato per qualsiasi linguaggio, Objective-C o App Swift. In questo caso, il problema è molto probabile che parte di Android o iOS stesso anziché come parte di Xamarin.

1. <a name="note-4" />[*^*](#ref-4) Alcuni esempi di informazioni da includere:

    1. Per gli errori che si verificano quando si compila un progetto, includere l'intero [output di compilazione diagnostica](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output) per il report di bug.
    
    1. Per gli errori che si verificano durante la compilazione o di debug di un progetto iOS da Visual Studio, eseguire _Guida > Xamarin > log Zip_ dopo aver individuato l'errore e includere il file ZIP risultante per il report di bug.
    
    1. Per le eccezioni o arresti anomali in App Android o iOS, includere rilevanti "[Debug registri per le app xamarin e xamarin](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps)."

1. <a name="note-5" />[*^*](#ref-5) Se possibile per un problema specifico, un eccellente consiste nel ricreare il problema aggiungendo un numero ridotto di file dalla soluzione originale in una nuova soluzione. Il team di Xamarin spesso sarà in grado di analizzare i problemi anche su dimensioni maggiori di test case (presupponendo che la procedura per riprodurre è illustrata chiaramente), ma più semplice assegnare test case che il miglior possibilità che il bug verrà risolti rapidamente.


1. <a name="note-6" />[*^*](#ref-6) Se è _non_ possibile riprodurre il problema aggiungendo un numero ridotto di file a una nuova soluzione, quindi è possibile comprimere e collegare la cartella dell'intera soluzione per l'applicazione completa. Eliminare il `bin`, `obj`, `Components`, e `packages` cartelle per rendere il file zip file più piccoli. (L'IDE e il processo di compilazione verranno in genere ripristinare o ricreare il contenuto di queste cartelle in base alle necessità.) Inoltre, è possibile eliminare tante codice e file di risorse dal progetto desiderata, purché la soluzione risultante è ancora dimostra il problema originale.

