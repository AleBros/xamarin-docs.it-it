---
title: Risoluzione dei problemi di associazione
description: Questa guida viene descritto cosa fare se si riscontrano difficoltà nell'associazione di una libreria Objective-C. In particolare, vengono illustrate le associazioni mancante, eccezioni di argomento quando si passa null per un'associazione e la segnalazione dei bug.
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
author: asb3993
ms.author: amburns
ms.date: 10/19/2016
ms.openlocfilehash: b0ce6c3792d6495cabdf8acdab1a644fe75f1219
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780235"
---
# <a name="binding-troubleshooting"></a>Risoluzione dei problemi di associazione

Alcuni suggerimenti per la risoluzione dei problemi di associazioni per macOS (precedentemente noto come OS X) API in Xamarin.Mac.

## <a name="missing-bindings"></a>Associazioni mancante

Mentre Xamarin.Mac copre la maggior parte delle API di Apple, in alcuni casi potrebbe essere necessario chiamare l'API Apple che non dispone di un'associazione ancora. In altri casi, è necessario chiamare C/Objective-C di terze parti che non rientrano nell'ambito delle associazioni Xamarin.Mac.

Se si sta utilizzando un'API di Apple, il primo passaggio è informare Xamarin che si raggiunge una sezione dell'API che non abbiamo code coverage per ancora. [Segnalare un bug](#reporting-bugs) notare l'API manca. Utilizziamo report dai clienti per classificare le API lavoriamo facendo clic su Avanti. Inoltre, se si dispone di una licenza aziendale e la mancanza di un'associazione è il blocco dello stato di avanzamento, inoltre seguire le istruzioni in [supporto](http://xamarin.com/support) per inviare un ticket. Non possiamo vedrai un'associazione, ma in alcuni casi è possibile aggirare è un lavoro.

Una volta inviare notifiche Xamarin (se applicabile) dell'associazione mancante, il passaggio successivo è da prendere in considerazione l'associazione. È disponibile una guida completa [qui](~/cross-platform/macios/binding/overview.md) e alcuni documenti non ufficiale [qui](http://brendanzagaeski.appspot.com/xamarin/0002.html) per eseguire il wrapping Objective-C associazioni manualmente. Se si sta chiamando un'API di C, è possibile utilizzare il meccanismo P/Invoke #, documentazione [qui](http://www.mono-project.com/docs/advanced/pinvoke/).

Se si decide di utilizzare l'associazione manualmente, tenere presente che gli errori di associazione possono generare tutti i tipi di arresti anomali interessanti nel runtime nativo. In particolare, prestare molta attenzione che la firma in c# corrisponde alla firma nativa nel numero di argomenti e le dimensioni di ciascun argomento. In caso contrario, potrebbe essere danneggiata memoria e/o lo stack e potrebbe arrestarsi in modo anomalo immediatamente o in un punto arbitrario in futuro o danneggiare i dati.

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>Eccezioni di argomento quando si passa null per un'associazione

Durante il funzionamento di Xamarin per fornire elevata qualità e le associazioni e testate per le API di Apple, talvolta errori e i bug di trasporto. Problema di gran lunga più comune che verifichino sono un'API generazione `ArgumentNullException` quando si passa null quando l'API sottostante accetta `nil`. I file di intestazione native che definiscono l'API spesso non forniscono informazioni sufficienti su cui accettano nil API e che verrà arrestato in modo anomalo se viene passato in.

Se si esegue un caso in cui passando `null` genera un `ArgumentNullException` ma si ritiene che deve funzionare, seguire questi passaggi:

1. Controllare la documentazione di Apple e/o esempi per vedere se è possibile trovare la prova che accetta `nil`. Se si ha familiarità con Objective-C, è possibile scrivere un programma di test di piccole dimensioni per la verifica.
2. [Segnalare un bug](#reporting-bugs).
3. Può risolvere i bug? Se è possibile evitare la chiamata dell'API con `null`, un semplice controllo null intorno alle chiamate può essere semplice per risolvere il problema.
4. Tuttavia, alcune API richiedono il passaggio di null per disattivare o disabilitare alcune funzionalità. In questi casi, è possibile risolvere il problema riportando il browser di assembly (vedere [trovare il membro in c# per un determinato selettore](~/mac/app-fundamentals/mac-apis.md#finding_selector)), la copia dell'associazione e la rimozione di controllo null. Assicurarsi di archiviare un bug (passaggio 2) se è possibile, come l'associazione copiato non riceverà gli aggiornamenti e correzioni apportate in Xamarin.Mac, e questo deve essere considerato per risolvere il problema a breve termine.

<a name="reporting-bugs"/>

## <a name="reporting-bugs"></a>Segnalazione dei bug

Commenti e suggerimenti sono importanti per Microsoft. Se i problemi di Xamarin.Mac:

- Controllare i [forum di Xamarin.Mac](https://forums.xamarin.com/categories/mac)
- Cercare nel [repository di problemi](https://github.com/xamarin/xamarin-macios/issues) 
- Prima di passare ai problemi di GitHub, si è tenuta traccia dei problemi di Xamarin in [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi), dove è possibile cercare i problemi corrispondenti.
- Se non si riesce a trovare un problema corrispondente, inserirne uno nuovo nel [repository di problemi GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

I problemi di GitHub sono tutti pubblici. Non è possibile nascondere commenti o allegati. 

Includere tutte le informazioni disponibili seguenti:

- Un esempio semplice che riproduce il problema. Questo è **molto importante**, ove possibile. 
- L'analisi dello stack completa dell'arresto anomalo.
- Il codice C# relativo all'arresto anomalo. 
