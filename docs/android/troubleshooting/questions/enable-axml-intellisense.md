---
title: Come abilitare Intellisense nei file .axml Android?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 84850CB2-1CE2-4D3F-BD01-6B3B033F5A4C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 8278576355299894eab1c7c6d3ceaadb607fe915
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
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






