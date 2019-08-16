---
title: MDocArchiveToMsxDocConverter.exe non trovato rver.BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: af90baf6d7b94973a76bcf391f2cb28c45340602
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528154"
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe non trovato rver.BaseCommand.OnRequest

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di Novell. Tuttavia, se il problema si verifica nella versione più recente del software, inserire un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con le informazioni complete sul controllo delle versioni e l'output del log di compilazione completo.


## <a name="error-message"></a>Messaggio di errore

Questo errore può essere visualizzato nel *log del server Mac* in Visual Studio:

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

In questo messaggio sono presenti due problemi distinti:

1. `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    Questo errore è innocuo, ma è anche fuorviante. [Verrà rimosso](https://bugzilla.xamarin.com/show_bug.cgi?id=21667) in una versione futura.

2. `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    Questo errore è il problema reale. Sfortunatamente, a causa di una [limitazione](https://bugzilla.xamarin.com/show_bug.cgi?id=22080) questa analisi dello stack di eccezioni è incompleta. Se si nota una traccia dello stack incompleta come questa nel log del server Mac, è possibile `~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log` controllare il file nell'host di compilazione Mac per trovare la traccia dello stack completa.
