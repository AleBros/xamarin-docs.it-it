---
title: Come si impostano le variabili di ambiente di runtime Mono per i progetti iOS in Xamarin Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/31/2017
ms.openlocfilehash: fdd208122934b6fa8194a644592d1e23c4000d57
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030900"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>Come si impostano le variabili di ambiente di runtime Mono per i progetti iOS in Xamarin Studio?

Se è necessario impostare le variabili di ambiente di runtime per mono, è possibile impostarle nelle **Opzioni del progetto > eseguire > pagina generale** .

Nota: le variabili di ambiente di Garbage Collection per SGen (MONO\_GC\_PARAMs) impostate in questo modo verranno usate solo quando si avvia da Xamarin Studio. Se si avvia l'app dal dispositivo, le impostazioni per SGen verranno ignorate. 

Per impostare in modo definitivo una variabile di ambiente per un'app, è necessario aggiungerla come argomento mTouch aggiuntivo (per tutte le configurazioni pertinenti):

```csharp
   --setenv=NAME=VALUE
```

Per visualizzare le variabili di ambiente che è possibile impostare, fare riferimento alla pagina Mono Man: [http://docs.go-mono.com/?link=man%3amono(1)](http://docs.go-mono.com/?link=man%3amono(1)) vedere la sezione intitolata: `ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "Setting environment variables for a project")
