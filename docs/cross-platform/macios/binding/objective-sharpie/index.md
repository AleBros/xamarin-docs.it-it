---
title: Creazione di associazioni con Objective Sharpie
description: Questa sezione fornisce un'introduzione a Objective Sharpie, lo strumento da riga di comando di Novell usato per automatizzare il processo di creazione di un'associazione a una libreria Objective-C.
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: conceptdev
ms.author: crdun
ms.date: 10/11/2017
ms.openlocfilehash: d5b9fa1edc09b831dbc69ab092dfb5270942e67a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765704"
---
# <a name="creating-bindings-with-objective-sharpie"></a>Creazione di associazioni con Objective Sharpie

_Questa sezione fornisce un'introduzione a Objective Sharpie, lo strumento da riga di comando di Novell usato per automatizzare il processo di creazione di un'associazione a una libreria Objective-C._

- [Panoramica](#overview) & [Cronologia](#history)
- [Introduzione](get-started.md)
- [Strumenti e comandi](tools.md)
- [Funzionalità](platform/index.md)
- [esempi](examples/index.md)
- [Procedura dettagliata completa](~/ios/platform/binding-objective-c/walkthrough.md)
- [Cronologia delle versioni](releases.md)

## <a name="overview"></a>Panoramica

Objective Sharpie è uno strumento da riga di comando che consente di avviare il primo passaggio di un'associazione.
Funziona analizzando i file di intestazione di una libreria nativa per eseguire il mapping dell'API pubblica nella [definizione di binding](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) (un processo precedentemente eseguito manualmente).

Objective Sharpie usa i file di intestazione di analisi Clang, quindi il binding è il più esatto e completo possibile. Questo può ridurre notevolmente il tempo e il lavoro richiesto per produrre un'associazione di qualità.

> [!IMPORTANT]
> Objective Sharpie è uno strumento per gli sviluppatori esperti di Novell con una conoscenza avanzata di Objective-C (e per estensione, C). Prima di tentare di associare una libreria Objective-C, è necessario avere una conoscenza approfondita di come compilare la libreria nativa dalla riga di comando (e una buona conoscenza del funzionamento della libreria nativa).

## <a name="history"></a>Cronologia

È in corso l'evoluzione e l'uso dell'obiettivo Sharpie internamente in Novell negli ultimi tre anni. Per quanto riguarda la potenza dell'obiettivo Sharpie, le API introdotte in Novell. iOS e Novell. Mac da iOS 8, Mac OS X 10,10 e watchos 2,0 sono state avviate interamente con Objective Sharpe. Novell si basa principalmente sull'obiettivo Sharpie per la creazione di propri prodotti.

Tuttavia, Objective è uno strumento molto avanzato che richiede una conoscenza avanzata di Objective-C e C, come usare il compilatore Clang nella riga di comando e in genere come vengono combinate le librerie native. A causa di questa alta barra, abbiamo pensato che la creazione guidata di GUI consentisse di impostare le aspettative errate e, di conseguenza, l'obiettivo Sharpie è attualmente disponibile solo come strumento da riga di comando.

## <a name="related-links"></a>Collegamenti correlati

- [Download dell'obiettivo Sharpie](https://aka.ms/objective-sharpie)
- [Procedura dettagliata: Associazione di una libreria Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)
- [Binding di librerie Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Dettagli associazione](~/cross-platform/macios/binding/overview.md)
- [Guida di riferimento ai tipi di binding](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin per sviluppatori Objective-C](~/ios/get-started/objective-c-developers/index.md)
