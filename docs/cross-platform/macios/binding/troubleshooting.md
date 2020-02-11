---
title: Risoluzione dei problemi di associazione
description: Questa guida descrive le operazioni da eseguire in caso di difficoltà nell'associazione di una libreria Objective-C. In particolare, vengono illustrate le associazioni mancanti, le eccezioni degli argomenti quando si passa null a un'associazione e si segnalano bug.
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
author: davidortinau
ms.author: daortin
ms.date: 10/19/2016
ms.openlocfilehash: fe4b9ade9e6e462c3472a8bb3bb8750ed6cac326
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015882"
---
# <a name="binding-troubleshooting"></a>Risoluzione dei problemi di associazione

Alcuni suggerimenti per la risoluzione dei problemi relativi alle associazioni alle API macOS (precedentemente note come OS X) in Xamarin.Mac.

## <a name="missing-bindings"></a>Associazioni mancanti

Sebbene Xamarin.Mac copra la maggior parte delle API Apple, a volte potrebbe essere necessario chiamare un'API Apple che non ha ancora un'associazione. In altri casi, è necessario chiamare la terza parte C/Objective-C che esula dall'ambito delle associazioni Xamarin.Mac.

Se si sta usando un'API Apple, il primo passaggio consiste nel lasciare che Xamarin sappia che si sta colpendo una sezione dell'API per cui non è ancora presente una copertura. [Archiviare un bug](#reporting-bugs) notando l'API mancante. Utilizziamo i report dei clienti per definire la priorità delle API che lavoriamo successivamente. Inoltre, se si dispone di una licenza aziendale o Enterprise e la mancanza di un binding blocca lo stato di avanzamento, seguire anche le istruzioni disponibili in [supporto](https://visualstudio.microsoft.com/vs/support/) per il file di un ticket. Non è possibile promettere un'associazione, ma in alcuni casi è possibile ottenere una soluzione.

Quando si invia una notifica a Xamarin (se applicabile) dell'associazione mancante, il passaggio successivo consiste nel prendere in considerazione l'associazione. [Qui abbiamo una guida completa](~/cross-platform/macios/binding/overview.md) e una documentazione non ufficiale per il wrapping [manuale dei binding](https://brendanzagaeski.appspot.com/xamarin/0002.html) Objective-C. Se si chiama un'API C, è possibile usare C#il meccanismo P/Invoke. la documentazione è disponibile [qui](https://www.mono-project.com/docs/advanced/pinvoke/).

Se si decide di lavorare sul binding autonomamente, tenere presente che gli errori nell'associazione possono produrre arresti anomali interessanti nel runtime nativo. In particolare, prestare molta attenzione che la firma in C# corrisponda alla firma nativa in numero di argomenti e alla dimensione di ogni argomento. In caso contrario, potrebbe danneggiare la memoria e/o lo stack ed è possibile che si verifichi un arresto anomalo immediatamente o in un punto arbitrario nel futuro o nei dati danneggiati.

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>Eccezioni degli argomenti durante il passaggio di valori null a un'associazione

Sebbene Xamarin funzioni per fornire associazioni di qualità elevata e collaudate per le API Apple, a volte gli errori e i bug scivolano. Il problema più comune che è possibile eseguire è che un'API genera `ArgumentNullException` quando si passa null quando l'API sottostante accetta `nil`. I file di intestazione nativi che definiscono l'API spesso non forniscono informazioni sufficienti sulle API che accettano nil e che si arresteranno in modo anomalo se lo si passa.

Se si verifica un caso in cui il passaggio di `null` genera un'`ArgumentNullException` ma si ritiene che funzioni correttamente, attenersi alla procedura seguente:

1. Consultare la documentazione di Apple e/o gli esempi per verificare se è possibile trovare la prova che accetta `nil`. Se si ha dimestichezza con Objective-C, è possibile scrivere un programma di test di piccole dimensioni per verificarlo.
2. [Archiviare un bug](#reporting-bugs).
3. È possibile risolvere il problema? Se è possibile evitare di chiamare l'API con `null`, un semplice controllo null intorno alle chiamate può essere una soluzione semplice.
4. Alcune API, tuttavia, richiedono il passaggio di valori null per disattivare o disabilitare alcune funzionalità. In questi casi, è possibile aggirare il problema aprendo il browser assembly (vedere [ C# ricerca del membro per un determinato selettore](~/mac/app-fundamentals/mac-apis.md#finding_selector)), copiando l'associazione e rimuovendo il controllo null. Assicurarsi di archiviare un bug (passaggio 2) se si esegue questa operazione, in quanto l'associazione copiata non riceverà gli aggiornamenti e le correzioni apportate in Xamarin.Mac e questa operazione dovrebbe essere considerata a breve termine.

<a name="reporting-bugs"/>

## <a name="reporting-bugs"></a>Segnalazione di bug

Il feedback è importante per noi. Se si riscontrano problemi con Xamarin.Mac:

- Controllare i [forum di Xamarin.Mac](https://forums.xamarin.com/categories/mac)
- Cercare nel [repository di problemi](https://github.com/xamarin/xamarin-macios/issues) 
- Prima di passare ai problemi di GitHub, si è tenuta traccia dei problemi di Xamarin in [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi), dove è possibile cercare i problemi corrispondenti.
- Se non si riesce a trovare un problema corrispondente, inserirne uno nuovo nel [repository di problemi GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

I problemi di GitHub sono tutti pubblici. Non è possibile nascondere commenti o allegati. 

Includere tutte le informazioni disponibili seguenti:

- Un esempio semplice che riproduce il problema. Questo è **molto importante**, ove possibile. 
- L'analisi dello stack completa dell'arresto anomalo.
- Il codice C# relativo all'arresto anomalo.
