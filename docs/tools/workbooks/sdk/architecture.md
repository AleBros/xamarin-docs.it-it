---
title: Panoramica dell'architettura
description: In questo documento viene descritta l'architettura di Xamarin Workbooks, esaminando il modo in cui interagiscono tra l'agente interattivo e il client interattivo.
ms.prod: xamarin
ms.assetid: 6C0226BE-A0C4-4108-B482-0A903696AB04
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: 7129d0bedddb272ef87e3d209cb05c2ca0c0acf4
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285286"
---
# <a name="architecture-overview"></a>Panoramica dell'architettura

Xamarin Workbooks include due componenti principali che devono funzionare insieme tra loro: _Agent_ e _client_.

## <a name="interactive-agent"></a>Agente interattivo

Il componente Agent è un piccolo assembly specifico della piattaforma, che viene eseguito nel contesto di un'applicazione .NET.

Xamarin Workbooks offre applicazioni "vuote" predefinite per diverse piattaforme, ad esempio iOS, Android, Mac e WPF. Queste applicazioni ospitano in modo esplicito l'agente.

Durante l'ispezione in tempo reale (Xamarin Inspector), l'agente viene inserito tramite il debugger IDE in un'applicazione esistente come parte del normale flusso di lavoro di debug & di sviluppo.

## <a name="interactive-client"></a>Client interattivo

Il client è una Shell nativa (Cocoa in Mac, WPF in Windows) che ospita una superficie del browser Web per la presentazione dell'interfaccia cartella di lavoro/REPL. Dal punto di vista dell'SDK, tutte le integrazioni client sono implementate in JavaScript e CSS.

Il client è responsabile (tramite Roslyn) per la compilazione di codice sorgente in piccoli assembly e per inviarli all'agente connesso per l'esecuzione. I risultati dell'esecuzione vengono restituiti al client per il rendering. Ogni cella di una cartella di lavoro produce un assembly che fa riferimento all'assembly della cella precedente.

Poiché un agente può essere eseguito in qualsiasi tipo di piattaforma .NET e può accedere a qualsiasi elemento nell'applicazione in esecuzione, è necessario prestare attenzione a serializzare i risultati in modo indipendente dalla piattaforma.
