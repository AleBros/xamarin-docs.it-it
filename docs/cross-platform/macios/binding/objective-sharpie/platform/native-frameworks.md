---
title: Binding di framework nativi
description: In questo documento viene descritto come utilizzare l'opzione Objective Sharpie-Framework per creare un'associazione a una libreria distribuita come Framework.
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: davidortinau
ms.author: daortin
ms.date: 01/15/2016
ms.openlocfilehash: 0733e2b406f5032a2e2717df66c96dcb28301f09
ms.sourcegitcommit: 24883be72e485e5311dd0eb91f9a22f78eeec11a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2020
ms.locfileid: "78292151"
---
# <a name="binding-native-frameworks"></a>Binding di framework nativi

A volte una libreria nativa viene distribuita come [Framework](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html). Obiettivo Sharpie fornisce una funzionalità utile per l'associazione di Framework definiti correttamente tramite l'opzione `-framework`.

Ad esempio, l'associazione del [Framework Adobe Creative SDK](https://creativesdk.adobe.com/downloads.html) per iOS è semplice:

```
$ sharpie bind \
    -framework ./AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1
```

In alcuni casi, in un Framework viene specificato un file **info. plist** che indica in quale SDK deve essere compilato il Framework. Se queste informazioni sono presenti e non viene passata alcuna opzione esplicita `-sdk`, l'obiettivo Sharpie lo dedurrà dal file **info. plist** del Framework (la chiave di `DTSDKName` o una combinazione di `DTPlatformName` e `DTPlatformVersion` chiavi).

L'opzione `-framework` non consente il passaggio di file di intestazione espliciti. Il file di intestazione Umbrella viene scelto per convenzione in base al nome del Framework. Se non è possibile trovare un'intestazione Umbrella, l'obiettivo Sharpie non tenterà di associare il Framework ed è necessario eseguire manualmente l'associazione specificando i file di intestazione dell'ombrello corretti da analizzare, insieme a tutti gli argomenti del Framework per Clang, ad esempio l'opzione del percorso di ricerca `-F` Framework.

Dietro le quinte, specificare `-framework` è semplicemente un tasto di scelta rapida. Gli argomenti di associazione seguenti sono identici all'abbreviazione `-framework` sopra.
Di particolare importanza è il percorso di ricerca di `-F .` Framework fornito a Clang (si noti lo spazio e il periodo, che sono necessari come parte del comando).

```
$ sharpie bind \
    -sdk iphoneos8.1 \
    ./AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .
```
