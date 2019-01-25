---
title: Operazioni a virgola mobile in xamarin. IOS
description: Questo documento descrive in che modo xamarin. IOS gestisce 32 bit e a 64 bit di precisione operazioni a virgola mobile e illustra associato impatto sulle prestazioni.
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 11/25/2015
ms.openlocfilehash: 28002ddbab80445757be907b5d9f1cc02fdefd9d
ms.sourcegitcommit: ee626f215de02707b7a94ba1d0fa1d75b22ab84f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54879355"
---
# <a name="floating-point-operations-in-xamarinios"></a>Operazioni a virgola mobile in xamarin. IOS

Xamarin. IOS eseguirà per impostazione predefinita a 32 e 64 bit operazioni a virgola mobile con precisione a 64 bit in ARM.  

Sebbene questa precisione superiore si avvicina a ciò che gli sviluppatori si aspettano da operazioni a virgola mobile in C# sul desktop, App per dispositivi mobili, l'impatto sulle prestazioni può essere significativo.

È possibile compilare il codice punto a virgola mobile a 32 bit per usare operazioni a virgola mobile a 32 bit.  A tale scopo, è necessario usare almeno 8.10 xamarin. IOS e set in iOS per la generazione del Pannello di opzioni sul "mtouch argomenti aggiuntivi" voce di riga il valore seguente:

     --aot-options=-O=float32

Ciò indicherà i compilatori statici (compilatore statico predefinito di Mono, o quella basata su LLVM) per eseguire operazioni a virgola mobile con valori a virgola mobile a 32 bit.
