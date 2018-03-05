---
title: Dietro le quinte
description: Una visualizzazione di meccanismi interni di Xamarin.Mac
ms.topic: article
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: 2ba3ffb421dc64bba7df1e10a40125f14365f29e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="under-the-hood"></a>Dietro le quinte

_Una visualizzazione di meccanismi interni di Xamarin.Mac_

## <a name="ahead-of-time-compilation-aotaotmd"></a>[Avanti di compilazione (AOT)](aot.md)

Avanti di tempo (AOT) compilazione è una tecnica di ottimizzazione avanzate per migliorare le prestazioni di avvio. Tuttavia, influisce anche il tempo di compilazione, dimensioni dell'applicazione e l'esecuzione del programma in modi profondi, è quindi utile comprenderne il funzionamento.

## <a name="mac-architecturearchitecturemd"></a>[Architettura Mac](architecture.md)

Relazione di Xamarin.Mac Objective-C, inclusi concetti quali compilazione, i selettori, Registrar, l'app viene avviata e il generatore.

## <a name="xamarinmac-registrarregistrarmd"></a>[Xamarin.Mac registrar](registrar.md)

Xamarin.Mac colma il divario tra ambiente gestito e di runtime del Cocoa, consentendo di classi gestite chiamare classi Objective-C non gestite ed essere richiamato quando si verificano eventi. Il lavoro necessario per eseguire questa "chiave" è gestito dal programma di registrazione, ma può talvolta essere utile per capire cosa accade "dietro le quinte".
