---
title: Denominazione di parametri con javadoc
description: Questo articolo illustra come ripristinare i nomi dei parametri in un progetto di associazione Java usando Javadoc generato dal progetto Java.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/20/2017
ms.openlocfilehash: 060c4759d39bc3b8c424ce46dc615644540fe9c2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027673"
---
# <a name="naming-parameters-with-javadoc"></a>Denominazione di parametri con javadoc

_Questo articolo illustra come ripristinare i nomi dei parametri in un progetto di associazione Java usando Javadoc generato dal progetto Java._

## <a name="overview"></a>Panoramica

Quando si associa una libreria Java esistente, alcuni metadati sull'API associata vengono persi. In particolare i nomi dei parametri per i metodi. I nomi dei parametri verranno visualizzati come `p0`, `p1`e così via. Ciò è dovuto al fatto che i file di `.class` Java non conservano i nomi dei parametri utilizzati nel codice sorgente Java. 

Un progetto di associazione Java Novell. Android può fornire i nomi dei parametri se ha accesso al codice HTML Javadoc dalla libreria originale. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>Integrazione di javadoc HTML in un progetto di binding Java

L'integrazione di javadoc HTML in un progetto di associazione Java è un processo manuale costituito dai passaggi seguenti: 

1. Scaricare Javadoc per la libreria
2. Modificare il file di `.csproj` e aggiungere una proprietà `<JavaDocPaths>`:
3. Pulire e ricompilare il progetto

Al termine di questa operazione, i nomi di parametro Java originali dovrebbero essere presenti nelle API vincolate da un progetto di associazione Java. 

> [!NOTE]
> L'output di JavaDoc presenta una grande quantità di varianza. Il. La regola di associazione JAR non supporta tutte le possibili permutazioni e di conseguenza è possibile che alcuni parametri non siano denominati correttamente.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare Javadoc in un progetto di associazione Java per fornire nomi di parametro significativi per le API di associazione. 
