---
title: "Errore MT1009: Impossibile copiare l'assembly"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4a67537cc53aeecf1b86d11dbf041cea79587dd2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105402"
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>Errore MT1009: Impossibile copiare l'assembly

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di xamarin. IOS. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.

Come descritto nel nostro [note sulla versione](https://developer.xamarin.com/releases/ios/xamarin.ios_7/xamarin.ios_7.2/), si tratta di un problema noto in xamarin. IOS 7.2.6. Quindi questo problema è causato le autorizzazioni del file che necessitano di privilegi più elevati quando viene installato xamarin. IOS con un account utente diverso, l'account dello sviluppatore principale.

Per risolvere il problema, aprire il terminal sulla workstation Mac ed eseguire il comando seguente:

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`

