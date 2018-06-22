---
title: Impostazione delle variabili di ambiente di Runtime Mono per i progetti iOS in Studio a Xamarin
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/31/2017
ms.openlocfilehash: 5f4f3a2de012d35ddca9c1fa830d599d9d5acb17
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776118"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>Impostazione delle variabili di ambiente di Runtime Mono per i progetti iOS in Studio a Xamarin

Se è necessario impostare qualsiasi runtime variabili di ambiente per Mono, possono essere impostate nel **opzioni progetto > eseguire > Generale** pagina.

Nota: Operazione di Garbage Collection le variabili di ambiente SGen (MONO\_GC\_PARAMS) impostato in questo modo verrà utilizzato solo durante l'avvio da Xamarin Studio. Se si avvia l'app dal dispositivo, è possibile che le impostazioni per Sgen verranno ignorate. 

Per impostare in modo permanente una variabile di ambiente per un'app, è necessario aggiungere questo elemento come argomento mtouch aggiuntive (per tutte le configurazioni pertinenti):

```csharp
   --setenv=NAME=VALUE
```

Per visualizzare le variabili di ambiente che è possibile impostare, fare riferimento alla pagina man Mono: [ http://docs.go-mono.com/?link=man%3amono(1) ](http://docs.go-mono.com/?link=man%3amono(1)) vedere la sezione intitolata: `ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "Impostazione delle variabili di ambiente per un progetto")
