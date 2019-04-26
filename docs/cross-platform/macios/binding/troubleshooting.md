---
title: Risoluzione dei problemi di associazione
description: Questa guida descrive cosa fare se si riscontrano difficoltà nell'associazione di una libreria Objective-C. In particolare, vengono illustrate le associazioni mancante, eccezioni di argomento quando si passa null per un'associazione e la segnalazione dei bug.
ms.prod: xamarin
ms.assetid: 7C65A55C-71FA-46C5-A1B4-955B82559844
author: asb3993
ms.author: amburns
ms.date: 10/19/2016
ms.openlocfilehash: fcdd712313becd1335479013f44886086dde7bff
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261243"
---
# <a name="binding-troubleshooting"></a>Risoluzione dei problemi di associazione

Alcuni suggerimenti per la risoluzione dei problemi di associazioni per macOS (precedentemente noto come OS X) le API in xamarin. Mac.

## <a name="missing-bindings"></a>Mancano le associazioni

Sebbene xamarin. Mac copre molte delle API di Apple, in alcuni casi potrebbe essere necessario chiamare API Apple che non dispone di un'associazione ancora. In altri casi, è necessario chiamare terze parti, C/Objective-C che non rientrano nell'ambito dei binding xamarin. Mac.

Se è necessario gestire un'API di Apple, il primo passaggio è indicare Xamarin che si raggiungono una sezione dell'API che non abbiamo code coverage per ancora. [Segnalare un bug](#reporting-bugs) annotando l'API manca. Usiamo i report da parte dei clienti per definire le priorità delle API di Microsoft si impegna facendo clic su Avanti. Inoltre, se si ha una licenza Business o Enterprise e l'assenza di un binding sta bloccando lo stato di avanzamento, anche seguire le istruzioni riportate in [supporto](http://xamarin.com/support) per inviare un ticket. Non possiamo promettere di un'associazione, ma in alcuni casi è possibile aggirare è un lavoro.

Una volta notificare a Xamarin (se applicabile) dell'associazione mancante, il passaggio successivo è da considerare associarla manualmente. È disponibile una guida completa [in questo caso](~/cross-platform/macios/binding/overview.md) e alcuni documenti non ufficiali [qui](http://brendanzagaeski.appspot.com/xamarin/0002.html) per eseguire il wrapping binding Objective-C manualmente. Se si chiama un'API C, è possibile usare C#del meccanismo P/Invoke, la documentazione è [qui](https://www.mono-project.com/docs/advanced/pinvoke/).

Se si decide di usare nell'associazione manualmente, tenere presente che gli errori di associazione possono produrre moltissime interessanti arresti anomali del sistema di runtime nativo. In particolare, prestare molta attenzione che la firma in C# corrisponde alla firma native nel numero di argomenti e le dimensioni di ogni argomento. In caso contrario, potrebbe essere danneggiata memoria e/o lo stack e si potrebbe arrestarsi in modo anomalo immediatamente o in un punto arbitrario nel futuro o danneggiare i dati.

## <a name="argument-exceptions-when-passing-null-to-a-binding"></a>Eccezioni di argomento quando si passa null per un'associazione

Sebbene Xamarin funzioni per fornire elevata qualità e ben collaudate associazioni per le API di Apple, talvolta errori e bug di trasporto. Problema di gran lunga il più comune che si potrebbe riscontrare è un'API generando un'eccezione `ArgumentNullException` quando si passa null quando l'API sottostante accetta `nil`. I file di intestazione native che definisce l'API spesso non forniscono informazioni sufficienti in cui le API accettano nil e che si arresteranno se lo si passa in.

Se si esegue in un caso in cui passando `null` genera un `ArgumentNullException` ma si ritiene che deve lavorare, seguire questa procedura:

1. Controllare la documentazione di Apple e/o esempi per vedere se è possibile trovare la prova che accetta `nil`. Se si ha familiarità con Objective-C, è possibile scrivere un programma di test di piccole dimensioni per la verifica.
2. [Segnalare un bug](#reporting-bugs).
3. Può risolvere i bug? Se non è possibile evitare la chiamata all'API con `null`, un semplice controllo null intorno alle chiamate può essere una semplice soluzione alternativa.
4. Tuttavia, alcune API richiedono il passaggio di null per disattivare o disabilitare alcune funzionalità. In questi casi, è possibile risolvere il problema, grazie alla disponibilità di browser di assembly (vedere [ricerca di C# membro per un selettore specificato](~/mac/app-fundamentals/mac-apis.md#finding_selector)), copia dell'associazione e rimuovendo il controllo dei valori null. Assicurarsi di archiviare un bug (passaggio 2) se questa operazione, come l'associazione copiato non riceverà gli aggiornamenti e correzioni che si prendono in xamarin. Mac e l'attività deve essere considerata per risolvere il problema a breve termine.

<a name="reporting-bugs"/>

## <a name="reporting-bugs"></a>Segnalazione dei bug

I tuoi commenti sono importanti per noi. Se individuare i problemi con xamarin. Mac:

- Controllare i [forum di Xamarin.Mac](https://forums.xamarin.com/categories/mac)
- Cercare nel [repository di problemi](https://github.com/xamarin/xamarin-macios/issues) 
- Prima di passare ai problemi di GitHub, si è tenuta traccia dei problemi di Xamarin in [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi), dove è possibile cercare i problemi corrispondenti.
- Se non si riesce a trovare un problema corrispondente, inserirne uno nuovo nel [repository di problemi GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

I problemi di GitHub sono tutti pubblici. Non è possibile nascondere commenti o allegati. 

Includere tutte le informazioni disponibili seguenti:

- Un esempio semplice che riproduce il problema. Questo è **molto importante**, ove possibile. 
- L'analisi dello stack completa dell'arresto anomalo.
- Il codice C# relativo all'arresto anomalo. 

## <a name="related-links"></a>Collegamenti correlati

- [Corsi di Xamarin University: Compila una libreria di binding Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Corsi di Xamarin University: Creazione di una libreria di binding Objective-C con Sharpie obiettivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
