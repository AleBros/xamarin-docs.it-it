---
title: MDocArchiveToMsxDocConverter.exe non trovato rver. BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 0746174857f66843ef9a09429b6286f2efca90d6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119761"
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe non trovato rver. BaseCommand.OnRequest

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di Xamarin. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.


## <a name="error-message"></a>Messaggio di errore

Questo errore potrebbe essere visualizzato nei *Mac Server Log* in Visual Studio:

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

Esistono 2 problemi separati in questo messaggio:

1.  `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    È un errore innocuo, ma anche è fuorviante. Si [verranno rimosse](https://bugzilla.xamarin.com/show_bug.cgi?id=21667) in una versione futura.

2.  `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    Questo errore è il problema reale. Purtroppo, a causa di un [limitazione](https://bugzilla.xamarin.com/show_bug.cgi?id=22080) questa analisi stack dell'eccezione viene *incompleto*. Se si nota una traccia stack incompleto simile al seguente nel Log del Server Mac, è possibile controllare il `~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log` file nell'host di compilazione Mac per trovare la traccia dello stack completo.
