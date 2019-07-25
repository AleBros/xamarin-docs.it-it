---
title: Integrazioni di esempio
description: Questo documento include esempi che illustrano le integrazioni di Xamarin Workbooks. Esempi collegati funzionano con SkiaSharp e il rendering di rappresentazione.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: e35577b116180d2745e2f6afb792547f63873214
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424602"
---
# <a name="sample-integrations"></a>Integrazioni di esempio

Vedere le [Sink cucina][KitchenSink] esempio per un esempio pratico di un'integrazione. È sufficiente compilare `KitchenSink.sln` in Visual Studio per Mac o Visual Studio e quindi aprire `KitchenSink.workbook`.

[![Schermata di integrazione di cucina Sink](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

L'esempio cucina Sink illustra entrambi i set di concetti:

* Le parti di rappresentazione viene illustrato come usare `RepresentationManager` per migliorare il rendering con le rappresentazioni incorporate.
* Il `Person` il renderer di JavaScript associato e oggetto illustrano l'uso `ISerializableObject` senza passare attraverso un provider di rappresentazione.

Vedere anche [SkiaSharp][skiasharp] per un esempio reale di un'integrazione che utilizzerà il [rappresentazioni](~/tools/workbooks/sdk/representations.md) forniti da Xamarin Workbooks per eseguire il rendering dei tipi.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
