---
title: Denominazione di parametri con javadoc
description: Questo articolo illustra come ripristinare i nomi dei parametri in un progetto di associazione Java usando Javadoc generato dal progetto Java.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/20/2017
ms.openlocfilehash: fa1fb0656384455322a2d0a3562fc0ee3ca52397
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757606"
---
# <a name="naming-parameters-with-javadoc"></a>Denominazione di parametri con javadoc

_Questo articolo illustra come ripristinare i nomi dei parametri in un progetto di associazione Java usando Javadoc generato dal progetto Java._

## <a name="overview"></a>Panoramica

Quando si associa una libreria Java esistente, alcuni metadati sull'API associata vengono persi. In particolare i nomi dei parametri per i metodi. I nomi dei parametri verranno `p0`visualizzati `p1`come, e così via. Questo perché i file Java `.class` non conservano i nomi dei parametri usati nel codice sorgente Java. 

Un progetto di associazione Java Novell. Android può fornire i nomi dei parametri se ha accesso al codice HTML Javadoc dalla libreria originale. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>Integrazione di javadoc HTML in un progetto di binding Java

L'integrazione di javadoc HTML in un progetto di associazione Java è un processo manuale costituito dai passaggi seguenti: 

1. Scaricare Javadoc per la libreria
2. Modificare il `.csproj` file e aggiungere una `<JavaDocPaths>` proprietà:
3. Pulire e ricompilare il progetto

Al termine di questa operazione, i nomi di parametro Java originali dovrebbero essere presenti nelle API vincolate da un progetto di associazione Java. 

> [!NOTE]
> L'output di JavaDoc presenta una grande quantità di varianza. Il. La regola di associazione JAR non supporta tutte le possibili permutazioni e di conseguenza è possibile che alcuni parametri non siano denominati correttamente.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare Javadoc in un progetto di associazione Java per fornire nomi di parametro significativi per le API di associazione. 
