---
title: Binding di framework nativi
description: In questo documento viene descritto come utilizzare l'opzione Objective Sharpie-Framework per creare un'associazione a una libreria distribuita come Framework.
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: conceptdev
ms.author: crdun
ms.date: 01/15/2016
ms.openlocfilehash: 560db570e915cc9bf261f482f03d972973968585
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279036"
---
# <a name="binding-native-frameworks"></a>Binding di framework nativi

A volte una libreria nativa viene distribuita come [Framework](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html). Objective Sharpie fornisce una funzionalità utile per l'associazione di Framework definiti correttamente tramite `-framework` l'opzione.

Ad esempio, l'associazione del [Framework Adobe Creative SDK](https://creativesdk.adobe.com/downloads.html) per iOS è semplice:

```
$ sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1
```

In alcuni casi, in un Framework viene specificato un file **info. plist** che indica in quale SDK deve essere compilato il Framework. Se queste informazioni sono presenti e non `-sdk` viene passata alcuna opzione esplicita, l'obiettivo Sharpie lo dedurrà dal file **info. plist** del `DTSDKName` Framework, ovvero la chiave o `DTPlatformName` una `DTPlatformVersion` combinazione di chiavi e.

L' `-framework` opzione non consente il passaggio di file di intestazione espliciti. Il file di intestazione Umbrella viene scelto per convenzione in base al nome del Framework. Se non è possibile trovare un'intestazione Umbrella, l'obiettivo Sharpie non tenterà di associare il Framework ed è necessario eseguire manualmente l'associazione fornendo i file di intestazione dell'ombrello corretti da analizzare, insieme a qualsiasi argomento del Framework per Clang (ad esempio `-F`opzione percorso di ricerca Framework).

Dietro le quinte, `-framework` specificare è semplicemente un tasto di scelta rapida. Gli argomenti di associazione seguenti sono identici `-framework` a quelli dell'abbreviazione.
Di particolare importanza è il `-F .` percorso di ricerca del Framework fornito a Clang (si noti lo spazio e il periodo, che sono necessari come parte del comando).

```
$ sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .
```
