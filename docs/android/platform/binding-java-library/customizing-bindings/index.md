---
title: Personalizzazione delle associazioni
description: È possibile personalizzare un'associazione Xamarin.Android modificando i metadati che controllano il processo di associazione. Queste modifiche manuali sono spesso necessarie per la risoluzione degli errori di compilazione e per il data shaping dell'API risultante, in modo che C#sia più coerente con/.NET. Queste guide illustrano la struttura di questi metadati, come modificare i metadati e come usare JavaDoc per recuperare i nomi dei parametri del metodo.
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 04f3720d8684129476c955819390e91330a7800a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020648"
---
# <a name="customizing-bindings"></a>Personalizzazione delle associazioni

_È possibile personalizzare un'associazione Xamarin.Android modificando i metadati che controllano il processo di associazione. Queste modifiche manuali sono spesso necessarie per la risoluzione degli errori di compilazione e per il data shaping dell'API risultante, in modo che C#sia più coerente con/.NET. Queste guide illustrano la struttura di questi metadati, come modificare i metadati e come usare JavaDoc per recuperare i nomi dei parametri del metodo._

## <a name="overview"></a>Panoramica

Xamarin.Android automatizza gran parte del processo di associazione. Tuttavia, in alcuni casi è necessaria una modifica manuale per risolvere i problemi seguenti:

- Risoluzione degli errori di compilazione causati da tipi mancanti, tipi offuscati, nomi duplicati, problemi di visibilità delle classi e altre situazioni che non possono essere risolti dagli strumenti Xamarin.Android. 

- Modifica del mapping usato da Xamarin.Android per associare l'API Android a tipi diversi in C# (ad esempio, molti sviluppatori preferiscono eseguire il mapping delle costanti C# Java `int`alle costanti`enum`).

- Rimozione dei tipi inutilizzati che non devono essere associati. 

- Aggiunta di tipi che non hanno alcuna controparte nell'API Java sottostante. 

È possibile apportare alcune o tutte queste modifiche modificando i metadati che controllano il processo di associazione.

## <a name="guides"></a>Guide

Le guide seguenti descrivono i metadati che controllano il processo di associazione e spiegano come modificare i metadati per risolvere questi problemi:

- I [metadati delle associazioni Java](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) forniscono una panoramica dei metadati che entrano in un'associazione Java.
    Vengono descritti i vari passaggi manuali talvolta necessari per completare una libreria di associazione Java e viene illustrato come creare un'API esposta da un'associazione per seguire più da vicino le linee guida per la progettazione di .NET.

- I [parametri di denominazione con javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) illustrano come recuperare i nomi dei parametri in un progetto di associazione Java usando Javadoc prodotto dal progetto Java associato.
