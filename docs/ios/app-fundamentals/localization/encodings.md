---
title: Codifiche di internazionalizzazione in Xamarin.iOS
description: Questo documento descrive le codifiche di internazionalizzazione in Xamarin.iOS, illustrando le codifiche disponibili e come aggiungerle a un'app.
ms.prod: xamarin
ms.assetid: F5117294-28BB-4583-B6A0-A339B050FDE1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/28/2017
ms.openlocfilehash: 2d204aeac18fc08bf6eb2221b6f64ecbbded8121
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009996"
---
# <a name="internationalization-encodings-in-xamarinios"></a>Codifiche di internazionalizzazione in Xamarin.iOS

Per impostazione predefinita, non tutte le codifiche sono incluse nella libreria di classi Xamarin.iOS.

Per ridurre le dimensioni dell'applicazione, Xamarin.iOS non include alcuna codifica specifica ed è necessario indicare a mTouch di includere gli assembly che contengono il supporto per la codifica necessaria.

Questa operazione viene eseguita selezionando le codifiche aggiuntive dal riquadro compilazione/avanzata iOS in Visual Studio per Mac o Visual Studio:

 [![](encodings-images/00.png "Selecting the extra encodings")](encodings-images/00.png#lightbox)

 [![](encodings-images/00a.png "Selecting the extra encodings")](encodings-images/00a.png#lightbox)

È possibile selezionare uno dei seguenti:

- CJK: per Chineese, giapponese e coreano
- Medio Oriente: arabo, ebraico, turco e Latin5.
- altro: cirillico, Baltico, vietnamita, ucraino e tailandese
- rare: codifiche EBCDIC e altre tabelle codici rare
- ovest: lingue latine, Pasqua ed Europa occidentale
- tutti

 <a name="cjk" />

## <a name="cjk"></a>CJK

- CP51932
- CP932
- CP936
- CP949
- CP950
- CP54936

 <a name="mideast" />

## <a name="mideast"></a>mo

- CP1254
- CP1255
- CP1256
- CP28596
- CP28598
- CP28599
- CP38598

 <a name="other" />

## <a name="other"></a>altro

- CP1251
- CP1257
- CP1258
- CP20866
- CP21866
- CP28594
- CP28595
- CP57002
- CP874

 <a name="rare" />

## <a name="rare"></a>rari

- CP1026
- CP1047
- CP1140
- CP1141
- CP1142
- CP1143
- CP1144
- CP1145
- CP1146
- CP1147
- CP1148
- CP1149
- CP20273
- CP20277
- CP20278
- CP20280
- CP20284
- CP20285
- CP20290
- CP20297
- CP20420
- CP20424
- CP20871
- CP21025
- CP37
- CP500
- CP708
- CP852
- CP855
- CP857
- CP858
- CP862
- CP864
- CP866
- CP869
- CP870
- CP875

 <a name="west" />

## <a name="west"></a>occidentale

- CP10000
- CP10079
- CP1250
- CP1252
- CP1253
- CP28592
- CP28593
- CP28597
- CP28605
- CP437
- CP850
- CP860
- CP861
- CP863
- CP865
