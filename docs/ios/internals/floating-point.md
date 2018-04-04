---
title: A virgola mobile
ms.prod: xamarin
ms.assetid: 003F25C1-B430-4339-9C95-7DF527EBC699
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 67fcf459747152346d32eb5836fa22b99719af12
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="floating-point"></a>A virgola mobile

Xamarin esegue per impostazione predefinita a 32 e 64 bit operazioni a virgola mobile con precisione a 64 bit su ARM.  

Durante questa precisione più elevata si avvicina a ciò che gli sviluppatori si aspettano da operazioni a virgola mobile in c# sul desktop, mobili, l'impatto sulle prestazioni può essere significativo.

È possibile compilare il codice punto a virgola mobile a 32 bit per l'utilizzo di operazioni a virgola mobile a 32 bit.  A tale scopo, è necessario utilizzare almeno 8.10 xamarin e set in iOS per la generazione del Pannello di opzioni sul "mtouch argomenti aggiuntivi" voce di riga il valore seguente:

     --aot-options=-O=float32

Ciò indicherà i compilatori statici (compilatore statico incorporato del Mono, o quello con tecnologia LLVM) per eseguire operazioni a virgola mobile con virgola mobile a 32 bit.
