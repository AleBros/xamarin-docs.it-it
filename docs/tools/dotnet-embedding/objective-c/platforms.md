---
title: Piattaforme Objective-C
description: In questo documento vengono descritte le diverse piattaforme che possono essere destinate all'incorporamento di .NET quando si utilizza il codice Objective-C. Vengono illustrati macOS, iOS, tvOS e watchos.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: dbc2c40e06f14ec636b4aa4cc11fc4f2d230ee6d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73006417"
---
# <a name="objective-c-platforms"></a>Piattaforme Objective-C

L'incorporamento .NET può essere destinato a diverse piattaforme durante la generazione del codice Objective-C:

* macOS
* iOS
* tvOS
* watchos [non ancora implementato]

La piattaforma viene selezionata passando il `--platform=<platform>` argomento della riga di comando all'incorporamento di .NET.

Quando si compila per le piattaforme iOS, tvOS e watchos, l'incorporamento di .NET creerà sempre un Framework che incorpora Xamarin.iOS, dal momento che Xamarin.iOS contiene molto codice di supporto in fase di esecuzione, necessario su queste piattaforme.

Tuttavia, quando si compila per la piattaforma macOS, è possibile scegliere se il Framework generato deve incorporare Xamarin.Mac o meno. È possibile non incorporare Xamarin.Mac se l'assembly associato non fa riferimento a Xamarin.Mac. dll (direttamente o indirettamente) ed è selezionato passando `--platform=macOS` allo strumento di incorporamento .NET.

Se l'assembly associato contiene un riferimento a Xamarin.Mac. dll, è necessario incorporare Xamarin.Mac. Inoltre, embeddinator deve essere in grado di individuare il Framework di destinazione da utilizzare.

Sono disponibili tre Framework di destinazione Xamarin.Mac: `modern` (in precedenza denominati `mobile`), `full` e `system` (la differenza tra ogni è descritta nella documentazione del [Framework di destinazione][1] di Xamarin.Mac) e ognuna viene selezionata passando `--platform=macOS-modern`, `--platform=macOS-full` o `--platform=macOS-system` allo strumento di incorporamento .NET.

[1]: ~/mac/platform/target-framework.md
