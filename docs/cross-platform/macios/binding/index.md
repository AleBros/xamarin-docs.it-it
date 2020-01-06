---
title: Binding di Objective-C
description: In questo documento vengono forniti i collegamenti a diverse guide che descrivono come creare C# binding per il codice Objective-C, consentendo agli sviluppatori di utilizzare librerie fuori programma nelle applicazioni Novell.
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
author: davidortinau
ms.author: daortin
ms.date: 01/25/2016
ms.openlocfilehash: b7764d63991ec636043982509319e7097ef2091b
ms.sourcegitcommit: d8af612b6b3218fea396d2f180e92071c4d4bf92
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663253"
---
# <a name="binding-objective-c"></a>Binding di Objective-C

Questa sezione include una serie di documenti che coprono la creazione di binding per le librerie Objective-C, in modo che possano C# essere chiamati da applicazioni create con Novell. iOS o Novell. Mac.

## <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[Panoramica](~/cross-platform/macios/binding/overview.md)

Questo documento contiene alcuni elementi interni della modalità di associazione di un'associazione. Si tratta di un documento avanzato con alcune informazioni tecniche.

## <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[Binding di librerie Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)

In questo documento viene descritto il processo utilizzato C# per creare associazioni di API Objective-c e il modo in cui viene eseguito il mapping degli idiomi in Objective-c agli idiomi utilizzati in .NET.
Se si stanno associando solo le API C, è necessario usare il meccanismo .NET standard per questo, il Framework P/Invoke.

## <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[Guida di riferimento per la definizione dell'associazione](~/cross-platform/macios/binding/binding-types-reference.md)

Questa è la Guida di riferimento che descrive tutti gli attributi disponibili per l'associazione degli autori al processo di generazione del binding.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Objective Sharpie è uno strumento da riga di comando che consente di avviare il primo passaggio di un'associazione. Funziona analizzando i file di intestazione di una libreria nativa per eseguire il mapping dell'API pubblica nella [definizione di binding](~/cross-platform/macios/binding/objective-c-libraries.md) (un processo che può essere eseguito anche manualmente).

## <a name="ios"></a>iOS

La [pagina Binding iOS](~/ios/platform/binding-objective-c/index.md) collega a queste risorse di associazione comuni, oltre agli esempi seguenti.

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[Procedura dettagliata: associazione di una libreria Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)

Questo articolo fornisce una procedura dettagliata per la creazione di un progetto di associazione usando il progetto open source [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective-C come esempio. La libreria InfColorPicker offre un controller di visualizzazione riutilizzabile che consente all'utente di selezionare un colore in base alla rappresentazione HSB, rendendo la selezione di colore più intuitiva. L'obiettivo Sharpie verrà usato per semplificare il processo di associazione.

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[Esempi di associazione](https://github.com/mono/monotouch-bindings)

Raccolta di associazioni di terze parti che possono essere utilizzate come riferimento durante la creazione di nuovi progetti di associazione.

## <a name="mac"></a>Mac

Seguire le istruzioni di [Binding Mac](~/mac/platform/binding.md) per associare le librerie MacOS. È possibile creare una nuova **libreria di binding Mac** dalla finestra **nuovo progetto** :

[finestra di dialogo nuovo progetto binding Mac file ![](images/new-bindings-library-sml.png)](images/new-bindings-library.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [Binding iOS](~/ios/platform/binding-objective-c/index.md)
- [Binding Mac](~/mac/platform/binding.md)
