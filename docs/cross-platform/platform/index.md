---
title: Supporto del linguaggio di programmazione in Novell
description: Questo documento descrive i vari linguaggi di programmazione supportati da Novell. Vengono C#illustrati F#i modelli,, Portable Visual Basic.NET e Razor.
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
author: conceptdev
ms.author: crdun
ms.date: 02/18/2018
ms.openlocfilehash: 95c63d446f961738ad71242671d632620316b879
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290894"
---
# <a name="programming-language-support-in-xamarin"></a>Supporto del linguaggio di programmazione in Novell

## <a name="c"></a>C\#

### <a name="async-support-overviewcross-platformplatformasyncmd"></a>[Panoramica del supporto asincrono](~/cross-platform/platform/async.md)

Nella versione 5 C# di sono state introdotte due nuove parole chiave per esprimere le operazioni asincrone: async e await. Queste parole chiave consentono di scrivere codice semplice che usa il Task Parallel Library per eseguire operazioni a esecuzione prolungata, ad esempio l'accesso alla rete, in un altro thread e accedere facilmente ai risultati al termine del completamento. Le versioni più recenti di Novell. iOS e Novell. Android supportano Async e await. questo documento fornisce spiegazioni e un esempio di utilizzo della nuova sintassi con Novell.

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[Funzionalità del linguaggio C# 6](~/cross-platform/platform/csharp-six.md)

La versione più recente del C# linguaggio-versione 6-continua a evolvere il linguaggio in modo da avere meno standard, una maggiore chiarezza e una maggiore coerenza. La sintassi di inizializzazione più pulita, `await` la `catch/finally` possibilità di usare in blocchi e l' `?` operatore condizionale null sono particolarmente utili.

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

Creazione di app per F# dispositivi mobili con e Novell.

## <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[Basic.NET visivi portatili](~/cross-platform/platform/visual-basic/index.md)

Visual Studio supporta la creazione di librerie di classi portabili usando Visual Basic.NET che può quindi essere incorporato in applicazioni Novell. Questo articolo illustra come creare un nuovo Visual Basic PCL e quindi usarlo in un'applicazione di esempio Novell. iOS, Novell. Android e Windows Phone.

## <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[Creazione di visualizzazioni HTML con i modelli Razor](~/cross-platform/platform/razor-html-templates/index.md)

Novell consente agli sviluppatori di sfruttare il motore di creazione di modelli Razor, originariamente introdotto con ASP.NET MVC C# , insieme a per combinare facilmente i dati con HTML, JavaScript e CSS senza dover compilare manualmente stringhe HTML nel codice.
Questo articolo illustra come usare i modelli Razor con Novell per Android e iOS.
