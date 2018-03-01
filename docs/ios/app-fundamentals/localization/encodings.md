---
title: Codifiche di internazionalizzazione
ms.topic: article
ms.prod: xamarin
ms.assetid: F5117294-28BB-4583-B6A0-A339B050FDE1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 2058a2453731899fcf8411e4798fd87f8e9b7b71
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="internationalization-encodings"></a>Codifiche di internazionalizzazione

Per impostazione predefinita, non tutte le codifiche sono inclusi nella libreria di classi di xamarin. IOS.

Per ridurre le dimensioni dell'applicazione, non includere xamarin qualsiasi codifica specifica, è necessario indicare mtouch per includere gli assembly che contiene il supporto per la codifica che è necessario.

Questa operazione viene eseguita selezionando le codifiche aggiuntive dal riquadro compilazione/opzioni avanzate di iOS in Visual Studio per Mac o Visual Studio:

 [ ![](encodings-images/00.png "Selezionare le codifiche aggiuntive")](encodings-images/00.png)

 [ ![](encodings-images/00a.png "Selezionare le codifiche aggiuntive")](encodings-images/00a.png)

È possibile selezionare uno dei seguenti:

-  CJK: per Chineese, giapponese e coreano
-  mideast: arabo, ebraico, turco e Latin5.
-  altri: Cirillico, Baltico, vietnamita, ucraino e Thai
-  rari: EBCDIC codifiche e le altre tabelle codici rari
-  occidentale: lingue latine, Pasqua e dell'Europa occidentale
-  tutti


 <a name="cjk" />


## <a name="cjk"></a>cjk

-  CP51932
-  CP932
-  CP936
-  CP949
-  CP950
-  CP54936


 <a name="mideast" />


## <a name="mideast"></a>mideast

-  CP1254
-  CP1255
-  CP1256
-  CP28596
-  CP28598
-  CP28599
-  CP38598


 <a name="other" />


## <a name="other"></a>altro

-  CP1251
-  CP1257
-  CP1258
-  CP20866
-  CP21866
-  CP28594
-  CP28595
-  CP57002
-  CP874


 <a name="rare" />


## <a name="rare"></a>rari

-  CP1026
-  CP1047
-  CP1140
-  CP1141
-  CP1142
-  CP1143
-  CP1144
-  CP1145
-  CP1146
-  CP1147
-  CP1148
-  CP1149
-  CP20273
-  CP20277
-  CP20278
-  CP20280
-  CP20284
-  CP20285
-  CP20290
-  CP20297
-  CP20420
-  CP20424
-  CP20871
-  CP21025
-  CP37
-  CP500
-  CP708
-  CP852
-  CP855
-  CP857
-  CP858
-  CP862
-  CP864
-  CP866
-  CP869
-  CP870
-  CP875


 <a name="west" />


## <a name="west"></a>Occidentale

-  CP10000
-  CP10079
-  CP1250
-  CP1252
-  CP1253
-  CP28592
-  CP28593
-  CP28597
-  CP28605
-  CP437
-  CP850
-  CP860
-  CP861
-  CP863
-  CP865

