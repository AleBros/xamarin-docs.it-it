---
title: Personalizzazione delle associazioni
description: È possibile personalizzare un'associazione Novell. Android modificando i metadati che controllano il processo di associazione. Queste modifiche manuali sono spesso necessarie per la risoluzione degli errori di compilazione e per il data shaping dell'API risultante, in modo che C#sia più coerente con/.NET. Queste guide illustrano la struttura di questi metadati, come modificare i metadati e come usare JavaDoc per recuperare i nomi dei parametri del metodo.
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/25/2017
ms.openlocfilehash: 37e91070ea10defedbeebdbb06220fabbd2554fc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756634"
---
# <a name="customizing-bindings"></a>Personalizzazione delle associazioni

_È possibile personalizzare un'associazione Novell. Android modificando i metadati che controllano il processo di associazione. Queste modifiche manuali sono spesso necessarie per la risoluzione degli errori di compilazione e per il data shaping dell'API risultante, in modo che C#sia più coerente con/.NET. Queste guide illustrano la struttura di questi metadati, come modificare i metadati e come usare JavaDoc per recuperare i nomi dei parametri del metodo._

## <a name="overview"></a>Panoramica

Novell. Android automatizza gran parte del processo di associazione. Tuttavia, in alcuni casi è necessaria una modifica manuale per risolvere i problemi seguenti:

- Risoluzione degli errori di compilazione causati da tipi mancanti, tipi offuscati, nomi duplicati, problemi di visibilità delle classi e altre situazioni che non possono essere risolti dagli strumenti Novell. Android. 

- Modifica del mapping usato da Novell. Android per associare l'API Android a tipi diversi in C# . ad esempio, molti sviluppatori preferiscono eseguire `int` il mapping delle C# `enum` costanti Java alle costanti.

- Rimozione dei tipi inutilizzati che non devono essere associati. 

- Aggiunta di tipi che non hanno alcuna controparte nell'API Java sottostante. 

È possibile apportare alcune o tutte queste modifiche modificando i metadati che controllano il processo di associazione.

## <a name="guides"></a>Guide

Le guide seguenti descrivono i metadati che controllano il processo di associazione e spiegano come modificare i metadati per risolvere questi problemi:

- I [metadati delle associazioni Java](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) forniscono una panoramica dei metadati che entrano in un'associazione Java.
    Vengono descritti i vari passaggi manuali talvolta necessari per completare una libreria di associazione Java e viene illustrato come creare un'API esposta da un'associazione per seguire più da vicino le linee guida per la progettazione di .NET.

- I [parametri di denominazione con javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) illustrano come recuperare i nomi dei parametri in un progetto di associazione Java usando Javadoc prodotto dal progetto Java associato.
