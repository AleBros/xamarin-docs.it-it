---
title: Creazione di associazioni con Sharpie obiettivo
description: In questa sezione viene fornita un'introduzione a Sharpie obiettivo, lo strumento della riga di comando di Xamarin consentono di automatizzare il processo di creazione di un'associazione a una raccolta di Objective-C
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: ae644038aa8b54f0d57b61767882dec8754040c8
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780757"
---
# <a name="creating-bindings-with-objective-sharpie"></a>Creazione di associazioni con Sharpie obiettivo

_In questa sezione viene fornita un'introduzione a Sharpie obiettivo, lo strumento della riga di comando di Xamarin consentono di automatizzare il processo di creazione di un'associazione a una raccolta di Objective-C_

- [Panoramica](#overview) & [cronologia](#history)
- [Introduzione](get-started.md)
- [Strumenti e comandi](tools.md)
- [Funzionalità](platform/index.md)
- [Esempi](examples/index.md)
- [Procedura dettagliata completa](~/ios/platform/binding-objective-c/walkthrough.md)
- [Cronologia delle versioni](releases.md)

## <a name="overview"></a>Panoramica

Obiettivo Sharpie è uno strumento da riga di comando per avviare il primo passaggio di un'associazione.
Analizzando i file di intestazione di una libreria nativa per eseguire il mapping dell'API pubblica in cui funziona la [definizione di associazioni](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) (un processo che in precedenza è stato apportato manualmente).

Obiettivo Sharpie Usa file di intestazione di analisi Clang, quindi l'associazione è esatta e più completo possibile. Questo può ridurre notevolmente il tempo e l'impegno che necessario per produrre un'associazione di qualità.

> [!IMPORTANT]
> Obiettivo Sharpie è uno strumento per sviluppatori Xamarin esperti con conoscenze avanzate di Objective-C (e, di conseguenza, C). Prima di tentare di eseguire l'associazione di una libreria Objective-C è necessario avere conoscenze a tinta unita di come compilare la libreria nativa nella riga di comando (e una buona conoscenza del funzionamento della libreria nativa).

## <a name="history"></a>Cronologia

Abbiamo stato evoluzione e utilizza internamente il Sharpie obiettivo in Xamarin per gli ultimi tre anni. Come una testimonianza alla potenza di Sharpie obiettivo, API introdotta in xamarin. IOS e Xamarin.Mac da iOS 8, Mac OS X 10.10 e watchOS 2.0 sono stati avvio gestito interamente con Sharpie obiettivo. Xamarin si basa fortemente sulle Sharpie obiettivo internamente per creare i propri prodotti.

Tuttavia, Sharpie obiettivo è uno strumento molto avanzato che richiede una conoscenza avanzata di Objective-C e C, come utilizzare il compilatore clang nella riga di comando e librerie in genere come native vengono inserite insieme. A causa di questa barra elevata, abbiamo ritenuto che un'interfaccia utente grafica di un procedura guidata imposta le aspettative errate e di conseguenza, Sharpie obiettivo è attualmente disponibile solo come uno strumento da riga di comando.

## <a name="related-links"></a>Collegamenti correlati

- [Download Sharpie obiettivo](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
- [Procedura dettagliata: Associazione di una libreria Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)
- [Binding di librerie Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Dettagli di associazione](~/cross-platform/macios/binding/overview.md)
- [Guida di riferimento per i tipi di associazione](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin per sviluppatori Objective-C](~/ios/get-started/objective-c-developers/index.md)
