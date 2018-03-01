---
title: Associazione Objective-C
ms.topic: article
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: e836081d79d904e2d0952386e536eefdabe361e1
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="binding-objective-c"></a>Associazione Objective-C

Questa sezione include un'ampia gamma di documenti che coprono la creazione di associazioni a librerie Objective-C, pertanto possono essere chiamati da c# applicazioni create con xamarin. IOS o Xamarin.Mac.

##  <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[Panoramica](~/cross-platform/macios/binding/overview.md)

Questo documento contiene alcuni dei componenti interni di come avviene un'associazione. È un documento con alcune informazioni tecniche avanzato.

##  <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[Associazione di raccolte Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)

Questo documento descrive il processo utilizzato per creare associazioni c# delle API di Objective-C e la modalità di mapping idiomi in Objective-C per gli idiomi usati in .NET.
Se si desidera associare solo le API di C, è necessario utilizzare il meccanismo standard di .NET a tale scopo, il framework di P/Invoke.

##  <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[Guida di riferimento di definizione di associazione](~/cross-platform/macios/binding/binding-types-reference.md)

Si tratta della Guida di riferimento che descrive tutti gli attributi disponibili per gli autori delle associazioni per gestire il processo di generazione di associazione.


## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Obiettivo Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Obiettivo Sharpie è uno strumento da riga di comando per avviare il primo passaggio di un'associazione. Analizzando i file di intestazione di una libreria nativa per eseguire il mapping dell'API pubblica in cui funziona la [definizione di associazioni](~/cross-platform/macios/binding/objective-c-libraries.md) (un processo che può essere eseguito manualmente).

## <a name="ios"></a>iOS

Il [pagina associazione iOS](~/ios/platform/binding-objective-c/index.md) consente di tornare a queste risorse comuni di associazione, inoltre per gli esempi seguenti.

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[Procedura dettagliata: Associazione di una libreria Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)

Questo articolo fornisce una procedura dettagliata della creazione di un progetto di associazione utilizzando la open source [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) progetto Objective-C come esempio. La libreria InfColorPicker fornisce un controller di visualizzazione riutilizzabili che consentono all'utente di selezionare un colore di base nella relativa rappresentazione HSB, rendendo più semplice la selezione dei colori. Obiettivo Sharpie verrà utilizzato per facilitare il processo di associazione.

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[Esempi di associazione](https://github.com/mono/monotouch-bindings)

Una raccolta di associazioni di terze parti che può essere utilizzato un riferimento durante la creazione di nuovi progetti di associazione.

## <a name="mac"></a>Mac

In passato [associazione Mac](~/mac/platform/binding.md) è stato un processo molto manuale. Al momento è presente un [anteprima scaricabile](https://forums.xamarin.com/discussion/59760/xamarin-mac-binding-project-preview) del supporto di progetto Binding Mac per una versione futura di Visual Studio per Mac.



## <a name="related-links"></a>Collegamenti correlati

- [iOS Binding](~/ios/platform/binding-objective-c/index.md)
- [Associazione Mac](~/mac/platform/binding.md)
