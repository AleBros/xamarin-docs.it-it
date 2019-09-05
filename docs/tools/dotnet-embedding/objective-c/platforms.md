---
title: Piattaforme Objective-C
description: In questo documento vengono descritte le diverse piattaforme che possono essere destinate all'incorporamento di .NET quando si utilizza il codice Objective-C. Vengono illustrati macOS, iOS, tvOS e watchos.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: f97b595f129cb1ad1ea56e3ae43b0f0a477fef5a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282735"
---
# <a name="objective-c-platforms"></a>Piattaforme Objective-C

L'incorporamento .NET può essere destinato a diverse piattaforme durante la generazione del codice Objective-C:

* macOS
* iOS
* tvOS
* watchos [non ancora implementato]

La piattaforma viene selezionata passando l'argomento `--platform=<platform>` della riga di comando all'incorporamento di .NET.

Quando si compila per le piattaforme iOS, tvOS e watchos, l'incorporamento di .NET creerà sempre un Framework che incorpora Novell. iOS, dal momento che Novell. iOS contiene molto codice di supporto in fase di esecuzione, necessario su queste piattaforme.

Tuttavia, quando si compila per la piattaforma macOS, è possibile scegliere se il Framework generato deve incorporare Novell. Mac o meno. È possibile non incorporare Novell. Mac se l'assembly associato non fa riferimento a Novell. Mac. dll (direttamente o indirettamente) ed è selezionato passando `--platform=macOS` allo strumento di incorporamento .NET.

Se l'assembly associato contiene un riferimento a Novell. Mac. dll, è necessario incorporare Novell. Mac. Inoltre, embeddinator deve essere in grado di individuare il Framework di destinazione da utilizzare.

Esistono tre possibili Framework di destinazione Novell `modern` . Mac: (chiamato `mobile`in precedenza) `full` e `system` (la differenza tra ogni è descritta nella documentazione del Framework di [destinazione][1] di Novell. Mac) e ogni viene selezionato passando `--platform=macOS-modern` `--platform=macOS-full` o `--platform=macOS-system` allo strumento di incorporamento .NET.

[1]: ~/mac/platform/target-framework.md
