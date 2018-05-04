---
title: Piattaforme Objective-C
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 22652baa941debf7ded2d43cfda8c95ec474816f
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
# <a name="objective-c-platforms"></a>Piattaforme Objective-C

Diverse piattaforme .NET incorporamento può di destinazione durante la generazione di codice Objective-C:

* macOS
* iOS
* tvOS
* watchOS [non ancora implementata]

La piattaforma è selezionata, passando il `--platform=<platform>` argomento della riga di comando per l'incorporamento .NET.

Quando si compila per iOS, tvOS e watchOS piattaforme, .NET incorporamento creerà sempre un framework che incorpora xamarin. IOS, poiché xamarin. IOS contiene una grande quantità di codice di supporto runtime che è necessaria su queste piattaforme.

Tuttavia, quando si compila per la piattaforma macOS, è possibile scegliere se il framework generato deve incorporare Xamarin.Mac o non. È possibile incorporare Xamarin.Mac se l'assembly associato non fa riferimento Xamarin.Mac.dll (direttamente o indirettamente), e questa opzione è selezionata, passando `--platform=macOS` allo strumento di incorporamento di .NET.

Se l'assembly associato contiene un riferimento a Xamarin.Mac.dll, è necessario incorporare Xamarin.Mac e inoltre la embeddinator deve conoscere quali framework di destinazione da utilizzare.

Esistono tre possibili Framework di destinazione Xamarin.Mac: `modern` (in precedenza denominate `mobile`), `full` e `system` (la differenza tra ciascuno di essi è descritto in Xamarin.Mac [framework di destinazione] [ 1] documentazione), e ognuno sia selezionata passando `--platform=macOS-modern`, `--platform=macOS-full` o `--platform=macOS-system` allo strumento di incorporamento di .NET.

[1]: ~/mac/platform/target-framework.md
