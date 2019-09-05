---
title: Come si impostano le variabili di ambiente di runtime Mono per i progetti iOS in Xamarin Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/31/2017
ms.openlocfilehash: f8e3855b10a20bd4312420f8faf6c68dedde0c67
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292097"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>Come si impostano le variabili di ambiente di runtime Mono per i progetti iOS in Xamarin Studio?

Se è necessario impostare le variabili di ambiente di runtime per mono, è possibile impostarle nelle **Opzioni del progetto > eseguire > pagina generale** .

Nota: Le variabili di ambiente di Garbage Collection per\_SGen\_(mono GC params) impostate in questo modo verranno usate solo quando si avvia da Xamarin Studio. Se si avvia l'app dal dispositivo, le impostazioni per SGen verranno ignorate. 

Per impostare in modo definitivo una variabile di ambiente per un'app, è necessario aggiungerla come argomento mTouch aggiuntivo (per tutte le configurazioni pertinenti):

```csharp
   --setenv=NAME=VALUE
```

Per visualizzare le variabili di ambiente che è possibile impostare, fare riferimento alla pagina Mono Man:  [http://docs.go-mono.com/?link=man%3amono(1)](http://docs.go-mono.com/?link=man%3amono(1))Vedere la sezione intitolata:`ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "Impostazione delle variabili di ambiente per un progetto")
