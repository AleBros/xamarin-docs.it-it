---
title: Piattaforme di Objective-C
description: Questo documento descrive le varie piattaforme .NET incorporamento potranno essere eseguite quando si lavora con codice Objective-C. Viene descritto macOS, iOS, tvOS e watchOS.
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 8091fb4e8328f61f1471d061b51b4735de3c089c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230724"
---
# <a name="objective-c-platforms"></a>Piattaforme di Objective-C

Incorporamento di .NET può destinazione varie piattaforme durante la generazione di codice Objective-C:

* macOS
* iOS
* tvOS
* [non ancora implementato] watchOS

La piattaforma è selezionata, passando il `--platform=<platform>` argomento della riga di comando all'incorporamento di .NET.

Quando si compila per iOS, tvOS e watchOS piattaforme, .NET incorporamento creerà sempre un framework che incorpora xamarin. IOS, poiché xamarin. IOS contiene una grande quantità di codice di supporto runtime che è richiesto in queste piattaforme.

Tuttavia, quando si compila per la piattaforma macOS, è possibile scegliere se il framework generato deve incorporare xamarin. Mac o non. È possibile incorporare xamarin. Mac se l'assembly associato non fa riferimento a xamarin (direttamente o indirettamente) e questa opzione è selezionata, passando `--platform=macOS` allo strumento di incorporamento di .NET.

Se l'assembly associato contiene un riferimento a xamarin, è necessario incorporare xamarin. Mac e anche il embeddinator deve sapere quale framework di destinazione da usare.

Esistono tre Framework di destinazione di xamarin. Mac possibili: `modern` (chiamato in precedenza `mobile`), `full` e `system` (la differenza tra ciascuno di essi è descritto in di xamarin. Mac [framework di destinazione] [ 1] documentazione), e ognuno è selezionata per il passaggio `--platform=macOS-modern`, `--platform=macOS-full` o `--platform=macOS-system` allo strumento di incorporamento di .NET.

[1]: ~/mac/platform/target-framework.md
