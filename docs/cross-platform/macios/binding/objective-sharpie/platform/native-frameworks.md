---
title: Associazione Framework nativo
ms.topic: article
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 4588a879452b4ee49535ac8882977be2c064a43f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="binding-native-frameworks"></a>Associazione Framework nativo

Talvolta una libreria nativa viene distribuita come una [framework](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html). Obiettivo Sharpie offre una comoda funzionalità per associazione correttamente definito Framework tramite il `-framework` opzione.

Ad esempio, l'associazione di [Adobe Creative SDK Framework](https://creativesdk.adobe.com/downloads.html) per iOS è semplici:

<pre>$ <b>sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1</b></pre>

In alcuni casi, un framework specificherà un **Info. plist** che indica su quale SDK framework deve essere compilato. Se queste informazioni esistono ed esplicite non `-sdk` viene passata l'opzione, verrà dedotto dal framework Sharpie obiettivo **Info. plist** (entrambi il `DTSDKName` chiave o una combinazione del `DTPlatformName` e `DTPlatformVersion`chiavi).

Il `-framework` opzione non consente i file di intestazione esplicita deve essere passato. Il file di intestazione generico viene scelto per convenzione in base al nome di framework. Se non è possibile trovare un'intestazione di tipo generico, obiettivo Sharpie non tenterà di associare il framework ed è necessario eseguire manualmente l'associazione, fornendo i file di intestazione ombrello corretto da analizzare, insieme a tutti gli argomenti di framework per clang (ad esempio il `-F`opzione percorso di ricerca framework).

Dietro le quinte, specificando `-framework` è solo una scelta rapida. Associazione degli argomenti seguenti sono identici al `-framework` a sintassi abbreviata precedente.
Di particolare importanza è il `-F .` percorso di ricerca di framework fornita per clang (si noti lo spazio e il periodo, quali sono necessari come parte del comando).

<pre>$ <b>sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .</b></pre>

