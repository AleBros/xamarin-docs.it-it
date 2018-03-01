---
title: MDocArchiveToMsxDocConverter.exe not found rver.BaseCommand.OnRequest
ms.topic: article
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: cacefa28725973b59b7af42211e132879dadfca2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe not found rver.BaseCommand.OnRequest

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di Xamarin. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.


## <a name="error-message"></a>Messaggio di errore

Questo errore può essere visualizzato nel *Mac Server Log* in Visual Studio:

> ```Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
> rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
>   at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0```

In questo messaggio sono presenti 2 problemi distinti:

1.  `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    Questo errore non provoca problemi, ma anche è fuorviante. Si [verrà rimosso](https://bugzilla.xamarin.com/show_bug.cgi?id=21667) in una versione futura.

2.  `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    Questo errore è il problema reale. Sfortunatamente, a causa di un [limitazione](https://bugzilla.xamarin.com/show_bug.cgi?id=22080) questo stack dell'eccezione è *incompleto*. Se si nota una traccia di stack incompleto simile al seguente nel Log del Server Mac, è possibile controllare il `~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log` file nell'host di compilazione Mac per trovare la traccia dello stack completa.
