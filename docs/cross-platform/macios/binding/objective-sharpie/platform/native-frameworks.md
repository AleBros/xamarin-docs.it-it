---
title: Binding di framework nativi
description: Questo documento viene descritto come utilizzare Sharpie Objective - opzione di framework per creare un'associazione a una libreria distribuito come un framework.
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 0da40918c8ae36c4ab3d4c41128429b49706d653
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977648"
---
# <a name="binding-native-frameworks"></a>Binding di framework nativi

In alcuni casi una libreria nativa viene distribuita come un [framework](https://developer.apple.com/library/mac/documentation/MacOSX/Conceptual/BPFrameworks/Concepts/WhatAreFrameworks.html). Sharpie obiettivo offre una comoda funzionalità per la corretta associazione definito Framework tramite la `-framework` opzione.

Ad esempio, l'associazione di [Adobe Creative SDK Framework](https://creativesdk.adobe.com/downloads.html) per iOS è molto semplice:

<pre>$ <b>sharpie bind \
    -framework AdobeCreativeSDKFoundation.framework \
    -sdk iphoneos8.1</b></pre>

In alcuni casi, un framework specificherà un' **Info. plist** indica su quale SDK il framework deve essere compilato. Se è presente queste informazioni e non esplicita `-sdk` opzione viene passato, l'obiettivo Sharpie perché venga dedotto del framework **Info. plist** (entrambi il `DTSDKName` chiave o una combinazione del `DTPlatformName` e `DTPlatformVersion`delle chiavi).

Il `-framework` opzione non consente i file esplicitamente l'intestazione deve essere passato. Il file di intestazione generico viene scelto per convenzione in base al nome del framework. Se non è stata trovata un'intestazione di ombrello, obiettivo Sharpie non tenterà di associare il framework ed è necessario eseguire manualmente l'associazione, fornendo i file di intestazione ombrello corretto da analizzare, insieme a eventuali argomenti framework per clang (ad esempio la `-F`opzione di percorso di ricerca framework).

Dietro le quinte, specificando `-framework` è solo una scelta rapida. I seguenti argomenti di binding sono identici al `-framework` a sintassi abbreviata precedente.
Di particolare importanza è il `-F .` percorso di ricerca framework fornito da clang (si noti lo spazio e il periodo, quali sono necessari come parte del comando).

<pre>$ <b>sharpie bind \
    -sdk iphoneos8.1 \
    AdobeCreativeSDKFoundation.framework/Headers/AdobeCreativeSDKFoundation.h \
    -scope AdobeCreativeSDKFoundation.framework/Headers \
    -c -F .</b></pre>
