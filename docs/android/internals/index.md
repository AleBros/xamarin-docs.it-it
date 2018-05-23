---
title: Concetti avanzati e elementi interni
description: Architettura sottostante dietro xamarin e la progettazione delle API.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/21/2018
ms.openlocfilehash: 79e61db4c27a2d29b4ee0a9d39f2d25ea5d93303
ms.sourcegitcommit: 9f8e7393019791bbd6af4fefaa24a1602adabb4e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2018
---
# <a name="advanced-concepts-and-internals"></a>Concetti avanzati e elementi interni

_In questa sezione contiene argomenti che illustrano l'architettura, la progettazione delle API e le limitazioni di xamarin. Android. Inoltre, sono inclusi argomenti che illustrano l'implementazione di garbage collection e gli assembly che sono disponibili in xamarin. Android. Poiché xamarin. Android [open source](https://github.com/xamarin/xamarin-android), è anche possibile comprendere il funzionamento interno di xamarin. Android esaminando il codice sorgente._


##  <a name="architectureandroidinternalsarchitecturemd"></a>[Architettura](~/android/internals/architecture.md)

Questo articolo illustra l'architettura sottostante protetti da un'applicazione di xamarin. Viene illustrato come applicazioni xamarin vengono eseguite all'interno di un ambiente di esecuzione Mono insieme con il runtime Android macchina virtuale e illustra i concetti quali chiave Android Callable Wrapper e gestiti Callable Wrapper. 



##  <a name="api-designandroidinternalsapi-designmd"></a>[Progettazione dell'API](~/android/internals/api-design.md)

Oltre alle librerie di classi di Base che fanno parte di Mono di core, xamarin viene fornito con le associazioni per varie API Android consente agli sviluppatori di creare applicazioni Android native con Mono.

Sono la base di xamarin è un motore di interoperabilità che world Bridge c# con il mondo Java e offre agli sviluppatori l'accesso alle API Java da c# o altri linguaggi .NET.



##  <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Assembly](~/cross-platform/internals/available-assemblies.md)

Xamarin viene fornito con diversi assembly. Come Silverlight è un sottoinsieme degli assembly .NET desktop esteso, xamarin è un sottoinsieme dei numerosi Silverlight e gli assembly .NET desktop esteso. 

