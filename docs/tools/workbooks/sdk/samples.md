---
title: Integrazioni di esempio
description: Questo documento include collegamenti a esempi che illustrano le integrazioni Xamarin Workbooks. Gli esempi collegati funzionano con rendering di rappresentazione e SkiaSharp.
ms.prod: xamarin
ms.assetid: 327DAD2E-1F76-4EB5-BCD0-9E7384D99E48
author: conceptdev
ms.author: crdun
ms.date: 03/30/2017
ms.openlocfilehash: fbe471aa7f08d85a870d68505cf2c983b7e442e9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292806"
---
# <a name="sample-integrations"></a>Integrazioni di esempio

Vedere l'esempio di [sink della cucina][KitchenSink] per un esempio funzionante di integrazione. È sufficiente `KitchenSink.sln` compilare in Visual Studio per Mac o Visual Studio, quindi `KitchenSink.workbook`aprire.

[![Schermata di integrazione del sink della cucina](samples-images/kitchensinkintegrationscreenshot.png)](samples-images/kitchensinkintegrationscreenshot.png#lightbox)

Nell'esempio kitchen sink vengono illustrati entrambi i set di concetti:

* Nelle parti della rappresentazione viene illustrato come `RepresentationManager` utilizzare per migliorare il rendering utilizzando le rappresentazioni predefinite.
* L' `Person` oggetto e il renderer JavaScript associato dimostrano l'uso `ISerializableObject` di senza passare attraverso un provider di rappresentazione.

Vedere anche [SkiaSharp][skiasharp] per un esempio reale di integrazione che usa le [rappresentazioni](~/tools/workbooks/sdk/representations.md) esistenti fornite da Xamarin Workbooks per eseguire il rendering dei relativi tipi.

[KitchenSink]: https://github.com/xamarin/Workbooks/tree/master/SDK/Samples/KitchenSink
[skiasharp]: https://github.com/mono/SkiaSharp/tree/master/source/SkiaSharp.Workbooks
