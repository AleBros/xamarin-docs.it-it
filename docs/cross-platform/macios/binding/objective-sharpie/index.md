---
title: Creazione di associazioni con Sharpie obiettivo
description: In questa sezione viene fornita un'introduzione a Sharpie obiettivo, lo strumento della riga di comando di Xamarin consente di automatizzare il processo di creazione di un'associazione a una libreria Objective-C
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 53fcbbc408ae147405a3285d9391457051d6e16e
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37854795"
---
# <a name="creating-bindings-with-objective-sharpie"></a>Creazione di associazioni con Sharpie obiettivo

_In questa sezione viene fornita un'introduzione a Sharpie obiettivo, lo strumento della riga di comando di Xamarin consente di automatizzare il processo di creazione di un'associazione a una libreria Objective-C_

- [Cenni preliminari sulla](#overview) & [cronologia](#history)
- [Introduzione](get-started.md)
- [Strumenti e comandi](tools.md)
- [Funzionalità](platform/index.md)
- [Esempi](examples/index.md)
- [Procedura dettagliata completa](~/ios/platform/binding-objective-c/walkthrough.md)
- [Cronologia delle versioni](releases.md)

## <a name="overview"></a>Panoramica

Obiettivo Sharpie è uno strumento da riga di comando per eseguire il bootstrap del primo passaggio di un'associazione.
Funziona analizzando i file di intestazione di una libreria nativa per eseguire il mapping dell'API pubblica nel [definizione di associazione](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) (un processo che in precedenza è stato apportato manualmente).

Obiettivo Sharpie Usa file di intestazione Clang analisi, in modo che l'associazione sia come esatta e più completa possibile. Ciò può ridurre notevolmente il tempo e impegno che necessario per produrre un'associazione di qualità.

> [!IMPORTANT]
> Obiettivo Sharpie è uno strumento per gli sviluppatori esperti di Xamarin con conoscenza approfondita di Objective-C (e di conseguenza, C). Prima di provare a eseguire l'associazione di una libreria Objective-C è solido delle conoscenze necessarie per compilare la libreria nativa nella riga di comando (e una buona conoscenza del funzionamento della libreria nativa).

## <a name="history"></a>Cronologia

Abbiamo stato in continua evoluzione e usando il Sharpie obiettivo internamente presso Xamarin negli ultimi tre anni. Come testimonia la potenza di Sharpie obiettivo, API introdotte in xamarin. IOS e xamarin. Mac dalla versione iOS 8, Mac OS X 10.10, e watchOS 2.0 sono state avviato automaticamente interamente con Sharpie obiettivo. Xamarin è basato soprattutto sulle obiettivo Sharpie internamente per creare i propri prodotti.

Tuttavia, Sharpie obiettivo è uno strumento molto avanzato che richiede una conoscenza avanzata di Objective-C e C, come usare il compiler clang nella riga di comando e le librerie native in genere come sono fatte. A causa di questa barra elevata, abbiamo ritenuto che un'interfaccia utente grafica di un procedura guidata imposta le aspettative errate e di conseguenza, Sharpie obiettivo è attualmente disponibile solo come uno strumento da riga di comando.

## <a name="related-links"></a>Collegamenti correlati

- [Download Sharpie obiettivo](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
- [Procedura dettagliata: Associazione di una libreria Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)
- [Binding di librerie Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Informazioni dettagliate sull'associazione](~/cross-platform/macios/binding/overview.md)
- [Guida di riferimento per i tipi di associazione](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin per sviluppatori Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Corso di Xamarin University: Creazione di una libreria di binding di Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Corsi di Xamarin University: Compilare una libreria di binding Objective-C con Sharpie obiettivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
