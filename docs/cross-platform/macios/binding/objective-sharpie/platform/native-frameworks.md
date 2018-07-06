---
title: Binding di Framework nativi
description: Questo documento viene descritto come utilizzare Sharpie Objective - opzione di framework per creare un'associazione a una libreria distribuito come un framework.
ms.prod: xamarin
ms.assetid: 91AE058A-3A1F-41A9-9DE4-4B96880A1869
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: ca103ee027597813be51e126aaa05f9aa969af35
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855097"
---
# <a name="binding-native-frameworks"></a>Binding di Framework nativi

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

## <a name="related-links"></a>Collegamenti correlati

- [Corso di Xamarin University: Creazione di una libreria di binding di Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Corsi di Xamarin University: Compilare una libreria di binding Objective-C con Sharpie obiettivo](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)

