---
title: Concetti avanzati e meccanismi interni
description: Architettura sottostante dietro Xamarin.Android e la progettazione dell'API.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/21/2018
ms.openlocfilehash: 97382243ac5f767d94a782b895401c1f2f8ae554
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027849"
---
# <a name="advanced-concepts-and-internals"></a>Concetti avanzati e meccanismi interni

_Questa sezione contiene argomenti che illustrano l'architettura, la progettazione di API e le limitazioni di Xamarin.Android. Sono inoltre inclusi argomenti che illustrano l'implementazione di Garbage Collection e gli assembly disponibili in Xamarin.Android. Poiché Xamarin.Android è [Open Source](https://github.com/xamarin/xamarin-android), è anche possibile comprendere il funzionamento interno di Xamarin.Android esaminando il codice sorgente._

## <a name="architectureandroidinternalsarchitecturemd"></a>[Architettura](~/android/internals/architecture.md)

Questo articolo illustra l'architettura sottostante dietro un'applicazione Xamarin.Android. Viene illustrato il modo in cui le applicazioni Xamarin.Android vengono eseguite all'interno di un ambiente di esecuzione mono insieme alla macchina virtuale di runtime di Android e spiegano questi concetti chiave come Android Callable Wrapper e Managed wrappers richiamabili. 

## <a name="api-designandroidinternalsapi-designmd"></a>[Progettazione dell'API](~/android/internals/api-design.md)

Oltre alle librerie di classi base core che fanno parte di mono, Xamarin.Android viene fornito con binding per varie API Android per consentire agli sviluppatori di creare applicazioni Android Native con mono.

Alla base di Xamarin.Android è disponibile un motore di interoperabilità che C# colma il mondo con Java World e fornisce agli sviluppatori l'accesso alle API Java C# da o altri linguaggi .NET.

## <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Assembly](~/cross-platform/internals/available-assemblies.md)

Xamarin.Android viene fornito con diversi assembly. Proprio come Silverlight è un subset esteso degli assembly .NET desktop, Xamarin.Android è anche un subset esteso di diversi assembly .NET per Silverlight e desktop. 
