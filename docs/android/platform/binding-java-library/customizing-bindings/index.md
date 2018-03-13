---
title: Personalizzazione delle associazioni
description: "È possibile personalizzare un'associazione xamarin modificando i metadati che controlla il processo di associazione. Queste modifiche manuali sono spesso necessarie per la risoluzione di errori di compilazione e per il data shaping l'API risulta in modo che risulti più coerenza con c# o .NET. Queste guide vengono illustrate la struttura dei metadati, come modificare i metadati e come utilizzare JavaDoc per recuperare i nomi dei parametri del metodo."
ms.topic: article
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 09/25/2017
ms.openlocfilehash: 14372c3ca42d1ba4a8ade1248f3c5f3210cc7e46
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="customizing-bindings"></a>Personalizzazione delle associazioni

_È possibile personalizzare un'associazione xamarin modificando i metadati che controlla il processo di associazione. Queste modifiche manuali sono spesso necessarie per la risoluzione di errori di compilazione e per il data shaping l'API risulta in modo che risulti più coerenza con c# o .NET. Queste guide vengono illustrate la struttura dei metadati, come modificare i metadati e come utilizzare JavaDoc per recuperare i nomi dei parametri del metodo._


## <a name="overview"></a>Panoramica
 
Xamarin consente di automatizzare gran parte del processo di associazione. Tuttavia, in alcuni casi la modifica manuale è necessaria per risolvere i problemi seguenti:

-   Risoluzione di compilare gli errori causati dalla mancanza di tipi, tipi offuscati, nomi duplicati, i problemi di visibilità di classe e altre situazioni in cui non possono essere risolti per gli strumenti di xamarin. 

-   La modifica del mapping che xamarin viene utilizzato per associare l'API Android a tipi diversi in c# (ad esempio, molti sviluppatori preferiscono eseguire il mapping di Java `int` costanti in c# `enum` costanti).

-   Rimozione dei tipi inutilizzati che non devono essere associate. 

-   Aggiunta di tipi che non dispongono di alcun equivalente nell'API Java sottostante. 

È possibile apportare alcuni o tutti questi cambiamenti modificando i metadati che controlla il processo di associazione.


## <a name="guides"></a>Guide

Le guide seguenti descrivono i metadati che controlla il processo di associazione e viene illustrato come modificare i metadati per risolvere questi problemi:

-   [I metadati di associazioni di Java](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) viene fornita una panoramica dei metadati che passa a un'associazione di Java.
    Descrive i vari passaggi manuali che sono talvolta necessarie per completare una raccolta di binding di linguaggio e viene descritto come definire la forma un'API esposta da un'associazione di più da vicino seguire indicazioni per la progettazione di .NET.

-   [Denominazione dei parametri con Javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) viene illustrato come recuperare i nomi dei parametri in un progetto di associazione Java utilizzando Javadoc generato dal progetto Java associato.


 

