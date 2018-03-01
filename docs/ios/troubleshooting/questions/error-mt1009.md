---
title: 'Errore MT1009: Impossibile copiare l''assembly'
ms.topic: article
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 030c54275ef384f4a020abaf79456705e8fac0d3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>Errore MT1009: Impossibile copiare l'assembly

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di xamarin. IOS. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.

Come descritto in questo [note sulla versione](https://developer.xamarin.com/releases/ios/xamarin.ios_7/xamarin.ios_7.2/), si tratta di un problema noto in xamarin. IOS 7.2.6. Questo problema sarà a causa di autorizzazioni di file che richiedono privilegi più elevati durante l'installazione di xamarin. IOS con un account utente diversi account principale dello sviluppatore.

Per risolvere questo problema, aprire il Terminal.app sulla workstation Mac ed eseguire il comando seguente:

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`

