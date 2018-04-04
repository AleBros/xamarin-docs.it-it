---
title: Denominazione dei parametri con Javadoc
description: In questo articolo viene illustrato come recuperare i nomi dei parametri in un progetto di associazione Java utilizzando Javadoc generato dal progetto Java.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/20/2017
ms.openlocfilehash: 7517e46c5b66123dc4e12fb5562c59f569f249aa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="naming-parameters-with-javadoc"></a>Denominazione dei parametri con Javadoc

_In questo articolo viene illustrato come recuperare i nomi dei parametri in un progetto di associazione Java utilizzando Javadoc generato dal progetto Java._


## <a name="overview"></a>Panoramica

Quando si associa una raccolta esistente di Java, alcuni metadati sull'API associata vengono persi. In particolare i nomi dei parametri ai metodi. I nomi dei parametri verranno visualizzati come `p0`, `p1`e così via. Infatti, il linguaggio `.class` file non mantengono i nomi dei parametri utilizzati nel codice sorgente Java. 

Un progetto di associazione xamarin Java è possibile specificare i nomi di parametro se ha accesso al codice HTML di Javadoc dalla libreria originale. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>Integrazione di Javadoc HTML in un progetto di associazione di Java

Integrazione di Javadoc HTML in un progetto Java Binding è un processo manuale costituita da quanto segue: 

1.  Scaricare la documentazione Java per la libreria
2.  Modificare il `.csproj` file e aggiungere un `<JavaDocPaths>` proprietà:
3.  Pulire e ricompilare il progetto

Al termine, i nomi dei parametri di Java originale deve essere presente nelle API associate da un progetto di associazione del linguaggio. 


> [!NOTE]
> È un notevole della varianza nell'output JavaDoc. Il file con estensione File JAR associazione toolchain non supporta ogni permutazione possibili singolo e di conseguenza alcuni parametri potrebbero non essere denominati in modo corretto.


## <a name="summary"></a>Riepilogo

In questo articolo trattato come usare Javadoc in un progetto di associazione del linguaggio per fornire i nomi dei parametri di significato per le API associate. 

