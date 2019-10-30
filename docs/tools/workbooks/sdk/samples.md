---
title: Integrazioni di esempio
description: Questo documento include collegamenti a esempi che illustrano le integrazioni Xamarin Workbooks. Gli esempi collegati funzionano con rendering di rappresentazione e SkiaSharp.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: davidortinau
ms.author: daortin
ms.date: 03/30/2017
ms.openlocfilehash: fa66ee2b2b469900381e2d31dc5dec49eb42c4f6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73018115"
---
# <a name="sample-integrations"></a>Integrazioni di esempio

Vedere l'esempio di [sink della cucina][KitchenSink] per un esempio funzionante di integrazione. È sufficiente compilare `KitchenSink.sln` in Visual Studio per Mac o in Visual Studio e quindi aprire `KitchenSink.workbook`.

[Schermata di integrazione di![kitchen sink](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

Nell'esempio kitchen sink vengono illustrati entrambi i set di concetti:

* Nelle parti della rappresentazione viene illustrato come utilizzare `RepresentationManager` per migliorare il rendering utilizzando le rappresentazioni predefinite.
* L'oggetto `Person` e il renderer JavaScript associato dimostrano l'uso di `ISerializableObject` senza passare attraverso un provider di rappresentazione.

Vedere anche [SkiaSharp][skiasharp] per un esempio reale di integrazione che usa le [rappresentazioni](~/tools/workbooks/sdk/representations.md) esistenti fornite da Xamarin Workbooks per eseguire il rendering dei relativi tipi.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
