---
title: Concetti avanzati e meccanismi interni
description: Architettura sottostante dietro la progettazione delle API xamarin. Android e le relative.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/21/2018
ms.openlocfilehash: f5844dd4340afa0596219a33ed1e479a0dbcfa76
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114171"
---
# <a name="advanced-concepts-and-internals"></a>Concetti avanzati e meccanismi interni

_In questa sezione contiene argomenti che illustrano l'architettura, progettazione di API e le limitazioni di xamarin. Android. Inoltre, sono inclusi argomenti che illustrano l'implementazione di garbage collection e gli assembly che sono disponibili in xamarin. Android. Perché è di xamarin. Android [open source](https://github.com/xamarin/xamarin-android), è anche possibile comprendere i meccanismi interni di xamarin. Android esaminando il codice sorgente._


##  <a name="architectureandroidinternalsarchitecturemd"></a>[Architettura](~/android/internals/architecture.md)

Questo articolo illustra l'architettura sottostante alla base di un'applicazione xamarin. Android. Illustra come le applicazioni xamarin. Android vengono eseguite all'interno di un ambiente di esecuzione Mono insieme con il runtime di Android macchina virtuale e illustra questi concetti chiave come Android Callable Wrapper e gestiti Callable Wrapper. 



##  <a name="api-designandroidinternalsapi-designmd"></a>[Progettazione dell'API](~/android/internals/api-design.md)

Oltre ai core di librerie di classi di Base che fanno parte di Mono, xamarin. Android viene fornito con le associazioni per varie API Android consente agli sviluppatori di creare applicazioni Android native con Mono.

Alla base di xamarin. Android si è un motore di interoperabilità questo mondo Bridge c# con il mondo di Java e offre agli sviluppatori l'accesso alle API Java da c# o altri linguaggi .NET.



##  <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Assembly](~/cross-platform/internals/available-assemblies.md)

Xamarin. Android viene fornito con molti assembly. Esattamente come Silverlight che rappresenta un sottoinsieme degli assembly di desktop .NET, xamarin. Android è anche un subset esteso di diversi Silverlight e desktop assembly .NET. 

