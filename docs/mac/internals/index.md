---
title: Dietro le quinte in Novell. Mac
description: Questo documento contiene collegamenti a diverse guide che descrivono il funzionamento interno di Novell. Mac. I documenti collegati discutono in anticipo della compilazione, dell'architettura Novell. Mac e del registrar Novell. Mac.
ms.prod: xamarin
ms.assetid: 84974D75-0CCE-4455-AA38-00DE68AE33B6
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: 51cf479ba07a769f5d7a875bb3f1203caef2ad0b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290102"
---
# <a name="under-the-hood-in-xamarinmac"></a>Dietro le quinte in Novell. Mac

## <a name="ahead-of-time-compilation-aotaotmd"></a>[Compilazione ahead of Time (AOT)](aot.md)

La compilazione ahead of Time (AOT) è una tecnica di ottimizzazione efficace per migliorare le prestazioni di avvio. Tuttavia, influiscono anche sul tempo di compilazione, sulle dimensioni dell'applicazione e sull'esecuzione del programma in modi profondi, quindi è utile comprenderne il funzionamento.

## <a name="mac-architecturearchitecturemd"></a>[Architettura Mac](architecture.md)

Relazione tra Novell. Mac e Objective-C, inclusi concetti quali la compilazione, i selettori, i registrar, l'avvio dell'app e il generatore.

## <a name="xamarinmac-registrarregistrarmd"></a>[Registrar Novell. Mac](registrar.md)

Novell. Mac colma il gap tra il runtime gestito e il runtime di Cocoa, consentendo alle classi gestite di chiamare le classi Objective-C non gestite e di essere richiamate quando si verificano gli eventi. Il lavoro richiesto per la preformulazione di questa "magia" viene gestito dal registrar, ma la comprensione di ciò che accade "dietro le quinte" può a volte essere utile.
