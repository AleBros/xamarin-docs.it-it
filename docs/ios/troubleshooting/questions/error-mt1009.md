---
title: "Errore MT1009: non è stato possibile copiare l'assembly"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F9FEDFF5-C84C-42B4-8F25-E34846E7315A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 1ccf85c03ab1f2cff6428a0120a8ceb4de250761
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031154"
---
# <a name="error-mt1009-could-not-copy-the-assembly"></a>Errore MT1009: non è stato possibile copiare l'assembly

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di Xamarin.iOS. Tuttavia, se il problema si verifica nella versione più recente del software, inserire un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con le informazioni complete sul controllo delle versioni e l'output del log di compilazione completo.

Come descritto nelle [Note sulla versione](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_7/xamarin.ios_7.2/index.md), si tratta di un problema noto in Xamarin.iOS 7.2.6. Questo problema è dovuto alle autorizzazioni per i file che richiedono privilegi più elevati quando Xamarin.iOS viene installato con un account utente diverso, quindi l'account principale dello sviluppatore.

Per aggirare il problema, aprire l'app Terminal. nella workstation Mac ed eseguire il comando seguente:

`sudo chmod 0644 /Developer/MonoTouch/usr/lib/mono/2.1/monotouch.dll.mdb`
