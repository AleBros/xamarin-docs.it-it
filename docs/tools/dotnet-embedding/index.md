---
title: .NET Embedding
description: 'Incorporamento di .NET consente al codice .NET esistente (c#, F # e altri utenti) devono essere usati dal codice scritto in altri linguaggi di programmazione.'
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 23233ea8b06e0db580ba99edf2705e3dae5b931f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107144"
---
# <a name="net-embedding"></a>.NET Embedding

![Anteprima](~/media/shared/preview.png)

Incorporamento di .NET consente al codice .NET esistente (c#, F # e altri utenti) devono essere usati da altri linguaggi di programmazione e in vari ambienti diversi.

Ciò significa che se si dispone di una libreria .NET che si desidera utilizzare dalla tua app iOS esistente, è possibile farlo.   O se si desidera collegare una libreria nativa C++, è possibile anche farlo.   O utilizzare il codice .NET da Java.

Incorporamento di .NET si basa sul [Embeddinator 4000](https://github.com/mono/Embeddinator-4000) progetto open source.

## <a name="environments-and-languages"></a>Linguaggi e ambienti

Lo strumento è entrambi conoscere l'ambiente che utilizzerà, nonché il linguaggio che lo utilizzano.   Ad esempio, la piattaforma iOS non supporta compilazione just-in-time (JIT), in modo che l'incorporamento .NET compilerà in modo statico il codice .NET nel codice nativo che può essere usato in iOS.  Altri ambienti si consentono la compilazione JIT e in tali ambienti, si è scelto a compilazione JIT.

Supporta diversi consumer del linguaggio, pertanto Evidenzia codice .NET idiomatico codice nella lingua di destinazione.   Al momento si tratta dell'elenco delle lingue supportate:

- [**Objective-C** ](objective-c/index.md) – mapping .NET idiomatico API Objective-C
- [**Java** ](android/index.md) – mapping .NET idiomatico API Java
- [**C** ](get-started/c.md) : mapping di .NET a orientate a oggetti, ad esempio le API C

Altre lingue saranno disponibile in un secondo momento.

## <a name="getting-started"></a>Introduzione

Per iniziare, esaminare uno dei nostri Guide per ognuna delle lingue attualmente supportate:

- [**Objective-C** ](get-started/objective-c/index.md) – descritto come iOS e macOS
- [**Java** ](get-started/java/index.md) – copre macOS e Android
- [**C** ](get-started/c.md) – descrive il linguaggio C nelle piattaforme desktop

## <a name="related-links"></a>Collegamenti correlati

- [Embeddinator-4000 su GitHub](https://github.com/mono/Embeddinator-4000)
