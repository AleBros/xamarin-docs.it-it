---
title: .NET Embedding
description: 'Incorporamento .NET consente il codice esistente .NET (c#, F # e altri) deve essere utilizzato da altri linguaggi di programmazione'
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 505c2902f2b8d112597b4b9b9b07282a7810db68
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="net-embedding"></a>.NET Embedding

![Anteprima](~/media/shared/preview.png)

Incorporamento .NET consente il codice esistente .NET (c#, F # e altri) deve essere utilizzato da altri linguaggi di programmazione e in vari ambienti diversi.

Ciò significa che se si dispone di una libreria .NET che si desidera utilizzare dall'app iOS esistente, è possibile farlo.   O se si desidera collegarlo con una libreria di C++ nativa, è possibile anche farlo.   O usare il codice .NET da Java.

Durante l'incorporamento .NET si basa sul [Embeddinator 4000](https://github.com/mono/Embeddinator-4000) progetto open source.

## <a name="environments-and-languages"></a>Gli ambienti e lingue

Lo strumento è entrambi comunicata l'ambiente che verrà utilizzato, nonché la lingua che si utilizzerà.   Ad esempio, la piattaforma iOS non consente di compilazione di just-in-time (JIT), in modo da incorporare .NET compilerà in modo statico il codice .NET in codice nativo che può essere usato in iOS.  Altri ambienti di consentire la compilazione JIT e in tali ambienti, si è scelto di compilazione JIT.

Supporta diversi consumer language, pertanto rilevato codice .NET come codice idiomatica nella lingua di destinazione.   Si tratta dell'elenco delle lingue supportate al momento:

- [**Objective-C** ](objective-c/index.md) : mapping di .NET a idiomatica API Objective-C
- [**Java** ](android/index.md) : mapping di .NET a idiomatica API Java
- [**C** ](get-started/c.md) : mapping tra .NET e orientati agli oggetti, ad esempio le API C

Più lingue, verrà in seguito.

## <a name="getting-started"></a>Introduzione

Per iniziare, selezionare uno dei nostri Guide per ognuna delle lingue attualmente supportate:

- [**Objective-C** ](get-started/objective-c/index.md) – copre macOS e iOS
- [**Java** ](get-started/java/index.md) – copre macOS e Android
- [**C** ](get-started/c.md) – copre linguaggio C nelle piattaforme desktop su

## <a name="related-links"></a>Collegamenti correlati

- [Embeddinator-4000 su GitHub](https://github.com/mono/Embeddinator-4000)
