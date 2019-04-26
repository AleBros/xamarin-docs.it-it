---
title: Personalizzazione delle associazioni
description: È possibile personalizzare un'associazione di xamarin. Android modificando i metadati che controlla il processo di associazione. Queste modifiche manuali sono spesso necessari per la risoluzione degli errori di compilazione e per il data shaping dell'API risulta, in modo che sia più coerenza con C#/.NET. Queste guide illustrano la struttura di questi metadati, come modificare i metadati e come usare Java per recuperare i nomi dei parametri del metodo.
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/25/2017
ms.openlocfilehash: 44bff372225ee1bf555eb3eeb34da918830980b4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60957564"
---
# <a name="customizing-bindings"></a>Personalizzazione delle associazioni

_È possibile personalizzare un'associazione di xamarin. Android modificando i metadati che controlla il processo di associazione. Queste modifiche manuali sono spesso necessari per la risoluzione degli errori di compilazione e per il data shaping dell'API risulta, in modo che sia più coerenza con C#/.NET. Queste guide illustrano la struttura di questi metadati, come modificare i metadati e come usare Java per recuperare i nomi dei parametri del metodo._


## <a name="overview"></a>Panoramica
 
Xamarin. Android consente di automatizzare gran parte del processo di associazione; in alcuni casi, tuttavia, la modifica manuale è necessario per risolvere i problemi seguenti:

-   Risoluzione di compilare gli errori causati dalla mancanza di tipi, tipi offuscati, nomi duplicati, i problemi di visibilità di classe e altre situazioni in cui non possono essere risolti dagli strumenti xamarin. Android. 

-   Modifica del mapping che xamarin. Android Usa per associare l'API Android a diversi tipi in C# (ad esempio, molti sviluppatori preferiscono eseguire il mapping di Java `int` le costanti per C# `enum` costanti).

-   Rimozione dei tipi inutilizzati che non devono essere associate. 

-   Aggiunta di tipi che non hanno una controparte nell'API Java sottostante. 

È possibile apportare alcune o tutte queste modifiche, modificando i metadati che controlla il processo di associazione.


## <a name="guides"></a>Guide

Le guide seguenti descrivono i metadati che controlla il processo di associazione e viene illustrato come modificare i metadati per risolvere questi problemi:

-   [Metadati per le associazioni Java](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) offre una panoramica dei metadati da inserire in un'associazione di Java.
    Descrive i vari passaggi manuali che sono talvolta necessarie per completare una libreria di binding Java e viene descritto come definire la forma un'API esposta da un'associazione di più da vicino seguire le indicazioni di progettazione .NET.

-   [Denominazione di parametri con Javadoc](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) viene illustrato come recuperare i nomi dei parametri in un progetto di Binding di linguaggio con Javadoc prodotta dal progetto Java associato.


 

