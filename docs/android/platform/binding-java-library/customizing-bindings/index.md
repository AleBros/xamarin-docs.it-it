---
title: Personalizzazione delle associazioni
description: È possibile personalizzare un'associazione Xamarin.Android modificando i metadati che controllano il processo di associazione. Queste modifiche manuali sono spesso necessarie per risolvere gli errori di compilazione e per modellare l'API risultante in modo che sia più coerente con C . Queste guide illustrano la struttura di questi metadati, come modificare i metadati e come utilizzare JavaDoc per recuperare i nomi dei parametri del metodo.
ms.prod: xamarin
ms.assetid: 63C5078D-9E42-4F70-AF8C-8CEEA84FB6AF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 04f3720d8684129476c955819390e91330a7800a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020648"
---
# <a name="customizing-bindings"></a>Personalizzazione delle associazioni

_È possibile personalizzare un'associazione Xamarin.Android modificando i metadati che controllano il processo di associazione. Queste modifiche manuali sono spesso necessarie per risolvere gli errori di compilazione e per modellare l'API risultante in modo che sia più coerente con C . Queste guide illustrano la struttura di questi metadati, come modificare i metadati e come utilizzare JavaDoc per recuperare i nomi dei parametri del metodo._

## <a name="overview"></a>Panoramica

Xamarin.Android automatizza gran parte del processo di binding; tuttavia, in alcuni casi è necessaria una modifica manuale per risolvere i seguenti problemi:

- Risoluzione degli errori di compilazione causati da tipi mancanti, tipi offuscati, nomi duplicati, problemi di visibilità delle classi e altre situazioni che non possono essere risolte dagli strumenti Xamarin.Android.Resolving build errors caused by missing types, obfuscated types, duplicate names, class visibility issues, and other situations that cannot be resolved by the Xamarin.Android tooling. 

- Modifica del mapping utilizzato da Xamarin.Android per associare l'API Android a tipi `int` diversi in C, ad esempio, molti sviluppatori preferiscono eseguire il mapping delle costanti Java alle `enum` costanti di C.

- Rimozione dei tipi inutilizzati che non devono essere associati. 

- Aggiunta di tipi senza controparte nell'API Java sottostante. 

È possibile apportare alcune o tutte queste modifiche modificando i metadati che controllano il processo di associazione.

## <a name="guides"></a>Guide

Le guide seguenti descrivono i metadati che controllano il processo di associazione e spiegano come modificarli per risolvere questi problemi:The following guides describe the metadata that controls the binding process and explain how to modify this metadata to address these issues:

- [Metadati delle associazioni Java](~/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata.md) fornisce una panoramica dei metadati che vengono inseriti in un'associazione Java.Java Bindings Metadata provides an overview of the metadata that goes into a Java binding.
    Vengono descritti i vari passaggi manuali che a volte sono necessari per completare una libreria di associazione Java e viene illustrato come modellare un'API esposta da un'associazione per seguire più da vicino le linee guida di progettazione .NET.

- [La denominazione](~/android/platform/binding-java-library/customizing-bindings/naming-parameters-with-javadoc.md) dei parametri con Javadoc viene illustrato come recuperare i nomi dei parametri in un progetto di associazione Java utilizzando Javadoc prodotto dal progetto Java associato.
