---
title: A virgola mobile
ms.topic: article
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: ea29132ad4ac55f6fb151ac2125ab1add82c8518
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="floating-point"></a>A virgola mobile

Xamarin esegue per impostazione predefinita a 32 e 64 bit operazioni a virgola mobile con precisione a 64 bit su ARM.  

Durante questa precisione più elevata si avvicina a ciò che gli sviluppatori si aspettano da operazioni a virgola mobile in c# sul desktop, mobili, l'impatto sulle prestazioni può essere significativo.

È possibile compilare il codice punto a virgola mobile a 32 bit per l'utilizzo di operazioni a virgola mobile a 32 bit.  A tale scopo, è necessario utilizzare almeno 8.10 xamarin e set in iOS per la generazione del Pannello di opzioni sul "mtouch argomenti aggiuntivi" voce di riga il valore seguente:

     --aot-options=-O=float32

Ciò indicherà i compilatori statici (compilatore statico incorporato del Mono, o quello con tecnologia LLVM) per eseguire operazioni a virgola mobile con virgola mobile a 32 bit.
