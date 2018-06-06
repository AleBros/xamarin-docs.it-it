---
title: Integrazioni di esempio
description: Questo documento include collegamenti ad esempi che illustrano le integrazioni per le cartelle di lavoro di Xamarin. Gli esempi collegati funzionano con SkiaSharp e il rendering di rappresentazione.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: 75d88af4c294a35d45f05724eb96ce822cddf126
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793973"
---
# <a name="sample-integrations"></a>Integrazioni di esempio

Vedere il [cucina Sink] [ KitchenSink] per un esempio funzionante di un'integrazione. Semplicemente compilare `KitchenSink.sln` in Visual Studio per Mac o Visual Studio e quindi aprire `KitchenSink.workbook`.

[![Schermata di integrazione di cucina Sink](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

L'esempio cucina Sink illustra entrambi i set di concetti:

* Le parti di rappresentazione viene illustrato come utilizzare `RepresentationManager` per migliorare il rendering con le rappresentazioni predefinite.
* Il `Person` oggetto e il renderer JavaScript associato illustrano l'utilizzo di `ISerializableObject` senza passare attraverso un provider di rappresentazione.

Vedere anche [SkiaSharp] [ skiasharp] per un esempio reale di un'integrazione che utilizzerà il [rappresentazioni](~/tools/workbooks/sdk/representations.md) fornito da cartelle di lavoro di Xamarin per eseguire il rendering nei relativi tipi.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
