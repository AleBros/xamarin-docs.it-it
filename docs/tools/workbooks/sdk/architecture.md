---
title: Panoramica dell'architettura
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 04ed545955b10ed95a15ac0820e4a99b697e0c3e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="architecture-overview"></a>Panoramica dell'architettura

Le cartelle di lavoro di Xamarin offre due componenti principali che devono operare in combinazione tra loro: _agente_ e _Client_.

## <a name="interactive-agent"></a>Agente interattiva

Il componente agente è un piccolo assembly specifico della piattaforma che viene eseguito nel contesto di un'applicazione .NET.

Le cartelle di lavoro di Xamarin fornisce applicazioni "empty" per il numero di piattaforme, ad esempio iOS, Android, Mac e WPF. Queste applicazioni host in modo esplicito l'agente.

Durante l'analisi in tempo reale (controllo Xamarin), l'agente viene inserito tramite il debugger IDE in un'applicazione esistente come parte dello sviluppo regolare & debug del flusso di lavoro.

## <a name="interactive-client"></a>Client interattivo

Il client è una shell nativa (Cocoa su Mac, WPF in Windows) che ospita una superficie di web browser per la presentazione dell'interfaccia di cartella di lavoro/REPL. Dal punto di Vista SDK, le integrazioni di tutti i client vengono implementate in JavaScript e CSS.

Il client è responsabile (tramite Roslyn) per la compilazione di codice sorgente in assembly di piccole dimensioni e inviarli per l'esecuzione dell'agente connesso. Risultati di esecuzione vengono inviati al client per il rendering. Ogni cella in una cartella di lavoro genera un assembly cui fa riferimento all'assembly della cella precedente.

Poiché un agente può essere eseguito su qualsiasi tipo di piattaforma .NET e ha accesso a qualsiasi elemento nell'applicazione in esecuzione, è necessario serializzare i risultati in modo indipendente dalla piattaforma.