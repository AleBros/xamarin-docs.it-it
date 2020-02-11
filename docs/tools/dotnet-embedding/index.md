---
title: .NET Embedding
description: L'incorporamento .NET consente di utilizzare il codiceC#.NET F#esistente (, e altri) dal codice scritto in altri linguaggi di programmazione.
ms.prod: xamarin
ms.assetid: 617C38CA-B921-4A76-8DFC-B0A3DF90E48A
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 5a0e7eeaee9b3189de63d0b82a3822cc68023505
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73006938"
---
# <a name="net-embedding"></a>.NET Embedding

![Anteprima](~/media/shared/preview.png)

L'incorporamento .NET consente di utilizzare il codiceC#.NET F#esistente (, e altri) da altri linguaggi di programmazione e in diversi ambienti.

Ciò significa che se si dispone di una libreria .NET che si vuole usare dall'app iOS esistente, è possibile farlo.   In alternativa, se si desidera collegarlo a una C++ libreria nativa, è anche possibile eseguire questa operazione.   Oppure utilizzare il codice .NET da Java.

L'incorporamento .NET è basato sul progetto open source [Embeddinator-4000](https://github.com/mono/Embeddinator-4000) .

## <a name="environments-and-languages"></a>Ambienti e linguaggi

Lo strumento è in grado di riconoscere l'ambiente che utilizzerà, nonché il linguaggio che lo utilizzerà.   La piattaforma iOS, ad esempio, non consente la compilazione JIT (just-in-Time), quindi l'incorporamento di .NET compilerà in modo statico il codice .NET in codice nativo che può essere usato in iOS.  Gli altri ambienti consentono la compilazione JIT e in tali ambienti si sceglie la compilazione JIT.

Supporta diversi consumer di lingue, quindi espone il codice .NET come codice idiomatiche nella lingua di destinazione.   Questo è l'elenco delle lingue supportate attualmente:

- [**Objective-c**](objective-c/index.md) : mapping tra .NET e le API idiomatiche Objective-c
- [**Java**](android/index.md) -mapping di .NET alle API Java idiomatiche
- [**C**](get-started/c.md) : mapping tra .NET e le API c e orientate a oggetti

Più lingue verranno riprendete in seguito.

## <a name="getting-started"></a>Introduzione

Per iniziare, vedere una delle guide per ogni lingua attualmente supportata:

- [**Objective-C**](get-started/objective-c/index.md) : illustra MacOS e iOS
- [**Java**](get-started/java/index.md) : illustra MacOS e Android
- [**C**](get-started/c.md) : copre il linguaggio c sulle piattaforme desktop

## <a name="related-links"></a>Collegamenti correlati

- [Embeddinator-4000 su GitHub](https://github.com/mono/Embeddinator-4000)
