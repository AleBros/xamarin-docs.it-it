---
title: Come abilitare Intellisense nei file .axml Android?
ms.topic: article
ms.prod: xamarin
ms.assetid: 84850CB2-1CE2-4D3F-BD01-6B3B033F5A4C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.openlocfilehash: ea5c4e9f7e7f1c487d4f53650b10f88afc1b41e6
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="how-do-i-enable-intellisense-in-android-axml-files"></a>Come abilitare Intellisense nei file .axml Android?

Android Intellisense in Visual Studio necessari due file di XML Schema per funzionare in modo appropriato. Per trovare questi file, passare a questa cartella:

`C:\Program Files (x86)\Xamarin Studio\AddIns\MonoDevelop.MonoDroid\schemas`*

* (In precedenza: `C:\Program Files (x86)\MSBuild\Xamarin\Android`)

Sono disponibili all'interno di due file con estensione xsd:

1. android-layout-xml.xsd
2. schemas.android.com.apk.res.android.xsd

Visual Studio consente di mantenere tutti gli schemi XML all'interno della rispettiva cartella:

`C:\Program Files (x86)\Microsoft Visual Studio 12.0\Xml\Schemas`

È possibile scegliere di spostare questi due file con estensione XSD nel percorso precedente, o semplicemente aggiungere tali schemi all'interno di Visual Studio. È quindi possibile "aggiungere" questi schemi all'interno di Visual Studio tramite il **XML > schemi > Aggiungi** finestra di dialogo.






