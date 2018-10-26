---
title: Panoramica dell'architettura
description: Questo documento descrive l'architettura di Xamarin Workbooks, esaminare l'interazione tra l'agente interattivo e un client interattivo.
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 352e8d0191512184ffd7d82fa0dfa0bc79fa24ca
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107261"
---
# <a name="architecture-overview"></a>Panoramica dell'architettura

Xamarin Workbooks include due componenti principali che devono funzionare in combinazione tra loro: _agente_ e _Client_.

## <a name="interactive-agent"></a>Agente interattiva

Il componente agente è un piccolo assembly specifici della piattaforma che viene eseguito nel contesto di un'applicazione .NET.

Xamarin Workbooks fornisce applicazioni "vuote" predefinite per numerose piattaforme, ad esempio iOS, Android, Mac e WPF. Queste applicazioni host in modo esplicito l'agente.

Durante l'ispezione live (Xamarin Inspector), l'agente viene inserito tramite il debugger IDE in un'applicazione esistente come parte del normale sviluppo & debug del flusso di lavoro.

## <a name="interactive-client"></a>Client Interactive

Il client è una shell nativa (Cocoa in Mac, WPF in Windows) che ospita una superficie di browser web per la presentazione dell'interfaccia della cartella di lavoro o REPL. Dal punto di Vista SDK, le integrazioni di tutti i client vengono implementate in JavaScript e CSS.

Il client è responsabile (tramite Roslyn) per la compilazione di codice sorgente in assembly di piccole dimensioni e inviarle per l'esecuzione dell'agente connesso. Risultati dell'esecuzione vengono inviati al client per il rendering. Ogni cella in una cartella di lavoro genera un assembly cui fa riferimento all'assembly della cella precedente.

Poiché un agente può essere eseguito su qualsiasi tipo di piattaforma .NET e può accedere a qualsiasi elemento dell'applicazione in esecuzione, è necessario prestare attenzione alla serializzazione dei risultati in modo indipendente dalla piattaforma.
