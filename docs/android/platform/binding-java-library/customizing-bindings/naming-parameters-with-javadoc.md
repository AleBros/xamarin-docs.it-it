---
title: Denominazione di parametri con Javadoc
description: Questo articolo illustra come ripristinare i nomi dei parametri in un progetto di Binding di linguaggio con Javadoc generato dal progetto Java.
ms.prod: xamarin
ms.assetid: 59E8EF16-1322-486A-BB16-353804B77356
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/20/2017
ms.openlocfilehash: e394377043953a297afed36a3ce0747a3e6d1512
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60955871"
---
# <a name="naming-parameters-with-javadoc"></a>Denominazione di parametri con Javadoc

_Questo articolo illustra come ripristinare i nomi dei parametri in un progetto di Binding di linguaggio con Javadoc generato dal progetto Java._


## <a name="overview"></a>Panoramica

Quando si associa una libreria Java esistente, alcuni metadati sull'API associata vengono perso. In particolare i nomi dei parametri a metodi. I nomi dei parametri verranno visualizzati come `p0`, `p1`e così via. Infatti, il linguaggio `.class` file non conservano i nomi dei parametri usati nel codice sorgente Java. 

Un progetto di associazione di xamarin. Android Java può fornire i nomi dei parametri se ha accesso al codice HTML Javadoc dalla libreria originale. 

## <a name="integrating-javadoc-html-into-a-java-binding-project"></a>L'integrazione di Javadoc HTML in un linguaggio di progetto di associazione

L'integrazione di HTML Javadoc in un progetto Java di associazione è un processo manuale costituito la procedura seguente: 

1.  Scaricare la documentazione Java relativa per la libreria
2.  Modificare il `.csproj` file e aggiungere un `<JavaDocPaths>` proprietà:
3.  Pulire e ricompilare il progetto

Al termine, i nomi dei parametri di Java originale deve essere presente nelle API associate da un progetto Java di associazione. 


> [!NOTE]
> È presente una grande quantità di varianza nell'output di JavaDoc. Il file con estensione JAR toolchain di associazione non supporta ogni permutazione possibili singolo e, di conseguenza, alcuni parametri potrebbero non essere denominati in modo corretto.


## <a name="summary"></a>Riepilogo

Questo articolo illustrato come utilizzare Javadoc in un progetto di Binding Java per fornire i nomi dei parametri di significato per le API associate. 

