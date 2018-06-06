---
title: Dietro le quinte in Xamarin.Mac
description: Questo documento include collegamenti ad varie guide che descrivono il funzionamento interno dei Xamarin.Mac. Documenti collegati discutere prima compilazione Xamarin.Mac architettura e il registrar Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: c940252a675c38247d2c5bb374b9c30237222bda
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792489"
---
# <a name="under-the-hood-in-xamarinmac"></a>Dietro le quinte in Xamarin.Mac

## <a name="ahead-of-time-compilation-aotaotmd"></a>[Avanti di compilazione (AOT)](aot.md)

Avanti di tempo (AOT) compilazione è una tecnica di ottimizzazione avanzate per migliorare le prestazioni di avvio. Tuttavia, influisce anche il tempo di compilazione, dimensioni dell'applicazione e l'esecuzione del programma in modi profondi, è quindi utile comprenderne il funzionamento.

## <a name="mac-architecturearchitecturemd"></a>[Architettura Mac](architecture.md)

Relazione di Xamarin.Mac Objective-C, inclusi concetti quali compilazione, i selettori, Registrar, l'app viene avviata e il generatore.

## <a name="xamarinmac-registrarregistrarmd"></a>[Xamarin.Mac registrar](registrar.md)

Xamarin.Mac colma il divario tra ambiente gestito e di runtime del Cocoa, consentendo di classi gestite chiamare classi Objective-C non gestite ed essere richiamato quando si verificano eventi. Il lavoro necessario per eseguire questa "chiave" è gestito dal programma di registrazione, ma può talvolta essere utile per capire cosa accade "dietro le quinte".
