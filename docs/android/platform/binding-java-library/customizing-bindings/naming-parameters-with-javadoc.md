---
title: Denominazione dei parametri con Javadoc
description: In questo articolo viene illustrato come recuperare i nomi dei parametri in un progetto di associazione Java utilizzando Javadoc generato dal progetto Java.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/20/2017
ms.openlocfilehash: 060c4759d39bc3b8c424ce46dc615644540fe9c2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027673"
---
# <a name="naming-parameters-with-javadoc"></a>Denominazione dei parametri con Javadoc

_In questo articolo viene illustrato come recuperare i nomi dei parametri in un progetto di associazione Java utilizzando Javadoc generato dal progetto Java._

## <a name="overview"></a>Panoramica

Quando si associa una libreria Java esistente, alcuni metadati sull'API associata vengono persi. In particolare i nomi dei parametri ai metodi. I nomi dei `p0` `p1`parametri verranno visualizzati come , , e così via. Ciò è `.class` dovuto al fatto che i file Java non mantengono i nomi dei parametri utilizzati nel codice sorgente Java. 

Un progetto di associazione Java Xamarin.Android può fornire i nomi dei parametri se ha accesso al codice HTML Javadoc dalla libreria originale. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>Integrazione di HTML Javadoc in un progetto di associazione JavaIntegrating Javadoc HTML into a Java Binding Project

L'integrazione del codice HTML Javadoc in un progetto Java Binding è un processo manuale costituito dai seguenti passaggi: 

1. Scarica il codice Javadoc per la libreria
2. Modificare `.csproj` il file `<JavaDocPaths>` e aggiungere una proprietà:
3. Pulire e ricompilare il progetto

Al termine di questa operazione, i nomi dei parametri Java originali devono essere presenti nelle API associate da un progetto di associazione Java.Once this is done, the original Java parameter names should be present in the APIs bound by a Java Binding Project. 

> [!NOTE]
> C'è una grande quantità di varianza nell'output JavaDoc. Le. La toolchain di associazione JAR non supporta ogni singola permutazione possibile e di conseguenza alcuni parametri potrebbero non essere denominati correttamente.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come utilizzare Javadoc in un progetto di associazione Java per fornire i nomi di parametro di significato per le API associate. 
