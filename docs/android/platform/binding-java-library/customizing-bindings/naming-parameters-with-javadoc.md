---
title: Denominazione di parametri con javadoc
description: Questo articolo illustra come ripristinare i nomi dei parametri in un progetto di associazione Java usando Javadoc generato dal progetto Java.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/20/2017
ms.openlocfilehash: bcfc5778ed4e5486d188f4eefbd32811792b44e5
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85852979"
---
# <a name="naming-parameters-with-javadoc"></a>Denominazione di parametri con javadoc

> [!IMPORTANT]
> Stiamo attualmente analizzando l'utilizzo dell'associazione personalizzata nella piattaforma Novell. Segui [**questo sondaggio**](https://www.surveymonkey.com/r/KKBHNLT) per informare le attività di sviluppo future.

_Questo articolo illustra come ripristinare i nomi dei parametri in un progetto di associazione Java usando Javadoc generato dal progetto Java._

## <a name="overview"></a>Panoramica

Quando si associa una libreria Java esistente, alcuni metadati sull'API associata vengono persi. In particolare i nomi dei parametri per i metodi. I nomi dei parametri verranno visualizzati come `p0` , `p1` e così via. Questo perché i file Java `.class` non conservano i nomi dei parametri usati nel codice sorgente Java. 

Un progetto di associazione Java Novell. Android può fornire i nomi dei parametri se ha accesso al codice HTML Javadoc dalla libreria originale. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>Integrazione di javadoc HTML in un progetto di binding Java

L'integrazione di javadoc HTML in un progetto di associazione Java è un processo manuale costituito dai passaggi seguenti: 

1. Scaricare Javadoc per la libreria
2. Modificare il `.csproj` file e aggiungere una `<JavaDocPaths>` proprietà:
3. Pulire e ricompilare il progetto

Al termine di questa operazione, i nomi di parametro Java originali dovrebbero essere presenti nelle API vincolate da un progetto di associazione Java. 

> [!NOTE]
> L'output di JavaDoc presenta una grande quantità di varianza. Il. La regola di associazione JAR non supporta tutte le possibili permutazioni e di conseguenza è possibile che alcuni parametri non siano denominati correttamente.

## <a name="summary"></a>Summary

Questo articolo ha illustrato come usare Javadoc in un progetto di associazione Java per fornire nomi di parametro significativi per le API di associazione. 
