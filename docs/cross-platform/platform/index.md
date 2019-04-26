---
title: Supporto delle lingue in Xamarin di programmazione
description: Questo documento descrive i diversi linguaggi di programmazione supportati da Xamarin. Viene descritto C#, F#, Visual Basic.NET portabile e i modelli Razor.
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 715f63a0be54ba3342bd63c1c76d89656313359a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035909"
---
# <a name="programming-language-support-in-xamarin"></a>Supporto delle lingue in Xamarin di programmazione

## <a name="c"></a>C# 

###  <a name="async-support-overviewcross-platformplatformasyncmd"></a>[Panoramica del supporto asincrono](~/cross-platform/platform/async.md)

Versione 5 di C# introdotte due nuove parole chiave per l'espressione di operazioni asincrone: async e await. Queste parole chiave consentono di scrivere codice semplice che utilizza la Task Parallel Library per l'esecuzione di operazioni a esecuzione prolungata (ad esempio l'accesso di rete) in un altro thread e accedere con facilità i risultati al completamento. Le ultime versioni di xamarin. IOS e xamarin. Android supportano async e await: questo documento vengono fornite spiegazioni e un esempio dell'uso della nuova sintassi con Xamarin.

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[Funzionalità del linguaggio C# 6](~/cross-platform/platform/csharp-six.md)

La versione più recente del C# language – versione 6 – continua ad evolvere il linguaggio in modo da avere meno boilerplate, maggiore chiarezza e coerenza delle altre. Una sintassi più chiara l'inizializzazione, la possibilità di usare `await` nelle `catch/finally` blocchi e il condizionale null `?` operatore sono particolarmente utili.

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

Creazione di App per dispositivi mobili con F# e Xamarin.

##  <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[Portabile Visual Basic.NET](~/cross-platform/platform/visual-basic/index.md)

Visual Studio supporta la creazione di librerie di classi portabili con Visual Basic.NET che può quindi essere incorporato nelle applicazioni Xamarin. Questo articolo illustra come creare una nuova libreria di classi Portabile Visual Basic e quindi usarlo in un'applicazione di esempio xamarin. IOS, xamarin. Android e Windows Phone.

##  <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[Visualizzazioni HTML predefiniti con i modelli Razor](~/cross-platform/platform/razor-html-templates/index.md)

Xamarin consente agli sviluppatori di sfruttare il motore di creazione modello Razor, funzionalità originariamente introdotto con ASP.NET MVC, insieme a C# per combinare facilmente i dati con HTML, Javascript e CSS senza il fastidioso uso di costruzione manuale di stringhe HTML nel codice.
Questo articolo illustra come usare i modelli Razor con Xamarin per Android e iOS.
