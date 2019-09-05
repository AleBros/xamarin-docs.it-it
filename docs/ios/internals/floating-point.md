---
title: Operazioni a virgola mobile in Novell. iOS
description: Questo documento descrive il modo in cui Novell. iOS gestisce le operazioni a virgola mobile e precisione a 32 bit a 64 bit e illustra gli effetti associati alle prestazioni.
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: 1ecb00fecaf14afb8c6d5c59297eb26821ed791a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291932"
---
# <a name="floating-point-operations-in-xamarinios"></a>Operazioni a virgola mobile in Novell. iOS

Per impostazione predefinita, Novell. iOS esegue operazioni a virgola mobile a 32 bit e a 64 bit con precisione 64 bit su ARM.  

Sebbene questa precisione più elevata si avvicini a ciò che gli sviluppatori si aspettano da operazioni a virgola mobile in C# sul desktop, su dispositivi mobili, l'impatto sulle prestazioni può essere significativo.

È possibile compilare il codice a virgola mobile a 32 bit per usare operazioni a virgola mobile a 32 bit.  A tale scopo, è necessario usare almeno Novell. iOS 8,10 e impostare nel pannello delle opzioni di compilazione iOS nella riga della voce "argomenti aggiuntivi di mTouch" il valore seguente:

```
--aot-options=-O=float32
```

In questo modo, i compilatori statici, ovvero il compilatore statico incorporato di mono o LLVM, vengono informati per eseguire operazioni a virgola mobile usando float a 32 bit.
