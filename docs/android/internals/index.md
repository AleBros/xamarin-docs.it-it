---
title: Concetti avanzati e elementi interni
description: Architettura sottostante dietro xamarin e la progettazione delle API.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/13/2017
ms.openlocfilehash: 517e21f2decd0dabbd03d752f13831a891ad7138
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="advanced-concepts-and-internals"></a>Concetti avanzati e elementi interni


##  <a name="architectureandroidinternalsarchitecturemd"></a>[Architettura](~/android/internals/architecture.md)

Questo articolo illustra l'architettura sottostante protetti da un'applicazione di xamarin. Viene illustrato come applicazioni xamarin vengono eseguite all'interno di un ambiente di esecuzione Mono insieme con il runtime Android macchina virtuale e illustra i concetti quali chiave Android Callable Wrapper e gestiti Callable Wrapper. 



##  <a name="api-designandroidinternalsapi-designmd"></a>[Progettazione dell'API](~/android/internals/api-design.md)

Oltre alle librerie di classi di Base che fanno parte di Mono di core, xamarin viene fornito con le associazioni per varie API Android consente agli sviluppatori di creare applicazioni Android native con Mono.

Sono la base di xamarin è un motore di interoperabilità che world Bridge c# con il mondo Java e offre agli sviluppatori l'accesso alle API Java da c# o altri linguaggi .NET.



##  <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Assembly](~/cross-platform/internals/available-assemblies.md)

Xamarin viene fornito con diversi assembly. Come Silverlight è un sottoinsieme degli assembly .NET desktop esteso, xamarin è un sottoinsieme dei numerosi Silverlight e gli assembly .NET desktop esteso. 

