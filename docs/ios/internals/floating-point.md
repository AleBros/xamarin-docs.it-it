---
title: Operazioni a virgola mobile in Novell. iOS
description: Questo documento descrive il modo in cui Novell. iOS gestisce le operazioni a virgola mobile e precisione a 32 bit a 64 bit e illustra gli effetti associati alle prestazioni.
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/25/2015
ms.openlocfilehash: cd1bd0507f89f7b29bfcd3ef1ba0a3b1215632ce
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527369"
---
# <a name="floating-point-operations-in-xamarinios"></a>Operazioni a virgola mobile in Novell. iOS

Per impostazione predefinita, Novell. iOS esegue operazioni a virgola mobile a 32 bit e a 64 bit con precisione 64 bit su ARM.  

Sebbene questa precisione più elevata si avvicini a ciò che gli sviluppatori si aspettano da operazioni a virgola mobile in C# sul desktop, su dispositivi mobili, l'impatto sulle prestazioni può essere significativo.

È possibile compilare il codice a virgola mobile a 32 bit per usare operazioni a virgola mobile a 32 bit.  A tale scopo, è necessario usare almeno Novell. iOS 8,10 e impostare nel pannello delle opzioni di compilazione iOS nella riga della voce "argomenti aggiuntivi di mTouch" il valore seguente:

```
--aot-options=-O=float32
```

In questo modo, i compilatori statici, ovvero il compilatore statico incorporato di mono o LLVM, vengono informati per eseguire operazioni a virgola mobile usando float a 32 bit.
