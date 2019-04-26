---
title: Binding di Objective-C
description: Questo documento vengono forniti collegamenti alle varie guide che descrivono come creare C# binding a codice Objective-C, consentendo agli sviluppatori di utilizzare librerie disponibili sul mercato in applicazioni Xamarin.
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: 3f1e1ce324e849c0c939d936eb6ee1470cf24a3b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61266608"
---
# <a name="binding-objective-c"></a>Binding di Objective-C

In questa sezione include una vasta gamma di documenti che illustrano la creazione di associazioni alle librerie Objective-C, in modo che possono essere chiamati da C# le applicazioni create con xamarin. IOS o xamarin. Mac.

##  <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[Panoramica](~/cross-platform/macios/binding/overview.md)

Questo documento è riportati alcuni dei componenti interni del modo in cui un'associazione viene eseguita. È un documento con alcune informazioni tecniche avanzato.

##  <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[Binding di librerie Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)

Questo documento descrive il processo usato per creare C# binding di Objective-C API e il mapping tra i linguaggi in Objective-C e gli idiomi usati in .NET.
Se si esegue il binding solo le API C, si deve usare il meccanismo di .NET standard per questa operazione, il framework di P/Invoke.

##  <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[Guida di riferimento di definizione di associazione](~/cross-platform/macios/binding/binding-types-reference.md)

Si tratta della Guida di riferimento che descrive tutti gli attributi disponibili per gli autori delle associazioni per guidare il processo di generazione dell'associazione.


## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Obiettivo Sharpie è uno strumento da riga di comando per eseguire il bootstrap del primo passaggio di un'associazione. Funziona analizzando i file di intestazione di una libreria nativa per eseguire il mapping dell'API pubblica nel [definizione di associazione](~/cross-platform/macios/binding/objective-c-libraries.md) (un processo che può essere eseguito anche manualmente).

## <a name="ios"></a>iOS

Il [pagina binding iOS](~/ios/platform/binding-objective-c/index.md) tornare a queste risorse comuni di associazione, anche per gli esempi seguenti.

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[Procedura dettagliata: Associazione di una libreria Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)

Questo articolo fornisce una procedura dettagliata di creazione di un progetto di associazione tramite open source [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) progetto Objective-C come esempio. La libreria InfColorPicker fornisce un controller di visualizzazione riutilizzabili che consentono all'utente di selezionare un colore di base nella relativa rappresentazione HSB, rendendo più semplice la selezione del colore. Obiettivo Sharpie verrà utilizzato per semplificare il processo di associazione.

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[Esempi di associazioni](https://github.com/mono/monotouch-bindings)

Una raccolta di associazioni di terze parti che può essere utilizzato un riferimento durante la creazione di nuovi progetti di Binding.

## <a name="mac"></a>Mac

In passato [associazione Mac](~/mac/platform/binding.md) è stato un processo molto arduo. Attualmente non è disponibile un' [preview scaricabile](https://forums.xamarin.com/discussion/59760/xamarin-mac-binding-project-preview) del supporto per il progetto di Binding Mac per una versione futura di Visual Studio per Mac.



## <a name="related-links"></a>Collegamenti correlati

- [iOS associazione](~/ios/platform/binding-objective-c/index.md)
- [Associazione di Mac](~/mac/platform/binding.md)
- [Corsi di Xamarin University: Compila una libreria di binding Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Corsi di Xamarin University: Creazione di una libreria di binding Objective-C con Sharpie obiettivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
