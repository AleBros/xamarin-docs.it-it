---
title: Piattaforme Objective-C
ms.prod: xamarin
ms.assetid: 43253BE4-A03A-4646-9A14-32C05174E672
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: a783dc554f54922f4fa4f99a43d83c4c864ef681
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="objective-c-platforms"></a>Piattaforme Objective-C


Diverse piattaforme .NET incorporamento può di destinazione durante la generazione di codice Objective-C:

* macOS
* iOS
* tvOS
* watchOS [non ancora implementata]

La piattaforma è selezionata, passando il `--platform=<platform>` argomento della riga di comando per il embeddinator.

Quando si compila per le piattaforme iOS, tvOS e watchOS il embeddinator creerà sempre un framework che incorpora xamarin, poiché xamarin contiene la quantità di codice di supporto di runtime che è richiesto in queste piattaforme.

Tuttavia, quando si compila per la piattaforma macOS, è possibile scegliere se il framework generato deve incorporare Xamarin.Mac o non. È possibile incorporare Xamarin.Mac se l'assembly associato non fa riferimento a Xamarin.Mac.dll (direttamente o indirettamente) e questa opzione è selezionata passando `--platform=macOS` per il embeddinator.

Se l'assembly associato contiene un riferimento a Xamarin.Mac.dll, è necessario incorporare Xamarin.Mac e inoltre la embeddinator deve conoscere quali framework di destinazione da utilizzare.

Esistono tre possibili Framework di destinazione Xamarin.Mac: `modern` (precedentemente denominato `mobile`), `full` e `system` (la differenza tra ciascuno di essi è descritto in Xamarin.Mac [framework di destinazione] [ 1] documentazione), e ognuno è selezionato per il passaggio di `--platform=macOS-modern`, `--platform=macOS-full` o `--platform=macOS-system` per il embeddinator.

[1]: ~/mac/platform/target-framework.md
