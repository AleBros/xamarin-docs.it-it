---
title: Dietro le quinte in xamarin. Mac
description: Questo documento include collegamenti alle varie guide che descrivono i meccanismi interni di xamarin. Mac. I documenti collegati discutere in anticipo rispetto alla fase di compilazione, l'architettura di xamarin. Mac e il programma di registrazione di xamarin. Mac.
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 872f26febf3abbe4d659773d2bf2d27348c64513
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61033243"
---
# <a name="under-the-hood-in-xamarinmac"></a>Dietro le quinte in xamarin. Mac

## <a name="ahead-of-time-compilation-aotaotmd"></a>[-Ahead di compilazione time (AOT)](aot.md)

Anticipo del tempo (AOT) della compilazione è una tecnica di ottimizzazione avanzate per migliorare le prestazioni di avvio. Tuttavia, influisce anche il tempo di compilazione, le dimensioni dell'applicazione e l'esecuzione del programma in modi profondi, è quindi utile comprenderne il funzionamento.

## <a name="mac-architecturearchitecturemd"></a>[Architettura Mac](architecture.md)

Relazione di xamarin. Mac e Objective-C, inclusi concetti quali compilazione, i selettori, Registrar, avvio dell'app e il generatore.

## <a name="xamarinmac-registrarregistrarmd"></a>[Registrar di xamarin. Mac](registrar.md)

Xamarin. Mac colma il divario tra ambiente gestito e il runtime di Cocoa, consentendo le classi gestite chiamare le classi di Objective-C non gestite ed essere richiamato quando si verificano eventi. Il lavoro necessario per eseguire questo comando "magic" viene gestito dal programma di registrazione, ma può talvolta essere utile comprendere cosa succede "dietro le quinte".
