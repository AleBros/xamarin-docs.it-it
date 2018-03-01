---
title: Integrazioni di esempio
ms.topic: article
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.openlocfilehash: 3b07e457bd54f16826f44a3b913434fea6ebe20b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="sample-integrations"></a>Integrazioni di esempio

## <a name="sample-integrations"></a>Integrazioni di esempio

Vedere il [cucina Sink] [ KitchenSink] per un esempio funzionante di un'integrazione. Semplicemente compilare `KitchenSink.sln` in Visual Studio per Mac o Visual Studio e quindi aprire `KitchenSink.workbook`.

[![Schermata di integrazione di cucina Sink](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png)

L'esempio cucina Sink illustra entrambi i set di concetti:

* Le parti di rappresentazione viene illustrato come utilizzare `RepresentationManager` per migliorare il rendering con le rappresentazioni predefinite.
* Il `Person` oggetto e il renderer JavaScript associato illustrano l'utilizzo di `ISerializableObject` senza passare attraverso un provider di rappresentazione.

Vedere anche [SkiaSharp] [ skiasharp] per un esempio reale di un'integrazione che utilizzerà il [rappresentazioni](~/tools/workbooks/sdk/representations.md) fornito da cartelle di lavoro di Xamarin per eseguire il rendering nei relativi tipi.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks