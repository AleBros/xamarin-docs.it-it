---
title: Operazioni a virgola mobile in Xamarin.iOS
description: Questo documento descrive il modo in cui Xamarin.iOS gestisce le operazioni a virgola mobile e precisione a 32 bit a 64 bit e illustra gli effetti associati alle prestazioni.
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: af0e37b41a7bbe831bc629b1fd916f62819b3711
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022335"
---
# <a name="floating-point-operations-in-xamarinios"></a>Operazioni a virgola mobile in Xamarin.iOS

Per impostazione predefinita, Xamarin.iOS esegue operazioni a virgola mobile a 32 bit e a 64 bit con precisione 64 bit su ARM.  

Sebbene questa precisione più elevata si avvicini a ciò che gli sviluppatori si aspettano da operazioni a virgola mobile in C# sul desktop, su dispositivi mobili, l'impatto sulle prestazioni può essere significativo.

È possibile compilare il codice a virgola mobile a 32 bit per usare operazioni a virgola mobile a 32 bit.  A tale scopo, è necessario usare almeno Xamarin.iOS 8,10 e impostare nel pannello delle opzioni di compilazione iOS nella riga della voce "argomenti aggiuntivi di mTouch" il valore seguente:

```
--aot-options=-O=float32
```

In questo modo, i compilatori statici, ovvero il compilatore statico incorporato di mono o LLVM, vengono informati per eseguire operazioni a virgola mobile usando float a 32 bit.
